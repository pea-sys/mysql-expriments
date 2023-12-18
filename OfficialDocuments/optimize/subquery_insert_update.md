### 8.2.2.1 準結合変換による IN および EXISTS サブクエリー述語の最適化
準結合は、テーブルのプルアウト、重複の除去、最初の一致、緩やかなスキャン、実体化などの複数の実行戦略を可能にする準備時変換です。

```sql
mysql> explain analyze select first_name, emp_no from employees inner join dept_emp where employees.emp_no = dept_emp.emp_no\G
ERROR 1052 (23000): Column 'emp_no' in field list is ambiguous
mysql> explain analyze select first_name, employees.emp_no from employees inner join dept_emp where employees.emp_no = dept_emp.emp_no\G
*************************** 1. row ***************************
EXPLAIN: -> Nested loop inner join  (cost=134825 rows=322996) (actual time=0.0744..1222 rows=331603 loops=1)
    -> Table scan on employees  (cost=29448 rows=292158) (actual time=0.0572..145 rows=300024 loops=1)
    -> Covering index lookup on dept_emp using PRIMARY (emp_no=employees.emp_no)  (cost=0.25 rows=1.11) (actual time=0.0025..0.00339 rows=1.11 loops=300024)

1 row in set (1.27 sec)

mysql> explain analyze select first_name, emp_no from employees where emp_no in (select emp_no from dept_emp)\G
*************************** 1. row ***************************
EXPLAIN: -> Nested loop inner join  (cost=9.67e+9 rows=96.7e+9) (actual time=873..1825 rows=300024 loops=1)
    -> Table scan on employees  (cost=29448 rows=292158) (actual time=0.0619..159 rows=300024 loops=1)
    -> Single-row index lookup on <subquery2> using <auto_distinct_key> (emp_no=employees.emp_no)  (cost=66413..66413 rows=1) (actual time=0.00536..0.00543 rows=1 loops=300024)
        -> Materialize with deduplication  (cost=66413..66413 rows=331143) (actual time=873..873 rows=300024 loops=1)
            -> Covering index scan on dept_emp using dept_no  (cost=33299 rows=331143) (actual time=0.0238..87.7 rows=331603 loops=1)

1 row in set (1.87 sec)

mysql> explain analyze select first_name, emp_no from employees where exists (select * from dept_emp where employees.emp_no = dept_emp.emp_no)\G
*************************** 1. row ***************************
EXPLAIN: -> Nested loop inner join  (cost=9.67e+9 rows=96.7e+9) (actual time=876..1837 rows=300024 loops=1)
    -> Table scan on employees  (cost=29448 rows=292158) (actual time=0.0576..161 rows=300024 loops=1)
    -> Single-row index lookup on <subquery2> using <auto_distinct_key> (emp_no=employees.emp_no)  (cost=66413..66413 rows=1) (actual time=0.00539..0.00545 rows=1 loops=300024)
        -> Materialize with deduplication  (cost=66413..66413 rows=331143) (actual time=876..876 rows=300024 loops=1)
            -> Covering index scan on dept_emp using dept_no  (cost=33299 rows=331143) (actual time=0.0236..91 rows=331603 loops=1)

1 row in set, 1 warning (1.88 sec)
```

### 8.2.2.2 実体化を使用したサブクエリーの最適化
オプティマイザは実体化を使用して、より効率的なサブクエリー処理を可能にします。


実体化
```sql
mysql> explain analyze SELECT * FROM t1
    -> WHERE t1.c1 IN (SELECT t2.c1 FROM t2 WHERE t2.c2 < 100)\G
*************************** 1. row ***************************
EXPLAIN: -> Remove duplicate t1 rows using temporary table (weedout)  (cost=0.7 rows=1) (actual time=0.0239..0.0239 rows=0 loops=1)
    -> Nested loop inner join  (cost=0.7 rows=1) (actual time=0.0219..0.0219 rows=0 loops=1)
        -> Filter: ((t2.c2 < 100) and (t2.c1 is not null))  (cost=0.35 rows=1) (actual time=0.021..0.021 rows=0 loops=1)
            -> Table scan on t2  (cost=0.35 rows=1) (actual time=0.0206..0.0206 rows=0 loops=1)
        -> Index lookup on t1 using PRIMARY (c1=t2.c1)  (cost=0.35 rows=1) (never executed)

1 row in set (0.00 sec)
```
### 8.2.2.4 マージまたは実体化を使用した導出テーブル、ビュー参照および共通テーブル式の最適化
オプティマイザは、次の 2 つの戦略を使用して導出テーブル参照を処理できます (ビュー参照および共通テーブル式にも適用されます):

* 導出テーブルの外部クエリーブロックへのマージ

* 導出テーブルを内部一時テーブルに実体化
```sql
mysql> explain analyze SELECT * FROM (SELECT * FROM t1) AS derived_t1\G
*************************** 1. row ***************************
EXPLAIN: -> Table scan on t1  (cost=0.35 rows=1) (actual time=0.0208..0.0208 rows=0 loops=1)

1 row in set (0.00 sec)

mysql> explain analyze SELECT * FROM t1\G
*************************** 1. row ***************************
EXPLAIN: -> Table scan on t1  (cost=0.35 rows=1) (actual time=0.0204..0.0204 rows=0 loops=1)

1 row in set (0.00 sec)
```
実体化回避
```sql
mysql> explain analyze SELECT * FROM t1 JOIN (SELECT t2.c1 FROM t2) AS derived_t2 ON t1.c2=derived_t2.c1 WHERE t1.c1 > 0\G
*************************** 1. row ***************************
EXPLAIN: -> Inner hash join (t1.c2 = t2.c1)  (cost=0.81 rows=1) (actual time=0.0239..0.0239 rows=0 loops=1)
    -> Filter: (t1.c1 > 0)  (cost=0.46 rows=1) (never executed)
        -> Index range scan on t1 using PRIMARY over (0 < c1)  (cost=0.46 rows=1) (never executed)
    -> Hash
        -> Table scan on t2  (cost=0.35 rows=1) (actual time=0.0154..0.0154 rows=0 loops=1)

1 row in set (0.00 sec)
```
cteにより実体化
```sql
mysql> explain analyze WITH
    ->   cte1 AS (SELECT c1, c2 FROM t1),
    ->   cte2 AS (SELECT c1, c2 FROM t2)
    -> SELECT /*+ MERGE(cte1) NO_MERGE(cte2) */ cte1.c1, cte2.c2
    -> FROM cte1 JOIN cte2
    -> WHERE cte1.c1 = cte2.c2\G
*************************** 1. row ***************************
EXPLAIN: -> Nested loop inner join  (cost=0.95 rows=1) (actual time=0.0224..0.0224 rows=0 loops=1)
    -> Covering index scan on t1 using PRIMARY  (cost=0.35 rows=1) (actual time=0.0203..0.0203 rows=0 loops=1)
    -> Index lookup on cte2 using <auto_key0> (c2=t1.c1)  (cost=0.8..1.15 rows=2) (never executed)
        -> Materialize CTE cte2  (cost=0.45..0.45 rows=1) (never executed)
            -> Table scan on t2  (cost=0.35 rows=1) (never executed)

1 row in set (0.00 sec)
```

### 8.2.3 INFORMATION_SCHEMA クエリーの最適化
データベースを監視するアプリケーションでは、INFORMATION_SCHEMA テーブルを頻繁に使用できます。
```sql
mysql> SELECT COLLATION_NAME
    ->        FROM INFORMATION_SCHEMA.COLLATION_CHARACTER_SET_APPLICABILITY
    ->        WHERE CHARACTER_SET_NAME = 'utf8mb4';
+----------------------------+
| COLLATION_NAME             |
+----------------------------+
| utf8mb4_general_ci         |
| utf8mb4_bin                |
| utf8mb4_unicode_ci         |
| utf8mb4_icelandic_ci       |
| utf8mb4_latvian_ci         |
| utf8mb4_romanian_ci        |
| utf8mb4_slovenian_ci       |
| utf8mb4_polish_ci          |
| utf8mb4_estonian_ci        |
| utf8mb4_spanish_ci         |
| utf8mb4_swedish_ci         |
| utf8mb4_turkish_ci         |
| utf8mb4_czech_ci           |
| utf8mb4_danish_ci          |
| utf8mb4_lithuanian_ci      |
| utf8mb4_slovak_ci          |
| utf8mb4_spanish2_ci        |
| utf8mb4_roman_ci           |
| utf8mb4_persian_ci         |
| utf8mb4_esperanto_ci       |
| utf8mb4_hungarian_ci       |
| utf8mb4_sinhala_ci         |
| utf8mb4_german2_ci         |
| utf8mb4_croatian_ci        |
| utf8mb4_unicode_520_ci     |
| utf8mb4_vietnamese_ci      |
| utf8mb4_0900_ai_ci         |
| utf8mb4_de_pb_0900_ai_ci   |
| utf8mb4_is_0900_ai_ci      |
| utf8mb4_lv_0900_ai_ci      |
| utf8mb4_ro_0900_ai_ci      |
| utf8mb4_sl_0900_ai_ci      |
| utf8mb4_pl_0900_ai_ci      |
| utf8mb4_et_0900_ai_ci      |
| utf8mb4_es_0900_ai_ci      |
| utf8mb4_sv_0900_ai_ci      |
| utf8mb4_tr_0900_ai_ci      |
| utf8mb4_cs_0900_ai_ci      |
| utf8mb4_da_0900_ai_ci      |
| utf8mb4_lt_0900_ai_ci      |
| utf8mb4_sk_0900_ai_ci      |
| utf8mb4_es_trad_0900_ai_ci |
| utf8mb4_la_0900_ai_ci      |
| utf8mb4_eo_0900_ai_ci      |
| utf8mb4_hu_0900_ai_ci      |
| utf8mb4_hr_0900_ai_ci      |
| utf8mb4_vi_0900_ai_ci      |
| utf8mb4_0900_as_cs         |
| utf8mb4_de_pb_0900_as_cs   |
| utf8mb4_is_0900_as_cs      |
| utf8mb4_lv_0900_as_cs      |
| utf8mb4_ro_0900_as_cs      |
| utf8mb4_sl_0900_as_cs      |
| utf8mb4_pl_0900_as_cs      |
| utf8mb4_et_0900_as_cs      |
| utf8mb4_es_0900_as_cs      |
| utf8mb4_sv_0900_as_cs      |
| utf8mb4_tr_0900_as_cs      |
| utf8mb4_cs_0900_as_cs      |
| utf8mb4_da_0900_as_cs      |
| utf8mb4_lt_0900_as_cs      |
| utf8mb4_sk_0900_as_cs      |
| utf8mb4_es_trad_0900_as_cs |
| utf8mb4_la_0900_as_cs      |
| utf8mb4_eo_0900_as_cs      |
| utf8mb4_hu_0900_as_cs      |
| utf8mb4_hr_0900_as_cs      |
| utf8mb4_vi_0900_as_cs      |
| utf8mb4_ja_0900_as_cs      |
| utf8mb4_ja_0900_as_cs_ks   |
| utf8mb4_0900_as_ci         |
| utf8mb4_ru_0900_ai_ci      |
| utf8mb4_ru_0900_as_cs      |
| utf8mb4_zh_0900_as_cs      |
| utf8mb4_0900_bin           |
| utf8mb4_nb_0900_ai_ci      |
| utf8mb4_nb_0900_as_cs      |
| utf8mb4_nn_0900_ai_ci      |
| utf8mb4_nn_0900_as_cs      |
| utf8mb4_sr_latn_0900_ai_ci |
| utf8mb4_sr_latn_0900_as_cs |
| utf8mb4_bs_0900_ai_ci      |
| utf8mb4_bs_0900_as_cs      |
| utf8mb4_bg_0900_ai_ci      |
| utf8mb4_bg_0900_as_cs      |
| utf8mb4_gl_0900_ai_ci      |
| utf8mb4_gl_0900_as_cs      |
| utf8mb4_mn_cyrl_0900_ai_ci |
| utf8mb4_mn_cyrl_0900_as_cs |
+----------------------------+
89 rows in set (0.01 sec)

mysql> EXPLAIN SELECT COLLATION_NAME
    ->        FROM INFORMATION_SCHEMA.COLLATION_CHARACTER_SET_APPLICABILITY
    ->        WHERE CHARACTER_SET_NAME = 'utf8mb4'\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: cs
   partitions: NULL
         type: const
possible_keys: PRIMARY,name
          key: name
      key_len: 194
          ref: const
         rows: 1
     filtered: 100.00
        Extra: Using index
*************************** 2. row ***************************
           id: 1
  select_type: SIMPLE
        table: col
   partitions: NULL
         type: ref
possible_keys: character_set_id
          key: character_set_id
      key_len: 8
          ref: const
         rows: 89
     filtered: 100.00
        Extra: NULL
2 rows in set, 1 warning (0.00 sec)

mysql> SHOW WARNINGS\G
*************************** 1. row ***************************
  Level: Note
   Code: 1003
Message: /* select#1 */ select `mysql`.`col`.`name` AS `COLLATION_NAME` from `mysql`.`character_sets` `cs` join `mysql`.`collations` `col` where ((`mysql`.`col`.`character_set_id` = '255') and ('utf8mb4' = 'utf8mb4'))
1 row in set (0.00 sec)
```

### 8.2.4 パフォーマンススキーマクエリーの最適化
データベースを監視するアプリケーションでは、「パフォーマンススキーマ」テーブルを頻繁に使用できます。 これらのテーブルに対するクエリーを最も効率的に記述するには、インデックスを利用します。

特定の「パフォーマンススキーマ」テーブルにインデックスがあるかどうかとその内容を確認するには、SHOW INDEX または SHOW CREATE TABLE を使用します
```sql
mysql> SHOW INDEX FROM performance_schema.accounts\G
*************************** 1. row ***************************
        Table: accounts
   Non_unique: 0
     Key_name: ACCOUNT
 Seq_in_index: 1
  Column_name: USER
    Collation: NULL
  Cardinality: NULL
     Sub_part: NULL
       Packed: NULL
         Null: YES
   Index_type: HASH
      Comment:
Index_comment:
      Visible: YES
   Expression: NULL
*************************** 2. row ***************************
        Table: accounts
   Non_unique: 0
     Key_name: ACCOUNT
 Seq_in_index: 2
  Column_name: HOST
    Collation: NULL
  Cardinality: NULL
     Sub_part: NULL
       Packed: NULL
         Null: YES
   Index_type: HASH
      Comment:
Index_comment:
      Visible: YES
   Expression: NULL
2 rows in set (0.01 sec)
```

```sql
mysql> EXPLAIN SELECT * FROM performance_schema.accounts
    ->        WHERE (USER,HOST) = ('root','localhost')\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: accounts
   partitions: NULL
         type: const
possible_keys: ACCOUNT
          key: ACCOUNT
      key_len: 385
          ref: const,const
         rows: 1
     filtered: 100.00
        Extra: NULL
1 row in set, 1 warning (0.01 sec)
```
パフォーマンススキーマのインデックスは仮想です: これらはパフォーマンススキーマストレージエンジンの構成であり、メモリーやディスクストレージを使用しません。 

### 8.2.5.1 INSERT ステートメントの最適化
行の挿入に必要な時間は、次の要因によって決まります。ここでの数はおよその割合を示しています。

* 接続: (3)
* サーバーへのクエリーの送信: (2)
* クエリーの解析: (2)
* 行の挿入: (1 ×行サイズ)
* インデックスの挿入: (1 ×インデックス数)
* クローズ: (1)

### 8.2.5.2 UPDATE ステートメントの最適化
更新ステートメントは、SELECT クエリーと同様に最適化されますが、書き込みの追加のオーバーヘッドがあります。

### 8.2.5.3 DELETE ステートメントの最適化
MyISAM テーブル内の個々の行を削除するために必要な時間は、インデックスの数に正確に比例します。 行をもっと速く削除するには、key_buffer_size システム変数を増やして、キーキャッシュのサイズを大きくできます。
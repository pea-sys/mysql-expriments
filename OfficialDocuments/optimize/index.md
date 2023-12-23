### 8.3.6 マルチカラムインデックス
MySQL は複合インデックス (つまり、複数のカラムに対するインデックス) を作成できます。

テーブルが次のような仕様であるとします。
```sql
mysql> CREATE TABLE test (
    ->     id         INT NOT NULL,
    ->     last_name  CHAR(30) NOT NULL,
    ->     first_name CHAR(30) NOT NULL,
    ->     PRIMARY KEY (id),
    ->     INDEX name (last_name,first_name)
    -> );
Query OK, 0 rows affected (0.07 sec)
```

```sql
mysql> explain analyze SELECT * FROM test WHERE last_name='Jones'\G
*************************** 1. row ***************************
EXPLAIN: -> Filter: (test.last_name = 'Jones')  (cost=0.35 rows=1) (actual time=0.0202..0.0202 rows=0 loops=1)
    -> Covering index lookup on test using name (last_name='Jones')  (cost=0.35 rows=1) (actual time=0.0191..0.0191 rows=0 loops=1)

1 row in set (0.00 sec)

mysql> explain analyze SELECT * FROM test
    ->   WHERE last_name='Jones' AND first_name='John'\G
*************************** 1. row ***************************
EXPLAIN: -> Filter: ((test.first_name = 'John') and (test.last_name = 'Jones'))  (cost=0.35 rows=1) (actual time=0.0162..0.0162 rows=0 loops=1)
    -> Covering index lookup on test using name (last_name='Jones', first_name='John')  (cost=0.35 rows=1) (actual time=0.0155..0.0155 rows=0 loops=1)

1 row in set (0.00 sec)

mysql> explain analyze SELECT * FROM test
    ->   WHERE last_name='Jones'
    ->   AND (first_name='John' OR first_name='Jon')\G
*************************** 1. row ***************************
EXPLAIN: -> Filter: ((test.last_name = 'Jones') and ((test.first_name = 'John') or (test.first_name = 'Jon')))  (cost=0.35 rows=1) (actual time=0.0152..0.0152 rows=0 loops=1)
    -> Covering index scan on test using name  (cost=0.35 rows=1) (actual time=0.0144..0.0144 rows=0 loops=1)

1 row in set (0.00 sec)

mysql> explain analyze SELECT * FROM test
    ->   WHERE last_name='Jones'
    ->   AND first_name >='M' AND first_name < 'N'\G
*************************** 1. row ***************************
EXPLAIN: -> Filter: ((test.last_name = 'Jones') and (test.first_name >= 'M') and (test.first_name < 'N'))  (cost=0.35 rows=1) (actual time=0.0197..0.0197 rows=0 loops=1)
    -> Covering index scan on test using name  (cost=0.35 rows=1) (actual time=0.0189..0.0189 rows=0 loops=1)

1 row in set (0.00 sec)
```
name インデックスは次のクエリーでのルックアップには使用されません。
```sql
mysql> explain analyze SELECT * FROM test WHERE first_name='John'\G
*************************** 1. row ***************************
EXPLAIN: -> Filter: (test.first_name = 'John')  (cost=0.35 rows=1) (actual time=0.0225..0.0225 rows=0 loops=1)
    -> Covering index scan on test using name  (cost=0.35 rows=1) (actual time=0.022..0.022 rows=0 loops=1)

1 row in set (0.00 sec)

mysql> explain analyze SELECT * FROM test
    ->   WHERE last_name='Jones' OR first_name='John'\G
*************************** 1. row ***************************
EXPLAIN: -> Filter: ((test.last_name = 'Jones') or (test.first_name = 'John'))  (cost=0.35 rows=1) (actual time=0.0233..0.0233 rows=0 loops=1)
    -> Covering index scan on test using name  (cost=0.35 rows=1) (actual time=0.0219..0.0219 rows=0 loops=1)

1 row in set (0.00 sec)
```
オプティマイザは、インデックスの左端のプリフィクスを使用して行をルックアップできます。 たとえば、(col1, col2, col3) に 3 カラムのインデックスがある場合、(col1)、(col1, col2)、および (col1, col2, col3) に対して、インデックス検索機能を使用できます。

### 8.3.10 インデックス拡張の使用
InnoDB は、自動的に各セカンダリインデックスに主キーカラムを追加して、それを拡張します。

Setup
```sql
mysql> CREATE TABLE t1 (
    ->   i1 INT NOT NULL DEFAULT 0,
    ->   i2 INT NOT NULL DEFAULT 0,
    ->   d DATE DEFAULT NULL,
    ->   PRIMARY KEY (i1, i2),
    ->   INDEX k_d (d)
    -> ) ENGINE = InnoDB;
Query OK, 0 rows affected (0.05 sec)

mysql> INSERT INTO t1 VALUES
    -> (1, 1, '1998-01-01'), (1, 2, '1999-01-01'),
    -> (1, 3, '2000-01-01'), (1, 4, '2001-01-01'),
    -> (1, 5, '2002-01-01'), (2, 1, '1998-01-01'),
    -> (2, 2, '1999-01-01'), (2, 3, '2000-01-01'),
    -> (2, 4, '2001-01-01'), (2, 5, '2002-01-01'),
    -> (3, 1, '1998-01-01'), (3, 2, '1999-01-01'),
    -> (3, 3, '2000-01-01'), (3, 4, '2001-01-01'),
    -> (3, 5, '2002-01-01'), (4, 1, '1998-01-01'),
    -> (4, 2, '1999-01-01'), (4, 3, '2000-01-01'),
    -> (4, 4, '2001-01-01'), (4, 5, '2002-01-01'),
    -> (5, 1, '1998-01-01'), (5, 2, '1999-01-01'),
    -> (5, 3, '2000-01-01'), (5, 4, '2001-01-01'),
    -> (5, 5, '2002-01-01');
Query OK, 25 rows affected (0.01 sec)
Records: 25  Duplicates: 0  Warnings: 0
```
実行計画は、拡張インデックスが使用されているかどうかによって異なります。  

```sql
mysql> EXPLAIN SELECT COUNT(*) FROM t1 WHERE i1 = 3 AND d = '2000-01-01'\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: t1
   partitions: NULL
         type: ref
possible_keys: PRIMARY,k_d
          key: k_d
      key_len: 8
          ref: const,const
         rows: 1
     filtered: 100.00
        Extra: Using index
1 row in set, 1 warning (0.00 sec)
```

```sql
mysql> FLUSH TABLE t1;
Query OK, 0 rows affected (0.00 sec)

mysql> FLUSH STATUS;
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT COUNT(*) FROM t1 WHERE i1 = 3 AND d = '2000-01-01';
+----------+
| COUNT(*) |
+----------+
|        1 |
+----------+
1 row in set (0.00 sec)

mysql> SHOW STATUS LIKE 'handler_read%'
    -> ;
+-----------------------+-------+
| Variable_name         | Value |
+-----------------------+-------+
| Handler_read_first    | 0     |
| Handler_read_key      | 4     |
| Handler_read_last     | 0     |
| Handler_read_next     | 1     |
| Handler_read_prev     | 0     |
| Handler_read_rnd      | 0     |
| Handler_read_rnd_next | 0     |
+-----------------------+-------+
7 rows in set (0.01 sec)
```
SHOW STATUSでインデックス拡張が使用されているか確認できる  
下記設定でインデックス拡張の設定切り替えができる
```sql
SET optimizer_switch = 'use_index_extensions=off';
```

### 8.3.11 生成されたカラムインデックスのオプティマイザによる使用
MySQL では、生成されたカラムのインデックスがサポートされます。

```sql
mysql> CREATE TABLE t1 (f1 INT, gc INT AS (f1 + 1) STORED, INDEX (gc));
Query OK, 0 rows affected (0.04 sec)
```

```sql
mysql> explain analyze SELECT * FROM t1 WHERE gc > 9\G
*************************** 1. row ***************************
EXPLAIN: -> Index range scan on t1 using gc over (9 < gc), with index condition: (t1.gc > 9)  (cost=0.71 rows=1) (actual time=0.019..0.019 rows=0 loops=1)

1 row in set (0.00 sec)

mysql> explain analyze SELECT * FROM t1 WHERE f1 + 1 > 9\G
*************************** 1. row ***************************
EXPLAIN: -> Index range scan on t1 using gc over (9 < gc), with index condition: (t1.gc > 9)  (cost=0.71 rows=1) (actual time=0.0235..0.0235 rows=0 loops=1)

1 row in set (0.00 sec)
```

### 8.3.12 不可視のインデックス
MySQL では、不可視のインデックス (オプティマイザで使用されないインデックス) がサポートされています。

```sql
mysql> CREATE TABLE t1 (
    ->   i INT,
    ->   j INT,
    ->   k INT,
    ->   INDEX i_idx (i) INVISIBLE
    -> ) ENGINE = InnoDB;
Query OK, 0 rows affected (0.04 sec)

mysql> CREATE INDEX j_idx ON t1 (j) INVISIBLE;
Query OK, 0 rows affected (0.03 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE t1 ADD INDEX k_idx (k) INVISIBLE;
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
既存のインデックスの可視性を変更するには、ALTER TABLE ... ALTER INDEX 操作で VISIBLE または INVISIBLE キーワードを使用します
```sql
mysql> ALTER TABLE t1 ALTER INDEX i_idx INVISIBLE;
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE t1 ALTER INDEX i_idx VISIBLE;
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
インデックスの確認
```sql
mysql> SELECT INDEX_NAME, IS_VISIBLE
    ->        FROM INFORMATION_SCHEMA.STATISTICS
    ->        WHERE TABLE_SCHEMA = 'sakila' AND TABLE_NAME = 't1';
+------------+------------+
| INDEX_NAME | IS_VISIBLE |
+------------+------------+
| i_idx      | YES        |
| j_idx      | NO         |
| k_idx      | NO         |
+------------+------------+
3 rows in set (0.00 sec)
```
不可視のインデックスを使用すると、インデックスが必要になった場合に元に戻す必要がある破壊的な変更を行わずに、クエリーのパフォーマンスに対するインデックスの削除の影響をテストできます。


オプティマイザヒントを使用して一時的に optimizer_switch の値を更新すると、次のように、単一のクエリーの間のみ不可視インデックスを有効にできます
```sql
mysql> EXPLAIN SELECT /*+ SET_VAR(optimizer_switch = 'use_invisible_indexes=on') */
    ->      i, j FROM t1 WHERE j >= 50\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: t1
   partitions: NULL
         type: range
possible_keys: j_idx
          key: j_idx
      key_len: 5
          ref: NULL
         rows: 1
     filtered: 100.00
        Extra: Using index condition
1 row in set, 1 warning (0.00 sec)

mysql> EXPLAIN SELECT i, j FROM t1 WHERE j >= 50\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: t1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 1
     filtered: 100.00
        Extra: Using where
1 row in set, 1 warning (0.00 sec)
```

```sql
mysql> CREATE TABLE t2 (
    ->   i INT NOT NULL,
    ->   j INT NOT NULL,
    ->   UNIQUE j_idx (j)
    -> ) ENGINE = InnoDB;
Query OK, 0 rows affected (0.02 sec)
```
NOT NULL カラム j のインデックスは主キーと同じ制約を行に設定し、非表示にすることはできません:

```sql
mysql> ALTER TABLE t2 ALTER INDEX j_idx INVISIBLE;
ERROR 3522 (HY000): A primary key index cannot be invisible
```
明示的な主キーは非表示にできません。 また、j の一意インデックスは暗黙的主キーとして機能しなくなり、その結果非表示になる可能性があります
```sql
mysql> ALTER TABLE t2 ADD PRIMARY KEY (i);
Query OK, 0 rows affected (0.06 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE t2 ALTER INDEX j_idx INVISIBLE;
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

### 8.3.13 降順インデックス
MySQL は降順インデックスをサポートしています

```sql
mysql> CREATE TABLE t (
    ->   c1 INT, c2 INT,
    ->   INDEX idx1 (c1 ASC, c2 ASC),
    ->   INDEX idx2 (c1 ASC, c2 DESC),
    ->   INDEX idx3 (c1 DESC, c2 ASC),
    ->   INDEX idx4 (c1 DESC, c2 DESC)
    -> );
Query OK, 0 rows affected (0.05 sec)
```

### 8.3.14 TIMESTAMP カラムからのインデックス付きルックアップ
一時値は UTC 値として TIMESTAMP カラムに格納され、TIMESTAMP カラムに対して挿入および取得された値はセッションタイムゾーンと UTC の間で変換されます。

```sql
mysql> CREATE TABLE tstable (ts TIMESTAMP);
Query OK, 0 rows affected (0.03 sec)

mysql> INSERT INTO tstable VALUES
    ->        ('2018-10-28 00:30:00'),
    ->        ('2018-10-28 01:30:00');
Query OK, 2 rows affected (0.01 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql> SELECT ts FROM tstable;
+---------------------+
| ts                  |
+---------------------+
| 2018-10-28 00:30:00 |
| 2018-10-28 01:30:00 |
+---------------------+
2 rows in set (0.00 sec)
```

```sql
mysql> SET time_zone = '+10:00';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT ts FROM tstable;
+---------------------+
| ts                  |
+---------------------+
| 2018-10-28 01:30:00 |
| 2018-10-28 02:30:00 |
+---------------------+
2 rows in set (0.00 sec)
```
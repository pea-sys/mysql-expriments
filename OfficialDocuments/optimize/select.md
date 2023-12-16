## 8.2 SQL ステートメントの最適化
### 8.2.1.1 WHERE 句の最適化
不要なかっこの削除:
```sql
mysql> explain analyze select * from actor where actor_id = 1 or actor_id = 3 or actor_id = 5;
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| EXPLAIN                                                                                                                                                                                                                                                                                                                 |
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| -> Filter: ((actor.actor_id = 1) or (actor.actor_id = 3) or (actor.actor_id = 5))  (cost=1.36 rows=3) (actual time=0.0791..0.101 rows=3 loops=1)
    -> Index range scan on actor using PRIMARY over (actor_id = 1) OR (actor_id = 3) OR (actor_id = 5)  (cost=1.36 rows=3) (actual time=0.0766..0.096 rows=3 loops=1)
 |
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)
```

```sql
mysql> explain analyze select * from actor where actor_id = 1 or actor_id = 3 or actor_id = 5 or actor_id = 1 or actor_id = 3 or actor_id = 5;
+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| EXPLAIN


                            |
+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| -> Filter: ((actor.actor_id = 1) or (actor.actor_id = 3) or (actor.actor_id = 5) or (actor.actor_id = 1) or (actor.actor_id = 3) or (actor.actor_id = 5))  (cost=1.36 rows=3) (actual time=0.0373..0.0474 rows=3 loops=1)
    -> Index range scan on actor using PRIMARY over (actor_id = 1) OR (actor_id = 3) OR (actor_id = 5)  (cost=1.36 rows=3) (actual time=0.0355..0.0446 rows=3 loops=1)
 |
+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)
```
定数畳み込み
```sql
mysql> explain analyze SELECT * FROM sakila.address where address_id < city_id and city_id = 300;
+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| EXPLAIN

 |
+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| -> Index range scan on address using idx_fk_city_id over (city_id = 300 AND address_id < 300), with index condition: ((address.city_id = 300) and (address.address_id < 300))  (cost=1.16 rows=2) (actual time=0.0544..0.0693 rows=2 loops=1)
 |
+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)
```
一定条件の削除
```sql
mysql> explain analyze SELECT * FROM sakila.address where 300 = 300;
+-------------------------------------------------------------------------------------------+
| EXPLAIN                                                                                   |
+-------------------------------------------------------------------------------------------+
| -> Table scan on address  (cost=0.35 rows=1) (actual time=0.0501..1.08 rows=603 loops=1)
 |
+-------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)
```
きわめて高速なクエリーのいくつかの例
```sql
mysql> SELECT COUNT(*) FROM actor;
+----------+
| COUNT(*) |
+----------+
|      200 |
+----------+
1 row in set (0.00 sec)

mysql> SELECT min(actor_id) FROM actor;
+---------------+
| min(actor_id) |
+---------------+
|             1 |
+---------------+
1 row in set (0.00 sec)

mysql> SELECT min(actor_id), max(actor_id) FROM actor;
+---------------+---------------+
| min(actor_id) | max(actor_id) |
+---------------+---------------+
|             1 |           200 |
+---------------+---------------+
1 row in set (0.00 sec)
mysql> SELECT max(address_id) FROM sakila.address where city_id = 300;
+-----------------+
| max(address_id) |
+-----------------+
|               3 |
+-----------------+
1 row in set (0.00 sec)
```

### 8.2.1.2 range の最適化
スキャン範囲アクセス方法のスキップ  
次のシナリオを考えてみます
```sql
mysql> use world;
Database changed
mysql> CREATE TABLE t1 (f1 INT NOT NULL, f2 INT NOT NULL, PRIMARY KEY(f1, f2));
Query OK, 0 rows affected (0.06 sec)

mysql> INSERT INTO t1 VALUES
    ->   (1,1), (1,2), (1,3), (1,4), (1,5),
    ->   (2,1), (2,2), (2,3), (2,4), (2,5);
Query OK, 10 rows affected (0.01 sec)
Records: 10  Duplicates: 0  Warnings: 0

mysql> INSERT INTO t1 SELECT f1, f2 + 5 FROM t1;
Query OK, 10 rows affected (0.01 sec)
Records: 10  Duplicates: 0  Warnings: 0

mysql> INSERT INTO t1 SELECT f1, f2 + 10 FROM t1;
Query OK, 20 rows affected (0.00 sec)
Records: 20  Duplicates: 0  Warnings: 0

mysql> INSERT INTO t1 SELECT f1, f2 + 20 FROM t1;
Query OK, 40 rows affected (0.00 sec)
Records: 40  Duplicates: 0  Warnings: 0

mysql> INSERT INTO t1 SELECT f1, f2 + 40 FROM t1;
Query OK, 80 rows affected (0.00 sec)
Records: 80  Duplicates: 0  Warnings: 0

mysql> ANALYZE TABLE t1;
+----------+---------+----------+----------+
| Table    | Op      | Msg_type | Msg_text |
+----------+---------+----------+----------+
| world.t1 | analyze | status   | OK       |
+----------+---------+----------+----------+
1 row in set (0.01 sec)

mysql>
mysql> EXPLAIN SELECT f1, f2 FROM t1 WHERE f2 > 40;
+----+-------------+-------+------------+-------+---------------+---------+---------+------+------+----------+----------------------------------------+
| id | select_type | table | partitions | type  | possible_keys | key     | key_len | ref  | rows | filtered | Extra                                  |
+----+-------------+-------+------------+-------+---------------+---------+---------+------+------+----------+----------------------------------------+
|  1 | SIMPLE      | t1    | NULL       | range | PRIMARY       | PRIMARY | 8       | NULL |   53 |   100.00 | Using where; Using index for skip scan |
+----+-------------+-------+------------+-------+---------------+---------+---------+------+------+----------+----------------------------------------+
1 row in set, 1 warning (0.01 sec)
```
スキップスキャンが使用されています  
範囲オプティマイザで使用可能なメモリーを制御するには、range_optimizer_max_mem_size システム変数を使用します:

### 8.2.1.4 ハッシュ結合の最適化
各結合に等価結合条件があり、結合条件に適用できるインデックスがないクエリーに対してハッシュ結合が使用されます:  

セットアップ
```sql
mysql> CREATE TABLE t1 (c1 INT, c2 INT);
Query OK, 0 rows affected (0.05 sec)

mysql> CREATE TABLE t2 (c1 INT, c2 INT);
Query OK, 0 rows affected (0.02 sec)

mysql> CREATE TABLE t3 (c1 INT, c2 INT);
Query OK, 0 rows affected (0.02 sec)
```
ハッシュジョインの例
```sql
mysql> explain analyze SELECT *
    ->     FROM t1
    ->     JOIN t2
    ->         ON t1.c1=t2.c1\G
*************************** 1. row ***************************
EXPLAIN: -> Inner hash join (t2.c1 = t1.c1)  (cost=0.7 rows=1) (actual time=0.0308..0.0308 rows=0 loops=1)
    -> Table scan on t2  (cost=0.35 rows=1) (never executed)
    -> Hash
        -> Table scan on t1  (cost=0.35 rows=1) (actual time=0.0216..0.0216 rows=0 loops=1)

1 row in set (0.00 sec)
```
等価結合ではない例
```sql
mysql> explain analyze SELECT * FROM t1
    ->     JOIN t2 ON (t1.c1 = t2.c1 AND t1.c2 < t2.c2)
    ->     JOIN t3 ON (t2.c1 = t3.c1)\G
*************************** 1. row ***************************
EXPLAIN: -> Inner hash join (t3.c1 = t1.c1)  (cost=1.05 rows=1) (actual time=0.0325..0.0325 rows=0 loops=1)
    -> Table scan on t3  (cost=0.35 rows=1) (never executed)
    -> Hash
        -> Filter: (t1.c2 < t2.c2)  (cost=0.7 rows=1) (actual time=0.0248..0.0248 rows=0 loops=1)
            -> Inner hash join (t2.c1 = t1.c1)  (cost=0.7 rows=1) (actual time=0.024..0.024 rows=0 loops=1)
                -> Table scan on t2  (cost=0.35 rows=1) (never executed)
                -> Hash
                    -> Table scan on t1  (cost=0.35 rows=1) (actual time=0.0132..0.0132 rows=0 loops=1)

1 row in set (0.00 sec)
```
ハッシュ結合はデカルト積にも適用されます
```sql
mysql> EXPLAIN FORMAT=TREE SELECT *  FROM t1 JOIN t2 WHERE t1.c2 > 50\G
*************************** 1. row ***************************
EXPLAIN: -> Inner hash join (no condition)  (cost=0.7 rows=1)
    -> Table scan on t2  (cost=0.35 rows=1)
    -> Hash
        -> Filter: (t1.c2 > 50)  (cost=0.35 rows=1)
            -> Table scan on t1  (cost=0.35 rows=1)

1 row in set (0.00 sec)
```
等価結合を含まない例
```sql
mysql> EXPLAIN FORMAT=TREE SELECT * FROM t1 JOIN t2 ON t1.c1 < t2.c1\G
*************************** 1. row ***************************
EXPLAIN: -> Filter: (t1.c1 < t2.c1)  (cost=0.7 rows=1)
    -> Inner hash join (no condition)  (cost=0.7 rows=1)
        -> Table scan on t2  (cost=0.35 rows=1)
        -> Hash
            -> Table scan on t1  (cost=0.35 rows=1)

1 row in set (0.00 sec)
```
準結合
```sql
mysql> EXPLAIN FORMAT=TREE SELECT * FROM t1 WHERE t1.c1 IN (SELECT t2.c2 FROM t2)\G
*************************** 1. row ***************************
EXPLAIN: -> Hash semijoin (t2.c2 = t1.c1)  (cost=0.7 rows=1)
    -> Table scan on t1  (cost=0.35 rows=1)
    -> Hash
        -> Table scan on t2  (cost=0.35 rows=1)

1 row in set (0.00 sec)
```
アンチ結合
```sql
mysql> EXPLAIN FORMAT=TREE SELECT * FROM t2 WHERE NOT EXISTS (SELECT * FROM t1 WHERE t1.c1 = t2.c1)\G
*************************** 1. row ***************************
EXPLAIN: -> Hash antijoin (t1.c1 = t2.c1)  (cost=0.7 rows=1)
    -> Table scan on t2  (cost=0.35 rows=1)
    -> Hash
        -> Table scan on t1  (cost=0.35 rows=1)

1 row in set, 1 warning (0.00 sec)
```
左外部結合
```sql
mysql> EXPLAIN FORMAT=TREE SELECT * FROM t1 LEFT JOIN t2 ON t1.c1 = t2.c1\G
*************************** 1. row ***************************
EXPLAIN: -> Left hash join (t2.c1 = t1.c1)  (cost=0.7 rows=1)
    -> Table scan on t1  (cost=0.35 rows=1)
    -> Hash
        -> Table scan on t2  (cost=0.35 rows=1)

1 row in set (0.00 sec)
```

### 8.2.1.8 ネストした結合の最適化
結合を表す構文では、ネストした結合を使用できます。 次の説明は、セクション13.2.10.2「JOIN 句」に説明する結合構文について言及しています。

セットアップ
```sql
mysql> CREATE TABLE t1 (a INT, b INT, c INT);
Query OK, 0 rows affected (0.02 sec)

mysql> CREATE TABLE t2 (a INT, b INT, c INT);
Query OK, 0 rows affected (0.02 sec)

mysql> CREATE TABLE t3 (a INT, b INT, c INT);
Query OK, 0 rows affected (0.02 sec)
```

```
mysql> EXPLAIN ANALYZE SELECT * FROM t1 LEFT JOIN (t2 CROSS JOIN t3 CROSS JOIN t4)
    -> ON (t2.a=t1.a AND t3.b=t1.b AND t4.c=t1.c)\G
*************************** 1. row ***************************
EXPLAIN: -> Left hash join (t2.a = t1.a), (t3.b = t1.b), (t4.c = t1.c)  (cost=1.4 rows=1) (actual time=0.0291..0.0291 rows=0 loops=1)
    -> Table scan on t1  (cost=0.35 rows=1) (actual time=0.0218..0.0218 rows=0 loops=1)
    -> Hash
        -> Inner hash join (no condition)  (cost=1.05 rows=1) (never executed)
            -> Table scan on t4  (cost=0.35 rows=1) (never executed)
            -> Hash
                -> Inner hash join (no condition)  (cost=0.7 rows=1) (never executed)
                    -> Table scan on t3  (cost=0.35 rows=1) (never executed)
                    -> Hash
                        -> Table scan on t2  (cost=0.35 rows=1) (never executed)

1 row in set (0.00 sec)
```

```sql
mysql> explain analyze SELECT * FROM t1 LEFT JOIN (t2 LEFT JOIN t3 ON t2.b=t3.b) ON t1.a=t2.a
    ->   WHERE t1.a > 1\G
*************************** 1. row ***************************
EXPLAIN: -> Left hash join (t2.a = t1.a)  (cost=1.05 rows=1) (actual time=0.0281..0.0281 rows=0 loops=1)
    -> Filter: (t1.a > 1)  (cost=0.35 rows=1) (actual time=0.0215..0.0215 rows=0 loops=1)
        -> Table scan on t1  (cost=0.35 rows=1) (actual time=0.0208..0.0208 rows=0 loops=1)
    -> Hash
        -> Left hash join (t3.b = t2.b)  (cost=0.7 rows=1) (never executed)
            -> Table scan on t2  (cost=0.35 rows=1) (never executed)
            -> Hash
                -> Table scan on t3  (cost=0.35 rows=1) (never executed)

1 row in set (0.00 sec)
```
### 8.2.1.14 定数 - フォールディングの最適化
定数値が範囲外であるか、カラムタイプに関して間違ったタイプである定数とカラム値の比較は、クエリーの最適化中に、実行中ではなく行単位で処理されるようになりました。

次のステートメントで作成されたテーブルについて考えてみます
```sql
CREATE TABLE t (c TINYINT UNSIGNED NOT NULL);
```
折りたたみ
```sql
mysql> EXPLAIN SELECT * FROM t WHERE c >= 255;
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | t     | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    1 |   100.00 | Using where |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)
mysql> SHOW WARNINGS;
+-------+------+------------------------------------------------------------------------------------------------+
| Level | Code | Message                                                                                        |
+-------+------+------------------------------------------------------------------------------------------------+
| Note  | 1003 | /* select#1 */ select `sakila`.`t`.`c` AS `c` from `sakila`.`t` where (`sakila`.`t`.`c` = 255) |
+-------+------+------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)

```

### 8.2.1.16 ORDER BY の最適化
このクエリーでは、(key_part1, key_part2) のインデックスにより、オプティマイザはソートを回避できます:
```sql
mysql> explain analyze select * from film_category order by film_id,category_id;
+-------------------------------------------------------------------------------------------------------------+
| EXPLAIN                                                                                                     |
+-------------------------------------------------------------------------------------------------------------+
| -> Index scan on film_category using PRIMARY  (cost=1.1 rows=1) (actual time=11.2..12.7 rows=1000 loops=1)
 |
+-------------------------------------------------------------------------------------------------------------+
1 row in set (0.02 sec)
```
テーブルスキャンよりも安価なインデックスレンジスキャンを行うことができる場合、(key_part1, key_part2) のインデックスはソートを回避します:
```sql
mysql> explain analyze select * from film_category where film_id = 20 order by category_id\G
*************************** 1. row ***************************
EXPLAIN: -> Index lookup on film_category using PRIMARY (film_id=20)  (cost=0.35 rows=1) (actual time=0.0212..0.0264 rows=1 loops=1)

1 row in set (0.00 sec)
```
次の 2 つのクエリーでは、key_part1 が定数と比較されます。 インデックスは、テーブルスキャンよりもインデックスレンジスキャンの方が安くなるように、WHERE 句が選択的である場合に使用されます:
```sql
mysql> explain analyze SELECT * FROM film_category  WHERE film_id > 20  ORDER BY film_id ASC;
+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| EXPLAIN
                                                                                                       |
+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| -> Filter: (film_category.film_id > 20)  (cost=0.35 rows=1) (actual time=0.0381..0.594 rows=980 loops=1)
    -> Index scan on film_category using PRIMARY  (cost=0.35 rows=1) (actual time=0.0189..0.522 rows=1000 loops=1)
 |
+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)

mysql> explain analyze SELECT * FROM film_category  WHERE film_id < 20  ORDER BY film_id DESC;
+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| EXPLAIN

                |
+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| -> Filter: (film_category.film_id < 20)  (cost=4.06 rows=19) (actual time=0.271..0.281 rows=19 loops=1)
    -> Index range scan on film_category using PRIMARY over (film_id < 20) (reverse)  (cost=4.06 rows=19) (actual time=0.269..0.277 rows=19 loops=1)
 |
+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)
```

### 8.2.1.17 GROUP BY の最適化
 Loose Index Scan アクセス方法は、次のクエリーに使用できます:
```sql
mysql> explain analyze SELECT film_id, category_id FROM film_category group by film_id , category_id\G
*************************** 1. row ***************************
EXPLAIN: -> Covering index skip scan for deduplication on film_category using PRIMARY  (cost=0.65 rows=2) (actual time=0.0365..4.95 rows=1000 loops=1)

1 row in set (0.01 sec)

mysql> explain analyze select film_id, category_id from film_category\G
*************************** 1. row ***************************
EXPLAIN: -> Covering index scan on film_category using fk_film_category_category  (cost=0.35 rows=1) (actual time=0.0394..0.423 rows=1000 loops=1)

1 row in set (0.00 sec)

mysql> explain analyze select film_id, min(category_id) from film_category group by film_id\G
*************************** 1. row ***************************
EXPLAIN: -> Group aggregate: min(film_category.category_id)  (cost=0.45 rows=1) (actual time=0.0412..0.662 rows=1000 loops=1)
    -> Covering index scan on film_category using PRIMARY  (cost=0.35 rows=1) (actual time=0.0297..0.471 rows=1000 loops=1)

1 row in set (0.00 sec)
```

### 8.2.1.19 LIMIT クエリーの最適化
結果セットから指定した数の行のみが必要な場合、結果セット全体をフェッチして、余分なデータを破棄するのではなく、クエリーで LIMIT 句を使用します

LIMIT で少数の行のみを選択すると、MySQL では、通常フルテーブルスキャンを実行するより望ましい特定の場合に、インデックスが使用されます。
```sql
mysql> explain analyze select film_id from film_category limit 3\G
*************************** 1. row ***************************
EXPLAIN: -> Limit: 3 row(s)  (cost=0.35 rows=1) (actual time=0.0422..0.0511 rows=3 loops=1)
    -> Covering index scan on film_category using fk_film_category_category  (cost=0.35 rows=1) (actual time=0.0402..0.0488 rows=3 loops=1)

1 row in set (0.00 sec)
```
例
```sql
mysql> CREATE TABLE t (
    ->          id1 BIGINT NOT NULL,
    ->          id2 BIGINT NOT NULL,
    ->          c1 VARCHAR(50) NOT NULL,
    ->          c2 VARCHAR(50) NOT NULL,
    ->       PRIMARY KEY (id1),
    ->       INDEX i (id2, c1)
    ->      );
Query OK, 0 rows affected (0.06 sec)

mysql> SELECT @@optimizer_switch LIKE '%prefer_ordering_index=on%';
+------------------------------------------------------+
| @@optimizer_switch LIKE '%prefer_ordering_index=on%' |
+------------------------------------------------------+
|                                                    1 |
+------------------------------------------------------+
1 row in set (0.00 sec)
```
次のクエリーには LIMIT 句があるため、可能な場合は順序付けされたインデックスを使用する必要があります。 この場合、EXPLAIN 出力からわかるように、テーブルの主キーが使用されます。
```sql
mysql> EXPLAIN SELECT c2 FROM t
    ->          WHERE id2 > 3
    ->          ORDER BY id1 ASC LIMIT 2\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: t
   partitions: NULL
         type: index
possible_keys: i
          key: PRIMARY
      key_len: 8
          ref: NULL
         rows: 1
     filtered: 100.00
        Extra: Using where
1 row in set, 1 warning (0.00 sec)
```
次に、prefer_ordering_index フラグを無効にし、同じクエリーを再実行します。
```sql
mysql> EXPLAIN SELECT c2 FROM t
    ->          WHERE id2 > 3
    ->          ORDER BY id1 ASC LIMIT 2\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: t
   partitions: NULL
         type: range
possible_keys: i
          key: i
      key_len: 8
          ref: NULL
         rows: 1
     filtered: 100.00
        Extra: Using index condition; Using filesort
1 row in set, 1 warning (0.00 sec)
```

### 8.2.1.20 関数コールの最適化
MySQL 関数は、内部的に決定論的または非決定的としてタグ付けされます

両方のクエリーで主キー参照が使用されているように見えますが、これは最初のクエリーにのみ当てはまります
```sql
mysql> explain analyze SELECT * FROM t WHERE id = POW(1,2)\G
*************************** 1. row ***************************
EXPLAIN: -> Zero rows (no matching row in const table)  (cost=0..0 rows=0) (actual time=100e-6..100e-6 rows=0 loops=1)

1 row in set (0.02 sec)

mysql> explain analyze SELECT * FROM t WHERE id = FLOOR(1 + RAND() * 49)\G
*************************** 1. row ***************************
EXPLAIN: -> Filter: (t.id = floor((1 + (rand() * 49))))  (cost=0.35 rows=1) (actual time=0.0371..0.0371 rows=0 loops=1)
    -> Table scan on t  (cost=0.35 rows=1) (actual time=0.0362..0.0362 rows=0 loops=1)

1 row in set (0.01 sec)
```
RNADはすべての行に対して実行されるため、次のようにすると決定論的になる
```sql
mysql> SET @keyval = FLOOR(1 + RAND() * 49);
mysql> explain analyze SELECT * FROM t WHERE id  = @keyval\G
*************************** 1. row ***************************
EXPLAIN: -> Zero rows (no matching row in const table)  (cost=0..0 rows=0) (actual time=300e-6..300e-6 rows=0 loops=1)

1 row in set (0.00 sec)
```

### 8.2.1.22 行コンストラクタ式の最適化
行コンストラクタを使用すると、複数の値を同時に比較できます。

```sql
mysql> CREATE TABLE t1 (
    ->   c1 INT, c2 INT, c3 INT, c4 CHAR(100),
    ->   PRIMARY KEY(c1,c2,c3)
    -> );
Query OK, 0 rows affected (0.02 sec)
```
このクエリーでは、WHERE 句はインデックス内のすべてのカラムを使用します。 ただし、行コンストラクタ自体はインデックス接頭辞をカバーしません。その結果、オプティマイザは c1 (key_len=4、つまり c1 のサイズ) のみを使用します
```sql
mysql> EXPLAIN SELECT * FROM t1
    ->        WHERE c1=1 AND (c2,c3) > (1,1)\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: t1
   partitions: NULL
         type: ref
possible_keys: PRIMARY
          key: PRIMARY
      key_len: 4
          ref: const
         rows: 1
     filtered: 100.00
        Extra: Using where
1 row in set, 1 warning (0.01 sec)
```
非コンストラクタ式を使用するようにクエリーをリライトすると、オプティマイザはインデックス内の 3 つのカラムすべて (key_len=12) を使用します
```sql
mysql> EXPLAIN SELECT * FROM t1
    ->        WHERE c1 = 1 AND (c2 > 1 OR ((c2 = 1) AND (c3 > 1)))\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: t1
   partitions: NULL
         type: range
possible_keys: PRIMARY
          key: PRIMARY
      key_len: 12
          ref: NULL
         rows: 2
     filtered: 100.00
        Extra: Using where
1 row in set, 1 warning (0.00 sec)
```
### 8.2.1.23 全テーブルスキャンの回避
* ANALYZE TABLE tbl_name を使用して、スキャンされるテーブルのキー分布を更新します。
* スキャンされるテーブルに FORCE INDEX を使用して、MySQL に、テーブルスキャンは指定したインデックスを使用するのと比較して著しく負荷が大きいことを伝えます。

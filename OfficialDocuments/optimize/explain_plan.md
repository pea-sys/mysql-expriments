### 8.8.3 拡張 EXPLAIN 出力形式
EXPLAIN ステートメントは、EXPLAIN 出力の一部ではないが、EXPLAIN の後に SHOW WARNINGS ステートメントを発行することで表示できる追加 (「extended」) 情報を生成します。

```sql
mysql> explain select actor.actor_id, actor.first_name IN (SELECT film_actor.actor_id FROM film_actor) FROM actor\G
*************************** 1. row ***************************
           id: 1
  select_type: PRIMARY
        table: actor
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 200
     filtered: 100.00
        Extra: NULL
*************************** 2. row ***************************
           id: 2
  select_type: DEPENDENT SUBQUERY
        table: film_actor
   partitions: NULL
         type: index_subquery
possible_keys: PRIMARY
          key: PRIMARY
      key_len: 2
          ref: func
         rows: 27
     filtered: 100.00
        Extra: Using where; Using index
2 rows in set, 1 warning (0.03 sec)

mysql> SHOW WARNINGS\G
*************************** 1. row ***************************
  Level: Note
   Code: 1003
Message: /* select#1 */ select `sakila`.`actor`.`actor_id` AS `actor_id`,<in_optimizer>(`sakila`.`actor`.`first_name`,<exists>(<index_lookup>(<cache>(`sakila`.`actor`.`first_name`) in film_actor on PRIMARY where (<cache>(`sakila`.`actor`.`first_name`) = `sakila`.`film_actor`.`actor_id`)))) AS `actor.first_name IN (SELECT film_actor.actor_id FROM film_actor)` from `sakila`.`actor`
1 row in set (0.00 sec)

```

### 8.8.4 名前付き接続の実行計画情報の取得
名前付き接続で実行されている説明可能なステートメントの実行計画を取得できます
```sql
mysql> EXPLAIN FOR CONNECTION (SELECT CONNECTION_ID());
ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near '(SELECT CONNECTION_ID())' at line 1
mysql> EXPLAIN FOR CONNECTION 26;
ERROR 3012 (HY000): EXPLAIN FOR CONNECTION command is supported only for SELECT/UPDATE/INSERT/DELETE/REPLACE
```

### 8.9.2 切り替え可能な最適化
optimizer_switch システム変数を使用するとオプティマイザの動作を制御できます。

```sql
mysql> SELECT @@optimizer_switch\G
*************************** 1. row ***************************
@@optimizer_switch: index_merge=on,index_merge_union=on,index_merge_sort_union=on,index_merge_intersection=on,engine_condition_pushdown=on,index_condition_pushdown=on,mrr=on,mrr_cost_based=on,block_nested_loop=on,batched_key_access=off,materialization=on,semijoin=on,loosescan=on,firstmatch=on,duplicateweedout=on,subquery_materialization_cost_based=on,use_index_extensions=on,condition_fanout_filter=on,derived_merge=on,use_invisible_indexes=off,skip_scan=on,hash_join=on,subquery_to_derived=off,prefer_ordering_index=on,hypergraph_optimizer=off,derived_condition_pushdown=on,hash_set_operations=on
1 row in set (0.00 sec)
```

* サブクエリー変換フラグ

    * subquery_to_derived (default off)

        MySQL 8.0.21 以降、オプティマイザは多くの場合、SELECT, WHERE, JOIN または HAVING 句のスカラーサブクエリーを導出テーブルの左外部結合に変換できます。

        ```sql
        mysql> CREATE TABLE t1(a INT);
        Query OK, 0 rows affected (0.03 sec)

        mysql> CREATE TABLE t2(a INT);
        Query OK, 0 rows affected (0.02 sec)

        mysql> INSERT INTO t1 VALUES ROW(1), ROW(2), ROW(3), ROW(4);
        Query OK, 4 rows affected (0.01 sec)
        Records: 4  Duplicates: 0  Warnings: 0

        mysql> INSERT INTO t2 VALUES ROW(1), ROW(2);
        Query OK, 2 rows affected (0.01 sec)
        Records: 2  Duplicates: 0  Warnings: 0

        mysql> SELECT * FROM t1
            ->          WHERE t1.a > (SELECT COUNT(a) FROM t2);
        +------+
        | a    |
        +------+
        |    3 |
        |    4 |
        +------+
        2 rows in set (0.00 sec)

        mysql> SELECT @@optimizer_switch LIKE '%subquery_to_derived=off%';
        +-----------------------------------------------------+
        | @@optimizer_switch LIKE '%subquery_to_derived=off%' |
        +-----------------------------------------------------+
        |                                                   1 |
        +-----------------------------------------------------+
        1 row in set (0.00 sec)

        mysql> EXPLAIN SELECT * FROM t1 WHERE t1.a > (SELECT COUNT(a) FROM t2)\G
        *************************** 1. row ***************************
                id: 1
        select_type: PRIMARY
                table: t1
        partitions: NULL
                type: ALL
        possible_keys: NULL
                key: NULL
            key_len: NULL
                ref: NULL
                rows: 4
            filtered: 33.33
                Extra: Using where
        *************************** 2. row ***************************
                id: 2
        select_type: SUBQUERY
                table: t2
        partitions: NULL
                type: ALL
        possible_keys: NULL
                key: NULL
            key_len: NULL
                ref: NULL
                rows: 2
            filtered: 100.00
                Extra: NULL
        2 rows in set, 1 warning (0.00 sec)

        mysql> SET @@optimizer_switch='subquery_to_derived=on';
        Query OK, 0 rows affected (0.00 sec)
        mysql> SELECT @@optimizer_switch LIKE '%subquery_to_derived=off%';
        +-----------------------------------------------------+
        | @@optimizer_switch LIKE '%subquery_to_derived=off%' |
        +-----------------------------------------------------+
        |                                                   0 |
        +-----------------------------------------------------+
        1 row in set (0.00 sec)

        mysql> SELECT @@optimizer_switch LIKE '%subquery_to_derived=on%';
        +----------------------------------------------------+
        | @@optimizer_switch LIKE '%subquery_to_derived=on%' |
        +----------------------------------------------------+
        |                                                  1 |
        +----------------------------------------------------+
        1 row in set (0.00 sec)
        
        mysql> EXPLAIN SELECT * FROM t1 WHERE t1.a > (SELECT COUNT(a) FROM t2)\G
        *************************** 1. row ***************************
                id: 1
        select_type: PRIMARY
                table: <derived2>
        partitions: NULL
                type: ALL
        possible_keys: NULL
                key: NULL
            key_len: NULL
                ref: NULL
                rows: 1
            filtered: 100.00
                Extra: NULL
        *************************** 2. row ***************************
                id: 1
        select_type: PRIMARY
                table: t1
        partitions: NULL
                type: ALL
        possible_keys: NULL
                key: NULL
            key_len: NULL
                ref: NULL
                rows: 4
            filtered: 33.33
                Extra: Using where; Using join buffer (hash join)
        *************************** 3. row ***************************
                id: 2
        select_type: DERIVED
                table: t2
        partitions: NULL
                type: ALL
        possible_keys: NULL
                key: NULL
            key_len: NULL
                ref: NULL
                rows: 2
            filtered: 100.00
                Extra: NULL
        3 rows in set, 1 warning (0.00 sec)
        ```
        IN (subquery) でクエリーを使用する例:
        ```sql
        mysql> DROP TABLE IF EXISTS t1, t2;
        Query OK, 0 rows affected (0.03 sec)

        mysql> CREATE TABLE t1 (a INT, b INT);
        Query OK, 0 rows affected (0.02 sec)

        mysql> CREATE TABLE t2 (a INT, b INT);
        Query OK, 0 rows affected (0.02 sec)

        mysql> INSERT INTO t1 VALUES ROW(1,10), ROW(2,20), ROW(3,30);
        Query OK, 3 rows affected (0.00 sec)
        Records: 3  Duplicates: 0  Warnings: 0

        mysql> INSERT INTO t2
            ->     VALUES ROW(1,10), ROW(2,20), ROW(3,30), ROW(1,110), ROW(2,120), ROW(3,130);
        Query OK, 6 rows affected (0.00 sec)
        Records: 6  Duplicates: 0  Warnings: 0

        mysql> SELECT * FROM t1
        ->          WHERE   t1.b < 0
        ->                  OR
        ->                  t1.a IN (SELECT t2.a + 1 FROM t2);
        +------+------+
        | a    | b    |
        +------+------+
        |    2 |   20 |
        |    3 |   30 |
        +------+------+
        2 rows in set (0.00 sec)

        mysql> SET @@optimizer_switch="subquery_to_derived=off";
        Query OK, 0 rows affected (0.00 sec)

        mysql> EXPLAIN SELECT * FROM t1
            ->                  WHERE   t1.b < 0
            ->                          OR
            ->                          t1.a IN (SELECT t2.a + 1 FROM t2)\G
        *************************** 1. row ***************************
                id: 1
        select_type: PRIMARY
                table: t1
        partitions: NULL
                type: ALL
        possible_keys: NULL
                key: NULL
            key_len: NULL
                ref: NULL
                rows: 3
            filtered: 100.00
                Extra: Using where
        *************************** 2. row ***************************
                id: 2
        select_type: DEPENDENT SUBQUERY
                table: t2
        partitions: NULL
                type: ALL
        possible_keys: NULL
                key: NULL
            key_len: NULL
                ref: NULL
                rows: 6
            filtered: 100.00
                Extra: Using where
        2 rows in set, 1 warning (0.00 sec)

        mysql> SET @@optimizer_switch="subquery_to_derived=on";
        Query OK, 0 rows affected (0.00 sec)

        mysql> EXPLAIN SELECT * FROM t1
            ->                  WHERE   t1.b < 0
            ->                          OR
            ->                          t1.a IN (SELECT t2.a + 1 FROM t2)\G
        *************************** 1. row ***************************
                id: 1
        select_type: PRIMARY
                table: t1
        partitions: NULL
                type: ALL
        possible_keys: NULL
                key: NULL
            key_len: NULL
                ref: NULL
                rows: 3
            filtered: 100.00
                Extra: NULL
        *************************** 2. row ***************************
                id: 1
        select_type: PRIMARY
                table: <derived2>
        partitions: NULL
                type: ref
        possible_keys: <auto_key0>
                key: <auto_key0>
            key_len: 9
                ref: sakila.t1.a
                rows: 2
            filtered: 100.00
                Extra: Using where; Using index
        *************************** 3. row ***************************
                id: 2
        select_type: DERIVED
                table: t2
        partitions: NULL
                type: ALL
        possible_keys: NULL
                key: NULL
            key_len: NULL
                ref: NULL
                rows: 6
            filtered: 100.00
                Extra: Using temporary
        3 rows in set, 1 warning (0.00 sec)
        ```
        例:EXISTS (subquery) と前述の例と同じテーブルおよびデータを使用したクエリーを使用します
        ```sql
        mysql> SELECT * FROM t1
        ->          WHERE   t1.b < 0
        ->                  OR
        ->                  EXISTS(SELECT * FROM t2 WHERE t2.a = t1.a + 1);
        +------+------+
        | a    | b    |
        +------+------+
        |    1 |   10 |
        |    2 |   20 |
        +------+------+
        2 rows in set (0.00 sec)

        mysql> SET @@optimizer_switch="subquery_to_derived=off";
        Query OK, 0 rows affected (0.00 sec)

        mysql> EXPLAIN SELECT * FROM t1
            ->                  WHERE   t1.b < 0
            ->                          OR
            ->                          EXISTS(SELECT * FROM t2 WHERE t2.a = t1.a + 1)\G
        *************************** 1. row ***************************
                id: 1
        select_type: PRIMARY
                table: t1
        partitions: NULL
                type: ALL
        possible_keys: NULL
                key: NULL
            key_len: NULL
                ref: NULL
                rows: 3
            filtered: 100.00
                Extra: Using where
        *************************** 2. row ***************************
                id: 2
        select_type: DEPENDENT SUBQUERY
                table: t2
        partitions: NULL
                type: ALL
        possible_keys: NULL
                key: NULL
            key_len: NULL
                ref: NULL
                rows: 6
            filtered: 16.67
                Extra: Using where
        2 rows in set, 2 warnings (0.00 sec)

        mysql> SET @@optimizer_switch="subquery_to_derived=on";
        Query OK, 0 rows affected (0.00 sec)

        mysql> EXPLAIN SELECT * FROM t1
            ->                  WHERE   t1.b < 0
            ->                          OR
            ->                          EXISTS(SELECT * FROM t2 WHERE t2.a = t1.a + 1)\G
        *************************** 1. row ***************************
                id: 1
        select_type: PRIMARY
                table: t1
        partitions: NULL
                type: ALL
        possible_keys: NULL
                key: NULL
            key_len: NULL
                ref: NULL
                rows: 3
            filtered: 100.00
                Extra: NULL
        *************************** 2. row ***************************
                id: 1
        select_type: PRIMARY
                table: <derived2>
        partitions: NULL
                type: ALL
        possible_keys: NULL
                key: NULL
            key_len: NULL
                ref: NULL
                rows: 6
            filtered: 100.00
                Extra: Using where; Using join buffer (hash join)
        *************************** 3. row ***************************
                id: 2
        select_type: DERIVED
                table: t2
        partitions: NULL
                type: ALL
        possible_keys: NULL
                key: NULL
            key_len: NULL
                ref: NULL
                rows: 6
            filtered: 100.00
                Extra: Using temporary
        3 rows in set, 2 warnings (0.00 sec)
        ```

### 8.9.3 オプティマイザヒント
オプティマイザヒントは、個々のステートメント内で指定できます。

```sql

mysql> explain analyze SELECT /*+ NO_RANGE_OPTIMIZATION(city_id PRIMARY, country_id) */ city_id
->   FROM city WHERE city_id > 0 AND city_id < 100\G
*************************** 1. row ***************************
EXPLAIN: -> Filter: ((city.city_id > 0) and (city.city_id < 100))  (cost=20.5 rows=99) (actual time=0.0239..0.0961 rows=99 loops=1)
-> Covering index range scan on city using PRIMARY over (0 < city_id < 100)  (cost=20.5 rows=99) (actual time=0.0211..0.0766 rows=99 loops=1)

1 row in set, 2 warnings (0.00 sec)
```

```sql
mysql> explain analyze SELECT /*+ INDEX_MERGE(address idx_fk_city_id, idx_location)*/ * FROM address
    ->   WHERE address_id = 1 AND city_id = 2\G
*************************** 1. row ***************************
EXPLAIN: -> Filter: ('300' = 2)  (cost=0.855..0.855 rows=0.05) (actual time=0.0291..0.0291 rows=0 loops=1)
    -> Constant row from address  (cost=0.855..0.855 rows=1) (actual time=0.0276..0.0277 rows=1 loops=1)

1 row in set (0.00 sec)

```
可変設定のヒント構文  
SET_VAR ヒントは、システム変数のセッション値を一時的に設定します

```sql
mysql> SELECT @@unique_checks;
+-----------------+
| @@unique_checks |
+-----------------+
|               1 |
+-----------------+
1 row in set (0.00 sec)

mysql> SELECT /*+ SET_VAR(unique_checks=OFF) */ @@unique_checks;
+-----------------+
| @@unique_checks |
+-----------------+
|               0 |
+-----------------+
1 row in set (0.00 sec)

mysql> SELECT @@unique_checks;
+-----------------+
| @@unique_checks |
+-----------------+
|               1 |
+-----------------+
1 row in set (0.00 sec)

```
変数がヒント可能でない場合は、警告が発生します
```sql
mysql> SELECT /*+ SET_VAR(collation_server = 'utf8') */ 1;
+---+
| 1 |
+---+
| 1 |
+---+
1 row in set, 1 warning (0.00 sec)

mysql> SHOW WARNINGS\G
*************************** 1. row ***************************
  Level: Warning
   Code: 3637
Message: Variable 'collation_server' cannot be set using SET_VAR hint.
1 row in set (0.00 sec)
```

### 8.9.4 インデックスヒント
インデックスヒントは、クエリー処理中にインデックスを選択する方法に関するオプティマイザ情報を提供します。  
インデックスヒントは、SELECT および UPDATE ステートメントにのみ適用されます。

```sql
mysql> EXPLAIN ANALYZE SELECT * FROM inventory USE INDEX (idx_fk_film_id) WHERE film_id < 100\G
*************************** 1. row ***************************
EXPLAIN: -> Index range scan on inventory using idx_fk_film_id over (film_id < 100), with index condition: (inventory.film_id < 100)  (cost=203 rows=450) (actual time=0.0428..1.69 rows=450 loops=1)

1 row in set (0.00 sec)

mysql> EXPLAIN ANALYZE SELECT * FROM inventory IGNORE INDEX (idx_fk_film_id) WHERE film_id < 100\G
*************************** 1. row ***************************
EXPLAIN: -> Filter: (inventory.film_id < 100)  (cost=461 rows=1527) (actual time=0.0597..2.24 rows=450 loops=1)
    -> Table scan on inventory  (cost=461 rows=4581) (actual time=0.0585..1.99 rows=4581 loops=1)

1 row in set (0.00 sec)
```

### 8.10.2.6 キーキャッシュの再構築
キーキャッシュはそのパラメータ値を更新することで、いつでも再構築できます。
```sql
mysql> SET GLOBAL cold_cache.key_buffer_size=4*1024*1024;
Query OK, 0 rows affected, 1 warning (0.00 sec)
```
キーキャッシュを再構築する場合、サーバーはまずダーティーバッファーの内容をディスクにフラッシュします。 その後、キャッシュの内容は使用できなくなります。
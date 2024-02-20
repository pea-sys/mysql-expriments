### 15.7.1 InnoDB ロック

### インテンションロック
挿入意図ロックは、行の挿入前に INSERT 操作によって設定されるギャップロックのタイプです

クライアントA
```Sql
mysql> CREATE TABLE child (id int(11) NOT NULL, PRIMARY KEY(id)) ENGINE=InnoDB;
Query OK, 0 rows affected, 1 warning (0.05 sec)

mysql> INSERT INTO child (id) values (90),(102);
Query OK, 2 rows affected (0.04 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql> START TRANSACTION;
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT * FROM child WHERE id > 100 FOR UPDATE;
+-----+
| id  |
+-----+
| 102 |
+-----+
1 row in set (0.01 sec)
```
クライアントB
```sql
mysql> START TRANSACTION;
Query OK, 0 rows affected (0.00 sec)

mysql> INSERT INTO child (id) VALUES (101);
ERROR 1205 (HY000): Lock wait timeout exceeded; try restarting transaction
```
### 15.7.2.1 トランザクション分離レベル
* READ COMMITTED

トランザクションA
```sql
mysql> CREATE TABLE t (a INT NOT NULL, b INT) ENGINE = InnoDB;
Query OK, 0 rows affected (0.03 sec)

mysql> INSERT INTO t VALUES (1,2),(2,3),(3,2),(4,3),(5,2);
Query OK, 5 rows affected (0.00 sec)
Records: 5  Duplicates: 0  Warnings: 0

mysql> COMMIT;
Query OK, 0 rows affected (0.00 sec)

mysql> # Session A
Query OK, 0 rows affected (0.00 sec)

mysql> START TRANSACTION;
Query OK, 0 rows affected (0.00 sec)

mysql> UPDATE t SET b = 5 WHERE b = 3;
Query OK, 2 rows affected (0.00 sec)
Rows matched: 2  Changed: 2  Warnings: 0
```
トランザクションB

```Sql
mysql> UPDATE t SET b = 4 WHERE b = 2;
ERROR 1205 (HY000): Lock wait timeout exceeded; try restarting transaction
```
### 15.7.2.2 自動コミット、コミットおよびロールバック
```sql
mysql> CREATE TABLE customer (a INT, b CHAR (20), INDEX (a));
Query OK, 0 rows affected (0.06 sec)

mysql> SET autocommit = 1;
Query OK, 0 rows affected (0.00 sec)

mysql> START TRANSACTION;
Query OK, 0 rows affected (0.00 sec)

mysql> INSERT INTO customer VALUES (10, 'Heikki');
Query OK, 1 row affected (0.00 sec)

mysql> COMMIT;
Query OK, 0 rows affected (0.00 sec)

mysql> SET autocommit=0;
Query OK, 0 rows affected (0.00 sec)

mysql> INSERT INTO customer VALUES (15, 'John');
Query OK, 1 row affected (0.00 sec)

mysql> INSERT INTO customer VALUES (20, 'Paul');
Query OK, 1 row affected (0.00 sec)

mysql> DELETE FROM customer WHERE b = 'Heikki';
Query OK, 1 row affected (0.00 sec)

mysql> ROLLBACK;
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT * FROM customer;
+------+--------+
| a    | b      |
+------+--------+
|   10 | Heikki |
+------+--------+
1 row in set (0.00 sec)
```

### 15.7.2.4 読取りのロック
* SKIP LOCKED

コネクションA
```sql
mysql> CREATE TABLE t (i INT, PRIMARY KEY (i)) ENGINE = InnoDB;
Query OK, 0 rows affected (0.02 sec)

mysql> INSERT INTO t (i) VALUES(1),(2),(3);
Query OK, 3 rows affected (0.00 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql> START TRANSACTION;
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT * FROM t WHERE i = 2 FOR UPDATE;
+---+
| i |
+---+
| 2 |
+---+
1 row in set (0.00 sec)
```
コネクションB
```sql
mysql> START TRANSACTION;
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT * FROM t WHERE i = 2 FOR UPDATE NOWAIT;
ERROR 3572 (HY000): Statement aborted because lock(s) could not be acquired immediately and NOWAIT is set.
```
コネクションC
```sql
mysql> START TRANSACTION;
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT * FROM t FOR UPDATE SKIP LOCKED;
+---+
| i |
+---+
| 1 |
| 3 |
+---+
2 rows in set (0.00 sec)
```
### 15.7.5.1 InnoDB デッドロックの例
トランザクションA
```Sql
mysql> CREATE TABLE t (i INT) ENGINE = InnoDB;
Query OK, 0 rows affected (0.02 sec)

mysql> INSERT INTO t (i) VALUES(1);
Query OK, 1 row affected (0.01 sec)

mysql> START TRANSACTION;
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT * FROM t WHERE i = 1 FOR SHARE;
+------+
| i    |
+------+
|    1 |
+------+
1 row in set (0.00 sec)
```
トランザクションB
```Sql
mysql> DELETE FROM t WHERE i = 1;
ERROR 1205 (HY000): Lock wait timeout exceeded; try restarting transaction
```
トランザクションA
```sql
mysql> DELETE FROM t WHERE i = 1;
Query OK, 1 row affected (0.00 sec)
```

### 15.8.2 読み取り専用操作用の InnoDB の構成
```sql
masami@masami-L ~/Desktop> mysqld --innodb-read-only=1
```
### 15.8.3.1 InnoDB バッファプールサイズの構成
InnoDB バッファプールのサイズは、オフラインまたはサーバーの実行中に構成できます。
```sql
root@masami-L:~# mysqld --innodb-buffer-pool-size=8G
```

```sql
mysql> SELECT @@innodb_buffer_pool_size/1024/1024/1024;
+------------------------------------------+
| @@innodb_buffer_pool_size/1024/1024/1024 |
+------------------------------------------+
|                           0.125000000000 |
+------------------------------------------+
1 row in set (0.00 sec)
```

### 15.8.3.6 バッファープールの状態の保存と復元
サーバーの再起動後の warmup 期間を短縮するために、InnoDB では、サーバーの停止時にバッファプールごとに最近使用されたページの割合が保存され、サーバーの起動時にこれらのページがリストアされます。

バッファープールページのダンプ率の構成
```sql
mysql> SET GLOBAL innodb_buffer_pool_dump_pct=40;
Query OK, 0 rows affected (0.00 sec)
```
シャットダウン時のバッファープールの状態の保存と起動時の復元
```sql
mysql> SET GLOBAL innodb_buffer_pool_dump_at_shutdown=ON;
Query OK, 0 rows affected (0.00 sec)
```

バッファープールの状態をオンラインで保存および復元
```sql
mysql> SET GLOBAL innodb_buffer_pool_dump_now=ON;
Query OK, 0 rows affected (0.00 sec)
```
バッファープールのダンプの進行状況の表示
```Sql
mysql> SHOW STATUS LIKE 'Innodb_buffer_pool_dump_status';
+--------------------------------+--------------------------------------------------+
| Variable_name                  | Value                                            |
+--------------------------------+--------------------------------------------------+
| Innodb_buffer_pool_dump_status | Buffer pool(s) dump completed at 231230 22:03:34 |
+--------------------------------+--------------------------------------------------+
1 row in set (0.02 sec)
```
バッファープールのロードの進行状況の表示
```Sql
mysql> SHOW STATUS LIKE 'Innodb_buffer_pool_load_status';
+--------------------------------+--------------------------------------------------+
| Variable_name                  | Value                                            |
+--------------------------------+--------------------------------------------------+
| Innodb_buffer_pool_load_status | Buffer pool(s) load completed at 231230 21:48:23 |
+--------------------------------+--------------------------------------------------+
1 row in set (0.00 sec)
```
バッファープールのロード操作の中止
```Sql
mysql> SET GLOBAL innodb_buffer_pool_load_abort=ON;
Query OK, 0 rows affected (0.00 sec)
```
パフォーマンススキーマを使用したバッファプールのロード進行状況の監視

次の例は、stage/innodb/buffer pool load ステージイベントインストゥルメントおよび関連するコンシューマテーブルを有効にして、バッファプールのロード進行状況を監視する方法を示しています

* stage/innodb/buffer pool load インストゥルメントを有効にします
```sql
mysql> UPDATE performance_schema.setup_instruments SET ENABLED = 'YES'
    ->        WHERE NAME LIKE 'stage/innodb/buffer%';
Query OK, 0 rows affected (0.01 sec)
Rows matched: 1  Changed: 0  Warnings: 0
```
* ステージイベントコンシューマテーブル (events_stages_current、events_stages_history および events_stages_history_long を含む) を有効にします。
```Sql
mysql> UPDATE performance_schema.setup_consumers SET ENABLED = 'YES'
    ->        WHERE NAME LIKE '%stages%';
Query OK, 3 rows affected (0.00 sec)
Rows matched: 3  Changed: 3  Warnings: 0
```
* innodb_buffer_pool_dump_now を有効にして、現在のバッファープールの状態をダンプします
```sql
mysql> SET GLOBAL innodb_buffer_pool_dump_now=ON;
Query OK, 0 rows affected (0.00 sec)
```
```sql
mysql> SHOW STATUS LIKE 'Innodb_buffer_pool_dump_status'\G
*************************** 1. row ***************************
Variable_name: Innodb_buffer_pool_dump_status
        Value: Buffer pool(s) dump completed at 231230 22:10:00
1 row in set (0.00 sec)
```
* innodb_buffer_pool_load_now を有効にしてバッファプールをロードします
```sql
mysql> SET GLOBAL innodb_buffer_pool_load_now=ON;
Query OK, 0 rows affected (0.00 sec)
```
* パフォーマンススキーマ events_stages_current テーブルをクエリーして、バッファープールのロード操作の現在のステータスを確認します
```Sql
mysql> SELECT EVENT_NAME, WORK_COMPLETED, WORK_ESTIMATED
    ->        FROM performance_schema.events_stages_current;
Empty set (0.00 sec)
```

### 15.8.10.1 永続的オプティマイザ統計のパラメータの構成

### 15.8.10.1.2 個々のテーブルのオプティマイザ統計パラメータの構成
次の CREATE TABLE の例では、3 つの句が自動統計集計がすべて指定されています
```sql
mysql> CREATE TABLE `t1` (
    -> `id` int(8) NOT NULL auto_increment,
    -> `data` varchar(255),
    -> `date` datetime,
    -> PRIMARY KEY  (`id`),
    -> INDEX `DATE_IX` (`date`)
    -> ) ENGINE=InnoDB,
    ->   STATS_PERSISTENT=1,
    ->   STATS_AUTO_RECALC=1,
    ->   STATS_SAMPLE_PAGES=25;
Query OK, 0 rows affected, 1 warning (0.04 sec)
```
### 15.8.10.1.6 InnoDB 永続的統計テーブルの例
```Sql
mysql> CREATE TABLE t1 (
    -> a INT, b INT, c INT, d INT, e INT, f INT,
    -> PRIMARY KEY (a, b), KEY i1 (c, d), UNIQUE KEY i2uniq (e, f)
    -> ) ENGINE=INNODB;
Query OK, 0 rows affected (0.04 sec)

mysql> SELECT * FROM t1;
+---+---+------+------+------+------+
| a | b | c    | d    | e    | f    |
+---+---+------+------+------+------+
| 1 | 1 |   10 |   11 |  100 |  201 |
| 1 | 2 |   10 |   11 |  100 |  101 |
| 1 | 3 |   10 |   11 |  100 |  202 |
| 1 | 4 |   10 |   11 |  100 |  105 |
| 1 | 5 |   10 |   11 |  100 |  107 |
+---+---+------+------+------+------+
5 rows in set (0.00 sec)


mysql> ANALYZE TABLE t1;
+-----------+---------+----------+----------+
| Table     | Op      | Msg_type | Msg_text |
+-----------+---------+----------+----------+
| sample.t1 | analyze | status   | OK       |
+-----------+---------+----------+----------+
1 row in set (0.01 sec)

mysql> SELECT * FROM mysql.innodb_table_stats WHERE table_name like 't1'\G
*************************** 1. row ***************************
           database_name: sample
              table_name: t1
             last_update: 2023-12-31 08:53:45
                  n_rows: 5
    clustered_index_size: 1
sum_of_other_index_sizes: 2
1 row in set (0.01 sec)

mysql> SELECT index_name, stat_name, stat_value, stat_description
    ->        FROM mysql.innodb_index_stats WHERE table_name like 't1';
+------------+--------------+------------+-----------------------------------+
| index_name | stat_name    | stat_value | stat_description                  |
+------------+--------------+------------+-----------------------------------+
| PRIMARY    | n_diff_pfx01 |          1 | a                                 |
| PRIMARY    | n_diff_pfx02 |          5 | a,b                               |
| PRIMARY    | n_leaf_pages |          1 | Number of leaf pages in the index |
| PRIMARY    | size         |          1 | Number of pages in the index      |
| i1         | n_diff_pfx01 |          1 | c                                 |
| i1         | n_diff_pfx02 |          1 | c,d                               |
| i1         | n_diff_pfx03 |          1 | c,d,a                             |
| i1         | n_diff_pfx04 |          5 | c,d,a,b                           |
| i1         | n_leaf_pages |          1 | Number of leaf pages in the index |
| i1         | size         |          1 | Number of pages in the index      |
| i2uniq     | n_diff_pfx01 |          1 | e                                 |
| i2uniq     | n_diff_pfx02 |          5 | e,f                               |
| i2uniq     | n_leaf_pages |          1 | Number of leaf pages in the index |
| i2uniq     | size         |          1 | Number of pages in the index      |
+------------+--------------+------------+-----------------------------------+
14 rows in set (0.00 sec)

mysql> SELECT index_name, stat_name, stat_value, stat_description
    ->        FROM mysql.innodb_index_stats
    ->        WHERE table_name like 't1' AND stat_name LIKE 'n_diff%';
+------------+--------------+------------+------------------+
| index_name | stat_name    | stat_value | stat_description |
+------------+--------------+------------+------------------+
| PRIMARY    | n_diff_pfx01 |          1 | a                |
| PRIMARY    | n_diff_pfx02 |          5 | a,b              |
| i1         | n_diff_pfx01 |          1 | c                |
| i1         | n_diff_pfx02 |          1 | c,d              |
| i1         | n_diff_pfx03 |          1 | c,d,a            |
| i1         | n_diff_pfx04 |          5 | c,d,a,b          |
| i2uniq     | n_diff_pfx01 |          1 | e                |
| i2uniq     | n_diff_pfx02 |          5 | e,f              |
+------------+--------------+------------+------------------+
8 rows in set (0.00 sec)
```
### 15.8.10.1.7 innodb_index_stats テーブルを使用したインデックスサイズの取得
innodb_index_stats テーブルを使用して、テーブル、パーティションまたはサブパーティションのインデックスサイズを取得できます。
```sql
mysql> SELECT SUM(stat_value) pages, index_name,
    ->        SUM(stat_value)*@@innodb_page_size size
    ->        FROM mysql.innodb_index_stats WHERE table_name='t1'
    ->        AND stat_name = 'size' GROUP BY index_name;
+-------+------------+-------+
| pages | index_name | size  |
+-------+------------+-------+
|     1 | PRIMARY    | 16384 |
|     1 | i1         | 16384 |
|     1 | i2uniq     | 16384 |
+-------+------------+-------+
3 rows in set (0.02 sec)
```

### 15.8.10.3 InnoDB テーブルに対する ANALYZE TABLE の複雑さの推定
```sql
mysql> CREATE TABLE t (
    ->   a INT,
    ->   b INT,
    ->   c INT,
    ->   d INT,
    ->   e INT,
    ->   f INT,
    ->   g INT,
    ->   h INT,
    ->   PRIMARY KEY (a, b),
    ->   UNIQUE KEY i1uniq (c, d),
    ->   KEY i2nonuniq (e, f),
    ->   KEY i3nonuniq (g, h)
    -> );
Query OK, 0 rows affected (0.05 sec)

mysql> SELECT index_name, stat_name, stat_description
    ->        FROM mysql.innodb_index_stats WHERE
    ->        database_name='sample' AND
    ->        table_name='t' AND
    ->        stat_name like 'n_diff_pfx%';
+------------+--------------+------------------+
| index_name | stat_name    | stat_description |
+------------+--------------+------------------+
| PRIMARY    | n_diff_pfx01 | a                |
| PRIMARY    | n_diff_pfx02 | a,b              |
| i1uniq     | n_diff_pfx01 | c                |
| i1uniq     | n_diff_pfx02 | c,d              |
| i2nonuniq  | n_diff_pfx01 | e                |
| i2nonuniq  | n_diff_pfx02 | e,f              |
| i2nonuniq  | n_diff_pfx03 | e,f,a            |
| i2nonuniq  | n_diff_pfx04 | e,f,a,b          |
| i3nonuniq  | n_diff_pfx01 | g                |
| i3nonuniq  | n_diff_pfx02 | g,h              |
| i3nonuniq  | n_diff_pfx03 | g,h,a            |
| i3nonuniq  | n_diff_pfx04 | g,h,a,b          |
+------------+--------------+------------------+
12 rows in set (0.00 sec)
```

### 15.8.11 インデックスページのマージしきい値の構成
テーブルに対する MERGE_THRESHOLD の設定
```sql
mysql> CREATE TABLE t1 (
    ->    id INT,
    ->   KEY id_index (id)
    -> ) COMMENT='MERGE_THRESHOLD=45';
Query OK, 0 rows affected (0.05 sec)
```
個々のインデックスに対する MERGE_THRESHOLD の設定
```Sql
mysql> CREATE TABLE t1 (
    ->    id INT,
    ->   KEY id_index (id) COMMENT 'MERGE_THRESHOLD=40'
    -> );
Query OK, 0 rows affected (0.04 sec)
```
インデックスの MERGE_THRESHOLD 値のクエリー
```Sql
mysql> SELECT * FROM INFORMATION_SCHEMA.INNODB_INDEXES WHERE NAME='id_index' \G
*************************** 1. row ***************************
       INDEX_ID: 232
           NAME: id_index
       TABLE_ID: 1104
           TYPE: 0
       N_FIELDS: 2
        PAGE_NO: 5
          SPACE: 43
MERGE_THRESHOLD: 40
1 row in set (0.07 sec)

```
MERGE_THRESHOLD 設定の影響の測定
```Sql
mysql> SELECT NAME, COMMENT FROM INFORMATION_SCHEMA.INNODB_METRICS
    ->        WHERE NAME like '%index_page_merge%';
+-----------------------------+----------------------------------------+
| NAME                        | COMMENT                                |
+-----------------------------+----------------------------------------+
| index_page_merge_attempts   | Number of index page merge attempts    |
| index_page_merge_successful | Number of successful index page merges |
+-----------------------------+----------------------------------------+
2 rows in set (0.00 sec)
```
### 15.9.1.2 圧縮テーブルの作成
File-Per-Table テーブルスペースでの圧縮テーブルの作成
```sql
mysql> SET GLOBAL innodb_file_per_table=1;
Query OK, 0 rows affected (0.00 sec)

mysql> CREATE TABLE t1
    ->  (c1 INT PRIMARY KEY)
    ->  ROW_FORMAT=COMPRESSED
    ->  KEY_BLOCK_SIZE=8;
Query OK, 0 rows affected (0.03 sec)
```
一般テーブルスペースでの圧縮テーブルの作成
```Sql
mysql> CREATE TABLESPACE `ts2` ADD DATAFILE 'ts2.ibd' FILE_BLOCK_SIZE = 8192 Engine=InnoDB;
Query OK, 0 rows affected (0.02 sec)

mysql> CREATE TABLE t4 (c1 INT PRIMARY KEY) TABLESPACE ts2 ROW_FORMAT=COMPRESSED KEY_BLOCK_SIZE=8;
Query OK, 0 rows affected (0.01 sec)
```

### 15.9.1.3 InnoDB テーブルの圧縮の調整
データの特性と圧縮
```sql
mysql> SET GLOBAL innodb_file_per_table=1;
Query OK, 0 rows affected (0.00 sec)

mysql> SET GLOBAL autocommit=0;
Query OK, 0 rows affected (0.00 sec)

mysql> CREATE TABLE big_table AS SELECT * FROM information_schema.columns;
Query OK, 3747 rows affected (0.42 sec)
Records: 3747  Duplicates: 0  Warnings: 0

mysql> INSERT INTO big_table SELECT * FROM big_table;
Query OK, 3747 rows affected (0.14 sec)
Records: 3747  Duplicates: 0  Warnings: 0

mysql> INSERT INTO big_table SELECT * FROM big_table;
Query OK, 7494 rows affected (0.33 sec)
Records: 7494  Duplicates: 0  Warnings: 0

mysql> INSERT INTO big_table SELECT * FROM big_table;
Query OK, 14988 rows affected (0.51 sec)
Records: 14988  Duplicates: 0  Warnings: 0

mysql> INSERT INTO big_table SELECT * FROM big_table;
Query OK, 29976 rows affected (0.98 sec)
Records: 29976  Duplicates: 0  Warnings: 0

mysql> INSERT INTO big_table SELECT * FROM big_table;
Query OK, 59952 rows affected (5.45 sec)
Records: 59952  Duplicates: 0  Warnings: 0

mysql> INSERT INTO big_table SELECT * FROM big_table;
Query OK, 119904 rows affected (16.56 sec)
Records: 119904  Duplicates: 0  Warnings: 0

mysql> INSERT INTO big_table SELECT * FROM big_table;
Query OK, 239808 rows affected (16.55 sec)
Records: 239808  Duplicates: 0  Warnings: 0

mysql> INSERT INTO big_table SELECT * FROM big_table;
Query OK, 479616 rows affected (40.39 sec)
Records: 479616  Duplicates: 0  Warnings: 0

mysql> INSERT INTO big_table SELECT * FROM big_table;
Query OK, 959232 rows affected (1 min 31.27 sec)
Records: 959232  Duplicates: 0  Warnings: 0

mysql> INSERT INTO big_table SELECT * FROM big_table;
Query OK, 1918464 rows affected (3 min 23.54 sec)
Records: 1918464  Duplicates: 0  Warnings: 0

mysql> COMMIT;
Query OK, 0 rows affected (0.00 sec)

mysql> ALTER TABLE big_table ADD id int unsigned NOT NULL PRIMARY KEY auto_increment;
Query OK, 0 rows affected (4 min 30.92 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql>
mysql> SHOW CREATE TABLE big_table\G
*************************** 1. row ***************************
       Table: big_table
Create Table: CREATE TABLE `big_table` (
  `TABLE_CATALOG` varchar(64) CHARACTER SET utf8mb3 COLLATE utf8mb3_tolower_ci DEFAULT NULL,
  `TABLE_SCHEMA` varchar(64) CHARACTER SET utf8mb3 COLLATE utf8mb3_tolower_ci DEFAULT NULL,
  `TABLE_NAME` varchar(64) CHARACTER SET utf8mb3 COLLATE utf8mb3_tolower_ci DEFAULT NULL,
  `COLUMN_NAME` varchar(64) CHARACTER SET utf8mb3 COLLATE utf8mb3_tolower_ci DEFAULT NULL,
  `ORDINAL_POSITION` int unsigned NOT NULL,
  `COLUMN_DEFAULT` text CHARACTER SET utf8mb3 COLLATE utf8mb3_bin,
  `IS_NULLABLE` varchar(3) CHARACTER SET utf8mb3 NOT NULL DEFAULT '',
  `DATA_TYPE` longtext CHARACTER SET utf8mb3 COLLATE utf8mb3_bin,
  `CHARACTER_MAXIMUM_LENGTH` bigint DEFAULT NULL,
  `CHARACTER_OCTET_LENGTH` bigint DEFAULT NULL,
  `NUMERIC_PRECISION` bigint unsigned DEFAULT NULL,
  `NUMERIC_SCALE` bigint unsigned DEFAULT NULL,
  `DATETIME_PRECISION` int unsigned DEFAULT NULL,
  `CHARACTER_SET_NAME` varchar(64) CHARACTER SET utf8mb3 DEFAULT NULL,
  `COLLATION_NAME` varchar(64) CHARACTER SET utf8mb3 DEFAULT NULL,
  `COLUMN_TYPE` mediumtext CHARACTER SET utf8mb3 COLLATE utf8mb3_bin NOT NULL,
  `COLUMN_KEY` enum('','PRI','UNI','MUL') CHARACTER SET utf8mb3 COLLATE utf8mb3_bin NOT NULL,
  `EXTRA` varchar(256) CHARACTER SET utf8mb3 DEFAULT NULL,
  `PRIVILEGES` varchar(154) CHARACTER SET utf8mb3 DEFAULT NULL,
  `COLUMN_COMMENT` text CHARACTER SET utf8mb3 COLLATE utf8mb3_bin NOT NULL,
  `GENERATION_EXPRESSION` longtext CHARACTER SET utf8mb3 COLLATE utf8mb3_bin NOT NULL,
  `SRS_ID` int unsigned DEFAULT NULL,
  `id` int unsigned NOT NULL AUTO_INCREMENT,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=3836929 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
1 row in set (0.00 sec)

mysql>
mysql> select count(id) from big_table;
+-----------+
| count(id) |
+-----------+
|   3836928 |
+-----------+
1 row in set (4.52 sec)

mysql> CREATE TABLE key_block_size_4 LIKE big_table;
Query OK, 0 rows affected (0.04 sec)

mysql> ALTER TABLE key_block_size_4 key_block_size=4 row_format=compressed;
Query OK, 0 rows affected (0.07 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql>
mysql> INSERT INTO key_block_size_4 SELECT * FROM big_table;
Query OK, 3836928 rows affected (7 min 40.40 sec)
Records: 3836928  Duplicates: 0  Warnings: 0

mysql> commit;
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT
    ->     table_name, engine, table_rows AS tbl_rows,
    ->     avg_row_length AS rlen,
    ->     floor((data_length+index_length)/1024/1024) AS allmb,  #総容量
    ->     floor((data_length)/1024/1024) AS dmb,  #データ容量
    ->     floor((index_length)/1024/1024) AS imb   #インデックス容量
    -> FROM
    ->     information_schema.tables
    -> WHERE
    ->     table_schema=database()
    -> ORDER BY
    ->     (data_length+index_length) DESC;
+------------------+--------+----------+------+-------+------+------+
| TABLE_NAME       | ENGINE | tbl_rows | rlen | allmb | dmb  | imb  |
+------------------+--------+----------+------+-------+------+------+
| big_table        | InnoDB |  3742800 |  214 |   763 |  763 |    0 |
| key_block_size_4 | InnoDB |  3901539 |   45 |   169 |  169 |    0 |
--+----------+------+-------+------+------+
```
次の例は、明示的に定義された行形式なしで作成されたテーブルの行形式を暗黙的に変更するテーブル再構築操作を示しています

```Sql
mysql> SELECT @@innodb_default_row_format;
+-----------------------------+
| @@innodb_default_row_format |
+-----------------------------+
| dynamic                     |
+-----------------------------+
1 row in set (0.00 sec)

mysql> CREATE TABLE t1 (c1 INT);
Query OK, 0 rows affected (0.03 sec)

mysql> SELECT * FROM INFORMATION_SCHEMA.INNODB_TABLES WHERE NAME LIKE 'sample/t1' \G
*************************** 1. row ***************************
          TABLE_ID: 1096
              NAME: sample/t1
              FLAG: 33
            N_COLS: 4
             SPACE: 34
        ROW_FORMAT: Dynamic
     ZIP_PAGE_SIZE: 0
        SPACE_TYPE: Single
      INSTANT_COLS: 0
TOTAL_ROW_VERSIONS: 0
1 row in set (0.02 sec)

mysql> SET GLOBAL innodb_default_row_format=COMPACT;
Query OK, 0 rows affected (0.00 sec)

mysql> ALTER TABLE t1 ADD COLUMN (c2 INT);
Query OK, 0 rows affected (0.02 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> SELECT * FROM INFORMATION_SCHEMA.INNODB_TABLES WHERE NAME LIKE 'sample/t1' \G
*************************** 1. row ***************************
          TABLE_ID: 1096
              NAME: sample/t1
              FLAG: 33
            N_COLS: 5
             SPACE: 34
        ROW_FORMAT: Dynamic
     ZIP_PAGE_SIZE: 0
        SPACE_TYPE: Single
      INSTANT_COLS: 0
TOTAL_ROW_VERSIONS: 1
1 row in set (0.00 sec)
```
テーブルの行形式の決定
```sql
mysql> SELECT NAME, ROW_FORMAT FROM INFORMATION_SCHEMA.INNODB_TABLES WHERE NAME='sample/t1';
+-----------+------------+
| NAME      | ROW_FORMAT |
+-----------+------------+
| sample/t1 | Dynamic    |
+-----------+------------+
1 row in set (0.00 sec)
```

### 15.11.4 テーブルのデフラグ
インデックススキャンを高速化するために、MySQL にテーブルを再構築させる次のALTER TABLE 操作を定期的に実行できます。
```sql
mysql> ALTER TABLE t1 ENGINE=INNODB;
Query OK, 0 rows affected (0.04 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
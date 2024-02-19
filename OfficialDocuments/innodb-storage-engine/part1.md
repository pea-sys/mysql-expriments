### 第 15 章 InnoDB ストレージエンジン

### 15.5.2 変更バッファ
変更バッファの監視には、次のオプションを使用できます
```sql
mysql> SHOW ENGINE INNODB STATUS\G
*************************** 1. row ***************************
  Type: InnoDB
  Name:
Status:
=====================================
2023-12-29 20:59:01 0x365c INNODB MONITOR OUTPUT
=====================================
Per second averages calculated from the last 18 seconds
-----------------
BACKGROUND THREAD
-----------------
srv_master_thread loops: 2 srv_active, 0 srv_shutdown, 8874 srv_idle
srv_master_thread log flush and writes: 0
----------
SEMAPHORES
----------
OS WAIT ARRAY INFO: reservation count 206
OS WAIT ARRAY INFO: signal count 206
RW-shared spins 0, rounds 0, OS waits 0
RW-excl spins 0, rounds 0, OS waits 0
RW-sx spins 0, rounds 0, OS waits 0
Spin rounds per wait: 0.00 RW-shared, 0.00 RW-excl, 0.00 RW-sx
------------
TRANSACTIONS
------------
Trx id counter 20294
Purge done for trx's n:o < 20294 undo n:o < 0 state: running but idle
History list length 0
LIST OF TRANSACTIONS FOR EACH SESSION:
---TRANSACTION 283896329282728, not started
0 lock struct(s), heap size 1128, 0 row lock(s)
---TRANSACTION 283896329281952, not started
0 lock struct(s), heap size 1128, 0 row lock(s)
---TRANSACTION 283896329281176, not started
0 lock struct(s), heap size 1128, 0 row lock(s)
---TRANSACTION 283896329280400, not started
0 lock struct(s), heap size 1128, 0 row lock(s)
---TRANSACTION 283896329279624, not started
0 lock struct(s), heap size 1128, 0 row lock(s)
---TRANSACTION 283896329278848, not started
0 lock struct(s), heap size 1128, 0 row lock(s)
---TRANSACTION 283896329278072, not started
0 lock struct(s), heap size 1128, 0 row lock(s)
---TRANSACTION 283896329277296, not started
0 lock struct(s), heap size 1128, 0 row lock(s)
--------
FILE I/O
--------
I/O thread 0 state: wait Windows aio ((null))
I/O thread 1 state: wait Windows aio (insert buffer thread)
I/O thread 2 state: wait Windows aio (read thread)
I/O thread 3 state: wait Windows aio (read thread)
I/O thread 4 state: wait Windows aio (read thread)
I/O thread 5 state: wait Windows aio (read thread)
I/O thread 6 state: wait Windows aio (write thread)
I/O thread 7 state: wait Windows aio (write thread)
I/O thread 8 state: wait Windows aio (write thread)
Pending normal aio reads: [0, 0, 0, 0] , aio writes: [0, 0, 0, 0] ,
 ibuf aio reads:
Pending flushes (fsync) log: 0; buffer pool: 0
1276 OS file reads, 534 OS file writes, 162 OS fsyncs
0.00 reads/s, 0 avg bytes/read, 0.00 writes/s, 0.00 fsyncs/s
-------------------------------------
INSERT BUFFER AND ADAPTIVE HASH INDEX
-------------------------------------
Ibuf: size 1, free list len 0, seg size 2, 0 merges
merged operations:
 insert 0, delete mark 0, delete 0
discarded operations:
 insert 0, delete mark 0, delete 0
Hash table size 34679, node heap has 0 buffer(s)
Hash table size 34679, node heap has 0 buffer(s)
Hash table size 34679, node heap has 0 buffer(s)
Hash table size 34679, node heap has 2 buffer(s)
Hash table size 34679, node heap has 0 buffer(s)
Hash table size 34679, node heap has 0 buffer(s)
Hash table size 34679, node heap has 0 buffer(s)
Hash table size 34679, node heap has 1 buffer(s)
0.00 hash searches/s, 0.00 non-hash searches/s
---
LOG
---
Log sequence number          390169005
Log buffer assigned up to    390169005
Log buffer completed up to   390169005
Log written up to            390169005
Log flushed up to            390169005
Added dirty pages up to      390169005
Pages flushed up to          390169005
Last checkpoint at           390169005
Log minimum file id is       119
Log maximum file id is       119
56 log i/o's done, 0.00 log i/o's/second
----------------------
BUFFER POOL AND MEMORY
----------------------
Total large memory allocated 0
Dictionary memory allocated 465609
Buffer pool size   8191
Free buffers       6794
Database pages     1394
Old database pages 534
Modified db pages  0
Pending reads      0
Pending writes: LRU 0, flush list 0, single page 0
Pages made young 0, not young 0
0.00 youngs/s, 0.00 non-youngs/s
Pages read 1251, created 143, written 274
0.00 reads/s, 0.00 creates/s, 0.00 writes/s
No buffer pool page gets since the last printout
Pages read ahead 0.00/s, evicted without access 0.00/s, Random read ahead 0.00/s
LRU len: 1394, unzip_LRU len: 0
I/O sum[0]:cur[0], unzip sum[0]:cur[0]
--------------
ROW OPERATIONS
--------------
0 queries inside InnoDB, 0 queries in queue
0 read views open inside InnoDB
Process ID=4756, Main thread ID=4016 , state=sleeping
Number of rows inserted 0, updated 0, deleted 0, read 0
0.00 inserts/s, 0.00 updates/s, 0.00 deletes/s, 0.00 reads/s
Number of system rows inserted 8, updated 349, deleted 8, read 5112
0.00 inserts/s, 0.00 updates/s, 0.00 deletes/s, 0.00 reads/s
----------------------------
END OF INNODB MONITOR OUTPUT
============================

1 row in set (0.00 sec)
```
INFORMATION_SCHEMA.INNODB_METRICS テーブルには、InnoDB 標準モニターの出力にあるデータポイントのほとんどと、その他のデータポイントが表示されます
```sql
mysql> SELECT NAME, COMMENT FROM INFORMATION_SCHEMA.INNODB_METRICS WHERE NAME LIKE '%ibuf%'\G
*************************** 1. row ***************************
   NAME: buffer_page_read_index_ibuf_leaf
COMMENT: Number of Insert Buffer Index Leaf Pages read
*************************** 2. row ***************************
   NAME: buffer_page_read_index_ibuf_non_leaf
COMMENT: Number of Insert Buffer Index Non-Leaf Pages read
*************************** 3. row ***************************
   NAME: buffer_page_read_ibuf_free_list
COMMENT: Number of Insert Buffer Free List Pages read
*************************** 4. row ***************************
   NAME: buffer_page_read_ibuf_bitmap
COMMENT: Number of Insert Buffer Bitmap Pages read
*************************** 5. row ***************************
   NAME: buffer_page_written_index_ibuf_leaf
COMMENT: Number of Insert Buffer Index Leaf Pages written
*************************** 6. row ***************************
   NAME: buffer_page_written_index_ibuf_non_leaf
COMMENT: Number of Insert Buffer Index Non-Leaf Pages written
*************************** 7. row ***************************
   NAME: buffer_page_written_ibuf_free_list
COMMENT: Number of Insert Buffer Free List Pages written
*************************** 8. row ***************************
   NAME: buffer_page_written_ibuf_bitmap
COMMENT: Number of Insert Buffer Bitmap Pages written
*************************** 9. row ***************************
   NAME: ibuf_merges_insert
COMMENT: Number of inserted records merged by change buffering
*************************** 10. row ***************************
   NAME: ibuf_merges_delete_mark
COMMENT: Number of deleted records merged by change buffering
*************************** 11. row ***************************
   NAME: ibuf_merges_delete
COMMENT: Number of purge records merged by change buffering
*************************** 12. row ***************************
   NAME: ibuf_merges_discard_insert
COMMENT: Number of insert merged operations discarded
*************************** 13. row ***************************
   NAME: ibuf_merges_discard_delete_mark
COMMENT: Number of deleted merged operations discarded
*************************** 14. row ***************************
   NAME: ibuf_merges_discard_delete
COMMENT: Number of purge merged operations discarded
*************************** 15. row ***************************
   NAME: ibuf_merges
COMMENT: Number of change buffer merges
*************************** 16. row ***************************
   NAME: ibuf_size
COMMENT: Change buffer size in pages
*************************** 17. row ***************************
   NAME: innodb_ibuf_merge_usec
COMMENT: Time (in microseconds) spent to process change buffer merge
17 rows in set (0.02 sec)
```
たとえば、INNODB_BUFFER_PAGE テーブルをクエリーして、合計バッファプールページ数に対する IBUF_INDEX および IBUF_BITMAP ページの概数を確認できます
```Sql
mysql>  SELECT (SELECT COUNT(*) FROM INFORMATION_SCHEMA.INNODB_BUFFER_PAGE
    ->        WHERE PAGE_TYPE LIKE 'IBUF%') AS change_buffer_pages,
    ->        (SELECT COUNT(*) FROM INFORMATION_SCHEMA.INNODB_BUFFER_PAGE) AS total_pages,
    ->        (SELECT ((change_buffer_pages/total_pages)*100))
    ->        AS change_buffer_page_percentage;
+---------------------+-------------+-------------------------------+
| change_buffer_pages | total_pages | change_buffer_page_percentage |
+---------------------+-------------+-------------------------------+
|                   2 |        8191 |                        0.0244 |
+---------------------+-------------+-------------------------------+
1 row in set (0.19 sec)
```
Performance Schema には、高度なパフォーマンス監視のための変更バッファ相互排他ロック待機インストゥルメンテーションが用意されています。 変更バッファーインストゥルメンテーションを表示するには、次のクエリーを発行します
```sql
mysql> SELECT * FROM performance_schema.setup_instruments
    ->        WHERE NAME LIKE '%wait/synch/mutex/innodb/ibuf%';
+-------------------------------------------------------+---------+-------+------------+-------+------------+---------------+
| NAME                                                  | ENABLED | TIMED | PROPERTIES | FLAGS | VOLATILITY | DOCUMENTATION |
+-------------------------------------------------------+---------+-------+------------+-------+------------+---------------+
| wait/synch/mutex/innodb/ibuf_bitmap_mutex             | NO      | NO    |            | NULL  |          0 | NULL          |
| wait/synch/mutex/innodb/ibuf_mutex                    | NO      | NO    |            | NULL  |          0 | NULL          |
| wait/synch/mutex/innodb/ibuf_pessimistic_insert_mutex | NO      | NO    |            | NULL  |          0 | NULL          |
+-------------------------------------------------------+---------+-------+------------+-------+------------+---------------+
3 rows in set (0.02 sec)

```
### 15.6.1.1 InnoDB テーブルの作成
InnoDB テーブルを作成するには、CREATE TABLE ステートメントを使用します
```Sql
mysql> CREATE TABLE t1 (a INT, b CHAR (20), PRIMARY KEY (a)) ENGINE=InnoDB;
Query OK, 0 rows affected (0.03 sec)
```
デフォルトのストレージエンジンを確認するには、次のステートメントを発行します
```sql
mysql> SELECT @@default_storage_engine;
+--------------------------+
| @@default_storage_engine |
+--------------------------+
| InnoDB                   |
+--------------------------+
1 row in set (0.00 sec)
```
InnoDB のテーブルと行の形式
```Sql
mysql> SET GLOBAL innodb_file_per_table=1;
Query OK, 0 rows affected (0.00 sec)


mysql> CREATE TABLE t3 (a INT, b CHAR (20), PRIMARY KEY (a)) ROW_FORMAT=DYNAMIC;
Query OK, 0 rows affected (0.03 sec)

mysql> CREATE TABLE t4 (a INT, b CHAR (20), PRIMARY KEY (a)) ROW_FORMAT=COMPRESSED;
Query OK, 0 rows affected (0.02 sec)

mysql> CREATE TABLE t1 (c1 INT PRIMARY KEY) TABLESPACE = innodb_system ROW_FORMAT=DYNAMIC;
Query OK, 0 rows affected (0.01 sec)
```
InnoDB テーブルおよび主キー
```Sql
mysql> CREATE TABLE t5 (id INT AUTO_INCREMENT, b CHAR (20), PRIMARY KEY (id));
Query OK, 0 rows affected (0.02 sec)

mysql> CREATE TABLE t6 (id INT AUTO_INCREMENT, a INT, b CHAR (20), PRIMARY KEY (id,a));
Query OK, 0 rows affected (0.02 sec)
```
InnoDB テーブルのプロパティの表示
```Sql
mysql> SHOW TABLE STATUS FROM test LIKE 't%' \G;
*************************** 1. row ***************************
           Name: t
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 0
 Avg_row_length: 0
    Data_length: 16384
Max_data_length: 0
   Index_length: 0
      Data_free: 0
 Auto_increment: NULL
    Create_time: 2023-12-26 22:28:17
    Update_time: NULL
     Check_time: NULL
      Collation: utf8mb3_general_ci
       Checksum: NULL
 Create_options:
        Comment:
1 row in set (0.03 sec)

ERROR:
No query specified

mysql> SELECT * FROM INFORMATION_SCHEMA.INNODB_TABLES WHERE NAME='sakila/t1' \G
*************************** 1. row ***************************
          TABLE_ID: 1290
              NAME: sakila/t1
              FLAG: 161
            N_COLS: 4
             SPACE: 0
        ROW_FORMAT: Dynamic
     ZIP_PAGE_SIZE: 0
        SPACE_TYPE: System
      INSTANT_COLS: 0
TOTAL_ROW_VERSIONS: 0
1 row in set (0.00 sec)
```
### 15.6.1.2 外部でのテーブルの作成
DATA DIRECTORY 句の使用
```sql
mysql> CREATE TABLE t1 (c1 INT PRIMARY KEY) DATA DIRECTORY = 'C:/external';
ERROR 2013 (HY000): Lost connection to MySQL server during query
No connection. Trying to reconnect...
Connection id:    14
Current database: sakila

Query OK, 0 rows affected (0.04 sec)
```
### CREATE TABLE ... TABLESPACE 構文の使用
```Sql
mysql> CREATE TABLE t2 (c1 INT PRIMARY KEY) TABLESPACE = innodb_file_per_table
    ->        DATA DIRECTORY = 'C:/external';
Query OK, 0 rows affected (0.02 sec)
```
### 15.6.1.6 InnoDB での AUTO_INCREMENT 処理
InnoDB には、AUTO_INCREMENT カラムを含むテーブルに行を追加する SQL ステートメントのスケーラビリティおよびパフォーマンスを大幅に向上させる構成可能なロックメカニズムが用意されています

InnoDB AUTO_INCREMENT ロックモードの使用上の意味
```sql
mysql> SELECT @@innodb_autoinc_lock_mode;
+----------------------------+
| @@innodb_autoinc_lock_mode |
+----------------------------+
|                          2 |
+----------------------------+
1 row in set (0.00 sec)

mysql> CREATE TABLE t1 (
    ->      c1 INT UNSIGNED NOT NULL AUTO_INCREMENT PRIMARY KEY,
    ->      c2 CHAR(1)
    ->      ) ENGINE = INNODB;
Query OK, 0 rows affected (0.03 sec)

mysql> INSERT INTO t1 (c1,c2) VALUES (1,'a'), (NULL,'b'), (5,'c'), (NULL,'d');
Query OK, 4 rows affected (0.02 sec)
Records: 4  Duplicates: 0  Warnings: 0

mysql> SELECT c1, c2 FROM t1 ORDER BY c2;
+----+------+
| c1 | c2   |
+----+------+
|  1 | a    |
|  2 | b    |
|  5 | c    |
|  6 | d    |
+----+------+
4 rows in set (0.01 sec)
```

```sql
mysql> CREATE TABLE t1 (
    ->      c1 INT UNSIGNED NOT NULL AUTO_INCREMENT PRIMARY KEY,
    ->      c2 CHAR(1)
    ->      ) ENGINE = INNODB;
Query OK, 0 rows affected (0.02 sec)

mysql> INSERT INTO t1 (c1,c2) VALUES (1,'a'), (NULL,'b'), (5,'c'), (NULL,'d');
Query OK, 4 rows affected (0.01 sec)
Records: 4  Duplicates: 0  Warnings: 0

mysql> SELECT c1, c2 FROM t1 ORDER BY c2;
+----+------+
| c1 | c2   |
+----+------+
|  1 | a    |
|  2 | b    |
|  5 | c    |
|  6 | d    |
+----+------+
4 rows in set (0.00 sec)

mysql> SELECT @@innodb_autoinc_lock_mode;
+----------------------------+
| @@innodb_autoinc_lock_mode |
+----------------------------+
|                          0 |
+----------------------------+
1 row in set (0.00 sec)
```
### 15.6.2.4 InnoDB FULLTEXT インデックス
InnoDB 全文インデックステーブル
```sql
mysql> CREATE TABLE opening_lines (
    ->        id INT UNSIGNED AUTO_INCREMENT NOT NULL PRIMARY KEY,
    ->        opening_line TEXT(500),
    ->        author VARCHAR(200),
    ->        title VARCHAR(200),
    ->        FULLTEXT idx (opening_line)
    ->        ) ENGINE=InnoDB;
Query OK, 0 rows affected (0.19 sec)

mysql> SELECT table_id, name, space from INFORMATION_SCHEMA.INNODB_TABLES
    ->        WHERE name LIKE 'sakila/%';
+----------+------------------------------------------------------+-------+
| table_id | name                                                 | space |
+----------+------------------------------------------------------+-------+
|     1064 | sakila/actor                                         |     2 |
|     1065 | sakila/address                                       |     3 |
|     1066 | sakila/category                                      |     4 |
|     1067 | sakila/city                                          |     5 |
|     1068 | sakila/country                                       |     6 |
|     1069 | sakila/customer                                      |     7 |
|     1070 | sakila/film                                          |     8 |
|     1071 | sakila/film_actor                                    |     9 |
|     1072 | sakila/film_category                                 |    10 |
|     1073 | sakila/film_text                                     |    11 |
|     1074 | sakila/fts_0000000000000431_being_deleted            |    12 |
|     1075 | sakila/fts_0000000000000431_being_deleted_cache      |    13 |
|     1076 | sakila/fts_0000000000000431_config                   |    14 |
|     1077 | sakila/fts_0000000000000431_deleted                  |    15 |
|     1078 | sakila/fts_0000000000000431_deleted_cache            |    16 |
|     1079 | sakila/fts_0000000000000431_00000000000000b6_index_1 |    17 |
|     1080 | sakila/fts_0000000000000431_00000000000000b6_index_2 |    18 |
|     1081 | sakila/fts_0000000000000431_00000000000000b6_index_3 |    19 |
|     1082 | sakila/fts_0000000000000431_00000000000000b6_index_4 |    20 |
|     1083 | sakila/fts_0000000000000431_00000000000000b6_index_5 |    21 |
|     1084 | sakila/fts_0000000000000431_00000000000000b6_index_6 |    22 |
|     1085 | sakila/inventory                                     |    23 |
|     1086 | sakila/language                                      |    24 |
|     1087 | sakila/payment                                       |    25 |
|     1088 | sakila/rental                                        |    26 |
|     1089 | sakila/staff                                         |    27 |
|     1090 | sakila/store                                         |    28 |
|     1095 | sakila/tz                                            |    33 |
|     1097 | sakila/md5_tbl                                       |    35 |
|     1101 | sakila/product                                       |    39 |
|     1103 | sakila/tj10                                          |    41 |
|     1104 | sakila/geo                                           |    42 |
|     1106 | sakila/jtable                                        |    44 |
|     1107 | sakila/sales                                         |    45 |
|     1109 | sakila/mytable                                       |    47 |
|     1113 | sakila/y                                             |    51 |
|     1270 | sakila/items                                         |   205 |
|     1271 | sakila/employees                                     |   206 |
|     1276 | sakila/t                                             |   211 |
|     1288 | sakila/t3                                            |   223 |
|     1289 | sakila/t4                                            |   224 |
|     1291 | sakila/t5                                            |   225 |
|     1292 | sakila/t6                                            |   226 |
|     1294 | sakila/t2                                            |   228 |
|     1298 | sakila/opening_lines                                 |   232 |
|     1299 | sakila/fts_0000000000000512_being_deleted            |   233 |
|     1300 | sakila/fts_0000000000000512_being_deleted_cache      |   234 |
|     1301 | sakila/fts_0000000000000512_config                   |   235 |
|     1302 | sakila/fts_0000000000000512_deleted                  |   236 |
|     1303 | sakila/fts_0000000000000512_deleted_cache            |   237 |
|     1304 | sakila/fts_0000000000000512_00000000000001c6_index_1 |   238 |
|     1305 | sakila/fts_0000000000000512_00000000000001c6_index_2 |   239 |
|     1306 | sakila/fts_0000000000000512_00000000000001c6_index_3 |   240 |
|     1307 | sakila/fts_0000000000000512_00000000000001c6_index_4 |   241 |
|     1308 | sakila/fts_0000000000000512_00000000000001c6_index_5 |   242 |
|     1309 | sakila/fts_0000000000000512_00000000000001c6_index_6 |   243 |
|     1280 | sakila/pt#p#p0                                       |   215 |
|     1281 | sakila/pt#p#p1                                       |   216 |
|     1282 | sakila/pt#p#p2                                       |   217 |
|     1283 | sakila/pt#p#p3                                       |   218 |
+----------+------------------------------------------------------+-------+
60 rows in set (0.00 sec)
```

```sql
mysql> SELECT index_id, name, table_id, space from INFORMATION_SCHEMA.INNODB_INDEXES
    ->        WHERE index_id=457;
+----------+---------------------+----------+-------+
| index_id | name                | table_id | space |
+----------+---------------------+----------+-------+
|      457 | FTS_INDEX_TABLE_IND |     1306 |   240 |
+----------+---------------------+----------+-------+
1 row in set (0.01 sec)
```
InnoDB 全文インデックスドキュメント ID および FTS_DOC_ID カラム
```sql
mysql> CREATE TABLE opening_lines (
    ->        id INT UNSIGNED AUTO_INCREMENT NOT NULL PRIMARY KEY,
    ->        opening_line TEXT(500),
    ->        author VARCHAR(200),
    ->        title VARCHAR(200)
    ->        ) ENGINE=InnoDB;
ERROR 1050 (42S01): Table 'opening_lines' already exists
mysql> drop table opening_lines;
Query OK, 0 rows affected (0.09 sec)
```
CREATE FULLTEXT INDEX 構文を使用して、テーブル上に全文インデックスを作成すると、FTS_DOC_ID カラムが追加されるように InnoDB がテーブルを再構築していることをレポートする警告が返されます
```sql
mysql> CREATE FULLTEXT INDEX idx ON opening_lines(opening_line);
Query OK, 0 rows affected, 1 warning (0.18 sec)
Records: 0  Duplicates: 0  Warnings: 1

mysql> SHOW WARNINGS;
+---------+------+--------------------------------------------------+
| Level   | Code | Message                                          |
+---------+------+--------------------------------------------------+
| Warning |  124 | InnoDB rebuilding table to add column FTS_DOC_ID |
+---------+------+--------------------------------------------------+
1 row in set (0.00 sec)
```
独自の FTS_DOC_ID カラムを作成する場合は、次の例のように、カラムを BIGINT UNSIGNED NOT NULL として定義し、FTS_DOC_ID (すべて大文字) という名前を付ける必要があります
```Sql
mysql> CREATE TABLE opening_lines (
    ->        FTS_DOC_ID BIGINT UNSIGNED AUTO_INCREMENT NOT NULL PRIMARY KEY,
    ->        opening_line TEXT(500),
    ->        author VARCHAR(200),
    ->        title VARCHAR(200)
    ->        ) ENGINE=InnoDB;
Query OK, 0 rows affected (0.02 sec)

mysql> CREATE UNIQUE INDEX FTS_DOC_ID_INDEX on opening_lines(FTS_DOC_ID);
Query OK, 0 rows affected (0.02 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
InnoDB による全文インデックスのトランザクション処理
```sql
mysql> CREATE TABLE opening_lines (
    ->        id INT UNSIGNED AUTO_INCREMENT NOT NULL PRIMARY KEY,
    ->        opening_line TEXT(500),
    ->        author VARCHAR(200),
    ->        title VARCHAR(200),
    ->        FULLTEXT idx (opening_line)
    ->        ) ENGINE=InnoDB;
Query OK, 0 rows affected (0.15 sec)

mysql> BEGIN;
Query OK, 0 rows affected (0.00 sec)

mysql> INSERT INTO opening_lines(opening_line,author,title) VALUES
    ->        ('Call me Ishmael.','Herman Melville','Moby-Dick'),
    ->        ('A screaming comes across the sky.','Thomas Pynchon','Gravity\'s Rainbow'),
    ->        ('I am an invisible man.','Ralph Ellison','Invisible Man'),
    ->        ('Where now? Who now? When now?','Samuel Beckett','The Unnamable'),
    ->        ('It was love at first sight.','Joseph Heller','Catch-22'),
    ->        ('All this happened, more or less.','Kurt Vonnegut','Slaughterhouse-Five'),
    ->        ('Mrs. Dalloway said she would buy the flowers herself.','Virginia Woolf','Mrs. Dalloway'),
    ->        ('It was a pleasure to burn.','Ray Bradbury','Fahrenheit 451');
Query OK, 8 rows affected (0.01 sec)
Records: 8  Duplicates: 0  Warnings: 0

mysql> SELECT COUNT(*) FROM opening_lines WHERE MATCH(opening_line) AGAINST('Ishmael');
+----------+
| COUNT(*) |
+----------+
|        0 |
+----------+
1 row in set (0.01 sec)

mysql> COMMIT;
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT COUNT(*) FROM opening_lines WHERE MATCH(opening_line) AGAINST('Ishmael');
+----------+
| COUNT(*) |
+----------+
|        1 |
+----------+
1 row in set (0.00 sec)
```

### 15.6.3.4 undo テーブルスペース
undo テーブルスペースには undo ログが含まれます。undo ログレコードには、クラスタ化されたインデックスレコードに対するトランザクションによる最新の変更を取り消す方法に関する情報が含まれます

undo テーブルスペースの追加
```sql
mysql> CREATE UNDO TABLESPACE tablespace_name ADD DATAFILE 'file_name.ibu';
Query OK, 0 rows affected (0.34 sec)
```
undo テーブルスペースの名前とパスを表示するには、INFORMATION_SCHEMA.FILES をクエリーします
```sql
mysql> SELECT TABLESPACE_NAME, FILE_NAME FROM INFORMATION_SCHEMA.FILES
    ->   WHERE FILE_TYPE LIKE 'UNDO LOG';
+-----------------+-----------------+
| TABLESPACE_NAME | FILE_NAME       |
+-----------------+-----------------+
| innodb_undo_001 | ./undo_001      |
| innodb_undo_002 | ./undo_002      |
| tablespace_name | ./file_name.ibu |
| undo_003        | ./undo_003.ibu  |
+-----------------+-----------------+
4 rows in set (0.05 sec)
```
undo テーブルスペースの削除
```sql
mysql> ALTER UNDO TABLESPACE tablespace_name SET INACTIVE;
Query OK, 0 rows affected (0.00 sec)

mysql> DROP UNDO TABLESPACE tablespace_name;
Query OK, 0 rows affected (0.01 sec)
```
自動切捨て
```sql
mysql> SET GLOBAL innodb_undo_log_truncate=ON;
Query OK, 0 rows affected (0.00 sec)
```
innodb_undo_log_truncate 変数が有効な場合、innodb_max_undo_log_size 変数で定義されたサイズ制限を超える undo テーブルスペースは切り捨てられる可能性があります。
```sql
mysql> SELECT @@innodb_max_undo_log_size;
+----------------------------+
| @@innodb_max_undo_log_size |
+----------------------------+
|                 1073741824 |
+----------------------------+
1 row in set (0.00 sec)
```
 CREATE UNDO TABLESPACE 構文を使用して作成されたユーザー定義の undo テーブルスペースを含むすべての undo テーブルスペースファイルの場所は、INFORMATION_SCHEMA.FILES テーブルをクエリーすることで確認できます
```Sql
mysql> SELECT TABLESPACE_NAME, FILE_NAME FROM INFORMATION_SCHEMA.FILES WHERE FILE_TYPE LIKE 'UNDO LOG';
+-----------------+----------------+
| TABLESPACE_NAME | FILE_NAME      |
+-----------------+----------------+
| innodb_undo_001 | ./undo_001     |
| innodb_undo_002 | ./undo_002     |
| undo_003        | ./undo_003.ibu |
+-----------------+----------------+
3 rows in set (0.02 sec)
```
undo テーブルスペースの状態を確認するには、INFORMATION_SCHEMA.INNODB_TABLESPACES テーブルをクエリーします
```sql
mysql> SELECT NAME, STATE FROM INFORMATION_SCHEMA.INNODB_TABLESPACES
    ->   WHERE NAME LIKE 'undo_003';
+----------+--------+
| NAME     | STATE  |
+----------+--------+
| undo_003 | active |
+----------+--------+
1 row in set (0.10 sec)
```
undo テーブルスペースが empty 状態になると、次のステートメントを発行して undo テーブルスペースを再アクティブ化できます
```sql
mysql> ALTER UNDO TABLESPACE undo_003 SET ACTIVE;
Query OK, 0 rows affected (0.00 sec)
```
undo テーブルスペースの自動切捨ての回避

パージスレッドは、undo テーブルスペースを空にしたり切り捨てたりします。 デフォルトでは、パージスレッドは undo テーブルスペースを検索し、パージが起動されるたびに 128 回切り捨てます。 パージスレッドが切り捨てる undo テーブルスペースを検索する頻度は、innodb_purge_rseg_truncate_frequency 変数 (デフォルト設定は 128) によって制御されます
```sql
mysql> SELECT @@innodb_purge_rseg_truncate_frequency;
+----------------------------------------+
| @@innodb_purge_rseg_truncate_frequency |
+----------------------------------------+
|                                    128 |
+----------------------------------------+
1 row in set (0.00 sec)
```
この頻度を上げるには、innodb_purge_rseg_truncate_frequency 設定を減らします。 たとえば、パージスレッドで undo テーブルスペースを 32 回起動するたびに検索するには、innodb_purge_rseg_truncate_frequency を 32 に設定します
```sql
mysql> SET GLOBAL innodb_purge_rseg_truncate_frequency=32;
Query OK, 0 rows affected (0.00 sec)
```
undo テーブルスペースの切捨ての監視

```Sql
mysql> SELECT NAME, SUBSYSTEM, COMMENT FROM INFORMATION_SCHEMA.INNODB_METRICS WHERE NAME LIKE '%truncate%';
+-----------------------------------+-----------+------------------------------------------------------------------------+
| NAME                              | SUBSYSTEM | COMMENT
 |
+-----------------------------------+-----------+------------------------------------------------------------------------+
| purge_truncate_history_count      | purge     | Number of times the purge thread attempted to truncate undo history    |
| purge_truncate_history_usec       | purge     | Time (in microseconds) the purge thread spent truncating undo history. |
| undo_truncate_count               | undo      | Number of times undo truncation was initiated
 |
| undo_truncate_start_logging_count | undo      | Number of times during undo truncation a log file was started
 |
| undo_truncate_done_logging_count  | undo      | Number of times during undo truncation a log file was deleted
 |
| undo_truncate_usec                | undo      | Time (in microseconds) spent to process undo truncation
 |
+-----------------------------------+-----------+------------------------------------------------------------------------+
6 rows in set (0.00 sec)
```
undo テーブルスペースのステータス変数
```sql
mysql> SHOW STATUS LIKE 'Innodb_undo_tablespaces%';
+----------------------------------+-------+
| Variable_name                    | Value |
+----------------------------------+-------+
| Innodb_undo_tablespaces_total    | 3     |
| Innodb_undo_tablespaces_implicit | 2     |
| Innodb_undo_tablespaces_explicit | 1     |
| Innodb_undo_tablespaces_active   | 3     |
+----------------------------------+-------+
4 rows in set (0.01 sec)
```
### 15.6.3.5 一時テーブルスペース
InnoDB では、セッション一時テーブルスペースおよびグローバル一時テーブルスペースが使用されます。


グローバル一時テーブルスペース
```sql
mysql> SELECT * FROM INFORMATION_SCHEMA.FILES WHERE TABLESPACE_NAME='innodb_temporary'\G
*************************** 1. row ***************************
             FILE_ID: 4294967293
           FILE_NAME: ./ibtmp1
           FILE_TYPE: TEMPORARY
     TABLESPACE_NAME: innodb_temporary
       TABLE_CATALOG:
        TABLE_SCHEMA: NULL
          TABLE_NAME: NULL
  LOGFILE_GROUP_NAME: NULL
LOGFILE_GROUP_NUMBER: NULL
              ENGINE: InnoDB
       FULLTEXT_KEYS: NULL
        DELETED_ROWS: NULL
        UPDATE_COUNT: NULL
        FREE_EXTENTS: 2
       TOTAL_EXTENTS: 12
         EXTENT_SIZE: 1048576
        INITIAL_SIZE: 12582912
        MAXIMUM_SIZE: NULL
     AUTOEXTEND_SIZE: 67108864
       CREATION_TIME: NULL
    LAST_UPDATE_TIME: NULL
    LAST_ACCESS_TIME: NULL
        RECOVER_TIME: NULL
 TRANSACTION_COUNTER: NULL
             VERSION: NULL
          ROW_FORMAT: NULL
          TABLE_ROWS: NULL
      AVG_ROW_LENGTH: NULL
         DATA_LENGTH: NULL
     MAX_DATA_LENGTH: NULL
        INDEX_LENGTH: NULL
           DATA_FREE: 6291456
         CREATE_TIME: NULL
         UPDATE_TIME: NULL
          CHECK_TIME: NULL
            CHECKSUM: NULL
              STATUS: NORMAL
               EXTRA: NULL
1 row in set (0.00 sec)

mysql> SELECT @@innodb_temp_data_file_path;
+------------------------------+
| @@innodb_temp_data_file_path |
+------------------------------+
| ibtmp1:12M:autoextend        |
+------------------------------+
1 row in set (0.00 sec)
```
グローバル一時テーブルスペースデータファイルのサイズを確認するには、次のようなクエリーを使用して INFORMATION_SCHEMA.FILES テーブルをクエリーします
```Sql
mysql> SELECT FILE_NAME, TABLESPACE_NAME, ENGINE, INITIAL_SIZE, TOTAL_EXTENTS*EXTENT_SIZE
    ->        AS TotalSizeBytes, DATA_FREE, MAXIMUM_SIZE FROM INFORMATION_SCHEMA.FILES
    ->        WHERE TABLESPACE_NAME = 'innodb_temporary'\G
*************************** 1. row ***************************
      FILE_NAME: ./ibtmp1
TABLESPACE_NAME: innodb_temporary
         ENGINE: InnoDB
   INITIAL_SIZE: 12582912
 TotalSizeBytes: 12582912
      DATA_FREE: 6291456
   MAXIMUM_SIZE: NULL
1 row in set (0.00 sec)
```

### 15.6.3.9 テーブルスペースの AUTOEXTEND_SIZE 構成
file-per-table テーブルスペースの拡張子サイズを構成するには、CREATE TABLE または ALTER TABLE ステートメントで AUTOEXTEND_SIZE サイズを指定します
```sql
mysql> CREATE TABLE t1 (c1 INT) AUTOEXTEND_SIZE = 4M;
Query OK, 0 rows affected (0.06 sec)

mysql> ALTER TABLE t1 AUTOEXTEND_SIZE = 8M;
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
一般的なテーブルスペースの拡張サイズを構成するには、CREATE TABLESPACE ステートメントまたは ALTER TABLESPACE ステートメントで AUTOEXTEND_SIZE サイズを指定します
```sql
mysql> CREATE TABLE t1 (c1 INT) AUTOEXTEND_SIZE = 4M;
Query OK, 0 rows affected (0.42 sec)

mysql> ALTER TABLE t1 AUTOEXTEND_SIZE = 8M;
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
一般的なテーブルスペースの拡張サイズを構成するには、CREATE TABLESPACE ステートメントまたは ALTER TABLESPACE ステートメントで AUTOEXTEND_SIZE サイズを指定します
```sql
mysql> CREATE TABLESPACE ts1 AUTOEXTEND_SIZE = 4M;
Query OK, 0 rows affected (0.08 sec)

mysql> ALTER TABLESPACE ts1 AUTOEXTEND_SIZE = 8M;
Query OK, 0 rows affected (0.01 sec)
```
デフォルトの InnoDB ページサイズは 16K (16384 バイト) です。 MySQL インスタンスの InnoDB ページサイズを確認するには、innodb_page_size 設定をクエリーします
```sql
mysql> SELECT @@GLOBAL.innodb_page_size;
+---------------------------+
| @@GLOBAL.innodb_page_size |
+---------------------------+
|                     16384 |
+---------------------------+
1 row in set (0.00 sec)
```
InnoDB テーブルスペースの AUTOEXTEND_SIZE を確認するには、INFORMATION_SCHEMA.INNODB_TABLESPACES テーブルをクエリーします
```Sql
mysql> SELECT NAME, AUTOEXTEND_SIZE FROM INFORMATION_SCHEMA.INNODB_TABLESPACES
    ->        WHERE NAME LIKE 'sample/t1';
+-----------+-----------------+
| NAME      | AUTOEXTEND_SIZE |
+-----------+-----------------+
| sample/t1 |         8388608 |
+-----------+-----------------+
1 row in set (0.04 sec)

mysql> SELECT NAME, AUTOEXTEND_SIZE FROM INFORMATION_SCHEMA.INNODB_TABLESPACES
    ->        WHERE NAME LIKE 'ts1';
+------+-----------------+
| NAME | AUTOEXTEND_SIZE |
+------+-----------------+
| ts1  |         8388608 |
+------+-----------------+
1 row in set (0.04 sec)
```

### 15.6.5 redo ログ
redo ログのアーカイブ
```sql
mysql> SET GLOBAL innodb_redo_log_archive_dirs='label1:C:/redo';
Query OK, 0 rows affected (0.00 sec)
```
redo ログのアーカイブをサポートするバックアップユーティリティを使用していない場合は、次に示すように、redo ログのアーカイブを手動でアクティブ化することもできます
```sql
mysql> SELECT innodb_redo_log_archive_start('label1', 'subdir');
+---------------------------------------------------+
| innodb_redo_log_archive_start('label1', 'subdir') |
+---------------------------------------------------+
|                                                 0 |
+---------------------------------------------------+
1 row in set (0.02 sec)
```
redo ログのアーカイブをサポートするバックアップユーティリティを使用していない場合は、次に示すように、redo ログのアーカイブを手動で非アクティブ化することもできます
```Sql
mysql> SELECT innodb_redo_log_archive_stop();
+--------------------------------+
| innodb_redo_log_archive_stop() |
+--------------------------------+
|                              0 |
+--------------------------------+
1 row in set (0.01 sec)
```
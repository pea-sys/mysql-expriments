### 15.15.6 InnoDB INFORMATION_SCHEMA メトリックテーブル
INNODB_METRICS テーブルのカラムを次に示します
```sql
mysql> SELECT * FROM INFORMATION_SCHEMA.INNODB_METRICS WHERE NAME="dml_inserts" \G
*************************** 1. row ***************************
           NAME: dml_inserts
      SUBSYSTEM: dml
          COUNT: 184491
      MAX_COUNT: 184491
      MIN_COUNT: NULL
      AVG_COUNT: 30.790931020657258
    COUNT_RESET: 184491
MAX_COUNT_RESET: 184491
MIN_COUNT_RESET: NULL
AVG_COUNT_RESET: NULL
   TIME_ENABLED: 2024-01-01 07:57:24
  TIME_DISABLED: NULL
   TIME_ELAPSED: 5992
     TIME_RESET: NULL
         STATUS: enabled
           TYPE: status_counter
        COMMENT: Number of rows inserted
1 row in set (0.00 sec)
```

カウンタモジュール

各カウンタは特定のモジュールに関連付けられています。 モジュール名を使用すると、特定のサブシステムのすべてのカウンタを有効化、無効化、またはリセットできます。 たとえば、dml サブシステムに関連付けられたすべてのカウンタを有効にするには、module_dml を使用します。
```sql
mysql> SET GLOBAL innodb_monitor_enable = module_dml;
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT name, subsystem, status FROM INFORMATION_SCHEMA.INNODB_METRICS
    ->        WHERE subsystem ='dml';
+--------------------+-----------+---------+
| name               | subsystem | status  |
+--------------------+-----------+---------+
| dml_reads          | dml       | enabled |
| dml_inserts        | dml       | enabled |
| dml_deletes        | dml       | enabled |
| dml_updates        | dml       | enabled |
| dml_system_reads   | dml       | enabled |
| dml_system_inserts | dml       | enabled |
| dml_system_deletes | dml       | enabled |
| dml_system_updates | dml       | enabled |
+--------------------+-----------+---------+
8 rows in set (0.00 sec)
```

カウンタの有効化、無効化、およびリセットと、INNODB_METRICS テーブル内のカウンタデータのクエリーを示します

単純な InnoDB テーブルを作成します
```sql
mysql> CREATE TABLE t1 (c1 INT) ENGINE=INNODB;
Query OK, 0 rows affected (0.03 sec)
```
dml_inserts カウンタを有効にします
```Sql
mysql> SET GLOBAL innodb_monitor_enable = dml_inserts;
Query OK, 0 rows affected (0.00 sec)
```
dml_inserts カウンタの説明は、INNODB_METRICS テーブルの COMMENT カラムで見つけることができます
```Sql
mysql> SELECT NAME, COMMENT FROM INFORMATION_SCHEMA.INNODB_METRICS WHERE NAME="dml_inserts";
+-------------+-------------------------+
| NAME        | COMMENT                 |
+-------------+-------------------------+
| dml_inserts | Number of rows inserted |
+-------------+-------------------------+
1 row in set (0.00 sec)
```
dml_inserts カウンタデータを取得するために INNODB_METRICS テーブルをクエリーします
```Sql
mysql> SELECT * FROM INFORMATION_SCHEMA.INNODB_METRICS WHERE NAME="dml_inserts" \G
*************************** 1. row ***************************
           NAME: dml_inserts
      SUBSYSTEM: dml
          COUNT: 184491
      MAX_COUNT: 184491
      MIN_COUNT: NULL
      AVG_COUNT: 29.04746136845529
    COUNT_RESET: 184491
MAX_COUNT_RESET: 184491
MIN_COUNT_RESET: NULL
AVG_COUNT_RESET: NULL
   TIME_ENABLED: 2024-01-01 07:57:24
  TIME_DISABLED: NULL
   TIME_ELAPSED: 6351
     TIME_RESET: NULL
         STATUS: enabled
           TYPE: status_counter
        COMMENT: Number of rows inserted
1 row in set (0.00 sec)
```
テーブルに 3 行のデータを挿入します
```Sql
mysql> INSERT INTO t1 values(1);
Query OK, 1 row affected (0.01 sec)

mysql> INSERT INTO t1 values(2);
Query OK, 1 row affected (0.00 sec)

mysql> INSERT INTO t1 values(3);
Query OK, 1 row affected (0.00 sec)
```
dml_inserts カウンタデータを取得するために再度 INNODB_METRICS テーブルをクエリーします
```Sql
mysql> SELECT * FROM INFORMATION_SCHEMA.INNODB_METRICS WHERE NAME="dml_inserts"\G
*************************** 1. row ***************************
           NAME: dml_inserts
      SUBSYSTEM: dml
          COUNT: 184494
      MAX_COUNT: 184494
      MIN_COUNT: NULL
      AVG_COUNT: 28.723923222313523
    COUNT_RESET: 184494
MAX_COUNT_RESET: 184494
MIN_COUNT_RESET: NULL
AVG_COUNT_RESET: NULL
   TIME_ENABLED: 2024-01-01 07:57:24
  TIME_DISABLED: NULL
   TIME_ELAPSED: 6423
     TIME_RESET: NULL
         STATUS: enabled
           TYPE: status_counter
        COMMENT: Number of rows inserted
1 row in set (0.00 sec)
```
dml_inserts カウンタをリセットし、INNODB_METRICS テーブルで dml_inserts カウンタデータを再度クエリーします
```Sql
mysql> SET GLOBAL innodb_monitor_reset = dml_inserts;
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT * FROM INFORMATION_SCHEMA.INNODB_METRICS WHERE NAME="dml_inserts"\G
*************************** 1. row ***************************
           NAME: dml_inserts
      SUBSYSTEM: dml
          COUNT: 184494
      MAX_COUNT: 184494
      MIN_COUNT: NULL
      AVG_COUNT: 28.539825783313105
    COUNT_RESET: 0
MAX_COUNT_RESET: 0
MIN_COUNT_RESET: NULL
AVG_COUNT_RESET: 0
   TIME_ENABLED: 2024-01-01 07:57:24
  TIME_DISABLED: NULL
   TIME_ELAPSED: 6464
     TIME_RESET: 2024-01-01 09:45:01
         STATUS: enabled
           TYPE: status_counter
        COMMENT: Number of rows inserted
1 row in set (0.00 sec)
```
すべてのカウンタ値をリセットするには、まずそのカウンタを無効にする必要があります。 カウンタを無効にすると、STATUS 値が disabled に設定されます。
```Sql
mysql> SET GLOBAL innodb_monitor_disable = dml_inserts;
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT * FROM INFORMATION_SCHEMA.INNODB_METRICS WHERE NAME="dml_inserts"\G
*************************** 1. row ***************************
           NAME: dml_inserts
      SUBSYSTEM: dml
          COUNT: 184494
      MAX_COUNT: 184494
      MIN_COUNT: NULL
      AVG_COUNT: 28.388394215845302
    COUNT_RESET: 0
MAX_COUNT_RESET: 0
MIN_COUNT_RESET: NULL
AVG_COUNT_RESET: 0
   TIME_ENABLED: 2024-01-01 07:57:24
  TIME_DISABLED: 2024-01-01 09:45:43
   TIME_ELAPSED: 6499
     TIME_RESET: NULL
         STATUS: disabled
           TYPE: status_counter
        COMMENT: Number of rows inserted
1 row in set (0.00 sec)
```
カウンタが無効になったら、innodb_monitor_reset_all オプションを使用して、すべてのカウンタ値をリセットできます
```Sql
mysql> SET GLOBAL innodb_monitor_reset_all = dml_inserts;
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT * FROM INFORMATION_SCHEMA.INNODB_METRICS WHERE NAME="dml_inserts"\G
*************************** 1. row ***************************
           NAME: dml_inserts
      SUBSYSTEM: dml
          COUNT: 0
      MAX_COUNT: NULL
      MIN_COUNT: NULL
      AVG_COUNT: NULL
    COUNT_RESET: 0
MAX_COUNT_RESET: NULL
MIN_COUNT_RESET: NULL
AVG_COUNT_RESET: NULL
   TIME_ENABLED: NULL
  TIME_DISABLED: NULL
   TIME_ELAPSED: NULL
     TIME_RESET: NULL
         STATUS: disabled
           TYPE: status_counter
        COMMENT: Number of rows inserted
1 row in set (0.00 sec)
```

### 15.15.7 InnoDB INFORMATION_SCHEMA 一時テーブル情報テーブル
NNODB_TEMP_TABLE_INFO は、InnoDB インスタンスでアクティブなユーザー作成の InnoDB 一時テーブルに関する情報を提供します
```sql
mysql> SHOW TABLES FROM INFORMATION_SCHEMA LIKE 'INNODB_TEMP%';
+---------------------------------------------+
| Tables_in_information_schema (INNODB_TEMP%) |
+---------------------------------------------+
| INNODB_TEMP_TABLE_INFO                      |
+---------------------------------------------+
1 row in set (0.05 sec)
```

INNODB_TEMP_TABLE_INFO

単純な InnoDB 一時テーブルを作成します
```sql
mysql> CREATE TEMPORARY TABLE t1 (c1 INT PRIMARY KEY) ENGINE=INNODB;
Query OK, 0 rows affected (0.00 sec)
```
INNODB_TEMP_TABLE_INFO をクエリーして、一時テーブルのメタデータを表示します
```sql
mysql> SELECT * FROM INFORMATION_SCHEMA.INNODB_TEMP_TABLE_INFO\G
*************************** 1. row ***************************
TABLE_ID: 1110
    NAME: #sql125c_8_1
  N_COLS: 4
   SPACE: 4243767290
1 row in set (0.00 sec)
```
MySQL を再起動し、INNODB_TEMP_TABLE_INFO をクエリーします
```sql
mysql> RESTART;
Query OK, 0 rows affected (0.00 sec)
mysql> SELECT * FROM INFORMATION_SCHEMA.INNODB_TEMP_TABLE_INFO\G
ERROR 2013 (HY000): Lost connection to MySQL server during query
No connection. Trying to reconnect...
Connection id:    8
Current database: test

Empty set (0.01 sec)
```
新しい一時テーブルを作成します
```Sql
mysql> CREATE TEMPORARY TABLE t1 (c1 INT PRIMARY KEY) ENGINE=INNODB;
Query OK, 0 rows affected (0.00 sec)
```
INNODB_TEMP_TABLE_INFO をクエリーして、一時テーブルのメタデータを表示します
```sql
mysql> SELECT * FROM INFORMATION_SCHEMA.INNODB_TEMP_TABLE_INFO\G
*************************** 1. row ***************************
TABLE_ID: 1111
    NAME: #sql2f1c_8_1
  N_COLS: 4
   SPACE: 4243767290
1 row in set (0.00 sec)
```

### 15.15.8 INFORMATION_SCHEMA.FILES からの InnoDB テーブルスペースメタデータの取得
InnoDB システムテーブルスペースに関するメタデータを、InnoDB テーブルスペースに関連する INFORMATION_SCHEMA.FILES テーブルのフィールドから取得します

```Sql

mysql> SELECT FILE_ID, FILE_NAME, FILE_TYPE, TABLESPACE_NAME, FREE_EXTENTS,
    ->        TOTAL_EXTENTS,  EXTENT_SIZE, INITIAL_SIZE, MAXIMUM_SIZE, AUTOEXTEND_SIZE, DATA_FREE, STATUS ENGINE
    ->        FROM INFORMATION_SCHEMA.FILES WHERE TABLESPACE_NAME LIKE 'innodb_system' \G
*************************** 1. row ***************************
        FILE_ID: 0
      FILE_NAME: ./ibdata1
      FILE_TYPE: TABLESPACE
TABLESPACE_NAME: innodb_system
   FREE_EXTENTS: 2
  TOTAL_EXTENTS: 12
    EXTENT_SIZE: 1048576
   INITIAL_SIZE: 12582912
   MAXIMUM_SIZE: NULL
AUTOEXTEND_SIZE: 67108864
      DATA_FREE: 6291456
         ENGINE: NORMAL
1 row in set (0.01 sec)
```
このクエリーは、InnoDB file-per-table および一般テーブルスペースの FILE_ID (スペース ID と同等) および FILE_NAME (パス情報を含む) を取得します
```Sql
mysql> SELECT FILE_ID, FILE_NAME FROM INFORMATION_SCHEMA.FILES
    ->        WHERE FILE_NAME LIKE '%.ibd%' ORDER BY FILE_ID;
+------------+------------------------------------------------------------+
| FILE_ID    | FILE_NAME                                                  |
+------------+------------------------------------------------------------+
|          1 | ./sys/sys_config.ibd                                       |
|          2 | ./sakila/actor.ibd                                         |
|          3 | ./sakila/address.ibd                                       |
|          4 | ./sakila/category.ibd                                      |
|          5 | ./sakila/city.ibd                                          |
|          6 | ./sakila/country.ibd                                       |
|          7 | ./sakila/customer.ibd                                      |
|          8 | ./sakila/film.ibd                                          |
|          9 | ./sakila/film_actor.ibd                                    |
|         10 | ./sakila/film_category.ibd                                 |
|         11 | ./sakila/film_text.ibd                                     |
|         12 | ./sakila/fts_0000000000000431_being_deleted.ibd            |
|         13 | ./sakila/fts_0000000000000431_being_deleted_cache.ibd      |
|         14 | ./sakila/fts_0000000000000431_config.ibd                   |
|         15 | ./sakila/fts_0000000000000431_deleted.ibd                  |
|         16 | ./sakila/fts_0000000000000431_deleted_cache.ibd            |
|         17 | ./sakila/fts_0000000000000431_00000000000000b6_index_1.ibd |
|         18 | ./sakila/fts_0000000000000431_00000000000000b6_index_2.ibd |
|         19 | ./sakila/fts_0000000000000431_00000000000000b6_index_3.ibd |
|         20 | ./sakila/fts_0000000000000431_00000000000000b6_index_4.ibd |
|         21 | ./sakila/fts_0000000000000431_00000000000000b6_index_5.ibd |
|         22 | ./sakila/fts_0000000000000431_00000000000000b6_index_6.ibd |
|         23 | ./sakila/inventory.ibd                                     |
|         24 | ./sakila/language.ibd                                      |
|         25 | ./sakila/payment.ibd                                       |
|         26 | ./sakila/rental.ibd                                        |
|         27 | ./sakila/staff.ibd                                         |
|         28 | ./sakila/store.ibd                                         |
|         29 | ./world/city.ibd                                           |
|         30 | ./world/country.ibd                                        |
|         31 | ./world/countrylanguage.ibd                                |
|         33 | ./test/parent.ibd                                          |
|         34 | ./test/child.ibd                                           |
|         35 | ./test/articles.ibd                                        |
|         36 | ./test/fts_0000000000000449_being_deleted.ibd              |
|         37 | ./test/fts_0000000000000449_being_deleted_cache.ibd        |
|         38 | ./test/fts_0000000000000449_config.ibd                     |
|         39 | ./test/fts_0000000000000449_deleted.ibd                    |
|         40 | ./test/fts_0000000000000449_deleted_cache.ibd              |
|         41 | ./test/fts_0000000000000449_00000000000000e1_index_1.ibd   |
|         42 | ./test/fts_0000000000000449_00000000000000e1_index_2.ibd   |
|         43 | ./test/fts_0000000000000449_00000000000000e1_index_3.ibd   |
|         44 | ./test/fts_0000000000000449_00000000000000e1_index_4.ibd   |
|         45 | ./test/fts_0000000000000449_00000000000000e1_index_5.ibd   |
|         46 | ./test/fts_0000000000000449_00000000000000e1_index_6.ibd   |
| 4294967294 | ./mysql.ibd                                                |
+------------+------------------------------------------------------------+
46 rows in set (0.01 sec)
```
このクエリーは、InnoDB グローバル一時テーブルスペースの FILE_ID および FILE_NAME を取得します
```Sql
mysql> SELECT FILE_ID, FILE_NAME FROM INFORMATION_SCHEMA.FILES
    ->        WHERE FILE_NAME LIKE '%ibtmp%';
+------------+-----------+
| FILE_ID    | FILE_NAME |
+------------+-----------+
| 4294967293 | ./ibtmp1  |
+------------+-----------+
1 row in set (0.00 sec)
```
同様に、InnoDB undo テーブルスペースのファイル名には undo という接頭辞が付きます
```Sql
mysql> SELECT FILE_ID, FILE_NAME FROM INFORMATION_SCHEMA.FILES
    ->        WHERE FILE_NAME LIKE '%undo%';
+------------+------------+
| FILE_ID    | FILE_NAME  |
+------------+------------+
| 4294967279 | ./undo_001 |
| 4294967278 | ./undo_002 |
+------------+------------+
2 rows in set (0.00 sec)
```
#### 15.16 InnoDB の MySQL パフォーマンススキーマとの統合
 InnoDB 関連のインストゥルメントを表示するには、innodb を含むインストゥルメント名を setup_instruments テーブルにクエリーすることができます
 ```Sql
 mysql> SELECT *
    ->        FROM performance_schema.setup_instruments
    ->        WHERE NAME LIKE '%innodb%' limit 5\G
*************************** 1. row ***************************
         NAME: wait/synch/mutex/innodb/commit_cond_mutex
      ENABLED: NO
        TIMED: NO
   PROPERTIES:
        FLAGS: NULL
   VOLATILITY: 0
DOCUMENTATION: NULL
*************************** 2. row ***************************
         NAME: wait/synch/mutex/innodb/innobase_share_mutex
      ENABLED: NO
        TIMED: NO
   PROPERTIES:
        FLAGS: NULL
   VOLATILITY: 0
DOCUMENTATION: NULL
*************************** 3. row ***************************
         NAME: wait/synch/mutex/innodb/resume_encryption_cond_mutex
      ENABLED: NO
        TIMED: NO
   PROPERTIES:
        FLAGS: NULL
   VOLATILITY: 0
DOCUMENTATION: NULL
*************************** 4. row ***************************
         NAME: wait/synch/mutex/innodb/autoinc_mutex
      ENABLED: NO
        TIMED: NO
   PROPERTIES:
        FLAGS: NULL
   VOLATILITY: 0
DOCUMENTATION: NULL
*************************** 5. row ***************************
         NAME: wait/synch/mutex/innodb/autoinc_persisted_mutex
      ENABLED: NO
        TIMED: NO
   PROPERTIES:
        FLAGS: NULL
   VOLATILITY: 0
DOCUMENTATION: NULL
5 rows in set (0.00 sec)
```
ファイル I/O インストゥルメンテーションの実行時にパフォーマンススキーマに表示されるインストゥルメントされた InnoDB ファイルオブジェクトに関する情報を表示するには、次のクエリーを発行します
```sql
mysql> SELECT *
    ->        FROM performance_schema.file_instances
    ->        WHERE EVENT_NAME LIKE '%innodb%' limit 5\G
*************************** 1. row ***************************
 FILE_NAME: C:\ProgramData\MySQL\MySQL Server 8.2\Data\ibdata1
EVENT_NAME: wait/io/file/innodb/innodb_data_file
OPEN_COUNT: 3
*************************** 2. row ***************************
 FILE_NAME: C:\ProgramData\MySQL\MySQL Server 8.2\Data\#ib_16384_0.dblwr
EVENT_NAME: wait/io/file/innodb/innodb_dblwr_file
OPEN_COUNT: 2
*************************** 3. row ***************************
 FILE_NAME: C:\ProgramData\MySQL\MySQL Server 8.2\Data\#ib_16384_1.dblwr
EVENT_NAME: wait/io/file/innodb/innodb_dblwr_file
OPEN_COUNT: 2
*************************** 4. row ***************************
 FILE_NAME: C:\ProgramData\MySQL\MySQL Server 8.2\Data\#innodb_redo\#ib_redo9
EVENT_NAME: wait/io/file/innodb/innodb_log_file
OPEN_COUNT: 12
*************************** 5. row ***************************
 FILE_NAME: C:\ProgramData\MySQL\MySQL Server 8.2\Data\#innodb_redo\#ib_redo10_tmp
EVENT_NAME: wait/io/file/innodb/innodb_log_file
OPEN_COUNT: 1
5 rows in set (0.00 sec)
```


### 15.16.1 パフォーマンススキーマを使用した InnoDB テーブルの ALTER TABLE の進行状況のモニタリング
stage/innodb/alter% インストゥルメントを有効にします
```sql
mysql> UPDATE performance_schema.setup_instruments
    ->        SET ENABLED = 'YES'
    ->        WHERE NAME LIKE 'stage/innodb/alter%';
Query OK, 0 rows affected (0.00 sec)
Rows matched: 8  Changed: 0  Warnings: 0
```
ステージイベントコンシューマテーブル (events_stages_current、events_stages_history および events_stages_history_long を含む) を有効にします
```Sql
mysql> UPDATE performance_schema.setup_consumers
    ->        SET ENABLED = 'YES'
    ->        WHERE NAME LIKE '%stages%';
Query OK, 3 rows affected (0.00 sec)
Rows matched: 3  Changed: 3  Warnings: 0
```
ALTER TABLE 操作を実行します。
```Sql
mysql> ALTER TABLE test.t ADD COLUMN middle_name varchar(14) AFTER a;
Query OK, 0 rows affected (0.02 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
パフォーマンススキーマ events_stages_current テーブルをクエリーして、ALTER TABLE 操作の進行状況を確認します  
ALTER TABLE 操作が完了すると、events_stages_current テーブルは空のセットを返します
```Sql
mysql> SELECT EVENT_NAME, WORK_COMPLETED, WORK_ESTIMATED
    ->        FROM performance_schema.events_stages_current;
Empty set (0.00 sec)
```

### 15.16.2 パフォーマンススキーマを使用した InnoDB Mutex 待機のモニタリング
mutex は、特定の時間に 1 つのスレッドのみが共通リソースにアクセスできるように強制するために、コードで使用される同期メカニズムです

設定ファイルを書き換えます
```sql
[mysqld]
performance-schema-instrument='wait/synch/mutex/innodb/%=ON'
```

使用可能な InnoDB mutex 待機インストゥルメントを表示するには、パフォーマンススキーマ setup_instruments テーブルをクエリーします
```Sql
mysql> SELECT *
    ->        FROM performance_schema.setup_instruments
    ->        WHERE NAME LIKE '%wait/synch/mutex/innodb%' limit 5\G
*************************** 1. row ***************************
         NAME: wait/synch/mutex/innodb/commit_cond_mutex
      ENABLED: NO
        TIMED: NO
   PROPERTIES:
        FLAGS: NULL
   VOLATILITY: 0
DOCUMENTATION: NULL
*************************** 2. row ***************************
         NAME: wait/synch/mutex/innodb/innobase_share_mutex
      ENABLED: NO
        TIMED: NO
   PROPERTIES:
        FLAGS: NULL
   VOLATILITY: 0
DOCUMENTATION: NULL
*************************** 3. row ***************************
         NAME: wait/synch/mutex/innodb/resume_encryption_cond_mutex
      ENABLED: NO
        TIMED: NO
   PROPERTIES:
        FLAGS: NULL
   VOLATILITY: 0
DOCUMENTATION: NULL
*************************** 4. row ***************************
         NAME: wait/synch/mutex/innodb/autoinc_mutex
      ENABLED: NO
        TIMED: NO
   PROPERTIES:
        FLAGS: NULL
   VOLATILITY: 0
DOCUMENTATION: NULL
*************************** 5. row ***************************
         NAME: wait/synch/mutex/innodb/autoinc_persisted_mutex
      ENABLED: NO
        TIMED: NO
   PROPERTIES:
        FLAGS: NULL
   VOLATILITY: 0
DOCUMENTATION: NULL
5 rows in set (0.00 sec)
```
待機イベントコンシューマが有効になっていることを確認するには、setup_consumers テーブルをクエリーします
```Sql
mysql> SELECT * FROM performance_schema.setup_consumers;
+----------------------------------+---------+
| NAME                             | ENABLED |
+----------------------------------+---------+
| events_stages_current            | YES     |
| events_stages_history            | YES     |
| events_stages_history_long       | YES     |
| events_statements_cpu            | NO      |
| events_statements_current        | YES     |
| events_statements_history        | YES     |
| events_statements_history_long   | NO      |
| events_transactions_current      | YES     |
| events_transactions_history      | YES     |
| events_transactions_history_long | NO      |
| events_waits_current             | NO      |
| events_waits_history             | NO      |
| events_waits_history_long        | NO      |
| global_instrumentation           | YES     |
| thread_instrumentation           | YES     |
| statements_digest                | YES     |
+----------------------------------+---------+
16 rows in set (0.00 sec)
```
次のクエリーは、インストゥルメント名 (EVENT_NAME)、待機イベントの数 (COUNT_STAR) およびそのインストゥルメントのイベントの合計待機時間 (SUM_TIMER_WAIT) を返します
```Sql
mysql> SELECT EVENT_NAME, COUNT_STAR, SUM_TIMER_WAIT/1000000000 SUM_TIMER_WAIT_MS
    ->        FROM performance_schema.events_waits_summary_global_by_event_name
    ->        WHERE SUM_TIMER_WAIT > 0 AND EVENT_NAME LIKE 'wait/synch/mutex/innodb/%'
    ->        ORDER BY COUNT_STAR DESC;
+---------------------------------------------------------+------------+-------------------+
| EVENT_NAME                                              | COUNT_STAR | SUM_TIMER_WAIT_MS |
+---------------------------------------------------------+------------+-------------------+
| wait/synch/mutex/innodb/trx_mutex                       |     399193 |           26.8631 |
| wait/synch/mutex/innodb/fil_system_mutex                |      53229 |            7.3090 |
| wait/synch/mutex/innodb/trx_sys_mutex                   |      28882 |           13.3390 |
| wait/synch/mutex/innodb/undo_space_rseg_mutex           |      25150 |            6.9291 |
| wait/synch/mutex/innodb/lock_sys_page_mutex             |      14547 |            1.3388 |
| wait/synch/mutex/innodb/trx_sys_shard_mutex             |      13097 |            2.1026 |
| wait/synch/mutex/innodb/lock_sys_table_mutex            |      12486 |            3.2359 |
| wait/synch/mutex/innodb/dict_sys_mutex                  |      10900 |            3.5276 |
| wait/synch/mutex/innodb/log_files_mutex                 |      10379 |          416.1684 |
| wait/synch/mutex/innodb/log_limits_mutex                |       8815 |            3.8818 |
| wait/synch/mutex/innodb/sync_array_mutex                |       8102 |            1.0699 |
| wait/synch/mutex/innodb/log_governor_mutex              |       8099 |           20.7252 |
| wait/synch/mutex/innodb/log_flusher_mutex               |       7858 |           10.5369 |
| wait/synch/mutex/innodb/trx_undo_mutex                  |       7637 |            0.6883 |
| wait/synch/mutex/innodb/buf_pool_free_list_mutex        |       6886 |            0.5150 |
| wait/synch/mutex/innodb/log_write_notifier_mutex        |       6548 |            2.7242 |
| wait/synch/mutex/innodb/log_flush_notifier_mutex        |       6493 |            2.9341 |
| wait/synch/mutex/innodb/buf_pool_LRU_list_mutex         |       6376 |            0.4004 |
| wait/synch/mutex/innodb/log_writer_mutex                |       5488 |           32.1415 |
| wait/synch/mutex/innodb/flush_list_mutex                |       4968 |            0.6433 |
| wait/synch/mutex/innodb/trx_pool_mutex                  |       4510 |         6930.1623 |
| wait/synch/mutex/innodb/srv_sys_mutex                   |       4078 |          840.0238 |
| wait/synch/mutex/innodb/dict_table_mutex                |       3704 |            0.4348 |
| wait/synch/mutex/innodb/buf_pool_flush_state_mutex      |       2492 |            0.4358 |
| wait/synch/mutex/innodb/trx_pool_manager_mutex          |       2258 |            0.3633 |
| wait/synch/mutex/innodb/innobase_share_mutex            |       2212 |            0.2487 |
| wait/synch/mutex/innodb/srv_threads_mutex               |       1642 |            0.3932 |
| wait/synch/mutex/innodb/trx_sys_serialisation_mutex     |       1365 |            0.7084 |
| wait/synch/mutex/innodb/dblwr_mutex                     |       1323 |            0.2139 |
| wait/synch/mutex/innodb/purge_sys_pq_mutex              |        816 |            0.1317 |
| wait/synch/mutex/innodb/rw_lock_list_mutex              |        556 |            0.0647 |
| wait/synch/mutex/innodb/recalc_pool_mutex               |        401 |            0.0865 |
| wait/synch/mutex/innodb/lock_wait_mutex                 |        385 |            0.1094 |
| wait/synch/mutex/innodb/autoinc_mutex                   |        360 |            0.0570 |
| wait/synch/mutex/innodb/autoinc_persisted_mutex         |        332 |            0.0317 |
| wait/synch/mutex/innodb/temp_space_rseg_mutex           |        256 |            0.0303 |
| wait/synch/mutex/innodb/log_checkpointer_mutex          |        135 |            0.0955 |
| wait/synch/mutex/innodb/row_drop_list_mutex             |        128 |            0.0840 |
| wait/synch/mutex/innodb/page_cleaner_mutex              |        113 |            0.0252 |
| wait/synch/mutex/innodb/parser_mutex                    |        111 |            0.0172 |
| wait/synch/mutex/innodb/dict_persist_dirty_tables_mutex |         86 |            0.0142 |
| wait/synch/mutex/innodb/clone_sys_mutex                 |         50 |            0.0286 |
| wait/synch/mutex/innodb/ibuf_mutex                      |         19 |            0.0038 |
| wait/synch/mutex/innodb/recv_sys_mutex                  |          9 |            0.0018 |
| wait/synch/mutex/innodb/page_sys_arch_oper_mutex        |          5 |            0.0013 |
| wait/synch/mutex/innodb/srv_innodb_monitor_mutex        |          2 |            0.0010 |
| wait/synch/mutex/innodb/recv_writer_mutex               |          1 |            0.0001 |
+---------------------------------------------------------+------------+-------------------+
47 rows in set (0.03 sec)
```
### 15.17.2 InnoDB モニターの有効化
InnoDB モニターで定期出力が有効になっている場合、InnoDB は約 15 秒ごとに mysqld サーバーの標準エラー出力 (stderr) に出力を書き込みます。

InnoDB 標準モニターの有効化

サーバーをシャットダウンすると、innodb_status_output 変数がデフォルトの OFF 値に設定されます
```sql
mysql> SET GLOBAL innodb_status_output=ON;
Query OK, 0 rows affected (0.00 sec)
```

InnoDB ロックモニターの有効化
```sql
mysql> SET GLOBAL innodb_status_output=ON;
Query OK, 0 rows affected (0.00 sec)

mysql> SET GLOBAL innodb_status_output_locks=ON;
Query OK, 0 rows affected (0.00 sec)
```
オンデマンドでの InnoDB 標準モニターの出力の取得
```sql
mysql> SHOW ENGINE INNODB STATUS\G
*************************** 1. row ***************************
  Type: InnoDB
  Name:
Status:
=====================================
2024-01-01 16:43:54 0x2710 INNODB MONITOR OUTPUT
=====================================
Per second averages calculated from the last 3 seconds
-----------------
BACKGROUND THREAD
-----------------
srv_master_thread loops: 17 srv_active, 0 srv_shutdown, 1611 srv_idle
srv_master_thread log flush and writes: 0
----------
SEMAPHORES
----------
OS WAIT ARRAY INFO: reservation count 3051
OS WAIT ARRAY INFO: signal count 1941
RW-shared spins 0, rounds 0, OS waits 0
RW-excl spins 0, rounds 0, OS waits 0
RW-sx spins 0, rounds 0, OS waits 0
Spin rounds per wait: 0.00 RW-shared, 0.00 RW-excl, 0.00 RW-sx
------------
TRANSACTIONS
------------
Trx id counter 15712
Purge done for trx's n:o < 15712 undo n:o < 0 state: running but idle
History list length 0
LIST OF TRANSACTIONS FOR EACH SESSION:
---TRANSACTION 283234748405504, not started
0 lock struct(s), heap size 1128, 0 row lock(s)
---TRANSACTION 283234748404728, not started
0 lock struct(s), heap size 1128, 0 row lock(s)
---TRANSACTION 283234748403952, not started
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
1170 OS file reads, 7582 OS file writes, 5268 OS fsyncs
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
Hash table size 34679, node heap has 1 buffer(s)
Hash table size 34679, node heap has 0 buffer(s)
Hash table size 34679, node heap has 1 buffer(s)
0.00 hash searches/s, 0.00 non-hash searches/s
---
LOG
---
Log sequence number          49430562
Log buffer assigned up to    49430562
Log buffer completed up to   49430562
Log written up to            49430562
Log flushed up to            49430562
Added dirty pages up to      49430562
Pages flushed up to          49430562
Last checkpoint at           49430562
Log minimum file id is       12
Log maximum file id is       15
5581 log i/o's done, 0.00 log i/o's/second
----------------------
BUFFER POOL AND MEMORY
----------------------
Total large memory allocated 0
Dictionary memory allocated 518679
Buffer pool size   8191
Free buffers       6811
Database pages     1376
Old database pages 501
Modified db pages  0
Pending reads      0
Pending writes: LRU 0, flush list 0, single page 0
Pages made young 1031, not young 955
0.00 youngs/s, 0.00 non-youngs/s
Pages read 1148, created 642, written 1124
0.00 reads/s, 0.00 creates/s, 0.00 writes/s
No buffer pool page gets since the last printout
Pages read ahead 0.00/s, evicted without access 0.00/s, Random read ahead 0.00/s
LRU len: 1376, unzip_LRU len: 0
I/O sum[0]:cur[0], unzip sum[0]:cur[0]
--------------
ROW OPERATIONS
--------------
0 queries inside InnoDB, 0 queries in queue
0 read views open inside InnoDB
Process ID=6084, Main thread ID=11252 , state=sleeping
Number of rows inserted 5990, updated 0, deleted 0, read 1907151
0.00 inserts/s, 0.00 updates/s, 0.00 deletes/s, 0.00 reads/s
Number of system rows inserted 378, updated 651, deleted 358, read 6196
0.00 inserts/s, 0.00 updates/s, 0.00 deletes/s, 0.00 reads/s
----------------------------
END OF INNODB MONITOR OUTPUT
============================

1 row in set (0.00 sec)
```

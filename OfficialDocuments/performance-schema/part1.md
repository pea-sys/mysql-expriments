### 第 27 章 MySQL パフォーマンススキーマ

### 27.1 パフォーマンススキーマクイックスタート

```sql
mysql> SHOW VARIABLES LIKE 'performance_schema';
+--------------------+-------+
| Variable_name      | Value |
+--------------------+-------+
| performance_schema | ON    |
+--------------------+-------+
1 row in set, 1 warning (0.00 sec)
```

```sql
mysql> SELECT * FROM INFORMATION_SCHEMA.ENGINES
    ->        WHERE ENGINE='PERFORMANCE_SCHEMA'\G
*************************** 1. row ***************************
      ENGINE: PERFORMANCE_SCHEMA
     SUPPORT: YES
     COMMENT: Performance Schema
TRANSACTIONS: NO
          XA: NO
  SAVEPOINTS: NO
1 row in set (0.00 sec)

mysql> SHOW ENGINES\G
*************************** 1. row ***************************
      Engine: MEMORY
     Support: YES
     Comment: Hash based, stored in memory, useful for temporary tables
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 2. row ***************************
      Engine: MRG_MYISAM
     Support: YES
     Comment: Collection of identical MyISAM tables
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 3. row ***************************
      Engine: CSV
     Support: YES
     Comment: CSV storage engine
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 4. row ***************************
      Engine: FEDERATED
     Support: NO
     Comment: Federated MySQL storage engine
Transactions: NULL
          XA: NULL
  Savepoints: NULL
*************************** 5. row ***************************
      Engine: PERFORMANCE_SCHEMA
     Support: YES
     Comment: Performance Schema
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 6. row ***************************
      Engine: MyISAM
     Support: YES
     Comment: MyISAM storage engine
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 7. row ***************************
      Engine: InnoDB
     Support: DEFAULT
     Comment: Supports transactions, row-level locking, and foreign keys
Transactions: YES
          XA: YES
  Savepoints: YES
*************************** 8. row ***************************
      Engine: ndbinfo
     Support: NO
     Comment: MySQL Cluster system information storage engine
Transactions: NULL
          XA: NULL
  Savepoints: NULL
*************************** 9. row ***************************
      Engine: BLACKHOLE
     Support: YES
     Comment: /dev/null storage engine (anything you write to it disappears)
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 10. row ***************************
      Engine: ARCHIVE
     Support: YES
     Comment: Archive storage engine
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 11. row ***************************
      Engine: ndbcluster
     Support: NO
     Comment: Clustered, fault-tolerant tables
Transactions: NULL
          XA: NULL
  Savepoints: NULL
11 rows in set (0.00 sec)
```

```sql
mysql> SELECT TABLE_NAME FROM INFORMATION_SCHEMA.TABLES
    ->        WHERE TABLE_SCHEMA = 'performance_schema';
+------------------------------------------------------+
| TABLE_NAME                                           |
+------------------------------------------------------+
| accounts                                             |
| binary_log_transaction_compression_stats             |
| cond_instances                                       |
| data_lock_waits                                      |
| data_locks                                           |
| error_log                                            |
| events_errors_summary_by_account_by_error            |
| events_errors_summary_by_host_by_error               |
| events_errors_summary_by_thread_by_error             |
| events_errors_summary_by_user_by_error               |
| events_errors_summary_global_by_error                |
| events_stages_current                                |
| events_stages_history                                |
| events_stages_history_long                           |
| events_stages_summary_by_account_by_event_name       |
| events_stages_summary_by_host_by_event_name          |
| events_stages_summary_by_thread_by_event_name        |
| events_stages_summary_by_user_by_event_name          |
| events_stages_summary_global_by_event_name           |
| events_statements_current                            |
| events_statements_histogram_by_digest                |
| events_statements_histogram_global                   |
| events_statements_history                            |
| events_statements_history_long                       |
| events_statements_summary_by_account_by_event_name   |
| events_statements_summary_by_digest                  |
| events_statements_summary_by_host_by_event_name      |
| events_statements_summary_by_program                 |
| events_statements_summary_by_thread_by_event_name    |
| events_statements_summary_by_user_by_event_name      |
| events_statements_summary_global_by_event_name       |
| events_transactions_current                          |
| events_transactions_history                          |
| events_transactions_history_long                     |
| events_transactions_summary_by_account_by_event_name |
| events_transactions_summary_by_host_by_event_name    |
| events_transactions_summary_by_thread_by_event_name  |
| events_transactions_summary_by_user_by_event_name    |
| events_transactions_summary_global_by_event_name     |
| events_waits_current                                 |
| events_waits_history                                 |
| events_waits_history_long                            |
| events_waits_summary_by_account_by_event_name        |
| events_waits_summary_by_host_by_event_name           |
| events_waits_summary_by_instance                     |
| events_waits_summary_by_thread_by_event_name         |
| events_waits_summary_by_user_by_event_name           |
| events_waits_summary_global_by_event_name            |
| file_instances                                       |
| file_summary_by_event_name                           |
| file_summary_by_instance                             |
| global_status                                        |
| global_variables                                     |
| host_cache                                           |
| hosts                                                |
| innodb_redo_log_files                                |
| keyring_component_status                             |
| keyring_keys                                         |
| log_status                                           |
| memory_summary_by_account_by_event_name              |
| memory_summary_by_host_by_event_name                 |
| memory_summary_by_thread_by_event_name               |
| memory_summary_by_user_by_event_name                 |
| memory_summary_global_by_event_name                  |
| metadata_locks                                       |
| mutex_instances                                      |
| objects_summary_global_by_type                       |
| performance_timers                                   |
| persisted_variables                                  |
| prepared_statements_instances                        |
| processlist                                          |
| replication_applier_configuration                    |
| replication_applier_filters                          |
| replication_applier_global_filters                   |
| replication_applier_status                           |
| replication_applier_status_by_coordinator            |
| replication_applier_status_by_worker                 |
| replication_asynchronous_connection_failover         |
| replication_asynchronous_connection_failover_managed |
| replication_connection_configuration                 |
| replication_connection_status                        |
| replication_group_member_stats                       |
| replication_group_members                            |
| rwlock_instances                                     |
| session_account_connect_attrs                        |
| session_connect_attrs                                |
| session_status                                       |
| session_variables                                    |
| setup_actors                                         |
| setup_consumers                                      |
| setup_instruments                                    |
| setup_meters                                         |
| setup_metrics                                        |
| setup_objects                                        |
| setup_threads                                        |
| socket_instances                                     |
| socket_summary_by_event_name                         |
| socket_summary_by_instance                           |
| status_by_account                                    |
| status_by_host                                       |
| status_by_thread                                     |
| status_by_user                                       |
| table_handles                                        |
| table_io_waits_summary_by_index_usage                |
| table_io_waits_summary_by_table                      |
| table_lock_waits_summary_by_table                    |
| threads                                              |
| tls_channel_status                                   |
| user_defined_functions                               |
| user_variables_by_thread                             |
| users                                                |
| variables_by_thread                                  |
| variables_info                                       |
+------------------------------------------------------+
113 rows in set (0.01 sec)
```

個々のテーブルの構造を表示するには、SHOW CREATE TABLE を使用します

```sql
mysql> SHOW CREATE TABLE performance_schema.setup_consumers\G
*************************** 1. row ***************************
       Table: setup_consumers
Create Table: CREATE TABLE `setup_consumers` (
  `NAME` varchar(64) NOT NULL,
  `ENABLED` enum('YES','NO') NOT NULL,
  PRIMARY KEY (`NAME`)
) ENGINE=PERFORMANCE_SCHEMA DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
1 row in set (0.00 sec)
```

最初に、すべてのインストゥルメントとコンシューマが有効にされていないため、パフォーマンススキーマはすべてのイベントを収集しません。 これらのすべてをオンにし、イベントタイミングを有効にするには、2 つのステートメントを実行します。

```Sql
mysql> UPDATE performance_schema.setup_instruments
    ->        SET ENABLED = 'YES', TIMED = 'YES';
Query OK, 972 rows affected (0.01 sec)
Rows matched: 1257  Changed: 972  Warnings: 0

mysql> UPDATE performance_schema.setup_consumers
    ->        SET ENABLED = 'YES';
Query OK, 9 rows affected (0.00 sec)
Rows matched: 16  Changed: 9  Warnings: 0
```

現在サーバーが何を行なっているかを確認するには、events_waits_current テーブルを調査します

```Sql
mysql> SELECT *
    ->        FROM performance_schema.events_waits_current limit 3\G
*************************** 1. row ***************************
            THREAD_ID: 13
             EVENT_ID: 315
         END_EVENT_ID: 315
           EVENT_NAME: wait/synch/mutex/innodb/dblwr_mutex
               SOURCE: buf0dblwr.cc:582
          TIMER_START: 517110614880290
            TIMER_END: 517110615151554
           TIMER_WAIT: 271264
                SPINS: NULL
        OBJECT_SCHEMA: NULL
          OBJECT_NAME: NULL
           INDEX_NAME: NULL
          OBJECT_TYPE: NULL
OBJECT_INSTANCE_BEGIN: 2738249667112
     NESTING_EVENT_ID: NULL
   NESTING_EVENT_TYPE: NULL
            OPERATION: lock
      NUMBER_OF_BYTES: NULL
                FLAGS: NULL
*************************** 2. row ***************************
            THREAD_ID: 14
             EVENT_ID: 220
         END_EVENT_ID: 220
           EVENT_NAME: wait/synch/mutex/innodb/log_limits_mutex
               SOURCE: log0chkp.cc:888
          TIMER_START: 517110660227396
            TIMER_END: 517110660267532
           TIMER_WAIT: 40136
                SPINS: NULL
        OBJECT_SCHEMA: NULL
          OBJECT_NAME: NULL
           INDEX_NAME: NULL
          OBJECT_TYPE: NULL
OBJECT_INSTANCE_BEGIN: 2738434608064
     NESTING_EVENT_ID: NULL
   NESTING_EVENT_TYPE: NULL
            OPERATION: lock
      NUMBER_OF_BYTES: NULL
                FLAGS: NULL
*************************** 3. row ***************************
            THREAD_ID: 15
             EVENT_ID: 410
         END_EVENT_ID: 410
           EVENT_NAME: wait/synch/mutex/innodb/log_flush_notifier_mutex
               SOURCE: log0write.cc:2794
          TIMER_START: 517392236529036
            TIMER_END: 517392236953924
           TIMER_WAIT: 424888
                SPINS: NULL
        OBJECT_SCHEMA: NULL
          OBJECT_NAME: NULL
           INDEX_NAME: NULL
          OBJECT_TYPE: NULL
OBJECT_INSTANCE_BEGIN: 2738434606736
     NESTING_EVENT_ID: NULL
   NESTING_EVENT_TYPE: NULL
            OPERATION: lock
      NUMBER_OF_BYTES: NULL
                FLAGS: NULL
3 rows in set (0.00 sec)
```

スレッド 13 によって生成された最新イベントの情報を表示するには、次を実行します

```Sql
mysql> SELECT EVENT_ID, EVENT_NAME, TIMER_WAIT
    ->        FROM performance_schema.events_waits_history
    ->        WHERE THREAD_ID = 13
    ->        ORDER BY EVENT_ID;
+----------+----------------------------------------------------+------------+
| EVENT_ID | EVENT_NAME                                         | TIMER_WAIT |
+----------+----------------------------------------------------+------------+
|     2212 | wait/synch/mutex/innodb/flush_list_mutex           |      45672 |
|     2213 | wait/synch/mutex/innodb/buf_pool_flush_state_mutex |      45326 |
|     2214 | wait/synch/mutex/innodb/dblwr_mutex                |     331468 |
|     2215 | wait/synch/mutex/innodb/flush_list_mutex           |     615880 |
|     2216 | wait/synch/mutex/innodb/log_limits_mutex           |     133556 |
|     2217 | wait/synch/mutex/innodb/log_limits_mutex           |     127328 |
|     2218 | wait/synch/mutex/innodb/buf_pool_flush_state_mutex |     284066 |
|     2219 | wait/synch/mutex/innodb/flush_list_mutex           |     120408 |
|     2220 | wait/synch/mutex/innodb/buf_pool_flush_state_mutex |     124214 |
|     2221 | wait/synch/mutex/innodb/dblwr_mutex                |     592006 |
+----------+----------------------------------------------------+------------+
10 rows in set (0.01 sec)
```

履歴テーブルに新しいイベントが追加されると、テーブルがいっぱいである場合、古いイベントが破棄されます

```sql
mysql> SELECT EVENT_NAME, COUNT_STAR
    ->        FROM performance_schema.events_waits_summary_global_by_event_name
    ->        ORDER BY COUNT_STAR DESC LIMIT 10;
+-----------------------------------------------------+------------+
| EVENT_NAME                                          | COUNT_STAR |
+-----------------------------------------------------+------------+
| wait/synch/mutex/innodb/fil_system_mutex            |      28912 |
| wait/synch/mutex/innodb/log_limits_mutex            |      28842 |
| wait/synch/mutex/innodb/log_files_mutex             |      27187 |
| wait/synch/mutex/innodb/log_governor_mutex          |      26715 |
| wait/synch/mutex/innodb/log_write_notifier_mutex    |       3952 |
| wait/synch/mutex/innodb/log_flush_notifier_mutex    |       3951 |
| wait/synch/mutex/innodb/log_writer_mutex            |       3951 |
| wait/synch/mutex/innodb/trx_sys_serialisation_mutex |       3951 |
| wait/synch/mutex/innodb/log_flusher_mutex           |       3951 |
| wait/io/file/innodb/innodb_data_file                |       2130 |
+-----------------------------------------------------+------------+
10 rows in set (0.01 sec)
```

```Sql
mysql> SELECT EVENT_NAME, SUM_TIMER_WAIT
    ->        FROM performance_schema.events_waits_summary_global_by_event_name
    ->        ORDER BY SUM_TIMER_WAIT DESC LIMIT 10;
+---------------------------------------------------------+------------------+
| EVENT_NAME                                              | SUM_TIMER_WAIT   |
+---------------------------------------------------------+------------------+
| idle                                                    | 1808285763000000 |
| wait/synch/cond/mysqlx/scheduler_dynamic_worker_pending |  841052934732622 |
| wait/io/file/innodb/innodb_data_file                    |    2329349583580 |
| wait/io/file/innodb/innodb_log_file                     |     688887727232 |
| wait/synch/mutex/innodb/log_governor_mutex              |      94823915414 |
| wait/io/file/innodb/innodb_temp_file                    |      66533825724 |
| wait/io/file/innodb/innodb_dblwr_file                   |      48127269284 |
| wait/synch/mutex/innodb/log_writer_mutex                |      22848431780 |
| wait/synch/mutex/innodb/log_limits_mutex                |      11325057134 |
| wait/synch/mutex/innodb/log_files_mutex                 |      10069179204 |
+---------------------------------------------------------+------------------+
10 rows in set (0.01 sec)
```

file_instances テーブルは、ファイル I/O 操作のインストゥルメントのインスタンスとそれらに関連付けられたファイルを一覧表示します

```sql
mysql> SELECT *
    ->        FROM performance_schema.file_instances limit 5\G
*************************** 1. row ***************************
 FILE_NAME: C:\Program Files\MySQL\MySQL Server 8.2\share\english\errmsg.sys
EVENT_NAME: wait/io/file/sql/ERRMSG
OPEN_COUNT: 0
*************************** 2. row ***************************
 FILE_NAME: C:\Program Files\MySQL\MySQL Server 8.2\share\charsets\Index.xml
EVENT_NAME: wait/io/file/mysys/charset
OPEN_COUNT: 0
*************************** 3. row ***************************
 FILE_NAME: C:\ProgramData\MySQL\MySQL Server 8.2\Data\auto.cnf
EVENT_NAME: wait/io/file/mysys/cnf
OPEN_COUNT: 0
*************************** 4. row ***************************
 FILE_NAME: C:\ProgramData\MySQL\MySQL Server 8.2\Data\ibdata1
EVENT_NAME: wait/io/file/innodb/innodb_data_file
OPEN_COUNT: 3
*************************** 5. row ***************************
 FILE_NAME: C:\ProgramData\MySQL\MySQL Server 8.2\Data\#ib_16384_0.dblwr
EVENT_NAME: wait/io/file/innodb/innodb_dblwr_file
OPEN_COUNT: 2
5 rows in set (0.00 sec)
```

セットアップテーブルは、モニタリング特性の構成と表示に使われます

```Sql
mysql> SELECT NAME, ENABLED, TIMED
    ->        FROM performance_schema.setup_instruments limit 5;
+-------------------------------------------------------+---------+-------+
| NAME                                                  | ENABLED | TIMED |
+-------------------------------------------------------+---------+-------+
| wait/synch/mutex/pfs/LOCK_pfs_share_list              | YES     | YES   |
| wait/synch/mutex/pfs/LOCK_pfs_tracing_callback        | YES     | YES   |
| wait/synch/mutex/sql/TC_LOG_MMAP::LOCK_tc             | YES     | YES   |
| wait/synch/mutex/sql/MYSQL_BIN_LOG::LOCK_commit       | YES     | YES   |
| wait/synch/mutex/sql/MYSQL_BIN_LOG::LOCK_commit_queue | YES     | YES   |
+-------------------------------------------------------+---------+-------+
5 rows in set (0.00 sec)
```

インストゥルメントのイベントを収集するかどうかを制御するには、その ENABLED 値を YES または NO に設定します

```Sql
mysql> UPDATE performance_schema.setup_instruments
    ->        SET ENABLED = 'NO'
    ->        WHERE NAME = 'wait/synch/mutex/sql/LOCK_mysql_create_db';
Query OK, 0 rows affected (0.00 sec)
Rows matched: 0  Changed: 0  Warnings: 0
```

setup_consumers テーブルは、使用可能なコンシューマとどれが有効にされているかを示します

```Sql
mysql> SELECT * FROM performance_schema.setup_consumers;
+----------------------------------+---------+
| NAME                             | ENABLED |
+----------------------------------+---------+
| events_stages_current            | YES     |
| events_stages_history            | YES     |
| events_stages_history_long       | YES     |
| events_statements_cpu            | YES     |
| events_statements_current        | YES     |
| events_statements_history        | YES     |
| events_statements_history_long   | YES     |
| events_transactions_current      | YES     |
| events_transactions_history      | YES     |
| events_transactions_history_long | YES     |
| events_waits_current             | YES     |
| events_waits_history             | YES     |
| events_waits_history_long        | YES     |
| global_instrumentation           | YES     |
| thread_instrumentation           | YES     |
| statements_digest                | YES     |
+----------------------------------+---------+
16 rows in set (0.00 sec)
```

### 27.4 パフォーマンススキーマ実行時構成

パフォーマンススキーマセットアップテーブルには、モニタリング構成に関する情報が含まれます

```Sql
mysql> SELECT TABLE_NAME FROM INFORMATION_SCHEMA.TABLES
    ->        WHERE TABLE_SCHEMA = 'performance_schema'
    ->        AND TABLE_NAME LIKE 'setup%';
+-------------------+
| TABLE_NAME        |
+-------------------+
| setup_actors      |
| setup_consumers   |
| setup_instruments |
| setup_meters      |
| setup_metrics     |
| setup_objects     |
| setup_threads     |
+-------------------+
7 rows in set (0.01 sec)
```

### 27.4.1 パフォーマンススキーマイベントタイミング

パフォーマンススキーマタイマーは、精度とオーバーヘッドの量が異なります。 使用可能なタイマーとそれらの特性を確認するには、performance_timers テーブルをチェックしてください

```sql
mysql> SELECT * FROM performance_schema.performance_timers;
+-------------+-----------------+------------------+----------------+
| TIMER_NAME  | TIMER_FREQUENCY | TIMER_RESOLUTION | TIMER_OVERHEAD |
+-------------+-----------------+------------------+----------------+
| CYCLE       |      2887711442 |                1 |             21 |
| NANOSECOND  |            NULL |             NULL |           NULL |
| MICROSECOND |        10000000 |                1 |             40 |
| MILLISECOND |            1297 |               15 |              0 |
| THREAD_CPU  |       998337875 |             1000 |           1212 |
+-------------+-----------------+------------------+----------------+
5 rows in set (0.01 sec)
```

### 27.4.2 パフォーマンススキーマイベントフィルタリング

イベントはプロデューサ/コンシューマ方式で処理されます

インストゥルメントされたコードは、イベントのソースで、収集されるイベントを生成します。 setup_instruments テーブルは、イベントを収集できるインストゥルメント、それらが有効にされているかどうか、および (有効にされているインストゥルメントの場合) タイミング情報を収集するかどうかを一覧表示します

```Sql
mysql> SELECT NAME, ENABLED, TIMED
    ->        FROM performance_schema.setup_instruments limit 5;
+-------------------------------------------------------+---------+-------+
| NAME                                                  | ENABLED | TIMED |
+-------------------------------------------------------+---------+-------+
| wait/synch/mutex/pfs/LOCK_pfs_share_list              | NO      | NO    |
| wait/synch/mutex/pfs/LOCK_pfs_tracing_callback        | NO      | NO    |
| wait/synch/mutex/sql/TC_LOG_MMAP::LOCK_tc             | NO      | NO    |
| wait/synch/mutex/sql/MYSQL_BIN_LOG::LOCK_commit       | NO      | NO    |
| wait/synch/mutex/sql/MYSQL_BIN_LOG::LOCK_commit_queue | NO      | NO    |
+-------------------------------------------------------+---------+-------+
5 rows in set (0.00 sec)
```

setup_consumers テーブルは、イベント情報を送信できるコンシューマの種類とそれらが有効にされているかどうかを一覧表示します

```Sql
mysql> SELECT * FROM performance_schema.setup_consumers limit 5;
+----------------------------+---------+
| NAME                       | ENABLED |
+----------------------------+---------+
| events_stages_current      | NO      |
| events_stages_history      | NO      |
| events_stages_history_long | NO      |
| events_statements_cpu      | NO      |
| events_statements_current  | YES     |
+----------------------------+---------+
5 rows in set (0.00 sec)
```

### 27.4.4 インストゥルメントによる事前フィルタリング

setup_instruments テーブルは使用可能なインストゥルメントを一覧表示します

```sql
mysql> SELECT NAME, ENABLED, TIMED
    ->        FROM performance_schema.setup_instruments limit 5;
+-------------------------------------------------------+---------+-------+
| NAME                                                  | ENABLED | TIMED |
+-------------------------------------------------------+---------+-------+
| wait/synch/mutex/pfs/LOCK_pfs_share_list              | NO      | NO    |
| wait/synch/mutex/pfs/LOCK_pfs_tracing_callback        | NO      | NO    |
| wait/synch/mutex/sql/TC_LOG_MMAP::LOCK_tc             | NO      | NO    |
| wait/synch/mutex/sql/MYSQL_BIN_LOG::LOCK_commit       | NO      | NO    |
| wait/synch/mutex/sql/MYSQL_BIN_LOG::LOCK_commit_queue | NO      | NO    |
+-------------------------------------------------------+---------+-------+
5 rows in set (0.00 sec)
```

インストゥルメントを有効にするかどうかを制御するには、その ENABLED カラムを YES または NO に設定します。 有効にされたインストゥルメントのタイミング情報を収集するかどうかを構成するには、その TIMED 値を YES または NO に設定します

すべてのインストゥルメントを無効にします

```Sql
mysql> UPDATE performance_schema.setup_instruments
    -> SET ENABLED = 'NO';
Query OK, 796 rows affected (0.01 sec)
Rows matched: 1257  Changed: 796  Warnings: 0
```

すべてのインストゥルメントを無効にし、それらを現在の無効にされているインストゥルメントのセットに追加します

```sql
mysql> UPDATE performance_schema.setup_instruments
    -> SET ENABLED = 'NO'
    -> WHERE NAME LIKE 'wait/io/file/%';
Query OK, 0 rows affected (0.01 sec)
Rows matched: 50  Changed: 0  Warnings: 0
```

mysys ライブラリ内のインストゥルメントを除くすべてのインストゥルメントを有効にします

```Sql
mysql> UPDATE performance_schema.setup_instruments
    -> SET ENABLED = CASE WHEN NAME LIKE '%/mysys/%' THEN 'YES' ELSE 'NO' END;
Query OK, 118 rows affected (0.00 sec)
Rows matched: 1257  Changed: 118  Warnings: 0
```

特定のインストゥルメントを無効にします

```sql
mysql> UPDATE performance_schema.setup_instruments
    -> SET ENABLED = 'NO'
    -> WHERE NAME = 'wait/synch/mutex/mysys/TMPDIR_mutex';
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0
```

インストゥルメントの状態を切り替えるには、その ENABLED 値を「反転」します

```Sql
mysql> UPDATE performance_schema.setup_instruments
    -> SET ENABLED = IF(ENABLED = 'YES', 'NO', 'YES')
    -> WHERE NAME = 'wait/synch/mutex/mysys/TMPDIR_mutex';
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0
```

すべてのイベントのタイミングを無効にします

```Sql
mysql> UPDATE performance_schema.setup_instruments
    -> SET TIMED = 'NO';
Query OK, 796 rows affected (0.00 sec)
Rows matched: 1257  Changed: 796  Warnings: 0
```

### 27.4.5 オブジェクトによる事前フィルタリング

setup_objects テーブルは、パフォーマンススキーマが特定のテーブルおよびストアドプログラムオブジェクトをモニターするかどうかを制御します

```sql
mysql> table performance_schema.setup_objects;
+-------------+--------------------+-------------+---------+-------+
| OBJECT_TYPE | OBJECT_SCHEMA      | OBJECT_NAME | ENABLED | TIMED |
+-------------+--------------------+-------------+---------+-------+
| EVENT       | mysql              | %           | NO      | NO    |
| EVENT       | performance_schema | %           | NO      | NO    |
| EVENT       | information_schema | %           | NO      | NO    |
| EVENT       | %                  | %           | YES     | YES   |
| FUNCTION    | mysql              | %           | NO      | NO    |
| FUNCTION    | performance_schema | %           | NO      | NO    |
| FUNCTION    | information_schema | %           | NO      | NO    |
| FUNCTION    | %                  | %           | YES     | YES   |
| PROCEDURE   | mysql              | %           | NO      | NO    |
| PROCEDURE   | performance_schema | %           | NO      | NO    |
| PROCEDURE   | information_schema | %           | NO      | NO    |
| PROCEDURE   | %                  | %           | YES     | YES   |
| TABLE       | mysql              | %           | NO      | NO    |
| TABLE       | performance_schema | %           | NO      | NO    |
| TABLE       | information_schema | %           | NO      | NO    |
| TABLE       | %                  | %           | YES     | YES   |
| TRIGGER     | mysql              | %           | NO      | NO    |
| TRIGGER     | performance_schema | %           | NO      | NO    |
| TRIGGER     | information_schema | %           | NO      | NO    |
| TRIGGER     | %                  | %           | YES     | YES   |
+-------------+--------------------+-------------+---------+-------+
20 rows in set (0.00 sec)
```

### 27.4.7 コンシューマによる事前フィルタリング

setup_consumers テーブルは使用可能なコンシューマの種類とどれが有効にされているかを一覧表示します

```sql
mysql> table performance_schema.setup_consumers;
+----------------------------------+---------+
| NAME                             | ENABLED |
+----------------------------------+---------+
| events_stages_current            | NO      |
| events_stages_history            | NO      |
| events_stages_history_long       | NO      |
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

コンシューマを無効にすると、サーバーはそのコンシューマの宛先の保守に時間を費やさなくなります。 たとえば、履歴イベント情報に関心がない場合、履歴コンシューマを無効にします

```sql
mysql> UPDATE performance_schema.setup_consumers
    -> SET ENABLED = 'NO'
    -> WHERE NAME LIKE '%history%';
Query OK, 2 rows affected (0.00 sec)
Rows matched: 8  Changed: 2  Warnings: 0
```

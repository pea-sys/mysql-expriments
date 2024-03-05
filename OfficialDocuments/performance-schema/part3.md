### 27.12 パフォーマンススキーマテーブルの説明

### 27.12.2 パフォーマンススキーマセットアップテーブル

セットアップテーブルは現在のインストゥルメンテーションに関する情報を提供し、モニタリング構成の変更を可能にします。 このため、UPDATE 権限を持つ場合、これらのテーブルの一部のカラムを変更できます。

### 27.12.2.1 setup_actors テーブル

新しいフォアグラウンドサーバースレッド (クライアント接続に関連付けられたスレッド) の監視および履歴イベントロギングを有効にするかどうかを決定する情報が含まれます

```sql
mysql> table performance_schema.setup_actors;
+------+------+------+---------+---------+
| HOST | USER | ROLE | ENABLED | HISTORY |
+------+------+------+---------+---------+
| %    | %    | %    | YES     | YES     |
+------+------+------+---------+---------+
1 row in set (0.01 sec)
```

### 27.12.2.2 setup_consumers テーブル

イベント情報を格納でき、有効にされているコンシューマの種類を一覧表示します

```Sql
mysql> table performance_schema.setup_consumers;
+----------------------------------+---------+
| NAME                             | ENABLED |
+----------------------------------+---------+
| events_stages_current            | NO      |
| events_stages_history            | NO      |
| events_stages_history_long       | NO      |
| events_statements_cpu            | NO      |
| events_statements_current        | YES     |
| events_statements_history        | NO      |
| events_statements_history_long   | NO      |
| events_transactions_current      | YES     |
| events_transactions_history      | NO      |
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

### 27.12.2.3 setup_instruments テーブル

イベントを収集できるインストゥルメントされたオブジェクトのクラスを一覧表示します

```Sql
mysql> table  performance_schema.setup_instruments limit 3 \G
*************************** 1. row ***************************
         NAME: wait/synch/mutex/pfs/LOCK_pfs_share_list
      ENABLED: NO
        TIMED: NO
   PROPERTIES: singleton
        FLAGS: NULL
   VOLATILITY: 1
DOCUMENTATION: Components can provide their own performance_schema tables. This lock protects the list of such tables definitions.
*************************** 2. row ***************************
         NAME: wait/synch/mutex/pfs/LOCK_pfs_tracing_callback
      ENABLED: NO
        TIMED: NO
   PROPERTIES: singleton
        FLAGS: NULL
   VOLATILITY: 1
DOCUMENTATION: This lock protects telemetry trace callback functions.
*************************** 3. row ***************************
         NAME: wait/synch/mutex/sql/TC_LOG_MMAP::LOCK_tc
      ENABLED: NO
        TIMED: NO
   PROPERTIES:
        FLAGS: NULL
   VOLATILITY: 0
DOCUMENTATION: NULL
3 rows in set (0.00 sec)
```

### 27.12.2.4 setup_objects テーブル

パフォーマンススキーマが特定のオブジェクトをモニターするかどうかを制御します

```Sql

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
20 rows in set (0.03 sec)
```

### 27.12.2.5 setup_threads テーブル

インストゥルメントスレッドクラスがリストされます

```Sql
mysql> table performance_schema.setup_threads limit 3\G
*************************** 1. row ***************************
         NAME: thread/performance_schema/setup
      ENABLED: YES
      HISTORY: YES
   PROPERTIES: singleton
   VOLATILITY: 0
DOCUMENTATION: NULL
*************************** 2. row ***************************
         NAME: thread/sql/con_named_pipes
      ENABLED: YES
      HISTORY: YES
   PROPERTIES: singleton
   VOLATILITY: 0
DOCUMENTATION: NULL
*************************** 3. row ***************************
         NAME: thread/sql/con_shared_mem
      ENABLED: YES
      HISTORY: YES
   PROPERTIES: singleton
   VOLATILITY: 0
DOCUMENTATION: NULL
3 rows in set (0.00 sec)
```

### 27.12.3 パフォーマンススキーマインスタンステーブル

### 27.12.3.1 cond_instances テーブル

サーバーの実行中にパフォーマンススキーマによって確認されるすべての条件を一覧表示します

```sql
mysql> table performance_schema.cond_instances limit 3;
+----------------------------------------------+-----------------------+
| NAME                                         | OBJECT_INSTANCE_BEGIN |
+----------------------------------------------+-----------------------+
| wait/synch/cond/sql/COND_manager             |       140695573630168 |
| wait/synch/cond/sql/COND_server_started      |       140695572934752 |
| wait/synch/cond/sql/COND_compress_gtid_table |       140695572934768 |
+----------------------------------------------+-----------------------+
3 rows in set (0.00 sec)
```

### 27.12.3.2 file_instances テーブル

ファイル I/O インストゥルメンテーションの実行中にパフォーマンススキーマによって確認されるすべてのファイルを一覧表示します

```Sql
mysql> table performance_schema.file_instances limit 3\G
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
3 rows in set (0.00 sec)
```

### 27.12.3.3 mutex_instances テーブル

サーバーの実行中にパフォーマンススキーマによって確認されるすべての相互排他ロックを一覧表示します

```Sql
mysql> table performance_schema.mutex_instances limit 3\G
*************************** 1. row ***************************
                 NAME: wait/synch/mutex/pfs/LOCK_pfs_share_list
OBJECT_INSTANCE_BEGIN: 140695573936520
  LOCKED_BY_THREAD_ID: NULL
*************************** 2. row ***************************
                 NAME: wait/synch/mutex/pfs/LOCK_pfs_tracing_callback
OBJECT_INSTANCE_BEGIN: 140695573961992
  LOCKED_BY_THREAD_ID: NULL
*************************** 3. row ***************************
                 NAME: wait/synch/mutex/mysys/THR_LOCK_heap
OBJECT_INSTANCE_BEGIN: 140695573701992
  LOCKED_BY_THREAD_ID: NULL
3 rows in set (0.00 sec)
```

### 27.12.3.4 rwlock_instances テーブル

サーバーの実行中にパフォーマンススキーマによって認識されるすべての rwlock (読み取り/書き込みロック) インスタンスが一覧表示されます

```Sql
mysql> table performance_schema.rwlock_instances limit 3\G
*************************** 1. row ***************************
                     NAME: wait/synch/rwlock/pfs/LOCK_pfs_tls_channels
    OBJECT_INSTANCE_BEGIN: 140695573958672
WRITE_LOCKED_BY_THREAD_ID: NULL
     READ_LOCKED_BY_COUNT: 0
*************************** 2. row ***************************
                     NAME: wait/synch/rwlock/pfs/LOCK_pfs_metrics
    OBJECT_INSTANCE_BEGIN: 140695573927000
WRITE_LOCKED_BY_THREAD_ID: NULL
     READ_LOCKED_BY_COUNT: 0
*************************** 3. row ***************************
                     NAME: wait/synch/rwlock/pfs/LOCK_pfs_meter_notify
    OBJECT_INSTANCE_BEGIN: 140695573927024
WRITE_LOCKED_BY_THREAD_ID: NULL
     READ_LOCKED_BY_COUNT: 0
3 rows in set (0.00 sec)
```

### 27.12.3.5 socket_instances テーブル

MySQL サーバーへのアクティブな接続のリアルタイムスナップショットを提供します

```Sql
mysql> table performance_schema.socket_instances;
+----------------------------------------+-----------------------+-----------+-----------+----+-------+--------+
| EVENT_NAME                             | OBJECT_INSTANCE_BEGIN | THREAD_ID | SOCKET_ID | IP | PORT  | STATE  |
+----------------------------------------+-----------------------+-----------+-----------+----+-------+--------+
| wait/io/socket/mysqlx/tcpip_socket     |         1780723090232 |        33 |      2024 | :: | 33060 | ACTIVE |
| wait/io/socket/sql/server_tcpip_socket |         1780723090544 |         1 |      1992 | :: |  3306 | ACTIVE |
+----------------------------------------+-----------------------+-----------+-----------+----+-------+--------+
2 rows in set (0.00 sec)
```

### 27.12.4 パフォーマンススキーマ待機イベントテーブル

### 27.12.4.1 events_waits_current テーブル

現在の待機イベントが含まれます

```Sql
mysql> table performance_schema.events_waits_current;
Empty set (0.01 sec)
```

### 27.12.4.2 events_waits_history テーブル

スレッドごとに終了した N の最新の待機イベントが含まれます

```sql
mysql> table performance_schema.events_waits_history;
Empty set (0.00 sec)
```

### 27.12.4.3 events_waits_history_long テーブル

べてのスレッドでグローバルに終了した最新の待機イベントが N に含まれます

```Sql
mysql> table performance_schema.events_waits_history_long;
Empty set (0.00 sec)
```

### 27.12.5 パフォーマンススキーマステージイベントテーブル

### 27.12.5.1 events_stages_current テーブル

現在のステージイベントが含まれます

```sql
mysql> table performance_schema.events_stages_current;
+-----------+----------+--------------+---------------------------------------+-------------------------+-----------------+-----------------+-----------------+----------------+----------------+------------------+--------------------+
| THREAD_ID | EVENT_ID | END_EVENT_ID | EVENT_NAME                            | SOURCE                  | TIMER_START     | TIMER_END       | TIMER_WAIT      | WORK_COMPLETED | WORK_ESTIMATED | NESTING_EVENT_ID | NESTING_EVENT_TYPE |
+-----------+----------+--------------+---------------------------------------+-------------------------+-----------------+-----------------+-----------------+----------------+----------------+------------------+--------------------+
|        38 |        3 |         NULL | stage/sql/waiting for handler commit  | handler.cc:1622         |   4269076600000 | 178424179900000 | 174155103300000 |           NULL |           NULL |             NULL | NULL               |
|        43 |        1 |         NULL | stage/sql/Waiting for next activation | event_queue.cc:579      |   4539931600000 | 178425209800000 | 173885278200000 |           NULL |           NULL |             NULL | NULL               |
|        46 |        7 |         NULL | stage/sql/Suspending                  | rpl_gtid_persist.cc:760 |   4552993400000 | 178425215200000 | 173872221800000 |           NULL |           NULL |             NULL | NULL               |
|        50 |       40 |         NULL | stage/sql/executing                   | sql_union.cc:1676       | 178424148200000 | 178425218500000 |      1070300000 |           NULL |           NULL |               31 | STATEMENT          |
+-----------+----------+--------------+---------------------------------------+-------------------------+-----------------+-----------------+-----------------+----------------+----------------+------------------+--------------------+
4 rows in set (0.00 sec)
```

### 27.12.5.2 events_stages_history テーブル

スレッドごとに終了した N の最新のステージイベントが含まれます

```sql
mysql> table performance_schema.events_stages_history limit 3\G
*************************** 1. row ***************************
         THREAD_ID: 1
          EVENT_ID: 80
      END_EVENT_ID: 81
        EVENT_NAME: stage/sql/waiting for handler commit
            SOURCE: handler.cc:1622
       TIMER_START: 4522318500000
         TIMER_END: 4524779200000
        TIMER_WAIT: 2460700000
    WORK_COMPLETED: NULL
    WORK_ESTIMATED: NULL
  NESTING_EVENT_ID: NULL
NESTING_EVENT_TYPE: NULL
*************************** 2. row ***************************
         THREAD_ID: 1
          EVENT_ID: 82
      END_EVENT_ID: 82
        EVENT_NAME: stage/sql/waiting for handler commit
            SOURCE: handler.cc:1622
       TIMER_START: 4524779200000
         TIMER_END: 4524790100000
        TIMER_WAIT: 10900000
    WORK_COMPLETED: NULL
    WORK_ESTIMATED: NULL
  NESTING_EVENT_ID: NULL
NESTING_EVENT_TYPE: NULL
*************************** 3. row ***************************
         THREAD_ID: 1
          EVENT_ID: 83
      END_EVENT_ID: 84
        EVENT_NAME: stage/sql/waiting for handler commit
            SOURCE: handler.cc:1622
       TIMER_START: 4524790100000
         TIMER_END: 4525395000000
        TIMER_WAIT: 604900000
    WORK_COMPLETED: NULL
    WORK_ESTIMATED: NULL
  NESTING_EVENT_ID: NULL
NESTING_EVENT_TYPE: NULL
3 rows in set (0.00 sec)
```

### 27.12.5.3 events_stages_history_long テーブル

すべてのスレッドでグローバルに終了した N の最新のステージイベントが含まれます

```Sql
mysql> table performance_schema.events_stages_history_long limit 3\G
*************************** 1. row ***************************
         THREAD_ID: 3
          EVENT_ID: 1
      END_EVENT_ID: 1
        EVENT_NAME: stage/sql/Executing hook on transaction begin.
            SOURCE: rpl_handler.cc:1483
       TIMER_START: 3636993200000
         TIMER_END: 3636994200000
        TIMER_WAIT: 1000000
    WORK_COMPLETED: NULL
    WORK_ESTIMATED: NULL
  NESTING_EVENT_ID: NULL
NESTING_EVENT_TYPE: NULL
*************************** 2. row ***************************
         THREAD_ID: 3
          EVENT_ID: 2
      END_EVENT_ID: 2
        EVENT_NAME: stage/sql/waiting for handler commit
            SOURCE: handler.cc:1622
       TIMER_START: 3637011100000
         TIMER_END: 3637129900000
        TIMER_WAIT: 118800000
    WORK_COMPLETED: NULL
    WORK_ESTIMATED: NULL
  NESTING_EVENT_ID: NULL
NESTING_EVENT_TYPE: NULL
*************************** 3. row ***************************
         THREAD_ID: 3
          EVENT_ID: 3
      END_EVENT_ID: 3
        EVENT_NAME: stage/sql/checking permissions
            SOURCE: sql_authorization.cc:2169
       TIMER_START: 3637129900000
         TIMER_END: 3638117600000
        TIMER_WAIT: 987700000
    WORK_COMPLETED: NULL
    WORK_ESTIMATED: NULL
  NESTING_EVENT_ID: NULL
NESTING_EVENT_TYPE: NULL
3 rows in set (0.00 sec)
```

### 27.12.6 パフォーマンススキーマステートメントイベントテーブル

### 27.12.6.1 events_statements_current テーブル

現在のステートメントイベントが含まれます

```sql
mysql> table performance_schema.events_statements_current\G
*************************** 1. row ***************************
              THREAD_ID: 50
               EVENT_ID: 24
           END_EVENT_ID: NULL
             EVENT_NAME: statement/sql/select
                 SOURCE: init_net_server_extension.cc:104
            TIMER_START: 50357818500000
              TIMER_END: 50358069000000
             TIMER_WAIT: 250500000
              LOCK_TIME: 1000000
               SQL_TEXT: table performance_schema.events_statements_current
                 DIGEST: c083c0e1d6b8c10ca336861318f2340d7f8634b72bec5e3e0984746b98e1beab
            DIGEST_TEXT: TABLE `performance_schema` . `events_statements_current`
         CURRENT_SCHEMA: NULL
            OBJECT_TYPE: NULL
          OBJECT_SCHEMA: NULL
            OBJECT_NAME: NULL
  OBJECT_INSTANCE_BEGIN: NULL
            MYSQL_ERRNO: 0
      RETURNED_SQLSTATE: NULL
           MESSAGE_TEXT: NULL
                 ERRORS: 0
               WARNINGS: 0
          ROWS_AFFECTED: 0
              ROWS_SENT: 0
          ROWS_EXAMINED: 0
CREATED_TMP_DISK_TABLES: 0
     CREATED_TMP_TABLES: 0
       SELECT_FULL_JOIN: 0
 SELECT_FULL_RANGE_JOIN: 0
           SELECT_RANGE: 0
     SELECT_RANGE_CHECK: 0
            SELECT_SCAN: 1
      SORT_MERGE_PASSES: 0
             SORT_RANGE: 0
              SORT_ROWS: 0
              SORT_SCAN: 0
          NO_INDEX_USED: 1
     NO_GOOD_INDEX_USED: 0
       NESTING_EVENT_ID: NULL
     NESTING_EVENT_TYPE: NULL
    NESTING_EVENT_LEVEL: 0
           STATEMENT_ID: 5
               CPU_TIME: 0
  MAX_CONTROLLED_MEMORY: 0
       MAX_TOTAL_MEMORY: 0
       EXECUTION_ENGINE: PRIMARY
1 row in set (0.00 sec)
```

### 27.12.6.2 events_statements_history テーブル

スレッドごとに終了した N の最新のステートメントイベントが含まれます

```sql
mysql> table performance_schema.events_statements_history limit 1\G
*************************** 1. row ***************************
              THREAD_ID: 50
               EVENT_ID: 1
           END_EVENT_ID: 4
             EVENT_NAME: statement/sql/error
                 SOURCE: init_net_server_extension.cc:104
            TIMER_START: 32825773000000
              TIMER_END: 32825898700000
             TIMER_WAIT: 125700000
              LOCK_TIME: 0
               SQL_TEXT: table events_statements_current
                 DIGEST: NULL
            DIGEST_TEXT: NULL
         CURRENT_SCHEMA: NULL
            OBJECT_TYPE: NULL
          OBJECT_SCHEMA: NULL
            OBJECT_NAME: NULL
  OBJECT_INSTANCE_BEGIN: NULL
            MYSQL_ERRNO: 1046
      RETURNED_SQLSTATE: 3D000
           MESSAGE_TEXT: No database selected
                 ERRORS: 1
               WARNINGS: 0
          ROWS_AFFECTED: 0
              ROWS_SENT: 0
          ROWS_EXAMINED: 0
CREATED_TMP_DISK_TABLES: 0
     CREATED_TMP_TABLES: 0
       SELECT_FULL_JOIN: 0
 SELECT_FULL_RANGE_JOIN: 0
           SELECT_RANGE: 0
     SELECT_RANGE_CHECK: 0
            SELECT_SCAN: 0
      SORT_MERGE_PASSES: 0
             SORT_RANGE: 0
              SORT_ROWS: 0
              SORT_SCAN: 0
          NO_INDEX_USED: 0
     NO_GOOD_INDEX_USED: 0
       NESTING_EVENT_ID: NULL
     NESTING_EVENT_TYPE: NULL
    NESTING_EVENT_LEVEL: 0
           STATEMENT_ID: 2
               CPU_TIME: 0
  MAX_CONTROLLED_MEMORY: 17208
       MAX_TOTAL_MEMORY: 68081
       EXECUTION_ENGINE: PRIMARY
1 row in set (0.00 sec)
```

### 27.12.6.3 events_statements_history_long テーブル

すべてのスレッドでグローバルに終了した N の最新のステートメントイベントが含まれます

```Sql
mysql> table performance_schema.events_statements_history_long limit 1\G
*************************** 1. row ***************************
              THREAD_ID: 50
               EVENT_ID: 1
           END_EVENT_ID: 4
             EVENT_NAME: statement/sql/error
                 SOURCE: init_net_server_extension.cc:104
            TIMER_START: 32825773000000
              TIMER_END: 32825898700000
             TIMER_WAIT: 125700000
              LOCK_TIME: 0
               SQL_TEXT: table events_statements_current
                 DIGEST: NULL
            DIGEST_TEXT: NULL
         CURRENT_SCHEMA: NULL
            OBJECT_TYPE: NULL
          OBJECT_SCHEMA: NULL
            OBJECT_NAME: NULL
  OBJECT_INSTANCE_BEGIN: NULL
            MYSQL_ERRNO: 1046
      RETURNED_SQLSTATE: 3D000
           MESSAGE_TEXT: No database selected
                 ERRORS: 1
               WARNINGS: 0
          ROWS_AFFECTED: 0
              ROWS_SENT: 0
          ROWS_EXAMINED: 0
CREATED_TMP_DISK_TABLES: 0
     CREATED_TMP_TABLES: 0
       SELECT_FULL_JOIN: 0
 SELECT_FULL_RANGE_JOIN: 0
           SELECT_RANGE: 0
     SELECT_RANGE_CHECK: 0
            SELECT_SCAN: 0
      SORT_MERGE_PASSES: 0
             SORT_RANGE: 0
              SORT_ROWS: 0
              SORT_SCAN: 0
          NO_INDEX_USED: 0
     NO_GOOD_INDEX_USED: 0
       NESTING_EVENT_ID: NULL
     NESTING_EVENT_TYPE: NULL
    NESTING_EVENT_LEVEL: 0
           STATEMENT_ID: 2
               CPU_TIME: 0
  MAX_CONTROLLED_MEMORY: 17208
       MAX_TOTAL_MEMORY: 68081
       EXECUTION_ENGINE: PRIMARY
1 row in set (0.00 sec)
```

### 27.12.6.4 prepared_statements_instances テーブル

プリペアドステートメントのインストゥルメンテーションを提供します

```Sql
mysql> table performance_schema.prepared_statements_instances;
Empty set (0.01 sec)
```

### 27.12.7 パフォーマンススキーマのトランザクションテーブル

### 27.12.7.1 events_transactions_current テーブル

現在のトランザクションイベントが含まれます

```Sql
mysql> table performance_schema.events_transactions_current limit 1 \G
*************************** 1. row ***************************
                      THREAD_ID: 1
                       EVENT_ID: 23
                   END_EVENT_ID: 23
                     EVENT_NAME: transaction
                          STATE: COMMITTED
                         TRX_ID: 282968202480504
                           GTID: AUTOMATIC
                  XID_FORMAT_ID: NULL
                      XID_GTRID: NULL
                      XID_BQUAL: NULL
                       XA_STATE: NULL
                         SOURCE: handler.cc:1360
                    TIMER_START: 4633598200000
                      TIMER_END: 4633618800000
                     TIMER_WAIT: 20600000
                    ACCESS_MODE: READ WRITE
                ISOLATION_LEVEL: REPEATABLE READ
                     AUTOCOMMIT: YES
           NUMBER_OF_SAVEPOINTS: 0
NUMBER_OF_ROLLBACK_TO_SAVEPOINT: 0
    NUMBER_OF_RELEASE_SAVEPOINT: 0
          OBJECT_INSTANCE_BEGIN: NULL
               NESTING_EVENT_ID: NULL
             NESTING_EVENT_TYPE: NULL
1 row in set (0.00 sec)
```

### 27.12.7.2 events_transactions_history テーブル

スレッドごとに終了した N の最新のトランザクションイベントが含まれます

```Sql
mysql> table performance_schema.events_transactions_history limit 2 \G
*************************** 1. row ***************************
                      THREAD_ID: 1
                       EVENT_ID: 21
                   END_EVENT_ID: 21
                     EVENT_NAME: transaction
                          STATE: COMMITTED
                         TRX_ID: 282968202480504
                           GTID: AUTOMATIC
                  XID_FORMAT_ID: NULL
                      XID_GTRID: NULL
                      XID_BQUAL: NULL
                       XA_STATE: NULL
                         SOURCE: handler.cc:1360
                    TIMER_START: 4630741600000
                      TIMER_END: 4630779100000
                     TIMER_WAIT: 37500000
                    ACCESS_MODE: READ WRITE
                ISOLATION_LEVEL: REPEATABLE READ
                     AUTOCOMMIT: YES
           NUMBER_OF_SAVEPOINTS: 0
NUMBER_OF_ROLLBACK_TO_SAVEPOINT: 0
    NUMBER_OF_RELEASE_SAVEPOINT: 0
          OBJECT_INSTANCE_BEGIN: NULL
               NESTING_EVENT_ID: NULL
             NESTING_EVENT_TYPE: NULL
*************************** 2. row ***************************
                      THREAD_ID: 1
                       EVENT_ID: 22
                   END_EVENT_ID: 22
                     EVENT_NAME: transaction
                          STATE: COMMITTED
                         TRX_ID: 282968202480504
                           GTID: AUTOMATIC
                  XID_FORMAT_ID: NULL
                      XID_GTRID: NULL
                      XID_BQUAL: NULL
                       XA_STATE: NULL
                         SOURCE: handler.cc:1360
                    TIMER_START: 4633084900000
                      TIMER_END: 4633121700000
                     TIMER_WAIT: 36800000
                    ACCESS_MODE: READ WRITE
                ISOLATION_LEVEL: REPEATABLE READ
                     AUTOCOMMIT: YES
           NUMBER_OF_SAVEPOINTS: 0
NUMBER_OF_ROLLBACK_TO_SAVEPOINT: 0
    NUMBER_OF_RELEASE_SAVEPOINT: 0
          OBJECT_INSTANCE_BEGIN: NULL
               NESTING_EVENT_ID: NULL
             NESTING_EVENT_TYPE: NULL
2 rows in set (0.00 sec)
```

### 27.12.7.3 events_transactions_history_long テーブル

すべてのスレッドでグローバルに終了した N の最新のトランザクションイベントが含まれます

```Sql
mysql> table performance_schema.events_transactions_history_long limit 2 \G
*************************** 1. row ***************************
                      THREAD_ID: 3
                       EVENT_ID: 1
                   END_EVENT_ID: 1
                     EVENT_NAME: transaction
                          STATE: COMMITTED
                         TRX_ID: 282968202479728
                           GTID: AUTOMATIC
                  XID_FORMAT_ID: NULL
                      XID_GTRID: NULL
                      XID_BQUAL: NULL
                       XA_STATE: NULL
                         SOURCE: handler.cc:1360
                    TIMER_START: 4407675400000
                      TIMER_END: 4430739100000
                     TIMER_WAIT: 23063700000
                    ACCESS_MODE: READ WRITE
                ISOLATION_LEVEL: REPEATABLE READ
                     AUTOCOMMIT: NO
           NUMBER_OF_SAVEPOINTS: 0
NUMBER_OF_ROLLBACK_TO_SAVEPOINT: 0
    NUMBER_OF_RELEASE_SAVEPOINT: 0
          OBJECT_INSTANCE_BEGIN: NULL
               NESTING_EVENT_ID: NULL
             NESTING_EVENT_TYPE: NULL
*************************** 2. row ***************************
                      THREAD_ID: 1
                       EVENT_ID: 1
                   END_EVENT_ID: 1
                     EVENT_NAME: transaction
                          STATE: COMMITTED
                         TRX_ID: 282968202479728
                           GTID: AUTOMATIC
                  XID_FORMAT_ID: NULL
                      XID_GTRID: NULL
                      XID_BQUAL: NULL
                       XA_STATE: NULL
                         SOURCE: handler.cc:1360
                    TIMER_START: 4440463500000
                      TIMER_END: 4440599000000
                     TIMER_WAIT: 135500000
                    ACCESS_MODE: READ WRITE
                ISOLATION_LEVEL: REPEATABLE READ
                     AUTOCOMMIT: YES
           NUMBER_OF_SAVEPOINTS: 0
NUMBER_OF_ROLLBACK_TO_SAVEPOINT: 0
    NUMBER_OF_RELEASE_SAVEPOINT: 0
          OBJECT_INSTANCE_BEGIN: NULL
               NESTING_EVENT_ID: NULL
             NESTING_EVENT_TYPE: NULL
2 rows in set (0.00 sec)
```

### 27.12.8 パフォーマンススキーマ接続テーブル

### 27.12.8.1 accounts テーブル

MySQL サーバーに接続した各アカウントの行を格納します

```Sql
mysql> table performance_schema.accounts;
+-----------------+-----------+---------------------+-------------------+-------------------------------+--------------------------+
| USER            | HOST      | CURRENT_CONNECTIONS | TOTAL_CONNECTIONS | MAX_SESSION_CONTROLLED_MEMORY | MAX_SESSION_TOTAL_MEMORY |
+-----------------+-----------+---------------------+-------------------+-------------------------------+--------------------------+
| NULL            | NULL      |                  39 |                49 |                        272128 |
  65811327 |
| event_scheduler | localhost |                   1 |                 1 |                             0 |
     38985 |
| root            | localhost |                   1 |                 1 |                         48024 |
    400636 |
+-----------------+-----------+---------------------+-------------------+-------------------------------+--------------------------+
3 rows in set (0.00 sec)
```

### 27.12.8.2 hosts テーブル

クライアントが MySQL サーバーに接続している各ホストの行を格納します

```Sql
mysql> table performance_schema.hosts;
+-----------+---------------------+-------------------+-------------------------------+--------------------------+
| HOST      | CURRENT_CONNECTIONS | TOTAL_CONNECTIONS | MAX_SESSION_CONTROLLED_MEMORY | MAX_SESSION_TOTAL_MEMORY |
+-----------+---------------------+-------------------+-------------------------------+--------------------------+
| NULL      |                  39 |                49 |                        272128 |                 65811327 |
| localhost |                   2 |                 2 |                         48024 |                   405252 |
+-----------+---------------------+-------------------+-------------------------------+--------------------------+
2 rows in set (0.00 sec)
```

### 27.12.8.3 users テーブル

MySQL サーバーに接続している各ユーザーの行を格納します

```Sql
mysql> table performance_schema.users;
+-----------------+---------------------+-------------------+-------------------------------+--------------------------+
| USER            | CURRENT_CONNECTIONS | TOTAL_CONNECTIONS | MAX_SESSION_CONTROLLED_MEMORY | MAX_SESSION_TOTAL_MEMORY |
+-----------------+---------------------+-------------------+-------------------------------+--------------------------+
| NULL            |                  39 |                49 |                        272128 |                 65811327 |
| event_scheduler |                   1 |                 1 |                             0 |                    38985 |
| root            |                   1 |                 1 |                         48024 |                   405820 |
+-----------------+---------------------+-------------------+-------------------------------+--------------------------+
3 rows in set (0.00 sec)
```

### 27.12.9 パフォーマンススキーマ接続属性テーブル

### 27.12.9.1 session_account_connect_attrs テーブル

接続時にサーバーに渡されるキーと値の接続属性を提供できます

```sql
mysql> table performance_schema.session_account_connect_attrs;
+----------------+-----------------+------------+------------------+
| PROCESSLIST_ID | ATTR_NAME       | ATTR_VALUE | ORDINAL_POSITION |
+----------------+-----------------+------------+------------------+
|              8 | _pid            | 8496       |                0 |
|              8 | program_name    | mysql      |                1 |
|              8 | os_user         | masami     |                2 |
|              8 | _client_name    | libmysql   |                3 |
|              8 | _thread         | 2972       |                4 |
|              8 | _client_version | 8.2.0      |                5 |
|              8 | _os             | Win64      |                6 |
|              8 | _platform       | x86_64     |                7 |
+----------------+-----------------+------------+------------------+
8 rows in set (0.00 sec)
```

### 27.12.9.2 session_connect_attrs テーブル

接続時にサーバーに渡されるキーと値の接続属性を提供できます

```Sql
mysql> table performance_schema.session_connect_attrs;
+----------------+-----------------+------------+------------------+
| PROCESSLIST_ID | ATTR_NAME       | ATTR_VALUE | ORDINAL_POSITION |
+----------------+-----------------+------------+------------------+
|              8 | _pid            | 8496       |                0 |
|              8 | program_name    | mysql      |                1 |
|              8 | os_user         | masami     |                2 |
|              8 | _client_name    | libmysql   |                3 |
|              8 | _thread         | 2972       |                4 |
|              8 | _client_version | 8.2.0      |                5 |
|              8 | _os             | Win64      |                6 |
|              8 | _platform       | x86_64     |                7 |
+----------------+-----------------+------------+------------------+
8 rows in set (0.00 sec)
```

### 27.12.13 パフォーマンススキーマロックテーブル

### 27.12.13.1 data_locks テーブル

保持およびリクエストされたデータロックが表示されます

```sql
mysql> table performance_schema.data_locks;
Empty set (0.00 sec)

```

### 27.12.13.2 data_lock_waits テーブル

data_locks テーブルのどのデータロックリクエストが data_locks テーブルのどのデータロックによってブロックされているかを示す多対多関係が実装されています

```sql
mysql> table performance_schema.data_lock_waits;
Empty set (0.00 sec)
```

### 27.12.13.3 metadata_locks テーブル

メタデータロックを使用して、データベースオブジェクトへの同時アクセスを管理し、データの一貫性を確保します

```Sql
mysql> table performance_schema.metadata_locks;
+-------------+--------------------+----------------+-------------+-----------------------+-------------+---------------+-------------+-------------------+-----------------+----------------+
| OBJECT_TYPE | OBJECT_SCHEMA      | OBJECT_NAME    | COLUMN_NAME | OBJECT_INSTANCE_BEGIN | LOCK_TYPE   | LOCK_DURATION | LOCK_STATUS | SOURCE            | OWNER_THREAD_ID | OWNER_EVENT_ID |
+-------------+--------------------+----------------+-------------+-----------------------+-------------+---------------+-------------+-------------------+-----------------+----------------+
| TABLE       | performance_schema | metadata_locks | NULL        |         1493178363824 | SHARED_READ | TRANSACTION   | GRANTED     | sql_parse.cc:6252 |              50 |             22 |
+-------------+--------------------+----------------+-------------+-----------------------+-------------+---------------+-------------+-------------------+-----------------+----------------+
1 row in set (0.00 sec)
```

### 27.12.13.4 table_handles テーブル

table_handles テーブルを介してテーブルロック情報を公開し、開いているテーブルハンドルごとに現在有効なテーブルロックを表示します

### 27.12.14.1 パフォーマンススキーマ persisted_variables テーブル

永続化されたグローバルシステム変数設定を格納する mysqld-auto.cnf ファイルへの SQL インタフェースを提供し、実行時に SELECT ステートメントを使用してファイルの内容を検査できるようにします

```Sql
mysql> SET PERSIST max_connections=200;
Query OK, 0 rows affected (0.01 sec)

mysql> table performance_schema.persisted_variables;
+-----------------+----------------+
| VARIABLE_NAME   | VARIABLE_VALUE |
+-----------------+----------------+
| max_connections | 200            |
+-----------------+----------------+
1 row in set (0.00 sec)
```

### 27.12.14.2 パフォーマンススキーマ variables_info テーブル

システム変数ごとに、最後に設定されたソースとその値の範囲が表示されます

```Sql
mysql> table performance_schema.variables_info limit 2\G
*************************** 1. row ***************************
  VARIABLE_NAME: activate_all_roles_on_login
VARIABLE_SOURCE: COMPILED
  VARIABLE_PATH:
      MIN_VALUE: 0
      MAX_VALUE: 0
       SET_TIME: NULL
       SET_USER: NULL
       SET_HOST: NULL
*************************** 2. row ***************************
  VARIABLE_NAME: admin_address
VARIABLE_SOURCE: COMPILED
  VARIABLE_PATH:
      MIN_VALUE: 0
      MAX_VALUE: 0
       SET_TIME: NULL
       SET_USER: NULL
       SET_HOST: NULL
2 rows in set (0.00 sec)
```

### 27.12.18 パフォーマンススキーマサマリーテーブル

### 27.12.18.1 待機イベント要約テーブル

現在および最近の待機イベントを収集するためのテーブルを保守し、その情報をサマリーテーブルに集計します

```sql
mysql> SELECT *
    ->        FROM performance_schema.events_waits_summary_global_by_event_name limit 2\G
*************************** 1. row ***************************
    EVENT_NAME: wait/synch/mutex/pfs/LOCK_pfs_share_list
    COUNT_STAR: 0
SUM_TIMER_WAIT: 0
MIN_TIMER_WAIT: 0
AVG_TIMER_WAIT: 0
MAX_TIMER_WAIT: 0
*************************** 2. row ***************************
    EVENT_NAME: wait/synch/mutex/pfs/LOCK_pfs_tracing_callback
    COUNT_STAR: 0
SUM_TIMER_WAIT: 0
MIN_TIMER_WAIT: 0
AVG_TIMER_WAIT: 0
MAX_TIMER_WAIT: 0
2 rows in set (0.00 sec)
```

### 27.12.18.2 ステージサマリーテーブル

現在および最近のステージイベントを収集するためのテーブルを保持し、その情報をサマリーテーブルに集約します

```Sql
mysql> SELECT *
    ->        FROM performance_schema.events_stages_summary_global_by_event_name limit 2\G
*************************** 1. row ***************************
    EVENT_NAME: stage/sql/After create
    COUNT_STAR: 0
SUM_TIMER_WAIT: 0
MIN_TIMER_WAIT: 0
AVG_TIMER_WAIT: 0
MAX_TIMER_WAIT: 0
*************************** 2. row ***************************
    EVENT_NAME: stage/sql/preparing for alter table
    COUNT_STAR: 0
SUM_TIMER_WAIT: 0
MIN_TIMER_WAIT: 0
AVG_TIMER_WAIT: 0
MAX_TIMER_WAIT: 0
2 rows in set (0.00 sec)
```

### 27.12.18.3 ステートメントサマリーテーブル

現在および最近のステートメントイベントを収集するためのテーブルを保持し、その情報をサマリーテーブルに集約します

```sql
mysql> SELECT *
    ->        FROM performance_schema.events_statements_summary_global_by_event_name limit 2\G
*************************** 1. row ***************************
                 EVENT_NAME: statement/sql/select
                 COUNT_STAR: 15
             SUM_TIMER_WAIT: 59453300000
             MIN_TIMER_WAIT: 258800000
             AVG_TIMER_WAIT: 3963500000
             MAX_TIMER_WAIT: 21384400000
              SUM_LOCK_TIME: 105000000
                 SUM_ERRORS: 4
               SUM_WARNINGS: 0
          SUM_ROWS_AFFECTED: 0
              SUM_ROWS_SENT: 1409
          SUM_ROWS_EXAMINED: 1409
SUM_CREATED_TMP_DISK_TABLES: 0
     SUM_CREATED_TMP_TABLES: 0
       SUM_SELECT_FULL_JOIN: 0
 SUM_SELECT_FULL_RANGE_JOIN: 0
           SUM_SELECT_RANGE: 0
     SUM_SELECT_RANGE_CHECK: 0
            SUM_SELECT_SCAN: 10
      SUM_SORT_MERGE_PASSES: 0
             SUM_SORT_RANGE: 0
              SUM_SORT_ROWS: 0
              SUM_SORT_SCAN: 0
          SUM_NO_INDEX_USED: 10
     SUM_NO_GOOD_INDEX_USED: 0
               SUM_CPU_TIME: 0
      MAX_CONTROLLED_MEMORY: 67480
           MAX_TOTAL_MEMORY: 378962
            COUNT_SECONDARY: 0
*************************** 2. row ***************************
                 EVENT_NAME: statement/sql/create_table
                 COUNT_STAR: 0
             SUM_TIMER_WAIT: 0
             MIN_TIMER_WAIT: 0
             AVG_TIMER_WAIT: 0
             MAX_TIMER_WAIT: 0
              SUM_LOCK_TIME: 0
                 SUM_ERRORS: 0
               SUM_WARNINGS: 0
          SUM_ROWS_AFFECTED: 0
              SUM_ROWS_SENT: 0
          SUM_ROWS_EXAMINED: 0
SUM_CREATED_TMP_DISK_TABLES: 0
     SUM_CREATED_TMP_TABLES: 0
       SUM_SELECT_FULL_JOIN: 0
 SUM_SELECT_FULL_RANGE_JOIN: 0
           SUM_SELECT_RANGE: 0
     SUM_SELECT_RANGE_CHECK: 0
            SUM_SELECT_SCAN: 0
      SUM_SORT_MERGE_PASSES: 0
             SUM_SORT_RANGE: 0
              SUM_SORT_ROWS: 0
              SUM_SORT_SCAN: 0
          SUM_NO_INDEX_USED: 0
     SUM_NO_GOOD_INDEX_USED: 0
               SUM_CPU_TIME: 0
      MAX_CONTROLLED_MEMORY: 0
           MAX_TOTAL_MEMORY: 0
            COUNT_SECONDARY: 0
2 rows in set (0.00 sec)
```

### 27.12.18.4 ステートメントヒストグラム要約テーブル

ステートメントの最小待機時間、最大待機時間、および平均待機時間に関する情報を含むステートメントイベントのサマリーテーブルを保守します

```sql
mysql> SELECT *
    ->        FROM performance_schema.events_statements_histogram_by_digest
    ->        WHERE SCHEMA_NAME = 'performance_schema';
Empty set (0.00 sec)
```

### 27.12.18.5 トランザクション要約テーブル

現在および最近のトランザクションイベントを収集するためのテーブルを保持し、その情報をサマリーテーブルに集約します

```sql
mysql> SELECT *
    ->        FROM performance_schema.events_transactions_summary_global_by_event_name
    ->        LIMIT 1\G
*************************** 1. row ***************************
          EVENT_NAME: transaction
          COUNT_STAR: 27
      SUM_TIMER_WAIT: 48864100000
      MIN_TIMER_WAIT: 9800000
      AVG_TIMER_WAIT: 1809700000
      MAX_TIMER_WAIT: 32847300000
    COUNT_READ_WRITE: 27
SUM_TIMER_READ_WRITE: 48864100000
MIN_TIMER_READ_WRITE: 9800000
AVG_TIMER_READ_WRITE: 1809700000
MAX_TIMER_READ_WRITE: 32847300000
     COUNT_READ_ONLY: 0
 SUM_TIMER_READ_ONLY: 0
 MIN_TIMER_READ_ONLY: 0
 AVG_TIMER_READ_ONLY: 0
 MAX_TIMER_READ_ONLY: 0
1 row in set (0.00 sec)
```

### 27.12.18.6 オブジェクト待機サマリーテーブル

オブジェクト待機イベントを集約するための objects_summary_global_by_type テーブルを保守します

```sql
mysql> SELECT * FROM performance_schema.objects_summary_global_by_type limit 2\G
*************************** 1. row ***************************
   OBJECT_TYPE: TABLE
 OBJECT_SCHEMA: mysql
   OBJECT_NAME: dd_properties
    COUNT_STAR: 0
SUM_TIMER_WAIT: 0
MIN_TIMER_WAIT: 0
AVG_TIMER_WAIT: 0
MAX_TIMER_WAIT: 0
*************************** 2. row ***************************
   OBJECT_TYPE: TABLE
 OBJECT_SCHEMA: mysql
   OBJECT_NAME: schemata
    COUNT_STAR: 0
SUM_TIMER_WAIT: 0
MIN_TIMER_WAIT: 0
AVG_TIMER_WAIT: 0
MAX_TIMER_WAIT: 0
2 rows in set (0.00 sec)
```

### 27.12.18.7 ファイル I/O サマリーテーブル

、I/O 操作に関する情報を集約するファイル I/O サマリーテーブルを保守します

```Sql
mysql> SELECT * FROM performance_schema.file_summary_by_event_name limit 2\G
*************************** 1. row ***************************
               EVENT_NAME: wait/io/file/sql/binlog
               COUNT_STAR: 20
           SUM_TIMER_WAIT: 5471722244
           MIN_TIMER_WAIT: 0
           AVG_TIMER_WAIT: 273585904
           MAX_TIMER_WAIT: 3185101549
               COUNT_READ: 4
           SUM_TIMER_READ: 78518119
           MIN_TIMER_READ: 8871402
           AVG_TIMER_READ: 19629443
           MAX_TIMER_READ: 31290725
 SUM_NUMBER_OF_BYTES_READ: 540
              COUNT_WRITE: 2
          SUM_TIMER_WRITE: 137026483
          MIN_TIMER_WRITE: 44620383
          AVG_TIMER_WRITE: 68513068
          MAX_TIMER_WRITE: 92406100
SUM_NUMBER_OF_BYTES_WRITE: 157
               COUNT_MISC: 14
           SUM_TIMER_MISC: 5256177642
           MIN_TIMER_MISC: 0
           AVG_TIMER_MISC: 375441161
           MAX_TIMER_MISC: 3185101549
*************************** 2. row ***************************
               EVENT_NAME: wait/io/file/sql/binlog_cache
               COUNT_STAR: 0
           SUM_TIMER_WAIT: 0
           MIN_TIMER_WAIT: 0
           AVG_TIMER_WAIT: 0
           MAX_TIMER_WAIT: 0
               COUNT_READ: 0
           SUM_TIMER_READ: 0
           MIN_TIMER_READ: 0
           AVG_TIMER_READ: 0
           MAX_TIMER_READ: 0
 SUM_NUMBER_OF_BYTES_READ: 0
              COUNT_WRITE: 0
          SUM_TIMER_WRITE: 0
          MIN_TIMER_WRITE: 0
          AVG_TIMER_WRITE: 0
          MAX_TIMER_WRITE: 0
SUM_NUMBER_OF_BYTES_WRITE: 0
               COUNT_MISC: 0
           SUM_TIMER_MISC: 0
           MIN_TIMER_MISC: 0
           AVG_TIMER_MISC: 0
           MAX_TIMER_MISC: 0
2 rows in set (0.00 sec)
```

### 27.12.18.8 テーブル I/O およびロック待機サマリーテーブル

### 27.12.18.9 ソケットサマリーテーブル

ソケット操作のタイマーおよびバイトカウント情報を集計します

```sql
mysql> SELECT * FROM performance_schema.socket_summary_by_event_name limit 2\G
*************************** 1. row ***************************
               EVENT_NAME: wait/io/socket/sql/server_tcpip_socket
               COUNT_STAR: 0
           SUM_TIMER_WAIT: 0
           MIN_TIMER_WAIT: 0
           AVG_TIMER_WAIT: 0
           MAX_TIMER_WAIT: 0
               COUNT_READ: 0
           SUM_TIMER_READ: 0
           MIN_TIMER_READ: 0
           AVG_TIMER_READ: 0
           MAX_TIMER_READ: 0
 SUM_NUMBER_OF_BYTES_READ: 0
              COUNT_WRITE: 0
          SUM_TIMER_WRITE: 0
          MIN_TIMER_WRITE: 0
          AVG_TIMER_WRITE: 0
          MAX_TIMER_WRITE: 0
SUM_NUMBER_OF_BYTES_WRITE: 0
               COUNT_MISC: 0
           SUM_TIMER_MISC: 0
           MIN_TIMER_MISC: 0
           AVG_TIMER_MISC: 0
           MAX_TIMER_MISC: 0
*************************** 2. row ***************************
               EVENT_NAME: wait/io/socket/sql/server_unix_socket
               COUNT_STAR: 0
           SUM_TIMER_WAIT: 0
           MIN_TIMER_WAIT: 0
           AVG_TIMER_WAIT: 0
           MAX_TIMER_WAIT: 0
               COUNT_READ: 0
           SUM_TIMER_READ: 0
           MIN_TIMER_READ: 0
           AVG_TIMER_READ: 0
           MAX_TIMER_READ: 0
 SUM_NUMBER_OF_BYTES_READ: 0
              COUNT_WRITE: 0
          SUM_TIMER_WRITE: 0
          MIN_TIMER_WRITE: 0
          AVG_TIMER_WRITE: 0
          MAX_TIMER_WRITE: 0
SUM_NUMBER_OF_BYTES_WRITE: 0
               COUNT_MISC: 0
           SUM_TIMER_MISC: 0
           MIN_TIMER_MISC: 0
           AVG_TIMER_MISC: 0
           MAX_TIMER_MISC: 0
2 rows in set (0.00 sec)

mysql> SELECT * FROM performance_schema.socket_summary_by_instance limit 2\G
*************************** 1. row ***************************
               EVENT_NAME: wait/io/socket/mysqlx/tcpip_socket
    OBJECT_INSTANCE_BEGIN: 2373085098680
               COUNT_STAR: 0
           SUM_TIMER_WAIT: 0
           MIN_TIMER_WAIT: 0
           AVG_TIMER_WAIT: 0
           MAX_TIMER_WAIT: 0
               COUNT_READ: 0
           SUM_TIMER_READ: 0
           MIN_TIMER_READ: 0
           AVG_TIMER_READ: 0
           MAX_TIMER_READ: 0
 SUM_NUMBER_OF_BYTES_READ: 0
              COUNT_WRITE: 0
          SUM_TIMER_WRITE: 0
          MIN_TIMER_WRITE: 0
          AVG_TIMER_WRITE: 0
          MAX_TIMER_WRITE: 0
SUM_NUMBER_OF_BYTES_WRITE: 0
               COUNT_MISC: 0
           SUM_TIMER_MISC: 0
           MIN_TIMER_MISC: 0
           AVG_TIMER_MISC: 0
           MAX_TIMER_MISC: 0
*************************** 2. row ***************************
               EVENT_NAME: wait/io/socket/sql/server_tcpip_socket
    OBJECT_INSTANCE_BEGIN: 2373085098992
               COUNT_STAR: 0
           SUM_TIMER_WAIT: 0
           MIN_TIMER_WAIT: 0
           AVG_TIMER_WAIT: 0
           MAX_TIMER_WAIT: 0
               COUNT_READ: 0
           SUM_TIMER_READ: 0
           MIN_TIMER_READ: 0
           AVG_TIMER_READ: 0
           MAX_TIMER_READ: 0
 SUM_NUMBER_OF_BYTES_READ: 0
              COUNT_WRITE: 0
          SUM_TIMER_WRITE: 0
          MIN_TIMER_WRITE: 0
          AVG_TIMER_WRITE: 0
          MAX_TIMER_WRITE: 0
SUM_NUMBER_OF_BYTES_WRITE: 0
               COUNT_MISC: 0
           SUM_TIMER_MISC: 0
           MIN_TIMER_MISC: 0
           AVG_TIMER_MISC: 0
           MAX_TIMER_MISC: 0
2 rows in set (0.00 sec)
```

### 27.12.18.10 メモリーサマリーテーブル

メモリー使用量を計測し、メモリー使用量の統計を集約します

```sql
mysql> SELECT *
    ->        FROM performance_schema.memory_summary_global_by_event_name
    ->        WHERE EVENT_NAME = 'memory/sql/TABLE'\G
*************************** 1. row ***************************
                  EVENT_NAME: memory/sql/TABLE
                 COUNT_ALLOC: 983
                  COUNT_FREE: 565
   SUM_NUMBER_OF_BYTES_ALLOC: 3457040
    SUM_NUMBER_OF_BYTES_FREE: 2050415
              LOW_COUNT_USED: 0
          CURRENT_COUNT_USED: 418
             HIGH_COUNT_USED: 420
    LOW_NUMBER_OF_BYTES_USED: 0
CURRENT_NUMBER_OF_BYTES_USED: 1406625
   HIGH_NUMBER_OF_BYTES_USED: 1582935
1 row in set (0.00 sec)
```

### 27.12.18.11 エラー要約テーブル

サーバーエラー (および警告) に関する統計情報を集約するためのサマリーテーブルを保持します

```sql
mysql> SELECT *
    ->        FROM performance_schema.events_errors_summary_global_by_error
    ->        WHERE SUM_ERROR_RAISED <> 0\G
*************************** 1. row ***************************
     ERROR_NUMBER: 1046
       ERROR_NAME: ER_NO_DB_ERROR
        SQL_STATE: 3D000
 SUM_ERROR_RAISED: 2
SUM_ERROR_HANDLED: 0
       FIRST_SEEN: 2024-01-05 16:32:50
        LAST_SEEN: 2024-01-05 16:50:44
*************************** 2. row ***************************
     ERROR_NUMBER: 1049
       ERROR_NAME: ER_BAD_DB_ERROR
        SQL_STATE: 42000
 SUM_ERROR_RAISED: 1
SUM_ERROR_HANDLED: 0
       FIRST_SEEN: 2024-01-05 16:19:56
        LAST_SEEN: 2024-01-05 16:19:56
*************************** 3. row ***************************
     ERROR_NUMBER: 1054
       ERROR_NAME: ER_BAD_FIELD_ERROR
        SQL_STATE: 42S22
 SUM_ERROR_RAISED: 1
SUM_ERROR_HANDLED: 0
       FIRST_SEEN: 2024-01-05 16:33:18
        LAST_SEEN: 2024-01-05 16:33:18
*************************** 4. row ***************************
     ERROR_NUMBER: 1064
       ERROR_NAME: ER_PARSE_ERROR
        SQL_STATE: 42000
 SUM_ERROR_RAISED: 5
SUM_ERROR_HANDLED: 0
       FIRST_SEEN: 2024-01-05 16:36:20
        LAST_SEEN: 2024-01-05 16:43:11
*************************** 5. row ***************************
     ERROR_NUMBER: 1146
       ERROR_NAME: ER_NO_SUCH_TABLE
        SQL_STATE: 42S02
 SUM_ERROR_RAISED: 3
SUM_ERROR_HANDLED: 0
       FIRST_SEEN: 2024-01-05 16:32:24
        LAST_SEEN: 2024-01-05 16:34:45
*************************** 6. row ***************************
     ERROR_NUMBER: 1287
       ERROR_NAME: ER_WARN_DEPRECATED_SYNTAX
        SQL_STATE: HY000
 SUM_ERROR_RAISED: 34
SUM_ERROR_HANDLED: 0
       FIRST_SEEN: 2024-01-05 16:17:51
        LAST_SEEN: 2024-01-05 16:17:51
*************************** 7. row ***************************
     ERROR_NUMBER: 3778
       ERROR_NAME: ER_WARN_DEPRECATED_UTF8MB3_COLLATION
        SQL_STATE: HY000
 SUM_ERROR_RAISED: 73
SUM_ERROR_HANDLED: 0
       FIRST_SEEN: 2024-01-05 16:17:51
        LAST_SEEN: 2024-01-05 16:17:51
7 rows in set (0.02 sec)
```

### 27.12.19 パフォーマンススキーマのその他のテーブル

### 27.12.19.1 error_log テーブル

診断メッセージを書き込むエラーログです

```Sql
mysql> SELECT * FROM performance_schema.error_log limit 2\G
*************************** 1. row ***************************
    LOGGED: 2024-01-01 07:57:23.827706
 THREAD_ID: 0
      PRIO: System
ERROR_CODE: MY-015015
 SUBSYSTEM: Server
      DATA: MySQL Server - start.
*************************** 2. row ***************************
    LOGGED: 2024-01-01 07:57:24.100790
 THREAD_ID: 0
      PRIO: System
ERROR_CODE: MY-010116
 SUBSYSTEM: Server
      DATA: C:\Program Files\MySQL\MySQL Server 8.2\bin\mysqld.exe (mysqld 8.2.0) starting as process 11920
2 rows in set (0.00 sec)

```

### 27.12.19.5 host_cache テーブル

クライアントホスト名および IP アドレス情報を含むインメモリーホストキャッシュを保持し、ドメインネームシステム (DNS) のルックアップを回避するために使用されます

```Sql
mysql> table performance_schema.host_cache;
Empty set (0.00 sec)
```

### 27.12.19.6 keyring_keys テーブル

内部サーバーコンポーネントおよびプラグインが機密情報を安全に格納して後で取得できるようにするキーリングをサポートしています

```Sql
mysql> table performance_schema.keyring_keys;
Empty set (0.00 sec)
```

### 27.12.19.7 log_status テーブル

オンラインバックアップツールが、コピープロセス中に必要なログファイルをロックせずにコピーできるようにする情報が示されます

```Sql
mysql> table performance_schema.log_status;
+--------------------------------------+--------------------------------------------------------------------------------------------------+------------------+-----------------------------------------------------------+
| SERVER_UUID                          | LOCAL
                  | REPLICATION      | STORAGE_ENGINES                                           |
+--------------------------------------+--------------------------------------------------------------------------------------------------+------------------+-----------------------------------------------------------+
| f1680d89-a82f-11ee-b301-e8e0b71f826a | {"gtid_executed": "", "binary_log_file": "DYNABOOK-B552-bin.000019", "binary_log_position": 157} | {"channels": []} | {"InnoDB": {"LSN": 59108113, "LSN_checkpoint": 59108113}} |
+--------------------------------------+--------------------------------------------------------------------------------------------------+------------------+-----------------------------------------------------------+
1 row in set (0.00 sec)
```

### 27.12.19.8 performance_timers テーブル

使用可能なイベントタイマーを示します

```sql
mysql> table performance_schema.performance_timers;
+-------------+-----------------+------------------+----------------+
| TIMER_NAME  | TIMER_FREQUENCY | TIMER_RESOLUTION | TIMER_OVERHEAD |
+-------------+-----------------+------------------+----------------+
| CYCLE       |      2885323383 |                1 |             21 |
| NANOSECOND  |            NULL |             NULL |           NULL |
| MICROSECOND |        10000000 |                1 |             76 |
| MILLISECOND |            1038 |                1 |             37 |
| THREAD_CPU  |      1016845810 |             1000 |           1569 |
+-------------+-----------------+------------------+----------------+
5 rows in set (0.00 sec)
```

### 27.12.19.9 processlist テーブル

サーバー内で実行されているスレッドのセットによって現在実行されている操作が示されます

```Sql
mysql> table performance_schema.processlist;
+----+-----------------+-----------+------+---------+------+-----------------------------+--------------------------------------+------------------+
| ID | USER            | HOST      | DB   | COMMAND | TIME | STATE                       | INFO
        | EXECUTION_ENGINE |
+----+-----------------+-----------+------+---------+------+-----------------------------+--------------------------------------+------------------+
|  5 | event_scheduler | localhost | NULL | Connect | 3353 | Waiting for next activation | NULL
        | PRIMARY          |
|  9 | root            | localhost | NULL | Query   |    0 | executing                   | table performance_schema.processlist | PRIMARY          |
+----+-----------------+-----------+------+---------+------+-----------------------------+--------------------------------------+------------------+
2 rows in set (0.00 sec)
```

### 27.12.19.10 スレッドテーブル

各サーバースレッドの行を格納します

```sql
mysql> SELECT * FROM performance_schema.threads limit 2\G
*************************** 1. row ***************************
            THREAD_ID: 1
                 NAME: thread/sql/main
                 TYPE: BACKGROUND
       PROCESSLIST_ID: NULL
     PROCESSLIST_USER: NULL
     PROCESSLIST_HOST: NULL
       PROCESSLIST_DB: mysql
  PROCESSLIST_COMMAND: NULL
     PROCESSLIST_TIME: 3585
    PROCESSLIST_STATE: NULL
     PROCESSLIST_INFO: NULL
     PARENT_THREAD_ID: NULL
                 ROLE: NULL
         INSTRUMENTED: YES
              HISTORY: YES
      CONNECTION_TYPE: NULL
         THREAD_OS_ID: 1812
       RESOURCE_GROUP: SYS_default
     EXECUTION_ENGINE: PRIMARY
    CONTROLLED_MEMORY: 1456
MAX_CONTROLLED_MEMORY: 67480
         TOTAL_MEMORY: 1643262
     MAX_TOTAL_MEMORY: 1668654
     TELEMETRY_ACTIVE: NO
*************************** 2. row ***************************
            THREAD_ID: 2
                 NAME: thread/mysys/thread_timer_notifier
                 TYPE: BACKGROUND
       PROCESSLIST_ID: NULL
     PROCESSLIST_USER: NULL
     PROCESSLIST_HOST: NULL
       PROCESSLIST_DB: NULL
  PROCESSLIST_COMMAND: NULL
     PROCESSLIST_TIME: NULL
    PROCESSLIST_STATE: NULL
     PROCESSLIST_INFO: NULL
     PARENT_THREAD_ID: 1
                 ROLE: NULL
         INSTRUMENTED: YES
              HISTORY: YES
      CONNECTION_TYPE: NULL
         THREAD_OS_ID: 12784
       RESOURCE_GROUP: SYS_default
     EXECUTION_ENGINE: PRIMARY
    CONTROLLED_MEMORY: 0
MAX_CONTROLLED_MEMORY: 0
         TOTAL_MEMORY: 0
     MAX_TOTAL_MEMORY: 0
     TELEMETRY_ACTIVE: NO
2 rows in set (0.00 sec)
```

### 27.12.19.11 tls_channel_status テーブル

接続インタフェース TLS プロパティに関する情報を提供します

```Sql
mysql> SELECT * FROM performance_schema.tls_channel_status limit 2\G
*************************** 1. row ***************************
 CHANNEL: mysql_main
PROPERTY: Enabled
   VALUE: Yes
*************************** 2. row ***************************
 CHANNEL: mysql_main
PROPERTY: Ssl_accept_renegotiates
   VALUE: 0
2 rows in set (0.00 sec)
```

### 27.12.19.12 user_defined_functions テーブル

コンポーネントまたはプラグインによって自動的に、または CREATE FUNCTION ステートメントによって手動で登録された各ユーザー定義関数 (UDF) の行が含まれています

```sql
mysql> SELECT * FROM performance_schema.user_defined_functions limit 2\G
*************************** 1. row ***************************
       UDF_NAME: asynchronous_connection_failover_delete_managed
UDF_RETURN_TYPE: char
       UDF_TYPE: function
    UDF_LIBRARY: NULL
UDF_USAGE_COUNT: 1
*************************** 2. row ***************************
       UDF_NAME: mysqlx_generate_document_id
UDF_RETURN_TYPE: char
       UDF_TYPE: function
    UDF_LIBRARY: NULL
UDF_USAGE_COUNT: 1
2 rows in set (0.00 sec)
```

### 27.15 パフォーマンススキーマシステム変数

構成情報を提供するいくつかのシステム変数を実装しています

```Sql
mysql> SHOW VARIABLES LIKE 'perf%';
+----------------------------------------------------------+-------+
| Variable_name                                            | Value |
+----------------------------------------------------------+-------+
| performance_schema                                       | ON    |
| performance_schema_accounts_size                         | -1    |
| performance_schema_digests_size                          | 10000 |
| performance_schema_error_size                            | 5377  |
| performance_schema_events_stages_history_long_size       | 10000 |
| performance_schema_events_stages_history_size            | 10    |
| performance_schema_events_statements_history_long_size   | 10000 |
| performance_schema_events_statements_history_size        | 10    |
| performance_schema_events_transactions_history_long_size | 10000 |
| performance_schema_events_transactions_history_size      | 10    |
| performance_schema_events_waits_history_long_size        | 10000 |
| performance_schema_events_waits_history_size             | 10    |
| performance_schema_hosts_size                            | -1    |
| performance_schema_max_cond_classes                      | 150   |
| performance_schema_max_cond_instances                    | -1    |
| performance_schema_max_digest_length                     | 1024  |
| performance_schema_max_digest_sample_age                 | 60    |
| performance_schema_max_file_classes                      | 80    |
| performance_schema_max_file_handles                      | 32768 |
| performance_schema_max_file_instances                    | -1    |
| performance_schema_max_index_stat                        | -1    |
| performance_schema_max_memory_classes                    | 470   |
| performance_schema_max_metadata_locks                    | -1    |
| performance_schema_max_meter_classes                     | 30    |
| performance_schema_max_metric_classes                    | 600   |
| performance_schema_max_mutex_classes                     | 350   |
| performance_schema_max_mutex_instances                   | -1    |
| performance_schema_max_prepared_statements_instances     | -1    |
| performance_schema_max_program_instances                 | -1    |
| performance_schema_max_rwlock_classes                    | 100   |
| performance_schema_max_rwlock_instances                  | -1    |
| performance_schema_max_socket_classes                    | 10    |
| performance_schema_max_socket_instances                  | -1    |
| performance_schema_max_sql_text_length                   | 1024  |
| performance_schema_max_stage_classes                     | 175   |
| performance_schema_max_statement_classes                 | 220   |
| performance_schema_max_statement_stack                   | 10    |
| performance_schema_max_table_handles                     | -1    |
| performance_schema_max_table_instances                   | -1    |
| performance_schema_max_table_lock_stat                   | -1    |
| performance_schema_max_thread_classes                    | 100   |
| performance_schema_max_thread_instances                  | -1    |
| performance_schema_session_connect_attrs_size            | 512   |
| performance_schema_setup_actors_size                     | -1    |
| performance_schema_setup_objects_size                    | -1    |
| performance_schema_show_processlist                      | OFF   |
| performance_schema_users_size                            | -1    |
+----------------------------------------------------------+-------+
47 rows in set, 1 warning (0.01 sec)
```

### 27.16 パフォーマンススキーマステータス変数

```sql
mysql> SHOW STATUS LIKE 'perf%';
+-------------------------------------------------------+-------+
| Variable_name                                         | Value |
+-------------------------------------------------------+-------+
| Performance_schema_accounts_lost                      | 0     |
| Performance_schema_cond_classes_lost                  | 0     |
| Performance_schema_cond_instances_lost                | 0     |
| Performance_schema_digest_lost                        | 0     |
| Performance_schema_file_classes_lost                  | 0     |
| Performance_schema_file_handles_lost                  | 0     |
| Performance_schema_file_instances_lost                | 0     |
| Performance_schema_hosts_lost                         | 0     |
| Performance_schema_index_stat_lost                    | 0     |
| Performance_schema_locker_lost                        | 0     |
| Performance_schema_memory_classes_lost                | 0     |
| Performance_schema_metadata_lock_lost                 | 0     |
| Performance_schema_meter_lost                         | 0     |
| Performance_schema_metric_lost                        | 0     |
| Performance_schema_mutex_classes_lost                 | 0     |
| Performance_schema_mutex_instances_lost               | 0     |
| Performance_schema_nested_statement_lost              | 0     |
| Performance_schema_prepared_statements_lost           | 0     |
| Performance_schema_program_lost                       | 0     |
| Performance_schema_rwlock_classes_lost                | 0     |
| Performance_schema_rwlock_instances_lost              | 0     |
| Performance_schema_session_connect_attrs_longest_seen | 129   |
| Performance_schema_session_connect_attrs_lost         | 0     |
| Performance_schema_socket_classes_lost                | 0     |
| Performance_schema_socket_instances_lost              | 0     |
| Performance_schema_stage_classes_lost                 | 0     |
| Performance_schema_statement_classes_lost             | 0     |
| Performance_schema_table_handles_lost                 | 0     |
| Performance_schema_table_instances_lost               | 0     |
| Performance_schema_table_lock_stat_lost               | 0     |
| Performance_schema_thread_classes_lost                | 0     |
| Performance_schema_thread_instances_lost              | 0     |
| Performance_schema_users_lost                         | 0     |
+-------------------------------------------------------+-------+
33 rows in set (0.00 sec)
```

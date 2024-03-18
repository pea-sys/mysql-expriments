### 28.4.2.1 sys_config テーブル

オプションごとに 1 行ずつ、sys スキーマ構成オプションが含まれています

```Sql
mysql> table sys.sys_config;
+--------------------------------------+-------+---------------------+--------+
| variable                             | value | set_time            | set_by |
+--------------------------------------+-------+---------------------+--------+
| diagnostics.allow_i_s_tables         | OFF   | 2024-01-01 07:57:21 | NULL   |
| diagnostics.include_raw              | OFF   | 2024-01-01 07:57:21 | NULL   |
| ps_thread_trx_info.max_length        | 65535 | 2024-01-01 07:57:21 | NULL   |
| statement_performance_analyzer.limit | 100   | 2024-01-01 07:57:21 | NULL   |
| statement_performance_analyzer.view  | NULL  | 2024-01-01 07:57:21 | NULL   |
| statement_truncate_len               | 64    | 2024-01-01 07:57:21 | NULL   |
+--------------------------------------+-------+---------------------+--------+
6 rows in set (0.02 sec)
```

### 28.4.3 sys スキーマビュー

### 28.4.3.1 host_summary および x$host_summary のビュー

ホストごとにグループ化されたステートメントアクティビティ、ファイル I/O,および接続が要約されます

```sql
mysql> SELECT * FROM sys.host_summary\G
*************************** 1. row ***************************
                  host: localhost
            statements: 134
     statement_latency: 517.44 ms
 statement_avg_latency: 3.86 ms
           table_scans: 76
              file_ios: 58
       file_io_latency: 21.27 ms
   current_connections: 2
     total_connections: 5
          unique_users: 2
        current_memory: 5.14 MiB
total_memory_allocated: 28.04 MiB
1 row in set (0.01 sec)
```

### 28.4.3.3 host_summary_by_file_io_type および x$host_summary_by_file_io_type ビュー

ホストおよびイベントタイプ別にグループ化されたファイル I/O,が要約されます

```sql
mysql> SELECT * FROM sys.host_summary_by_file_io_type;
+------------+---------------------------------------+-------+---------------+-------------+
| host       | event_name                            | total | total_latency | max_latency |
+------------+---------------------------------------+-------+---------------+-------------+
| background | wait/io/file/innodb/innodb_data_file  |  1998 | 2.24 s        | 36.57 ms    |
| background | wait/io/file/innodb/innodb_log_file   |   351 | 768.65 ms     | 39.24 ms    |
| background | wait/io/file/innodb/innodb_dblwr_file |    32 | 275.07 ms     | 239.21 ms   |
| background | wait/io/file/innodb/innodb_temp_file  |    80 | 57.28 ms      | 7.77 ms     |
| background | wait/io/file/sql/binlog_index         |    23 | 28.73 ms      | 25.17 ms    |
| background | wait/io/file/sql/binlog               |    20 | 5.47 ms       | 3.19 ms     |
| background | wait/io/file/sql/casetest             |    15 | 3.74 ms       | 993.47 us   |
| background | wait/io/file/sql/misc                 |     5 | 731.88 us     | 179.10 us   |
| background | wait/io/file/sql/pid                  |     3 | 503.83 us     | 321.21 us   |
| background | wait/io/file/sql/ERRMSG               |     5 | 339.95 us     | 174.42 us   |
| background | wait/io/file/sql/slow_log             |     4 | 330.00 us     | 220.67 us   |
| background | wait/io/file/mysys/cnf                |     5 | 255.99 us     | 135.62 us   |
| background | wait/io/file/mysys/charset            |     3 | 187.79 us     | 73.41 us    |
| localhost  | wait/io/file/innodb/innodb_data_file  |    30 | 11.30 ms      | 664.41 us   |
+------------+---------------------------------------+-------+---------------+-------------+
14 rows in set (0.00 sec)
```

### 28.4.3.4 host_summary_by_stages および x$host_summary_by_stages のビュー

ホストごとにグループ化されたステートメントステージを要約します

```sql
mysql> SELECT * FROM sys.host_summary_by_stages;
+------------+-------------------------------+-------+---------------+-------------+
| host       | event_name                    | total | total_latency | avg_latency |
+------------+-------------------------------+-------+---------------+-------------+
| background | stage/innodb/buffer pool load |     1 | 804.50 us     | 804.50 us   |
+------------+-------------------------------+-------+---------------+-------------+
1 row in set (0.00 sec)
```

### 28.4.3.5 host_summary_by_statement_latency および x$host_summary_by_statement_latency ビュー

ホストごとにグループ化されたステートメント全体の統計が要約されます。 デフォルトでは、行は合計レイテンシの降順でソートされます

```sql
mysql> SELECT * FROM sys.host_summary_by_statement_latency;
+------------+-------+---------------+-------------+--------------+-------------+-----------+---------------+---------------+------------+
| host       | total | total_latency | max_latency | lock_latency | cpu_latency | rows_sent | rows_examined | rows_affected | full_scans |
+------------+-------+---------------+-------------+--------------+-------------+-----------+---------------+---------------+------------+
| localhost  |    70 | 275.60 ms     | 28.92 ms    | 595.00 us    |   0 ps      |      9190 |         15622 |             0 |         41 |
| background |     0 |   0 ps        |   0 ps      |   0 ps       |   0 ps      |         0 |             0 |
  0 |          0 |
+------------+-------+---------------+-------------+--------------+-------------+-----------+---------------+---------------+------------+
2 rows in set (0.00 sec)
```

### 28.4.3.6 host_summary_by_statement_type および x$host_summary_by_statement_type のビュー

実行されたステートメントに関する情報が、ホストおよびステートメントタイプ別にグループ化されて要約されます

```sql
mysql> SELECT * FROM sys.host_summary_by_statement_type;
+-----------+----------------+-------+---------------+-------------+--------------+-------------+-----------+---------------+---------------+------------+
| host      | statement      | total | total_latency | max_latency | lock_latency | cpu_latency | rows_sent | rows_examined | rows_affected | full_scans |
+-----------+----------------+-------+---------------+-------------+--------------+-------------+-----------+---------------+---------------+------------+
| localhost | select         |    51 | 238.79 ms     | 28.92 ms    | 572.00 us    |   0 ps      |      9112 |         15544 |             0 |         40 |
| localhost | show_variables |     1 | 15.61 ms      | 15.61 ms    | 16.00 us     |   0 ps      |        47 |
 47 |             0 |          1 |
| localhost | event          |     1 | 13.40 ms      | 13.40 ms    | 3.00 us      |   0 ps      |         0 |
  0 |             0 |          0 |
| localhost | set_option     |     1 | 4.75 ms       | 4.75 ms     |   0 ps       |   0 ps      |         0 |
  0 |             0 |          0 |
| localhost | show_status    |     1 | 4.57 ms       | 4.57 ms     | 10.00 us     |   0 ps      |        33 |
 33 |             0 |          1 |
| localhost | stmt           |     1 | 1.23 ms       | 1.23 ms     | 3.00 us      |   0 ps      |         0 |
  0 |             0 |          0 |
| localhost | error          |    11 | 892.80 us     | 143.90 us   |   0 ps       |   0 ps      |         0 |
  0 |             0 |          0 |
| localhost | kill           |     2 | 219.10 us     | 119.90 us   |   0 ps       |   0 ps      |         0 |
  0 |             0 |          0 |
| localhost | Quit           |     2 | 17.30 us      | 11.30 us    |   0 ps       |   0 ps      |         0 |
  0 |             0 |          0 |
+-----------+----------------+-------+---------------+-------------+--------------+-------------+-----------+---------------+---------------+------------+
9 rows in set (0.01 sec)
```

### 28.4.3.7 innodb_buffer_stats_by_schema および x$innodb_buffer_stats_by_schema のビュー

スキーマ別にグループ化された INFORMATION_SCHEMA INNODB_BUFFER_PAGE テーブルの情報がまとめられています

```sql
mysql> SELECT * FROM sys.innodb_buffer_stats_by_schema;
+---------------+-----------+------------+-------+--------------+-----------+-------------+
| object_schema | allocated | data       | pages | pages_hashed | pages_old | rows_cached |
+---------------+-----------+------------+-------+--------------+-----------+-------------+
| mysql         | 3.95 MiB  | 1.93 MiB   |   253 |           35 |       120 |        1002 |
| sys           | 16.00 KiB |  338 bytes |     1 |            0 |         1 |           6 |
+---------------+-----------+------------+-------+--------------+-----------+-------------+
2 rows in set (0.12 sec)
```

### 28.4.3.8 innodb_buffer_stats_by_table および x$innodb_buffer_stats_by_table のビュー

スキーマおよびテーブル別にグループ化された INFORMATION_SCHEMA INNODB_BUFFER_PAGE テーブルの情報がまとめられています

```sql
mysql> SELECT * FROM sys.innodb_buffer_stats_by_table limit 2;
+---------------+-------------+------------+-------------+-------+--------------+-----------+-------------+
| object_schema | object_name | allocated  | data        | pages | pages_hashed | pages_old | rows_cached |
+---------------+-------------+------------+-------------+-------+--------------+-----------+-------------+
| mysql         | columns     | 1.30 MiB   | 1004.58 KiB |    83 |           32 |        43 |        3915 |
| mysql         | tables      | 640.00 KiB | 442.72 KiB  |    40 |            0 |        18 |         478 |
+---------------+-------------+------------+-------------+-------+--------------+-----------+-------------+
2 rows in set (0.04 sec)
```

### 28.4.3.9 innodb_lock_waits および x$innodb_lock_waits のビュー

トランザクションが待機している InnoDB ロックの概要が示されます

```sql
mysql> SELECT * FROM sys.innodb_lock_waits;
Empty set (0.03 sec)
```

### 28.4.3.10 io_by_thread_by_latency および x$io_by_thread_by_latency のビュー

I/O コンシューマを要約して、I/O を待機している時間をスレッド別にグループ化して表示します

```sql
mysql> SELECT * FROM sys.io_by_thread_by_latency limit 2;
+-------------------------------+-------+---------------+-------------+-------------+-------------+-----------+----------------+
| user                          | total | total_latency | min_latency | avg_latency | max_latency | thread_id | processlist_id |
+-------------------------------+-------+---------------+-------------+-------------+-------------+-----------+----------------+
| log_files_governor_thread     |   217 | 649.92 ms     | 103.22 us   | 3.00 ms     | 39.24 ms    |        19 |
  NULL |
| page_flush_coordinator_thread |   525 | 283.55 ms     | 15.33 us    | 3.45 ms     | 239.21 ms   |        13 |
  NULL |
+-------------------------------+-------+---------------+-------------+-------------+-------------+-----------+----------------+
2 rows in set (0.01 sec)
```

### 28.4.3.11 io_global_by_file_by_bytes および x$io_global_by_file_by_bytes のビュー

グローバル I/O コンシューマを要約して、ファイルごとにグループ化された I/O の量を表示します

```sql
mysql> SELECT * FROM sys.io_global_by_file_by_bytes limit 2;
+------------------------------------+------------+------------+------------+-------------+---------------+------------+-----------+-----------+
| file                               | count_read | total_read | avg_read   | count_write | total_written | avg_write  | total     | write_pct |
+------------------------------------+------------+------------+------------+-------------+---------------+------------+-----------+-----------+
| @@datadir\ibtmp1                   |          0 |    0 bytes |    0 bytes |         143 | 14.05 MiB     | 100.59 KiB | 14.05 MiB |    100.00 |
| @@datadir\#innodb_temp\temp_10.ibt |          0 |    0 bytes |    0 bytes |         313 | 13.28 MiB     | 43.45 KiB  | 13.28 MiB |    100.00 |
+------------------------------------+------------+------------+------------+-------------+---------------+------------+-----------+-----------+
2 rows in set (0.00 sec)
```

### 28.4.3.12 io_global_by_file_by_latency および x$io_global_by_file_by_latency のビュー

グローバル I/O コンシューマを要約して、I/O を待機している時間をファイル別にグループ化して表示します

```sql
mysql> SELECT * FROM sys.io_global_by_file_by_latency limit 2;
+--------------------+-------+---------------+------------+--------------+-------------+---------------+------------+--------------+
| file               | total | total_latency | count_read | read_latency | count_write | write_latency | count_misc | misc_latency |
+--------------------+-------+---------------+------------+--------------+-------------+---------------+------------+--------------+
| @@datadir\undo_001 |   523 | 1.01 s        |        495 | 1.01 s       |          14 | 614.80 us     |         14 | 5.58 ms      |
| @@datadir\undo_002 |   517 | 919.61 ms     |        495 | 913.27 ms    |          11 | 1.09 ms       |         11 | 5.25 ms      |
+--------------------+-------+---------------+------------+--------------+-------------+---------------+------------+--------------+
2 rows in set (0.00 sec)
```

### 28.4.3.13 io_global_by_wait_by_bytes および x$io_global_by_wait_by_bytes のビュー

グローバル I/O コンシューマの概要が示され、I/O を待機している時間がイベント別にグループ化されて表示されます

```sql
mysql> SELECT * FROM sys.io_global_by_wait_by_bytes limit 2;
+-------------------------+-------+---------------+-------------+-------------+-------------+------------+------------+-----------+-------------+---------------+-------------+-----------------+
| event_name              | total | total_latency | min_latency | avg_latency | max_latency | count_read | total_read | avg_read  | count_write | total_written | avg_written | total_requested |
+-------------------------+-------+---------------+-------------+-------------+-------------+------------+------------+-----------+-------------+---------------+-------------+-----------------+
| innodb/innodb_log_file  |   351 | 768.65 ms     |   0 ps      | 2.19 ms     | 39.24 ms    |          7 | 67.00 KiB  | 9.57 KiB  |         157 | 96.96 MiB     | 632.38 KiB  | 97.02 MiB       |
| innodb/innodb_data_file |  2039 | 2.25 s        |   0 ps      | 1.11 ms     | 36.57 ms    |       1315 | 20.73 MiB  | 16.15 KiB |         226 | 15.34 MiB     | 69.52 KiB   | 36.08 MiB       |
+-------------------------+-------+---------------+-------------+-------------+-------------+------------+------------+-----------+-------------+---------------+-------------+-----------------+
2 rows in set (0.00 sec)
```

### 28.4.3.14 io_global_by_wait_by_latency および x$io_global_by_wait_by_latency ビュー

グローバル I/O コンシューマの概要が示され、I/O を待機している時間がイベント別にグループ化されて表示されます

```sql
mysql> SELECT * FROM sys.io_global_by_wait_by_latency limit 2;
+-------------------------+-------+---------------+-------------+-------------+--------------+---------------+--------------+------------+------------+-----------+-------------+---------------+-------------+
| event_name              | total | total_latency | avg_latency | max_latency | read_latency | write_latency | misc_latency | count_read | total_read | avg_read  | count_write | total_written | avg_written |
+-------------------------+-------+---------------+-------------+-------------+--------------+---------------+--------------+------------+------------+-----------+-------------+---------------+-------------+
| innodb/innodb_data_file |  2040 | 2.25 s        | 1.10 ms     | 36.57 ms    | 2.08 s       | 78.56 ms      | 93.16 ms     |       1316 | 20.75 MiB  | 16.15 KiB |         226 | 15.34 MiB     | 69.52 KiB   |
| innodb/innodb_log_file  |   351 | 768.65 ms     | 2.19 ms     | 39.24 ms    | 831.10 us    | 71.71 ms      | 696.12 ms    |          7 | 67.00 KiB  | 9.57 KiB  |         157 | 96.96 MiB     | 632.38 KiB  |
+-------------------------+-------+---------------+-------------+-------------+--------------+---------------+--------------+------------+------------+-----------+-------------+---------------+-------------+
2 rows in set (0.00 sec)
```

### 28.4.3.15 latest_file_io および x$latest_file_io のビュー

ファイルおよびスレッド別にグループ化されたファイル I/O アクティビティが要約されます

```Sql
mysql> SELECT * FROM sys.latest_file_io;
Empty set (0.00 sec)
```

### 28.4.3.16 memory_by_host_by_current_bytes および x$memory_by_host_by_current_bytes ビュー

ホストごとにグループ化されたメモリー使用量の概要が示されます

```sql
mysql> SELECT * FROM sys.memory_by_host_by_current_bytes;
+------------+--------------------+-------------------+-------------------+-------------------+-----------------+
| host       | current_count_used | current_allocated | current_avg_alloc | current_max_alloc | total_allocated |
+------------+--------------------+-------------------+-------------------+-------------------+-----------------+
| localhost  |               5025 | 3.32 MiB          |  692 bytes        | 1.00 MiB          | 110.25 MiB      |
| background |              10973 | 2.25 MiB          |  214 bytes        | 575.52 KiB        | 123.02 MiB      |
+------------+--------------------+-------------------+-------------------+-------------------+-----------------+
2 rows in set (0.01 sec)
```

### 28.4.3.17 memory_by_thread_by_current_bytes および x$memory_by_thread_by_current_bytes のビュー

スレッドごとにグループ化されたメモリー使用量の概要が示されます

```sql
mysql> SELECT * FROM sys.memory_by_thread_by_current_bytes limit 2;
+-----------+----------------+--------------------+-------------------+-------------------+-------------------+-----------------+
| thread_id | user           | current_count_used | current_allocated | current_avg_alloc | current_max_alloc | total_allocated |
+-----------+----------------+--------------------+-------------------+-------------------+-------------------+-----------------+
|        51 | root@localhost |               4902 | 10.29 MiB         | 2.15 KiB          | 8.00 MiB          | 479.35 MiB      |
|         1 | sql/main       |               8462 | 1.57 MiB          |  194 bytes        | 433.12 KiB        | 13.97 MiB       |
+-----------+----------------+--------------------+-------------------+-------------------+-------------------+-----------------+
2 rows in set (0.06 sec)
```

### 28.4.3.18 memory_by_user_by_current_bytes および x$memory_by_user_by_current_bytes のビュー

ユーザー別にグループ化されたメモリー使用量の概要が示されます

```sql
mysql> SELECT * FROM sys.memory_by_user_by_current_bytes;
+-----------------+--------------------+-------------------+-------------------+-------------------+-----------------+
| user            | current_count_used | current_allocated | current_avg_alloc | current_max_alloc | total_allocated |
+-----------------+--------------------+-------------------+-------------------+-------------------+-----------------+
| root            |               5027 | 3.32 MiB          |  692 bytes        | 1.00 MiB          | 481.03 MiB      |
| background      |              10973 | 2.25 MiB          |  214 bytes        | 575.52 KiB        | 124.30 MiB      |
| event_scheduler |                268 | 65.71 KiB         |  251 bytes        | 19.52 KiB         | 470.99 KiB      |
+-----------------+--------------------+-------------------+-------------------+-------------------+-----------------+
3 rows in set (0.01 sec)
```

### 28.4.3.19 memory_global_by_current_bytes および x$memory_global_by_current_bytes のビュー

割当てタイプ (つまり、イベント別) 別にグループ化されたメモリー使用量が要約されます

```sql
mysql> SELECT * FROM sys.memory_global_by_current_bytes limit 2;
+---------------------------------------------------------------+---------------+---------------+-------------------+------------+------------+----------------+
| event_name                                                    | current_count | current_alloc | current_avg_alloc | high_count | high_alloc | high_avg_alloc |
+---------------------------------------------------------------+---------------+---------------+-------------------+------------+------------+----------------+
| memory/innodb/buf_buf_pool                                    |             1 | 130.82 MiB    | 130.82 MiB        |          1 | 130.82 MiB | 130.82 MiB     |
| memory/performance_schema/events_statements_summary_by_digest |             1 | 40.28 MiB     | 40.28 MiB         |          1 | 40.28 MiB  | 40.28 MiB      |
+---------------------------------------------------------------+---------------+---------------+-------------------+------------+------------+----------------+
2 rows in set (0.00 sec)
```

### 28.4.3.20 memory_global_total および x$memory_global_total のビュー

サーバー内の合計メモリー使用量のサマリーが表示されます

```Sql
mysql> SELECT * FROM sys.memory_global_total;
+-----------------+
| total_allocated |
+-----------------+
| 446.34 MiB      |
+-----------------+
1 row in set (0.00 sec)
```

### 28.4.3.22 processlist ビューと x$processlist ビュー

サーバー内で実行されているスレッドのセットによって現在実行されている操作が示されます

```sql
mysql> SELECT * FROM sys.processlist limit 2\G
*************************** 1. row ***************************
                thd_id: 13
               conn_id: NULL
                  user: innodb/page_flush_coordinator_thread
                    db: NULL
               command: NULL
                 state: NULL
                  time: 10058
     current_statement: NULL
      execution_engine: PRIMARY
     statement_latency: NULL
              progress: NULL
          lock_latency: NULL
           cpu_latency: NULL
         rows_examined: NULL
             rows_sent: NULL
         rows_affected: NULL
            tmp_tables: NULL
       tmp_disk_tables: NULL
             full_scan: NO
        last_statement: NULL
last_statement_latency: NULL
        current_memory: 6.56 KiB
             last_wait: NULL
     last_wait_latency: NULL
                source: NULL
           trx_latency: NULL
             trx_state: NULL
        trx_autocommit: NULL
                   pid: NULL
          program_name: NULL
*************************** 2. row ***************************
                thd_id: 1
               conn_id: NULL
                  user: sql/main
                    db: mysql
               command: NULL
                 state: NULL
                  time: 10056
     current_statement: NULL
      execution_engine: PRIMARY
     statement_latency: NULL
              progress: NULL
          lock_latency: NULL
           cpu_latency: NULL
         rows_examined: NULL
             rows_sent: NULL
         rows_affected: NULL
            tmp_tables: NULL
       tmp_disk_tables: NULL
             full_scan: NO
        last_statement: NULL
last_statement_latency: NULL
        current_memory: 1.57 MiB
             last_wait: NULL
     last_wait_latency: NULL
                source: NULL
           trx_latency: 27.00 us
             trx_state: COMMITTED
        trx_autocommit: YES
                   pid: NULL
          program_name: NULL
2 rows in set (0.06 sec)
```

### 28.4.3.23 ps_check_lost_instrumentation ビュー

失われたパフォーマンススキーマインストゥルメントに関する情報を返し、パフォーマンススキーマがすべての実行時データをモニターできないかどうかを示します

```sql
mysql> SELECT * FROM sys.ps_check_lost_instrumentation;
Empty set (0.00 sec)
```

### 28.4.3.24 schema_auto_increment_columns ビュー

AUTO_INCREMENT カラムを持つテーブルを示し、現在のカラム値と最大カラム値、使用率 (使用可能な値に対する使用率) など、これらのカラムに関する情報を提供します

```sql
mysql> SELECT * FROM sys. schema_auto_increment_columns limit 2;
+--------------+------------+-------------+-----------+-------------------+-----------+-------------+-----------+----------------+----------------------+
| table_schema | table_name | column_name | data_type | column_type       | is_signed | is_unsigned | max_value | auto_increment | auto_increment_ratio |
+--------------+------------+-------------+-----------+-------------------+-----------+-------------+-----------+----------------+----------------------+
| sakila       | payment    | payment_id  | smallint  | smallint unsigned |         0 |           1 |     65535 |          16049 |               0.2449 |
| sakila       | category   | category_id | tinyint   | tinyint unsigned  |         0 |           1 |       255 |             16 |               0.0627 |
+--------------+------------+-------------+-----------+-------------------+-----------+-------------+-----------+----------------+----------------------+
2 rows in set, 1 warning (0.01 sec)
```

### 28.4.3.25 schema_index_statistics および x$schema_index_statistics のビュー

インデックス統計を提供します

```sql
mysql> SELECT * FROM sys.schema_index_statistics limit 2;
+--------------------+---------------------------------------+------------+---------------+----------------+---------------+----------------+--------------+----------------+--------------+----------------+
| table_schema       | table_name                            | index_name | rows_selected | select_latency | rows_inserted | insert_latency | rows_updated | update_latency | rows_deleted | delete_latency |
+--------------------+---------------------------------------+------------+---------------+----------------+---------------+----------------+--------------+----------------+--------------+----------------+
| sys                | sys_config                            | PRIMARY    |             1 | 45.46 us       |
 0 |   0 ps         |            0 |   0 ps         |            0 |   0 ps         |
| performance_schema | table_io_waits_summary_by_index_usage | OBJECT     |             0 |   0 ps         |
 0 |   0 ps         |            0 |   0 ps         |            0 |   0 ps         |
+--------------------+---------------------------------------+------------+---------------+----------------+---------------+----------------+--------------+----------------+--------------+----------------+
2 rows in set (0.01 sec)
```

### 28.4.3.26 schema_object_overview ビュー

各スキーマ内のオブジェクトのタイプがまとめられています

```sql
mysql> SELECT * FROM sys.schema_object_overview limit 2;
+--------------------+-------------+-------+
| db                 | object_type | count |
+--------------------+-------------+-------+
| information_schema | SYSTEM VIEW |    79 |
| mysql              | BASE TABLE  |    38 |
+--------------------+-------------+-------+
2 rows in set (0.03 sec)
```

### 28.4.3.27 schema_redundant_indexes および x$schema_flattened_keys のビュー

他のインデックスを複製するインデックス、またはそれらによって冗長化されたインデックスが表示されます

```sql
mysql> SELECT * FROM sys.schema_redundant_indexes;
+--------------+-----------------+----------------------+-------------------------+----------------------------+---------------------+------------------------+---------------------------+----------------+------------------------------------------------------------------+
| table_schema | table_name      | redundant_index_name | redundant_index_columns | redundant_index_non_unique | dominant_index_name | dominant_index_columns | dominant_index_non_unique | subpart_exists | sql_drop_index
                              |
+--------------+-----------------+----------------------+-------------------------+----------------------------+---------------------+------------------------+---------------------------+----------------+------------------------------------------------------------------+
| world        | countrylanguage | CountryCode          | CountryCode             |                          1 | PRIMARY             | CountryCode,Language   |                         0 |              0 | ALTER TABLE `world`.`countrylanguage` DROP INDEX `CountryCode`   |
| world_x      | countrylanguage | CountryCode          | CountryCode             |                          1 | PRIMARY             | CountryCode,Language   |                         0 |              0 | ALTER TABLE `world_x`.`countrylanguage` DROP INDEX `CountryCode` |
+--------------+-----------------+----------------------+-------------------------+----------------------------+---------------------+------------------------+---------------------------+----------------+------------------------------------------------------------------+
2 rows in set (0.01 sec)
```

### 28.4.3.28 schema_table_lock_waits および x$schema_table_lock_waits のビュー

メタデータロックで待機中にブロックされているセッションとブロックされているセッションが表示されます

```sql
mysql> SELECT * FROM sys.schema_table_lock_waits;
Empty set (0.01 sec)
```

### 28.4.3.29 schema_table_statistics および x$schema_table_statistics のビュー

テーブルの統計がまとめられています

```sql
mysql> SELECT * FROM sys.schema_table_statistics limit 2\G
*************************** 1. row ***************************
     table_schema: sys
       table_name: sys_config
    total_latency: 1.41 ms
     rows_fetched: 7
    fetch_latency: 1.41 ms
    rows_inserted: 0
   insert_latency:   0 ps
     rows_updated: 0
   update_latency:   0 ps
     rows_deleted: 0
   delete_latency:   0 ps
 io_read_requests: 3
          io_read: 48.00 KiB
  io_read_latency: 781.72 us
io_write_requests: 0
         io_write:    0 bytes
 io_write_latency:   0 ps
 io_misc_requests: 5
  io_misc_latency: 510.66 us
*************************** 2. row ***************************
     table_schema: mysql
       table_name: dd_properties
    total_latency:   0 ps
     rows_fetched: 0
    fetch_latency:   0 ps
    rows_inserted: 0
   insert_latency:   0 ps
     rows_updated: 0
   update_latency:   0 ps
     rows_deleted: 0
   delete_latency:   0 ps
 io_read_requests: NULL
          io_read: NULL
  io_read_latency: NULL
io_write_requests: NULL
         io_write: NULL
 io_write_latency: NULL
 io_misc_requests: NULL
  io_misc_latency: NULL
2 rows in set (0.06 sec)
```

### 28.4.3.30 schema_table_statistics_with_buffer および x$schema_table_statistics_with_buffer のビュー

InnoDB バッファプール統計を含むテーブル統計を要約します

```sql
mysql> SELECT * FROM sys.schema_table_statistics_with_buffer limit 2\G
*************************** 1. row ***************************
              table_schema: sys
                table_name: sys_config
              rows_fetched: 7
             fetch_latency: 1.41 ms
             rows_inserted: 0
            insert_latency:   0 ps
              rows_updated: 0
            update_latency:   0 ps
              rows_deleted: 0
            delete_latency:   0 ps
          io_read_requests: 3
                   io_read: 48.00 KiB
           io_read_latency: 781.72 us
         io_write_requests: 0
                  io_write:    0 bytes
          io_write_latency:   0 ps
          io_misc_requests: 5
           io_misc_latency: 510.66 us
   innodb_buffer_allocated: 16.00 KiB
        innodb_buffer_data:  338 bytes
        innodb_buffer_free: 15.67 KiB
       innodb_buffer_pages: 1
innodb_buffer_pages_hashed: 0
   innodb_buffer_pages_old: 1
 innodb_buffer_rows_cached: 6
*************************** 2. row ***************************
              table_schema: mysql
                table_name: dd_properties
              rows_fetched: 0
             fetch_latency:   0 ps
             rows_inserted: 0
            insert_latency:   0 ps
              rows_updated: 0
            update_latency:   0 ps
              rows_deleted: 0
            delete_latency:   0 ps
          io_read_requests: NULL
                   io_read: NULL
           io_read_latency: NULL
         io_write_requests: NULL
                  io_write: NULL
          io_write_latency: NULL
          io_misc_requests: NULL
           io_misc_latency: NULL
   innodb_buffer_allocated: 16.00 KiB
        innodb_buffer_data:   47 bytes
        innodb_buffer_free: 15.95 KiB
       innodb_buffer_pages: 1
innodb_buffer_pages_hashed: 0
   innodb_buffer_pages_old: 1
 innodb_buffer_rows_cached: 1
2 rows in set (0.10 sec)
```

### 28.4.3.31 schema_tables_with_full_table_scans および x$schema_tables_with_full_table_scans のビュー

全テーブルスキャンでアクセスされているテーブルが表示されます

```sql
mysql> SELECT * FROM sys.schema_tables_with_full_table_scans;
+---------------+-------------+-------------------+---------+
| object_schema | object_name | rows_full_scanned | latency |
+---------------+-------------+-------------------+---------+
| sys           | sys_config  |                 6 | 1.36 ms |
+---------------+-------------+-------------------+---------+
1 row in set (0.01 sec)
```

### 28.4.3.32 schema_unused_indexes ビュー

使用されていないことを示すイベントがないインデックスが表示されます

```sql
mysql> SELECT * FROM sys.schema_unused_indexes limit 5;
+--------------------+-----------------+----------------------------------+
| object_schema      | object_name     | index_name                       |
+--------------------+-----------------+----------------------------------+
| performance_schema | data_lock_waits | BLOCKING_ENGINE_LOCK_ID          |
| performance_schema | data_lock_waits | BLOCKING_THREAD_ID               |
| performance_schema | data_lock_waits | REQUESTING_ENGINE_TRANSACTION_ID |
| performance_schema | data_lock_waits | REQUESTING_THREAD_ID             |
| performance_schema | data_lock_waits | BLOCKING_ENGINE_TRANSACTION_ID   |
+--------------------+-----------------+----------------------------------+
5 rows in set (0.01 sec)
```

### 28.4.3.33 セッションおよび x$session ビュー

ユーザーセッションのみを表示するようにバックグラウンドプロセスをフィルタで除外します

```sql
mysql> SELECT * FROM sys.session\G
*************************** 1. row ***************************
                thd_id: 43
               conn_id: 5
                  user: sql/event_scheduler
                    db: NULL
               command: Connect
                 state: Waiting for next activation
                  time: 13864
     current_statement: NULL
      execution_engine: PRIMARY
     statement_latency: NULL
              progress: NULL
          lock_latency: NULL
           cpu_latency: NULL
         rows_examined: NULL
             rows_sent: NULL
         rows_affected: NULL
            tmp_tables: NULL
       tmp_disk_tables: NULL
             full_scan: NO
        last_statement: NULL
last_statement_latency: NULL
        current_memory: 65.71 KiB
             last_wait: NULL
     last_wait_latency: NULL
                source: NULL
           trx_latency: 1.84 ms
             trx_state: COMMITTED
        trx_autocommit: NO
                   pid: NULL
          program_name: NULL
*************************** 2. row ***************************
                thd_id: 51
               conn_id: 9
                  user: root@localhost
                    db: NULL
               command: Query
                 state: NULL
                  time: 0
     current_statement: SELECT * FROM sys.session
      execution_engine: PRIMARY
     statement_latency: 57.69 ms
              progress: NULL
          lock_latency: 6.00 us
           cpu_latency:   0 ps
         rows_examined: 0
             rows_sent: 0
         rows_affected: 0
            tmp_tables: 4
       tmp_disk_tables: 0
             full_scan: YES
        last_statement: NULL
last_statement_latency: NULL
        current_memory: 12.55 MiB
             last_wait: NULL
     last_wait_latency: NULL
                source: NULL
           trx_latency: 56.77 ms
             trx_state: ACTIVE
        trx_autocommit: YES
                   pid: 8496
          program_name: mysql
2 rows in set (0.06 sec)
```

### 28.4.3.34 session_ssl_status ビュー

接続ごとに SSL バージョン、暗号および再利用された SSL セッションの数が表示されます

```sql
mysql> SELECT * FROM sys.session_ssl_status;
+-----------+-------------+------------+---------------------+
| thread_id | ssl_version | ssl_cipher | ssl_sessions_reused |
+-----------+-------------+------------+---------------------+
|        51 |             |            | 0                   |
+-----------+-------------+------------+---------------------+
1 row in set (0.01 sec)
```

### 28.4.3.35 statement_analysis および x$statement_analysis のビュー

集計された統計とともに正規化されたステートメントがリストされます

```sql
mysql> SELECT * FROM sys.statement_analysis limit 2\G
*************************** 1. row ***************************
                query: SELECT * FROM `sys` . `io_global_by_file_by_bytes`
                   db: NULL
            full_scan: *
           exec_count: 1
            err_count: 0
           warn_count: 0
        total_latency: 270.68 ms
          max_latency: 270.68 ms
          avg_latency: 270.68 ms
         lock_latency: 60.00 us
          cpu_latency:   0 ps
            rows_sent: 274
        rows_sent_avg: 274
        rows_examined: 822
    rows_examined_avg: 822
        rows_affected: 0
    rows_affected_avg: 0
           tmp_tables: 0
      tmp_disk_tables: 0
          rows_sorted: 274
    sort_merge_passes: 0
max_controlled_memory: 1.23 MiB
     max_total_memory: 3.19 MiB
               digest: ec3b22463eb28331ea3f36f55c4f9465a8844e7e8212bda8d5ffa13381b77d2c
           first_seen: 2024-01-05 18:41:32.668190
            last_seen: 2024-01-05 18:41:32.668190
*************************** 2. row ***************************
                query: SELECT * FROM `sys` . `io_global_by_file_by_latency`
                   db: NULL
            full_scan: *
           exec_count: 1
            err_count: 0
           warn_count: 0
        total_latency: 255.70 ms
          max_latency: 255.70 ms
          avg_latency: 255.70 ms
         lock_latency: 12.00 us
          cpu_latency:   0 ps
            rows_sent: 274
        rows_sent_avg: 274
        rows_examined: 822
    rows_examined_avg: 822
        rows_affected: 0
    rows_affected_avg: 0
           tmp_tables: 0
      tmp_disk_tables: 0
          rows_sorted: 274
    sort_merge_passes: 0
max_controlled_memory: 1.22 MiB
     max_total_memory: 3.20 MiB
               digest: 0365ec2583f54d7563c12ff61cbcb32bd735eed5bffa1c9adc0673668d5ad2f8
           first_seen: 2024-01-05 18:47:06.997572
            last_seen: 2024-01-05 18:47:06.997572
2 rows in set (0.00 sec)
```

### 28.4.3.36 statements_with_errors_or_warnings および x$statements_with_errors_or_warnings ビュー

エラーまたは警告が生成された正規化されたステートメントが表示されます

```sql
mysql> SELECT * FROM sys.statements_with_errors_or_warnings limit 2 \G
*************************** 1. row ***************************
      query: TABLE `performance_schema` . `tp_thread_group_state`
         db: NULL
 exec_count: 1
     errors: 1
  error_pct: 100.0000
   warnings: 0
warning_pct: 0.0000
 first_seen: 2024-01-05 16:32:24.074493
  last_seen: 2024-01-05 16:32:24.074493
     digest: 6fddab4bdbebfc9b8609ea26d4de41c4cdc846e80ec5dbfa7d1b4c9ce4956d06
*************************** 2. row ***************************
      query: SELECT `version`
         db: NULL
 exec_count: 1
     errors: 1
  error_pct: 100.0000
   warnings: 0
warning_pct: 0.0000
 first_seen: 2024-01-05 16:33:18.050725
  last_seen: 2024-01-05 16:33:18.050725
     digest: 7622bb4ea4c67c72323188694cef635b4c12e9a1483822344e0b483cd17ea74e
2 rows in set (0.00 sec)
```

### 28.4.3.37 statements_with_full_table_scans および x$statements_with_full_table_scans のビュー

全テーブルスキャンを実行した正規化されたステートメントが表示されます

```sql
mysql> SELECT * FROM sys.statements_with_full_table_scans limit 2 \G
*************************** 1. row ***************************
                   query: SELECT * FROM `performance_sch ... _summary_global_by_event_name`
                      db: NULL
              exec_count: 1
           total_latency: 9.42 ms
     no_index_used_count: 1
no_good_index_used_count: 0
       no_index_used_pct: 100
               rows_sent: 399
           rows_examined: 399
           rows_sent_avg: 399
       rows_examined_avg: 399
              first_seen: 2024-01-05 16:36:37.383304
               last_seen: 2024-01-05 16:36:37.383304
                  digest: e57b6bdefcfbe963e76b14c4ecbaed79822f363f17ff21519e756a81ebe4dc24
*************************** 2. row ***************************
                   query: SELECT * FROM `sys` . `memory_by_user_by_current_bytes`
                      db: NULL
              exec_count: 1
           total_latency: 9.13 ms
     no_index_used_count: 1
no_good_index_used_count: 0
       no_index_used_pct: 100
               rows_sent: 3
           rows_examined: 3
           rows_sent_avg: 3
       rows_examined_avg: 3
              first_seen: 2024-01-05 18:59:52.151728
               last_seen: 2024-01-05 18:59:52.151728
                  digest: 1b550df163ef48bc025514ca40726305c86809308ffbf6857445d856bfdf9c03
2 rows in set (0.00 sec)
```

### 28.4.3.38 statements_with_runtimes_in_95th_percentile および x$statements_with_runtimes_in_95th_percentile のビュー

95 パーセンタイルのランタイムを含むステートメントがリストされます

```sql
mysql> SELECT * FROM sys.statements_with_runtimes_in_95th_percentile limit 2\G
*************************** 1. row ***************************
            query: SELECT * FROM `sys` . `io_global_by_file_by_bytes`
               db: NULL
        full_scan: *
       exec_count: 1
        err_count: 0
       warn_count: 0
    total_latency: 270.68 ms
      max_latency: 270.68 ms
      avg_latency: 270.68 ms
        rows_sent: 274
    rows_sent_avg: 274
    rows_examined: 822
rows_examined_avg: 822
       first_seen: 2024-01-05 18:41:32.668190
        last_seen: 2024-01-05 18:41:32.668190
           digest: ec3b22463eb28331ea3f36f55c4f9465a8844e7e8212bda8d5ffa13381b77d2c
*************************** 2. row ***************************
            query: SELECT * FROM `sys` . `io_global_by_file_by_latency`
               db: NULL
        full_scan: *
       exec_count: 1
        err_count: 0
       warn_count: 0
    total_latency: 255.70 ms
      max_latency: 255.70 ms
      avg_latency: 255.70 ms
        rows_sent: 274
    rows_sent_avg: 274
    rows_examined: 822
rows_examined_avg: 822
       first_seen: 2024-01-05 18:47:06.997572
        last_seen: 2024-01-05 18:47:06.997572
           digest: 0365ec2583f54d7563c12ff61cbcb32bd735eed5bffa1c9adc0673668d5ad2f8
2 rows in set (0.02 sec)
```

### 28.4.3.39 statements_with_sorting および x$statements_with_sorting のビュー

ソートを実行した正規化されたステートメントがリストされます

```sql
mysql> SELECT * FROM sys.statements_with_sorting limit 2\G
*************************** 1. row ***************************
            query: SELECT * FROM `sys` . `io_global_by_file_by_bytes`
               db: NULL
       exec_count: 1
    total_latency: 270.68 ms
sort_merge_passes: 0
  avg_sort_merges: 0
sorts_using_scans: 1
 sort_using_range: 0
      rows_sorted: 274
  avg_rows_sorted: 274
       first_seen: 2024-01-05 18:41:32.668190
        last_seen: 2024-01-05 18:41:32.668190
           digest: ec3b22463eb28331ea3f36f55c4f9465a8844e7e8212bda8d5ffa13381b77d2c
*************************** 2. row ***************************
            query: SELECT * FROM `sys` . `io_global_by_file_by_latency`
               db: NULL
       exec_count: 1
    total_latency: 255.70 ms
sort_merge_passes: 0
  avg_sort_merges: 0
sorts_using_scans: 1
 sort_using_range: 0
      rows_sorted: 274
  avg_rows_sorted: 274
       first_seen: 2024-01-05 18:47:06.997572
        last_seen: 2024-01-05 18:47:06.997572
           digest: 0365ec2583f54d7563c12ff61cbcb32bd735eed5bffa1c9adc0673668d5ad2f8
2 rows in set (0.00 sec)
```

### 28.4.3.40 statements_with_temp_tables および x$statements_with_temp_tables のビュー

一時テーブルを使用した正規化されたステートメントがリストされます

```sql

mysql> SELECT * FROM sys.statements_with_temp_tables limit 2\G
*************************** 1. row ***************************
                   query: SELECT * FROM `sys` . `schema_table_statistics_with_buffer`
                      db: NULL
              exec_count: 1
           total_latency: 113.66 ms
       memory_tmp_tables: 6
         disk_tmp_tables: 1
avg_tmp_tables_per_query: 6
  tmp_tables_to_disk_pct: 17
              first_seen: 2024-01-05 19:35:16.158430
               last_seen: 2024-01-05 19:35:16.158430
                  digest: e57241cd5ac0222b2063ebe6191473a7928a88b90d7d2d5bd827766345e741a4
*************************** 2. row ***************************
                   query: SELECT * FROM `sys` . `schema_ ... tatistics_with_buffer` LIMIT ?
                      db: NULL
              exec_count: 1
           total_latency: 101.11 ms
       memory_tmp_tables: 6
         disk_tmp_tables: 1
avg_tmp_tables_per_query: 6
  tmp_tables_to_disk_pct: 17
              first_seen: 2024-01-05 19:35:23.305316
               last_seen: 2024-01-05 19:35:23.305316
                  digest: 0493b1f4d9cfee26dc81988f54a715c9a15fd9f2bcf921cc5a351add37e32ac3
2 rows in set (0.00 sec)
```

### 28.4.3.41 user_summary および x$user_summary のビュー

ステートメントアクティビティ、ファイル I/O,および接続がユーザー別にグループ化されて要約されます

```sql
mysql> SELECT * FROM sys.user_summary;
+-----------------+------------+-------------------+-----------------------+-------------+----------+-----------------+---------------------+-------------------+--------------+----------------+------------------------+
| user            | statements | statement_latency | statement_avg_latency | table_scans | file_ios | file_io_latency | current_connections | total_connections | unique_hosts | current_memory | total_memory_allocated |
+-----------------+------------+-------------------+-----------------------+-------------+----------+-----------------+---------------------+-------------------+--------------+----------------+------------------------+
| root            |      12820 | 2.75 s            | 214.33 us             |          98 |      241 | 88.60 ms        |                   1 |                 3 |            1 | 6.04 MiB       | 1.46 GiB               |
| event_scheduler |          2 | 14.64 ms          | 7.32 ms               |           0 |        0 |   0 ps          |                   1 |                 2 |            1 | 65.71 KiB      | 470.99 KiB             |
| background      |          0 |   0 ps            |   0 ps                |           0 |     3216 | 3.50 s          |                  39 |                49 |            0 | 2.25 MiB       | 158.26 MiB             |
+-----------------+------------+-------------------+-----------------------+-------------+----------+-----------------+---------------------+-------------------+--------------+----------------+------------------------+
3 rows in set (0.03 sec)
```

### 28.4.3.42 user_summary_by_file_io および x$user_summary_by_file_io のビュー

ユーザー別にグループ化されたファイル I/O,が要約されます

```sql
mysql> SELECT * FROM sys.user_summary_by_file_io;
+-----------------+------+------------+
| user            | ios  | io_latency |
+-----------------+------+------------+
| background      | 3216 | 3.50 s     |
| root            |  241 | 88.60 ms   |
| event_scheduler |    0 |   0 ps     |
+-----------------+------+------------+
3 rows in set (0.01 sec)
```

### 28.4.3.43 user_summary_by_file_io_type および x$user_summary_by_file_io_type のビュー

ユーザーおよびイベントタイプ別にグループ化されたファイル I/O,が要約されます

```sql
mysql> SELECT * FROM sys.user_summary_by_file_io_type;
+------------+---------------------------------------+-------+-----------+-------------+
| user       | event_name                            | total | latency   | max_latency |
+------------+---------------------------------------+-------+-----------+-------------+
| background | wait/io/file/innodb/innodb_data_file  |  2121 | 2.27 s    | 36.57 ms    |
| background | wait/io/file/innodb/innodb_log_file   |   429 | 823.71 ms | 39.24 ms    |
| background | wait/io/file/innodb/innodb_dblwr_file |    55 | 281.22 ms | 239.21 ms   |
| background | wait/io/file/innodb/innodb_temp_file  |   528 | 88.32 ms  | 7.77 ms     |
| background | wait/io/file/sql/binlog_index         |    23 | 28.73 ms  | 25.17 ms    |
| background | wait/io/file/sql/binlog               |    20 | 5.47 ms   | 3.19 ms     |
| background | wait/io/file/sql/casetest             |    15 | 3.74 ms   | 993.47 us   |
| background | wait/io/file/sql/misc                 |     5 | 731.88 us | 179.10 us   |
| background | wait/io/file/sql/pid                  |     3 | 503.83 us | 321.21 us   |
| background | wait/io/file/sql/ERRMSG               |     5 | 339.95 us | 174.42 us   |
| background | wait/io/file/sql/slow_log             |     4 | 330.00 us | 220.67 us   |
| background | wait/io/file/mysys/cnf                |     5 | 255.99 us | 135.62 us   |
| background | wait/io/file/mysys/charset            |     3 | 187.79 us | 73.41 us    |
| root       | wait/io/file/innodb/innodb_temp_file  |    42 | 36.48 ms  | 11.07 ms    |
| root       | wait/io/file/innodb/innodb_data_file  |    82 | 32.56 ms  | 727.92 us   |
| root       | wait/io/file/sql/io_cache             |   111 | 12.76 ms  | 987.14 us   |
| root       | wait/io/file/myisammrg/MRG            |     6 | 6.79 ms   | 6.68 ms     |
+------------+---------------------------------------+-------+-----------+-------------+
17 rows in set (0.01 sec)
```

### 28.4.3.44 user_summary_by_stages および x$user_summary_by_stages のビュー

ユーザー別にグループ化されたステージが要約されます

```sql
mysql> SELECT * FROM sys.user_summary_by_stages;
+------------+-------------------------------+-------+---------------+-------------+
| user       | event_name                    | total | total_latency | avg_latency |
+------------+-------------------------------+-------+---------------+-------------+
| background | stage/innodb/buffer pool load |     1 | 804.50 us     | 804.50 us   |
+------------+-------------------------------+-------+---------------+-------------+
1 row in set (0.01 sec)
```

### 28.4.3.45 user_summary_by_statement_latency および x$user_summary_by_statement_latency のビュー

ユーザー別にグループ化されたステートメント全体の統計が要約されます

```Sql
mysql> SELECT * FROM sys.user_summary_by_statement_latency;
+-----------------+-------+---------------+-------------+--------------+-------------+-----------+---------------+---------------+------------+
| user            | total | total_latency | max_latency | lock_latency | cpu_latency | rows_sent | rows_examined | rows_affected | full_scans |
+-----------------+-------+---------------+-------------+--------------+-------------+-----------+---------------+---------------+------------+
| root            | 12824 | 2.79 s        | 300.03 ms   | 1.68 ms      |   0 ps      |     10973 |         30397 |             0 |        102 |
| event_scheduler |     2 | 14.64 ms      | 14.64 ms    | 6.00 us      |   0 ps      |         0 |             0 |             0 |          0 |
| background      |     0 |   0 ps        |   0 ps      |   0 ps       |   0 ps      |         0 |             0 |             0 |          0 |
+-----------------+-------+---------------+-------------+--------------+-------------+-----------+---------------+---------------+------------+
3 rows in set (0.01 sec)
```

### 28.4.3.46 user_summary_by_statement_type および x$user_summary_by_statement_type のビュー

ユーザーおよびステートメントタイプ別にグループ化された、実行されたステートメントに関する情報が要約されます

```sql
mysql> SELECT * FROM sys.user_summary_by_statement_type limit 2;
+-----------------+-----------+-------+---------------+-------------+--------------+-------------+-----------+---------------+---------------+------------+
| user            | statement | total | total_latency | max_latency | lock_latency | cpu_latency | rows_sent | rows_examined | rows_affected | full_scans |
+-----------------+-----------+-------+---------------+-------------+--------------+-------------+-----------+---------------+---------------+------------+
| event_scheduler | event     |     1 | 13.40 ms      | 13.40 ms    | 3.00 us      |   0 ps      |         0 |
   0 |             0 |          0 |
| event_scheduler | stmt      |     1 | 1.23 ms       | 1.23 ms     | 3.00 us      |   0 ps      |         0 |
   0 |             0 |          0 |
+-----------------+-----------+-------+---------------+-------------+--------------+-------------+-----------+---------------+---------------+------------+
2 rows in set (0.00 sec)
```

### 28.4.3.48 wait_classes_global_by_avg_latency および x$wait_classes_global_by_avg_latency のビュー

待機クラスの平均待機時間がイベントクラス別にグループ化されて要約されます

```sql
mysql> SELECT * FROM sys.wait_classes_global_by_avg_latency;
+-----------------+-------+---------------+-------------+-------------+-------------+
| event_class     | total | total_latency | min_latency | avg_latency | max_latency |
+-----------------+-------+---------------+-------------+-------------+-------------+
| wait/io/file    |  3457 | 3.59 s        |   0 ps      | 1.04 ms     | 239.21 ms   |
| wait/io/table   |     7 | 1.41 ms       | 45.46 us    | 201.46 us   | 1.36 ms     |
| wait/lock/table |    20 | 44.51 us      | 1.70 us     | 2.23 us     | 2.89 us     |
+-----------------+-------+---------------+-------------+-------------+-------------+
3 rows in set (0.01 sec)
```

### 28.4.3.49 wait_classes_global_by_latency および x$wait_classes_global_by_latency ビュー

待機クラスの合計待機時間がイベントクラス別にグループ化されて要約されます

```Sql
mysql> SELECT * FROM sys.wait_classes_global_by_latency;
+-----------------+-------+---------------+-------------+-------------+-------------+
| event_class     | total | total_latency | min_latency | avg_latency | max_latency |
+-----------------+-------+---------------+-------------+-------------+-------------+
| wait/io/file    |  3457 | 3.59 s        |   0 ps      | 1.04 ms     | 239.21 ms   |
| wait/io/table   |     7 | 1.41 ms       | 45.46 us    | 201.46 us   | 1.36 ms     |
| wait/lock/table |    20 | 44.51 us      | 1.70 us     | 2.23 us     | 2.89 us     |
+-----------------+-------+---------------+-------------+-------------+-------------+
3 rows in set (0.01 sec)
```

### 28.4.3.50 waits_by_host_by_latency および x$waits_by_host_by_latency のビュー

ホストおよびイベント別にグループ化された待機イベントが要約されます

```sql
mysql> SELECT * FROM sys.waits_by_host_by_latency;
+------------+---------------------------------------+-------+---------------+-------------+-------------+
| host       | event                                 | total | total_latency | avg_latency | max_latency |
+------------+---------------------------------------+-------+---------------+-------------+-------------+
| background | wait/io/file/innodb/innodb_data_file  |  2121 | 2.27 s        | 1.07 ms     | 36.57 ms    |
| background | wait/io/file/innodb/innodb_log_file   |   429 | 823.71 ms     | 1.92 ms     | 39.24 ms    |
| background | wait/io/file/innodb/innodb_dblwr_file |    55 | 281.22 ms     | 5.11 ms     | 239.21 ms   |
| background | wait/io/file/innodb/innodb_temp_file  |   528 | 88.32 ms      | 167.26 us   | 7.77 ms     |
| background | wait/io/file/sql/binlog_index         |    23 | 28.73 ms      | 1.25 ms     | 25.17 ms    |
| background | wait/io/file/sql/binlog               |    20 | 5.47 ms       | 273.59 us   | 3.19 ms     |
| background | wait/io/file/sql/casetest             |    15 | 3.74 ms       | 249.46 us   | 993.47 us   |
| background | wait/io/file/sql/misc                 |     5 | 731.88 us     | 146.38 us   | 179.10 us   |
| background | wait/io/file/sql/pid                  |     3 | 503.83 us     | 167.94 us   | 321.21 us   |
| background | wait/io/file/sql/ERRMSG               |     5 | 339.95 us     | 67.99 us    | 174.42 us   |
| background | wait/io/file/sql/slow_log             |     4 | 330.00 us     | 82.50 us    | 220.67 us   |
| background | wait/io/file/mysys/cnf                |     5 | 255.99 us     | 51.20 us    | 135.62 us   |
| background | wait/io/file/mysys/charset            |     3 | 187.79 us     | 62.60 us    | 73.41 us    |
| localhost  | wait/io/file/innodb/innodb_temp_file  |    42 | 36.48 ms      | 868.60 us   | 11.07 ms    |
| localhost  | wait/io/file/innodb/innodb_data_file  |    82 | 32.56 ms      | 397.11 us   | 727.92 us   |
| localhost  | wait/io/file/sql/io_cache             |   111 | 12.76 ms      | 114.96 us   | 987.14 us   |
| localhost  | wait/io/file/myisammrg/MRG            |     6 | 6.79 ms       | 1.13 ms     | 6.68 ms     |
| localhost  | wait/io/table/sql/handler             |     7 | 1.41 ms       | 201.46 us   | 1.36 ms     |
| localhost  | wait/lock/table/sql/handler           |    20 | 44.51 us      | 2.23 us     | 2.89 us     |
+------------+---------------------------------------+-------+---------------+-------------+-------------+
19 rows in set (0.00 sec)
```

### 28.4.3.51 waits_by_user_by_latency および x$waits_by_user_by_latency のビュー

ユーザーおよびイベント別にグループ化された待機イベントが要約されます

```sql
mysql> SELECT * FROM sys.waits_by_user_by_latency;
+------+--------------------------------------+-------+---------------+-------------+-------------+
| user | event                                | total | total_latency | avg_latency | max_latency |
+------+--------------------------------------+-------+---------------+-------------+-------------+
| root | wait/io/file/innodb/innodb_temp_file |    42 | 36.48 ms      | 868.60 us   | 11.07 ms    |
| root | wait/io/file/innodb/innodb_data_file |    82 | 32.56 ms      | 397.11 us   | 727.92 us   |
| root | wait/io/file/sql/io_cache            |   111 | 12.76 ms      | 114.96 us   | 987.14 us   |
| root | wait/io/file/myisammrg/MRG           |     6 | 6.79 ms       | 1.13 ms     | 6.68 ms     |
| root | wait/io/table/sql/handler            |     7 | 1.41 ms       | 201.46 us   | 1.36 ms     |
| root | wait/lock/table/sql/handler          |    20 | 44.51 us      | 2.23 us     | 2.89 us     |
+------+--------------------------------------+-------+---------------+-------------+-------------+
6 rows in set (0.01 sec)
```

### 28.4.3.52 waits_global_by_latency および x$waits_global_by_latency のビュー

イベント別にグループ化された待機イベントが要約されます

```sql
mysql> SELECT * FROM sys.waits_global_by_latency;
+---------------------------------------+-------+---------------+-------------+-------------+
| events                                | total | total_latency | avg_latency | max_latency |
+---------------------------------------+-------+---------------+-------------+-------------+
| wait/io/file/innodb/innodb_data_file  |  2193 | 2.30 s        | 1.05 ms     | 36.57 ms    |
| wait/io/file/innodb/innodb_log_file   |   429 | 823.71 ms     | 1.92 ms     | 39.24 ms    |
| wait/io/file/innodb/innodb_dblwr_file |    55 | 281.22 ms     | 5.11 ms     | 239.21 ms   |
| wait/io/file/innodb/innodb_temp_file  |   580 | 126.86 ms     | 218.73 us   | 11.07 ms    |
| wait/io/file/sql/binlog_index         |    23 | 28.73 ms      | 1.25 ms     | 25.17 ms    |
| wait/io/file/sql/io_cache             |   111 | 12.76 ms      | 114.96 us   | 987.14 us   |
| wait/io/file/myisammrg/MRG            |     6 | 6.79 ms       | 1.13 ms     | 6.68 ms     |
| wait/io/file/sql/binlog               |    20 | 5.47 ms       | 273.59 us   | 3.19 ms     |
| wait/io/file/sql/casetest             |    15 | 3.74 ms       | 249.46 us   | 993.47 us   |
| wait/io/table/sql/handler             |     7 | 1.41 ms       | 201.46 us   | 1.36 ms     |
| wait/io/file/sql/misc                 |     5 | 731.88 us     | 146.38 us   | 179.10 us   |
| wait/io/file/sql/pid                  |     3 | 503.83 us     | 167.94 us   | 321.21 us   |
| wait/io/file/sql/ERRMSG               |     5 | 339.95 us     | 67.99 us    | 174.42 us   |
| wait/io/file/sql/slow_log             |     4 | 330.00 us     | 82.50 us    | 220.67 us   |
| wait/io/file/mysys/cnf                |     5 | 255.99 us     | 51.20 us    | 135.62 us   |
| wait/io/file/mysys/charset            |     3 | 187.79 us     | 62.60 us    | 73.41 us    |
| wait/lock/table/sql/handler           |    20 | 44.51 us      | 2.23 us     | 2.89 us     |
+---------------------------------------+-------+---------------+-------------+-------------+
17 rows in set (0.01 sec)
```

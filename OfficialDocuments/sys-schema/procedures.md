### 28.4.4 sys スキーマストアドプロシージャ

### 28.4.4.1 create_synonym_db() プロシージャ

スキーマ名を指定すると、元のスキーマ内のすべてのテーブルおよびビューを参照するビューを含むシノニムスキーマを作成します

```Sql
mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| myschema           |
| mysql              |
| performance_schema |
| sakila             |
| sys                |
| test               |
| world              |
| world_x            |
+--------------------+
9 rows in set (0.01 sec)

mysql> CALL sys.create_synonym_db('INFORMATION_SCHEMA', 'sample');
+-------------------------------------------+
| summary                                   |
+-------------------------------------------+
| Created 79 views in the `sample` database |
+-------------------------------------------+
1 row in set (0.52 sec)

Query OK, 0 rows affected (0.52 sec)

mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| myschema           |
| mysql              |
| performance_schema |
| sakila             |
| sample             |
| sys                |
| test               |
| world              |
| world_x            |
+--------------------+
10 rows in set (0.00 sec)

mysql> SHOW FULL TABLES FROM sample;
+---------------------------------------+------------+
| Tables_in_sample                      | Table_type |
+---------------------------------------+------------+
| administrable_role_authorizations     | VIEW       |
| applicable_roles                      | VIEW       |
| character_sets                        | VIEW       |
| check_constraints                     | VIEW       |
| collation_character_set_applicability | VIEW       |
| collations                            | VIEW       |
| column_privileges                     | VIEW       |
| column_statistics                     | VIEW       |
| columns                               | VIEW       |
| columns_extensions                    | VIEW       |
| enabled_roles                         | VIEW       |
| engines                               | VIEW       |
| events                                | VIEW       |
| files                                 | VIEW       |
| innodb_buffer_page                    | VIEW       |
| innodb_buffer_page_lru                | VIEW       |
| innodb_buffer_pool_stats              | VIEW       |
| innodb_cached_indexes                 | VIEW       |
| innodb_cmp                            | VIEW       |
| innodb_cmp_per_index                  | VIEW       |
| innodb_cmp_per_index_reset            | VIEW       |
| innodb_cmp_reset                      | VIEW       |
| innodb_cmpmem                         | VIEW       |
| innodb_cmpmem_reset                   | VIEW       |
| innodb_columns                        | VIEW       |
| innodb_datafiles                      | VIEW       |
| innodb_fields                         | VIEW       |
| innodb_foreign                        | VIEW       |
| innodb_foreign_cols                   | VIEW       |
| innodb_ft_being_deleted               | VIEW       |
| innodb_ft_config                      | VIEW       |
| innodb_ft_default_stopword            | VIEW       |
| innodb_ft_deleted                     | VIEW       |
| innodb_ft_index_cache                 | VIEW       |
| innodb_ft_index_table                 | VIEW       |
| innodb_indexes                        | VIEW       |
| innodb_metrics                        | VIEW       |
| innodb_session_temp_tablespaces       | VIEW       |
| innodb_tables                         | VIEW       |
| innodb_tablespaces                    | VIEW       |
| innodb_tablespaces_brief              | VIEW       |
| innodb_tablestats                     | VIEW       |
| innodb_temp_table_info                | VIEW       |
| innodb_trx                            | VIEW       |
| innodb_virtual                        | VIEW       |
| key_column_usage                      | VIEW       |
| keywords                              | VIEW       |
| optimizer_trace                       | VIEW       |
| parameters                            | VIEW       |
| partitions                            | VIEW       |
| plugins                               | VIEW       |
| processlist                           | VIEW       |
| profiling                             | VIEW       |
| referential_constraints               | VIEW       |
| resource_groups                       | VIEW       |
| role_column_grants                    | VIEW       |
| role_routine_grants                   | VIEW       |
| role_table_grants                     | VIEW       |
| routines                              | VIEW       |
| schema_privileges                     | VIEW       |
| schemata                              | VIEW       |
| schemata_extensions                   | VIEW       |
| st_geometry_columns                   | VIEW       |
| st_spatial_reference_systems          | VIEW       |
| st_units_of_measure                   | VIEW       |
| statistics                            | VIEW       |
| table_constraints                     | VIEW       |
| table_constraints_extensions          | VIEW       |
| table_privileges                      | VIEW       |
| tables                                | VIEW       |
| tables_extensions                     | VIEW       |
| tablespaces                           | VIEW       |
| tablespaces_extensions                | VIEW       |
| triggers                              | VIEW       |
| user_attributes                       | VIEW       |
| user_privileges                       | VIEW       |
| view_routine_usage                    | VIEW       |
| view_table_usage                      | VIEW       |
| views                                 | VIEW       |
+---------------------------------------+------------+
79 rows in set (0.00 sec)
```

### 28.4.4.2 diagnostics() プロシージャ

診断目的で現在のサーバーステータスのレポートを作成します

```sql
mysql> tee diag.out;
mysql> CALL sys.diagnostics(10, 10, 'current');
mysql> notee;
```

### 28.4.4.3 execute_prepared_stmt() プロシージャ

SQL ステートメントを文字列として指定すると、準備されたステートメントとして実行されます

```sql
mysql> CALL sys.execute_prepared_stmt('SELECT COUNT(*) FROM mysql.user');
+----------+
| COUNT(*) |
+----------+
|        5 |
+----------+
1 row in set (0.00 sec)

Query OK, 0 rows affected (0.00 sec)
```

### 28.4.4.4 ps_setup_disable_background_threads() プロシージャ

すべてのバックグラウンドスレッドのパフォーマンススキーマインストゥルメンテーションを無効にします

```Sql
mysql> CALL sys.ps_setup_disable_background_threads();
+--------------------------------+
| summary                        |
+--------------------------------+
| Disabled 38 background threads |
+--------------------------------+
1 row in set (0.00 sec)

Query OK, 0 rows affected (0.00 sec)
```

### 28.4.4.5 ps_setup_disable_consumer() プロシージャ

引数を含む名前を持つパフォーマンススキーマコンシューマを無効にします

```sql
mysql> CALL sys.ps_setup_disable_consumer('statement');
+----------------------+
| summary              |
+----------------------+
| Disabled 3 consumers |
+----------------------+
1 row in set (0.00 sec)

Query OK, 0 rows affected (0.00 sec)
```

### 28.4.4.6 ps_setup_disable_instrument() プロシージャ

引数を含む名前を持つパフォーマンススキーマインストゥルメントを無効にします

```sql
mysql> CALL sys.ps_setup_disable_instrument('wait/lock/metadata/sql/mdl');
+-----------------------+
| summary               |
+-----------------------+
| Disabled 1 instrument |
+-----------------------+
1 row in set (0.00 sec)

Query OK, 0 rows affected (0.00 sec)
```

### 28.4.4.7 ps_setup_disable_thread() プロシージャ

接続 ID を指定すると、スレッドのパフォーマンススキーマインストゥルメンテーションが無効になります

```sql
mysql> CALL sys.ps_setup_disable_thread(CONNECTION_ID());
+--------------------+
| summary            |
+--------------------+
| Disabled 0 threads |
+--------------------+
1 row in set (0.00 sec)

Query OK, 0 rows affected (0.00 sec)
```

### 28.4.4.8 ps_setup_enable_background_threads() プロシージャ

すべてのバックグラウンドスレッドのパフォーマンススキーマインストゥルメンテーションを有効にします

```sql
mysql> CALL sys.ps_setup_enable_background_threads();
+-------------------------------+
| summary                       |
+-------------------------------+
| Enabled 38 background threads |
+-------------------------------+
1 row in set (0.00 sec)

Query OK, 0 rows affected (0.00 sec)
```

### 28.4.4.9 ps_setup_enable_consumer() プロシージャ

引数を含む名前を持つパフォーマンススキーマコンシューマを有効にします

```sql
mysql> CALL sys.ps_setup_enable_consumer('statement');
+---------------------+
| summary             |
+---------------------+
| Enabled 5 consumers |
+---------------------+
1 row in set (0.00 sec)

Query OK, 0 rows affected (0.00 sec)
```

### 28.4.4.10 ps_setup_enable_instrument() プロシージャ

引数を含む名前を持つパフォーマンススキーマインストゥルメントを有効にします

```sql
mysql> CALL sys.ps_setup_enable_instrument('wait/lock/metadata/sql/mdl');
+----------------------+
| summary              |
+----------------------+
| Enabled 1 instrument |
+----------------------+
1 row in set (0.00 sec)

Query OK, 0 rows affected (0.00 sec)
```

### 28.4.4.11 ps_setup_enable_thread() プロシージャ

接続 ID を指定すると、スレッドのパフォーマンススキーマインストゥルメンテーションが有効になります

```sql
mysql> CALL sys.ps_setup_enable_thread(225);
+-------------------+
| summary           |
+-------------------+
| Enabled 0 threads |
+-------------------+
1 row in set (0.00 sec)

Query OK, 0 rows affected (0.00 sec)
```

### 28.4.4.13 ps_setup_reset_to_default() プロシージャ

パフォーマンススキーマ構成をデフォルト設定にリセットします

```Sql
mysql> CALL sys.ps_setup_reset_to_default(TRUE)\G
*************************** 1. row ***************************
status: Resetting: setup_actors
DELETE
FROM performance_schema.setup_actors
 WHERE NOT (HOST = '%' AND USER = '%' AND `ROLE` = '%')
1 row in set (0.00 sec)

*************************** 1. row ***************************
status: Resetting: setup_actors
INSERT IGNORE INTO performance_schema.setup_actors
VALUES ('%', '%', '%', 'YES', 'YES')
1 row in set (0.01 sec)

*************************** 1. row ***************************
status: Resetting: setup_instruments
UPDATE performance_schema.setup_instruments
 SET ENABLED = sys.ps_is_instrument_default_enabled(NAME),
 TIMED = sys.ps_is_instrument_default_timed(NAME)
1 row in set (0.01 sec)

*************************** 1. row ***************************
status: Resetting: setup_consumers
UPDATE performance_schema.setup_consumers
 SET ENABLED = IF(NAME IN ('events_statements_current', 'events_transactions_current', 'global_instrumentation', 'thread_instrumentation', 'statements_digest'), 'YES', 'NO')
1 row in set (0.05 sec)

*************************** 1. row ***************************
status: Resetting: setup_objects
DELETE
FROM performance_schema.setup_objects
 WHERE NOT (OBJECT_TYPE IN ('EVENT', 'FUNCTION', 'PROCEDURE', 'TABLE', 'TRIGGER') AND OBJECT_NAME = '%'
 AND (OBJECT_SCHEMA = 'mysql'AND ENABLED = 'NO'AND TIMED = 'NO' )
OR (OBJECT_SCHEMA = 'performance_schema' AND ENABLED = 'NO'AND TIMED = 'NO' )
OR (OBJECT_SCHEMA = 'information_schema' AND ENABLED = 'NO'AND TIMED = 'NO' )
OR (OBJECT_SCHEMA = '%'AND ENABLED = 'YES' AND TIMED = 'YES'))
1 row in set (0.06 sec)

*************************** 1. row ***************************
status: Resetting: setup_objects
INSERT IGNORE INTO performance_schema.setup_objects
VALUES ('EVENT', 'mysql' , '%', 'NO' , 'NO' ),
 ('EVENT', 'performance_schema', '%', 'NO' , 'NO' ),
 ('EVENT', 'information_schema', '%', 'NO' , 'NO' ),
 ('EVENT', '%' , '%', 'YES', 'YES'),
 ('FUNCTION' , 'mysql' , '%', 'NO' , 'NO' ),
 ('FUNCTION' , 'performance_schema', '%', 'NO' , 'NO' ),
 ('FUNCTION' , 'information_schema', '%', 'NO' , 'NO' ),
 ('FUNCTION' , '%' , '%', 'YES', 'YES'),
 ('PROCEDURE', 'mysql' , '%', 'NO' , 'NO' ),
 ('PROCEDURE', 'performance_schema', '%', 'NO' , 'NO' ),
 ('PROCEDURE', 'information_schema', '%', 'NO' , 'NO' ),
 ('PROCEDURE', '%' , '%', 'YES', 'YES'),
 ('TABLE', 'mysql' , '%', 'NO' , 'NO' ),
 ('TABLE', 'performance_schema', '%', 'NO' , 'NO' ),
 ('TABLE', 'information_schema', '%', 'NO' , 'NO' ),
 ('TABLE', '%' , '%', 'YES', 'YES'),
 ('TRIGGER', 'mysql' , '%', 'NO' , 'NO' ),
 ('TRIGGER', 'performance_schema', '%', 'NO' , 'NO' ),
 ('TRIGGER', 'information_schema', '%', 'NO' , 'NO' ),
 ('TRIGGER', '%' , '%', 'YES', 'YES')
1 row in set (0.07 sec)

*************************** 1. row ***************************
status: Resetting: threads
UPDATE performance_schema.threads
 SET INSTRUMENTED = 'YES'
1 row in set (0.09 sec)

Query OK, 0 rows affected (0.10 sec)
```

### 28.4.4.14 ps_setup_save() プロシージャ

現在のパフォーマンススキーマ構成を保存します

```sql
mysql> CALL sys.ps_setup_save(10);
Query OK, 0 rows affected (0.03 sec)

mysql> CALL sys.ps_setup_reload_saved();
Query OK, 0 rows affected (0.02 sec)
```

### 28.4.4.15 ps_setup_show_disabled() プロシージャ

現在無効になっているすべてのパフォーマンススキーマ構成を表示します

```Sql
mysql> CALL sys.ps_setup_show_disabled(TRUE, TRUE);
+----------------------------+
| performance_schema_enabled |
+----------------------------+
|                          1 |
+----------------------------+
1 row in set (0.00 sec)

Empty set (0.00 sec)

+-------------+----------------------+---------+-------+
| object_type | objects              | enabled | timed |
+-------------+----------------------+---------+-------+
| EVENT       | information_schema.% | NO      | NO    |
| EVENT       | mysql.%              | NO      | NO    |
| EVENT       | performance_schema.% | NO      | NO    |
| FUNCTION    | information_schema.% | NO      | NO    |
・・・
```

### 28.4.4.16 ps_setup_show_disabled_consumers() プロシージャ

現在無効になっているすべてのパフォーマンススキーマコンシューマを表示します

```Sql
mysql> CALL sys.ps_setup_show_disabled_consumers();
+----------------------------------+
| disabled_consumers               |
+----------------------------------+
| events_stages_current            |
| events_stages_history            |
| events_stages_history_long       |
| events_statements_cpu            |
| events_statements_history        |
| events_statements_history_long   |
| events_transactions_history      |
| events_transactions_history_long |
| events_waits_current             |
| events_waits_history             |
| events_waits_history_long        |
+----------------------------------+
11 rows in set (0.00 sec)

Query OK, 0 rows affected (0.01 sec)
```

### 28.4.4.17 ps_setup_show_disabled_instruments() プロシージャ

現在無効になっているすべてのパフォーマンススキーマインストゥルメントを表示します

```Sql
mysql> CALL sys.ps_setup_show_disabled_instruments()\G
*************************** 1. row ***************************
disabled_instruments: stage/mysys/Waiting for table level lock
               timed: NO
*************************** 2. row ***************************
disabled_instruments: stage/sql/After create
               timed: NO
*************************** 3. row ***************************
disabled_instruments: stage/sql/altering table
               timed: NO
*************************** 4. row ***************************
disabled_instruments: stage/sql/Changing replication source
               timed: NO
*************************** 5. row ***************************
disabled_instruments: stage/sql/checking permissions
               timed: NO
*************************** 6. row ***************************
disabled_instruments: stage/sql/Checking source version
               timed: NO
・・・
```

### 28.4.4.18 ps_setup_show_enabled() プロシージャ

現在有効になっているすべてのパフォーマンススキーマ構成を表示します

```sql
mysql> CALL sys.ps_setup_show_enabled(FALSE, FALSE);
+----------------------------+
| performance_schema_enabled |
+----------------------------+
|                          1 |
+----------------------------+
1 row in set (0.00 sec)

+---------------+
| enabled_users |
+---------------+
| '%'@'%'       |
+---------------+
1 row in set (0.00 sec)

+-------------+---------+---------+-------+
| object_type | objects | enabled | timed |
+-------------+---------+---------+-------+
| EVENT       | %.%     | YES     | YES   |
| FUNCTION    | %.%     | YES     | YES   |
| PROCEDURE   | %.%     | YES     | YES   |
| TABLE       | %.%     | YES     | YES   |
| TRIGGER     | %.%     | YES     | YES   |
+-------------+---------+---------+-------+
5 rows in set (0.00 sec)

+-----------------------------+
| enabled_consumers           |
+-----------------------------+
| events_statements_current   |
| events_transactions_current |
| global_instrumentation      |
| statements_digest           |
| thread_instrumentation      |
+-----------------------------+
5 rows in set (0.01 sec)

Query OK, 0 rows affected (0.01 sec)
```

### 28.4.4.19 ps_setup_show_enabled_consumers() プロシージャ

現在有効になっているすべてのパフォーマンススキーマコンシューマを表示します

```Sql
mysql> CALL sys.ps_setup_show_enabled_consumers();
+-----------------------------+
| enabled_consumers           |
+-----------------------------+
| events_statements_current   |
| events_transactions_current |
| global_instrumentation      |
| statements_digest           |
| thread_instrumentation      |
+-----------------------------+
5 rows in set (0.00 sec)

Query OK, 0 rows affected (0.01 sec)
```

### 28.4.4.20 ps_setup_show_enabled_instruments() プロシージャ

現在有効になっているパフォーマンススキーマインストゥルメントをすべて表示します

```sql
mysql> CALL sys.ps_setup_show_disabled_instruments()\G
*************************** 1. row ***************************
disabled_instruments: stage/mysys/Waiting for table level lock
               timed: NO
*************************** 2. row ***************************
disabled_instruments: stage/sql/After create
               timed: NO
*************************** 3. row ***************************
disabled_instruments: stage/sql/altering table
               timed: NO
*************************** 4. row ***************************
disabled_instruments: stage/sql/Changing replication source
               timed: NO
```

### 28.4.4.21 ps_statement_avg_latency_histogram() プロシージャ

パフォーマンススキーマ events_statements_summary_by_digest テーブル内で追跡されたすべての正規化ステートメントの平均待機時間値のテキストヒストグラムグラフを表示します

```sql
mysql> CALL sys.ps_statement_avg_latency_histogram()\G
*************************** 1. row ***************************
Performance Schema Statement Digest Average Latency Histogram:

  . = 1 unit
  * = 2 units
  # = 3 units

(0 - 3620ms)     129 | #################################################################################################################################
(3620 - 7240ms)  0   |
(7240 - 10860ms) 0   |
(10860 - 14480ms)0   |
(14480 - 18100ms)0   |
(18100 - 21720ms)0   |
(21720 - 25340ms)0   |
(25340 - 28960ms)0   |
(28960 - 32580ms)0   |
(32580 - 36200ms)0   |
(36200 - 39820ms)0   |
(39820 - 43440ms)0   |
(43440 - 47060ms)0   |
(47060 - 50680ms)0   |
(50680 - 54300ms)0   |
(54300 - 57920ms)0   |

  Total Statements: 130; Buckets: 16; Bucket Size: 3620 ms;

1 row in set (0.05 sec)

Query OK, 0 rows affected (0.07 sec)
```

### 28.4.4.22 ps_trace_statement_digest() プロシージャ

特定のステートメントダイジェストのすべてのパフォーマンススキーマインストゥルメンテーションをトレースします

```sql
mysql> CALL sys.ps_trace_statement_digest('891ec6860f98ba46d89dd20b0c03652c', 10, 0.1, TRUE, TRUE);
+-------------------+
| summary           |
+-------------------+
| Disabled 1 thread |
+-------------------+
1 row in set (0.00 sec)

+--------------------+
| SUMMARY STATISTICS |
+--------------------+
| SUMMARY STATISTICS |
+--------------------+
1 row in set (9.88 sec)

+------------+-----------+-----------+-----------+---------------+---------------+------------+------------+
| executions | exec_time | lock_time | rows_sent | rows_affected | rows_examined | tmp_tables | full_scans |
+------------+-----------+-----------+-----------+---------------+---------------+------------+------------+
|          0 | NULL      | NULL      |      NULL |          NULL |          NULL |       NULL |       NULL |
+------------+-----------+-----------+-----------+---------------+---------------+------------+------------+
1 row in set (9.88 sec)

Empty set (9.89 sec)

+---------------------------+
| LONGEST RUNNING STATEMENT |
+---------------------------+
| LONGEST RUNNING STATEMENT |
+---------------------------+
1 row in set (9.89 sec)

Empty set (9.90 sec)

Empty set (9.90 sec)

+------------------+
| summary          |
+------------------+
| Enabled 1 thread |
+------------------+
1 row in set (9.95 sec)

Query OK, 0 rows affected (9.95 sec)
```

### 28.4.4.23 ps_trace_thread() プロシージャ

インストゥルメントされたスレッドのすべてのパフォーマンススキーマデータを .dot 形式のグラフファイルにダンプします

```Sql
mysql> CALL sys.ps_trace_thread(25, CONCAT('/tmp/stack-', REPLACE(NOW(), ' ', '-'), '.dot'), NULL, NULL, TRUE, TRUE, TRUE);
ERROR 2013 (HY000): Lost connection to MySQL server during query
No connection. Trying to reconnect...
Connection id:    8
Current database: *** NONE ***

+-------------------+
| summary           |
+-------------------+
| Disabled 1 thread |
+-------------------+
1 row in set (0.04 sec)

+---------------------------------------------+
| Info                                        |
+---------------------------------------------+
| Data collection starting for THREAD_ID = 25 |
+---------------------------------------------+
1 row in set (0.09 sec)

ERROR 1 (HY000): Can't create/write to file '\tmp\stack-2024-01-05-22:56:59.dot' (OS errno 13 - Permission denied)
```

### 28.4.4.24 ps_truncate_all_tables() プロシージャ

すべてのパフォーマンススキーマサマリーテーブルを切り捨てて、すべての集約インストゥルメンテーションをスナップショットとしてリセットします

```sql
mysql> CALL sys.ps_truncate_all_tables(FALSE);
+---------------------+
| summary             |
+---------------------+
| Truncated 49 tables |
+---------------------+
1 row in set (0.11 sec)

Query OK, 0 rows affected (0.11 sec)
```

### 28.4.4.25 statement_performance_analyzer() プロシージャ

サーバーで実行されているステートメントのレポートを作成します

```sql
mysql>  CALL sys.statement_performance_analyzer('create_tmp', 'test.tmp_digests_ini', NULL);
Query OK, 0 rows affected (0.01 sec)

mysql> CALL sys.statement_performance_analyzer('snapshot', NULL, NULL);
Query OK, 0 rows affected (0.01 sec)

mysql> CALL sys.statement_performance_analyzer('save', 'test.tmp_digests_ini', NULL);
Query OK, 0 rows affected (0.00 sec)

mysql> DO SLEEP(60);
Query OK, 0 rows affected (1 min 0.01 sec)

mysql> CALL sys.statement_performance_analyzer('snapshot', NULL, NULL);
Query OK, 0 rows affected (0.00 sec)

mysql> CALL sys.statement_performance_analyzer('overall', NULL, 'with_runtimes_in_95th_percentile');
+-----------------------------------------+
| Next Output                             |
+-----------------------------------------+
| Queries with Runtime in 95th Percentile |
+-----------------------------------------+
1 row in set (0.01 sec)

Empty set (0.02 sec)

Query OK, 0 rows affected (0.02 sec)

mysql> CALL sys.statement_performance_analyzer('delta', 'mydb.tmp_digests_ini', 'with_runtimes_in_95th_percentile');
ERROR 1049 (42000): Unknown database 'mydb'
mysql> CALL sys.statement_performance_analyzer('delta', 'test.tmp_digests_ini', 'with_runtimes_in_95th_percentile');
+-----------------------------------------+
| Next Output                             |
+-----------------------------------------+
| Queries with Runtime in 95th Percentile |
+-----------------------------------------+
1 row in set (0.00 sec)

Empty set (0.01 sec)

Query OK, 0 rows affected (0.01 sec)
```

### 28.4.4.26 table_exists() プロシージャ

特定のテーブルが通常のテーブル、TEMPORARY テーブルまたはビューとして存在するかどうかをテストします

```sql
mysql> CREATE DATABASE db1;
Query OK, 1 row affected (0.00 sec)

mysql> USE db1;
Database changed
mysql> CREATE TABLE t1 (id INT PRIMARY KEY);
Query OK, 0 rows affected (0.04 sec)

mysql> CREATE TABLE t2 (id INT PRIMARY KEY);
Query OK, 0 rows affected (0.02 sec)

mysql> CREATE view v_t1 AS SELECT * FROM t1;
Query OK, 0 rows affected (0.00 sec)

mysql> CREATE TEMPORARY TABLE t1 (id INT PRIMARY KEY);
Query OK, 0 rows affected (0.00 sec)

mysql> CALL sys.table_exists('db1', 't1', @exists); SELECT @exists;
Query OK, 0 rows affected (0.00 sec)

+-----------+
| @exists   |
+-----------+
| TEMPORARY |
+-----------+
1 row in set (0.00 sec)

mysql> CALL sys.table_exists('db1', 't2', @exists); SELECT @exists;
Query OK, 0 rows affected (0.00 sec)

+------------+
| @exists    |
+------------+
| BASE TABLE |
+------------+
1 row in set (0.00 sec)

mysql> CALL sys.table_exists('db1', 'v_t1', @exists); SELECT @exists;
Query OK, 0 rows affected (0.00 sec)

+---------+
| @exists |
+---------+
| VIEW    |
+---------+
1 row in set (0.00 sec)

mysql> CALL sys.table_exists('db1', 't3', @exists); SELECT @exists;
Query OK, 0 rows affected (0.00 sec)

+---------+
| @exists |
+---------+
|         |
+---------+
1 row in set (0.00 sec)
```

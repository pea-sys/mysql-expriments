### 27.4.8 コンシューマ構成の例

setup_consumers テーブル内のコンシューマ設定は、高いレベルから低いレベルまでの階層を形成します

```
global_instrumentation
 thread_instrumentation
   events_waits_current
     events_waits_history
     events_waits_history_long
   events_stages_current
     events_stages_history
     events_stages_history_long
   events_statements_current
     events_statements_history
     events_statements_history_long
   events_transactions_current
     events_transactions_history
     events_transactions_history_long
 statements_digest
```

### 27.4.9 フィルタリング操作のインストゥルメントまたはコンシューマの指定

単一のインストゥルメントまたはコンシューマを示すには、その名前を省略せずに指定します

```sql
mysql> UPDATE performance_schema.setup_instruments
    -> SET ENABLED = 'NO'
    -> WHERE NAME = 'wait/synch/mutex/myisammrg/MYRG_INFO::mutex';
Query OK, 0 rows affected (0.00 sec)
Rows matched: 1  Changed: 0  Warnings: 0

mysql>
mysql> UPDATE performance_schema.setup_consumers
    -> SET ENABLED = 'NO'
    -> WHERE NAME = 'events_waits_current';
Query OK, 0 rows affected (0.00 sec)
Rows matched: 1  Changed: 0  Warnings: 0
```

インストゥルメントまたはコンシューマのグループを指定するには、グループメンバーに一致するパターンを使用します

```Sql
mysql> UPDATE performance_schema.setup_instruments
    -> SET ENABLED = 'NO'
    -> WHERE NAME LIKE 'wait/synch/mutex/%';
Query OK, 13 rows affected (0.00 sec)
Rows matched: 233  Changed: 13  Warnings: 0

mysql>
mysql> UPDATE performance_schema.setup_consumers
    -> SET ENABLED = 'NO'
    -> WHERE NAME LIKE '%history%';
Query OK, 0 rows affected (0.00 sec)
Rows matched: 8  Changed: 0  Warnings: 0
```

### 27.4.10 インストゥルメントされているものの特定

パフォーマンススキーマにどのインストゥルメントが含まれているかを特定するには、常に setup_instruments テーブルをチェックすることで可能です

```Sql
mysql> SELECT NAME, ENABLED, TIMED
    ->        FROM performance_schema.setup_instruments
    ->        WHERE NAME LIKE 'wait/io/file/innodb/%';
+-------------------------------------------------+---------+-------+
| NAME                                            | ENABLED | TIMED |
+-------------------------------------------------+---------+-------+
| wait/io/file/innodb/innodb_dblwr_file           | NO      | NO    |
| wait/io/file/innodb/innodb_tablespace_open_file | NO      | NO    |
| wait/io/file/innodb/innodb_data_file            | NO      | NO    |
| wait/io/file/innodb/innodb_log_file             | NO      | NO    |
| wait/io/file/innodb/innodb_temp_file            | NO      | NO    |
| wait/io/file/innodb/innodb_arch_file            | NO      | NO    |
| wait/io/file/innodb/innodb_clone_file           | NO      | NO    |
| wait/io/file/innodb/meb::redo_log_archive_file  | NO      | NO    |
+-------------------------------------------------+---------+-------+
8 rows in set (0.00 sec)
```

### 27.5 パフォーマンススキーマクエリー

イベントテーブルにファイルと非ファイルの両方の情報が格納されている場合、事後フィルタリングはファイルイベントのみの情報を表示するもう 1 つの方法です。 イベント選択を適切に制限するには、クエリーに WHERE 句を追加します。

```sql
mysql> SELECT THREAD_ID, NUMBER_OF_BYTES
    ->        FROM performance_schema.events_waits_history
    ->        WHERE EVENT_NAME LIKE 'wait/io/file/%'
    ->        AND NUMBER_OF_BYTES IS NOT NULL;
Empty set (0.01 sec)
```

### 27.7 パフォーマンススキーマステータスモニタリング

パフォーマンススキーマに関連付けられたいくつかのステータス変数があります

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
| Performance_schema_session_connect_attrs_longest_seen | 127   |
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
33 rows in set (0.02 sec)
```

Performance_schema_mutex_classes_lost は失われたインストゥルメント (相互排他ロッククラス) の数を示します

```sql
mysql> SHOW STATUS LIKE "perf%mutex_classes_lost";
+---------------------------------------+-------+
| Variable_name                         | Value |
+---------------------------------------+-------+
| Performance_schema_mutex_classes_lost | 0     |
+---------------------------------------+-------+
1 row in set (0.00 sec)
```

SHOW ENGINE PERFORMANCE_SCHEMA STATUS を使用して、パフォーマンススキーマコードの内部操作を検査します

```sql
mysql> SHOW ENGINE PERFORMANCE_SCHEMA STATUS\G
*************************** 1. row ***************************
  Type: performance_schema
  Name: events_waits_current.size
Status: 168
*************************** 2. row ***************************
  Type: performance_schema
  Name: events_waits_current.count
Status: 1536
*************************** 3. row ***************************
  Type: performance_schema
  Name: events_waits_history.size
Status: 168
*************************** 4. row ***************************
  Type: performance_schema
  Name: events_waits_history.count
Status: 2560
*************************** 5. row ***************************
  Type: performance_schema
  Name: events_waits_history.memory
Status: 430080
・・・
```

### 27.11 パフォーマンススキーマの一般的なテーブル特性

performance_schema データベースの多くのテーブルは読取り専用で、変更できません

```Sql
mysql> TRUNCATE TABLE performance_schema.setup_instruments;
ERROR 1142 (42000): DROP command denied to user 'root'@'localhost' for table 'setup_instruments'

mysql> GRANT ALL ON performance_schema.*
    ->        TO 'u1'@'localhost';
ERROR 1044 (42000): Access denied for user 'root'@'localhost' to database 'performance_schema'
```

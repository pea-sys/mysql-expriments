### 28.4.5 sys スキーマストアドファンクション

### 28.4.5.1 extract_schema_from_file_name() 関数

ファイルパス名を指定すると、スキーマ名を表すパスコンポーネントを返します

```Sql
mysql> SELECT sys.extract_schema_from_file_name('/usr/local/mysql/data/world/City.ibd');
+---------------------------------------------------------------------------+
| sys.extract_schema_from_file_name('/usr/local/mysql/data/world/City.ibd') |
+---------------------------------------------------------------------------+
| world                                                                     |
+---------------------------------------------------------------------------+
1 row in set (0.03 sec)

mysql> SELECT sys.extract_schema_from_file_name('C:/ProgramData/MySQL/MySQL Server 8.2/Data/sakila/city.ibd');
+-------------------------------------------------------------------------------------------------+
| sys.extract_schema_from_file_name('C:/ProgramData/MySQL/MySQL Server 8.2/Data/sakila/city.ibd') |
+-------------------------------------------------------------------------------------------------+
| sakila                                                                                          |
+-------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)
```

### 28.4.5.2 extract_table_from_file_name() 関数

ファイルパス名を指定すると、テーブル名を表すパスコンポーネントを返します

```Sql
mysql>  SELECT sys.extract_table_from_file_name('C:/ProgramData/MySQL/MySQL Server 8.2/Data/sakila/city.ibd');
+------------------------------------------------------------------------------------------------+
| sys.extract_table_from_file_name('C:/ProgramData/MySQL/MySQL Server 8.2/Data/sakila/city.ibd') |
+------------------------------------------------------------------------------------------------+
| city                                                                                           |
+------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)
```

### 28.4.5.3 format_bytes() 関数

バイト数を指定すると、人間が読める形式に変換され、値と単位インジケータで構成される文字列が返されます

```Sql
mysql> SELECT sys.format_bytes(512), sys.format_bytes(18446644073709551615);
+-----------------------+----------------------------------------+
| sys.format_bytes(512) | sys.format_bytes(18446644073709551615) |
+-----------------------+----------------------------------------+
|  512 bytes            | 16.00 EiB                              |
+-----------------------+----------------------------------------+
1 row in set, 1 warning (0.01 sec)
```

### 28.4.5.4 format_path() 関数

パス名を指定すると、次のシステム変数の値と一致するサブパスを次の順序で置換した後に、変更されたパス名を返します

```Sql
mysql> SELECT sys.format_path('C:/ProgramData/MySQL/MySQL Server 8.2/Data/sakila/city.ibd');
+-------------------------------------------------------------------------------+
| sys.format_path('C:/ProgramData/MySQL/MySQL Server 8.2/Data/sakila/city.ibd') |
+-------------------------------------------------------------------------------+
| C:/ProgramData/MySQL/MySQL Server 8.2/Data/sakila/city.ibd                    |
+-------------------------------------------------------------------------------+
1 row in set (0.02 sec)
```

### 28.4.5.5 format_statement() 関数

文字列 (通常は SQL ステートメントを表します) を指定すると、statement_truncate_len 構成オプションで指定された長さまで短くなり、結果が返されます

```sql
mysql> SET @stmt = 'SELECT variable, value, set_time, set_by FROM sys_config';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT sys.format_statement(@stmt);
+----------------------------------------------------------+
| sys.format_statement(@stmt)                              |
+----------------------------------------------------------+
| SELECT variable, value, set_time, set_by FROM sys_config |
+----------------------------------------------------------+
1 row in set (0.02 sec)

mysql> SET @sys.statement_truncate_len = 32;
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT sys.format_statement(@stmt);
+-----------------------------------+
| sys.format_statement(@stmt)       |
+-----------------------------------+
| SELECT variabl ... ROM sys_config |
+-----------------------------------+
1 row in set (0.00 sec)
```

### 28.4.5.6 format_time() 関数

ピコ秒単位のパフォーマンススキーマレイテンシまたは待機時間を指定すると、それを人間が読める形式に変換し、値と単位インジケータで構成される文字列を返します

```sql
mysql> SELECT sys.format_time(3501), sys.format_time(188732396662000);
+-----------------------+----------------------------------+
| sys.format_time(3501) | sys.format_time(188732396662000) |
+-----------------------+----------------------------------+
| 3.5 ns                | 3.15 m                           |
+-----------------------+----------------------------------+
1 row in set (0.00 sec)
```

### 28.4.5.7 list_add() 関数

カンマ区切りの値リストに値を追加し、結果を返します

```sql
mysql> SET @@sql_mode = sys.list_add(@@sql_mode, 'NO_ENGINE_SUBSTITUTION');
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT @@sql_mode;
+-----------------------------------------------------------------------------------------------------------------------+
| @@sql_mode
|
+-----------------------------------------------------------------------------------------------------------------------+
| ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION |
+-----------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)
```

### 28.4.5.8 list_drop() 関数

カンマ区切りの値リストから値を削除し、結果を返します

```sql
mysql> SET @@sql_mode = sys.list_drop(@@sql_mode, 'NO_ENGINE_SUBSTITUTION');
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT @@sql_mode;
+------------------------------------------------------------------------------------------------+
| @@sql_mode                                                                                     |
+------------------------------------------------------------------------------------------------+
| ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO |
+------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)
```

### 28.4.5.9 ps_is_account_enabled() 関数

指定されたアカウントのパフォーマンススキーマインストゥルメンテーションが有効かどうかを示す YES または NO を返します

```Sql
mysql> SELECT sys.ps_is_account_enabled('localhost', 'root');
+------------------------------------------------+
| sys.ps_is_account_enabled('localhost', 'root') |
+------------------------------------------------+
| YES                                            |
+------------------------------------------------+
1 row in set (0.01 sec)
```

### 28.4.5.10 ps_is_consumer_enabled() 関数

指定されたパフォーマンススキーマコンシューマが有効かどうかを示す YES または NO を返します

```Sql
mysql> SELECT sys.ps_is_consumer_enabled('thread_instrumentation');
+------------------------------------------------------+
| sys.ps_is_consumer_enabled('thread_instrumentation') |
+------------------------------------------------------+
| YES                                                  |
+------------------------------------------------------+
1 row in set (0.00 sec)
```

### 28.4.5.11 ps_is_instrument_default_enabled() 関数

指定されたパフォーマンススキーマインストゥルメントがデフォルトで有効になっているかどうかを示す YES または NO を返します

```Sql
mysql> SELECT sys.ps_is_instrument_default_enabled('memory/innodb/row_log_buf');
+-------------------------------------------------------------------+
| sys.ps_is_instrument_default_enabled('memory/innodb/row_log_buf') |
+-------------------------------------------------------------------+
| YES                                                               |
+-------------------------------------------------------------------+
1 row in set (0.00 sec)
```

### 28.4.5.12 ps_is_instrument_default_timed() 関数

指定されたパフォーマンススキーマインストゥルメントがデフォルトで時間指定されているかどうかを示す YES または NO を返します

```sql
mysql> SELECT sys.ps_is_instrument_default_timed('memory/innodb/row_log_buf');
+-----------------------------------------------------------------+
| sys.ps_is_instrument_default_timed('memory/innodb/row_log_buf') |
+-----------------------------------------------------------------+
| NO                                                              |
+-----------------------------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT sys.ps_is_instrument_default_timed('statement/sql/alter_user');
+----------------------------------------------------------------+
| sys.ps_is_instrument_default_timed('statement/sql/alter_user') |
+----------------------------------------------------------------+
| YES                                                            |
+----------------------------------------------------------------+
1 row in set (0.00 sec)
```

### 28.4.5.13 ps_is_thread_instrumented() 関数

指定された接続 ID のパフォーマンススキーマインストゥルメンテーションが有効かどうかを示す YES または NO を返します

```Sql
mysql> SELECT sys.ps_is_thread_instrumented(43);
+-----------------------------------+
| sys.ps_is_thread_instrumented(43) |
+-----------------------------------+
| UNKNOWN                           |
+-----------------------------------+
1 row in set (0.01 sec)

mysql> SELECT sys.ps_is_thread_instrumented(CONNECTION_ID());
+------------------------------------------------+
| sys.ps_is_thread_instrumented(CONNECTION_ID()) |
+------------------------------------------------+
| YES                                            |
+------------------------------------------------+
1 row in set (0.00 sec)
```

### 28.4.5.14 ps_thread_account() 関数

パフォーマンススキーマスレッド ID を指定すると、スレッドに関連付けられた user_name@host_name アカウントを返します

```Sql
mysql> SELECT sys.ps_thread_account(sys.ps_thread_id(CONNECTION_ID()));
+----------------------------------------------------------+
| sys.ps_thread_account(sys.ps_thread_id(CONNECTION_ID())) |
+----------------------------------------------------------+
| root@localhost                                           |
+----------------------------------------------------------+
1 row in set, 1 warning (0.00 sec)
```

### 28.4.5.15 ps_thread_id() 関数

指定された接続 ID に割り当てられたパフォーマンススキーマスレッド ID、または接続 ID が NULL の場合は現在の接続のスレッド ID を返します

```Sql
mysql> SELECT sys.ps_thread_id(260);
+-----------------------+
| sys.ps_thread_id(260) |
+-----------------------+
|                    50 |
+-----------------------+
1 row in set (0.00 sec)
```

### 28.4.5.16 ps_thread_stack() 関数

指定されたスレッド ID のパフォーマンススキーマ内のすべてのステートメント、ステージおよびイベントの JSON 形式のスタックを返します

```Sql
mysql> SELECT sys.ps_thread_stack(37, FALSE) AS thread_stack\G
*************************** 1. row ***************************
thread_stack: {"rankdir": "LR","nodesep": "0.10","stack_created": "2024-01-06 07:39:54","mysql_version": "8.2.0","mysql_user": "root@localhost","events": []}
1 row in set (0.02 sec)
```

### 28.4.5.17 ps_thread_trx_info() 関数

指定されたスレッドに関する情報を含む JSON オブジェクトを返します

```Sql
mysql> SELECT sys.ps_thread_trx_info(1)\G
*************************** 1. row ***************************
sys.ps_thread_trx_info(1): [
  {
    "time": "532.40 us",
    "state": "COMMITTED",
    "mode": "READ WRITE",
    "autocommitted": "NO",
    "gtid": "AUTOMATIC",
    "isolation": "REPEATABLE READ",
    "statements_executed": [ ]
  },
  {
    "time": "15.20 us",
    "state": "COMMITTED",
    "mode": "READ WRITE",
    "autocommitted": "YES",
    "gtid": "AUTOMATIC",
    "isolation": "REPEATABLE READ",
    "statements_executed": [ ]
  },
  {
    "time": "103.70 us",
    "state": "COMMITTED",
    "mode": "READ WRITE",
    "autocommitted": "YES",
    "gtid": "AUTOMATIC",
    "isolation": "REPEATABLE READ",
    "statements_executed": [ ]
  },
  {
    "time": "2.09 ms",
    "state": "COMMITTED",
    "mode": "READ WRITE",
    "autocommitted": "YES",
    "gtid": "AUTOMATIC",
    "isolation": "REPEATABLE READ",
    "statements_executed": [ ]
  },
  {
    "time": "125.90 us",
    "state": "COMMITTED",
    "mode": "READ WRITE",
    "autocommitted": "YES",
    "gtid": "AUTOMATIC",
    "isolation": "REPEATABLE READ",
    "statements_executed": [ ]
  },
  {
    "time": "64.30 us",
    "state": "COMMITTED",
    "mode": "READ WRITE",
    "autocommitted": "YES",
    "gtid": "AUTOMATIC",
    "isolation": "REPEATABLE READ",
    "statements_executed": [ ]
  },
  {
    "time": "54.70 us",
    "state": "COMMITTED",
    "mode": "READ WRITE",
    "autocommitted": "YES",
    "gtid": "AUTOMATIC",
    "isolation": "REPEATABLE READ",
    "statements_executed": [ ]
  },
  {
    "time": "71.20 us",
    "state": "COMMITTED",
    "mode": "READ WRITE",
    "autocommitted": "YES",
    "gtid": "AUTOMATIC",
    "isolation": "REPEATABLE READ",
    "statements_executed": [ ]
  },
  {
    "time": "37.70 us",
    "state": "COMMITTED",
    "mode": "READ WRITE",
    "autocommitted": "YES",
    "gtid": "AUTOMATIC",
    "isolation": "REPEATABLE READ",
    "statements_executed": [ ]
  },
  {
    "time": "67.00 us",
    "state": "COMMITTED",
    "mode": "READ WRITE",
    "autocommitted": "YES",
    "gtid": "AUTOMATIC",
    "isolation": "REPEATABLE READ",
    "statements_executed": [ ]
  }
]
1 row in set (0.01 sec)
```

### 28.4.5.18 quote_identifier() 関数

文字列引数を指定すると、この関数は SQL ステートメントに含めるのに適した引用符付き識別子を生成します

```Sql
mysql> SELECT sys.quote_identifier('plain');
+-------------------------------+
| sys.quote_identifier('plain') |
+-------------------------------+
| `plain`                       |
+-------------------------------+
1 row in set (0.00 sec)

mysql> SELECT sys.quote_identifier('trick`ier');
+-----------------------------------+
| sys.quote_identifier('trick`ier') |
+-----------------------------------+
| `trick``ier`                      |
+-----------------------------------+
1 row in set (0.00 sec)
```

### 28.4.5.19 sys_get_config() 関数

構成オプション名を指定すると、sys_config テーブルからオプション値を返します

```Sql
mysql> SELECT sys.sys_get_config('statement_truncate_len', 128) AS Value;
+-------+
| Value |
+-------+
| 64    |
+-------+
1 row in set (0.00 sec)
```

### 28.4.5.20 version_major() 関数

MySQL サーバーのメジャーバージョンを戻します

```Sql
mysql> SELECT VERSION(), sys.version_major();
+-----------+---------------------+
| VERSION() | sys.version_major() |
+-----------+---------------------+
| 8.2.0     |                   8 |
+-----------+---------------------+
1 row in set (0.00 sec)
```

### 28.4.5.21 version_minor() 関数

MySQL サーバーのマイナーバージョンを戻します

```Sql
mysql> SELECT VERSION(), sys.version_minor();
+-----------+---------------------+
| VERSION() | sys.version_minor() |
+-----------+---------------------+
| 8.2.0     |                   2 |
+-----------+---------------------+
1 row in set (0.00 sec)
```

### 28.4.5.22 version_patch() 関数

この関数は、MySQL サーバーのパッチリリースバージョンを戻します

```Sql
mysql> SELECT VERSION(), sys.version_patch();
+-----------+---------------------+
| VERSION() | sys.version_patch() |
+-----------+---------------------+
| 8.2.0     |                   0 |
+-----------+---------------------+
1 row in set (0.00 sec)
```

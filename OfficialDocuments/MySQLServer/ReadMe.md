## 5.4.1 一般クエリーログおよびスロークエリーログの出力先の選択


### ログテーブルの利点と特性

```
mysql> SHOW CREATE TABLE mysql.general_log;
+-------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Table       | Create Table                                                                                                                                                                                                                                                                                                                                                                      |
+-------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| general_log | CREATE TABLE `general_log` (
  `event_time` timestamp(6) NOT NULL DEFAULT CURRENT_TIMESTAMP(6) ON UPDATE CURRENT_TIMESTAMP(6),
  `user_host` mediumtext NOT NULL,
  `thread_id` bigint unsigned NOT NULL,
  `server_id` int unsigned NOT NULL,
  `command_type` varchar(64) NOT NULL,
  `argument` mediumblob NOT NULL
) ENGINE=CSV DEFAULT CHARSET=utf8mb3 COMMENT='General log' |
+-------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)

mysql> SHOW CREATE TABLE mysql.slow_log;
+----------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Table    | Create Table                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
+----------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| slow_log | CREATE TABLE `slow_log` (
  `start_time` timestamp(6) NOT NULL DEFAULT CURRENT_TIMESTAMP(6) ON UPDATE CURRENT_TIMESTAMP(6),
  `user_host` mediumtext NOT NULL,
  `query_time` time(6) NOT NULL,
  `lock_time` time(6) NOT NULL,
  `rows_sent` int NOT NULL,
  `rows_examined` int NOT NULL,
  `db` varchar(512) NOT NULL,
  `last_insert_id` int NOT NULL,
  `insert_id` int NOT NULL,
  `server_id` int unsigned NOT NULL,
  `sql_text` mediumblob NOT NULL,
  `thread_id` bigint unsigned NOT NULL
) ENGINE=CSV DEFAULT CHARSET=utf8mb3 COMMENT='Slow log' |
+----------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)
```

* RENAME TABLE は、ログテーブル上での有効な操作です。 次の方法を使用して、(たとえばログローテーションを実行するために) ログテーブルを原子的に名前変更できます。
```
mysql> USE mysql;
Database changed
mysql> DROP TABLE IF EXISTS general_log2;
Query OK, 0 rows affected, 1 warning (0.01 sec)

mysql> CREATE TABLE general_log2 LIKE general_log;
Query OK, 0 rows affected (0.01 sec)

mysql> RENAME TABLE general_log TO general_log_backup, general_log2 TO general_log;
Query OK, 0 rows affected (0.02 sec)
```

### 5.4.2.1 エラーログ構成
```
mysql> SELECT @@GLOBAL.log_error_services;
+----------------------------------------+
| @@GLOBAL.log_error_services            |
+----------------------------------------+
| log_filter_internal; log_sink_internal |
+----------------------------------------+
1 row in set (0.00 sec)
```

* デフォルトのシンク (log_sink_internal) のかわりにシステムログシンク (log_sink_syseventlog) を使用するには、まずシンクコンポーネントをロードしてから、log_error_services 値を変更します
```
mysql> INSTALL COMPONENT 'file://component_log_sink_syseventlog';
Query OK, 0 rows affected (0.06 sec)

mysql> SET GLOBAL log_error_services = 'log_filter_internal; log_sink_syseventlog';
Query OK, 0 rows affected, 1 warning (0.00 sec)
```
* 複数のログ出力先設定
```
mysql> SET GLOBAL log_error_services = 'log_filter_internal; log_sink_internal; log_sink_syseventlog';
Query OK, 0 rows affected (0.00 sec)
```
* デフォルトのシンクのみを使用してシステムログシンクをアンロードするには、次のステートメントを実行
```
mysql> SET GLOBAL log_error_services = 'log_filter_internal; log_sink_internal';
Query OK, 0 rows affected (0.00 sec)

mysql> UNINSTALL COMPONENT 'file://component_log_sink_syseventlog';
Query OK, 0 rows affected (0.01 sec)
```
* サーバーの起動ごとに有効になるようにログコンポーネントを構成する
```
mysql> INSTALL COMPONENT 'file://component_log_sink_json';
Query OK, 0 rows affected (0.04 sec)

mysql> SET PERSIST log_error_services = 'log_filter_internal; log_sink_internal; log_sink_json';
Query OK, 0 rows affected (0.01 sec)
```

### 5.4.2.6 ルールベースのエラーログのフィルタリング (log_filter_dragnet)
log_filter_dragnet のログフィルタコンポーネントを使用すると、ユーザー定義ルールに基づいてログをフィルタリングできます。
```
mysql> INSTALL COMPONENT 'file://component_log_filter_dragnet';
Query OK, 0 rows affected (0.08 sec)

mysql> SET GLOBAL log_error_services = 'log_filter_dragnet; log_sink_internal';
Query OK, 0 rows affected (0.00 sec)
```
例 1 このルールセットは情報イベントを削除し、他のイベントの場合は source_line フィールドを削除します:
```
mysql> SET GLOBAL dragnet.log_error_filter_rules =
    ->   'IF prio>=INFORMATION THEN drop. IF EXISTS source_line THEN unset source_line.';
Query OK, 0 rows affected (0.00 sec)
```
例 2 : このルールは、情報イベントを 60 秒当たり 1 つ以下に制限します:
```
mysql> SET GLOBAL dragnet.log_error_filter_rules =
    ->   'IF prio>=INFORMATION THEN throttle 1/60.';
Query OK, 0 rows affected (0.00 sec)
```
フィルタ log_filter_dragnet コンポーネントをアンインストールします:
```
UNINSTALL COMPONENT 'file://component_log_filter_dragnet';
```

### 5.4.2.8 システムログへのエラーロギング
mysqld では、エラーログをシステムログ (Windows の場合はイベントログ、Unix および Unix に似たシステムの場合は syslog) に書き込むことができます。
```
mysql> INSTALL COMPONENT 'file://component_log_sink_syseventlog';
Query OK, 0 rows affected (0.03 sec)

mysql> SET PERSIST log_error_services = 'log_filter_internal; log_sink_syseventlog';
Query OK, 0 rows affected, 1 warning (0.02 sec)
```
ログを無効にすることによって、実行時に一般クエリーログファイルを名前変更することができます。
```
mysql> SET GLOBAL general_log = 'OFF';
Query OK, 0 rows affected (0.01 sec)

mysql> SET GLOBAL general_log = 'ON';
Query OK, 0 rows affected (0.02 sec)
```

### 5.4.4.2 バイナリログ形式の設定
* STATEMENT の場合、ロギングはステートメントに基づきます。
* ROW の場合、ロギングは行に基づきます。 これはデフォルトです。
* MIXED の場合、ロギングは混合形式を使用します。
  
  変更後に接続するクライアントの形式を指定
```
mysql> SET GLOBAL binlog_format = 'STATEMENT';
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql> SET GLOBAL binlog_format = 'ROW';
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql> SET GLOBAL binlog_format = 'MIXED';
Query OK, 0 rows affected, 1 warning (0.00 sec)
```
クライアント自身のステートメントについてのロギング形式を制御する
```
mysql> SET SESSION binlog_format = 'STATEMENT';
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql> SET SESSION binlog_format = 'ROW';
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql> SET SESSION binlog_format = 'MIXED';
Query OK, 0 rows affected, 1 warning (0.00 sec)
```

### 5.5.1 コンポーネントのインストールおよびアンインストール
 MySQL は、実行時の手動コンポーネントロードおよびサーバー起動時の自動ロードをサポートしています。
```
mysql> INSTALL COMPONENT 'file://component_validate_password';
Query OK, 0 rows affected (0.05 sec)

mysql> UNINSTALL COMPONENT 'file://component_validate_password';
Query OK, 0 rows affected (0.03 sec)
```
### 5.5.2 コンポーネント情報の取得
```
mysql> SELECT * FROM mysql.component;
+--------------+--------------------+---------------------------------------+
| component_id | component_group_id | component_urn                         |
+--------------+--------------------+---------------------------------------+
|            2 |                  2 | file://component_log_sink_json        |
|            3 |                  3 | file://component_log_filter_dragnet   |
|            6 |                  5 | file://component_log_sink_syseventlog |
+--------------+--------------------+---------------------------------------+
3 rows in set (0.01 sec)
```
### 5.6.2 サーバープラグイン情報の取得
```
mysql> SELECT * FROM INFORMATION_SCHEMA.PLUGINS\G
*************************** 1. row ***************************
           PLUGIN_NAME: binlog
        PLUGIN_VERSION: 1.0
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: STORAGE ENGINE
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: This is a pseudo storage engine to represent the binlog in a transaction
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 2. row ***************************
           PLUGIN_NAME: sha256_password
        PLUGIN_VERSION: 1.1
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: AUTHENTICATION
   PLUGIN_TYPE_VERSION: 2.1
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: SHA256 password authentication
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 3. row ***************************
           PLUGIN_NAME: caching_sha2_password
        PLUGIN_VERSION: 1.0
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: AUTHENTICATION
   PLUGIN_TYPE_VERSION: 2.1
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: Caching sha2 authentication
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 4. row ***************************
           PLUGIN_NAME: sha2_cache_cleaner
        PLUGIN_VERSION: 1.0
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: AUDIT
   PLUGIN_TYPE_VERSION: 4.1
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: Cache cleaner for Caching sha2 authentication
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 5. row ***************************
           PLUGIN_NAME: daemon_keyring_proxy_plugin
        PLUGIN_VERSION: 1.0
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: DAEMON
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle
    PLUGIN_DESCRIPTION: A plugin that implements the keyring component services atop of the keyring plugin
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 6. row ***************************
           PLUGIN_NAME: CSV
        PLUGIN_VERSION: 1.0
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: STORAGE ENGINE
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: CSV storage engine
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 7. row ***************************
           PLUGIN_NAME: MEMORY
        PLUGIN_VERSION: 1.0
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: STORAGE ENGINE
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: Hash based, stored in memory, useful for temporary tables
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 8. row ***************************
           PLUGIN_NAME: InnoDB
        PLUGIN_VERSION: 8.2
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: STORAGE ENGINE
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: Supports transactions, row-level locking, and foreign keys
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 9. row ***************************
           PLUGIN_NAME: INNODB_TRX
        PLUGIN_VERSION: 8.2
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: INFORMATION SCHEMA
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: InnoDB transactions
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 10. row ***************************
           PLUGIN_NAME: INNODB_CMP
        PLUGIN_VERSION: 8.2
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: INFORMATION SCHEMA
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: Statistics for the InnoDB compression
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 11. row ***************************
           PLUGIN_NAME: INNODB_CMP_RESET
        PLUGIN_VERSION: 8.2
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: INFORMATION SCHEMA
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: Statistics for the InnoDB compression; reset cumulated counts
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 12. row ***************************
           PLUGIN_NAME: INNODB_CMPMEM
        PLUGIN_VERSION: 8.2
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: INFORMATION SCHEMA
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: Statistics for the InnoDB compressed buffer pool
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 13. row ***************************
           PLUGIN_NAME: INNODB_CMPMEM_RESET
        PLUGIN_VERSION: 8.2
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: INFORMATION SCHEMA
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: Statistics for the InnoDB compressed buffer pool; reset cumulated counts
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 14. row ***************************
           PLUGIN_NAME: INNODB_CMP_PER_INDEX
        PLUGIN_VERSION: 8.2
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: INFORMATION SCHEMA
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: Statistics for the InnoDB compression (per index)
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 15. row ***************************
           PLUGIN_NAME: INNODB_CMP_PER_INDEX_RESET
        PLUGIN_VERSION: 8.2
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: INFORMATION SCHEMA
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: Statistics for the InnoDB compression (per index); reset cumulated counts
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 16. row ***************************
           PLUGIN_NAME: INNODB_BUFFER_PAGE
        PLUGIN_VERSION: 8.2
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: INFORMATION SCHEMA
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: InnoDB Buffer Page Information
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 17. row ***************************
           PLUGIN_NAME: INNODB_BUFFER_PAGE_LRU
        PLUGIN_VERSION: 8.2
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: INFORMATION SCHEMA
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: InnoDB Buffer Page in LRU
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 18. row ***************************
           PLUGIN_NAME: INNODB_BUFFER_POOL_STATS
        PLUGIN_VERSION: 8.2
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: INFORMATION SCHEMA
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: InnoDB Buffer Pool Statistics Information
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 19. row ***************************
           PLUGIN_NAME: INNODB_TEMP_TABLE_INFO
        PLUGIN_VERSION: 8.2
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: INFORMATION SCHEMA
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: InnoDB Temp Table Stats
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 20. row ***************************
           PLUGIN_NAME: INNODB_METRICS
        PLUGIN_VERSION: 8.2
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: INFORMATION SCHEMA
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: InnoDB Metrics Info
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 21. row ***************************
           PLUGIN_NAME: INNODB_FT_DEFAULT_STOPWORD
        PLUGIN_VERSION: 8.2
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: INFORMATION SCHEMA
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: Default stopword list for InnDB Full Text Search
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 22. row ***************************
           PLUGIN_NAME: INNODB_FT_DELETED
        PLUGIN_VERSION: 8.2
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: INFORMATION SCHEMA
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: INNODB AUXILIARY FTS DELETED TABLE
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 23. row ***************************
           PLUGIN_NAME: INNODB_FT_BEING_DELETED
        PLUGIN_VERSION: 8.2
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: INFORMATION SCHEMA
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: INNODB AUXILIARY FTS BEING DELETED TABLE
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 24. row ***************************
           PLUGIN_NAME: INNODB_FT_CONFIG
        PLUGIN_VERSION: 8.2
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: INFORMATION SCHEMA
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: INNODB AUXILIARY FTS CONFIG TABLE
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 25. row ***************************
           PLUGIN_NAME: INNODB_FT_INDEX_CACHE
        PLUGIN_VERSION: 8.2
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: INFORMATION SCHEMA
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: INNODB AUXILIARY FTS INDEX CACHED
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 26. row ***************************
           PLUGIN_NAME: INNODB_FT_INDEX_TABLE
        PLUGIN_VERSION: 8.2
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: INFORMATION SCHEMA
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: INNODB AUXILIARY FTS INDEX TABLE
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 27. row ***************************
           PLUGIN_NAME: INNODB_TABLES
        PLUGIN_VERSION: 8.2
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: INFORMATION SCHEMA
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: InnoDB INNODB_TABLES
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 28. row ***************************
           PLUGIN_NAME: INNODB_TABLESTATS
        PLUGIN_VERSION: 8.2
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: INFORMATION SCHEMA
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: InnoDB INNODB_TABLESTATS
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 29. row ***************************
           PLUGIN_NAME: INNODB_INDEXES
        PLUGIN_VERSION: 8.2
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: INFORMATION SCHEMA
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: InnoDB INNODB_INDEXES
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 30. row ***************************
           PLUGIN_NAME: INNODB_TABLESPACES
        PLUGIN_VERSION: 8.2
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: INFORMATION SCHEMA
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: InnoDB INNODB_TABLESPACES
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 31. row ***************************
           PLUGIN_NAME: INNODB_COLUMNS
        PLUGIN_VERSION: 8.2
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: INFORMATION SCHEMA
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: InnoDB INNODB_COLUMNS
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 32. row ***************************
           PLUGIN_NAME: INNODB_VIRTUAL
        PLUGIN_VERSION: 8.2
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: INFORMATION SCHEMA
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: InnoDB INNODB_VIRTUAL
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 33. row ***************************
           PLUGIN_NAME: INNODB_CACHED_INDEXES
        PLUGIN_VERSION: 8.2
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: INFORMATION SCHEMA
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: InnoDB cached indexes
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 34. row ***************************
           PLUGIN_NAME: INNODB_SESSION_TEMP_TABLESPACES
        PLUGIN_VERSION: 8.2
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: INFORMATION SCHEMA
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: InnoDB Session Temporary tablespaces
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 35. row ***************************
           PLUGIN_NAME: MyISAM
        PLUGIN_VERSION: 1.0
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: STORAGE ENGINE
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: MyISAM storage engine
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 36. row ***************************
           PLUGIN_NAME: MRG_MYISAM
        PLUGIN_VERSION: 1.0
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: STORAGE ENGINE
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: Collection of identical MyISAM tables
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 37. row ***************************
           PLUGIN_NAME: PERFORMANCE_SCHEMA
        PLUGIN_VERSION: 0.1
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: STORAGE ENGINE
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: Performance Schema
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 38. row ***************************
           PLUGIN_NAME: TempTable
        PLUGIN_VERSION: 1.0
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: STORAGE ENGINE
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: InnoDB temporary storage engine
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: FORCE
*************************** 39. row ***************************
           PLUGIN_NAME: ARCHIVE
        PLUGIN_VERSION: 3.0
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: STORAGE ENGINE
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: Archive storage engine
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: ON
*************************** 40. row ***************************
           PLUGIN_NAME: BLACKHOLE
        PLUGIN_VERSION: 1.0
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: STORAGE ENGINE
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: /dev/null storage engine (anything you write to it disappears)
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: ON
*************************** 41. row ***************************
           PLUGIN_NAME: FEDERATED
        PLUGIN_VERSION: 1.0
         PLUGIN_STATUS: DISABLED
           PLUGIN_TYPE: STORAGE ENGINE
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: Federated MySQL storage engine
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: OFF
*************************** 42. row ***************************
           PLUGIN_NAME: ndbcluster
        PLUGIN_VERSION: 1.0
         PLUGIN_STATUS: DISABLED
           PLUGIN_TYPE: STORAGE ENGINE
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: Clustered, fault-tolerant tables
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: OFF
*************************** 43. row ***************************
           PLUGIN_NAME: ndbinfo
        PLUGIN_VERSION: 0.1
         PLUGIN_STATUS: DISABLED
           PLUGIN_TYPE: STORAGE ENGINE
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: MySQL Cluster system information storage engine
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: OFF
*************************** 44. row ***************************
           PLUGIN_NAME: ndb_transid_mysql_connection_map
        PLUGIN_VERSION: 0.1
         PLUGIN_STATUS: DISABLED
           PLUGIN_TYPE: INFORMATION SCHEMA
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: Map between MySQL connection ID and NDB transaction ID
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: OFF
*************************** 45. row ***************************
           PLUGIN_NAME: ngram
        PLUGIN_VERSION: 0.1
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: FTPARSER
   PLUGIN_TYPE_VERSION: 1.1
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: Ngram Full-Text Parser
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: ON
*************************** 46. row ***************************
           PLUGIN_NAME: mysqlx_cache_cleaner
        PLUGIN_VERSION: 1.0
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: AUDIT
   PLUGIN_TYPE_VERSION: 4.1
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: Cache cleaner for sha2 authentication in X plugin
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: ON
*************************** 47. row ***************************
           PLUGIN_NAME: mysqlx
        PLUGIN_VERSION: 1.0
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: DAEMON
   PLUGIN_TYPE_VERSION: 80200.0
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: X Plugin for MySQL
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: ON
*************************** 48. row ***************************
           PLUGIN_NAME: mysql_native_password
        PLUGIN_VERSION: 1.1
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: AUTHENTICATION
   PLUGIN_TYPE_VERSION: 2.1
        PLUGIN_LIBRARY: NULL
PLUGIN_LIBRARY_VERSION: NULL
         PLUGIN_AUTHOR: Oracle Corporation
    PLUGIN_DESCRIPTION: Native MySQL authentication
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: ON
48 rows in set (0.00 sec)
```

```
mysql> SHOW PLUGINS\G
*************************** 1. row ***************************
   Name: binlog
 Status: ACTIVE
   Type: STORAGE ENGINE
Library: NULL
License: GPL
*************************** 2. row ***************************
   Name: sha256_password
 Status: ACTIVE
   Type: AUTHENTICATION
Library: NULL
License: GPL
*************************** 3. row ***************************
   Name: caching_sha2_password
 Status: ACTIVE
   Type: AUTHENTICATION
Library: NULL
License: GPL
*************************** 4. row ***************************
   Name: sha2_cache_cleaner
 Status: ACTIVE
   Type: AUDIT
Library: NULL
License: GPL
*************************** 5. row ***************************
   Name: daemon_keyring_proxy_plugin
 Status: ACTIVE
   Type: DAEMON
Library: NULL
License: GPL
*************************** 6. row ***************************
   Name: CSV
 Status: ACTIVE
   Type: STORAGE ENGINE
Library: NULL
License: GPL
*************************** 7. row ***************************
   Name: MEMORY
 Status: ACTIVE
   Type: STORAGE ENGINE
Library: NULL
License: GPL
*************************** 8. row ***************************
   Name: InnoDB
 Status: ACTIVE
   Type: STORAGE ENGINE
Library: NULL
License: GPL
*************************** 9. row ***************************
   Name: INNODB_TRX
 Status: ACTIVE
   Type: INFORMATION SCHEMA
Library: NULL
License: GPL
*************************** 10. row ***************************
   Name: INNODB_CMP
 Status: ACTIVE
   Type: INFORMATION SCHEMA
Library: NULL
License: GPL
*************************** 11. row ***************************
   Name: INNODB_CMP_RESET
 Status: ACTIVE
   Type: INFORMATION SCHEMA
Library: NULL
License: GPL
*************************** 12. row ***************************
   Name: INNODB_CMPMEM
 Status: ACTIVE
   Type: INFORMATION SCHEMA
Library: NULL
License: GPL
*************************** 13. row ***************************
   Name: INNODB_CMPMEM_RESET
 Status: ACTIVE
   Type: INFORMATION SCHEMA
Library: NULL
License: GPL
*************************** 14. row ***************************
   Name: INNODB_CMP_PER_INDEX
 Status: ACTIVE
   Type: INFORMATION SCHEMA
Library: NULL
License: GPL
*************************** 15. row ***************************
   Name: INNODB_CMP_PER_INDEX_RESET
 Status: ACTIVE
   Type: INFORMATION SCHEMA
Library: NULL
License: GPL
*************************** 16. row ***************************
   Name: INNODB_BUFFER_PAGE
 Status: ACTIVE
   Type: INFORMATION SCHEMA
Library: NULL
License: GPL
*************************** 17. row ***************************
   Name: INNODB_BUFFER_PAGE_LRU
 Status: ACTIVE
   Type: INFORMATION SCHEMA
Library: NULL
License: GPL
*************************** 18. row ***************************
   Name: INNODB_BUFFER_POOL_STATS
 Status: ACTIVE
   Type: INFORMATION SCHEMA
Library: NULL
License: GPL
*************************** 19. row ***************************
   Name: INNODB_TEMP_TABLE_INFO
 Status: ACTIVE
   Type: INFORMATION SCHEMA
Library: NULL
License: GPL
*************************** 20. row ***************************
   Name: INNODB_METRICS
 Status: ACTIVE
   Type: INFORMATION SCHEMA
Library: NULL
License: GPL
*************************** 21. row ***************************
   Name: INNODB_FT_DEFAULT_STOPWORD
 Status: ACTIVE
   Type: INFORMATION SCHEMA
Library: NULL
License: GPL
*************************** 22. row ***************************
   Name: INNODB_FT_DELETED
 Status: ACTIVE
   Type: INFORMATION SCHEMA
Library: NULL
License: GPL
*************************** 23. row ***************************
   Name: INNODB_FT_BEING_DELETED
 Status: ACTIVE
   Type: INFORMATION SCHEMA
Library: NULL
License: GPL
*************************** 24. row ***************************
   Name: INNODB_FT_CONFIG
 Status: ACTIVE
   Type: INFORMATION SCHEMA
Library: NULL
License: GPL
*************************** 25. row ***************************
   Name: INNODB_FT_INDEX_CACHE
 Status: ACTIVE
   Type: INFORMATION SCHEMA
Library: NULL
License: GPL
*************************** 26. row ***************************
   Name: INNODB_FT_INDEX_TABLE
 Status: ACTIVE
   Type: INFORMATION SCHEMA
Library: NULL
License: GPL
*************************** 27. row ***************************
   Name: INNODB_TABLES
 Status: ACTIVE
   Type: INFORMATION SCHEMA
Library: NULL
License: GPL
*************************** 28. row ***************************
   Name: INNODB_TABLESTATS
 Status: ACTIVE
   Type: INFORMATION SCHEMA
Library: NULL
License: GPL
*************************** 29. row ***************************
   Name: INNODB_INDEXES
 Status: ACTIVE
   Type: INFORMATION SCHEMA
Library: NULL
License: GPL
*************************** 30. row ***************************
   Name: INNODB_TABLESPACES
 Status: ACTIVE
   Type: INFORMATION SCHEMA
Library: NULL
License: GPL
*************************** 31. row ***************************
   Name: INNODB_COLUMNS
 Status: ACTIVE
   Type: INFORMATION SCHEMA
Library: NULL
License: GPL
*************************** 32. row ***************************
   Name: INNODB_VIRTUAL
 Status: ACTIVE
   Type: INFORMATION SCHEMA
Library: NULL
License: GPL
*************************** 33. row ***************************
   Name: INNODB_CACHED_INDEXES
 Status: ACTIVE
   Type: INFORMATION SCHEMA
Library: NULL
License: GPL
*************************** 34. row ***************************
   Name: INNODB_SESSION_TEMP_TABLESPACES
 Status: ACTIVE
   Type: INFORMATION SCHEMA
Library: NULL
License: GPL
*************************** 35. row ***************************
   Name: MyISAM
 Status: ACTIVE
   Type: STORAGE ENGINE
Library: NULL
License: GPL
*************************** 36. row ***************************
   Name: MRG_MYISAM
 Status: ACTIVE
   Type: STORAGE ENGINE
Library: NULL
License: GPL
*************************** 37. row ***************************
   Name: PERFORMANCE_SCHEMA
 Status: ACTIVE
   Type: STORAGE ENGINE
Library: NULL
License: GPL
*************************** 38. row ***************************
   Name: TempTable
 Status: ACTIVE
   Type: STORAGE ENGINE
Library: NULL
License: GPL
*************************** 39. row ***************************
   Name: ARCHIVE
 Status: ACTIVE
   Type: STORAGE ENGINE
Library: NULL
License: GPL
*************************** 40. row ***************************
   Name: BLACKHOLE
 Status: ACTIVE
   Type: STORAGE ENGINE
Library: NULL
License: GPL
*************************** 41. row ***************************
   Name: FEDERATED
 Status: DISABLED
   Type: STORAGE ENGINE
Library: NULL
License: GPL
*************************** 42. row ***************************
   Name: ndbcluster
 Status: DISABLED
   Type: STORAGE ENGINE
Library: NULL
License: GPL
*************************** 43. row ***************************
   Name: ndbinfo
 Status: DISABLED
   Type: STORAGE ENGINE
Library: NULL
License: GPL
*************************** 44. row ***************************
   Name: ndb_transid_mysql_connection_map
 Status: DISABLED
   Type: INFORMATION SCHEMA
Library: NULL
License: GPL
*************************** 45. row ***************************
   Name: ngram
 Status: ACTIVE
   Type: FTPARSER
Library: NULL
License: GPL
*************************** 46. row ***************************
   Name: mysqlx_cache_cleaner
 Status: ACTIVE
   Type: AUDIT
Library: NULL
License: GPL
*************************** 47. row ***************************
   Name: mysqlx
 Status: ACTIVE
   Type: DAEMON
Library: NULL
License: GPL
*************************** 48. row ***************************
   Name: mysql_native_password
 Status: ACTIVE
   Type: AUTHENTICATION
Library: NULL
License: GPL
48 rows in set (0.00 sec)
```
### 5.6.6.1 バージョントークン要素

* version_tokens という名前のサーバー側プラグインは、サーバーに関連付けられたバージョントークンのリストを保持し、ステートメント実行イベントの通知をサブスクライブします。 version_tokens プラグインは、audit plugin API を使用してクライアントからの受信ステートメントをモニターし、各クライアントセッション固有のバージョントークンリストをサーバーのバージョントークンリストと照合します。 一致がある場合、プラグインはステートメントを通過させ、サーバーはそのステートメントの処理を続行します。 それ以外の場合、プラグインはクライアントにエラーを返し、ステートメントは失敗します。

※アプリでバージョントークンを使って、アクセスするインスタンスを切り替えてるイメージ？

### 5.6.6.2 バージョントークンのインストールまたはアンインストール

インストール
```
mysql> INSTALL PLUGIN version_tokens SONAME 'version_token.so';
Query OK, 0 rows affected (0.01 sec)

mysql> CREATE FUNCTION version_tokens_set RETURNS STRING
    ->   SONAME 'version_token.so';
Query OK, 0 rows affected (0.01 sec)

mysql> CREATE FUNCTION version_tokens_show RETURNS STRING
    ->   SONAME 'version_token.so';
Query OK, 0 rows affected (0.06 sec)

mysql> CREATE FUNCTION version_tokens_edit RETURNS STRING
    ->   SONAME 'version_token.so';
Query OK, 0 rows affected (0.01 sec)

mysql> CREATE FUNCTION version_tokens_delete RETURNS STRING
    ->   SONAME 'version_token.so';
Query OK, 0 rows affected (0.02 sec)

mysql> CREATE FUNCTION version_tokens_lock_shared RETURNS INT
    ->   SONAME 'version_token.so';
Query OK, 0 rows affected (0.02 sec)

mysql> CREATE FUNCTION version_tokens_lock_exclusive RETURNS INT
    ->   SONAME 'version_token.so';
Query OK, 0 rows affected (0.01 sec)

mysql> CREATE FUNCTION version_tokens_unlock RETURNS INT
    ->   SONAME 'version_token.so';
Query OK, 0 rows affected (0.06 sec)
```

アンインストール
```
mysql> UNINSTALL PLUGIN version_tokens;
sion_tokens_set;
DROP FUNCTION version_tokens_showQuery OK, 0 rows affected, 1 warning (0.00 sec)

mysql> DROP FUNCTION version_tokens_set;
;
DROP FUNCTION version_tokens_edit;
DROP FUNCTION version_tokens_delete;
DROP FUNCTION version_tokens_lock_shared;
DROP FUNCTION version_tokens_lock_Query OK, 0 rows affected (0.02 sec)

mysql> DROP FUNCTION version_tokens_show;
exclusive;
DROP FUNCTION version_tokens_unlock;Query OK, 0 rows affected (0.01 sec)

mysql> DROP FUNCTION version_tokens_edit;
Query OK, 0 rows affected (0.01 sec)

mysql> DROP FUNCTION version_tokens_delete;
Query OK, 0 rows affected (0.01 sec)

mysql> DROP FUNCTION version_tokens_lock_shared;
Query OK, 0 rows affected (0.02 sec)

mysql> DROP FUNCTION version_tokens_lock_exclusive;
Query OK, 0 rows affected (0.01 sec)

mysql> DROP FUNCTION version_tokens_unlock;
Query OK, 0 rows affected (0.01 sec)
```

## 5.6.7 クローンプラグイン
クローンプラグインを使用すると、ローカルまたはリモートの MySQL サーバーインスタンスからデータをクローニングできます。

```
root@masami-L:/etc/mysql# vi mysql.cnf
```
```
[mysqld]
plugin-load-add=mysql_clone.so
```
```
root@masami-L:/etc/mysql# service mysql restart
```
すでに入っていた
```
mysql> INSTALL PLUGIN clone SONAME 'mysql_clone.so';
ERROR 1125 (HY000): Function 'clone' already exists
```

```

mysql> SELECT PLUGIN_NAME, PLUGIN_STATUS
    ->        FROM INFORMATION_SCHEMA.PLUGINS
    ->        WHERE PLUGIN_NAME = 'clone';
+-------------+---------------+
| PLUGIN_NAME | PLUGIN_STATUS |
+-------------+---------------+
| clone       | ACTIVE        |
+-------------+---------------+
1 row in set (0.05 sec)
```

### 5.6.7.2 ローカルでのデータのクローニング
ローカルでのデータのクローニングを示しています:
```
mysql> CLONE LOCAL DATA DIRECTORY = '/tmp/clone_testdb';
Query OK, 0 rows affected (0.46 sec)
```
クローンデータでmysqlを起動しようとするとソケットファイルがないと怒られた
```
root@masami-L:/etc/mysql# mysqld_safe --datadir=clone_dir
2023-11-30T11:42:24.569240Z mysqld_safe Logging to '/var/log/mysql/error.log'.
2023-11-30T11:42:24.571406Z mysqld_safe Directory '/var/run/mysqld' for UNIX socket file don't exists.
```

### 5.6.7.6 レプリケーション用のクローニング
受信者に転送されたバイナリログの位置を確認します
現在何も設定していない
```
mysql> SELECT BINLOG_FILE, BINLOG_POSITION FROM performance_schema.clone_status;
Empty set (0.05 sec)
```
、受信者に転送された gtid_executed GTID セットを確認
```
mysql> SELECT @@GLOBAL.GTID_EXECUTED;
+------------------------+
| @@GLOBAL.GTID_EXECUTED |
+------------------------+
|                        |
+------------------------+
1 row in set (0.00 sec)

```
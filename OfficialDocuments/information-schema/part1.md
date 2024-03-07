### 第 26 章 INFORMATION_SCHEMA テーブル

INFORMATION_SCHEMA では、データベースメタデータへのアクセスを実現し、データベースまたはテーブルの名前、カラムのデータ型、アクセス権限などの MySQL Server に関する情報を提供します

INFORMATION_SCHEMA から情報を取り出すステートメントの例を示します

```Sql
mysql> SELECT table_name, table_type, engine
    ->        FROM information_schema.tables
    ->        WHERE table_schema = 'test'
    ->        ORDER BY table_name;
+-------------------------+------------+------------+
| TABLE_NAME              | TABLE_TYPE | ENGINE     |
+-------------------------+------------+------------+
| account                 | BASE TABLE | InnoDB     |
| articles                | BASE TABLE | InnoDB     |
| child                   | BASE TABLE | InnoDB     |
| clients                 | BASE TABLE | InnoDB     |
| clients_lk              | BASE TABLE | InnoDB     |
| customers_1             | BASE TABLE | InnoDB     |
| customers_2             | BASE TABLE | InnoDB     |
| customers_3             | BASE TABLE | InnoDB     |
| departments             | BASE TABLE | InnoDB     |
| e                       | BASE TABLE | InnoDB     |
| e2                      | BASE TABLE | InnoDB     |
| e3                      | BASE TABLE | InnoDB     |
| employees               | BASE TABLE | InnoDB     |
| employees_by_lname      | BASE TABLE | InnoDB     |
| employees_sub           | BASE TABLE | InnoDB     |
| es                      | BASE TABLE | InnoDB     |
| es2                     | BASE TABLE | InnoDB     |
| es3                     | BASE TABLE | MyISAM     |
| fc1                     | BASE TABLE | InnoDB     |
| fc2                     | BASE TABLE | InnoDB     |
| h2                      | BASE TABLE | InnoDB     |
| k1                      | BASE TABLE | InnoDB     |
| lookup                  | BASE TABLE | MEMORY     |
| members                 | BASE TABLE | InnoDB     |
| parent                  | BASE TABLE | InnoDB     |
| quarterly_report_status | BASE TABLE | InnoDB     |
| r1                      | BASE TABLE | InnoDB     |
| rc1                     | BASE TABLE | InnoDB     |
| rc2                     | BASE TABLE | InnoDB     |
| rc3                     | BASE TABLE | InnoDB     |
| rc4                     | BASE TABLE | InnoDB     |
| rcx                     | BASE TABLE | InnoDB     |
| rx                      | BASE TABLE | InnoDB     |
| stores                  | BASE TABLE | InnoDB     |
| t                       | BASE TABLE | InnoDB     |
| t1                      | BASE TABLE | InnoDB     |
| t2                      | BASE TABLE | InnoDB     |
| t3                      | BASE TABLE | MEMORY     |
| t4                      | BASE TABLE | MyISAM     |
| test1                   | BASE TABLE | InnoDB     |
| test2                   | BASE TABLE | InnoDB     |
| test3                   | BASE TABLE | InnoDB     |
| test4                   | BASE TABLE | InnoDB     |
| th                      | BASE TABLE | InnoDB     |
| ti                      | BASE TABLE | InnoDB     |
| tk                      | BASE TABLE | InnoDB     |
| tndate                  | BASE TABLE | InnoDB     |
| total                   | BASE TABLE | MRG_MYISAM |
| tr                      | BASE TABLE | InnoDB     |
| trb1                    | BASE TABLE | InnoDB     |
| trb3                    | BASE TABLE | InnoDB     |
| ts                      | BASE TABLE | InnoDB     |
| ts1                     | BASE TABLE | InnoDB     |
| ts2                     | BASE TABLE | InnoDB     |
| ts3                     | BASE TABLE | InnoDB     |
| tt                      | BASE TABLE | InnoDB     |
| tu                      | BASE TABLE | InnoDB     |
| v                       | VIEW       | NULL       |
| v1                      | VIEW       | NULL       |
| v2                      | VIEW       | NULL       |
| v3                      | VIEW       | NULL       |
| vjoin                   | VIEW       | NULL       |
| vmat                    | VIEW       | NULL       |
| vup                     | VIEW       | NULL       |
+-------------------------+------------+------------+
64 rows in set (0.00 sec)
```

### 26.2 INFORMATION_SCHEMA ADMINISTRABLE_ROLE_AUTHORIZATIONS テーブル

他のユーザーまたはロールに付与できる現在のユーザーまたはロールに適用可能なロールに関する情報が表示されます

```Sql
mysql> table INFORMATION_SCHEMA.ADMINISTRABLE_ROLE_AUTHORIZATIONS;
Empty set (0.02 sec)
```

### 26.3 INFORMATION_SCHEMA APPLICABLE_ROLES テーブル

現在のユーザーに適用可能なロールに関する情報が表示されます

```Sql
mysql> table INFORMATION_SCHEMA.APPLICABLE_ROLES;
Empty set (0.00 sec)
```

### 26.4 INFORMATION_SCHEMA CHARACTER_SETS テーブル

CHARACTER_SETS テーブルは、利用できる文字セットに関する情報を提供します

```Sql
mysql> table INFORMATION_SCHEMA.CHARACTER_SETS;
+--------------------+----------------------+---------------------------------+--------+
| CHARACTER_SET_NAME | DEFAULT_COLLATE_NAME | DESCRIPTION                     | MAXLEN |
+--------------------+----------------------+---------------------------------+--------+
| big5               | big5_chinese_ci      | Big5 Traditional Chinese        |      2 |
| dec8               | dec8_swedish_ci      | DEC West European               |      1 |
| cp850              | cp850_general_ci     | DOS West European               |      1 |
| hp8                | hp8_english_ci       | HP West European                |      1 |
| koi8r              | koi8r_general_ci     | KOI8-R Relcom Russian           |      1 |
| latin1             | latin1_swedish_ci    | cp1252 West European            |      1 |
| latin2             | latin2_general_ci    | ISO 8859-2 Central European     |      1 |
| swe7               | swe7_swedish_ci      | 7bit Swedish                    |      1 |
| ascii              | ascii_general_ci     | US ASCII                        |      1 |
| ujis               | ujis_japanese_ci     | EUC-JP Japanese                 |      3 |
| sjis               | sjis_japanese_ci     | Shift-JIS Japanese              |      2 |
| hebrew             | hebrew_general_ci    | ISO 8859-8 Hebrew               |      1 |
| tis620             | tis620_thai_ci       | TIS620 Thai                     |      1 |
| euckr              | euckr_korean_ci      | EUC-KR Korean                   |      2 |
| koi8u              | koi8u_general_ci     | KOI8-U Ukrainian                |      1 |
| gb2312             | gb2312_chinese_ci    | GB2312 Simplified Chinese       |      2 |
| greek              | greek_general_ci     | ISO 8859-7 Greek                |      1 |
| cp1250             | cp1250_general_ci    | Windows Central European        |      1 |
| gbk                | gbk_chinese_ci       | GBK Simplified Chinese          |      2 |
| latin5             | latin5_turkish_ci    | ISO 8859-9 Turkish              |      1 |
| armscii8           | armscii8_general_ci  | ARMSCII-8 Armenian              |      1 |
| utf8mb3            | utf8mb3_general_ci   | UTF-8 Unicode                   |      3 |
| ucs2               | ucs2_general_ci      | UCS-2 Unicode                   |      2 |
| cp866              | cp866_general_ci     | DOS Russian                     |      1 |
| keybcs2            | keybcs2_general_ci   | DOS Kamenicky Czech-Slovak      |      1 |
| macce              | macce_general_ci     | Mac Central European            |      1 |
| macroman           | macroman_general_ci  | Mac West European               |      1 |
| cp852              | cp852_general_ci     | DOS Central European            |      1 |
| latin7             | latin7_general_ci    | ISO 8859-13 Baltic              |      1 |
| cp1251             | cp1251_general_ci    | Windows Cyrillic                |      1 |
| utf16              | utf16_general_ci     | UTF-16 Unicode                  |      4 |
| utf16le            | utf16le_general_ci   | UTF-16LE Unicode                |      4 |
| cp1256             | cp1256_general_ci    | Windows Arabic                  |      1 |
| cp1257             | cp1257_general_ci    | Windows Baltic                  |      1 |
| utf32              | utf32_general_ci     | UTF-32 Unicode                  |      4 |
| binary             | binary               | Binary pseudo charset           |      1 |
| geostd8            | geostd8_general_ci   | GEOSTD8 Georgian                |      1 |
| cp932              | cp932_japanese_ci    | SJIS for Windows Japanese       |      2 |
| eucjpms            | eucjpms_japanese_ci  | UJIS for Windows Japanese       |      3 |
| gb18030            | gb18030_chinese_ci   | China National Standard GB18030 |      4 |
| utf8mb4            | utf8mb4_0900_ai_ci   | UTF-8 Unicode                   |      4 |
+--------------------+----------------------+---------------------------------+--------+
41 rows in set (0.00 sec)
```

### 26.5 INFORMATION_SCHEMA CHECK_CONSTRAINTS テーブル

テーブルに定義されている CHECK 制約に関する情報を提供します

```Sql
mysql> table INFORMATION_SCHEMA.CHECK_CONSTRAINTS;
Empty set (0.00 sec)
```

### 26.7 INFORMATION_SCHEMA COLLATION_CHARACTER_SET_APPLICABILITY テーブル

どの文字セットがどの照合順序に適用されるかを示します

```sql
mysql> table INFORMATION_SCHEMA.COLLATION_CHARACTER_SET_APPLICABILITY limit 5;
+---------------------+--------------------+
| COLLATION_NAME      | CHARACTER_SET_NAME |
+---------------------+--------------------+
| armscii8_general_ci | armscii8           |
| armscii8_bin        | armscii8           |
| ascii_general_ci    | ascii              |
| ascii_bin           | ascii              |
| big5_chinese_ci     | big5               |
+---------------------+--------------------+
5 rows in set (0.00 sec)
```

### 26.8 INFORMATION_SCHEMA COLUMNS テーブル

テーブル内のカラムに関する情報を提供します

```Sql
mysql> table INFORMATION_SCHEMA.COLUMNS limit 3\G
*************************** 1. row ***************************
           TABLE_CATALOG: def
            TABLE_SCHEMA: mysql
              TABLE_NAME: innodb_table_stats
             COLUMN_NAME: database_name
        ORDINAL_POSITION: 1
          COLUMN_DEFAULT: NULL
             IS_NULLABLE: NO
               DATA_TYPE: varchar
CHARACTER_MAXIMUM_LENGTH: 64
  CHARACTER_OCTET_LENGTH: 192
       NUMERIC_PRECISION: NULL
           NUMERIC_SCALE: NULL
      DATETIME_PRECISION: NULL
      CHARACTER_SET_NAME: utf8mb3
          COLLATION_NAME: utf8mb3_bin
             COLUMN_TYPE: varchar(64)
              COLUMN_KEY: PRI
                   EXTRA:
              PRIVILEGES: select,insert,update,references
          COLUMN_COMMENT:
   GENERATION_EXPRESSION:
                  SRS_ID: NULL
*************************** 2. row ***************************
           TABLE_CATALOG: def
            TABLE_SCHEMA: mysql
              TABLE_NAME: innodb_table_stats
             COLUMN_NAME: table_name
        ORDINAL_POSITION: 2
          COLUMN_DEFAULT: NULL
             IS_NULLABLE: NO
               DATA_TYPE: varchar
CHARACTER_MAXIMUM_LENGTH: 199
  CHARACTER_OCTET_LENGTH: 597
       NUMERIC_PRECISION: NULL
           NUMERIC_SCALE: NULL
      DATETIME_PRECISION: NULL
      CHARACTER_SET_NAME: utf8mb3
          COLLATION_NAME: utf8mb3_bin
             COLUMN_TYPE: varchar(199)
              COLUMN_KEY: PRI
                   EXTRA:
              PRIVILEGES: select,insert,update,references
          COLUMN_COMMENT:
   GENERATION_EXPRESSION:
                  SRS_ID: NULL
*************************** 3. row ***************************
           TABLE_CATALOG: def
            TABLE_SCHEMA: mysql
              TABLE_NAME: innodb_table_stats
             COLUMN_NAME: last_update
        ORDINAL_POSITION: 3
          COLUMN_DEFAULT: CURRENT_TIMESTAMP
             IS_NULLABLE: NO
               DATA_TYPE: timestamp
CHARACTER_MAXIMUM_LENGTH: NULL
  CHARACTER_OCTET_LENGTH: NULL
       NUMERIC_PRECISION: NULL
           NUMERIC_SCALE: NULL
      DATETIME_PRECISION: 0
      CHARACTER_SET_NAME: NULL
          COLLATION_NAME: NULL
             COLUMN_TYPE: timestamp
              COLUMN_KEY:
                   EXTRA: DEFAULT_GENERATED on update CURRENT_TIMESTAMP
              PRIVILEGES: select,insert,update,references
          COLUMN_COMMENT:
   GENERATION_EXPRESSION:
                  SRS_ID: NULL
3 rows in set (0.00 sec)
```

### 26.9 INFORMATION_SCHEMA COLUMNS_EXTENSIONS テーブル

プライマリストレージエンジンおよびセカンダリストレージエンジンに定義されているカラム属性に関する情報を提供します

```sql
mysql> table INFORMATION_SCHEMA.COLUMNS_EXTENSIONS limit 3\G
*************************** 1. row ***************************
             TABLE_CATALOG: def
              TABLE_SCHEMA: mysql
                TABLE_NAME: innodb_table_stats
               COLUMN_NAME: database_name
          ENGINE_ATTRIBUTE: NULL
SECONDARY_ENGINE_ATTRIBUTE: NULL
*************************** 2. row ***************************
             TABLE_CATALOG: def
              TABLE_SCHEMA: mysql
                TABLE_NAME: innodb_table_stats
               COLUMN_NAME: table_name
          ENGINE_ATTRIBUTE: NULL
SECONDARY_ENGINE_ATTRIBUTE: NULL
*************************** 3. row ***************************
             TABLE_CATALOG: def
              TABLE_SCHEMA: mysql
                TABLE_NAME: innodb_table_stats
               COLUMN_NAME: last_update
          ENGINE_ATTRIBUTE: NULL
SECONDARY_ENGINE_ATTRIBUTE: NULL
3 rows in set (0.00 sec)

```

### 26.10 INFORMATION_SCHEMA COLUMN_PRIVILEGES テーブル

カラムの権限に関する情報を提供します。 mysql.columns_priv システムテーブルから値を取得します

```Sql
mysql> table INFORMATION_SCHEMA.COLUMN_PRIVILEGES;
Empty set (0.00 sec)
```

### 26.11 INFORMATION_SCHEMA COLUMN_STATISTICS テーブル

、カラム値のヒストグラム統計にアクセスできます

```Sql
mysql> table INFORMATION_SCHEMA.COLUMN_STATISTICS;
Empty set (0.00 sec)
```

### 26.12 INFORMATION_SCHEMA ENABLED_ROLES テーブル

現在のセッション内で有効になっているロールに関する情報が表示されます

```sql
mysql> table INFORMATION_SCHEMA.ENABLED_ROLES;
Empty set (0.01 sec)
```

### 26.13 INFORMATION_SCHEMA ENGINES テーブル

ストレージエンジンに関する情報を提供します

```sql
mysql> table INFORMATION_SCHEMA.ENGINES;
+--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
| ENGINE             | SUPPORT | COMMENT                                                        | TRANSACTIONS | XA   | SAVEPOINTS |
+--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
| MEMORY             | YES     | Hash based, stored in memory, useful for temporary tables      | NO           | NO   | NO         |
| MRG_MYISAM         | YES     | Collection of identical MyISAM tables                          | NO           | NO   | NO         |
| CSV                | YES     | CSV storage engine                                             | NO           | NO   | NO         |
| FEDERATED          | NO      | Federated MySQL storage engine                                 | NULL         | NULL | NULL       |
| PERFORMANCE_SCHEMA | YES     | Performance Schema                                             | NO           | NO   | NO         |
| MyISAM             | YES     | MyISAM storage engine                                          | NO           | NO   | NO         |
| InnoDB             | DEFAULT | Supports transactions, row-level locking, and foreign keys     | YES          | YES  | YES        |
| ndbinfo            | NO      | MySQL Cluster system information storage engine                | NULL         | NULL | NULL       |
| BLACKHOLE          | YES     | /dev/null storage engine (anything you write to it disappears) | NO           | NO   | NO         |
| ARCHIVE            | YES     | Archive storage engine                                         | NO           | NO   | NO         |
| ndbcluster         | NO      | Clustered, fault-tolerant tables                               | NULL         | NULL | NULL       |
+--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
11 rows in set (0.00 sec)
```

### 26.14 INFORMATION_SCHEMA EVENTS テーブル

イベントマネージャイベントに関する情報が表示されます

```Sql
mysql> DELIMITER |
mysql>
mysql> CREATE EVENT e_daily
    ->     ON SCHEDULE
    ->       EVERY 1 DAY
    ->     COMMENT 'Saves total number of sessions then clears the table each day'
    ->     DO
    ->       BEGIN
    ->         INSERT INTO site_activity.totals (time, total)
    ->           SELECT CURRENT_TIMESTAMP, COUNT(*)
    ->             FROM site_activity.sessions;
    ->         DELETE FROM site_activity.sessions;
    ->       END |
Query OK, 0 rows affected (0.01 sec)

mysql>
mysql> DELIMITER ;

mysql> table INFORMATION_SCHEMA.EVENTS\G
*************************** 1. row ***************************
       EVENT_CATALOG: def
        EVENT_SCHEMA: test
          EVENT_NAME: e_daily
             DEFINER: root@localhost
           TIME_ZONE: SYSTEM
          EVENT_BODY: SQL
    EVENT_DEFINITION: BEGIN
        INSERT INTO site_activity.totals (time, total)
          SELECT CURRENT_TIMESTAMP, COUNT(*)
            FROM site_activity.sessions;
        DELETE FROM site_activity.sessions;
      END
          EVENT_TYPE: RECURRING
          EXECUTE_AT: NULL
      INTERVAL_VALUE: 1
      INTERVAL_FIELD: DAY
            SQL_MODE: ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION
              STARTS: 2024-01-04 16:19:55
                ENDS: NULL
              STATUS: ENABLED
       ON_COMPLETION: NOT PRESERVE
             CREATED: 2024-01-04 16:19:55
        LAST_ALTERED: 2024-01-04 16:19:56
       LAST_EXECUTED: 2024-01-04 16:19:55
       EVENT_COMMENT: Saves total number of sessions then clears the table each day
          ORIGINATOR: 1
CHARACTER_SET_CLIENT: cp932
COLLATION_CONNECTION: cp932_japanese_ci
  DATABASE_COLLATION: utf8mb4_0900_ai_ci
1 row in set (0.00 sec)
```

### 26.15 INFORMATION_SCHEMA FILES テーブル

MySQL テーブルスペースデータが格納されているファイルに関する情報が表示されます

```Sql
mysql> table INFORMATION_SCHEMA.FILES limit 3\G
*************************** 1. row ***************************
             FILE_ID: 4294967294
           FILE_NAME: ./mysql.ibd
           FILE_TYPE: TABLESPACE
     TABLESPACE_NAME: mysql
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
       TOTAL_EXTENTS: 26
         EXTENT_SIZE: 1048576
        INITIAL_SIZE: 0
        MAXIMUM_SIZE: NULL
     AUTOEXTEND_SIZE: 1048576
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
           DATA_FREE: 4194304
         CREATE_TIME: NULL
         UPDATE_TIME: NULL
          CHECK_TIME: NULL
            CHECKSUM: NULL
              STATUS: NORMAL
               EXTRA: NULL
*************************** 2. row ***************************
             FILE_ID: 0
           FILE_NAME: ./ibdata1
           FILE_TYPE: TABLESPACE
     TABLESPACE_NAME: innodb_system
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
*************************** 3. row ***************************
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
3 rows in set (0.00 sec)
```

### 26.16 INFORMATION_SCHEMA KEY_COLUMN_USAGE テーブル

どのキーカラムに制約があるかを説明します

```Sql
mysql> table INFORMATION_SCHEMA.KEY_COLUMN_USAGE limit 2\G
*************************** 1. row ***************************
           CONSTRAINT_CATALOG: def
            CONSTRAINT_SCHEMA: mysql
              CONSTRAINT_NAME: PRIMARY
                TABLE_CATALOG: def
                 TABLE_SCHEMA: mysql
                   TABLE_NAME: innodb_table_stats
                  COLUMN_NAME: database_name
             ORDINAL_POSITION: 1
POSITION_IN_UNIQUE_CONSTRAINT: NULL
      REFERENCED_TABLE_SCHEMA: NULL
        REFERENCED_TABLE_NAME: NULL
       REFERENCED_COLUMN_NAME: NULL
*************************** 2. row ***************************
           CONSTRAINT_CATALOG: def
            CONSTRAINT_SCHEMA: mysql
              CONSTRAINT_NAME: PRIMARY
                TABLE_CATALOG: def
                 TABLE_SCHEMA: mysql
                   TABLE_NAME: innodb_table_stats
                  COLUMN_NAME: table_name
             ORDINAL_POSITION: 2
POSITION_IN_UNIQUE_CONSTRAINT: NULL
      REFERENCED_TABLE_SCHEMA: NULL
        REFERENCED_TABLE_NAME: NULL
       REFERENCED_COLUMN_NAME: NULL
2 rows in set (0.00 sec)
```

### 26.18 INFORMATION_SCHEMA KEYWORDS テーブル

ySQL で考慮されるキーワードがリストされ、各キーワードについて予約されているかどうかが示されます

```Sql
mysql> table INFORMATION_SCHEMA.KEYWORDS limit 5;
+------------+----------+
| WORD       | RESERVED |
+------------+----------+
| ACCESSIBLE |        1 |
| ACCOUNT    |        0 |
| ACTION     |        0 |
| ACTIVE     |        0 |
| ADD        |        1 |
+------------+----------+
5 rows in set (0.00 sec)
```

### 26.19 INFORMATION_SCHEMA OPTIMIZER_TRACE テーブル

トレースされたステートメントのオプティマイザトレース機能によって生成される情報を提供します

```Sql
mysql> table INFORMATION_SCHEMA.OPTIMIZER_TRACE;
Empty set (0.01 sec)
```

### 26.20 INFORMATION_SCHEMA PARAMETERS テーブル

ストアドルーチン (ストアドプロシージャーとストアドファンクション) のパラメータ、およびストアドファンクションの戻り値に関する情報を提供します

```sql
mysql> table INFORMATION_SCHEMA.PARAMETERS limit 2\G
*************************** 1. row ***************************
        SPECIFIC_CATALOG: def
         SPECIFIC_SCHEMA: sys
           SPECIFIC_NAME: ps_thread_stack
        ORDINAL_POSITION: 0
          PARAMETER_MODE: NULL
          PARAMETER_NAME: NULL
               DATA_TYPE: longtext
CHARACTER_MAXIMUM_LENGTH: 4294967295
  CHARACTER_OCTET_LENGTH: 4294967295
       NUMERIC_PRECISION: NULL
           NUMERIC_SCALE: NULL
      DATETIME_PRECISION: NULL
      CHARACTER_SET_NAME: latin1
          COLLATION_NAME: latin1_swedish_ci
          DTD_IDENTIFIER: longtext
            ROUTINE_TYPE: FUNCTION
*************************** 2. row ***************************
        SPECIFIC_CATALOG: def
         SPECIFIC_SCHEMA: sakila
           SPECIFIC_NAME: get_customer_balance
        ORDINAL_POSITION: 2
          PARAMETER_MODE: IN
          PARAMETER_NAME: p_effective_date
               DATA_TYPE: datetime
CHARACTER_MAXIMUM_LENGTH: NULL
  CHARACTER_OCTET_LENGTH: NULL
       NUMERIC_PRECISION: NULL
           NUMERIC_SCALE: NULL
      DATETIME_PRECISION: 0
      CHARACTER_SET_NAME: NULL
          COLLATION_NAME: NULL
          DTD_IDENTIFIER: datetime
            ROUTINE_TYPE: FUNCTION
2 rows in set (0.00 sec)
```

### 26.21 INFORMATION_SCHEMA PARTITIONS テーブル

テーブルパーティションに関する情報を提供します

```Sql
mysql> table INFORMATION_SCHEMA.PARTITIONS limit 2\G
*************************** 1. row ***************************
                TABLE_CATALOG: def
                 TABLE_SCHEMA: mysql
                   TABLE_NAME: innodb_table_stats
               PARTITION_NAME: NULL
            SUBPARTITION_NAME: NULL
   PARTITION_ORDINAL_POSITION: NULL
SUBPARTITION_ORDINAL_POSITION: NULL
             PARTITION_METHOD: NULL
          SUBPARTITION_METHOD: NULL
         PARTITION_EXPRESSION: NULL
      SUBPARTITION_EXPRESSION: NULL
        PARTITION_DESCRIPTION: NULL
                   TABLE_ROWS: 191
               AVG_ROW_LENGTH: 85
                  DATA_LENGTH: 16384
              MAX_DATA_LENGTH: 0
                 INDEX_LENGTH: 0
                    DATA_FREE: 4194304
                  CREATE_TIME: 2023-12-31 22:57:16
                  UPDATE_TIME: NULL
                   CHECK_TIME: NULL
                     CHECKSUM: NULL
            PARTITION_COMMENT:
                    NODEGROUP:
              TABLESPACE_NAME: NULL
*************************** 2. row ***************************
                TABLE_CATALOG: def
                 TABLE_SCHEMA: mysql
                   TABLE_NAME: innodb_index_stats
               PARTITION_NAME: NULL
            SUBPARTITION_NAME: NULL
   PARTITION_ORDINAL_POSITION: NULL
SUBPARTITION_ORDINAL_POSITION: NULL
             PARTITION_METHOD: NULL
          SUBPARTITION_METHOD: NULL
         PARTITION_EXPRESSION: NULL
      SUBPARTITION_EXPRESSION: NULL
        PARTITION_DESCRIPTION: NULL
                   TABLE_ROWS: 725
               AVG_ROW_LENGTH: 180
                  DATA_LENGTH: 131072
              MAX_DATA_LENGTH: 0
                 INDEX_LENGTH: 0
                    DATA_FREE: 4194304
                  CREATE_TIME: 2023-12-31 22:57:16
                  UPDATE_TIME: NULL
                   CHECK_TIME: NULL
                     CHECKSUM: NULL
            PARTITION_COMMENT:
                    NODEGROUP:
              TABLESPACE_NAME: NULL
2 rows in set (0.01 sec)

mysql> CREATE TABLE tp (
    ->     c1 INT,
    ->     c2 INT,
    ->     c3 VARCHAR(25)
    -> )
    -> PARTITION BY HASH(c1 + c2)
    -> PARTITIONS 4;
Query OK, 0 rows affected (0.07 sec)

mysql> SELECT DISTINCT PARTITION_EXPRESSION
    ->        FROM INFORMATION_SCHEMA.PARTITIONS
    ->        WHERE TABLE_NAME='tp' AND TABLE_SCHEMA='test';
+----------------------+
| PARTITION_EXPRESSION |
+----------------------+
| (`c1` + `c2`)        |
+----------------------+
1 row in set (0.00 sec)
```

### 26.22 INFORMATION_SCHEMA PLUGINS テーブル

サーバーのプラグインに関する情報を提供します

```Sql
mysql> table INFORMATION_SCHEMA.PLUGINS limit 2\G
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
2 rows in set (0.00 sec)
```

### 26.23 INFORMATION_SCHEMA PROCESSLIST テーブル

サーバー内で実行されているスレッドのセットによって現在実行されている操作が示されます

```Sql
mysql> table INFORMATION_SCHEMA.PROCESSLIST;
+----+-----------------+-----------+------+---------+------+-----------------------------+--------------------------------------+
| ID | USER            | HOST      | DB   | COMMAND | TIME | STATE                       | INFO
        |
+----+-----------------+-----------+------+---------+------+-----------------------------+--------------------------------------+
|  8 | root            | localhost | test | Query   |    0 | executing                   | table INFORMATION_SCHEMA.PROCESSLIST |
|  5 | event_scheduler | localhost | NULL | Daemon  | 1000 | Waiting for next activation | NULL
        |
+----+-----------------+-----------+------+---------+------+-----------------------------+--------------------------------------+
2 rows in set, 1 warning (0.00 sec)
```

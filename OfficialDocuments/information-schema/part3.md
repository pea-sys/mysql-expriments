### 26.51 INFORMATION_SCHEMA InnoDB テーブル

### 26.51.1 INFORMATION_SCHEMA INNODB_BUFFER_PAGE テーブル

InnoDB buffer pool の各 page に関する情報が表示されます

```Sql
mysql> table INFORMATION_SCHEMA.INNODB_BUFFER_PAGE limit 3\G
*************************** 1. row ***************************
            POOL_ID: 0
           BLOCK_ID: 0
              SPACE: 0
        PAGE_NUMBER: 7
          PAGE_TYPE: SYSTEM
         FLUSH_TYPE: 1
          FIX_COUNT: 0
          IS_HASHED: NO
NEWEST_MODIFICATION: 58301906
OLDEST_MODIFICATION: 0
        ACCESS_TIME: 10474
         TABLE_NAME: NULL
         INDEX_NAME: NULL
     NUMBER_RECORDS: 0
          DATA_SIZE: 0
    COMPRESSED_SIZE: 0
         PAGE_STATE: FILE_PAGE
             IO_FIX: IO_NONE
             IS_OLD: YES
    FREE_PAGE_CLOCK: 0
           IS_STALE: NO
*************************** 2. row ***************************
            POOL_ID: 0
           BLOCK_ID: 1
              SPACE: 0
        PAGE_NUMBER: 3
          PAGE_TYPE: SYSTEM
         FLUSH_TYPE: 0
          FIX_COUNT: 0
          IS_HASHED: NO
NEWEST_MODIFICATION: 0
OLDEST_MODIFICATION: 0
        ACCESS_TIME: 10475
         TABLE_NAME: NULL
         INDEX_NAME: NULL
     NUMBER_RECORDS: 0
          DATA_SIZE: 0
    COMPRESSED_SIZE: 0
         PAGE_STATE: FILE_PAGE
             IO_FIX: IO_NONE
             IS_OLD: YES
    FREE_PAGE_CLOCK: 0
           IS_STALE: NO
*************************** 3. row ***************************
            POOL_ID: 0
           BLOCK_ID: 2
              SPACE: 0
        PAGE_NUMBER: 2
          PAGE_TYPE: INODE
         FLUSH_TYPE: 0
          FIX_COUNT: 0
          IS_HASHED: NO
NEWEST_MODIFICATION: 0
OLDEST_MODIFICATION: 0
        ACCESS_TIME: 10475
         TABLE_NAME: NULL
         INDEX_NAME: NULL
     NUMBER_RECORDS: 0
          DATA_SIZE: 0
    COMPRESSED_SIZE: 0
         PAGE_STATE: FILE_PAGE
             IO_FIX: IO_NONE
             IS_OLD: YES
    FREE_PAGE_CLOCK: 0
           IS_STALE: NO
3 rows in set (0.02 sec)
```

### 26.51.2 INFORMATION_SCHEMA INNODB_BUFFER_PAGE_LRU テーブル

InnoDB buffer pool のページに関する情報、特に LRU リストでのページの順序付け方法が示されます

```Sql
mysql> table INFORMATION_SCHEMA.INNODB_BUFFER_PAGE_LRU limit 2\G
*************************** 1. row ***************************
            POOL_ID: 0
       LRU_POSITION: 0
              SPACE: 0
        PAGE_NUMBER: 7
          PAGE_TYPE: SYSTEM
         FLUSH_TYPE: 1
          FIX_COUNT: 0
          IS_HASHED: NO
NEWEST_MODIFICATION: 58301906
OLDEST_MODIFICATION: 0
        ACCESS_TIME: 10474
         TABLE_NAME: NULL
         INDEX_NAME: NULL
     NUMBER_RECORDS: 0
          DATA_SIZE: 0
    COMPRESSED_SIZE: 0
         COMPRESSED: NO
             IO_FIX: IO_NONE
             IS_OLD: YES
    FREE_PAGE_CLOCK: 0
*************************** 2. row ***************************
            POOL_ID: 0
       LRU_POSITION: 1
              SPACE: 0
        PAGE_NUMBER: 3
          PAGE_TYPE: SYSTEM
         FLUSH_TYPE: 0
          FIX_COUNT: 0
          IS_HASHED: NO
NEWEST_MODIFICATION: 0
OLDEST_MODIFICATION: 0
        ACCESS_TIME: 10475
         TABLE_NAME: NULL
         INDEX_NAME: NULL
     NUMBER_RECORDS: 0
          DATA_SIZE: 0
    COMPRESSED_SIZE: 0
         COMPRESSED: NO
             IO_FIX: IO_NONE
             IS_OLD: YES
    FREE_PAGE_CLOCK: 0
2 rows in set (0.01 sec)
```

### 26.51.3 INFORMATION_SCHEMA INNODB_BUFFER_POOL_STATS テーブル

SHOW ENGINE INNODB STATUS 出力で提供されるものと同じバッファープール情報の多くを提供します

```Sql
mysql> table INFORMATION_SCHEMA.INNODB_BUFFER_POOL_STATS\G
*************************** 1. row ***************************
                         POOL_ID: 0
                       POOL_SIZE: 8191
                    FREE_BUFFERS: 6128
                  DATABASE_PAGES: 2045
              OLD_DATABASE_PAGES: 742
         MODIFIED_DATABASE_PAGES: 0
              PENDING_DECOMPRESS: 0
                   PENDING_READS: 0
               PENDING_FLUSH_LRU: 0
              PENDING_FLUSH_LIST: 0
                PAGES_MADE_YOUNG: 0
            PAGES_NOT_MADE_YOUNG: 0
           PAGES_MADE_YOUNG_RATE: 0
       PAGES_MADE_NOT_YOUNG_RATE: 0
               NUMBER_PAGES_READ: 1791
            NUMBER_PAGES_CREATED: 254
            NUMBER_PAGES_WRITTEN: 817
                 PAGES_READ_RATE: 0
               PAGES_CREATE_RATE: 0
              PAGES_WRITTEN_RATE: 0
                NUMBER_PAGES_GET: 107245
                        HIT_RATE: 0
    YOUNG_MAKE_PER_THOUSAND_GETS: 0
NOT_YOUNG_MAKE_PER_THOUSAND_GETS: 0
         NUMBER_PAGES_READ_AHEAD: 251
       NUMBER_READ_AHEAD_EVICTED: 0
                 READ_AHEAD_RATE: 0
         READ_AHEAD_EVICTED_RATE: 0
                    LRU_IO_TOTAL: 0
                  LRU_IO_CURRENT: 0
                UNCOMPRESS_TOTAL: 0
              UNCOMPRESS_CURRENT: 0
1 row in set (0.00 sec)
```

### 26.51.4 INFORMATION_SCHEMA INNODB_CACHED_INDEXES テーブル

インデックスごとに InnoDB バッファプールにキャッシュされたインデックスページの数がレポートされます

```Sql

mysql> table INFORMATION_SCHEMA.INNODB_CACHED_INDEXES limit 5;
+------------+----------+----------------+
| SPACE_ID   | INDEX_ID | N_CACHED_PAGES |
+------------+----------+----------------+
| 4294967294 |        1 |              1 |
| 4294967294 |        2 |              1 |
| 4294967294 |        3 |              1 |
| 4294967294 |        4 |              7 |
| 4294967294 |        5 |              1 |
+------------+----------+----------------+
5 rows in set (0.00 sec)
```

### 26.51.5 INFORMATION_SCHEMA INNODB_CMP および INNODB_CMP_RESET テーブル

compressed InnoDB テーブルに関連する操作のステータス情報を提供します

```sql
mysql> table INFORMATION_SCHEMA.INNODB_CMP;
+-----------+--------------+-----------------+---------------+----------------+-----------------+
| page_size | compress_ops | compress_ops_ok | compress_time | uncompress_ops | uncompress_time |
+-----------+--------------+-----------------+---------------+----------------+-----------------+
|      1024 |            0 |               0 |             0 |              0 |               0 |
|      2048 |            0 |               0 |             0 |              0 |               0 |
|      4096 |            0 |               0 |             0 |              0 |               0 |
|      8192 |            0 |               0 |             0 |              0 |               0 |
|     16384 |            0 |               0 |             0 |              0 |               0 |
+-----------+--------------+-----------------+---------------+----------------+-----------------+
5 rows in set (0.00 sec)

mysql> table INFORMATION_SCHEMA.INNODB_CMP_RESET;
+-----------+--------------+-----------------+---------------+----------------+-----------------+
| page_size | compress_ops | compress_ops_ok | compress_time | uncompress_ops | uncompress_time |
+-----------+--------------+-----------------+---------------+----------------+-----------------+
|      1024 |            0 |               0 |             0 |              0 |               0 |
|      2048 |            0 |               0 |             0 |              0 |               0 |
|      4096 |            0 |               0 |             0 |              0 |               0 |
|      8192 |            0 |               0 |             0 |              0 |               0 |
|     16384 |            0 |               0 |             0 |              0 |               0 |
+-----------+--------------+-----------------+---------------+----------------+-----------------+
5 rows in set (0.00 sec)
```

### 26.51.6 INFORMATION_SCHEMA INNODB_CMPMEM および INNODB_CMPMEM_RESET テーブル

InnoDB buffer pool 内の圧縮された pages のステータス情報を提供します

```sql
mysql> table INFORMATION_SCHEMA.INNODB_CMPMEM;
+-----------+----------------------+------------+------------+----------------+-----------------+
| page_size | buffer_pool_instance | pages_used | pages_free | relocation_ops | relocation_time |
+-----------+----------------------+------------+------------+----------------+-----------------+
|      1024 |                    0 |          0 |          0 |              0 |               0 |
|      2048 |                    0 |          0 |          0 |              0 |               0 |
|      4096 |                    0 |          0 |          0 |              0 |               0 |
|      8192 |                    0 |          0 |          0 |              0 |               0 |
|     16384 |                    0 |          0 |          0 |              0 |               0 |
+-----------+----------------------+------------+------------+----------------+-----------------+
5 rows in set (0.00 sec)

mysql> table INFORMATION_SCHEMA.INNODB_CMPMEM_RESET;
+-----------+----------------------+------------+------------+----------------+-----------------+
| page_size | buffer_pool_instance | pages_used | pages_free | relocation_ops | relocation_time |
+-----------+----------------------+------------+------------+----------------+-----------------+
|      1024 |                    0 |          0 |          0 |              0 |               0 |
|      2048 |                    0 |          0 |          0 |              0 |               0 |
|      4096 |                    0 |          0 |          0 |              0 |               0 |
|      8192 |                    0 |          0 |          0 |              0 |               0 |
|     16384 |                    0 |          0 |          0 |              0 |               0 |
+-----------+----------------------+------------+------------+----------------+-----------------+
5 rows in set (0.00 sec)
```

### 26.51.7 INFORMATION_SCHEMA INNODB_CMP_PER_INDEX および INNODB_CMP_PER_INDEX_RESET テーブル

compressed InnoDB のテーブルおよびインデックスに関連する操作のステータス情報が提供され、データベース、テーブルおよびインデックスの組合せごとに個別の統計が提供されるため、特定のテーブルの圧縮のパフォーマンスおよび有用性を評価できます。

```sql
mysql> table  INFORMATION_SCHEMA.INNODB_CMP_PER_INDEX;
Empty set (0.00 sec)

mysql> table  INFORMATION_SCHEMA.INNODB_CMP_PER_INDEX_RESET;
Empty set (0.00 sec)
```

### 26.51.8 INFORMATION_SCHEMA INNODB_COLUMNS テーブル

InnoDB テーブルのカラムに関するメタデータを提供します

```Sql
mysql> table INFORMATION_SCHEMA.INNODB_COLUMNS limit 3\G
*************************** 1. row ***************************
     TABLE_ID: 1025
         NAME: Host
          POS: 0
        MTYPE: 13
       PRTYPE: 721406
          LEN: 255
  HAS_DEFAULT: 0
DEFAULT_VALUE: NULL
*************************** 2. row ***************************
     TABLE_ID: 1025
         NAME: Db
          POS: 1
        MTYPE: 13
       PRTYPE: 5439998
          LEN: 192
  HAS_DEFAULT: 0
DEFAULT_VALUE: NULL
*************************** 3. row ***************************
     TABLE_ID: 1025
         NAME: User
          POS: 2
        MTYPE: 13
       PRTYPE: 5439998
          LEN: 96
  HAS_DEFAULT: 0
DEFAULT_VALUE: NULL
3 rows in set (0.01 sec)
```

### 26.51.9 INFORMATION_SCHEMA INNODB_DATAFILES テーブル

InnoDB file-per-table および一般テーブルスペースのデータファイルパス情報が表示されます

```Sql
mysql> table INFORMATION_SCHEMA.INNODB_DATAFILES limit 5;
+------------------------+----------------------+
| SPACE                  | PATH                 |
+------------------------+----------------------+
| 0x30                   | ibdata1              |
| 0x34323934393637323739 | .\undo_001           |
| 0x34323934393637323738 | .\undo_002           |
| 0x31                   | .\sys\sys_config.ibd |
| 0x32                   | .\sakila\actor.ibd   |
+------------------------+----------------------+
5 rows in set (0.00 sec)
```

### 26.51.10 INFORMATION_SCHEMA INNODB_FIELDS テーブル

InnoDB インデックスのキーカラム (フィールド) に関するメタデータを提供します

```sql
mysql> table INFORMATION_SCHEMA.INNODB_FIELDS limit 5;
+--------------------+---------------+-----+
| INDEX_ID           | NAME          | POS |
+--------------------+---------------+-----+
| 0x33               | database_name |   0 |
| 0x33               | table_name    |   1 |
| 0x34               | database_name |   0 |
| 0x34               | table_name    |   1 |
| 0x34               | index_name    |   2 |
+--------------------+---------------+-----+
5 rows in set (0.00 sec)
```

### 26.51.11 INFORMATION_SCHEMA INNODB_FOREIGN テーブル

InnoDB foreign keys に関するメタデータを提供します

```Sql
mysql> table INFORMATION_SCHEMA.INNODB_FOREIGN limit 5;
+----------------------------+-----------------+-----------------+--------+------+
| ID                         | FOR_NAME        | REF_NAME        | N_COLS | TYPE |
+----------------------------+-----------------+-----------------+--------+------+
| sakila/fk_address_city     | sakila/address  | sakila/city     |      1 |    4 |
| sakila/fk_city_country     | sakila/city     | sakila/country  |      1 |    4 |
| sakila/fk_customer_address | sakila/customer | sakila/address  |      1 |    4 |
| sakila/fk_customer_store   | sakila/customer | sakila/store    |      1 |    4 |
| sakila/fk_film_language    | sakila/film     | sakila/language |      1 |    4 |
+----------------------------+-----------------+-----------------+--------+------+
5 rows in set (0.00 sec)
```

### 26.51.12 INFORMATION_SCHEMA INNODB_FOREIGN_COLS テーブル

InnoDB 外部キーカラムに関するステータス情報が表示されます

```Sql
mysql> table INFORMATION_SCHEMA.INNODB_FOREIGN_COLS limit 5;
+----------------------------+--------------+--------------+-----+
| ID                         | FOR_COL_NAME | REF_COL_NAME | POS |
+----------------------------+--------------+--------------+-----+
| sakila/fk_address_city     | city_id      | city_id      |   1 |
| sakila/fk_city_country     | country_id   | country_id   |   1 |
| sakila/fk_customer_address | address_id   | address_id   |   1 |
| sakila/fk_customer_store   | store_id     | store_id     |   1 |
| sakila/fk_film_language    | language_id  | language_id  |   1 |
+----------------------------+--------------+--------------+-----+
5 rows in set (0.00 sec)
```

### 26.51.13 INFORMATION_SCHEMA INNODB_FT_BEING_DELETED テーブル

INNODB_FT_DELETED テーブルのスナップショットを提供します

```Sql
mysql> table  INFORMATION_SCHEMA.INNODB_FT_BEING_DELETED;
Empty set (0.00 sec)
```

### 26.51.15 INFORMATION_SCHEMA INNODB_FT_DEFAULT_STOPWORD テーブル

InnoDB テーブルに FULLTEXT インデックスを作成するときにデフォルトで使用される stopwords のリストが格納されます

```Sql
mysql> table  INFORMATION_SCHEMA.INNODB_FT_DEFAULT_STOPWORD limit 5;
+-------+
| value |
+-------+
| a     |
| about |
| an    |
| are   |
| as    |
+-------+
5 rows in set (0.00 sec)
```

### 26.51.16 INFORMATION_SCHEMA INNODB_FT_DELETED テーブル

InnoDB テーブルの FULLTEXT インデックスから削除された行が格納されます

```sql
mysql> table INFORMATION_SCHEMA.INNODB_FT_DELETED;
Empty set (0.00 sec)
```

### 26.51.17 INFORMATION_SCHEMA INNODB_FT_INDEX_CACHE テーブル

FULLTEXT インデックスに新しく挿入された行に関するトークン情報が表示されます

```Sql
mysql> CREATE TABLE articles (
    ->          id INT UNSIGNED AUTO_INCREMENT NOT NULL PRIMARY KEY,
    ->          title VARCHAR(200),
    ->          body TEXT,
    ->          FULLTEXT (title,body)
    ->        ) ENGINE=InnoDB;
Query OK, 0 rows affected (0.16 sec)

mysql> INSERT INTO articles (title,body) VALUES
    ->        ('MySQL Tutorial','DBMS stands for DataBase ...'),
    ->        ('How To Use MySQL Well','After you went through a ...'),
    ->        ('Optimizing MySQL','In this tutorial we show ...'),
    ->        ('1001 MySQL Tricks','1. Never run mysqld as root. 2. ...'),
    ->        ('MySQL vs. YourSQL','In the following database comparison ...'),
    ->        ('MySQL Security','When configured properly, MySQL ...');
Query OK, 6 rows affected (0.01 sec)
Records: 6  Duplicates: 0  Warnings: 0

mysql> SET GLOBAL innodb_ft_aux_table = 'test/articles';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT WORD, DOC_COUNT, DOC_ID, POSITION
    ->        FROM INFORMATION_SCHEMA.INNODB_FT_INDEX_CACHE LIMIT 5;
+------------+-----------+--------+----------+
| WORD       | DOC_COUNT | DOC_ID | POSITION |
+------------+-----------+--------+----------+
| 1001       |         1 |      5 |        0 |
| after      |         1 |      3 |       22 |
| comparison |         1 |      6 |       44 |
| configured |         1 |      7 |       20 |
| database   |         2 |      2 |       31 |
+------------+-----------+--------+----------+
5 rows in set (0.00 sec)
```

### 26.51.18 INFORMATION_SCHEMA INNODB_FT_INDEX_TABLE テーブル

InnoDB テーブルの FULLTEXT インデックスに対するテキスト検索の処理に使用される逆インデックスに関する情報を提供します

```Sql
mysql> CREATE TABLE articles (
    ->          id INT UNSIGNED AUTO_INCREMENT NOT NULL PRIMARY KEY,
    ->          title VARCHAR(200),
    ->          body TEXT,
    ->          FULLTEXT (title,body)
    ->        ) ENGINE=InnoDB;
Query OK, 0 rows affected (0.15 sec)

mysql> INSERT INTO articles (title,body) VALUES
    ->        ('MySQL Tutorial','DBMS stands for DataBase ...'),
    ->        ('How To Use MySQL Well','After you went through a ...'),
    ->        ('Optimizing MySQL','In this tutorial we show ...'),
    ->        ('1001 MySQL Tricks','1. Never run mysqld as root. 2. ...'),
    ->        ('MySQL vs. YourSQL','In the following database comparison ...'),
    ->        ('MySQL Security','When configured properly, MySQL ...');
Query OK, 6 rows affected (0.00 sec)
Records: 6  Duplicates: 0  Warnings: 0

mysql>
mysql> SET GLOBAL innodb_optimize_fulltext_only=ON;
Query OK, 0 rows affected (0.00 sec)

mysql> OPTIMIZE TABLE articles;
+---------------+----------+----------+----------+
| Table         | Op       | Msg_type | Msg_text |
+---------------+----------+----------+----------+
| test.articles | optimize | status   | OK       |
+---------------+----------+----------+----------+
1 row in set (0.01 sec)

mysql> SET GLOBAL innodb_ft_aux_table = 'test/articles';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT WORD, DOC_COUNT, DOC_ID, POSITION
    ->        FROM INFORMATION_SCHEMA.INNODB_FT_INDEX_TABLE LIMIT 5;
+------------+-----------+--------+----------+
| WORD       | DOC_COUNT | DOC_ID | POSITION |
+------------+-----------+--------+----------+
| 1001       |         1 |      5 |        0 |
| after      |         1 |      3 |       22 |
| comparison |         1 |      6 |       44 |
| configured |         1 |      7 |       20 |
| database   |         2 |      2 |       31 |
+------------+-----------+--------+----------+
5 rows in set (0.00 sec)

mysql> SET GLOBAL innodb_optimize_fulltext_only=OFF;
Query OK, 0 rows affected (0.00 sec)
```

### 26.51.19 INFORMATION_SCHEMA INNODB_INDEXES テーブル

InnoDB インデックスに関するメタデータを提供します

```Sql
mysql> table INFORMATION_SCHEMA.INNODB_INDEXES limit 5;
+----------+---------------------+----------+------+----------+---------+-------+-----------------+
| INDEX_ID | NAME                | TABLE_ID | TYPE | N_FIELDS | PAGE_NO | SPACE | MERGE_THRESHOLD |
+----------+---------------------+----------+------+----------+---------+-------+-----------------+
|      153 | PRIMARY             |     1063 |    3 |        6 |       4 |     1 |              50 |
|      154 | PRIMARY             |     1064 |    3 |        6 |       4 |     2 |              50 |
|      155 | idx_actor_last_name |     1064 |    0 |        2 |       5 |     2 |              50 |
|      156 | PRIMARY             |     1065 |    3 |       11 |       4 |     3 |              50 |
|      157 | idx_fk_city_id      |     1065 |    0 |        2 |       5 |     3 |              50 |
+----------+---------------------+----------+------+----------+---------+-------+-----------------+
5 rows in set (0.00 sec)
```

### 26.51.22 INFORMATION_SCHEMA INNODB_METRICS テーブル

様々な InnoDB パフォーマンス情報を提供し、InnoDB の「パフォーマンススキーマ」テーブルの特定のフォーカス領域を補完します

```Sql
mysql> table INFORMATION_SCHEMA.INNODB_METRICS limit 2\G
*************************** 1. row ***************************
           NAME: metadata_table_handles_opened
      SUBSYSTEM: metadata
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
           TYPE: counter
        COMMENT: Number of table handles opened
*************************** 2. row ***************************
           NAME: metadata_table_handles_closed
      SUBSYSTEM: metadata
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
           TYPE: counter
        COMMENT: Number of table handles closed
2 rows in set (0.00 sec)
```

### 26.51.23 INFORMATION_SCHEMA INNODB_SESSION_TEMP_TABLESPACES テーブル

内部およびユーザー作成の一時テーブルに使用されるセッション一時テーブルスペースに関するメタデータを提供します

```Sql
mysql> table INFORMATION_SCHEMA.INNODB_SESSION_TEMP_TABLESPACES;
+----+------------+----------------------------+---------+----------+-----------+
| ID | SPACE      | PATH                       | SIZE    | STATE    | PURPOSE   |
+----+------------+----------------------------+---------+----------+-----------+
|  8 | 4243767290 | .\#innodb_temp\temp_10.ibt |   98304 | ACTIVE   | INTRINSIC |
| 13 | 4243767289 | .\#innodb_temp\temp_9.ibt  | 9437184 | ACTIVE   | INTRINSIC |
|  0 | 4243767281 | .\#innodb_temp\temp_1.ibt  |   81920 | INACTIVE | NONE      |
|  0 | 4243767282 | .\#innodb_temp\temp_2.ibt  |   81920 | INACTIVE | NONE      |
|  0 | 4243767283 | .\#innodb_temp\temp_3.ibt  |   81920 | INACTIVE | NONE      |
|  0 | 4243767284 | .\#innodb_temp\temp_4.ibt  |   81920 | INACTIVE | NONE      |
|  0 | 4243767285 | .\#innodb_temp\temp_5.ibt  |   81920 | INACTIVE | NONE      |
|  0 | 4243767286 | .\#innodb_temp\temp_6.ibt  |   81920 | INACTIVE | NONE      |
|  0 | 4243767287 | .\#innodb_temp\temp_7.ibt  |   81920 | INACTIVE | NONE      |
|  0 | 4243767288 | .\#innodb_temp\temp_8.ibt  |   81920 | INACTIVE | NONE      |
+----+------------+----------------------------+---------+----------+-----------+
10 rows in set (0.00 sec)
```

### 26.51.24 INFORMATION_SCHEMA INNODB_TABLES テーブル

InnoDB テーブルに関するメタデータを提供します

```Sql
mysql> table  INFORMATION_SCHEMA.INNODB_TABLES limit 2\G
*************************** 1. row ***************************
          TABLE_ID: 1025
              NAME: mysql/db
              FLAG: 161
            N_COLS: 25
             SPACE: 4294967294
        ROW_FORMAT: Dynamic
     ZIP_PAGE_SIZE: 0
        SPACE_TYPE: General
      INSTANT_COLS: 0
TOTAL_ROW_VERSIONS: 0
*************************** 2. row ***************************
          TABLE_ID: 1026
              NAME: mysql/user
              FLAG: 161
            N_COLS: 54
             SPACE: 4294967294
        ROW_FORMAT: Dynamic
     ZIP_PAGE_SIZE: 0
        SPACE_TYPE: General
      INSTANT_COLS: 0
TOTAL_ROW_VERSIONS: 0
2 rows in set (0.00 sec)
```

### 26.51.25 INFORMATION_SCHEMA INNODB_TABLESPACES テーブル

InnoDB file-per-table、general および undo テーブルスペースに関するメタデータを提供します

```Sql
mysql> table INFORMATION_SCHEMA.INNODB_TABLESPACES limit 3\G
*************************** 1. row ***************************
          SPACE: 4294967294
           NAME: mysql
           FLAG: 18432
     ROW_FORMAT: Any
      PAGE_SIZE: 16384
  ZIP_PAGE_SIZE: 0
     SPACE_TYPE: General
  FS_BLOCK_SIZE: 65536
      FILE_SIZE: 27262976
 ALLOCATED_SIZE: 27262976
AUTOEXTEND_SIZE: 0
 SERVER_VERSION: 8.2.0
  SPACE_VERSION: 1
     ENCRYPTION: N
          STATE: normal
*************************** 2. row ***************************
          SPACE: 4294967293
           NAME: innodb_temporary
           FLAG: 4096
     ROW_FORMAT: Compact or Redundant
      PAGE_SIZE: 16384
  ZIP_PAGE_SIZE: 0
     SPACE_TYPE: System
  FS_BLOCK_SIZE: 65536
      FILE_SIZE: 12582912
 ALLOCATED_SIZE: 12582912
AUTOEXTEND_SIZE: 0
 SERVER_VERSION: 8.2.0
  SPACE_VERSION: 1
     ENCRYPTION: N
          STATE: normal
*************************** 3. row ***************************
          SPACE: 4294967279
           NAME: innodb_undo_001
           FLAG: 0
     ROW_FORMAT: Undo
      PAGE_SIZE: 16384
  ZIP_PAGE_SIZE: 0
     SPACE_TYPE: Undo
  FS_BLOCK_SIZE: 65536
      FILE_SIZE: 16777216
 ALLOCATED_SIZE: 16777216
AUTOEXTEND_SIZE: 0
 SERVER_VERSION: 8.2.0
  SPACE_VERSION: 1
     ENCRYPTION: N
          STATE: active
3 rows in set (0.18 sec)
```

### 26.51.26 INFORMATION_SCHEMA INNODB_TABLESPACES_BRIEF テーブル

file-per-table、general、undo および system テーブルスペースの領域 ID、名前、パス、フラグおよび領域タイプのメタデータが表示されます

```Sql
mysql> table INFORMATION_SCHEMA.INNODB_TABLESPACES_BRIEF limit 3\G
*************************** 1. row ***************************
     SPACE: 0x30
      NAME: innodb_system
      PATH: ibdata1
      FLAG: 0x3138343332
SPACE_TYPE: System
*************************** 2. row ***************************
     SPACE: 0x34323934393637323739
      NAME: innodb_undo_001
      PATH: .\undo_001
      FLAG: 0x30
SPACE_TYPE: Single
*************************** 3. row ***************************
     SPACE: 0x34323934393637323738
      NAME: innodb_undo_002
      PATH: .\undo_002
      FLAG: 0x30
SPACE_TYPE: Single
3 rows in set (0.00 sec)
```

### 26.51.27 INFORMATION_SCHEMA INNODB_TABLESTATS ビュー

InnoDB テーブルに関する低レベルのステータス情報のビューが表示されます

````sql
mysql> table INFORMATION_SCHEMA.INNODB_TABLESTATS limit 3\G
*************************** 1. row ***************************
         TABLE_ID: 1025
             NAME: mysql/db
STATS_INITIALIZED: Initialized
         NUM_ROWS: 3
 CLUST_INDEX_SIZE: 1
 OTHER_INDEX_SIZE: 1
 MODIFIED_COUNTER: 0
          AUTOINC: 0
        REF_COUNT: 2
*************************** 2. row ***************************
         TABLE_ID: 1026
             NAME: mysql/user
STATS_INITIALIZED: Initialized
         NUM_ROWS: 5
 CLUST_INDEX_SIZE: 1
 OTHER_INDEX_SIZE: 0
 MODIFIED_COUNTER: 0
          AUTOINC: 0
        REF_COUNT: 3
*************************** 3. row ***************************
         TABLE_ID: 1027
             NAME: mysql/default_roles
STATS_INITIALIZED: Initialized
         NUM_ROWS: 0
 CLUST_INDEX_SIZE: 1
 OTHER_INDEX_SIZE: 0
 MODIFIED_COUNTER: 0
          AUTOINC: 0
        REF_COUNT: 2
3 rows in set (0.00 sec)```
````

### 26.51.28 INFORMATION_SCHEMA INNODB_TEMP_TABLE_INFO テーブル

InnoDB インスタンスでアクティブなユーザー作成の InnoDB 一時テーブルに関する情報を提供します

```Sql
mysql> CREATE TEMPORARY TABLE t1 (c1 INT PRIMARY KEY) ENGINE=INNODB;
Query OK, 0 rows affected (0.00 sec)

mysql> table INFORMATION_SCHEMA.INNODB_TEMP_TABLE_INFO;
+----------+---------------+--------+------------+
| TABLE_ID | NAME          | N_COLS | SPACE      |
+----------+---------------+--------+------------+
|     1474 | #sql1264_d_44 |      4 | 4243767288 |
+----------+---------------+--------+------------+
1 row in set (0.00 sec)
```

### 26.51.29 INFORMATION_SCHEMA INNODB_TRX テーブル

InnoDB 内で現在実行されているすべてのトランザクションに関する情報が表示されます

```sql
mysql> table INFORMATION_SCHEMA.INNODB_TRX;
Empty set (0.00 sec)
```

### 26.51.30 INFORMATION_SCHEMA INNODB_VIRTUAL テーブル

InnoDB virtual generated columns および仮想生成カラムのベースとなるカラムに関するメタデータを提供します

```Sql
mysql> CREATE TABLE `t1` (
    ->          `a` int(11) DEFAULT NULL,
    ->          `b` int(11) DEFAULT NULL,
    ->          `c` int(11) GENERATED ALWAYS AS (a+b) VIRTUAL,
    ->          `h` varchar(10) DEFAULT NULL
    ->        ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
Query OK, 0 rows affected, 3 warnings (0.03 sec)

mysql> SELECT * FROM INFORMATION_SCHEMA.INNODB_VIRTUAL
    ->        WHERE TABLE_ID IN
    ->          (SELECT TABLE_ID FROM INFORMATION_SCHEMA.INNODB_TABLES
    ->           WHERE NAME LIKE "test/t1");
+----------+-------+----------+
| TABLE_ID | POS   | BASE_POS |
+----------+-------+----------+
|     1475 | 65538 |        0 |
|     1475 | 65538 |        1 |
+----------+-------+----------+
2 rows in set (0.01 sec)
```

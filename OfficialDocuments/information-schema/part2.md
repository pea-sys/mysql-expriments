### 26.24 INFORMATION_SCHEMA PROFILING テーブル

ステートメントプロファイリング情報を提供します

```Sql
mysql> table INFORMATION_SCHEMA.PROFILING;
Empty set, 1 warning (0.00 sec)
```

### 26.25 INFORMATION_SCHEMA REFERENTIAL_CONSTRAINTS テーブル

外部キーに関する情報を提供します

```Sql
mysql> table INFORMATION_SCHEMA.REFERENTIAL_CONSTRAINTS limit 2\G
*************************** 1. row ***************************
       CONSTRAINT_CATALOG: def
        CONSTRAINT_SCHEMA: sakila
          CONSTRAINT_NAME: fk_address_city
UNIQUE_CONSTRAINT_CATALOG: def
 UNIQUE_CONSTRAINT_SCHEMA: sakila
   UNIQUE_CONSTRAINT_NAME: PRIMARY
             MATCH_OPTION: NONE
              UPDATE_RULE: CASCADE
              DELETE_RULE: RESTRICT
               TABLE_NAME: address
    REFERENCED_TABLE_NAME: city
*************************** 2. row ***************************
       CONSTRAINT_CATALOG: def
        CONSTRAINT_SCHEMA: sakila
          CONSTRAINT_NAME: fk_city_country
UNIQUE_CONSTRAINT_CATALOG: def
 UNIQUE_CONSTRAINT_SCHEMA: sakila
   UNIQUE_CONSTRAINT_NAME: PRIMARY
             MATCH_OPTION: NONE
              UPDATE_RULE: CASCADE
              DELETE_RULE: RESTRICT
               TABLE_NAME: city
    REFERENCED_TABLE_NAME: country
2 rows in set (0.00 sec)
```

### 26.26 INFORMATION_SCHEMA RESOURCE_GROUPS テーブル

リソースグループに関する情報にアクセスできます

```Sql
mysql> table  INFORMATION_SCHEMA.RESOURCE_GROUPS;
+---------------------+---------------------+------------------------+--------------------+-----------------+
| RESOURCE_GROUP_NAME | RESOURCE_GROUP_TYPE | RESOURCE_GROUP_ENABLED | VCPU_IDS           | THREAD_PRIORITY |
+---------------------+---------------------+------------------------+--------------------+-----------------+
| USR_default         | USER                |                      1 | 0x302D33           |               0 |
| SYS_default         | SYSTEM              |                      1 | 0x302D33           |               0 |
+---------------------+---------------------+------------------------+--------------------+-----------------+
2 rows in set (0.00 sec)
```

### 26.27 INFORMATION_SCHEMA ROLE_COLUMN_GRANTS テーブル

現在有効なロールで使用可能または付与されているロールのカラム権限に関する情報が表示されます

```Sql
mysql> table INFORMATION_SCHEMA.ROLE_COLUMN_GRANTS;
Empty set (0.04 sec)

```

### 26.28 INFORMATION_SCHEMA ROLE_ROUTINE_GRANTS テーブル

現在有効なロールで使用可能または付与されているロールのルーチン権限に関する情報が表示されます

```Sql
mysql> table  INFORMATION_SCHEMA.ROLE_ROUTINE_GRANTS;
Empty set (0.00 sec)
```

### 26.29 INFORMATION_SCHEMA ROLE_TABLE_GRANTS テーブル

現在有効なロールで使用可能または付与されているロールのテーブル権限に関する情報が表示されます

```Sql
mysql> table  INFORMATION_SCHEMA.ROLE_TABLE_GRANTS;
Empty set (0.01 sec)
```

### 26.30 INFORMATION_SCHEMA ROUTINES テーブル

ストアドルーチン (ストアドプロシージャーとストアドファンクション) に関する情報を提供します

```sql
mysql> table INFORMATION_SCHEMA.ROUTINES limit 2\G
*************************** 1. row ***************************
           SPECIFIC_NAME: extract_schema_from_file_name
         ROUTINE_CATALOG: def
          ROUTINE_SCHEMA: sys
            ROUTINE_NAME: extract_schema_from_file_name
            ROUTINE_TYPE: FUNCTION
               DATA_TYPE: varchar
CHARACTER_MAXIMUM_LENGTH: 64
  CHARACTER_OCTET_LENGTH: 256
       NUMERIC_PRECISION: NULL
           NUMERIC_SCALE: NULL
      DATETIME_PRECISION: NULL
      CHARACTER_SET_NAME: utf8mb4
          COLLATION_NAME: utf8mb4_0900_ai_ci
          DTD_IDENTIFIER: varchar(64)
            ROUTINE_BODY: SQL
      ROUTINE_DEFINITION: BEGIN
    RETURN LEFT(SUBSTRING_INDEX(SUBSTRING_INDEX(REPLACE(path, '\', '/'), '/', -2), '/', 1), 64);
END
           EXTERNAL_NAME: NULL
       EXTERNAL_LANGUAGE: SQL
         PARAMETER_STYLE: SQL
        IS_DETERMINISTIC: YES
         SQL_DATA_ACCESS: NO SQL
                SQL_PATH: NULL
           SECURITY_TYPE: INVOKER
                 CREATED: 2024-01-01 07:57:21
            LAST_ALTERED: 2024-01-01 07:57:21
                SQL_MODE: ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION
         ROUTINE_COMMENT:
Description
-----------

Takes a raw file path, and attempts to extract the schema name from it.

Useful for when interacting with Performance Schema data
concerning IO statistics, for example.

Currently relies on the fact that a table data file will be within a
specified database directory (will not work with partitions or tables
that specify an individual DATA_DIRECTORY).

Parameters
-----------

path (VARCHAR(512)):
  The full file path to a data file to extract the schema name from.

Returns
-----------

VARCHAR(64)

Example
-----------

mysql> SELECT sys.extract_schema_from_file_name('/var/lib/mysql/employees/employee.ibd');
+----------------------------------------------------------------------------+
| sys.extract_schema_from_file_name('/var/lib/mysql/employees/employee.ibd') |
+----------------------------------------------------------------------------+
| employees                                                                  |
+----------------------------------------------------------------------------+
1 row in set (0.00 sec)

                 DEFINER: mysql.sys@localhost
    CHARACTER_SET_CLIENT: utf8mb4
    COLLATION_CONNECTION: utf8mb4_0900_ai_ci
      DATABASE_COLLATION: utf8mb4_0900_ai_ci
*************************** 2. row ***************************
           SPECIFIC_NAME: extract_table_from_file_name
         ROUTINE_CATALOG: def
          ROUTINE_SCHEMA: sys
            ROUTINE_NAME: extract_table_from_file_name
            ROUTINE_TYPE: FUNCTION
               DATA_TYPE: varchar
CHARACTER_MAXIMUM_LENGTH: 64
  CHARACTER_OCTET_LENGTH: 256
       NUMERIC_PRECISION: NULL
           NUMERIC_SCALE: NULL
      DATETIME_PRECISION: NULL
      CHARACTER_SET_NAME: utf8mb4
          COLLATION_NAME: utf8mb4_0900_ai_ci
          DTD_IDENTIFIER: varchar(64)
            ROUTINE_BODY: SQL
      ROUTINE_DEFINITION: BEGIN
    RETURN LEFT(SUBSTRING_INDEX(REPLACE(SUBSTRING_INDEX(REPLACE(path, '\', '/'), '/', -1), '@0024', '$'), '.', 1), 64);
END
           EXTERNAL_NAME: NULL
       EXTERNAL_LANGUAGE: SQL
         PARAMETER_STYLE: SQL
        IS_DETERMINISTIC: YES
         SQL_DATA_ACCESS: NO SQL
                SQL_PATH: NULL
           SECURITY_TYPE: INVOKER
                 CREATED: 2024-01-01 07:57:21
            LAST_ALTERED: 2024-01-01 07:57:21
                SQL_MODE: ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION
         ROUTINE_COMMENT:
Description
-----------

Takes a raw file path, and extracts the table name from it.

Useful for when interacting with Performance Schema data
concerning IO statistics, for example.

Parameters
-----------

path (VARCHAR(512)):
  The full file path to a data file to extract the table name from.

Returns
-----------

VARCHAR(64)

Example
-----------

mysql> SELECT sys.extract_table_from_file_name('/var/lib/mysql/employees/employee.ibd');
+---------------------------------------------------------------------------+
| sys.extract_table_from_file_name('/var/lib/mysql/employees/employee.ibd') |
+---------------------------------------------------------------------------+
| employee                                                                  |
+---------------------------------------------------------------------------+
1 row in set (0.02 sec)

                 DEFINER: mysql.sys@localhost
    CHARACTER_SET_CLIENT: utf8mb4
    COLLATION_CONNECTION: utf8mb4_0900_ai_ci
      DATABASE_COLLATION: utf8mb4_0900_ai_ci
2 rows in set (0.00 sec)
```

### 26.31 INFORMATION_SCHEMA SCHEMATA テーブル

SCHEMATA テーブルはデータベースに関する情報を提供します

```Sql
mysql> table  INFORMATION_SCHEMA/SCHEMATA;
ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near '/SCHEMATA' at line 1
mysql> table  INFORMATION_SCHEMA.SCHEMATA;
+--------------+--------------------+----------------------------+------------------------+----------+--------------------+
| CATALOG_NAME | SCHEMA_NAME        | DEFAULT_CHARACTER_SET_NAME | DEFAULT_COLLATION_NAME | SQL_PATH | DEFAULT_ENCRYPTION |
+--------------+--------------------+----------------------------+------------------------+----------+--------------------+
| def          | mysql              | utf8mb4                    | utf8mb4_0900_ai_ci     |     NULL | NO
  |
| def          | information_schema | utf8mb3                    | utf8mb3_general_ci     |     NULL | NO
  |
| def          | performance_schema | utf8mb4                    | utf8mb4_0900_ai_ci     |     NULL | NO
  |
| def          | sys                | utf8mb4                    | utf8mb4_0900_ai_ci     |     NULL | NO
  |
| def          | sakila             | utf8mb4                    | utf8mb4_0900_ai_ci     |     NULL | NO
  |
| def          | world              | utf8mb4                    | utf8mb4_0900_ai_ci     |     NULL | NO
  |
| def          | test               | utf8mb4                    | utf8mb4_0900_ai_ci     |     NULL | NO
  |
| def          | world_x            | utf8mb4                    | utf8mb4_0900_ai_ci     |     NULL | NO
  |
| def          | myschema           | utf8mb4                    | utf8mb4_0900_ai_ci     |     NULL | NO
  |
+--------------+--------------------+----------------------------+------------------------+----------+--------------------+
9 rows in set (0.00 sec)
```

### 26.32 INFORMATION_SCHEMA SCHEMATA_EXTENSIONS テーブル

SCHEMATA テーブルをスキーマオプションに関する情報で拡張します

```sql
mysql> table INFORMATION_SCHEMA.SCHEMATA_EXTENSIONS;
+--------------+--------------------+---------+
| CATALOG_NAME | SCHEMA_NAME        | OPTIONS |
+--------------+--------------------+---------+
| def          | mysql              |         |
| def          | information_schema |         |
| def          | performance_schema |         |
| def          | sys                |         |
| def          | sakila             |         |
| def          | world              |         |
| def          | test               |         |
| def          | world_x            |         |
| def          | myschema           |         |
+--------------+--------------------+---------+
9 rows in set (0.00 sec)
```

### 26.33 INFORMATION_SCHEMA SCHEMA_PRIVILEGES テーブル

スキーマ (データベース) 権限に関する情報を提供します

```Sql
mysql> table INFORMATION_SCHEMA.SCHEMA_PRIVILEGES;
+-----------------------------+---------------+--------------------+----------------+--------------+
| GRANTEE                     | TABLE_CATALOG | TABLE_SCHEMA       | PRIVILEGE_TYPE | IS_GRANTABLE |
+-----------------------------+---------------+--------------------+----------------+--------------+
| 'jon'@'ghidora'             | def           | myschema           | EVENT          | NO           |
| 'mysql.sys'@'localhost'     | def           | sys                | TRIGGER        | NO           |
| 'mysql.session'@'localhost' | def           | performance_schema | SELECT         | NO           |
+-----------------------------+---------------+--------------------+----------------+--------------+
3 rows in set (0.00 sec)
```

### 26.34 INFORMATION_SCHEMA STATISTICS テーブル

テーブルインデックスの情報を提供します

```Sql
mysql> table INFORMATION_SCHEMA.STATISTICS limit 2\G
*************************** 1. row ***************************
TABLE_CATALOG: def
 TABLE_SCHEMA: mysql
   TABLE_NAME: innodb_table_stats
   NON_UNIQUE: 0
 INDEX_SCHEMA: mysql
   INDEX_NAME: PRIMARY
 SEQ_IN_INDEX: 1
  COLUMN_NAME: database_name
    COLLATION: A
  CARDINALITY: 6
     SUB_PART: NULL
       PACKED: NULL
     NULLABLE:
   INDEX_TYPE: BTREE
      COMMENT:
INDEX_COMMENT:
   IS_VISIBLE: YES
   EXPRESSION: NULL
*************************** 2. row ***************************
TABLE_CATALOG: def
 TABLE_SCHEMA: mysql
   TABLE_NAME: innodb_table_stats
   NON_UNIQUE: 0
 INDEX_SCHEMA: mysql
   INDEX_NAME: PRIMARY
 SEQ_IN_INDEX: 2
  COLUMN_NAME: table_name
    COLLATION: A
  CARDINALITY: 191
     SUB_PART: NULL
       PACKED: NULL
     NULLABLE:
   INDEX_TYPE: BTREE
      COMMENT:
INDEX_COMMENT:
   IS_VISIBLE: YES
   EXPRESSION: NULL
2 rows in set (0.00 sec)
```

### 26.35 INFORMATION_SCHEMA ST_GEOMETRY_COLUMNS テーブル

空間データを格納するテーブルのカラムに関する情報を提供します

```Sql
mysql> table INFORMATION_SCHEMA.ST_GEOMETRY_COLUMNS;
+---------------+--------------+------------+-------------+----------+--------+--------------------+
| TABLE_CATALOG | TABLE_SCHEMA | TABLE_NAME | COLUMN_NAME | SRS_NAME | SRS_ID | GEOMETRY_TYPE_NAME |
+---------------+--------------+------------+-------------+----------+--------+--------------------+
| def           | sakila       | address    | location    |          |      0 | geometry           |
+---------------+--------------+------------+-------------+----------+--------+--------------------+
1 row in set, 2 warnings (0.06 sec)
```

### 26.36 INFORMATION_SCHEMA ST_SPATIAL_REFERENCE_SYSTEMS テーブル

空間データに使用可能な空間参照システム (SRS) に関する情報が表示されます

```sql
mysql> table INFORMATION_SCHEMA.ST_SPATIAL_REFERENCE_SYSTEMS limit 2\G
*************************** 1. row ***************************
                SRS_NAME:
                  SRS_ID: 0
            ORGANIZATION: NULL
ORGANIZATION_COORDSYS_ID: NULL
              DEFINITION:
             DESCRIPTION: NULL
*************************** 2. row ***************************
                SRS_NAME: Anguilla 1957 / British West Indies Grid
                  SRS_ID: 2000
            ORGANIZATION: EPSG
ORGANIZATION_COORDSYS_ID: 2000
              DEFINITION: PROJCS["Anguilla 1957 / British West Indies Grid",GEOGCS["Anguilla 1957",DATUM["Anguilla 1957",SPHEROID["Clarke 1880 (RGS)",6378249.145,293.465,AUTHORITY["EPSG","7012"]],AUTHORITY["EPSG","6600"]],PRIMEM["Greenwich",0,AUTHORITY["EPSG","8901"]],UNIT["degree",0.017453292519943278,AUTHORITY["EPSG","9122"]],AXIS["Lat",NORTH],AXIS["Lon",EAST],AUTHORITY["EPSG","4600"]],PROJECTION["Transverse Mercator",AUTHORITY["EPSG","9807"]],PARAMETER["Latitude of natural origin",0,AUTHORITY["EPSG","8801"]],PARAMETER["Longitude of natural origin",-62,AUTHORITY["EPSG","8802"]],PARAMETER["Scale factor at natural origin",0.9995,AUTHORITY["EPSG","8805"]],PARAMETER["False easting",400000,AUTHORITY["EPSG","8806"]],PARAMETER["False northing",0,AUTHORITY["EPSG","8807"]],UNIT["metre",1,AUTHORITY["EPSG","9001"]],AXIS["E",EAST],AXIS["N",NORTH],AUTHORITY["EPSG","2000"]]
             DESCRIPTION: NULL
2 rows in set (0.00 sec)
```

### 26.37 INFORMATION_SCHEMA ST_UNITS_OF_MEASURE テーブル

ST_Distance() 関数で使用可能なユニットに関する情報が表示されます

```Sql
mysql> table INFORMATION_SCHEMA.ST_UNITS_OF_MEASURE;
+--------------------------------------+-----------+---------------------+-------------+
| UNIT_NAME                            | UNIT_TYPE | CONVERSION_FACTOR   | DESCRIPTION |
+--------------------------------------+-----------+---------------------+-------------+
| metre                                | LINEAR    |                   1 |             |
| millimetre                           | LINEAR    |               0.001 |             |
| centimetre                           | LINEAR    |                0.01 |             |
| German legal metre                   | LINEAR    |        1.0000135965 |             |
| foot                                 | LINEAR    |              0.3048 |             |
| US survey foot                       | LINEAR    | 0.30480060960121924 |             |
| Clarke's yard                        | LINEAR    |        0.9143917962 |             |
| Clarke's foot                        | LINEAR    |        0.3047972654 |             |
| British link (Sears 1922 truncated)  | LINEAR    |          0.20116756 |             |
| nautical mile                        | LINEAR    |                1852 |             |
| fathom                               | LINEAR    |              1.8288 |             |
| US survey chain                      | LINEAR    |   20.11684023368047 |             |
| US survey link                       | LINEAR    |  0.2011684023368047 |             |
| US survey mile                       | LINEAR    |  1609.3472186944375 |             |
| Indian yard                          | LINEAR    |  0.9143985307444408 |             |
| kilometre                            | LINEAR    |                1000 |             |
| Clarke's chain                       | LINEAR    |       20.1166195164 |             |
| Clarke's link                        | LINEAR    |      0.201166195164 |             |
| British yard (Benoit 1895 A)         | LINEAR    |           0.9143992 |             |
| British yard (Sears 1922)            | LINEAR    |  0.9143984146160288 |             |
| British foot (Sears 1922)            | LINEAR    |  0.3047994715386762 |             |
| Gold Coast foot                      | LINEAR    |  0.3047997101815088 |             |
| British chain (Sears 1922)           | LINEAR    |  20.116765121552632 |             |
| yard                                 | LINEAR    |              0.9144 |             |
| British link (Sears 1922)            | LINEAR    |  0.2011676512155263 |             |
| British foot (Benoit 1895 A)         | LINEAR    |  0.3047997333333333 |             |
| Indian foot (1962)                   | LINEAR    |           0.3047996 |             |
| British chain (Benoit 1895 A)        | LINEAR    |          20.1167824 |             |
| chain                                | LINEAR    |             20.1168 |             |
| British link (Benoit 1895 A)         | LINEAR    |         0.201167824 |             |
| British yard (Benoit 1895 B)         | LINEAR    |  0.9143992042898124 |             |
| British foot (Benoit 1895 B)         | LINEAR    | 0.30479973476327077 |             |
| British chain (Benoit 1895 B)        | LINEAR    |  20.116782494375872 |             |
| British link (Benoit 1895 B)         | LINEAR    |  0.2011678249437587 |             |
| British foot (1865)                  | LINEAR    | 0.30480083333333335 |             |
| Indian foot                          | LINEAR    | 0.30479951024814694 |             |
| Indian foot (1937)                   | LINEAR    |          0.30479841 |             |
| Indian foot (1975)                   | LINEAR    |           0.3047995 |             |
| British foot (1936)                  | LINEAR    |        0.3048007491 |             |
| Indian yard (1937)                   | LINEAR    |          0.91439523 |             |
| Indian yard (1962)                   | LINEAR    |           0.9143988 |             |
| Indian yard (1975)                   | LINEAR    |           0.9143985 |             |
| Statute mile                         | LINEAR    |            1609.344 |             |
| link                                 | LINEAR    |            0.201168 |             |
| British yard (Sears 1922 truncated)  | LINEAR    |            0.914398 |             |
| British foot (Sears 1922 truncated)  | LINEAR    | 0.30479933333333337 |             |
| British chain (Sears 1922 truncated) | LINEAR    |           20.116756 |             |
+--------------------------------------+-----------+---------------------+-------------+
47 rows in set (0.00 sec)
```

### 26.38 INFORMATION_SCHEMA TABLES テーブル

データベース内のテーブルに関する情報を提供します

```Sql
mysql> table INFORMATION_SCHEMA.TABLES limit 2 \G
*************************** 1. row ***************************
  TABLE_CATALOG: def
   TABLE_SCHEMA: mysql
     TABLE_NAME: innodb_table_stats
     TABLE_TYPE: BASE TABLE
         ENGINE: InnoDB
        VERSION: 10
     ROW_FORMAT: Dynamic
     TABLE_ROWS: 191
 AVG_ROW_LENGTH: 85
    DATA_LENGTH: 16384
MAX_DATA_LENGTH: 0
   INDEX_LENGTH: 0
      DATA_FREE: 4194304
 AUTO_INCREMENT: NULL
    CREATE_TIME: 2023-12-31 22:57:16
    UPDATE_TIME: NULL
     CHECK_TIME: NULL
TABLE_COLLATION: utf8mb3_bin
       CHECKSUM: NULL
 CREATE_OPTIONS: row_format=DYNAMIC stats_persistent=0
  TABLE_COMMENT:
*************************** 2. row ***************************
  TABLE_CATALOG: def
   TABLE_SCHEMA: mysql
     TABLE_NAME: innodb_index_stats
     TABLE_TYPE: BASE TABLE
         ENGINE: InnoDB
        VERSION: 10
     ROW_FORMAT: Dynamic
     TABLE_ROWS: 725
 AVG_ROW_LENGTH: 180
    DATA_LENGTH: 131072
MAX_DATA_LENGTH: 0
   INDEX_LENGTH: 0
      DATA_FREE: 4194304
 AUTO_INCREMENT: NULL
    CREATE_TIME: 2023-12-31 22:57:16
    UPDATE_TIME: NULL
     CHECK_TIME: NULL
TABLE_COLLATION: utf8mb3_bin
       CHECKSUM: NULL
 CREATE_OPTIONS: row_format=DYNAMIC stats_persistent=0
  TABLE_COMMENT:
2 rows in set (0.00 sec)
```

### 26.39 INFORMATION_SCHEMA TABLES_EXTENSIONS テーブル

プライマリストレージエンジンおよびセカンダリストレージエンジン用に定義されたテーブル属性に関する情報を提供します

```Sql
mysql> table INFORMATION_SCHEMA.TABLES_EXTENSIONS limit 2\G
*************************** 1. row ***************************
             TABLE_CATALOG: def
              TABLE_SCHEMA: mysql
                TABLE_NAME: innodb_table_stats
          ENGINE_ATTRIBUTE: NULL
SECONDARY_ENGINE_ATTRIBUTE: NULL
*************************** 2. row ***************************
             TABLE_CATALOG: def
              TABLE_SCHEMA: mysql
                TABLE_NAME: innodb_index_stats
          ENGINE_ATTRIBUTE: NULL
SECONDARY_ENGINE_ATTRIBUTE: NULL
2 rows in set (0.00 sec)
```

### 26.40 INFORMATION_SCHEMA TABLESPACES テーブル

このテーブルは未使用です。 非推奨になりました。将来の MySQL リリースで削除される予定です。 その他の INFORMATION_SCHEMA テーブルでは、関連情報が提供される場合があります

```Sql
mysql> table INFORMATION_SCHEMA.TABLESPACES;
Empty set, 1 warning (0.00 sec)
```

### 26.41 INFORMATION_SCHEMA TABLESPACES_EXTENSIONS テーブル

プライマリストレージエンジンに定義されているテーブルスペース属性に関する情報を提供します

```Sql
mysql> table INFORMATION_SCHEMA.TABLESPACES_EXTENSIONS limit 2\G
*************************** 1. row ***************************
 TABLESPACE_NAME: mysql
ENGINE_ATTRIBUTE: NULL
*************************** 2. row ***************************
 TABLESPACE_NAME: innodb_system
ENGINE_ATTRIBUTE: NULL
2 rows in set (0.00 sec)
```

### 26.42 INFORMATION_SCHEMA TABLE_CONSTRAINTS テーブル

どのテーブルに制約があるかを説明します

```Sql
mysql> table INFORMATION_SCHEMA.TABLE_CONSTRAINTS limit 2\G
*************************** 1. row ***************************
CONSTRAINT_CATALOG: def
 CONSTRAINT_SCHEMA: mysql
   CONSTRAINT_NAME: PRIMARY
      TABLE_SCHEMA: mysql
        TABLE_NAME: innodb_table_stats
   CONSTRAINT_TYPE: PRIMARY KEY
          ENFORCED: YES
*************************** 2. row ***************************
CONSTRAINT_CATALOG: def
 CONSTRAINT_SCHEMA: mysql
   CONSTRAINT_NAME: PRIMARY
      TABLE_SCHEMA: mysql
        TABLE_NAME: innodb_index_stats
   CONSTRAINT_TYPE: PRIMARY KEY
          ENFORCED: YES
2 rows in set (0.00 sec)
```

### 26.43 INFORMATION_SCHEMA TABLE_CONSTRAINTS_EXTENSIONS テーブル

プライマリストレージエンジンおよびセカンダリストレージエンジン用に定義されたテーブル制約属性に関する情報を提供します

```Sql
mysql> table INFORMATION_SCHEMA.TABLE_CONSTRAINTS_EXTENSIONS limit 2\G
*************************** 1. row ***************************
        CONSTRAINT_CATALOG: def
         CONSTRAINT_SCHEMA: mysql
           CONSTRAINT_NAME: PRIMARY
                TABLE_NAME: innodb_table_stats
          ENGINE_ATTRIBUTE: NULL
SECONDARY_ENGINE_ATTRIBUTE: NULL
*************************** 2. row ***************************
        CONSTRAINT_CATALOG: def
         CONSTRAINT_SCHEMA: mysql
           CONSTRAINT_NAME: PRIMARY
                TABLE_NAME: innodb_index_stats
          ENGINE_ATTRIBUTE: NULL
SECONDARY_ENGINE_ATTRIBUTE: NULL
2 rows in set (0.00 sec)
```

### 26.44 INFORMATION_SCHEMA TABLE_PRIVILEGES テーブル

テーブル権限に関する情報を提供します

```sql
mysql> table INFORMATION_SCHEMA.TABLE_PRIVILEGES;
+-----------------------------+---------------+--------------+------------+----------------+--------------+
| GRANTEE                     | TABLE_CATALOG | TABLE_SCHEMA | TABLE_NAME | PRIVILEGE_TYPE | IS_GRANTABLE |
+-----------------------------+---------------+--------------+------------+----------------+--------------+
| 'mysql.session'@'localhost' | def           | mysql        | user       | SELECT         | NO           |
| 'mysql.sys'@'localhost'     | def           | sys          | sys_config | SELECT         | NO           |
+-----------------------------+---------------+--------------+------------+----------------+--------------+
2 rows in set (0.00 sec)
```

### 26.45 INFORMATION_SCHEMA TRIGGERS テーブル

トリガーに関する情報を提供します

```Sql
mysql> table INFORMATION_SCHEMA.TRIGGERS limit 2\G
*************************** 1. row ***************************
           TRIGGER_CATALOG: def
            TRIGGER_SCHEMA: sys
              TRIGGER_NAME: sys_config_insert_set_user
        EVENT_MANIPULATION: INSERT
      EVENT_OBJECT_CATALOG: def
       EVENT_OBJECT_SCHEMA: sys
        EVENT_OBJECT_TABLE: sys_config
              ACTION_ORDER: 1
          ACTION_CONDITION: NULL
          ACTION_STATEMENT: BEGIN
    IF @sys.ignore_sys_config_triggers != true AND NEW.set_by IS NULL THEN
        SET NEW.set_by = USER();
    END IF;
END
        ACTION_ORIENTATION: ROW
             ACTION_TIMING: BEFORE
ACTION_REFERENCE_OLD_TABLE: NULL
ACTION_REFERENCE_NEW_TABLE: NULL
  ACTION_REFERENCE_OLD_ROW: OLD
  ACTION_REFERENCE_NEW_ROW: NEW
                   CREATED: 2024-01-01 07:57:21.72
                  SQL_MODE: ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION
                   DEFINER: mysql.sys@localhost
      CHARACTER_SET_CLIENT: utf8mb4
      COLLATION_CONNECTION: utf8mb4_0900_ai_ci
        DATABASE_COLLATION: utf8mb4_0900_ai_ci
*************************** 2. row ***************************
           TRIGGER_CATALOG: def
            TRIGGER_SCHEMA: sys
              TRIGGER_NAME: sys_config_update_set_user
        EVENT_MANIPULATION: UPDATE
      EVENT_OBJECT_CATALOG: def
       EVENT_OBJECT_SCHEMA: sys
        EVENT_OBJECT_TABLE: sys_config
              ACTION_ORDER: 1
          ACTION_CONDITION: NULL
          ACTION_STATEMENT: BEGIN
    IF @sys.ignore_sys_config_triggers != true AND NEW.set_by IS NULL THEN
        SET NEW.set_by = USER();
    END IF;
END
        ACTION_ORIENTATION: ROW
             ACTION_TIMING: BEFORE
ACTION_REFERENCE_OLD_TABLE: NULL
ACTION_REFERENCE_NEW_TABLE: NULL
  ACTION_REFERENCE_OLD_ROW: OLD
  ACTION_REFERENCE_NEW_ROW: NEW
                   CREATED: 2024-01-01 07:57:21.72
                  SQL_MODE: ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION
                   DEFINER: mysql.sys@localhost
      CHARACTER_SET_CLIENT: utf8mb4
      COLLATION_CONNECTION: utf8mb4_0900_ai_ci
        DATABASE_COLLATION: utf8mb4_0900_ai_ci
2 rows in set (0.00 sec)
```

### 26.46 INFORMATION_SCHEMA USER_ATTRIBUTES テーブル

ユーザーコメントおよびユーザー属性に関する情報が表示されます

```sql
mysql> table INFORMATION_SCHEMA.USER_ATTRIBUTES;
+------------------+-----------+-----------+
| USER             | HOST      | ATTRIBUTE |
+------------------+-----------+-----------+
| jon              | ghidora   | NULL      |
| mysql.infoschema | localhost | NULL      |
| mysql.session    | localhost | NULL      |
| mysql.sys        | localhost | NULL      |
| root             | localhost | NULL      |
+------------------+-----------+-----------+
5 rows in set (0.01 sec)
```

### 26.47 INFORMATION_SCHEMA USER_PRIVILEGES テーブル

グローバル権限に関する情報を提供します

````Sql
mysql> table INFORMATION_SCHEMA.USER_PRIVILEGES limit 5;
+--------------------------------+---------------+--------------------+--------------+
| GRANTEE                        | TABLE_CATALOG | PRIVILEGE_TYPE     | IS_GRANTABLE |
+--------------------------------+---------------+--------------------+--------------+
| 'jon'@'ghidora'                | def           | EVENT              | NO           |
| 'mysql.infoschema'@'localhost' | def           | SELECT             | NO           |
| 'mysql.infoschema'@'localhost' | def           | AUDIT_ABORT_EXEMPT | NO           |
| 'mysql.infoschema'@'localhost' | def           | FIREWALL_EXEMPT    | NO           |
| 'mysql.infoschema'@'localhost' | def           | SYSTEM_USER        | NO           |
+--------------------------------+---------------+--------------------+--------------+
5 rows in set (0.00 sec)```
````

### 26.48 INFORMATION_SCHEMA VIEWS テーブル

データベース内のビューに関する情報を提供します

```Sql
mysql> table INFORMATION_SCHEMA.VIEWS limit 2\G
*************************** 1. row ***************************
       TABLE_CATALOG: def
        TABLE_SCHEMA: sys
          TABLE_NAME: version
     VIEW_DEFINITION: select '2.1.2' AS `sys_version`,version() AS `mysql_version`
        CHECK_OPTION: NONE
        IS_UPDATABLE: NO
             DEFINER: mysql.sys@localhost
       SECURITY_TYPE: INVOKER
CHARACTER_SET_CLIENT: utf8mb4
COLLATION_CONNECTION: utf8mb4_0900_ai_ci
*************************** 2. row ***************************
       TABLE_CATALOG: def
        TABLE_SCHEMA: sys
          TABLE_NAME: innodb_buffer_stats_by_schema
     VIEW_DEFINITION: select if((locate('.',`ibp`.`TABLE_NAME`) = 0),'InnoDB System',replace(substring_index(`ibp`.`TABLE_NAME`,'.',1),'`','')) AS `object_schema`,format_bytes(sum(if((`ibp`.`COMPRESSED_SIZE` = 0),16384,`ibp`.`COMPRESSED_SIZE`))) AS `allocated`,format_bytes(sum(`ibp`.`DATA_SIZE`)) AS `data`,count(`ibp`.`PAGE_NUMBER`) AS `pages`,count(if((`ibp`.`IS_HASHED` = 'YES'),1,NULL)) AS `pages_hashed`,count(if((`ibp`.`IS_OLD` = 'YES'),1,NULL)) AS `pages_old`,round((sum(`ibp`.`NUMBER_RECORDS`) / count(distinct `ibp`.`INDEX_NAME`)),0) AS `rows_cached` from `information_schema`.`INNODB_BUFFER_PAGE` `ibp` where (`ibp`.`TABLE_NAME` is not null) group by `object_schema` order by sum(if((`ibp`.`COMPRESSED_SIZE` = 0),16384,`ibp`.`COMPRESSED_SIZE`)) desc
        CHECK_OPTION: NONE
        IS_UPDATABLE: NO
             DEFINER: mysql.sys@localhost
       SECURITY_TYPE: INVOKER
CHARACTER_SET_CLIENT: utf8mb4
COLLATION_CONNECTION: utf8mb4_0900_ai_ci
2 rows in set (0.00 sec)
```

### 26.49 INFORMATION_SCHEMA VIEW_ROUTINE_USAGE テーブル

ビュー定義で使用されるストアドファンクションに関する情報にアクセスできます

```Sql
mysql> table INFORMATION_SCHEMA.VIEW_ROUTINE_USAGE limit 5;
+---------------+--------------+---------------------------------+------------------+-----------------+-------------------------------+
| TABLE_CATALOG | TABLE_SCHEMA | TABLE_NAME                      | SPECIFIC_CATALOG | SPECIFIC_SCHEMA | SPECIFIC_NAME                 |
+---------------+--------------+---------------------------------+------------------+-----------------+-------------------------------+
| def           | sys          | x$ps_schema_table_statistics_io | def              | sys             | extract_schema_from_file_name |
| def           | sys          | x$ps_schema_table_statistics_io | def              | sys             | extract_table_from_file_name  |
| def           | sys          | io_global_by_file_by_latency    | def              | sys             | format_path                   |
| def           | sys          | io_global_by_file_by_bytes      | def              | sys             | format_path                   |
| def           | sys          | latest_file_io                  | def              | sys             | format_path                   |
+---------------+--------------+---------------------------------+------------------+-----------------+-------------------------------+
5 rows in set (0.00 sec)
```

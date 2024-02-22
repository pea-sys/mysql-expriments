### 15.12.1 オンライン DDL 操作
セカンダリインデックスの作成または追加
```sql
mysql> create table t(a int);
Query OK, 0 rows affected (0.02 sec)

mysql> ALTER TABLE t ADD INDEX a_index (a);
Query OK, 0 rows affected (0.02 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
インデックスの削除
```sql
mysql> ALTER TABLE t DROP INDEX a_index;
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
インデックスの名前変更
```sql
mysql> ALTER TABLE t RENAME INDEX a_index TO new_index_name, ALGORITHM=INPLACE, LOCK=NONE;
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

SPATIAL インデックスの追加
```sql
mysql> CREATE TABLE geom (g GEOMETRY NOT NULL);
Query OK, 0 rows affected (0.03 sec)

mysql> ALTER TABLE geom ADD SPATIAL INDEX(g), ALGORITHM=INPLACE, LOCK=SHARED;
Query OK, 0 rows affected, 1 warning (0.02 sec)
Records: 0  Duplicates: 0  Warnings: 1
```
主キーの追加
```sql
mysql> ALTER TABLE t ADD PRIMARY KEY (a), ALGORITHM=INPLACE, LOCK=NONE;
Query OK, 0 rows affected (0.04 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
主キーの削除
```sql
mysql> ALTER TABLE t DROP PRIMARY KEY, ALGORITHM=COPY;
Query OK, 0 rows affected (0.05 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
カラムの追加
```sql
mysql> ALTER TABLE t ADD COLUMN b text, ALGORITHM=INSTANT;
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
カラム名の変更
```sql
mysql> ALTER TABLE t CHANGE a c int, ALGORITHM=INPLACE, LOCK=NONE;
Query OK, 0 rows affected (0.05 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
カラムの削除
```sql
mysql> ALTER TABLE t DROP COLUMN c, ALGORITHM=INPLACE, LOCK=NONE;
Query OK, 0 rows affected (0.04 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
カラムのデータ型の変更
```sql
mysql> ALTER TABLE t CHANGE a a BIGINT, ALGORITHM=COPY;
Query OK, 1 row affected (0.04 sec)
Records: 1  Duplicates: 0  Warnings: 0
```
ENUM または SET カラムの定義の変更
```Sql
mysql> CREATE TABLE t1 (c1 ENUM('a', 'b', 'c'));
Query OK, 0 rows affected (0.02 sec)

mysql> ALTER TABLE t1 MODIFY COLUMN c1 ENUM('a', 'b', 'c', 'd'), ALGORITHM=INSTANT;
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
### 15.12.2 オンライン DDL のパフォーマンスと同時実行性
次の例は、排他的メタデータロックを待機しているオンライン DDL 操作と、保留中のメタデータロックがテーブルの後続のトランザクションをブロックする方法を示しています

トランザクションA
```Sql
mysql> CREATE TABLE t1 (c1 INT) ENGINE=InnoDB;
Query OK, 0 rows affected (0.03 sec)

mysql> START TRANSACTION;
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT * FROM t1;
Empty set (0.00 sec)
```
トランザクションB
```Sql
mysql> ALTER TABLE t1 ADD COLUMN x INT, ALGORITHM=INPLACE, LOCK=NONE;
```
トランザクションC
```Sql
mysql> SELECT * FROM t1;
```
```sql
mysql> SHOW FULL PROCESSLIST\G
*************************** 1. row ***************************
     Id: 5
   User: event_scheduler
   Host: localhost
     db: NULL
Command: Daemon
   Time: 27716
  State: Waiting on empty queue
   Info: NULL
*************************** 2. row ***************************
     Id: 13
   User: root
   Host: localhost
     db: sample
Command: Sleep
   Time: 72
  State:
   Info: NULL
*************************** 3. row ***************************
     Id: 16
   User: root
   Host: localhost:55518
     db: sample
Command: Sleep
   Time: 51
  State:
   Info: NULL
*************************** 4. row ***************************
     Id: 19
   User: root
   Host: localhost:55566
     db: sample
Command: Sleep
   Time: 51
  State:
   Info: NULL
*************************** 5. row ***************************
     Id: 20
   User: root
   Host: localhost
     db: sample
Command: Query
   Time: 48
  State: Waiting for table metadata lock
   Info: ALTER TABLE t1 ADD COLUMN x INT, ALGORITHM=INPLACE, LOCK=NONE
*************************** 6. row ***************************
     Id: 21
   User: root
   Host: localhost
     db: sample
Command: Query
   Time: 20
  State: Waiting for table metadata lock
   Info: SELECT * FROM t1
*************************** 7. row ***************************
     Id: 22
   User: root
   Host: localhost
     db: sample
Command: Query
   Time: 0
  State: init
   Info: SHOW FULL PROCESSLIST
7 rows in set, 1 warning (0.00 sec)
```
### 15.13 InnoDB 保存データ暗号化
次のステートメントは、デフォルトのスキーマおよびテーブルスペースの暗号化をグローバルに有効にします
```Sql
mysql> SET GLOBAL default_table_encryption=ON;
Query OK, 0 rows affected (0.00 sec)
```
スキーマのデフォルトの暗号化設定は、次の例に示すように、スキーマの作成または変更時に DEFAULT ENCRYPTION 句を使用して定義することもできます
```Sql
mysql> CREATE SCHEMA test DEFAULT ENCRYPTION = 'Y';
Query OK, 1 row affected (0.01 sec)
```
### 15.15 InnoDB INFORMATION_SCHEMA テーブル
InnoDB INFORMATION_SCHEMA テーブルのリストを表示できます
```sql
mysql> SHOW TABLES FROM INFORMATION_SCHEMA LIKE 'INNODB%';
+----------------------------------------+
| Tables_in_information_schema (INNODB%) |
+----------------------------------------+
| INNODB_BUFFER_PAGE                     |
| INNODB_BUFFER_PAGE_LRU                 |
| INNODB_BUFFER_POOL_STATS               |
| INNODB_CACHED_INDEXES                  |
| INNODB_CMP                             |
| INNODB_CMP_PER_INDEX                   |
| INNODB_CMP_PER_INDEX_RESET             |
| INNODB_CMP_RESET                       |
| INNODB_CMPMEM                          |
| INNODB_CMPMEM_RESET                    |
| INNODB_COLUMNS                         |
| INNODB_DATAFILES                       |
| INNODB_FIELDS                          |
| INNODB_FOREIGN                         |
| INNODB_FOREIGN_COLS                    |
| INNODB_FT_BEING_DELETED                |
| INNODB_FT_CONFIG                       |
| INNODB_FT_DEFAULT_STOPWORD             |
| INNODB_FT_DELETED                      |
| INNODB_FT_INDEX_CACHE                  |
| INNODB_FT_INDEX_TABLE                  |
| INNODB_INDEXES                         |
| INNODB_METRICS                         |
| INNODB_SESSION_TEMP_TABLESPACES        |
| INNODB_TABLES                          |
| INNODB_TABLESPACES                     |
| INNODB_TABLESPACES_BRIEF               |
| INNODB_TABLESTATS                      |
| INNODB_TEMP_TABLE_INFO                 |
| INNODB_TRX                             |
| INNODB_VIRTUAL                         |
+----------------------------------------+
31 rows in set (0.00 sec)
```

### 15.15.2.1 InnoDB トランザクションの使用および情報のロック
ブロックしているトランザクションの識別

トランザクションA
```sql
mysql> BEGIN;
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT a FROM t FOR UPDATE;
+------+
| a    |
+------+
| 1    |
+------+
1 row in set (0.00 sec)

mysql> SELECT SLEEP(100);
```
トランザクションB
```sql
mysql> SELECT b FROM t FOR UPDATE;
```
トランザクションC
```sql
mysql> SELECT a FROM t FOR UPDATE;
```

```Sql
mysql> SELECT
    ->   r.trx_id waiting_trx_id,
    ->   r.trx_mysql_thread_id waiting_thread,
    ->   r.trx_query waiting_query,
    ->   b.trx_id blocking_trx_id,
    ->   b.trx_mysql_thread_id blocking_thread,
    ->   b.trx_query blocking_query
    -> FROM       performance_schema.data_lock_waits w
    -> INNER JOIN information_schema.innodb_trx b
    ->   ON b.trx_id = w.blocking_engine_transaction_id
    -> INNER JOIN information_schema.innodb_trx r
    ->   ON r.trx_id = w.requesting_engine_transaction_id;
+----------------+----------------+----------------------------+-----------------+-----------------+----------------------------+
| waiting_trx_id | waiting_thread | waiting_query              | blocking_trx_id | blocking_thread | blocking_query             |
+----------------+----------------+----------------------------+-----------------+-----------------+----------------------------+
|           2060 |             26 | SELECT a FROM t FOR UPDATE |            2058 |              24 | SELECT SLEEP(100)          |
|           2060 |             26 | SELECT a FROM t FOR UPDATE |            2059 |              25 | SELECT b FROM t FOR UPDATE |
|           2059 |             25 | SELECT b FROM t FOR UPDATE |            2058 |              24 | SELECT SLEEP(100)          |
+----------------+----------------+----------------------------+-----------------+-----------------+----------------------------+
3 rows in set (0.01 sec)
```
### 15.15.3 InnoDB INFORMATION_SCHEMA スキーマオブジェクトテーブル
この例では、単一のインデックス (i1) を持つ単純なテーブル (t1) を使用して、InnoDB INFORMATION_SCHEMA スキーマオブジェクトテーブルにあるメタデータのタイプを示します
```sql
mysql> CREATE DATABASE test;
Query OK, 1 row affected (0.00 sec)

mysql> USE test;
Database changed
mysql> CREATE TABLE t1 (
    ->        col1 INT,
    ->        col2 CHAR(10),
    ->        col3 VARCHAR(10))
    ->        ENGINE = InnoDB;
Query OK, 0 rows affected (0.02 sec)

mysql> CREATE INDEX i1 ON t1(col1);
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
テーブル t1 を作成した後、INNODB_TABLES をクエリーして test/t1 のメタデータを検索します
```sql
mysql> SELECT * FROM INFORMATION_SCHEMA.INNODB_TABLES WHERE NAME='test/t1' \G
*************************** 1. row ***************************
          TABLE_ID: 1094
              NAME: test/t1
              FLAG: 33
            N_COLS: 6
             SPACE: 32
        ROW_FORMAT: Dynamic
     ZIP_PAGE_SIZE: 0
        SPACE_TYPE: Single
      INSTANT_COLS: 0
TOTAL_ROW_VERSIONS: 0
1 row in set (0.02 sec)
```
INNODB_TABLES の TABLE_ID 情報を使用して、INNODB_COLUMNS テーブルにテーブルのカラムに関する情報をクエリーします
```Sql
mysql> CREATE DATABASE test;
Query OK, 1 row affected (0.00 sec)

mysql> USE test;
Database changed
mysql> CREATE TABLE t1 (
    ->        col1 INT,
    ->        col2 CHAR(10),
    ->        col3 VARCHAR(10))
    ->        ENGINE = InnoDB;
Query OK, 0 rows affected (0.02 sec)

mysql> CREATE INDEX i1 ON t1(col1);
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> SELECT * FROM INFORMATION_SCHEMA.INNODB_TABLES WHERE NAME='test/t1' \G
*************************** 1. row ***************************
          TABLE_ID: 1094
              NAME: test/t1
              FLAG: 33
            N_COLS: 6
             SPACE: 32
        ROW_FORMAT: Dynamic
     ZIP_PAGE_SIZE: 0
        SPACE_TYPE: Single
      INSTANT_COLS: 0
TOTAL_ROW_VERSIONS: 0
1 row in set (0.02 sec)

mysql> SELECT * FROM INFORMATION_SCHEMA.INNODB_COLUMNS where TABLE_ID = 1094\G
*************************** 1. row ***************************
     TABLE_ID: 1094
         NAME: col1
          POS: 0
        MTYPE: 6
       PRTYPE: 1027
          LEN: 4
  HAS_DEFAULT: 0
DEFAULT_VALUE: NULL
*************************** 2. row ***************************
     TABLE_ID: 1094
         NAME: col2
          POS: 1
        MTYPE: 13
       PRTYPE: 16711934
          LEN: 40
  HAS_DEFAULT: 0
DEFAULT_VALUE: NULL
*************************** 3. row ***************************
     TABLE_ID: 1094
         NAME: col3
          POS: 2
        MTYPE: 12
       PRTYPE: 16711695
          LEN: 40
  HAS_DEFAULT: 0
DEFAULT_VALUE: NULL
3 rows in set (0.00 sec)
```
INNODB_INDEXES の INDEX_ID 情報を使用して、INNODB_FIELDS にインデックス i1 のフィールドに関する情報をクエリーします
```Sql
mysql> SELECT * FROM INFORMATION_SCHEMA.INNODB_INDEXES WHERE TABLE_ID = 1094 \G
*************************** 1. row ***************************
       INDEX_ID: 213
           NAME: GEN_CLUST_INDEX
       TABLE_ID: 1094
           TYPE: 1
       N_FIELDS: 6
        PAGE_NO: 4
          SPACE: 32
MERGE_THRESHOLD: 50
*************************** 2. row ***************************
       INDEX_ID: 214
           NAME: i1
       TABLE_ID: 1094
           TYPE: 0
       N_FIELDS: 2
        PAGE_NO: 5
          SPACE: 32
MERGE_THRESHOLD: 50
2 rows in set (0.00 sec)
```
INNODB_TABLES の SPACE 情報を使用して、INNODB_TABLESPACES テーブルにテーブルのテーブルスペースに関する情報をクエリーします。
```Sql
mysql> SELECT * FROM INFORMATION_SCHEMA.INNODB_TABLESPACES WHERE SPACE = 32 \G
*************************** 1. row ***************************
          SPACE: 32
           NAME: test/t1
           FLAG: 16417
     ROW_FORMAT: Dynamic
      PAGE_SIZE: 16384
  ZIP_PAGE_SIZE: 0
     SPACE_TYPE: Single
  FS_BLOCK_SIZE: 65536
      FILE_SIZE: 131072
 ALLOCATED_SIZE: 131072
AUTOEXTEND_SIZE: 0
 SERVER_VERSION: 8.2.0
  SPACE_VERSION: 1
     ENCRYPTION: N
          STATE: normal
1 row in set (0.04 sec)
```
INNODB_TABLES の SPACE 情報を再度使用して、INNODB_DATAFILES にテーブルスペースデータファイルの場所をクエリーします
```Sql
mysql> SELECT * FROM INFORMATION_SCHEMA.INNODB_DATAFILES WHERE SPACE = 32 \G
*************************** 1. row ***************************
SPACE: 0x3332
 PATH: .\test\t1.ibd
1 row in set (0.00 sec)
```
最後のステップとして、テーブル t1 (TABLE_ID = 71) に行を挿入し、INNODB_TABLESTATS テーブルのデータを表示します
```Sql
mysql> SELECT * FROM INFORMATION_SCHEMA.INNODB_TABLESTATS where TABLE_ID = 1094 \G
*************************** 1. row ***************************
         TABLE_ID: 1094
             NAME: test/t1
STATS_INITIALIZED: Initialized
         NUM_ROWS: 1
 CLUST_INDEX_SIZE: 1
 OTHER_INDEX_SIZE: 0
 MODIFIED_COUNTER: 1
          AUTOINC: 0
        REF_COUNT: 2
1 row in set (0.00 sec)
```


外部キー INFORMATION_SCHEMA スキーマオブジェクトテーブル

テストデータベースおよび親テーブルと子テーブルを作成します
```Sql
mysql> CREATE TABLE parent (id INT NOT NULL,
    ->        PRIMARY KEY (id)) ENGINE=INNODB;
Query OK, 0 rows affected (0.02 sec)

mysql> CREATE TABLE child (id INT, parent_id INT,
    ->        INDEX par_ind (parent_id),
    ->        CONSTRAINT fk1
    ->        FOREIGN KEY (parent_id) REFERENCES parent(id)
    ->        ON DELETE CASCADE) ENGINE=INNODB;
Query OK, 0 rows affected (0.04 sec)
```
親テーブルと子テーブルが作成されたら、INNODB_FOREIGN をクエリーして、test/child と test/parent の外部キー関係の外部キーデータを見つけます
```Sql
mysql> SELECT * FROM INFORMATION_SCHEMA.INNODB_FOREIGN \G
*************************** 1. row ***************************
      ID: sakila/fk_address_city
FOR_NAME: sakila/address
REF_NAME: sakila/city
  N_COLS: 1
    TYPE: 4
*************************** 2. row ***************************
      ID: sakila/fk_city_country
FOR_NAME: sakila/city
REF_NAME: sakila/country
  N_COLS: 1
    TYPE: 4
*************************** 3. row ***************************
      ID: sakila/fk_customer_store
FOR_NAME: sakila/customer
REF_NAME: sakila/store
  N_COLS: 1
    TYPE: 4
*************************** 4. row ***************************
      ID: sakila/fk_customer_address
FOR_NAME: sakila/customer
REF_NAME: sakila/address
  N_COLS: 1
    TYPE: 4
*************************** 5. row ***************************
      ID: sakila/fk_film_language
FOR_NAME: sakila/film
REF_NAME: sakila/language
  N_COLS: 1
    TYPE: 4
*************************** 6. row ***************************
      ID: sakila/fk_film_language_original
FOR_NAME: sakila/film
REF_NAME: sakila/language
  N_COLS: 1
    TYPE: 4
*************************** 7. row ***************************
      ID: sakila/fk_film_actor_actor
FOR_NAME: sakila/film_actor
REF_NAME: sakila/actor
  N_COLS: 1
    TYPE: 4
*************************** 8. row ***************************
      ID: sakila/fk_film_actor_film
FOR_NAME: sakila/film_actor
REF_NAME: sakila/film
  N_COLS: 1
    TYPE: 4
*************************** 9. row ***************************
      ID: sakila/fk_film_category_film
FOR_NAME: sakila/film_category
REF_NAME: sakila/film
  N_COLS: 1
    TYPE: 4
*************************** 10. row ***************************
      ID: sakila/fk_film_category_category
FOR_NAME: sakila/film_category
REF_NAME: sakila/category
  N_COLS: 1
    TYPE: 4
*************************** 11. row ***************************
      ID: sakila/fk_inventory_film
FOR_NAME: sakila/inventory
REF_NAME: sakila/film
  N_COLS: 1
    TYPE: 4
*************************** 12. row ***************************
      ID: sakila/fk_inventory_store
FOR_NAME: sakila/inventory
REF_NAME: sakila/store
  N_COLS: 1
    TYPE: 4
*************************** 13. row ***************************
      ID: sakila/fk_payment_customer
FOR_NAME: sakila/payment
REF_NAME: sakila/customer
  N_COLS: 1
    TYPE: 4
*************************** 14. row ***************************
      ID: sakila/fk_payment_staff
FOR_NAME: sakila/payment
REF_NAME: sakila/staff
  N_COLS: 1
    TYPE: 4
*************************** 15. row ***************************
      ID: sakila/fk_payment_rental
FOR_NAME: sakila/payment
REF_NAME: sakila/rental
  N_COLS: 1
    TYPE: 6
*************************** 16. row ***************************
      ID: sakila/fk_rental_inventory
FOR_NAME: sakila/rental
REF_NAME: sakila/inventory
  N_COLS: 1
    TYPE: 4
*************************** 17. row ***************************
      ID: sakila/fk_rental_customer
FOR_NAME: sakila/rental
REF_NAME: sakila/customer
  N_COLS: 1
    TYPE: 4
*************************** 18. row ***************************
      ID: sakila/fk_rental_staff
FOR_NAME: sakila/rental
REF_NAME: sakila/staff
  N_COLS: 1
    TYPE: 4
*************************** 19. row ***************************
      ID: sakila/fk_staff_address
FOR_NAME: sakila/staff
REF_NAME: sakila/address
  N_COLS: 1
    TYPE: 4
*************************** 20. row ***************************
      ID: sakila/fk_staff_store
FOR_NAME: sakila/staff
REF_NAME: sakila/store
  N_COLS: 1
    TYPE: 4
*************************** 21. row ***************************
      ID: sakila/fk_store_staff
FOR_NAME: sakila/store
REF_NAME: sakila/staff
  N_COLS: 1
    TYPE: 4
*************************** 22. row ***************************
      ID: sakila/fk_store_address
FOR_NAME: sakila/store
REF_NAME: sakila/address
  N_COLS: 1
    TYPE: 4
*************************** 23. row ***************************
      ID: world/city_ibfk_1
FOR_NAME: world/city
REF_NAME: world/country
  N_COLS: 1
    TYPE: 48
*************************** 24. row ***************************
      ID: world/countryLanguage_ibfk_1
FOR_NAME: world/countrylanguage
REF_NAME: world/country
  N_COLS: 1
    TYPE: 48
*************************** 25. row ***************************
      ID: test/fk1
FOR_NAME: test/child
REF_NAME: test/parent
  N_COLS: 1
    TYPE: 33
25 rows in set (0.00 sec)
```
外部キー ID を使用して、INNODB_FOREIGN_COLS をクエリーして、外部キーのカラムに関するデータを表示します
```sql
mysql> SELECT * FROM INFORMATION_SCHEMA.INNODB_FOREIGN_COLS WHERE ID = 'test/fk1' \G
*************************** 1. row ***************************
          ID: test/fk1
FOR_COL_NAME: parent_id
REF_COL_NAME: id
         POS: 1
1 row in set (0.00 sec)
```

InnoDB INFORMATION_SCHEMA スキーマオブジェクトテーブルの結合
```sql
mysql> SELECT a.NAME, a.ROW_FORMAT,
    ->         @page_size :=
    ->          IF(a.ROW_FORMAT='Compressed',
    ->           b.ZIP_PAGE_SIZE, b.PAGE_SIZE)
    ->           AS page_size,
    ->          ROUND((@page_size * c.CLUST_INDEX_SIZE)
    ->           /(1024*1024)) AS pk_mb,
    ->          ROUND((@page_size * c.OTHER_INDEX_SIZE)
    ->           /(1024*1024)) AS secidx_mb
    ->        FROM INFORMATION_SCHEMA.INNODB_TABLES a
    ->        INNER JOIN INFORMATION_SCHEMA.INNODB_TABLESPACES b on a.NAME = b.NAME
    ->        INNER JOIN INFORMATION_SCHEMA.INNODB_TABLESTATS c on b.NAME = c.NAME
    ->        WHERE a.NAME LIKE 'test/%'
    ->        ORDER BY a.NAME DESC;
+-------------+------------+-----------+-------+-----------+
| NAME        | ROW_FORMAT | page_size | pk_mb | secidx_mb |
+-------------+------------+-----------+-------+-----------+
| test/parent | Dynamic    |     16384 |     0 |         0 |
| test/child  | Dynamic    |     16384 |     0 |         0 |
+-------------+------------+-----------+-------+-----------+
2 rows in set, 1 warning (0.04 sec)
```

### 15.15.4 InnoDB INFORMATION_SCHEMA FULLTEXT インデックステーブル
次のテーブルに、FULLTEXT インデックスのメタデータを示します
```sql
mysql> SHOW TABLES FROM INFORMATION_SCHEMA LIKE 'INNODB_FT%';
+-------------------------------------------+
| Tables_in_information_schema (INNODB_FT%) |
+-------------------------------------------+
| INNODB_FT_BEING_DELETED                   |
| INNODB_FT_CONFIG                          |
| INNODB_FT_DEFAULT_STOPWORD                |
| INNODB_FT_DELETED                         |
| INNODB_FT_INDEX_CACHE                     |
| INNODB_FT_INDEX_TABLE                     |
+-------------------------------------------+
6 rows in set (0.01 sec)
```
InnoDB FULLTEXT インデックスの INFORMATION_SCHEMA テーブル


FULLTEXT インデックスを含むテーブルを作成し、一部のデータを挿入します
```Sql
mysql> CREATE TABLE articles (
    ->          id INT UNSIGNED AUTO_INCREMENT NOT NULL PRIMARY KEY,
    ->          title VARCHAR(200),
    ->          body TEXT,
    ->          FULLTEXT (title,body)
    ->        ) ENGINE=InnoDB;
Query OK, 0 rows affected (0.17 sec)

mysql> INSERT INTO articles (title,body) VALUES
    ->        ('MySQL Tutorial','DBMS stands for DataBase ...'),
    ->        ('How To Use MySQL Well','After you went through a ...'),
    ->        ('Optimizing MySQL','In this tutorial we show ...'),
    ->        ('1001 MySQL Tricks','1. Never run mysqld as root. 2. ...'),
    ->        ('MySQL vs. YourSQL','In the following database comparison ...'),
    ->        ('MySQL Security','When configured properly, MySQL ...');
Query OK, 6 rows affected (0.01 sec)
Records: 6  Duplicates: 0  Warnings: 0
```
innodb_ft_aux_table 変数を FULLTEXT インデックスを含むテーブルの名前に設定します。 この変数が設定されていない場合、INNODB_FT_DEFAULT_STOPWORD を除き、InnoDB FULLTEXT INFORMATION_SCHEMA テーブルは空です
```sql
mysql> SET GLOBAL innodb_ft_aux_table = 'test/articles';
Query OK, 0 rows affected (0.00 sec)
```
INNODB_FT_INDEX_CACHE テーブルをクエリーします。これにより、FULLTEXT インデックス内の新しく挿入された行に関する情報が示されます。 DML 操作中の高コストのインデックス再編成を回避するために、新しく挿入された行のデータは、OPTIMIZE TABLE が実行されるまで (またはサーバーが停止するか、キャッシュ制限を超えるまで)、FULLTEXT インデックスキャッシュに残ります
```Sql
mysql> SELECT * FROM INFORMATION_SCHEMA.INNODB_FT_INDEX_CACHE LIMIT 5;
+------------+--------------+-------------+-----------+--------+----------+
| WORD       | FIRST_DOC_ID | LAST_DOC_ID | DOC_COUNT | DOC_ID | POSITION |
+------------+--------------+-------------+-----------+--------+----------+
| 1001       |            5 |           5 |         1 |      5 |        0 |
| after      |            3 |           3 |         1 |      3 |       22 |
| comparison |            6 |           6 |         1 |      6 |       44 |
| configured |            7 |           7 |         1 |      7 |       20 |
| database   |            2 |           6 |         2 |      2 |       31 |
+------------+--------------+-------------+-----------+--------+----------+
5 rows in set (0.00 sec)
```
innodb_optimize_fulltext_only システム変数を有効にし、FULLTEXT インデックスを含むテーブルで OPTIMIZE TABLE を実行します。 この操作により、FULLTEXT インデックスキャッシュの内容がメインの FULLTEXT インデックスにフラッシュされます。
```Sql
mysql> SET GLOBAL innodb_optimize_fulltext_only=ON;
Query OK, 0 rows affected (0.00 sec)

mysql> OPTIMIZE TABLE articles;
+---------------+----------+----------+----------+
| Table         | Op       | Msg_type | Msg_text |
+---------------+----------+----------+----------+
| test.articles | optimize | status   | OK       |
+---------------+----------+----------+----------+
1 row in set (0.01 sec)
```
INNODB_FT_INDEX_TABLE テーブルにクエリーして、メインの FULLTEXT インデックス内のデータに関する情報 (FULLTEXT インデックスキャッシュからフラッシュされたばかりのデータに関する情報を含む) を表示します
```Sql
mysql> SELECT * FROM INFORMATION_SCHEMA.INNODB_FT_INDEX_TABLE LIMIT 5;
+------------+--------------+-------------+-----------+--------+----------+
| WORD       | FIRST_DOC_ID | LAST_DOC_ID | DOC_COUNT | DOC_ID | POSITION |
+------------+--------------+-------------+-----------+--------+----------+
| 1001       |            5 |           5 |         1 |      5 |        0 |
| after      |            3 |           3 |         1 |      3 |       22 |
| comparison |            6 |           6 |         1 |      6 |       44 |
| configured |            7 |           7 |         1 |      7 |       20 |
| database   |            2 |           6 |         2 |      2 |       31 |
+------------+--------------+-------------+-----------+--------+----------+
5 rows in set (0.00 sec)
```
OPTIMIZE TABLE 操作によって FULLTEXT インデックスキャッシュがフラッシュされたため、INNODB_FT_INDEX_CACHE テーブルは空になっています
```Sql
mysql> SELECT * FROM INFORMATION_SCHEMA.INNODB_FT_INDEX_CACHE LIMIT 5;
Empty set (0.00 sec)
```
test/articles テーブルからいくつかのレコードを削除します
```Sql
mysql> DELETE FROM test.articles WHERE id < 4;
Query OK, 3 rows affected (0.01 sec)
```
INNODB_FT_DELETED テーブルをクエリーします。 このテーブルには、FULLTEXT インデックスから削除された行が記録されます。
```Sql
mysql> SELECT * FROM INFORMATION_SCHEMA.INNODB_FT_DELETED;
+--------+
| DOC_ID |
+--------+
|      2 |
|      3 |
|      4 |
+--------+
3 rows in set (0.00 sec)
```
OPTIMIZE TABLE を実行して、削除されたレコードを消去します
```Sql

mysql> OPTIMIZE TABLE articles;
+---------------+----------+----------+----------+
| Table         | Op       | Msg_type | Msg_text |
+---------------+----------+----------+----------+
| test.articles | optimize | status   | OK       |
+---------------+----------+----------+----------+
1 row in set (0.04 sec)
```
INNODB_FT_CONFIG テーブルをクエリーします。 このテーブルには、FULLTEXT インデックスに関するメタデータとそれに関連する処理が含まれています
```Sql
mysql> SELECT * FROM INFORMATION_SCHEMA.INNODB_FT_CONFIG;
+---------------------------+-------+
| KEY                       | VALUE |
+---------------------------+-------+
| optimize_checkpoint_limit | 180   |
| synced_doc_id             | 8     |
| stopword_table_name       |       |
| use_stopword              | 1     |
+---------------------------+-------+
4 rows in set (0.00 sec)
```
innodb_optimize_fulltext_only は一時的にのみ有効にすることを意図しているため、無効にします
```Sql
mysql> SET GLOBAL innodb_optimize_fulltext_only=OFF;
Query OK, 0 rows affected (0.00 sec)
```

### 15.15.5 InnoDB INFORMATION_SCHEMA バッファープールテーブル
InnoDB INFORMATION_SCHEMA バッファープールテーブルには、下に一覧表示されているものが含まれます
```Sql
mysql>  SHOW TABLES FROM INFORMATION_SCHEMA LIKE 'INNODB_BUFFER%';
+-----------------------------------------------+
| Tables_in_information_schema (INNODB_BUFFER%) |
+-----------------------------------------------+
| INNODB_BUFFER_PAGE                            |
| INNODB_BUFFER_PAGE_LRU                        |
| INNODB_BUFFER_POOL_STATS                      |
+-----------------------------------------------+
3 rows in set (0.00 sec)
```
 INNODB_BUFFER_PAGE テーブル内のシステムデータのクエリー

 このクエリーでは、TABLE_NAME 値が NULL であるページ、またはユーザー定義テーブルを示すスラッシュ/またはピリオド . がテーブル名に含まれるページを除外することで、システムデータを含むページの概算数が提供されます
```sql
mysql> SELECT COUNT(*) FROM INFORMATION_SCHEMA.INNODB_BUFFER_PAGE
    ->        WHERE TABLE_NAME IS NULL OR (INSTR(TABLE_NAME, '/') = 0 AND INSTR(TABLE_NAME, '.') = 0);
+----------+
| COUNT(*) |
+----------+
|     7512 |
+----------+
1 row in set (0.07 sec)
```

このクエリーは、システムデータを含むページの概数、バッファープールページの総数、およびシステムデータを含むページの概略の割合 (%) を返します
```Sql
mysql> SELECT
    ->        (SELECT COUNT(*) FROM INFORMATION_SCHEMA.INNODB_BUFFER_PAGE
    ->        WHERE TABLE_NAME IS NULL OR (INSTR(TABLE_NAME, '/') = 0 AND INSTR(TABLE_NAME, '.') = 0)
    ->        ) AS system_pages,
    ->        (
    ->        SELECT COUNT(*)
    ->        FROM INFORMATION_SCHEMA.INNODB_BUFFER_PAGE
    ->        ) AS total_pages,
    ->        (
    ->        SELECT ROUND((system_pages/total_pages) * 100)
    ->        ) AS system_page_percentage;
+--------------+-------------+------------------------+
| system_pages | total_pages | system_page_percentage |
+--------------+-------------+------------------------+
|         7512 |        8191 |                     92 |
+--------------+-------------+------------------------+
1 row in set (0.08 sec)
```
バッファープール内のシステムデータのタイプは、PAGE_TYPE 値をクエリーすることによって確認できます。 たとえば、次のクエリーは、システムデータを含むページ間の 8 つの個別の PAGE_TYPE 値を返します
```Sql
mysql> SELECT DISTINCT PAGE_TYPE FROM INFORMATION_SCHEMA.INNODB_BUFFER_PAGE
    ->        WHERE TABLE_NAME IS NULL OR (INSTR(TABLE_NAME, '/') = 0 AND INSTR(TABLE_NAME, '.') = 0);
+-------------------+
| PAGE_TYPE         |
+-------------------+
| SYSTEM            |
| INODE             |
| IBUF_INDEX        |
| TRX_SYSTEM        |
| RSEG_ARRAY        |
| UNDO_LOG          |
| FILE_SPACE_HEADER |
| IBUF_BITMAP       |
| LOB_FIRST         |
| LOB_DATA          |
| LOB_INDEX         |
| UNKNOWN           |
| SDI_INDEX         |
| INDEX             |
+-------------------+
14 rows in set (0.04 sec)
```

INNODB_BUFFER_PAGE テーブル内のユーザーデータのクエリー

このクエリーでは、TABLE_NAME 値が NOT NULL および NOT LIKE '%INNODB_TABLES%'であるページをカウントすることで、ユーザーデータを含むページの概算数が提供されます
```Sql
mysql> SELECT COUNT(*) FROM INFORMATION_SCHEMA.INNODB_BUFFER_PAGE
    ->        WHERE TABLE_NAME IS NOT NULL AND TABLE_NAME NOT LIKE '%INNODB_TABLES%';
+----------+
| COUNT(*) |
+----------+
|      723 |
+----------+
1 row in set (0.04 sec)
```
このクエリーは、ユーザーデータを含むページの概数、バッファープールページの総数、およびユーザーデータを含むページの概略の割合 (%) を返します
```Sql
mysql> SELECT
    ->        (SELECT COUNT(*) FROM INFORMATION_SCHEMA.INNODB_BUFFER_PAGE
    ->        WHERE TABLE_NAME IS NOT NULL AND (INSTR(TABLE_NAME, '/') > 0 OR INSTR(TABLE_NAME, '.') > 0)
    ->        ) AS user_pages,
    ->        (
    ->        SELECT COUNT(*)
    ->        FROM information_schema.INNODB_BUFFER_PAGE
    ->        ) AS total_pages,
    ->        (
    ->        SELECT ROUND((user_pages/total_pages) * 100)
    ->        ) AS user_page_percentage;
+------------+-------------+----------------------+
| user_pages | total_pages | user_page_percentage |
+------------+-------------+----------------------+
|        679 |        8191 |                    8 |
+------------+-------------+----------------------+
1 row in set (0.07 sec)
```
このクエリーは、バッファープール内のページを含むユーザー定義のテーブルを識別します
```Sql
mysql> SELECT DISTINCT TABLE_NAME FROM INFORMATION_SCHEMA.INNODB_BUFFER_PAGE
    ->        WHERE TABLE_NAME IS NOT NULL AND (INSTR(TABLE_NAME, '/') > 0 OR INSTR(TABLE_NAME, '.') > 0)
    ->        AND TABLE_NAME NOT LIKE '`mysql`.`innodb_%';
+----------------------------------------------------------+
| TABLE_NAME                                               |
+----------------------------------------------------------+
| `mysql`.`resource_groups`                                |
| `mysql`.`dd_properties`                                  |
| `mysql`.`schemata`                                       |
| `mysql`.`tablespace_files`                               |
| `mysql`.`tablespaces`                                    |
| `mysql`.`check_constraints`                              |
| `mysql`.`column_type_elements`                           |
| `mysql`.`columns`                                        |
| `mysql`.`foreign_key_column_usage`                       |
| `mysql`.`foreign_keys`                                   |
| `mysql`.`index_column_usage`                             |
| `mysql`.`index_partitions`                               |
| `mysql`.`indexes`                                        |
| `mysql`.`table_partition_values`                         |
| `mysql`.`table_partitions`                               |
| `mysql`.`tables`                                         |
| `mysql`.`triggers`                                       |
| `mysql`.`view_routine_usage`                             |
| `mysql`.`view_table_usage`                               |
| `mysql`.`collations`                                     |
| `mysql`.`character_sets`                                 |
| `mysql`.`plugin`                                         |
| `mysql`.`server_cost`                                    |
| `mysql`.`engine_cost`                                    |
| `mysql`.`column_statistics`                              |
| `mysql`.`parameter_type_elements`                        |
| `mysql`.`parameters`                                     |
| `mysql`.`routines`                                       |
| `mysql`.`gtid_executed`                                  |
| `mysql`.`component`                                      |
| `mysql`.`user`                                           |
| `mysql`.`db`                                             |
| `mysql`.`tables_priv`                                    |
| `mysql`.`columns_priv`                                   |
| `mysql`.`procs_priv`                                     |
| `mysql`.`proxies_priv`                                   |
| `mysql`.`role_edges`                                     |
| `mysql`.`default_roles`                                  |
| `mysql`.`global_grants`                                  |
| `mysql`.`password_history`                               |
| `mysql`.`time_zone_leap_second`                          |
| `mysql`.`time_zone_name`                                 |
| `mysql`.`time_zone`                                      |
| `mysql`.`time_zone_transition_type`                      |
| `mysql`.`time_zone_transition`                           |
| `mysql`.`servers`                                        |
| `mysql`.`func`                                           |
| `mysql`.`slave_master_info`                              |
| `mysql`.`slave_relay_log_info`                           |
| `mysql`.`slave_worker_info`                              |
| `mysql`.`events`                                         |
| `sakila`.`actor`                                         |
| `sakila`.`address`                                       |
| `sakila`.`category`                                      |
| `sakila`.`city`                                          |
| `sakila`.`country`                                       |
| `sakila`.`customer`                                      |
| `sakila`.`film`                                          |
| `sakila`.`film_actor`                                    |
| `sakila`.`film_category`                                 |
| `world`.`city`                                           |
| `sakila`.`film_text`                                     |
| `sakila`.`fts_0000000000000431_being_deleted`            |
| `sakila`.`fts_0000000000000431_being_deleted_cache`      |
| `sakila`.`fts_0000000000000431_config`                   |
| `sakila`.`fts_0000000000000431_deleted`                  |
| `sakila`.`fts_0000000000000431_deleted_cache`            |
| `sakila`.`fts_0000000000000431_00000000000000b6_index_1` |
| `sakila`.`fts_0000000000000431_00000000000000b6_index_2` |
| `sakila`.`fts_0000000000000431_00000000000000b6_index_3` |
| `sakila`.`fts_0000000000000431_00000000000000b6_index_4` |
| `sakila`.`fts_0000000000000431_00000000000000b6_index_5` |
| `sakila`.`fts_0000000000000431_00000000000000b6_index_6` |
| `sakila`.`inventory`                                     |
| `sakila`.`language`                                      |
| `sakila`.`payment`                                       |
| `sakila`.`rental`                                        |
| `sakila`.`staff`                                         |
| `sakila`.`store`                                         |
| `world`.`country`                                        |
| `world`.`countrylanguage`                                |
| `mysql`.`index_stats`                                    |
| `mysql`.`table_stats`                                    |
| `test`.`parent`                                          |
| `test`.`child`                                           |
| `mysql`.`catalogs`                                       |
| `test`.`articles`                                        |
| `test`.`fts_0000000000000449_being_deleted`              |
| `test`.`fts_0000000000000449_being_deleted_cache`        |
| `test`.`fts_0000000000000449_config`                     |
| `test`.`fts_0000000000000449_deleted`                    |
| `test`.`fts_0000000000000449_deleted_cache`              |
| `test`.`fts_0000000000000449_00000000000000e1_index_1`   |
| `test`.`fts_0000000000000449_00000000000000e1_index_2`   |
| `test`.`fts_0000000000000449_00000000000000e1_index_3`   |
| `test`.`fts_0000000000000449_00000000000000e1_index_4`   |
| `test`.`fts_0000000000000449_00000000000000e1_index_5`   |
| `test`.`fts_0000000000000449_00000000000000e1_index_6`   |
+----------------------------------------------------------+
98 rows in set (0.04 sec)
```

 INNODB_BUFFER_PAGE テーブル内のインデックスデータのクエリー

 インデックスページに関する情報を取得するには、そのインデックスの名前を使用して INDEX_NAME カラムをクエリーします。 たとえば、次のクエリーは、employees.salaries テーブルで定義されている emp_no インデックスのページの数とページの合計データサイズを返します
 ```Sql
 mysql> SELECT INDEX_NAME, COUNT(*) AS Pages,
    -> ROUND(SUM(IF(COMPRESSED_SIZE = 0, @@GLOBAL.innodb_page_size, COMPRESSED_SIZE))/1024/1024)
    -> AS 'Total Data (MB)'
    -> FROM INFORMATION_SCHEMA.INNODB_BUFFER_PAGE
    -> WHERE INDEX_NAME='emp_no' AND TABLE_NAME = '`sakila`.`staff`';
+------------+-------+-----------------+
| INDEX_NAME | Pages | Total Data (MB) |
+------------+-------+-----------------+
| NULL       |     0 |            NULL |
+------------+-------+-----------------+
1 row in set (0.04 sec)
```
このクエリーは、employees.salaries テーブルで定義されているすべてのインデックスのページの数とページの合計データサイズを返します

```sql
mysql> SELECT INDEX_NAME, COUNT(*) AS Pages,
    ->        ROUND(SUM(IF(COMPRESSED_SIZE = 0, @@GLOBAL.innodb_page_size, COMPRESSED_SIZE))/1024/1024)
    ->        AS 'Total Data (MB)'
    ->        FROM INFORMATION_SCHEMA.INNODB_BUFFER_PAGE
    ->        WHERE TABLE_NAME = '`sakila`.`staff`'
    -> GROUP BY INDEX_NAME;
+-------------------+-------+-----------------+
| INDEX_NAME        | Pages | Total Data (MB) |
+-------------------+-------+-----------------+
| PRIMARY           |     1 |               0 |
| idx_fk_store_id   |     1 |               0 |
| idx_fk_address_id |     1 |               0 |
+-------------------+-------+-----------------+
3 rows in set (0.04 sec)
```
INNODB_BUFFER_PAGE_LRU テーブル内の LRU_POSITION データのクエリー

```Sql
mysql> SELECT COUNT(LRU_POSITION) FROM INFORMATION_SCHEMA.INNODB_BUFFER_PAGE_LRU
    ->        WHERE TABLE_NAME='`sakila`.`staff`' AND LRU_POSITION < 3072;
+---------------------+
| COUNT(LRU_POSITION) |
+---------------------+
|                   3 |
+---------------------+
1 row in set (0.01 sec)
```
 INNODB_BUFFER_POOL_STATS テーブルのクエリー
 ```sql
 mysql> SELECT * FROM information_schema.INNODB_BUFFER_POOL_STATS \G
*************************** 1. row ***************************
                         POOL_ID: 0
                       POOL_SIZE: 8191
                    FREE_BUFFERS: 6133
                  DATABASE_PAGES: 2033
              OLD_DATABASE_PAGES: 730
         MODIFIED_DATABASE_PAGES: 0
              PENDING_DECOMPRESS: 0
                   PENDING_READS: 0
               PENDING_FLUSH_LRU: 0
              PENDING_FLUSH_LIST: 0
                PAGES_MADE_YOUNG: 1349
            PAGES_NOT_MADE_YOUNG: 3196
           PAGES_MADE_YOUNG_RATE: 0
       PAGES_MADE_NOT_YOUNG_RATE: 0
               NUMBER_PAGES_READ: 860
            NUMBER_PAGES_CREATED: 1177
            NUMBER_PAGES_WRITTEN: 3532
                 PAGES_READ_RATE: 0
               PAGES_CREATE_RATE: 0
              PAGES_WRITTEN_RATE: 0
                NUMBER_PAGES_GET: 557045
                        HIT_RATE: 0
    YOUNG_MAKE_PER_THOUSAND_GETS: 0
NOT_YOUNG_MAKE_PER_THOUSAND_GETS: 0
         NUMBER_PAGES_READ_AHEAD: 0
       NUMBER_READ_AHEAD_EVICTED: 0
                 READ_AHEAD_RATE: 0
         READ_AHEAD_EVICTED_RATE: 0
                    LRU_IO_TOTAL: 0
                  LRU_IO_CURRENT: 0
                UNCOMPRESS_TOTAL: 0
              UNCOMPRESS_CURRENT: 0
1 row in set (0.00 sec)
```
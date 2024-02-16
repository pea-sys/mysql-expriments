### SHOW ステートメント

### 13.7.7.1 SHOW BINARY LOGS ステートメント
```sql
mysql> SHOW BINARY LOGS;
+--------------------------+-----------+-----------+
| Log_name                 | File_size | Encrypted |
+--------------------------+-----------+-----------+
| DYNABOOK-B552-bin.001234 |       180 | No        |
| DYNABOOK-B552-bin.001235 |       180 | No        |
| DYNABOOK-B552-bin.001236 |      8731 | No        |
| DYNABOOK-B552-bin.001237 |     24183 | No        |
| DYNABOOK-B552-bin.001238 |      2684 | No        |
+--------------------------+-----------+-----------+
5 rows in set (0.03 sec)

mysql> SHOW MASTER LOGS;
+--------------------------+-----------+-----------+
| Log_name                 | File_size | Encrypted |
+--------------------------+-----------+-----------+
| DYNABOOK-B552-bin.001234 |       180 | No        |
| DYNABOOK-B552-bin.001235 |       180 | No        |
| DYNABOOK-B552-bin.001236 |      8731 | No        |
| DYNABOOK-B552-bin.001237 |     24183 | No        |
| DYNABOOK-B552-bin.001238 |      2684 | No        |
+--------------------------+-----------+-----------+
5 rows in set, 1 warning (0.00 sec)
```

### 13.7.7.3 SHOW CHARACTER SET ステートメント
```sql
mysql> SHOW CHARACTER SET LIKE 'latin%';
+---------+-----------------------------+-------------------+--------+
| Charset | Description                 | Default collation | Maxlen |
+---------+-----------------------------+-------------------+--------+
| latin1  | cp1252 West European        | latin1_swedish_ci |      1 |
| latin2  | ISO 8859-2 Central European | latin2_general_ci |      1 |
| latin5  | ISO 8859-9 Turkish          | latin5_turkish_ci |      1 |
| latin7  | ISO 8859-13 Baltic          | latin7_general_ci |      1 |
+---------+-----------------------------+-------------------+--------+
4 rows in set (0.01 sec)

mysql> SHOW COLLATION WHERE `Default` = 'Yes';
+---------------------+----------+-----+---------+----------+---------+---------------+
| Collation           | Charset  | Id  | Default | Compiled | Sortlen | Pad_attribute |
+---------------------+----------+-----+---------+----------+---------+---------------+
| armscii8_general_ci | armscii8 |  32 | Yes     | Yes      |       1 | PAD SPACE     |
| ascii_general_ci    | ascii    |  11 | Yes     | Yes      |       1 | PAD SPACE     |
| big5_chinese_ci     | big5     |   1 | Yes     | Yes      |       1 | PAD SPACE     |
| binary              | binary   |  63 | Yes     | Yes      |       1 | NO PAD        |
| cp1250_general_ci   | cp1250   |  26 | Yes     | Yes      |       1 | PAD SPACE     |
| cp1251_general_ci   | cp1251   |  51 | Yes     | Yes      |       1 | PAD SPACE     |
| cp1256_general_ci   | cp1256   |  57 | Yes     | Yes      |       1 | PAD SPACE     |
| cp1257_general_ci   | cp1257   |  59 | Yes     | Yes      |       1 | PAD SPACE     |
| cp850_general_ci    | cp850    |   4 | Yes     | Yes      |       1 | PAD SPACE     |
| cp852_general_ci    | cp852    |  40 | Yes     | Yes      |       1 | PAD SPACE     |
| cp866_general_ci    | cp866    |  36 | Yes     | Yes      |       1 | PAD SPACE     |
| cp932_japanese_ci   | cp932    |  95 | Yes     | Yes      |       1 | PAD SPACE     |
| dec8_swedish_ci     | dec8     |   3 | Yes     | Yes      |       1 | PAD SPACE     |
| eucjpms_japanese_ci | eucjpms  |  97 | Yes     | Yes      |       1 | PAD SPACE     |
| euckr_korean_ci     | euckr    |  19 | Yes     | Yes      |       1 | PAD SPACE     |
| gb18030_chinese_ci  | gb18030  | 248 | Yes     | Yes      |       2 | PAD SPACE     |
| gb2312_chinese_ci   | gb2312   |  24 | Yes     | Yes      |       1 | PAD SPACE     |
| gbk_chinese_ci      | gbk      |  28 | Yes     | Yes      |       1 | PAD SPACE     |
| geostd8_general_ci  | geostd8  |  92 | Yes     | Yes      |       1 | PAD SPACE     |
| greek_general_ci    | greek    |  25 | Yes     | Yes      |       1 | PAD SPACE     |
| hebrew_general_ci   | hebrew   |  16 | Yes     | Yes      |       1 | PAD SPACE     |
| hp8_english_ci      | hp8      |   6 | Yes     | Yes      |       1 | PAD SPACE     |
| keybcs2_general_ci  | keybcs2  |  37 | Yes     | Yes      |       1 | PAD SPACE     |
| koi8r_general_ci    | koi8r    |   7 | Yes     | Yes      |       1 | PAD SPACE     |
| koi8u_general_ci    | koi8u    |  22 | Yes     | Yes      |       1 | PAD SPACE     |
| latin1_swedish_ci   | latin1   |   8 | Yes     | Yes      |       1 | PAD SPACE     |
| latin2_general_ci   | latin2   |   9 | Yes     | Yes      |       1 | PAD SPACE     |
| latin5_turkish_ci   | latin5   |  30 | Yes     | Yes      |       1 | PAD SPACE     |
| latin7_general_ci   | latin7   |  41 | Yes     | Yes      |       1 | PAD SPACE
     |
| macce_general_ci    | macce    |  38 | Yes     | Yes      |       1 | PAD SPACE     |
| macroman_general_ci | macroman |  39 | Yes     | Yes      |       1 | PAD SPACE     |
| sjis_japanese_ci    | sjis     |  13 | Yes     | Yes      |       1 | PAD SPACE     |
| swe7_swedish_ci     | swe7     |  10 | Yes     | Yes      |       1 | PAD SPACE     |
| tis620_thai_ci      | tis620   |  18 | Yes     | Yes      |       4 | PAD SPACE     |
| ucs2_general_ci     | ucs2     |  35 | Yes     | Yes      |       1 | PAD SPACE     |
| ujis_japanese_ci    | ujis     |  12 | Yes     | Yes      |       1 | PAD SPACE     |
| utf16le_general_ci  | utf16le  |  56 | Yes     | Yes      |       1 | PAD SPACE
     |
| utf16_general_ci    | utf16    |  54 | Yes     | Yes      |       1 | PAD SPACE     |
| utf32_general_ci    | utf32    |  60 | Yes     | Yes      |       1 | PAD SPACE     |
| utf8mb3_general_ci  | utf8mb3  |  33 | Yes     | Yes      |       1 | PAD SPACE     |
| utf8mb4_0900_ai_ci  | utf8mb4  | 255 | Yes     | Yes      |       0 | NO PAD        |
+---------------------+----------+-----+---------+----------+---------+---------------+
41 rows in set (0.01 sec)
```
### 13.7.7.5 SHOW COLUMNS ステートメント
```Sql
mysql> SHOW COLUMNS FROM t;
+-------+------+------+-----+---------+-------+
| Field | Type | Null | Key | Default | Extra |
+-------+------+------+-----+---------+-------+
| a     | int  | YES  |     | NULL    |       |
| b     | int  | YES  |     | NULL    |       |
| c     | int  | YES  |     | NULL    |       |
+-------+------+------+-----+---------+-------+
3 rows in set (0.02 sec)
```
### 13.7.7.6 SHOW CREATE DATABASE ステートメント
```sql
mysql> SHOW CREATE DATABASE test\G
*************************** 1. row ***************************
       Database: test
Create Database: CREATE DATABASE `test` /*!40100 DEFAULT CHARACTER SET utf8mb3 */ /*!80016 DEFAULT ENCRYPTION='N' */
1 row in set (0.00 sec)

mysql> SHOW CREATE SCHEMA test\G
*************************** 1. row ***************************
       Database: test
Create Database: CREATE DATABASE `test` /*!40100 DEFAULT CHARACTER SET utf8mb3 */ /*!80016 DEFAULT ENCRYPTION='N' */
1 row in set (0.00 sec)
```
### 13.7.7.10 SHOW CREATE TABLE ステートメント
```sql
mysql> CREATE TABLE t1 (
    ->          i1 INT CHECK (i1 <> 0),      -- column constraint
    ->          i2 INT,
    ->          CHECK (i2 > i1),             -- table constraint
    ->          CHECK (i2 <> 0) NOT ENFORCED -- table constraint, not enforced
    ->        );
Query OK, 0 rows affected (0.02 sec)

mysql> SHOW CREATE TABLE t1\G
*************************** 1. row ***************************
       Table: t1
Create Table: CREATE TABLE `t1` (
  `i1` int DEFAULT NULL,
  `i2` int DEFAULT NULL,
  CONSTRAINT `t1_chk_1` CHECK ((`i1` <> 0)),
  CONSTRAINT `t1_chk_2` CHECK ((`i2` > `i1`)),
  CONSTRAINT `t1_chk_3` CHECK ((`i2` <> 0)) /*!80016 NOT ENFORCED */
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
1 row in set (0.00 sec)
```

### 13.7.7.12 SHOW CREATE USER ステートメント
```sql
mysql> CREATE USER 'u1'@'localhost' IDENTIFIED BY 'secret';
Query OK, 0 rows affected (0.02 sec)

mysql> SET print_identified_with_as_hex = ON;
Query OK, 0 rows affected (0.00 sec)

mysql> SHOW CREATE USER 'u1'@'localhost'\G
*************************** 1. row ***************************
CREATE USER for u1@localhost: CREATE USER `u1`@`localhost` IDENTIFIED WITH 'caching_sha2_password' AS 0x2441243030352446254A552E230B250D6B01753C76155E37591F46666D54754D314B79705147733938684C55434A4151765057796D544A314665462E66423561337A6B433043 REQUIRE NONE PASSWORD EXPIRE DEFAULT ACCOUNT UNLOCK PASSWORD HISTORY DEFAULT PASSWORD REUSE INTERVAL DEFAULT PASSWORD REQUIRE CURRENT DEFAULT
1 row in set (0.00 sec)
```
### 13.7.7.13 SHOW CREATE VIEW ステートメント
```sql
mysql> SET sql_mode = 'ANSI';
Query OK, 0 rows affected (0.00 sec)

mysql> CREATE VIEW test.v AS SELECT 'a' || 'b' as col1;
Query OK, 0 rows affected (0.01 sec)

mysql> SHOW CREATE VIEW test.v\G
*************************** 1. row ***************************
                View: v
         Create View: CREATE VIEW "test"."v" AS select concat('a','b') AS "col1"
character_set_client: cp932
collation_connection: cp932_japanese_ci
1 row in set (0.00 sec)
```
### 13.7.7.14 SHOW DATABASES ステートメント
```Sql
mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| employees          |
| information_schema |
| mydb               |
| mysql              |
| performance_schema |
| sakila             |
| sample             |
| sys                |
| test               |
| world              |
+--------------------+
10 rows in set (0.01 sec)
```
### 13.7.7.15 SHOW ENGINE ステートメント
```Sql
mysql> SHOW ENGINE INNODB STATUS\G
*************************** 1. row ***************************
  Type: InnoDB
  Name:
Status:
=====================================
2023-12-28 11:25:51 0x33c8 INNODB MONITOR OUTPUT
=====================================
Per second averages calculated from the last 38 seconds
-----------------
BACKGROUND THREAD
-----------------
srv_master_thread loops: 18 srv_active, 0 srv_shutdown, 16776 srv_idle
srv_master_thread log flush and writes: 0
----------
SEMAPHORES
----------
OS WAIT ARRAY INFO: reservation count 838
OS WAIT ARRAY INFO: signal count 819
RW-shared spins 0, rounds 0, OS waits 0
RW-excl spins 0, rounds 0, OS waits 0
RW-sx spins 0, rounds 0, OS waits 0
Spin rounds per wait: 0.00 RW-shared, 0.00 RW-excl, 0.00 RW-sx
------------
TRANSACTIONS
------------
Trx id counter 16271
Purge done for trx's n:o < 16268 undo n:o < 0 state: running but idle
History list length 0
LIST OF TRANSACTIONS FOR EACH SESSION:
---TRANSACTION 283834507403112, not started
0 lock struct(s), heap size 1128, 0 row lock(s)
---TRANSACTION 283834507402336, not started
0 lock struct(s), heap size 1128, 0 row lock(s)
---TRANSACTION 283834507401560, not started
0 lock struct(s), heap size 1128, 0 row lock(s)
---TRANSACTION 283834507400784, not started
0 lock struct(s), heap size 1128, 0 row lock(s)
---TRANSACTION 283834507400008, not started
0 lock struct(s), heap size 1128, 0 row lock(s)
---TRANSACTION 283834507399232, not started
0 lock struct(s), heap size 1128, 0 row lock(s)
---TRANSACTION 283834507398456, not started
0 lock struct(s), heap size 1128, 0 row lock(s)
---TRANSACTION 283834507397680, not started
0 lock struct(s), heap size 1128, 0 row lock(s)
--------
FILE I/O
--------
I/O thread 0 state: wait Windows aio ((null))
I/O thread 1 state: wait Windows aio (insert buffer thread)
I/O thread 2 state: wait Windows aio (read thread)
I/O thread 3 state: wait Windows aio (read thread)
I/O thread 4 state: wait Windows aio (read thread)
I/O thread 5 state: wait Windows aio (read thread)
I/O thread 6 state: wait Windows aio (write thread)
I/O thread 7 state: wait Windows aio (write thread)
I/O thread 8 state: wait Windows aio (write thread)
Pending normal aio reads: [0, 0, 0, 0] , aio writes: [0, 0, 0, 0] ,
 ibuf aio reads:
Pending flushes (fsync) log: 0; buffer pool: 0
1428 OS file reads, 1241 OS file writes, 547 OS fsyncs
0.00 reads/s, 0 avg bytes/read, 0.00 writes/s, 0.00 fsyncs/s
-------------------------------------
INSERT BUFFER AND ADAPTIVE HASH INDEX
-------------------------------------
Ibuf: size 1, free list len 0, seg size 2, 0 merges
merged operations:
 insert 0, delete mark 0, delete 0
discarded operations:
 insert 0, delete mark 0, delete 0
Hash table size 34679, node heap has 0 buffer(s)
Hash table size 34679, node heap has 0 buffer(s)
Hash table size 34679, node heap has 0 buffer(s)
Hash table size 34679, node heap has 2 buffer(s)
Hash table size 34679, node heap has 0 buffer(s)
Hash table size 34679, node heap has 0 buffer(s)
Hash table size 34679, node heap has 0 buffer(s)
Hash table size 34679, node heap has 1 buffer(s)
0.00 hash searches/s, 0.00 non-hash searches/s
---
LOG
---
Log sequence number          389519570
Log buffer assigned up to    389519570
Log buffer completed up to   389519570
Log written up to            389519570
Log flushed up to            389519570
Added dirty pages up to      389519570
Pages flushed up to          389519570
Last checkpoint at           389519570
Log minimum file id is       118
Log maximum file id is       118
216 log i/o's done, 0.00 log i/o's/second
----------------------
BUFFER POOL AND MEMORY
----------------------
Total large memory allocated 0
Dictionary memory allocated 580843
Buffer pool size   8191
Free buffers       6630
Database pages     1558
Old database pages 593
Modified db pages  0
Pending reads      0
Pending writes: LRU 0, flush list 0, single page 0
Pages made young 0, not young 0
0.00 youngs/s, 0.00 non-youngs/s
Pages read 1399, created 159, written 663
0.00 reads/s, 0.00 creates/s, 0.00 writes/s
No buffer pool page gets since the last printout
Pages read ahead 0.00/s, evicted without access 0.00/s, Random read ahead 0.00/s
LRU len: 1558, unzip_LRU len: 0
I/O sum[0]:cur[0], unzip sum[0]:cur[0]
--------------
ROW OPERATIONS
--------------
0 queries inside InnoDB, 0 queries in queue
0 read views open inside InnoDB
Process ID=4608, Main thread ID=5584 , state=sleeping
Number of rows inserted 0, updated 0, deleted 0, read 0
0.00 inserts/s, 0.00 updates/s, 0.00 deletes/s, 0.00 reads/s
Number of system rows inserted 72, updated 374, deleted 53, read 6708
0.00 inserts/s, 0.00 updates/s, 0.00 deletes/s, 0.00 reads/s
----------------------------
END OF INNODB MONITOR OUTPUT
============================

1 row in set (0.00 sec)
```
```sql
mysql> SHOW ENGINE INNODB MUTEX;
+--------+-------------------------+---------+
| Type   | Name                    | Status  |
+--------+-------------------------+---------+
| InnoDB | rwlock: fil0fil.cc:3280 | waits=5 |
+--------+-------------------------+---------+
1 row in set (0.00 sec)
```
### 13.7.7.16 SHOW ENGINES ステートメント
```sql
mysql> SHOW ENGINES\G
*************************** 1. row ***************************
      Engine: MEMORY
     Support: YES
     Comment: Hash based, stored in memory, useful for temporary tables
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 2. row ***************************
      Engine: MRG_MYISAM
     Support: YES
     Comment: Collection of identical MyISAM tables
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 3. row ***************************
      Engine: CSV
     Support: YES
     Comment: CSV storage engine
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 4. row ***************************
      Engine: FEDERATED
     Support: NO
     Comment: Federated MySQL storage engine
Transactions: NULL
          XA: NULL
  Savepoints: NULL
*************************** 5. row ***************************
      Engine: PERFORMANCE_SCHEMA
     Support: YES
     Comment: Performance Schema
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 6. row ***************************
      Engine: MyISAM
     Support: YES
     Comment: MyISAM storage engine
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 7. row ***************************
      Engine: InnoDB
     Support: DEFAULT
     Comment: Supports transactions, row-level locking, and foreign keys
Transactions: YES
          XA: YES
  Savepoints: YES
*************************** 8. row ***************************
      Engine: ndbinfo
     Support: NO
     Comment: MySQL Cluster system information storage engine
Transactions: NULL
          XA: NULL
  Savepoints: NULL
*************************** 9. row ***************************
      Engine: BLACKHOLE
     Support: YES
     Comment: /dev/null storage engine (anything you write to it disappears)
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 10. row ***************************
      Engine: ARCHIVE
     Support: YES
     Comment: Archive storage engine
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 11. row ***************************
      Engine: ndbcluster
     Support: NO
     Comment: Clustered, fault-tolerant tables
Transactions: NULL
          XA: NULL
  Savepoints: NULL
11 rows in set (0.00 sec)
```
### 13.7.7.17 SHOW ERRORS ステートメント
```Sql
mysql> SHOW COUNT(*) ERRORS;
+-----------------------+
| @@session.error_count |
+-----------------------+
|                     1 |
+-----------------------+
1 row in set (0.00 sec)

mysql> SELECT @@error_count;
+---------------+
| @@error_count |
+---------------+
|             1 |
+---------------+
1 row in set (0.00 sec)
```
### 13.7.7.18 SHOW EVENTS ステートメント
```Sql
mysql>  SHOW EVENTS\G
Empty set (0.00 sec)
```
### 13.7.7.21 SHOW GRANTS ステートメント
```sql
mysql> SHOW GRANTS FOR CURRENT_USER()\G
*************************** 1. row ***************************
Grants for root@localhost: GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, SHUTDOWN, PROCESS, FILE, REFERENCES, INDEX, ALTER, SHOW DATABASES, SUPER, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER, CREATE TABLESPACE, CREATE ROLE, DROP ROLE ON *.* TO "root"@"localhost" WITH GRANT OPTION
*************************** 2. row ***************************
Grants for root@localhost: GRANT ALLOW_NONEXISTENT_DEFINER,APPLICATION_PASSWORD_ADMIN,AUDIT_ABORT_EXEMPT,AUDIT_ADMIN,AUTHENTICATION_POLICY_ADMIN,BACKUP_ADMIN,BINLOG_ADMIN,BINLOG_ENCRYPTION_ADMIN,CLONE_ADMIN,CONNECTION_ADMIN,ENCRYPTION_KEY_ADMIN,FIREWALL_EXEMPT,FLUSH_OPTIMIZER_COSTS,FLUSH_STATUS,FLUSH_TABLES,FLUSH_USER_RESOURCES,GROUP_REPLICATION_ADMIN,GROUP_REPLICATION_STREAM,INNODB_REDO_LOG_ARCHIVE,INNODB_REDO_LOG_ENABLE,PASSWORDLESS_USER_ADMIN,PERSIST_RO_VARIABLES_ADMIN,REPLICATION_APPLIER,REPLICATION_SLAVE_ADMIN,RESOURCE_GROUP_ADMIN,RESOURCE_GROUP_USER,ROLE_ADMIN,SENSITIVE_VARIABLES_OBSERVER,SERVICE_CONNECTION_ADMIN,SESSION_VARIABLES_ADMIN,SET_ANY_DEFINER,SHOW_ROUTINE,SYSTEM_USER,SYSTEM_VARIABLES_ADMIN,TABLE_ENCRYPTION_ADMIN,TELEMETRY_LOG_ADMIN,XA_RECOVER_ADMIN ON *.* TO "root"@"localhost" WITH GRANT OPTION
*************************** 3. row ***************************
Grants for root@localhost: GRANT PROXY ON "jeffrey"@"localhost" TO "root"@"localhost"
*************************** 4. row ***************************
Grants for root@localhost: GRANT PROXY ON ""@"" TO "root"@"localhost" WITH GRANT OPTION
4 rows in set (0.00 sec)
```

```Sql
mysql> CREATE USER u1;
Query OK, 0 rows affected (0.01 sec)

mysql> GRANT SELECT, INSERT, DELETE ON *.* TO u1;
Query OK, 0 rows affected (0.01 sec)

mysql> REVOKE SELECT, INSERT ON mysql.* FROM u1;
Query OK, 0 rows affected (0.00 sec)

mysql> REVOKE DELETE ON world.* FROM u1;
Query OK, 0 rows affected (0.00 sec)

mysql> SHOW GRANTS FOR u1;
+--------------------------------------------------+
| Grants for u1@%                                  |
+--------------------------------------------------+
| GRANT SELECT, INSERT, DELETE ON *.* TO "u1"@"%"  |
| REVOKE SELECT, INSERT ON "mysql".* FROM "u1"@"%" |
| REVOKE DELETE ON "world".* FROM "u1"@"%"         |
+--------------------------------------------------+
3 rows in set (0.00 sec)
```
### 13.7.7.22 SHOW INDEX ステートメント
```Sql
mysql> SHOW INDEX FROM City\G
*************************** 1. row ***************************
        Table: city
   Non_unique: 0
     Key_name: PRIMARY
 Seq_in_index: 1
  Column_name: city_id
    Collation: A
  Cardinality: 600
     Sub_part: NULL
       Packed: NULL
         Null:
   Index_type: BTREE
      Comment:
Index_comment:
      Visible: YES
   Expression: NULL
*************************** 2. row ***************************
        Table: city
   Non_unique: 1
     Key_name: idx_fk_country_id
 Seq_in_index: 1
  Column_name: country_id
    Collation: A
  Cardinality: 109
     Sub_part: NULL
       Packed: NULL
         Null:
   Index_type: BTREE
      Comment:
Index_comment:
      Visible: YES
   Expression: NULL
2 rows in set (0.00 sec)
```
### 13.7.7.23 SHOW MASTER STATUS ステートメント
```Sql
mysql> SHOW MASTER STATUS\G
*************************** 1. row ***************************
             File: DYNABOOK-B552-bin.001238
         Position: 5030
     Binlog_Do_DB:
 Binlog_Ignore_DB:
Executed_Gtid_Set:
1 row in set, 1 warning (0.00 sec)
```
### 13.7.7.25 SHOW PLUGINS ステートメント
```Sql
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
### 13.7.7.26 SHOW PRIVILEGES ステートメント
```sql
mysql> SHOW PRIVILEGES\G
*************************** 1. row ***************************
Privilege: Alter
  Context: Tables
  Comment: To alter the table
*************************** 2. row ***************************
Privilege: Alter routine
  Context: Functions,Procedures
  Comment: To alter or drop stored functions/procedures
*************************** 3. row ***************************
Privilege: Create
  Context: Databases,Tables,Indexes
  Comment: To create new databases and tables
*************************** 4. row ***************************
Privilege: Create routine
  Context: Databases
  Comment: To use CREATE FUNCTION/PROCEDURE
*************************** 5. row ***************************
Privilege: Create role
  Context: Server Admin
  Comment: To create new roles
*************************** 6. row ***************************
Privilege: Create temporary tables
  Context: Databases
  Comment: To use CREATE TEMPORARY TABLE
*************************** 7. row ***************************
Privilege: Create view
  Context: Tables
  Comment: To create new views
*************************** 8. row ***************************
Privilege: Create user
  Context: Server Admin
  Comment: To create new users
*************************** 9. row ***************************
Privilege: Delete
  Context: Tables
  Comment: To delete existing rows
*************************** 10. row ***************************
Privilege: Drop
  Context: Databases,Tables
  Comment: To drop databases, tables, and views
*************************** 11. row ***************************
Privilege: Drop role
  Context: Server Admin
  Comment: To drop roles
*************************** 12. row ***************************
Privilege: Event
  Context: Server Admin
  Comment: To create, alter, drop and execute events
*************************** 13. row ***************************
Privilege: Execute
  Context: Functions,Procedures
  Comment: To execute stored routines
*************************** 14. row ***************************
Privilege: File
  Context: File access on server
  Comment: To read and write files on the server
*************************** 15. row ***************************
Privilege: Grant option
  Context: Databases,Tables,Functions,Procedures
  Comment: To give to other users those privileges you possess
*************************** 16. row ***************************
Privilege: Index
  Context: Tables
  Comment: To create or drop indexes
*************************** 17. row ***************************
Privilege: Insert
  Context: Tables
  Comment: To insert data into tables
*************************** 18. row ***************************
Privilege: Lock tables
  Context: Databases
  Comment: To use LOCK TABLES (together with SELECT privilege)
*************************** 19. row ***************************
Privilege: Process
  Context: Server Admin
  Comment: To view the plain text of currently executing queries
*************************** 20. row ***************************
Privilege: Proxy
  Context: Server Admin
  Comment: To make proxy user possible
*************************** 21. row ***************************
Privilege: References
  Context: Databases,Tables
  Comment: To have references on tables
*************************** 22. row ***************************
Privilege: Reload
  Context: Server Admin
  Comment: To reload or refresh tables, logs and privileges
*************************** 23. row ***************************
Privilege: Replication client
  Context: Server Admin
  Comment: To ask where the slave or master servers are
*************************** 24. row ***************************
Privilege: Replication slave
  Context: Server Admin
  Comment: To read binary log events from the master
*************************** 25. row ***************************
Privilege: Select
  Context: Tables
  Comment: To retrieve rows from table
*************************** 26. row ***************************
Privilege: Show databases
  Context: Server Admin
  Comment: To see all databases with SHOW DATABASES
*************************** 27. row ***************************
Privilege: Show view
  Context: Tables
  Comment: To see views with SHOW CREATE VIEW
*************************** 28. row ***************************
Privilege: Shutdown
  Context: Server Admin
  Comment: To shut down the server
*************************** 29. row ***************************
Privilege: Super
  Context: Server Admin
  Comment: To use KILL thread, SET GLOBAL, CHANGE MASTER, etc.
*************************** 30. row ***************************
Privilege: Trigger
  Context: Tables
  Comment: To use triggers
*************************** 31. row ***************************
Privilege: Create tablespace
  Context: Server Admin
  Comment: To create/alter/drop tablespaces
*************************** 32. row ***************************
Privilege: Update
  Context: Tables
  Comment: To update existing rows
*************************** 33. row ***************************
Privilege: Usage
  Context: Server Admin
  Comment: No privileges - allow connect only
*************************** 34. row ***************************
Privilege: INNODB_REDO_LOG_ARCHIVE
  Context: Server Admin
  Comment:
*************************** 35. row ***************************
Privilege: ENCRYPTION_KEY_ADMIN
  Context: Server Admin
  Comment:
*************************** 36. row ***************************
Privilege: CONNECTION_ADMIN
  Context: Server Admin
  Comment:
*************************** 37. row ***************************
Privilege: TABLE_ENCRYPTION_ADMIN
  Context: Server Admin
  Comment:
*************************** 38. row ***************************
Privilege: ROLE_ADMIN
  Context: Server Admin
  Comment:
*************************** 39. row ***************************
Privilege: REPLICATION_SLAVE_ADMIN
  Context: Server Admin
  Comment:
*************************** 40. row ***************************
Privilege: SYSTEM_VARIABLES_ADMIN
  Context: Server Admin
  Comment:
*************************** 41. row ***************************
Privilege: BINLOG_ADMIN
  Context: Server Admin
  Comment:
*************************** 42. row ***************************
Privilege: AUDIT_ABORT_EXEMPT
  Context: Server Admin
  Comment:
*************************** 43. row ***************************
Privilege: SERVICE_CONNECTION_ADMIN
  Context: Server Admin
  Comment:
*************************** 44. row ***************************
Privilege: SET_USER_ID
  Context: Server Admin
  Comment:
*************************** 45. row ***************************
Privilege: GROUP_REPLICATION_ADMIN
  Context: Server Admin
  Comment:
*************************** 46. row ***************************
Privilege: PASSWORDLESS_USER_ADMIN
  Context: Server Admin
  Comment:
*************************** 47. row ***************************
Privilege: XA_RECOVER_ADMIN
  Context: Server Admin
  Comment:
*************************** 48. row ***************************
Privilege: PERSIST_RO_VARIABLES_ADMIN
  Context: Server Admin
  Comment:
*************************** 49. row ***************************
Privilege: SHOW_ROUTINE
  Context: Server Admin
  Comment:
*************************** 50. row ***************************
Privilege: BACKUP_ADMIN
  Context: Server Admin
  Comment:
*************************** 51. row ***************************
Privilege: GROUP_REPLICATION_STREAM
  Context: Server Admin
  Comment:
*************************** 52. row ***************************
Privilege: CLONE_ADMIN
  Context: Server Admin
  Comment:
*************************** 53. row ***************************
Privilege: SESSION_VARIABLES_ADMIN
  Context: Server Admin
  Comment:
*************************** 54. row ***************************
Privilege: RESOURCE_GROUP_ADMIN
  Context: Server Admin
  Comment:
*************************** 55. row ***************************
Privilege: FIREWALL_EXEMPT
  Context: Server Admin
  Comment:
*************************** 56. row ***************************
Privilege: RESOURCE_GROUP_USER
  Context: Server Admin
  Comment:
*************************** 57. row ***************************
Privilege: BINLOG_ENCRYPTION_ADMIN
  Context: Server Admin
  Comment:
*************************** 58. row ***************************
Privilege: APPLICATION_PASSWORD_ADMIN
  Context: Server Admin
  Comment:
*************************** 59. row ***************************
Privilege: SYSTEM_USER
  Context: Server Admin
  Comment:
*************************** 60. row ***************************
Privilege: FLUSH_OPTIMIZER_COSTS
  Context: Server Admin
  Comment:
*************************** 61. row ***************************
Privilege: AUDIT_ADMIN
  Context: Server Admin
  Comment:
*************************** 62. row ***************************
Privilege: TELEMETRY_LOG_ADMIN
  Context: Server Admin
  Comment:
*************************** 63. row ***************************
Privilege: INNODB_REDO_LOG_ENABLE
  Context: Server Admin
  Comment:
*************************** 64. row ***************************
Privilege: REPLICATION_APPLIER
  Context: Server Admin
  Comment:
*************************** 65. row ***************************
Privilege: FLUSH_STATUS
  Context: Server Admin
  Comment:
*************************** 66. row ***************************
Privilege: FLUSH_USER_RESOURCES
  Context: Server Admin
  Comment:
*************************** 67. row ***************************
Privilege: FLUSH_TABLES
  Context: Server Admin
  Comment:
*************************** 68. row ***************************
Privilege: AUTHENTICATION_POLICY_ADMIN
  Context: Server Admin
  Comment:
*************************** 69. row ***************************
Privilege: SENSITIVE_VARIABLES_OBSERVER
  Context: Server Admin
  Comment:
*************************** 70. row ***************************
Privilege: SET_ANY_DEFINER
  Context: Server Admin
  Comment:
*************************** 71. row ***************************
Privilege: ALLOW_NONEXISTENT_DEFINER
  Context: Server Admin
  Comment:
71 rows in set (0.00 sec)
```
### 13.7.7.29 SHOW PROCESSLIST ステートメント
```Sql
mysql> SHOW FULL PROCESSLIST\G
*************************** 1. row ***************************
     Id: 5
   User: event_scheduler
   Host: localhost
     db: NULL
Command: Daemon
   Time: 25273
  State: Waiting on empty queue
   Info: NULL
*************************** 2. row ***************************
     Id: 9
   User: system user
   Host:
     db: NULL
Command: Query
   Time: 25272
  State: Replica has read all relay log; waiting for more updates
   Info: NULL
*************************** 3. row ***************************
     Id: 10
   User: system user
   Host:
     db: NULL
Command: Connect
   Time: 25273
  State: Waiting for an event from Coordinator
   Info: NULL
*************************** 4. row ***************************
     Id: 11
   User: system user
   Host:
     db: NULL
Command: Connect
   Time: 25273
  State: Waiting for an event from Coordinator
   Info: NULL
*************************** 5. row ***************************
     Id: 12
   User: system user
   Host:
     db: NULL
Command: Connect
   Time: 25272
  State: Waiting for an event from Coordinator
   Info: NULL
*************************** 6. row ***************************
     Id: 13
   User: system user
   Host:
     db: NULL
Command: Connect
   Time: 25272
  State: Waiting for an event from Coordinator
   Info: NULL
*************************** 7. row ***************************
     Id: 14
   User: root
   Host: localhost
     db: sakila
Command: Query
   Time: 0
  State: init
   Info: SHOW FULL PROCESSLIST
7 rows in set, 1 warning (0.00 sec)
```
### 13.7.7.30 SHOW PROFILE ステートメント
```sql
mysql> SET profiling = 1;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql> DROP TABLE IF EXISTS t1;
ERROR 1046 (3D000): No database selected
mysql> use sakila;
Database changed
mysql> DROP TABLE IF EXISTS t1;
Query OK, 0 rows affected (0.03 sec)

mysql> CREATE TABLE T1 (id INT);
Query OK, 0 rows affected (0.03 sec)

mysql> SHOW PROFILES;
+----------+------------+--------------------------+
| Query_ID | Duration   | Query                    |
+----------+------------+--------------------------+
|        1 | 0.00119800 | DROP TABLE IF EXISTS t1  |
|        2 | 0.00213925 | SELECT DATABASE()        |
|        3 | 0.03359950 | DROP TABLE IF EXISTS t1  |
|        4 | 0.02855675 | CREATE TABLE T1 (id INT) |
+----------+------------+--------------------------+
4 rows in set, 1 warning (0.00 sec)

mysql> SHOW PROFILE;
+--------------------------------+----------+
| Status                         | Duration |
+--------------------------------+----------+
| starting                       | 0.000111 |
| Executing hook on transaction  | 0.000009 |
| starting                       | 0.000027 |
| checking permissions           | 0.000013 |
| Opening tables                 | 0.000695 |
| creating table                 | 0.022531 |
| After create                   | 0.000385 |
| waiting for handler commit     | 0.000011 |
| waiting for handler commit     | 0.004691 |
| query end                      | 0.000017 |
| closing tables                 | 0.000004 |
| waiting for handler commit     | 0.000015 |
| freeing items                  | 0.000018 |
| cleaning up                    | 0.000031 |
+--------------------------------+----------+
14 rows in set, 1 warning (0.01 sec)

mysql> SHOW PROFILE FOR QUERY 1;
+---------------+----------+
| Status        | Duration |
+---------------+----------+
| starting      | 0.001168 |
| freeing items | 0.000020 |
| cleaning up   | 0.000011 |
+---------------+----------+
3 rows in set, 1 warning (0.00 sec)

mysql> SHOW PROFILE CPU FOR QUERY 2;
+----------------------+----------+----------+------------+
| Status               | Duration | CPU_user | CPU_system |
+----------------------+----------+----------+------------+
| starting             | 0.001178 | 0.000000 |   0.000000 |
| checking permissions | 0.000008 | 0.000000 |   0.000000 |
| Opening tables       | 0.000017 | 0.000000 |   0.000000 |
| init                 | 0.000006 | 0.000000 |   0.000000 |
| optimizing           | 0.000011 | 0.000000 |   0.000000 |
| executing            | 0.000508 | 0.000000 |   0.000000 |
| end                  | 0.000007 | 0.000000 |   0.000000 |
| query end            | 0.000009 | 0.000000 |   0.000000 |
| closing tables       | 0.000005 | 0.000000 |   0.000000 |
| freeing items        | 0.000367 | 0.000000 |   0.000000 |
| cleaning up          | 0.000024 | 0.000000 |   0.000000 |
+----------------------+----------+----------+------------+
11 rows in set, 1 warning (0.00 sec)

mysql> SET profiling = 0;
Query OK, 0 rows affected, 1 warning (0.00 sec)
```

### 13.7.7.33 SHOW REPLICAS | SHOW SLAVE HOSTS ステートメント
ソースに現在登録されているレプリカのリストを表示します。
```sql
mysql> SHOW REPLICAS;
Empty set (0.00 sec)
```

### 13.7.7.35 SHOW REPLICA | SLAVE STATUS ステートメント
```sql
mysql> SHOW REPLICA STATUS\G
*************************** 1. row ***************************
             Replica_IO_State:
                  Source_Host: source2.example.com
                  Source_User: replication
                  Source_Port: 3306
                Connect_Retry: 10
              Source_Log_File: source2-bin.001
          Read_Source_Log_Pos: 4
               Relay_Log_File: dynabook-B552-relay-bin.000001
                Relay_Log_Pos: 4
        Relay_Source_Log_File: source2-bin.001
           Replica_IO_Running: No
          Replica_SQL_Running: Yes
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Source_Log_Pos: 4
              Relay_Log_Space: 1057
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Source_SSL_Allowed: No
           Source_SSL_CA_File:
           Source_SSL_CA_Path:
              Source_SSL_Cert:
            Source_SSL_Cipher:
               Source_SSL_Key:
        Seconds_Behind_Source: NULL
Source_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 2005
                Last_IO_Error: Error connecting to source 'replication@source2.example.com:3306'. This was attempt 10/10, with a delay of 10 seconds between attempts. Message: Unknown MySQL server host 'source2.example.com' (11001)
               Last_SQL_Errno: 0
               Last_SQL_Error:
  Replicate_Ignore_Server_Ids:
             Source_Server_Id: 0
                  Source_UUID:
             Source_Info_File: mysql.slave_master_info
                    SQL_Delay: 0
          SQL_Remaining_Delay: NULL
    Replica_SQL_Running_State: Replica has read all relay log; waiting for more updates
           Source_Retry_Count: 10
                  Source_Bind:
      Last_IO_Error_Timestamp: 231228 15:16:12
     Last_SQL_Error_Timestamp:
               Source_SSL_Crl:
           Source_SSL_Crlpath:
           Retrieved_Gtid_Set:
            Executed_Gtid_Set:
                Auto_Position: 0
         Replicate_Rewrite_DB:
                 Channel_Name:
           Source_TLS_Version:
       Source_public_key_path:
        Get_Source_public_key: 0
            Network_Namespace:
1 row in set (0.00 sec)
```
### 13.7.7.37 SHOW STATUS ステートメント
```sql
mysql> SHOW STATUS LIKE 'Key%';
+------------------------+-------+
| Variable_name          | Value |
+------------------------+-------+
| Key_blocks_not_flushed | 0     |
| Key_blocks_unused      | 6698  |
| Key_blocks_used        | 0     |
| Key_read_requests      | 0     |
| Key_reads              | 0     |
| Key_write_requests     | 0     |
| Key_writes             | 0     |
+------------------------+-------+
7 rows in set (0.01 sec)
```
### 13.7.7.38 SHOW TABLE STATUS ステートメント
```Sql
mysql> SHOW TABLE STATUS\G
*************************** 1. row ***************************
           Name: actor
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 200
 Avg_row_length: 81
    Data_length: 16384
Max_data_length: 0
   Index_length: 16384
      Data_free: 0
 Auto_increment: 200
    Create_time: 2023-12-17 14:19:00
    Update_time: NULL
     Check_time: NULL
      Collation: utf8mb4_0900_ai_ci
       Checksum: NULL
 Create_options:
        Comment:
*************************** 2. row ***************************
           Name: actor_info
         Engine: NULL
        Version: NULL
     Row_format: NULL
           Rows: NULL
 Avg_row_length: NULL
    Data_length: NULL
Max_data_length: NULL
   Index_length: NULL
      Data_free: NULL
 Auto_increment: NULL
    Create_time: 2023-12-17 14:19:02
    Update_time: NULL
     Check_time: NULL
      Collation: NULL
       Checksum: NULL
 Create_options: NULL
        Comment: VIEW
*************************** 3. row ***************************
           Name: address
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 603
 Avg_row_length: 163
    Data_length: 98304
Max_data_length: 0
   Index_length: 16384
      Data_free: 0
 Auto_increment: 605
    Create_time: 2023-12-17 14:19:00
    Update_time: NULL
     Check_time: NULL
      Collation: utf8mb4_0900_ai_ci
       Checksum: NULL
 Create_options:
        Comment:
*************************** 4. row ***************************
           Name: category
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 16
 Avg_row_length: 1024
    Data_length: 16384
Max_data_length: 0
   Index_length: 0
      Data_free: 0
 Auto_increment: 16
    Create_time: 2023-12-17 14:19:00
    Update_time: NULL
     Check_time: NULL
      Collation: utf8mb4_0900_ai_ci
       Checksum: NULL
 Create_options:
        Comment:
*************************** 5. row ***************************
           Name: city
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 600
 Avg_row_length: 81
    Data_length: 49152
Max_data_length: 0
   Index_length: 16384
      Data_free: 0
 Auto_increment: 600
    Create_time: 2023-12-17 14:19:00
    Update_time: NULL
     Check_time: NULL
      Collation: utf8mb4_0900_ai_ci
       Checksum: NULL
 Create_options:
        Comment:
*************************** 6. row ***************************
           Name: country
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 109
 Avg_row_length: 150
    Data_length: 16384
Max_data_length: 0
   Index_length: 0
      Data_free: 0
 Auto_increment: 109
    Create_time: 2023-12-17 14:19:00
    Update_time: NULL
     Check_time: NULL
      Collation: utf8mb4_0900_ai_ci
       Checksum: NULL
 Create_options:
        Comment:
*************************** 7. row ***************************
           Name: customer
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 599
 Avg_row_length: 136
    Data_length: 81920
Max_data_length: 0
   Index_length: 49152
      Data_free: 0
 Auto_increment: 599
    Create_time: 2023-12-17 14:19:00
    Update_time: NULL
     Check_time: NULL
      Collation: utf8mb4_0900_ai_ci
       Checksum: NULL
 Create_options:
        Comment:
*************************** 8. row ***************************
           Name: customer_list
         Engine: NULL
        Version: NULL
     Row_format: NULL
           Rows: NULL
 Avg_row_length: NULL
    Data_length: NULL
Max_data_length: NULL
   Index_length: NULL
      Data_free: NULL
 Auto_increment: NULL
    Create_time: 2023-12-17 14:19:02
    Update_time: NULL
     Check_time: NULL
      Collation: NULL
       Checksum: NULL
 Create_options: NULL
        Comment: VIEW
*************************** 9. row ***************************
           Name: employees
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 7
 Avg_row_length: 2340
    Data_length: 16384
Max_data_length: 0
   Index_length: 16384
      Data_free: 0
 Auto_increment: NULL
    Create_time: 2023-12-25 20:03:56
    Update_time: NULL
     Check_time: NULL
      Collation: utf8mb4_0900_ai_ci
       Checksum: NULL
 Create_options:
        Comment:
*************************** 10. row ***************************
           Name: film
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 1000
 Avg_row_length: 196
    Data_length: 196608
Max_data_length: 0
   Index_length: 81920
      Data_free: 0
 Auto_increment: 1000
    Create_time: 2023-12-17 14:19:00
    Update_time: NULL
     Check_time: NULL
      Collation: utf8mb4_0900_ai_ci
       Checksum: NULL
 Create_options:
        Comment:
*************************** 11. row ***************************
           Name: film_actor
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 5462
 Avg_row_length: 35
    Data_length: 196608
Max_data_length: 0
   Index_length: 81920
      Data_free: 0
 Auto_increment: NULL
    Create_time: 2023-12-17 14:19:00
    Update_time: NULL
     Check_time: NULL
      Collation: utf8mb4_0900_ai_ci
       Checksum: NULL
 Create_options:
        Comment:
*************************** 12. row ***************************
           Name: film_category
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 1000
 Avg_row_length: 65
    Data_length: 65536
Max_data_length: 0
   Index_length: 16384
      Data_free: 0
 Auto_increment: NULL
    Create_time: 2023-12-17 14:19:00
    Update_time: NULL
     Check_time: NULL
      Collation: utf8mb4_0900_ai_ci
       Checksum: NULL
 Create_options:
        Comment:
*************************** 13. row ***************************
           Name: film_list
         Engine: NULL
        Version: NULL
     Row_format: NULL
           Rows: NULL
 Avg_row_length: NULL
    Data_length: NULL
Max_data_length: NULL
   Index_length: NULL
      Data_free: NULL
 Auto_increment: NULL
    Create_time: 2023-12-17 14:19:02
    Update_time: NULL
     Check_time: NULL
      Collation: NULL
       Checksum: NULL
 Create_options: NULL
        Comment: VIEW
*************************** 14. row ***************************
           Name: film_text
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 1000
 Avg_row_length: 180
    Data_length: 180224
Max_data_length: 0
   Index_length: 16384
      Data_free: 0
 Auto_increment: NULL
    Create_time: 2023-12-17 14:19:01
    Update_time: NULL
     Check_time: NULL
      Collation: utf8mb4_0900_ai_ci
       Checksum: NULL
 Create_options:
        Comment:
*************************** 15. row ***************************
           Name: geo
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 0
 Avg_row_length: 0
    Data_length: 16384
Max_data_length: 0
   Index_length: 0
      Data_free: 0
 Auto_increment: NULL
    Create_time: 2023-12-20 07:43:46
    Update_time: NULL
     Check_time: NULL
      Collation: utf8mb4_0900_ai_ci
       Checksum: NULL
 Create_options:
        Comment:
*************************** 16. row ***************************
           Name: inventory
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 4581
 Avg_row_length: 39
    Data_length: 180224
Max_data_length: 0
   Index_length: 196608
      Data_free: 0
 Auto_increment: 4581
    Create_time: 2023-12-17 14:19:02
    Update_time: NULL
     Check_time: NULL
      Collation: utf8mb4_0900_ai_ci
       Checksum: NULL
 Create_options:
        Comment:
*************************** 17. row ***************************
           Name: items
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 0
 Avg_row_length: 0
    Data_length: 16384
Max_data_length: 0
   Index_length: 0
      Data_free: 0
 Auto_increment: NULL
    Create_time: 2023-12-24 22:38:40
    Update_time: NULL
     Check_time: NULL
      Collation: utf8mb4_0900_ai_ci
       Checksum: NULL
 Create_options:
        Comment:
*************************** 18. row ***************************
           Name: jtable
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 0
 Avg_row_length: 0
    Data_length: 16384
Max_data_length: 0
   Index_length: 0
      Data_free: 0
 Auto_increment: NULL
    Create_time: 2023-12-20 08:12:10
    Update_time: NULL
     Check_time: NULL
      Collation: utf8mb4_0900_ai_ci
       Checksum: NULL
 Create_options:
        Comment:
*************************** 19. row ***************************
           Name: language
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 6
 Avg_row_length: 2730
    Data_length: 16384
Max_data_length: 0
   Index_length: 0
      Data_free: 0
 Auto_increment: 6
    Create_time: 2023-12-17 14:19:02
    Update_time: NULL
     Check_time: NULL
      Collation: utf8mb4_0900_ai_ci
       Checksum: NULL
 Create_options:
        Comment:
*************************** 20. row ***************************
           Name: md5_tbl
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 0
 Avg_row_length: 0
    Data_length: 16384
Max_data_length: 0
   Index_length: 0
      Data_free: 0
 Auto_increment: NULL
    Create_time: 2023-12-17 20:14:32
    Update_time: NULL
     Check_time: NULL
      Collation: utf8mb4_0900_ai_ci
       Checksum: NULL
 Create_options:
        Comment:
*************************** 21. row ***************************
           Name: mytable
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 6
 Avg_row_length: 2730
    Data_length: 16384
Max_data_length: 0
   Index_length: 0
      Data_free: 0
 Auto_increment: NULL
    Create_time: 2023-12-20 19:11:25
    Update_time: NULL
     Check_time: NULL
      Collation: utf8mb4_0900_ai_ci
       Checksum: NULL
 Create_options:
        Comment:
*************************** 22. row ***************************
           Name: nicer_but_slower_film_list
         Engine: NULL
        Version: NULL
     Row_format: NULL
           Rows: NULL
 Avg_row_length: NULL
    Data_length: NULL
Max_data_length: NULL
   Index_length: NULL
      Data_free: NULL
 Auto_increment: NULL
    Create_time: 2023-12-17 14:19:02
    Update_time: NULL
     Check_time: NULL
      Collation: NULL
       Checksum: NULL
 Create_options: NULL
        Comment: VIEW
*************************** 23. row ***************************
           Name: payment
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 15400
 Avg_row_length: 103
    Data_length: 1589248
Max_data_length: 0
   Index_length: 638976
      Data_free: 0
 Auto_increment: 16049
    Create_time: 2023-12-17 14:19:02
    Update_time: NULL
     Check_time: NULL
      Collation: utf8mb4_0900_ai_ci
       Checksum: NULL
 Create_options:
        Comment:
*************************** 24. row ***************************
           Name: product
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 6
 Avg_row_length: 2730
    Data_length: 16384
Max_data_length: 0
   Index_length: 0
      Data_free: 0
 Auto_increment: NULL
    Create_time: 2023-12-19 07:19:20
    Update_time: NULL
     Check_time: NULL
      Collation: utf8mb4_0900_ai_ci
       Checksum: NULL
 Create_options:
        Comment:
*************************** 25. row ***************************
           Name: rental
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 16010
 Avg_row_length: 99
    Data_length: 1589248
Max_data_length: 0
   Index_length: 1196032
      Data_free: 0
 Auto_increment: 16049
    Create_time: 2023-12-17 14:19:02
    Update_time: NULL
     Check_time: NULL
      Collation: utf8mb4_0900_ai_ci
       Checksum: NULL
 Create_options:
        Comment:
*************************** 26. row ***************************
           Name: sales
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 0
 Avg_row_length: 0
    Data_length: 16384
Max_data_length: 0
   Index_length: 0
      Data_free: 0
 Auto_increment: NULL
    Create_time: 2023-12-20 19:04:38
    Update_time: NULL
     Check_time: NULL
      Collation: utf8mb4_0900_ai_ci
       Checksum: NULL
 Create_options:
        Comment:
*************************** 27. row ***************************
           Name: sales_by_film_category
         Engine: NULL
        Version: NULL
     Row_format: NULL
           Rows: NULL
 Avg_row_length: NULL
    Data_length: NULL
Max_data_length: NULL
   Index_length: NULL
      Data_free: NULL
 Auto_increment: NULL
    Create_time: 2023-12-17 14:19:02
    Update_time: NULL
     Check_time: NULL
      Collation: NULL
       Checksum: NULL
 Create_options: NULL
        Comment: VIEW
*************************** 28. row ***************************
           Name: sales_by_store
         Engine: NULL
        Version: NULL
     Row_format: NULL
           Rows: NULL
 Avg_row_length: NULL
    Data_length: NULL
Max_data_length: NULL
   Index_length: NULL
      Data_free: NULL
 Auto_increment: NULL
    Create_time: 2023-12-17 14:19:02
    Update_time: NULL
     Check_time: NULL
      Collation: NULL
       Checksum: NULL
 Create_options: NULL
        Comment: VIEW
*************************** 29. row ***************************
           Name: staff
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 2
 Avg_row_length: 32768
    Data_length: 65536
Max_data_length: 0
   Index_length: 32768
      Data_free: 0
 Auto_increment: 2
    Create_time: 2023-12-17 14:19:02
    Update_time: NULL
     Check_time: NULL
      Collation: utf8mb4_0900_ai_ci
       Checksum: NULL
 Create_options:
        Comment:
*************************** 30. row ***************************
           Name: staff_list
         Engine: NULL
        Version: NULL
     Row_format: NULL
           Rows: NULL
 Avg_row_length: NULL
    Data_length: NULL
Max_data_length: NULL
   Index_length: NULL
      Data_free: NULL
 Auto_increment: NULL
    Create_time: 2023-12-17 14:19:02
    Update_time: NULL
     Check_time: NULL
      Collation: NULL
       Checksum: NULL
 Create_options: NULL
        Comment: VIEW
*************************** 31. row ***************************
           Name: store
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 2
 Avg_row_length: 8192
    Data_length: 16384
Max_data_length: 0
   Index_length: 32768
      Data_free: 0
 Auto_increment: 2
    Create_time: 2023-12-17 14:19:02
    Update_time: NULL
     Check_time: NULL
      Collation: utf8mb4_0900_ai_ci
       Checksum: NULL
 Create_options:
        Comment:
*************************** 32. row ***************************
           Name: t
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 0
 Avg_row_length: 0
    Data_length: 16384
Max_data_length: 0
   Index_length: 0
      Data_free: 0
 Auto_increment: NULL
    Create_time: 2023-12-28 07:41:25
    Update_time: NULL
     Check_time: NULL
      Collation: utf8mb4_0900_ai_ci
       Checksum: NULL
 Create_options:
        Comment:
*************************** 33. row ***************************
           Name: t1
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 0
 Avg_row_length: 0
    Data_length: 16384
Max_data_length: 0
   Index_length: 0
      Data_free: 0
 Auto_increment: NULL
    Create_time: 2023-12-28 15:21:43
    Update_time: NULL
     Check_time: NULL
      Collation: utf8mb4_0900_ai_ci
       Checksum: NULL
 Create_options:
        Comment:
*************************** 34. row ***************************
           Name: t2
         Engine: InnoDB
        Version: 10
     Row_format: Compressed
           Rows: 0
 Avg_row_length: 0
    Data_length: 8192
Max_data_length: 0
   Index_length: 0
      Data_free: 0
 Auto_increment: NULL
    Create_time: 2023-12-21 07:04:32
    Update_time: NULL
     Check_time: NULL
      Collation: utf8mb3_general_ci
       Checksum: NULL
 Create_options: row_format=COMPRESSED
        Comment: New table comment
*************************** 35. row ***************************
           Name: tj10
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 2
 Avg_row_length: 8192
    Data_length: 16384
Max_data_length: 0
   Index_length: 0
      Data_free: 0
 Auto_increment: NULL
    Create_time: 2023-12-19 08:02:57
    Update_time: NULL
     Check_time: NULL
      Collation: utf8mb4_0900_ai_ci
       Checksum: NULL
 Create_options:
        Comment:
*************************** 36. row ***************************
           Name: tz
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 2
 Avg_row_length: 8192
    Data_length: 16384
Max_data_length: 0
   Index_length: 0
      Data_free: 0
 Auto_increment: NULL
    Create_time: 2023-12-17 16:48:50
    Update_time: NULL
     Check_time: NULL
      Collation: utf8mb4_0900_ai_ci
       Checksum: NULL
 Create_options:
        Comment:
*************************** 37. row ***************************
           Name: y
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 0
 Avg_row_length: 0
    Data_length: 16384
Max_data_length: 0
   Index_length: 0
      Data_free: 0
 Auto_increment: NULL
    Create_time: 2023-12-20 21:24:09
    Update_time: NULL
     Check_time: NULL
      Collation: utf8mb4_0900_ai_ci
       Checksum: NULL
 Create_options:
        Comment:
37 rows in set (0.00 sec)
```

### 13.7.7.40 SHOW TRIGGERS ステートメント
```Sql
mysql> SHOW TRIGGERS LIKE 'acc%'\G
Empty set (0.01 sec)
```
### 13.7.7.41 SHOW VARIABLES ステートメント
```sql
mysql> SELECT @@GLOBAL.innodb_data_file_path;
+--------------------------------+
| @@GLOBAL.innodb_data_file_path |
+--------------------------------+
| ibdata1:12M:autoextend         |
+--------------------------------+
1 row in set (0.00 sec)

mysql> SHOW VARIABLES LIKE 'max_join_size';
+---------------+----------------------+
| Variable_name | Value                |
+---------------+----------------------+
| max_join_size | 18446744073709551615 |
+---------------+----------------------+
1 row in set, 1 warning (0.01 sec)

mysql> SHOW SESSION VARIABLES LIKE 'max_join_size';
+---------------+----------------------+
| Variable_name | Value                |
+---------------+----------------------+
| max_join_size | 18446744073709551615 |
+---------------+----------------------+
1 row in set, 1 warning (0.00 sec)
```
### 13.7.7.42 SHOW WARNINGS ステートメント

```sql
mysql> SHOW COUNT(*) WARNINGS;
+-------------------------+
| @@session.warning_count |
+-------------------------+
|                       1 |
+-------------------------+
1 row in set (0.00 sec)

mysql> SELECT @@warning_count;
+-----------------+
| @@warning_count |
+-----------------+
|               1 |
+-----------------+
1 row in set (0.00 sec)

mysql> CREATE TABLE t1 (a TINYINT NOT NULL, b CHAR(4));
Query OK, 0 rows affected (0.02 sec)

mysql> INSERT INTO t1 VALUES(10,'mysql'), (NULL,'test'), (300,'xyz');
ERROR 1406 (22001): Data too long for column 'b' at row 1
mysql> SHOW WARNINGS\G
*************************** 1. row ***************************
  Level: Error
   Code: 1406
Message: Data too long for column 'b' at row 1
1 row in set (0.00 sec)
```

```sql
mysql> \W
Show warnings enabled.
mysql> SELECT 1/0;
+------+
| 1/0  |
+------+
| NULL |
+------+
1 row in set, 1 warning (0.00 sec)

Warning (Code 1365): Division by 0
mysql> \w
Show warnings disabled.
```
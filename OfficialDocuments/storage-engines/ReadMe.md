### 第 16 章 代替ストレージエンジン

```Sql
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

### 16.1 ストレージエンジンの設定

```sql
mysql> CREATE TABLE t1 (i INT) ENGINE = INNODB;
Query OK, 0 rows affected (0.03 sec)

mysql> CREATE TABLE t2 (i INT) ENGINE = CSV;
ERROR 1178 (42000): The storage engine for the table doesnt support nullable columns

mysql> CREATE TABLE t2 (i INT NOT NULL) ENGINE = CSV;
Query OK, 0 rows affected (0.01 sec)

mysql> CREATE TABLE t3 (i INT) ENGINE = MEMORY;
Query OK, 0 rows affected (0.01 sec)
```

### 16.2 MyISAM ストレージエンジン
```sql
mysql> CREATE TABLE t4 (i INT) ENGINE = MYISAM;
Query OK, 0 rows affected (0.01 sec)
```

### 16.3 MEMORY ストレージエンジン
```sql
mysql> CREATE TABLE t (i INT) ENGINE = MEMORY;
Query OK, 0 rows affected (0.01 sec)

mysql> SELECT COUNT(*) FROM t;
+----------+
| COUNT(*) |
+----------+
|        0 |
+----------+
1 row in set (0.00 sec)

mysql> DROP TABLE t;
Query OK, 0 rows affected (0.01 sec)
```
インデックス
```sql
mysql> CREATE TABLE lookup
    ->     (id INT, INDEX USING HASH (id))
    ->     ENGINE = MEMORY;
Query OK, 0 rows affected (0.01 sec)
```
max_heap_table_size システム変数は MEMORY テーブルの最大サイズの制限値を設定します
```sql
--1MB
mysql> SET max_heap_table_size = 1024*1024;
Query OK, 0 rows affected (0.00 sec)
```

### 16.4 CSV ストレージエンジン
```Sql
mysql> CREATE TABLE test (i INT NOT NULL, c CHAR(10) NOT NULL)
    ->        ENGINE = CSV;
Query OK, 0 rows affected (0.01 sec)

mysql> INSERT INTO test VALUES(1,'record one'),(2,'record two');
Query OK, 2 rows affected (0.01 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql> SELECT * FROM test;
+---+------------+
| i | c          |
+---+------------+
| 1 | record one |
| 2 | record two |
+---+------------+
2 rows in set (0.00 sec)
```

### 16.4.1 CSV テーブルの修復と確認
```Sql
mysql> CHECK TABLE csvtest;
+--------------+-------+----------+------------------------------------+
| Table        | Op    | Msg_type | Msg_text                           |
+--------------+-------+----------+------------------------------------+
| test.csvtest | check | Error    | Table 'test.csvtest' doesn't exist |
| test.csvtest | check | status   | Operation failed                   |
+--------------+-------+----------+------------------------------------+
2 rows in set (0.00 sec)

mysql> CHECK TABLE test;
+-----------+-------+----------+----------+
| Table     | Op    | Msg_type | Msg_text |
+-----------+-------+----------+----------+
| test.test | check | status   | OK       |
+-----------+-------+----------+----------+
1 row in set (0.00 sec)


mysql> REPAIR TABLE csvtest;
+--------------+--------+----------+------------------------------------+
| Table        | Op     | Msg_type | Msg_text                           |
+--------------+--------+----------+------------------------------------+
| test.csvtest | repair | Error    | Table 'test.csvtest' doesn't exist |
| test.csvtest | repair | status   | Operation failed                   |
+--------------+--------+----------+------------------------------------+
2 rows in set (0.00 sec)

mysql> REPAIR TABLE test;
+-----------+--------+----------+----------+
| Table     | Op     | Msg_type | Msg_text |
+-----------+--------+----------+----------+
| test.test | repair | status   | OK       |
+-----------+--------+----------+----------+
1 row in set (0.00 sec)
```

### 16.6 BLACKHOLE ストレージエンジン
BLACKHOLE ストレージエンジンは、データを受け取るけれども破棄して格納しない「ブラックホール」として機能します。 検索は、常に空の結果を返します
```sql
mysql> CREATE TABLE test(i INT, c CHAR(10)) ENGINE = BLACKHOLE;
Query OK, 0 rows affected (0.01 sec)

mysql> INSERT INTO test VALUES(1,'record one'),(2,'record two');
Query OK, 2 rows affected (0.00 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql> SELECT * FROM test;
Empty set (0.00 sec)
```

### 16.7 MERGE ストレージエンジン

```sql
mysql> CREATE TABLE t1 (
    ->         a INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    ->         message CHAR(20)) ENGINE=MyISAM;
Query OK, 0 rows affected (0.01 sec)

mysql> CREATE TABLE t2 (
    ->         a INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    ->         message CHAR(20)) ENGINE=MyISAM;
Query OK, 0 rows affected (0.01 sec)

mysql> INSERT INTO t1 (message) VALUES ('Testing'),('table'),('t1');
Query OK, 3 rows affected (0.01 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql> INSERT INTO t2 (message) VALUES ('Testing'),('table'),('t2');
Query OK, 3 rows affected (0.00 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql> CREATE TABLE total (
    ->         a INT NOT NULL AUTO_INCREMENT,
    ->         message CHAR(20), INDEX(a))
    ->         ENGINE=MERGE UNION=(t1,t2) INSERT_METHOD=LAST;
Query OK, 0 rows affected (0.01 sec)

mysql> SELECT * FROM total;
+---+---------+
| a | message |
+---+---------+
| 1 | Testing |
| 2 | table   |
| 3 | t1      |
| 1 | Testing |
| 2 | table   |
| 3 | t2      |
+---+---------+
6 rows in set (0.00 sec)
```

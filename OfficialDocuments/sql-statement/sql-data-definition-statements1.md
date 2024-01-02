### 13.1.1 アトミックデータ定義ステートメントのサポート
```sql
mysql> CREATE TABLE t1 (c1 INT);
Query OK, 0 rows affected (0.04 sec)

mysql> DROP TABLE t1, t2;
ERROR 1051 (42S02): Unknown table 'sample.t2'
mysql> SHOW TABLES;
+------------------+
| Tables_in_sample |
+------------------+
| t1               |
+------------------+
1 row in set (0.00 sec)
```

```sql
mysql> CREATE USER userA;
Query OK, 0 rows affected (0.03 sec)

mysql> CREATE USER userA, userB;
ERROR 1396 (HY000): Operation CREATE USER failed for 'userA'@'%'
mysql> SELECT User FROM mysql.user WHERE User LIKE 'user%';
+-------+
| User  |
+-------+
| userA |
+-------+
1 row in set (0.00 sec)
```
### 13.1.2 ALTER DATABASE ステートメント
読取り専用オプション
```sql
mysql> create database mydb;
Query OK, 1 row affected (0.01 sec)

mysql> ALTER DATABASE mydb READ ONLY = 1;
Query OK, 1 row affected (0.01 sec)

mysql> ALTER DATABASE mydb READ ONLY = 0;
Query OK, 1 row affected (0.00 sec)
```
### 13.1.3 ALTER EVENT ステートメント
```sql
mysql> CREATE EVENT myevent
    ->     ON SCHEDULE
    ->       EVERY 6 HOUR
    ->     COMMENT 'A sample comment.'
    ->     DO
    ->       UPDATE myschema.mytable SET mycol = mycol + 1;
Query OK, 0 rows affected (0.01 sec)

mysql> ALTER EVENT myevent
    ->     ON SCHEDULE
    ->       EVERY 12 HOUR
    ->     STARTS CURRENT_TIMESTAMP + INTERVAL 4 HOUR;
Query OK, 0 rows affected (0.01 sec)

mysql> ALTER EVENT myevent
    ->     ON SCHEDULE
    ->       AT CURRENT_TIMESTAMP + INTERVAL 1 DAY
    ->     DO
    ->       TRUNCATE TABLE myschema.mytable;
Query OK, 0 rows affected (0.00 sec)
```

### 13.1.9 ALTER TABLE ステートメント
```sql
mysql> create table t2 (c int, d int, e int);
Query OK, 0 rows affected (0.02 sec)

mysql> ALTER TABLE t2 DROP COLUMN c, DROP COLUMN d;
Query OK, 0 rows affected (0.02 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE t2 ENGINE = InnoDB;
Query OK, 0 rows affected (0.04 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE t2 ROW_FORMAT = COMPRESSED;
Query OK, 0 rows affected (0.04 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE t2 AUTO_INCREMENT = 13;
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE t2 CHARACTER SET = utf8;
Query OK, 0 rows affected, 1 warning (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 1

mysql> ALTER TABLE t2 COMMENT = 'New table comment';
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
カラムの名前変更、再定義および並替え
```sql
mysql> create table t1(a int);
Query OK, 0 rows affected (0.02 sec)

mysql> ALTER TABLE t1 CHANGE a b BIGINT NOT NULL;
Query OK, 0 rows affected (0.05 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE t1 CHANGE b b INT NOT NULL;
Query OK, 0 rows affected (0.04 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE t1 MODIFY b INT NOT NULL;
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE t1 CHANGE b a INT NOT NULL;
Query OK, 0 rows affected (0.02 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> create table t1(a int, b int);
Query OK, 0 rows affected (0.02 sec)

mysql> ALTER TABLE t1 RENAME COLUMN a TO b,
    ->                RENAME COLUMN b TO a;
Query OK, 0 rows affected (0.02 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
### 13.1.9.1 ALTER TABLE パーティション操作
```sql
mysql> CREATE TABLE t1 (
    ->     id INT,
    ->     year_col INT
    -> );
Query OK, 0 rows affected (0.02 sec)

mysql> ALTER TABLE t1
    ->     PARTITION BY HASH(id)
    ->     PARTITIONS 8;
Query OK, 0 rows affected (0.18 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE t1 ADD PARTITION (PARTITION p3 VALUES LESS THAN (2002));
Query OK, 0 rows affected (0.04 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE t1 DROP PARTITION p0, p1;
Query OK, 0 rows affected (0.04 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE t1 DISCARD PARTITION p2, p3 TABLESPACE;
Query OK, 0 rows affected (0.01 sec)

```
選択したパーティションから行を削除するには、TRUNCATE PARTITION オプションを使用します。 このオプションは、1 つ以上のカンマ区切りのパーティション名のリストを取ります。
```sql
mysql> CREATE TABLE t1 (
    ->     id INT,
    ->     year_col INT
    -> )
    -> PARTITION BY RANGE (year_col) (
    ->     PARTITION p0 VALUES LESS THAN (1991),
    ->     PARTITION p1 VALUES LESS THAN (1995),
    ->     PARTITION p2 VALUES LESS THAN (1999),
    ->     PARTITION p3 VALUES LESS THAN (2003),
    ->     PARTITION p4 VALUES LESS THAN (2007)
    -> );
Query OK, 0 rows affected (0.09 sec)

mysql> ALTER TABLE t1 TRUNCATE PARTITION p0;
Query OK, 0 rows affected (0.05 sec)

mysql> ALTER TABLE t1 TRUNCATE PARTITION p1, p3;
Query OK, 0 rows affected (0.06 sec)

mysql> SELECT PARTITION_NAME, TABLE_ROWS
    ->     FROM INFORMATION_SCHEMA.PARTITIONS
    ->     WHERE TABLE_NAME = 't1';
+----------------+------------+
| PARTITION_NAME | TABLE_ROWS |
+----------------+------------+
| p0             |          0 |
| p1             |          0 |
| p2             |          0 |
| p3             |          0 |
| p4             |          0 |
+----------------+------------+
5 rows in set (0.04 sec)
```
パーティションを減らす
```sql
mysql> CREATE TABLE t2 (
    ->     name VARCHAR (30),
    ->     started DATE
    -> )
    -> PARTITION BY HASH( YEAR(started) )
    -> PARTITIONS 6;
Query OK, 0 rows affected (0.09 sec)

mysql> ALTER TABLE t2 COALESCE PARTITION 2;
Query OK, 0 rows affected (0.18 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> SELECT PARTITION_NAME, TABLE_ROWS
    ->          FROM INFORMATION_SCHEMA.PARTITIONS
    ->          WHERE TABLE_NAME = 't2';
+----------------+------------+
| PARTITION_NAME | TABLE_ROWS |
+----------------+------------+
| NULL           |          0 |
| p0             |          0 |
| p1             |          0 |
| p2             |          0 |
| p3             |          0 |
+----------------+------------+
5 rows in set (0.00 sec)
```
### 13.1.9.2 ALTER TABLE および生成されるカラム
生成されるカラムに対して許可される ALTER TABLE 操作は、ADD、MODIFY および CHANGE です。
```sql
mysql> CREATE TABLE t1 (c1 INT);
Query OK, 0 rows affected (0.02 sec)

mysql> ALTER TABLE t1 ADD COLUMN c2 INT GENERATED ALWAYS AS (c1 + 1) STORED;
Query OK, 0 rows affected (0.04 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE t1 MODIFY COLUMN c2 TINYINT GENERATED ALWAYS AS (c1 + 5) STORED;
Query OK, 0 rows affected (0.05 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE t1 CHANGE c2 c3 INT GENERATED ALWAYS AS (c1 + 1) STORED;
Query OK, 0 rows affected (0.05 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE t1 DROP COLUMN c3;
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
仮想生成カラムは、格納された生成カラムに変更できません。
```sql
mysql> CREATE TABLE t1 (c1 INT, c2 INT GENERATED ALWAYS AS (c1 + 1) VIRTUAL);
Query OK, 0 rows affected (0.03 sec)

mysql> ALTER TABLE t1 DROP COLUMN c2;
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE t1 ADD COLUMN c2 INT GENERATED ALWAYS AS (c1 + 1) STORED;
Query OK, 0 rows affected (0.05 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
### 13.1.9.3 ALTER TABLE の例
```sql
mysql> CREATE TABLE t1 (a INTEGER, b CHAR(10));
Query OK, 0 rows affected (0.03 sec)

mysql> ALTER TABLE t1 RENAME t2;
Query OK, 0 rows affected (0.02 sec)

mysql> ALTER TABLE t2 MODIFY a TINYINT NOT NULL, CHANGE b c CHAR(20);
Query OK, 0 rows affected (0.06 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE t2 ADD d TIMESTAMP;
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE t2 ADD INDEX (d), ADD UNIQUE (a);
Query OK, 0 rows affected (0.06 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE t2 DROP COLUMN c;
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE t2 ADD c INT UNSIGNED NOT NULL AUTO_INCREMENT,
    ->   ADD PRIMARY KEY (c);
Query OK, 0 rows affected (0.07 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
### 13.1.13 CREATE EVENT ステートメント

```sql
mysql> SELECT NOW();
+---------------------+
| NOW()               |
+---------------------+
| 2023-12-21 22:51:05 |
+---------------------+
1 row in set (0.00 sec)

mysql> CREATE EVENT e_totals
    ->          ON SCHEDULE AT '2006-02-10 23:59:00'
    ->          DO INSERT INTO test.totals VALUES (NOW());
Query OK, 0 rows affected, 1 warning (0.01 sec)

mysql> SHOW WARNINGS\G
*************************** 1. row ***************************
  Level: Note
   Code: 1588
Message: Event execution time is in the past and ON COMPLETION NOT PRESERVE is set. The event was dropped immediately after creation.
1 row in set (0.00 sec)


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
```
### 13.1.15 CREATE INDEX ステートメント
```sql
mysql> CREATE TABLE t1 (
    ->   col1 VARCHAR(10),
    ->   col2 VARCHAR(20),
    ->   INDEX (col1, col2(10))
    -> );
Query OK, 0 rows affected (0.06 sec)

mysql> CREATE TABLE t1 (col1 INT, col2 INT, INDEX func_index ((ABS(col1))));
ERROR 1050 (42S01): Table 't1' already exists
mysql> CREATE INDEX idx1 ON t1 ((col1 + col2));
Query OK, 0 rows affected (0.04 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> CREATE INDEX idx2 ON t1 ((col1 + col2), (col1 - col2), col1);
Query OK, 0 rows affected (0.02 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE t1 ADD INDEX ((col1 * 40) DESC);
Query OK, 0 rows affected (0.09 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
非機能キー部分は接頭辞の長さの指定をサポートしていますが、これは機能キー部分では不可能です。 解決策は、SUBSTRING()(または、このセクションの後半で説明する CAST()) を使用することです
```sql
mysql> CREATE TABLE tbl (
    ->   col1 LONGTEXT,
    ->   INDEX idx1 ((SUBSTRING(col1, 1, 10)))
    -> );
Query OK, 0 rows affected (0.09 sec)

mysql> SELECT * FROM tbl WHERE SUBSTRING(col1, 1, 9) = '123456789';
Empty set (0.01 sec)

mysql> SELECT * FROM tbl WHERE SUBSTRING(col1, 1, 10) = '1234567890';
Empty set (0.00 sec)
```
JSON カラムをインデックス付けするには、次のように CAST() 関数を使用します:
```sql
mysql> CREATE TABLE employees (
    ->   data JSON,
    ->   INDEX ((CAST(data->>'$.name' AS CHAR(30))))
    -> );
Query OK, 0 rows affected (0.04 sec)

mysql> INSERT INTO employees VALUES
    ->   ('{ "name": "james", "salary": 9000 }'),
    ->   ('{ "name": "James", "salary": 10000 }'),
    ->   ('{ "name": "Mary", "salary": 12000 }'),
    ->   ('{ "name": "Peter", "salary": 8000 }');
Query OK, 4 rows affected (0.02 sec)
Records: 4  Duplicates: 0  Warnings: 0

mysql> SELECT * FROM employees WHERE data->>'$.name' = 'James';
+------------------------------------+
| data                               |
+------------------------------------+
| {"name": "James", "salary": 10000} |
+------------------------------------+
1 row in set (0.00 sec)
```
クエリーに完全な式を指定します
```sql
mysql> CREATE TABLE employees (
    ->   data JSON,
    ->   INDEX idx ((CAST(data->>"$.name" AS CHAR(30))))
    -> );
Query OK, 0 rows affected (0.03 sec)

mysql> INSERT INTO employees VALUES
    ->   ('{ "name": "james", "salary": 9000 }'),
    ->   ('{ "name": "James", "salary": 10000 }'),
    ->   ('{ "name": "Mary", "salary": 12000 }'),
    ->   ('{ "name": "Peter", "salary": 8000 }');
Query OK, 4 rows affected (0.00 sec)
Records: 4  Duplicates: 0  Warnings: 0

mysql> SELECT * FROM employees WHERE CAST(data->>'$.name' AS CHAR(30)) = 'James';
+------------------------------------+
| data                               |
+------------------------------------+
| {"name": "james", "salary": 9000}  |
| {"name": "James", "salary": 10000} |
+------------------------------------+
2 rows in set (0.01 sec)
```
複数値インデックスの作成
```sql
mysql> CREATE TABLE customers (
    ->     id BIGINT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    ->     modified DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    ->     custinfo JSON,
    ->     INDEX zips( (CAST(custinfo->'$.zip' AS UNSIGNED ARRAY)) )
    ->     );
Query OK, 0 rows affected (0.04 sec)

mysql> drop table customers;
Query OK, 0 rows affected (0.02 sec)

mysql> CREATE TABLE customers (
    ->     id BIGINT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    ->     modified DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    ->     custinfo JSON
    ->     );
Query OK, 0 rows affected (0.02 sec)

mysql>
mysql> ALTER TABLE customers ADD INDEX zips( (CAST(custinfo->'$.zip' AS UNSIGNED ARRAY)) );
Query OK, 0 rows affected (0.03 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> drop table customers;
Query OK, 0 rows affected (0.01 sec)

mysql> CREATE TABLE customers (
    ->     id BIGINT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    ->     modified DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    ->     custinfo JSON
    ->     );
Query OK, 0 rows affected (0.03 sec)

mysql>
mysql> CREATE INDEX zips ON customers ( (CAST(custinfo->'$.zip' AS UNSIGNED ARRAY)) );
Query OK, 0 rows affected (0.02 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> drop table customers;
Query OK, 0 rows affected (0.01 sec)

mysql> CREATE TABLE customers (
    ->     id BIGINT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    ->     modified DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    ->     custinfo JSON
    ->     );
Query OK, 0 rows affected (0.02 sec)

mysql>
mysql> ALTER TABLE customers ADD INDEX comp(id, modified,
    ->     (CAST(custinfo->'$.zipcode' AS UNSIGNED ARRAY)) );
Query OK, 0 rows affected (0.02 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
複数値インデックスの使用
```sql
mysql> drop table customers;
Query OK, 0 rows affected (0.01 sec)

mysql> CREATE TABLE customers (
    ->          id BIGINT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    ->          modified DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    ->          custinfo JSON
    ->          );
Query OK, 0 rows affected (0.02 sec)

mysql> INSERT INTO customers VALUES
    ->          (NULL, NOW(), '{"user":"Jack","user_id":37,"zipcode":[94582,94536]}'),
    ->          (NULL, NOW(), '{"user":"Jill","user_id":22,"zipcode":[94568,94507,94582]}'),
    ->          (NULL, NOW(), '{"user":"Bob","user_id":31,"zipcode":[94477,94507]}'),
    ->          (NULL, NOW(), '{"user":"Mary","user_id":72,"zipcode":[94536]}'),
    ->          (NULL, NOW(), '{"user":"Ted","user_id":56,"zipcode":[94507,94582]}');
Query OK, 5 rows affected (0.01 sec)
Records: 5  Duplicates: 0  Warnings: 0

mysql> SELECT * FROM customers
    ->          WHERE 94507 MEMBER OF(custinfo->'$.zipcode');
+----+---------------------+-------------------------------------------------------------------+
| id | modified            | custinfo                                                          |
+----+---------------------+-------------------------------------------------------------------+
|  2 | 2023-12-22 19:46:39 | {"user": "Jill", "user_id": 22, "zipcode": [94568, 94507, 94582]} |
|  3 | 2023-12-22 19:46:39 | {"user": "Bob", "user_id": 31, "zipcode": [94477, 94507]}         |
|  5 | 2023-12-22 19:46:39 | {"user": "Ted", "user_id": 56, "zipcode": [94507, 94582]}         |
+----+---------------------+-------------------------------------------------------------------+
3 rows in set (0.00 sec)

mysql> SELECT * FROM customers
    ->          WHERE JSON_CONTAINS(custinfo->'$.zipcode', CAST('[94507,94582]' AS JSON));
+----+---------------------+-------------------------------------------------------------------+
| id | modified            | custinfo                                                          |
+----+---------------------+-------------------------------------------------------------------+
|  2 | 2023-12-22 19:46:39 | {"user": "Jill", "user_id": 22, "zipcode": [94568, 94507, 94582]} |
|  5 | 2023-12-22 19:46:39 | {"user": "Ted", "user_id": 56, "zipcode": [94507, 94582]}         |
+----+---------------------+-------------------------------------------------------------------+
2 rows in set (0.00 sec)

mysql> SELECT * FROM customers
    ->          WHERE JSON_OVERLAPS(custinfo->'$.zipcode', CAST('[94507,94582]' AS JSON));
+----+---------------------+-------------------------------------------------------------------+
| id | modified            | custinfo                                                          |
+----+---------------------+-------------------------------------------------------------------+
|  1 | 2023-12-22 19:46:39 | {"user": "Jack", "user_id": 37, "zipcode": [94582, 94536]}        |
|  2 | 2023-12-22 19:46:39 | {"user": "Jill", "user_id": 22, "zipcode": [94568, 94507, 94582]} |
|  3 | 2023-12-22 19:46:39 | {"user": "Bob", "user_id": 31, "zipcode": [94477, 94507]}         |
|  5 | 2023-12-22 19:46:39 | {"user": "Ted", "user_id": 56, "zipcode": [94507, 94582]}         |
+----+---------------------+-------------------------------------------------------------------+
4 rows in set (0.00 sec)
```
インデックスを使わない
```sql
mysql> EXPLAIN SELECT * FROM customers
    ->          WHERE 94507 MEMBER OF(custinfo->'$.zipcode');
+----+-------------+-----------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table     | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+-----------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | customers | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    5 |   100.00 | Using where |
+----+-------------+-----------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)

mysql> EXPLAIN SELECT * FROM customers
    ->          WHERE JSON_CONTAINS(custinfo->'$.zipcode', CAST('[94507,94582]' AS JSON));
+----+-------------+-----------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table     | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+-----------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | customers | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    5 |   100.00 | Using where |
+----+-------------+-----------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)

mysql> EXPLAIN SELECT * FROM customers
    ->          WHERE JSON_OVERLAPS(custinfo->'$.zipcode', CAST('[94507,94582]' AS JSON));
+----+-------------+-----------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table     | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+-----------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | customers | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    5 |   100.00 | Using where |
+----+-------------+-----------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)
```
インデックスを使用する
```Sql
mysql> ALTER TABLE customers
    ->          ADD INDEX zips( (CAST(custinfo->'$.zipcode' AS UNSIGNED ARRAY)) );
Query OK, 0 rows affected (0.03 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> EXPLAIN SELECT * FROM customers
    ->          WHERE 94507 MEMBER OF(custinfo->'$.zipcode');
+----+-------------+-----------+------------+------+---------------+------+---------+-------+------+----------+-------------+
| id | select_type | table     | partitions | type | possible_keys | key  | key_len | ref   | rows | filtered | Extra       |
+----+-------------+-----------+------------+------+---------------+------+---------+-------+------+----------+-------------+
|  1 | SIMPLE      | customers | NULL       | ref  | zips          | zips | 9       | const |    3 |   100.00 | Using where |
+----+-------------+-----------+------------+------+---------------+------+---------+-------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)

mysql> EXPLAIN SELECT * FROM customers
    ->          WHERE JSON_CONTAINS(custinfo->'$.zipcode', CAST('[94507,94582]' AS JSON));
+----+-------------+-----------+------------+-------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table     | partitions | type  | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+-----------+------------+-------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | customers | NULL       | range | zips          | zips | 9       | NULL |    6 |   100.00 | Using where |
+----+-------------+-----------+------------+-------+---------------+------+---------+------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)
```

### 13.1.17 CREATE PROCEDURE ステートメントおよび CREATE FUNCTION ステートメント
```sql
mysql> CREATE FUNCTION hello (s CHAR(20))
    -> RETURNS CHAR(50) DETERMINISTIC
    ->        RETURN CONCAT('Hello, ',s,'!');
Query OK, 0 rows affected (0.01 sec)

mysql> SELECT hello('world');
+----------------+
| hello('world') |
+----------------+
| Hello, world!  |
+----------------+
1 row in set (0.00 sec)
```

### 13.1.18 CREATE SERVER ステートメント

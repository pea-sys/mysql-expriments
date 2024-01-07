### 13.2.1 CALL ステートメント
CALL ステートメントは、以前に CREATE PROCEDURE を使用して定義されたストアドプロシージャーを呼び出します。
```sql
mysql> DELIMITER $$
mysql> CREATE PROCEDURE p (OUT ver_param VARCHAR(25), INOUT incr_param INT) BEGIN  SELECT VERSION() INTO ver_param;SET incr_param = incr_param + 1; END$$
Query OK, 0 rows affected (0.01 sec)

mysql> DELIMITER ;
```

```sql
mysql> SET @increment = 10;
Query OK, 0 rows affected (0.00 sec)

mysql> CALL p(@version, @increment);
Query OK, 1 row affected (0.01 sec)

mysql> SELECT @version, @increment;
+----------+------------+
| @version | @increment |
+----------+------------+
| 8.2.0    |         11 |
+----------+------------+
1 row in set (0.00 sec)

mysql> SET @increment = 10;
Query OK, 0 rows affected (0.00 sec)

mysql> PREPARE s FROM 'CALL p(?, ?)';
Query OK, 0 rows affected (0.00 sec)
Statement prepared

mysql> EXECUTE s USING @version, @increment;
Query OK, 1 row affected (0.00 sec)

mysql> SELECT @version, @increment;
+--------------------+------------+
| @version           | @increment |
+--------------------+------------+
| 0x382E322E30       |         11 |
+--------------------+------------+
1 row in set (0.00 sec)
```
### 13.2.3 DO ステートメント
```sql
mysql> SELECT SLEEP(5);
+----------+
| SLEEP(5) |
+----------+
|        0 |
+----------+
1 row in set (5.01 sec)

mysql> DO SLEEP(5);
Query OK, 0 rows affected (5.00 sec)
```
### 13.2.6 INSERT ステートメント
```sql
mysql> create table t1(a int, b int);
Query OK, 0 rows affected (0.03 sec)

mysql> INSERT INTO t1 (a,b) VALUES(15,a*2);
Query OK, 1 row affected (0.01 sec)

mysql> INSERT INTO t1 (a,b) VALUES(1,2), (4,5), (7,8);
Query OK, 3 rows affected (0.00 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql> INSERT INTO t1 (a,b) VALUES ROW(1,2), ROW(4,5), ROW(7,8);
Query OK, 3 rows affected (0.00 sec)
Records: 3  Duplicates: 0  Warnings: 0
```
### 13.2.6.1 INSERT ... SELECT ステートメント
```sql
mysql> INSERT INTO t1 TABLE t1;
Query OK, 7 rows affected (0.01 sec)
Records: 7  Duplicates: 0  Warnings: 0

mysql> INSERT INTO t1 SELECT * FROM t1;
Query OK, 14 rows affected (0.00 sec)
Records: 14  Duplicates: 0  Warnings: 0
```

### 13.2.6.2 INSERT ... ON DUPLICATE KEY UPDATE ステートメント
UPSERT
```sql
mysql> create table t1(a int unique, b int);
Query OK, 0 rows affected (0.04 sec)

mysql> INSERT INTO t1 (a,b) VALUES (1,2),(1,3) AS new ON DUPLICATE KEY UPDATE b = new.a+new.b;
Query OK, 3 rows affected (0.01 sec)
Records: 2  Duplicates: 1  Warnings: 0

mysql> table t1;
+------+------+
| a    | b    |
+------+------+
|    1 |    4 |
+------+------+
1 row in set (0.00 sec)
```
### 13.2.6.3 INSERT DELAYED ステートメント
```sql
mysql> create table bit_test (b binary);

mysql> LOAD DATA INFILE 'bit_test.txt'
    ->        INTO TABLE bit_test (@var1)
    ->        SET b = CAST(CONV(MID(@var1, 3, LENGTH(@var1)-3), 2, 10) AS UNSIGNED);
ERROR 1406 (22001): Data too long for column 'b' at row 2

```

### 13.2.8 LOAD XML ステートメント
```Sql
mysql -u root -p --enable-local-infile

mysql> use sample;
Database changed

mysql> CREATE TABLE person (
    ->     person_id INT NOT NULL PRIMARY KEY,
    ->     fname VARCHAR(40) NULL,
    ->     lname VARCHAR(40) NULL,
    ->     created TIMESTAMP
    -> );
Query OK, 0 rows affected (0.03 sec)


mysql> LOAD XML LOCAL INFILE 'C:/Users/masami/source/repos/mysql-expriments/OfficialDocuments/sql-statement/person.xml'
    ->        INTO TABLE person
    ->        ROWS IDENTIFIED BY '<person>';
Query OK, 8 rows affected (0.00 sec)
Records: 8  Deleted: 0  Skipped: 0  Warnings: 0

mysql> SELECT * FROM person;
+-----------+--------+------------+---------+
| person_id | fname  | lname      | created |
+-----------+--------+------------+---------+
|         1 | Kapek  | Sainnouine | NULL    |
|         2 | Sajon  | Rondela    | NULL    |
|         3 | Likame | ?rrtmons   | NULL    |
|         4 | Slar   | Manlanth   | NULL    |
|         5 | Stoma  | Milu       | NULL    |
|         6 | Nirtam | Skl?d      | NULL    |
|         7 | Sungam | Dulb?d     | NULL    |
|         8 | Sraref | Encmelt    | NULL    |
+-----------+--------+------------+---------+
8 rows in set (0.00 sec)

C:\Users\masami>mysql -u root -p --xml -e "SELECT * FROM sample.person" > person-dump.xml
Enter password: **********
```
### 13.2.9 REPLACE ステートメント
REPLACE は、INSERT とまったく同じように機能します。ただし、テーブル内の古い行に、PRIMARY KEY または UNIQUE インデックスに関して新しい行と同じ値が含まれている場合、その古い行は新しい行が挿入される前に削除されます。
```sql
mysql> CREATE TABLE test (
    ->   id INT UNSIGNED NOT NULL AUTO_INCREMENT,
    ->   data VARCHAR(64) DEFAULT NULL,
    ->   ts TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    ->   PRIMARY KEY (id)
    -> );
Query OK, 0 rows affected (0.02 sec)

mysql> REPLACE INTO test VALUES (1, 'Old', '2014-08-20 18:47:00');
Query OK, 1 row affected (0.01 sec)

mysql> REPLACE INTO test VALUES (1, 'New', '2014-08-20 18:47:42');
Query OK, 2 rows affected (0.01 sec)

mysql> table test;
+----+------+---------------------+
| id | data | ts                  |
+----+------+---------------------+
|  1 | New  | 2014-08-20 18:47:42 |
+----+------+---------------------+
1 row in set (0.00 sec)

mysql> CREATE TABLE test2 (
    ->   id INT UNSIGNED NOT NULL AUTO_INCREMENT,
    ->   data VARCHAR(64) DEFAULT NULL,
    ->   ts TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    ->   PRIMARY KEY (id, ts)
    -> );
Query OK, 0 rows affected (0.03 sec)

mysql> REPLACE INTO test2 VALUES (1, 'Old', '2014-08-20 18:47:00');
Query OK, 1 row affected (0.00 sec)

mysql> REPLACE INTO test2 VALUES (1, 'New', '2014-08-20 18:47:42');
Query OK, 1 row affected (0.00 sec)

mysql> table test2;
+----+------+---------------------+
| id | data | ts                  |
+----+------+---------------------+
|  1 | Old  | 2014-08-20 18:47:00 |
|  1 | New  | 2014-08-20 18:47:42 |
+----+------+---------------------+
2 rows in set (0.00 sec)
```
### 13.2.10 SELECT ステートメント
```sql
mysql> SELECT 1 + 1;
+-------+
| 1 + 1 |
+-------+
|     2 |
+-------+
1 row in set (0.00 sec)
```
テーブルが参照されない状況では、ダミーのテーブル名として DUAL を指定することが許可されます
```sql
mysql> SELECT 1 + 1 FROM DUAL;
+-------+
| 1 + 1 |
+-------+
|     2 |
+-------+
1 row in set (0.00 sec)
```
テーブル参照は、tbl_name AS alias_name または tbl_name alias_name を使用してエイリアス設定できます。 次のステートメントは同等です
```Sql
mysql> select t1.Name,t2.Name FROM city as t1, country as t2 where t1.CountryCode = t2.Code limit 5;
+----------------+-------------+
| Name           | Name        |
+----------------+-------------+
| Oranjestad     | Aruba       |
| Kabul          | Afghanistan |
| Qandahar       | Afghanistan |
| Herat          | Afghanistan |
| Mazar-e-Sharif | Afghanistan |
+----------------+-------------+
5 rows in set (0.00 sec)

mysql> select t1.Name,t2.Name FROM city t1, country t2 where t1.CountryCode = t2.Code limit 5;
+----------------+-------------+
| Name           | Name        |
+----------------+-------------+
| Oranjestad     | Aruba       |
| Kabul          | Afghanistan |
| Qandahar       | Afghanistan |
| Herat          | Afghanistan |
| Mazar-e-Sharif | Afghanistan |
+----------------+-------------+
5 rows in set (0.00 sec)
```
カラム名、カラムのエイリアス、またはカラム位置を使用して、出力のために選択されたカラムを ORDER BY および GROUP BY 句で参照できます。 カラム位置は整数であり、1 から始まります。
```sql
mysql> select CountryCode from countrylanguage order by CountryCode limit 5;
+-------------+
| CountryCode |
+-------------+
| ABW         |
| ABW         |
| ABW         |
| ABW         |
| AFG         |
+-------------+
5 rows in set (0.00 sec)

mysql> select CountryCode from countrylanguage order by 1 limit 5;
+-------------+
| CountryCode |
+-------------+
| ABW         |
| ABW         |
| ABW         |
| ABW         |
| AFG         |
+-------------+
5 rows in set (0.00 sec)
```
LIMIT 句を使用すると、SELECT ステートメントによって返される行数を制約できます。 LIMIT は 1 つまたは 2 つの数値引数を受け取ります。
```sql
mysql> select CountryCode from countrylanguage order by 1 limit 10,1;
+-------------+
| CountryCode |
+-------------+
| AGO         |
+-------------+
1 row in set (0.00 sec)
```
準備済みステートメントでは、プレースホルダを使用できます。
```sql
mysql> SET @a=1;
Query OK, 0 rows affected (0.00 sec)

mysql> PREPARE STMT FROM 'SELECT * FROM countrylanguage LIMIT ?';
Query OK, 0 rows affected (0.00 sec)
Statement prepared

mysql> EXECUTE STMT USING @a;
+-------------+----------+------------+------------+
| CountryCode | Language | IsOfficial | Percentage |
+-------------+----------+------------+------------+
| ABW         | Dutch    | T          |        5.3 |
+-------------+----------+------------+------------+
1 row in set (0.00 sec)
```
### 13.2.10.1 SELECT ... INTO ステートメント
```sql
mysql> SELECT COUNT(*) INTO @myvar FROM countrylanguage;
Query OK, 1 row affected (0.01 sec)

mysql> SELECT @myvar;
+--------+
| @myvar |
+--------+
|    984 |
+--------+
1 row in set (0.00 sec)
```

### 13.2.10.2 JOIN 句
MySQL では、JOIN、CROSS JOIN、および INNER JOIN は構文上同等です
```Sql
mysql> select `cu`.`customer_id` AS `ID`,concat(`cu`.`first_name`,_utf8mb4' ',`cu`.`last_name`) AS `name`,`a`.`address` AS `address`,`a`.`postal_code` AS `zip code`,`a`.`phone` AS `phone`,`sakila`.`city`.`city` AS `city`,`sakila`.`country`.`country` AS `country`,if(`cu`.`active`,_utf8mb4'active',_utf8mb4'') AS `notes`,`cu`.`store_id` AS `SID` from (((`sakila`.`customer` `cu` join `sakila`.`address` `a` on((`cu`.`address_id` = `a`.`address_id`))) join `sakila`.`city` on((`a`.`city_id` = `sakila`.`city`.`city_id`))) join `sakila`.`country` on((`sakila`.`city`.`country_id` = `sakila`.`country`.`country_id`))) limit 5;
+-----+----------------+-----------------------------+----------+--------------+--------+----------------+--------+-----+
| ID  | name           | address                     | zip code | phone        | city   | country        | notes  | SID |
+-----+----------------+-----------------------------+----------+--------------+--------+----------------+--------+-----+
| 218 | VERA MCCOY     | 1168 Najafabad Parkway      | 40301    | 886649065861 | Kabul  | Afghanistan    | active |   1 |
| 441 | MARIO CHEATHAM | 1924 Shimonoseki Drive      | 52625    | 406784385440 | Batna  | Algeria        | active |   1 |
|  69 | JUDY GRAY      | 1031 Daugavpils Parkway     | 59025    | 107137400143 | B?char | Algeria        | active |   2 |
| 176 | JUNE CARROLL   | 757 Rustenburg Avenue       | 89668    | 506134035434 | Skikda | Algeria        | active |   1 |
| 320 | ANTHONY SCHWAB | 1892 Nabereznyje Telny Lane | 28396    | 478229987054 | Tafuna | American Samoa | active |   2 |
+-----+----------------+-----------------------------+----------+--------------+--------+----------------+--------+-----+
5 rows in set (0.00 sec)

mysql> select `cu`.`customer_id` AS `ID`,concat(`cu`.`first_name`,_utf8mb4' ',`cu`.`last_name`) AS `name`,`a`.`address` AS `address`,`a`.`postal_code` AS `zip code`,`a`.`phone` AS `phone`,`sakila`.`city`.`city` AS `city`,`sakila`.`country`.`country` AS `country`,if(`cu`.`active`,_utf8mb4'active',_utf8mb4'') AS `notes`,`cu`.`store_id` AS `SID` from (((`sakila`.`customer` `cu` cross join `sakila`.`address` `a` on((`cu`.`address_id` = `a`.`address_id`))) cross join `sakila`.`city` on((`a`.`city_id` = `sakila`.`city`.`city_id`))) cross join `sakila`.`country` on((`sakila`.`city`.`country_id` = `sakila`.`country`.`country_id`))) limit 5;
+-----+----------------+-----------------------------+----------+--------------+--------+----------------+--------+-----+
| ID  | name           | address                     | zip code | phone        | city   | country        | notes  | SID |
+-----+----------------+-----------------------------+----------+--------------+--------+----------------+--------+-----+
| 218 | VERA MCCOY     | 1168 Najafabad Parkway      | 40301    | 886649065861 | Kabul  | Afghanistan    | active |   1 |
| 441 | MARIO CHEATHAM | 1924 Shimonoseki Drive      | 52625    | 406784385440 | Batna  | Algeria        | active |   1 |
|  69 | JUDY GRAY      | 1031 Daugavpils Parkway     | 59025    | 107137400143 | B?char | Algeria        | active |   2 |
| 176 | JUNE CARROLL   | 757 Rustenburg Avenue       | 89668    | 506134035434 | Skikda | Algeria        | active |   1 |
| 320 | ANTHONY SCHWAB | 1892 Nabereznyje Telny Lane | 28396    | 478229987054 | Tafuna | American Samoa | active |   2 |
+-----+----------------+-----------------------------+----------+--------------+--------+----------------+--------+-----+
5 rows in set (0.00 sec)

mysql> select `cu`.`customer_id` AS `ID`,concat(`cu`.`first_name`,_utf8mb4' ',`cu`.`last_name`) AS `name`,`a`.`address` AS `address`,`a`.`postal_code` AS `zip code`,`a`.`phone` AS `phone`,`sakila`.`city`.`city` AS `city`,`sakila`.`country`.`country` AS `country`,if(`cu`.`active`,_utf8mb4'active',_utf8mb4'') AS `notes`,`cu`.`store_id` AS `SID` from (((`sakila`.`customer` `cu` inner join `sakila`.`address` `a` on((`cu`.`address_id` = `a`.`address_id`))) inner join `sakila`.`city` on((`a`.`city_id` = `sakila`.`city`.`city_id`))) inner join `sakila`.`country` on((`sakila`.`city`.`country_id` = `sakila`.`country`.`country_id`))) limit 5;
+-----+----------------+-----------------------------+----------+--------------+--------+----------------+--------+-----+
| ID  | name           | address                     | zip code | phone        | city   | country        | notes  | SID |
+-----+----------------+-----------------------------+----------+--------------+--------+----------------+--------+-----+
| 218 | VERA MCCOY     | 1168 Najafabad Parkway      | 40301    | 886649065861 | Kabul  | Afghanistan    | active |   1 |
| 441 | MARIO CHEATHAM | 1924 Shimonoseki Drive      | 52625    | 406784385440 | Batna  | Algeria        | active |   1 |
|  69 | JUDY GRAY      | 1031 Daugavpils Parkway     | 59025    | 107137400143 | B?char | Algeria        | active |   2 |
| 176 | JUNE CARROLL   | 757 Rustenburg Avenue       | 89668    | 506134035434 | Skikda | Algeria        | active |   1 |
| 320 | ANTHONY SCHWAB | 1892 Nabereznyje Telny Lane | 28396    | 478229987054 | Tafuna | American Samoa | active |   2 |
+-----+----------------+-----------------------------+----------+--------------+--------+----------------+--------+-----+
5 rows in set (0.00 sec)

```

```sql
mysql> SELECT * FROM (SELECT 1, 2, 3) AS t1;
+---+---+---+
| 1 | 2 | 3 |
+---+---+---+
| 1 | 2 | 3 |
+---+---+---+
1 row in set (0.01 sec)
```

```sql
mysql> CREATE TABLE t1 (i INT, j INT);
Query OK, 0 rows affected (0.02 sec)

mysql> CREATE TABLE t2 (k INT, j INT);
Query OK, 0 rows affected (0.02 sec)

mysql> INSERT INTO t1 VALUES(1, 1);
Query OK, 1 row affected (0.01 sec)

mysql> INSERT INTO t2 VALUES(1, 1);
Query OK, 1 row affected (0.00 sec)

mysql> SELECT * FROM t1 NATURAL JOIN t2;
+------+------+------+
| j    | i    | k    |
+------+------+------+
|    1 |    1 |    1 |
+------+------+------+
1 row in set (0.01 sec)

mysql> SELECT * FROM t1 JOIN t2 USING (j);
+------+------+------+
| j    | i    | k    |
+------+------+------+
|    1 |    1 |    1 |
+------+------+------+
1 row in set (0.00 sec)
```
ON 句は、そのオペランドのみを参照できます

```sql
mysql> CREATE TABLE t1 (i1 INT);
Query OK, 0 rows affected (0.02 sec)

mysql> CREATE TABLE t2 (i2 INT);
Query OK, 0 rows affected (0.02 sec)

mysql> CREATE TABLE t3 (i3 INT);
Query OK, 0 rows affected (0.02 sec)

mysql> SELECT * FROM t1 JOIN t2 ON (i1 = i3) JOIN t3;
ERROR 1054 (42S22): Unknown column 'i3' in 'on clause'
mysql> SELECT * FROM t1 JOIN t2 JOIN t3 ON (i1 = i3);
Empty set (0.00 sec)
```

```sql
mysql> CREATE TABLE t1 (i1 INT, j1 INT);
Query OK, 0 rows affected (0.02 sec)

mysql> CREATE TABLE t2 (i2 INT, j2 INT);
Query OK, 0 rows affected (0.02 sec)

mysql> CREATE TABLE t3 (i3 INT, j3 INT);
Query OK, 0 rows affected (0.02 sec)

mysql> INSERT INTO t1 VALUES(1, 1);
Query OK, 1 row affected (0.00 sec)

mysql> INSERT INTO t2 VALUES(1, 1);
Query OK, 1 row affected (0.00 sec)

mysql> INSERT INTO t3 VALUES(1, 1);
Query OK, 1 row affected (0.00 sec)

mysql> SELECT * FROM t1, t2 JOIN t3 ON (t1.i1 = t3.i3);
ERROR 1054 (42S22): Unknown column 't1.i1' in 'on clause'

mysql> SELECT * FROM (t1, t2) JOIN t3 ON (t1.i1 = t3.i3);
+------+------+------+------+------+------+
| i1   | j1   | i2   | j2   | i3   | j3   |
+------+------+------+------+------+------+
|    1 |    1 |    1 |    1 |    1 |    1 |
+------+------+------+------+------+------+
1 row in set (0.00 sec)

mysql> SELECT * FROM t1 JOIN t2 JOIN t3 ON (t1.i1 = t3.i3);
+------+------+------+------+------+------+
| i1   | j1   | i2   | j2   | i3   | j3   |
+------+------+------+------+------+------+
|    1 |    1 |    1 |    1 |    1 |    1 |
+------+------+------+------+------+------+
1 row in set (0.00 sec)
```

### 13.2.10.3 UNION 句
```sql
mysql> SELECT 1, 2 UNION SELECT 'a', 'b';
+---+---+
| 1 | 2 |
+---+---+
| 1 | 2 |
| a | b |
+---+---+
2 rows in set (0.00 sec)

mysql> SELECT REPEAT('a',1) UNION SELECT REPEAT('b',20);
+----------------------+
| REPEAT('a',1)        |
+----------------------+
| a                    |
| bbbbbbbbbbbbbbbbbbbb |
+----------------------+
2 rows in set (0.00 sec)
```

```sql
mysql> CREATE TABLE t1 (x INT, y INT);
Query OK, 0 rows affected (0.03 sec)

mysql> INSERT INTO t1 VALUES ROW(4,-2),ROW(5,9);
Query OK, 2 rows affected (0.00 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql>
mysql> CREATE TABLE t2 (a INT, b INT);
Query OK, 0 rows affected (0.03 sec)

mysql> INSERT INTO t2 VALUES ROW(1,2),ROW(3,4);
Query OK, 2 rows affected (0.00 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql> SELECT * FROM t1 UNION SELECT * FROM t2;
+------+------+
| x    | y    |
+------+------+
|    4 |   -2 |
|    5 |    9 |
|    1 |    2 |
|    3 |    4 |
+------+------+
4 rows in set (0.00 sec)

mysql> TABLE t1 UNION SELECT * FROM t2;
+------+------+
| x    | y    |
+------+------+
|    4 |   -2 |
|    5 |    9 |
|    1 |    2 |
|    3 |    4 |
+------+------+
4 rows in set (0.00 sec)

mysql> VALUES ROW(4,-2), ROW(5,9) UNION SELECT * FROM t2;
+----------+----------+
| column_0 | column_1 |
+----------+----------+
|        4 |       -2 |
|        5 |        9 |
|        1 |        2 |
|        3 |        4 |
+----------+----------+
4 rows in set (0.00 sec)

mysql> SELECT * FROM t1 UNION TABLE t2;
+------+------+
| x    | y    |
+------+------+
|    4 |   -2 |
|    5 |    9 |
|    1 |    2 |
|    3 |    4 |
+------+------+
4 rows in set (0.00 sec)

mysql> TABLE t1 UNION TABLE t2;
+------+------+
| x    | y    |
+------+------+
|    4 |   -2 |
|    5 |    9 |
|    1 |    2 |
|    3 |    4 |
+------+------+
4 rows in set (0.00 sec)

mysql> VALUES ROW(4,-2), ROW(5,9) UNION TABLE t2;
+----------+----------+
| column_0 | column_1 |
+----------+----------+
|        4 |       -2 |
|        5 |        9 |
|        1 |        2 |
|        3 |        4 |
+----------+----------+
4 rows in set (0.00 sec)

mysql> SELECT * FROM t1 UNION VALUES ROW(4,-2),ROW(5,9);
+------+------+
| x    | y    |
+------+------+
|    4 |   -2 |
|    5 |    9 |
+------+------+
2 rows in set (0.00 sec)

mysql> TABLE t1 UNION VALUES ROW(4,-2),ROW(5,9);
+------+------+
| x    | y    |
+------+------+
|    4 |   -2 |
|    5 |    9 |
+------+------+
2 rows in set (0.00 sec)

mysql> VALUES ROW(4,-2), ROW(5,9) UNION VALUES ROW(4,-2),ROW(5,9);
+----------+----------+
| column_0 | column_1 |
+----------+----------+
|        4 |       -2 |
|        5 |        9 |
+----------+----------+
2 rows in set (0.00 sec)
```

```sql
mysql> (SELECT 1 UNION SELECT 1) UNION SELECT 1;
+---+
| 1 |
+---+
| 1 |
+---+
1 row in set (0.00 sec)
```
### 13.2.10.4 カッコで囲まれたクエリー式
 最も単純なのは、カッコで囲まれたクエリー式に単一の SELECT が含まれ、次のオプション句は含まれないことです
 ```sql
 mysql> (SELECT 1);
+---+
| 1 |
+---+
| 1 |
+---+
1 row in set (0.00 sec)

mysql> (SELECT * FROM INFORMATION_SCHEMA.SCHEMATA WHERE SCHEMA_NAME = 'mysql');
+--------------+-------------+----------------------------+------------------------+----------+--------------------+
| CATALOG_NAME | SCHEMA_NAME | DEFAULT_CHARACTER_SET_NAME | DEFAULT_COLLATION_NAME | SQL_PATH | DEFAULT_ENCRYPTION |
+--------------+-------------+----------------------------+------------------------+----------+--------------------+
| def          | mysql       | utf8mb4                    | utf8mb4_0900_ai_ci     |     NULL | NO                 |
+--------------+-------------+----------------------------+------------------------+----------+--------------------+
1 row in set (0.02 sec)
```
カッコで囲まれたクエリー式には、複数の SELECT ステートメントで構成される UNION を含めることもでき、オプションの句の一部またはすべてで終わることができます
```sql
mysql> (SELECT 1 AS result UNION SELECT 2);
+--------+
| result |
+--------+
|      1 |
|      2 |
+--------+
2 rows in set (0.00 sec)

mysql> (SELECT 1 AS result UNION SELECT 2) LIMIT 1;
+--------+
| result |
+--------+
|      1 |
+--------+
1 row in set (0.00 sec)

mysql> (SELECT 1 AS result UNION SELECT 2) LIMIT 1 OFFSET 1;
+--------+
| result |
+--------+
|      2 |
+--------+
1 row in set (0.00 sec)

mysql> (SELECT 1 AS result UNION SELECT 2)
    ->        ORDER BY result DESC LIMIT 1;
+--------+
| result |
+--------+
|      2 |
+--------+
1 row in set (0.00 sec)

mysql> (SELECT 1 AS result UNION SELECT 2)
    ->        ORDER BY result DESC LIMIT 1 OFFSET 1;
+--------+
| result |
+--------+
|      1 |
+--------+
1 row in set (0.00 sec)

mysql> (SELECT 1 AS result UNION SELECT 3 UNION SELECT 2)
    ->        ORDER BY result LIMIT 1 OFFSET 1 INTO @var;
Query OK, 1 row affected (0.00 sec)

mysql> SELECT @var;
+------+
| @var |
+------+
|    2 |
+------+
1 row in set (0.00 sec)
```
各クエリーブロックに LIMIT を適用するには
```sql
mysql> (SELECT 1 LIMIT 1) UNION (SELECT 2 LIMIT 1);
+---+
| 1 |
+---+
| 1 |
| 2 |
+---+
2 rows in set (0.00 sec)
```
クエリーブロックとクエリー式全体の両方に LIMIT を適用するには
```sql
mysql> (SELECT 1 LIMIT 1) UNION (SELECT 2 LIMIT 1) LIMIT 1;
+---+
| 1 |
+---+
| 1 |
+---+
1 row in set (0.00 sec)
```
クエリー式全体に LIMIT を適用するには
```sql
mysql> SELECT 1 UNION SELECT 2 LIMIT 1;
+---+
| 1 |
+---+
| 1 |
+---+
1 row in set (0.00 sec)
```
ハイブリッド強制: 最初のクエリーブロックおよびクエリー式全体に対する LIMIT
```sql
mysql> (SELECT 1 LIMIT 1) UNION SELECT 2 LIMIT 1;
+---+
| 1 |
+---+
| 1 |
+---+
1 row in set (0.00 sec)
```
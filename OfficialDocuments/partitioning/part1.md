### 24.1 MySQL のパーティショニングの概要

次の例では、ハッシュによって 6 つのパーティションにパーティション化され、(default_storage_engine の値に関係なく) InnoDB ストレージエンジンを使用するテーブルを作成する方法を示します

```sql
mysql> CREATE TABLE ti (id INT, amount DECIMAL(7,2), tr_date DATE)
    ->     ENGINE=INNODB
    ->     PARTITION BY HASH( MONTH(tr_date) )
    ->     PARTITIONS 6;
Query OK, 0 rows affected (0.13 sec)
```

### 24.2.1 RANGE パーティショニング

範囲によってパーティション化されるテーブルは、各パーティションに含まれる行のパーティショニング式値が指定された範囲に収まるようにパーティション化されます

```sql
mysql> CREATE TABLE employees (
    ->     id INT NOT NULL,
    ->     fname VARCHAR(30),
    ->     lname VARCHAR(30),
    ->     hired DATE NOT NULL DEFAULT '1970-01-01',
    ->     separated DATE NOT NULL DEFAULT '9999-12-31',
    ->     job_code INT NOT NULL,
    ->     store_id INT NOT NULL
    -> );
Query OK, 0 rows affected (0.02 sec)
```

```sql
mysql> CREATE TABLE employees (
    ->     id INT NOT NULL,
    ->     fname VARCHAR(30),
    ->     lname VARCHAR(30),
    ->     hired DATE NOT NULL DEFAULT '1970-01-01',
    ->     separated DATE NOT NULL DEFAULT '9999-12-31',
    ->     job_code INT NOT NULL,
    ->     store_id INT NOT NULL
    -> )
    -> PARTITION BY RANGE (store_id) (
    ->     PARTITION p0 VALUES LESS THAN (6),
    ->     PARTITION p1 VALUES LESS THAN (11),
    ->     PARTITION p2 VALUES LESS THAN (16),
    ->     PARTITION p3 VALUES LESS THAN (21)
    -> );
Query OK, 0 rows affected (0.07 sec)
```

```Sql
mysql> CREATE TABLE employees (
    ->     id INT NOT NULL,
    ->     fname VARCHAR(30),
    ->     lname VARCHAR(30),
    ->     hired DATE NOT NULL DEFAULT '1970-01-01',
    ->     separated DATE NOT NULL DEFAULT '9999-12-31',
    ->     job_code INT NOT NULL,
    ->     store_id INT NOT NULL
    -> )
    -> PARTITION BY RANGE (store_id) (
    ->     PARTITION p0 VALUES LESS THAN (6),
    ->     PARTITION p1 VALUES LESS THAN (11),
    ->     PARTITION p2 VALUES LESS THAN (16),
    ->     PARTITION p3 VALUES LESS THAN MAXVALUE
    -> );
Query OK, 0 rows affected (0.06 sec)
```

```Sql
mysql> CREATE TABLE employees (
    ->     id INT NOT NULL,
    ->     fname VARCHAR(30),
    ->     lname VARCHAR(30),
    ->     hired DATE NOT NULL DEFAULT '1970-01-01',
    ->     separated DATE NOT NULL DEFAULT '9999-12-31',
    ->     job_code INT,
    ->     store_id INT
    -> )
    -> PARTITION BY RANGE ( YEAR(separated) ) (
    ->     PARTITION p0 VALUES LESS THAN (1991),
    ->     PARTITION p1 VALUES LESS THAN (1996),
    ->     PARTITION p2 VALUES LESS THAN (2001),
    ->     PARTITION p3 VALUES LESS THAN MAXVALUE
    -> );
Query OK, 0 rows affected (0.06 sec)
```

```sql
mysql> CREATE TABLE quarterly_report_status (
    ->     report_id INT NOT NULL,
    ->     report_status VARCHAR(20) NOT NULL,
    ->     report_updated TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
    -> )
    -> PARTITION BY RANGE ( UNIX_TIMESTAMP(report_updated) ) (
    ->     PARTITION p0 VALUES LESS THAN ( UNIX_TIMESTAMP('2008-01-01 00:00:00') ),
    ->     PARTITION p1 VALUES LESS THAN ( UNIX_TIMESTAMP('2008-04-01 00:00:00') ),
    ->     PARTITION p2 VALUES LESS THAN ( UNIX_TIMESTAMP('2008-07-01 00:00:00') ),
    ->     PARTITION p3 VALUES LESS THAN ( UNIX_TIMESTAMP('2008-10-01 00:00:00') ),
    ->     PARTITION p4 VALUES LESS THAN ( UNIX_TIMESTAMP('2009-01-01 00:00:00') ),
    ->     PARTITION p5 VALUES LESS THAN ( UNIX_TIMESTAMP('2009-04-01 00:00:00') ),
    ->     PARTITION p6 VALUES LESS THAN ( UNIX_TIMESTAMP('2009-07-01 00:00:00') ),
    ->     PARTITION p7 VALUES LESS THAN ( UNIX_TIMESTAMP('2009-10-01 00:00:00') ),
    ->     PARTITION p8 VALUES LESS THAN ( UNIX_TIMESTAMP('2010-01-01 00:00:00') ),
    ->     PARTITION p9 VALUES LESS THAN (MAXVALUE)
    -> );
Query OK, 0 rows affected (0.14 sec)
```

### 24.2.2 LIST パーティショニング

MySQL の LIST パーティショニングは、多くの点で RANGE パーティショニングに似ています

```sql
mysql> CREATE TABLE employees (
    ->     id INT NOT NULL,
    ->     fname VARCHAR(30),
    ->     lname VARCHAR(30),
    ->     hired DATE NOT NULL DEFAULT '1970-01-01',
    ->     separated DATE NOT NULL DEFAULT '9999-12-31',
    ->     job_code INT,
    ->     store_id INT
    -> )
    -> PARTITION BY LIST(store_id) (
    ->     PARTITION pNorth VALUES IN (3,5,6,9,17),
    ->     PARTITION pEast VALUES IN (1,2,10,11,19,20),
    ->     PARTITION pWest VALUES IN (4,12,13,14,18),
    ->     PARTITION pCentral VALUES IN (7,8,15,16)
    -> );
Query OK, 0 rows affected (0.06 sec)
```

```sql
mysql> CREATE TABLE h2 (
    ->        c1 INT,
    ->        c2 INT
    ->      )
    ->      PARTITION BY LIST(c1) (
    ->        PARTITION p0 VALUES IN (1, 4, 7),
    ->        PARTITION p1 VALUES IN (2, 5, 8)
    ->      );
Query OK, 0 rows affected (0.04 sec)

mysql> INSERT IGNORE INTO h2 VALUES (2, 5), (6, 10), (7, 5), (3, 1), (1, 9);
Query OK, 3 rows affected, 2 warnings (0.02 sec)
Records: 5  Duplicates: 2  Warnings: 2

mysql> SELECT * FROM h2;
+------+------+
| c1   | c2   |
+------+------+
|    7 |    5 |
|    1 |    9 |
|    2 |    5 |
+------+------+
3 rows in set (0.00 sec)
```

### 24.2.3.1 RANGE COLUMNS パーティショニング

RANGE COLUMNS パーティショニングは RANGE パーティショニングに似ていますが、複数のカラム値に基づく範囲を使用してパーティションを定義できます

```Sql
mysql> CREATE TABLE rcx (
    ->          a INT,
    ->          b INT,
    ->          c CHAR(3),
    ->          d INT
    ->      )
    ->      PARTITION BY RANGE COLUMNS(a,d,c) (
    ->          PARTITION p0 VALUES LESS THAN (5,10,'ggg'),
    ->          PARTITION p1 VALUES LESS THAN (10,20,'mmm'),
    ->          PARTITION p2 VALUES LESS THAN (15,30,'sss'),
    ->          PARTITION p3 VALUES LESS THAN (MAXVALUE,MAXVALUE,MAXVALUE)
    ->      );
Query OK, 0 rows affected (0.06 sec)
```

```sql
mysql> CREATE TABLE r1 (
    ->     a INT,
    ->     b INT
    -> )
    -> PARTITION BY RANGE (a)  (
    ->     PARTITION p0 VALUES LESS THAN (5),
    ->     PARTITION p1 VALUES LESS THAN (MAXVALUE)
    -> );
Query OK, 0 rows affected (0.04 sec)

mysql> INSERT INTO r1 VALUES (5,10), (5,11), (5,12);
Query OK, 3 rows affected (0.01 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql> SELECT PARTITION_NAME,TABLE_ROWS
    ->          FROM INFORMATION_SCHEMA.PARTITIONS
    ->          WHERE TABLE_NAME = 'r1';
+----------------+------------+
| PARTITION_NAME | TABLE_ROWS |
+----------------+------------+
| p0             |          0 |
| p1             |          3 |
+----------------+------------+
2 rows in set (0.02 sec)
```

```sql
mysql> CREATE TABLE rc1 (
    ->     a INT,
    ->     b INT
    -> )
    -> PARTITION BY RANGE COLUMNS(a, b) (
    ->     PARTITION p0 VALUES LESS THAN (5, 12),
    ->     PARTITION p3 VALUES LESS THAN (MAXVALUE, MAXVALUE)
    -> );
Query OK, 0 rows affected (0.04 sec)

mysql> INSERT INTO rc1 VALUES (5,10), (5,11), (5,12);
Query OK, 3 rows affected (0.01 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql> SELECT PARTITION_NAME,TABLE_ROWS
    ->          FROM INFORMATION_SCHEMA.PARTITIONS
    ->          WHERE TABLE_NAME = 'rc1';
+----------------+------------+
| PARTITION_NAME | TABLE_ROWS |
+----------------+------------+
| p0             |          2 |
| p3             |          1 |
+----------------+------------+
2 rows in set (0.00 sec)

mysql> SELECT (5,10) < (5,12), (5,11) < (5,12), (5,12) < (5,12);
+-----------------+-----------------+-----------------+
| (5,10) < (5,12) | (5,11) < (5,12) | (5,12) < (5,12) |
+-----------------+-----------------+-----------------+
|               1 |               1 |               0 |
+-----------------+-----------------+-----------------+
1 row in set (0.00 sec)
```

```sql
mysql> CREATE TABLE rx (
    ->     a INT,
    ->     b INT
    -> )
    -> PARTITION BY RANGE COLUMNS (a)  (
    ->     PARTITION p0 VALUES LESS THAN (5),
    ->     PARTITION p1 VALUES LESS THAN (MAXVALUE)
    -> );
Query OK, 0 rows affected (0.04 sec)

mysql> INSERT INTO rx VALUES (5,10), (5,11), (5,12);
Query OK, 3 rows affected (0.01 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql> SELECT PARTITION_NAME,TABLE_ROWS
    ->          FROM INFORMATION_SCHEMA.PARTITIONS
    ->          WHERE TABLE_NAME = 'rx';
+----------------+------------+
| PARTITION_NAME | TABLE_ROWS |
+----------------+------------+
| p0             |          0 |
| p1             |          3 |
+----------------+------------+
2 rows in set (0.00 sec)
```

```sql
mysql> CREATE TABLE rc2 (
    ->     a INT,
    ->     b INT
    -> )
    -> PARTITION BY RANGE COLUMNS(a,b) (
    ->     PARTITION p0 VALUES LESS THAN (0,10),
    ->     PARTITION p1 VALUES LESS THAN (10,20),
    ->     PARTITION p2 VALUES LESS THAN (10,30),
    ->     PARTITION p3 VALUES LESS THAN (MAXVALUE,MAXVALUE)
    ->  );
Query OK, 0 rows affected (0.07 sec)

mysql>
mysql> CREATE TABLE rc3 (
    ->     a INT,
    ->     b INT
    -> )
    -> PARTITION BY RANGE COLUMNS(a,b) (
    ->     PARTITION p0 VALUES LESS THAN (0,10),
    ->     PARTITION p1 VALUES LESS THAN (10,20),
    ->     PARTITION p2 VALUES LESS THAN (10,30),
    ->     PARTITION p3 VALUES LESS THAN (10,35),
    ->     PARTITION p4 VALUES LESS THAN (20,40),
    ->     PARTITION p5 VALUES LESS THAN (MAXVALUE,MAXVALUE)
    ->  );
Query OK, 0 rows affected (0.08 sec)
```

```sql
mysql> CREATE TABLE rc4 (
    ->     a INT,
    ->     b INT,
    ->     c INT
    -> )
    -> PARTITION BY RANGE COLUMNS(a,b,c) (
    ->     PARTITION p0 VALUES LESS THAN (0,25,50),
    ->     PARTITION p1 VALUES LESS THAN (10,20,100),
    ->     PARTITION p2 VALUES LESS THAN (10,30,50),
    ->     PARTITION p3 VALUES LESS THAN (MAXVALUE,MAXVALUE,MAXVALUE)
    ->  );
Query OK, 0 rows affected (0.07 sec)
```

```Sql
mysql> CREATE TABLE employees_by_lname (
    ->     id INT NOT NULL,
    ->     fname VARCHAR(30),
    ->     lname VARCHAR(30),
    ->     hired DATE NOT NULL DEFAULT '1970-01-01',
    ->     separated DATE NOT NULL DEFAULT '9999-12-31',
    ->     job_code INT NOT NULL,
    ->     store_id INT NOT NULL
    -> )
    -> PARTITION BY RANGE COLUMNS (lname)  (
    ->     PARTITION p0 VALUES LESS THAN ('g'),
    ->     PARTITION p1 VALUES LESS THAN ('m'),
    ->     PARTITION p2 VALUES LESS THAN ('t'),
    ->     PARTITION p3 VALUES LESS THAN (MAXVALUE)
    -> );
Query OK, 0 rows affected (0.10 sec)

mysql> ALTER TABLE employees PARTITION BY RANGE COLUMNS (lname)  (
    ->     PARTITION p0 VALUES LESS THAN ('g'),
    ->     PARTITION p1 VALUES LESS THAN ('m'),
    ->     PARTITION p2 VALUES LESS THAN ('t'),
    ->     PARTITION p3 VALUES LESS THAN (MAXVALUE)
    -> );
Query OK, 0 rows affected (0.20 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE employees PARTITION BY RANGE COLUMNS (hired)  (
    ->     PARTITION p0 VALUES LESS THAN ('1970-01-01'),
    ->     PARTITION p1 VALUES LESS THAN ('1980-01-01'),
    ->     PARTITION p2 VALUES LESS THAN ('1990-01-01'),
    ->     PARTITION p3 VALUES LESS THAN ('2000-01-01'),
    ->     PARTITION p4 VALUES LESS THAN ('2010-01-01'),
    ->     PARTITION p5 VALUES LESS THAN (MAXVALUE)
    -> );
Query OK, 0 rows affected (0.20 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

### 24.2.3.2 LIST COLUMNS パーティショニング

これは LIST パーティショニングのバリアントで、複数のカラムをパーティションキーとして使用でき、整数型以外のデータ型のカラムをパーティショニングカラムとして使用できます

````sql
mysql> CREATE TABLE customers_1 (
    ->     first_name VARCHAR(25),
    ->     last_name VARCHAR(25),
    ->     street_1 VARCHAR(30),
    ->     street_2 VARCHAR(30),
    ->     city VARCHAR(15),
    ->     renewal DATE
    -> )
    -> PARTITION BY LIST COLUMNS(city) (
    ->     PARTITION pRegion_1 VALUES IN('Oskarshamn', 'Högsby', 'Mönsterås'),
    ->     PARTITION pRegion_2 VALUES IN('Vimmerby', 'Hultsfred', 'Västervik'),
    ->     PARTITION pRegion_3 VALUES IN('Nässjö', 'Eksjö', 'Vetlanda'),
    ->     PARTITION pRegion_4 VALUES IN('Uppvidinge', 'Alvesta', 'Växjo')
    -> );
Query OK, 0 rows affected (0.09 sec)

mysql> CREATE TABLE customers_2 (
    ->     first_name VARCHAR(25),
    ->     last_name VARCHAR(25),
    ->     street_1 VARCHAR(30),
    ->     street_2 VARCHAR(30),
    ->     city VARCHAR(15),
    ->     renewal DATE
    -> )
    -> PARTITION BY LIST COLUMNS(renewal) (
    ->     PARTITION pWeek_1 VALUES IN('2010-02-01', '2010-02-02', '2010-02-03',
    ->         '2010-02-04', '2010-02-05', '2010-02-06', '2010-02-07'),
    ->     PARTITION pWeek_2 VALUES IN('2010-02-08', '2010-02-09', '2010-02-10',
    ->         '2010-02-11', '2010-02-12', '2010-02-13', '2010-02-14'),
    ->     PARTITION pWeek_3 VALUES IN('2010-02-15', '2010-02-16', '2010-02-17',
    ->         '2010-02-18', '2010-02-19', '2010-02-20', '2010-02-21'),
    ->     PARTITION pWeek_4 VALUES IN('2010-02-22', '2010-02-23', '2010-02-24',
    ->         '2010-02-25', '2010-02-26', '2010-02-27', '2010-02-28')
    -> );
Query OK, 0 rows affected (0.06 sec)

mysql> CREATE TABLE customers_3 (
    ->     first_name VARCHAR(25),
    ->     last_name VARCHAR(25),
    ->     street_1 VARCHAR(30),
    ->     street_2 VARCHAR(30),
    ->     city VARCHAR(15),
    ->     renewal DATE
    -> )
    -> PARTITION BY RANGE COLUMNS(renewal) (
    ->     PARTITION pWeek_1 VALUES LESS THAN('2010-02-09'),
    ->     PARTITION pWeek_2 VALUES LESS THAN('2010-02-15'),
    ->     PARTITION pWeek_3 VALUES LESS THAN('2010-02-22'),
    ->     PARTITION pWeek_4 VALUES LESS THAN('2010-03-01')
    -> );
Query OK, 0 rows affected (0.08 sec)```
````

### 24.2.4 HASH パーティショニング

HASH によるパーティショニングは、事前に決められた数のパーティションにデータを均等に配分するために主に使用されます

```sql
mysql> CREATE TABLE employees (
    ->     id INT NOT NULL,
    ->     fname VARCHAR(30),
    ->     lname VARCHAR(30),
    ->     hired DATE NOT NULL DEFAULT '1970-01-01',
    ->     separated DATE NOT NULL DEFAULT '9999-12-31',
    ->     job_code INT,
    ->     store_id INT
    -> )
    -> PARTITION BY HASH(store_id)
    -> PARTITIONS 4;
Query OK, 0 rows affected (0.11 sec)
```

```sql
mysql> CREATE TABLE employees (
    ->     id INT NOT NULL,
    ->     fname VARCHAR(30),
    ->     lname VARCHAR(30),
    ->     hired DATE NOT NULL DEFAULT '1970-01-01',
    ->     separated DATE NOT NULL DEFAULT '9999-12-31',
    ->     job_code INT,
    ->     store_id INT
    -> )
    -> PARTITION BY HASH( YEAR(hired) )
    -> PARTITIONS 4;
Query OK, 0 rows affected (0.07 sec)
```

### 24.2.4.1 LINEAR HASH パーティショニング

MySQL は線形ハッシュもサポートしています。通常のハッシュと異なるところは、線形ハッシュは線形二乗アルゴリズムを使用し、通常のハッシュはハッシュ関数の値の法を使用することです

```Sql
mysql> CREATE TABLE employees (
    ->     id INT NOT NULL,
    ->     fname VARCHAR(30),
    ->     lname VARCHAR(30),
    ->     hired DATE NOT NULL DEFAULT '1970-01-01',
    ->     separated DATE NOT NULL DEFAULT '9999-12-31',
    ->     job_code INT,
    ->     store_id INT
    -> )
    -> PARTITION BY LINEAR HASH( YEAR(hired) )
    -> PARTITIONS 4;
Query OK, 0 rows affected (0.07 sec)
```

```sql
mysql> CREATE TABLE t1 (col1 INT, col2 CHAR(5), col3 DATE)
    ->     PARTITION BY LINEAR HASH( YEAR(col3) )
    ->     PARTITIONS 6;
Query OK, 0 rows affected (0.08 sec)
```

### 24.2.5 KEY パーティショニング

キーによるパーティショニングはハッシュによるパーティショニングと似ていますが、ハッシュパーティショニングはユーザー定義の式を使用し、キーパーティショニング用のハッシュ関数は MySQL サーバーによって提供されます

```sql
mysql> CREATE TABLE k1 (
    ->     id INT NOT NULL PRIMARY KEY,
    ->     name VARCHAR(20)
    -> )
    -> PARTITION BY KEY()
    -> PARTITIONS 2;
Query OK, 0 rows affected (0.04 sec)
```

主キーはないけれども一意キーはある場合は、パーティショニングキーに一意キーが使用されます

```sql
mysql> CREATE TABLE k1 (
    ->     id INT NOT NULL,
    ->     name VARCHAR(20),
    ->     UNIQUE KEY (id)
    -> )
    -> PARTITION BY KEY()
    -> PARTITIONS 2;
Query OK, 0 rows affected (0.15 sec)
```

リニアキーによってテーブルをパーティション化することもできます。 次に、単純な例を示します

```sql
mysql> CREATE TABLE tk (
    ->     col1 INT NOT NULL,
    ->     col2 CHAR(5),
    ->     col3 DATE
    -> )
    -> PARTITION BY LINEAR KEY (col1)
    -> PARTITIONS 3;
Query OK, 0 rows affected (0.05 sec)
```

### 24.2.6 サブパーティショニング

サブパーティショニング (複合パーティショニングとも呼ばれます) は、パーティション化されたテーブルの各パーティションをさらに分割することです

```sql
mysql> CREATE TABLE ts (id INT, purchased DATE)
    ->     PARTITION BY RANGE( YEAR(purchased) )
    ->     SUBPARTITION BY HASH( TO_DAYS(purchased) )
    ->     SUBPARTITIONS 2 (
    ->         PARTITION p0 VALUES LESS THAN (1990),
    ->         PARTITION p1 VALUES LESS THAN (2000),
    ->         PARTITION p2 VALUES LESS THAN MAXVALUE
    ->     );
Query OK, 0 rows affected (0.09 sec)
```

サブパーティションを明示的に定義することもできます

```sql
mysql> CREATE TABLE ts (id INT, purchased DATE)
    ->     PARTITION BY RANGE( YEAR(purchased) )
    ->     SUBPARTITION BY HASH( TO_DAYS(purchased) ) (
    ->         PARTITION p0 VALUES LESS THAN (1990) (
    ->             SUBPARTITION s0,
    ->             SUBPARTITION s1
    ->         ),
    ->         PARTITION p1 VALUES LESS THAN (2000) (
    ->             SUBPARTITION s2,
    ->             SUBPARTITION s3
    ->         ),
    ->         PARTITION p2 VALUES LESS THAN MAXVALUE (
    ->             SUBPARTITION s4,
    ->             SUBPARTITION s5
    ->         )
    ->     );
Query OK, 0 rows affected (0.10 sec)
```

### 24.2.7 MySQL パーティショニングによる NULL の扱い

```sql
mysql> CREATE TABLE t1 (
    ->          c1 INT,
    ->          c2 VARCHAR(20)
    ->      )
    ->      PARTITION BY RANGE(c1) (
    ->          PARTITION p0 VALUES LESS THAN (0),
    ->          PARTITION p1 VALUES LESS THAN (10),
    ->          PARTITION p2 VALUES LESS THAN MAXVALUE
    ->      );
Query OK, 0 rows affected (0.06 sec)

mysql> CREATE TABLE t2 (
    ->          c1 INT,
    ->          c2 VARCHAR(20)
    ->      )
    ->      PARTITION BY RANGE(c1) (
    ->          PARTITION p0 VALUES LESS THAN (-5),
    ->          PARTITION p1 VALUES LESS THAN (0),
    ->          PARTITION p2 VALUES LESS THAN (10),
    ->          PARTITION p3 VALUES LESS THAN MAXVALUE
    ->      );
Query OK, 0 rows affected (0.06 sec)

mysql> SELECT TABLE_NAME, PARTITION_NAME, TABLE_ROWS, AVG_ROW_LENGTH, DATA_LENGTH
    ->         FROM INFORMATION_SCHEMA.PARTITIONS
    ->         WHERE TABLE_SCHEMA = 'test' AND TABLE_NAME LIKE 't_';
+------------+----------------+------------+----------------+-------------+
| TABLE_NAME | PARTITION_NAME | TABLE_ROWS | AVG_ROW_LENGTH | DATA_LENGTH |
+------------+----------------+------------+----------------+-------------+
| t1         | p0             |          0 |              0 |       16384 |
| t1         | p1             |          0 |              0 |       16384 |
| t1         | p2             |          0 |              0 |       16384 |
| t2         | p0             |          0 |              0 |       16384 |
| t2         | p1             |          0 |              0 |       16384 |
| t2         | p2             |          0 |              0 |       16384 |
| t2         | p3             |          0 |              0 |       16384 |
| t3         | NULL           |          0 |              8 |           0 |
| t4         | NULL           |          0 |              0 |           0 |
| ti         | p0             |          0 |              0 |       16384 |
| ti         | p1             |          0 |              0 |       16384 |
| ti         | p2             |          0 |              0 |       16384 |
| ti         | p3             |          0 |              0 |       16384 |
| ti         | p4             |          0 |              0 |       16384 |
| ti         | p5             |          0 |              0 |       16384 |
| tk         | p0             |          0 |              0 |       16384 |
| tk         | p1             |          0 |              0 |       16384 |
| tk         | p2             |          0 |              0 |       16384 |
| ts         | p0             |          0 |              0 |       16384 |
| ts         | p0             |          0 |              0 |       16384 |
| ts         | p1             |          0 |              0 |       16384 |
| ts         | p1             |          0 |              0 |       16384 |
| ts         | p2             |          0 |              0 |       16384 |
| ts         | p2             |          0 |              0 |       16384 |
+------------+----------------+------------+----------------+-------------+
24 rows in set (0.03 sec)
```

```sql
mysql> SELECT * FROM t1;
+------+--------+
| c1   | c2     |
+------+--------+
| NULL | mothra |
+------+--------+
1 row in set (0.00 sec)

mysql> SELECT * FROM t2;
+------+--------+
| c1   | c2     |
+------+--------+
| NULL | mothra |
+------+--------+
1 row in set (0.00 sec)
```

挿入された行を格納するためにどのパーティションが使用されたかについては、前のクエリーを INFORMATION_SCHEMA.PARTITIONS に対して再実行して出力を検査することで確認できます

```sql
mysql> SELECT TABLE_NAME, PARTITION_NAME, TABLE_ROWS, AVG_ROW_LENGTH, DATA_LENGTH
    ->         FROM INFORMATION_SCHEMA.PARTITIONS
    ->         WHERE TABLE_SCHEMA = 'test' AND TABLE_NAME LIKE 't_';
+------------+----------------+------------+----------------+-------------+
| TABLE_NAME | PARTITION_NAME | TABLE_ROWS | AVG_ROW_LENGTH | DATA_LENGTH |
+------------+----------------+------------+----------------+-------------+
| t1         | p0             |          1 |          16384 |       16384 |
| t1         | p1             |          0 |              0 |       16384 |
| t1         | p2             |          0 |              0 |       16384 |
| t2         | p0             |          1 |          16384 |       16384 |
| t2         | p1             |          0 |              0 |       16384 |
| t2         | p2             |          0 |              0 |       16384 |
| t2         | p3             |          0 |              0 |       16384 |
| t3         | NULL           |          0 |              8 |           0 |
| t4         | NULL           |          0 |              0 |           0 |
| ti         | p0             |          0 |              0 |       16384 |
| ti         | p1             |          0 |              0 |       16384 |
| ti         | p2             |          0 |              0 |       16384 |
| ti         | p3             |          0 |              0 |       16384 |
| ti         | p4             |          0 |              0 |       16384 |
| ti         | p5             |          0 |              0 |       16384 |
| tk         | p0             |          0 |              0 |       16384 |
| tk         | p1             |          0 |              0 |       16384 |
| tk         | p2             |          0 |              0 |       16384 |
| ts         | p0             |          0 |              0 |       16384 |
| ts         | p0             |          0 |              0 |       16384 |
| ts         | p1             |          0 |              0 |       16384 |
| ts         | p1             |          0 |              0 |       16384 |
| ts         | p2             |          0 |              0 |       16384 |
| ts         | p2             |          0 |              0 |       16384 |
+------------+----------------+------------+----------------+-------------+
24 rows in set (0.01 sec)
```

これらのパーティションを削除してから SELECT ステートメントを再実行することで、これらの行が各テーブルの最小番号のパーティションに格納されたことを示すこともできます

```sql
mysql> ALTER TABLE t1 DROP PARTITION p0;
Query OK, 0 rows affected (0.04 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE t2 DROP PARTITION p0;
Query OK, 0 rows affected (0.02 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> SELECT * FROM t1;
Empty set (0.00 sec)

mysql> SELECT * FROM t2;
Empty set (0.00 sec)
```

SQL 関数を使用するパーティショニング式の場合も、NULL はこのように扱われます。 次のような CREATE TABLE ステートメントを使用してテーブルを定義するとします

```sql
mysql> CREATE TABLE tndate (
    ->     id INT,
    ->     dt DATE
    -> )
    -> PARTITION BY RANGE( YEAR(dt) ) (
    ->     PARTITION p0 VALUES LESS THAN (1990),
    ->     PARTITION p1 VALUES LESS THAN (2000),
    ->     PARTITION p2 VALUES LESS THAN MAXVALUE
    -> );
Query OK, 0 rows affected (0.06 sec)

```

```sql
mysql> CREATE TABLE tndate (
    ->     id INT,
    ->     dt DATE
    -> )
    -> PARTITION BY RANGE( YEAR(dt) ) (
    ->     PARTITION p0 VALUES LESS THAN (1990),
    ->     PARTITION p1 VALUES LESS THAN (2000),
    ->     PARTITION p2 VALUES LESS THAN MAXVALUE
    -> );
Query OK, 0 rows affected (0.06 sec)

mysql>  CREATE TABLE ts1 (
    ->          c1 INT,
    ->          c2 VARCHAR(20)
    ->      )
    ->      PARTITION BY LIST(c1) (
    ->          PARTITION p0 VALUES IN (0, 3, 6),
    ->          PARTITION p1 VALUES IN (1, 4, 7),
    ->          PARTITION p2 VALUES IN (2, 5, 8)
    ->      );
Query OK, 0 rows affected (0.05 sec)

mysql> INSERT INTO ts1 VALUES (9, 'mothra');
ERROR 1526 (HY000): Table has no partition for value 9
mysql> INSERT INTO ts1 VALUES (NULL, 'mothra');
ERROR 1526 (HY000): Table has no partition for value NULL
```

ts1 に挿入できるのは、c1 値が 0 以上 8 以下の行のみです。 NULL は、数値 9 と同様にこの範囲を外れます。 NULL が含まれる値リストを持つテーブル ts2 および ts3 は次のように作成できます

```sql
mysql> CREATE TABLE ts2 (
    ->          c1 INT,
    ->          c2 VARCHAR(20)
    ->      )
    ->      PARTITION BY LIST(c1) (
    ->          PARTITION p0 VALUES IN (0, 3, 6),
    ->          PARTITION p1 VALUES IN (1, 4, 7),
    ->          PARTITION p2 VALUES IN (2, 5, 8),
    ->          PARTITION p3 VALUES IN (NULL)
    ->      );
Query OK, 0 rows affected (0.07 sec)

mysql> CREATE TABLE ts3 (
    ->          c1 INT,
    ->          c2 VARCHAR(20)
    ->      )
    ->      PARTITION BY LIST(c1) (
    ->          PARTITION p0 VALUES IN (0, 3, 6),
    ->          PARTITION p1 VALUES IN (1, 4, 7, NULL),
    ->          PARTITION p2 VALUES IN (2, 5, 8)
    ->      );
Query OK, 0 rows affected (0.05 sec)

mysql> INSERT INTO ts2 VALUES (NULL, 'mothra');
Query OK, 1 row affected (0.01 sec)

mysql> INSERT INTO ts3 VALUES (NULL, 'mothra');
Query OK, 1 row affected (0.01 sec)
```

```sql
mysql> SELECT TABLE_NAME, PARTITION_NAME, TABLE_ROWS, AVG_ROW_LENGTH, DATA_LENGTH
    ->         FROM INFORMATION_SCHEMA.PARTITIONS
    ->         WHERE TABLE_SCHEMA = 'test' AND TABLE_NAME LIKE 'ts_';
+------------+----------------+------------+----------------+-------------+
| TABLE_NAME | PARTITION_NAME | TABLE_ROWS | AVG_ROW_LENGTH | DATA_LENGTH |
+------------+----------------+------------+----------------+-------------+
| ts1        | p0             |          0 |              0 |       16384 |
| ts1        | p1             |          0 |              0 |       16384 |
| ts1        | p2             |          0 |              0 |       16384 |
| ts2        | p0             |          0 |              0 |       16384 |
| ts2        | p1             |          0 |              0 |       16384 |
| ts2        | p2             |          0 |              0 |       16384 |
| ts2        | p3             |          1 |          16384 |       16384 |
| ts3        | p0             |          0 |              0 |       16384 |
| ts3        | p1             |          1 |          16384 |       16384 |
| ts3        | p2             |          0 |              0 |       16384 |
+------------+----------------+------------+----------------+-------------+
10 rows in set (0.00 sec)
```

HASH および KEY パーティショニングでの NULL の扱い. HASH または KEY によってパーティション化されたテーブルの場合、NULL の扱いは少し異なります。 これらの場合、NULL 値を返すパーティショニング式は、戻り値がゼロであったかのように扱われます。 この動作については、HASH によってパーティション化されたテーブルを作成して該当する値が含まれるレコードを挿入することで、ファイルシステムにどのような影響があるかを検査することで確認できます

```sql
mysql> CREATE TABLE th (
    ->          c1 INT,
    ->          c2 VARCHAR(20)
    ->      )
    ->      PARTITION BY HASH(c1)
    ->      PARTITIONS 2;
Query OK, 0 rows affected (0.04 sec)
```

このテーブルに属するパーティションは、次のクエリーを使用して表示できます

```sql
mysql> SELECT TABLE_NAME,PARTITION_NAME,TABLE_ROWS,AVG_ROW_LENGTH,DATA_LENGTH
    ->         FROM INFORMATION_SCHEMA.PARTITIONS
    ->         WHERE TABLE_SCHEMA = 'test' AND TABLE_NAME ='th';
+------------+----------------+------------+----------------+-------------+
| TABLE_NAME | PARTITION_NAME | TABLE_ROWS | AVG_ROW_LENGTH | DATA_LENGTH |
+------------+----------------+------------+----------------+-------------+
| th         | p0             |          0 |              0 |       16384 |
| th         | p1             |          0 |              0 |       16384 |
+------------+----------------+------------+----------------+-------------+
2 rows in set (0.00 sec)
```

各パーティションの TABLE_ROWS は 0 です。 ここで次に示すように、c1 カラム値が NULL および 0 である 2 つの行を th に挿入し、それらの行が挿入されたことを確認します。

```sql
mysql> INSERT INTO th VALUES (NULL, 'mothra'), (0, 'gigan');
Query OK, 2 rows affected (0.00 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql> SELECT * FROM th;
+------+--------+
| c1   | c2     |
+------+--------+
| NULL | mothra |
|    0 | gigan  |
+------+--------+
2 rows in set (0.00 sec)
```

INFORMATION_SCHEMA.PARTITIONS テーブルを再度確認すると、両方の行がパーティション p0 に挿入されたことがわかります

```sql
mysql> SELECT TABLE_NAME, PARTITION_NAME, TABLE_ROWS, AVG_ROW_LENGTH, DATA_LENGTH
    ->         FROM INFORMATION_SCHEMA.PARTITIONS
    ->         WHERE TABLE_SCHEMA = 'test' AND TABLE_NAME ='th';
+------------+----------------+------------+----------------+-------------+
| TABLE_NAME | PARTITION_NAME | TABLE_ROWS | AVG_ROW_LENGTH | DATA_LENGTH |
+------------+----------------+------------+----------------+-------------+
| th         | p0             |          2 |           8192 |       16384 |
| th         | p1             |          0 |              0 |       16384 |
+------------+----------------+------------+----------------+-------------+
2 rows in set (0.00 sec)
```

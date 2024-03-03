### 24.3 パーティション管理

SQL ステートメントを使用してパーティションテーブルを変更する方法は多数あります。ALTER TABLE ステートメントのパーティション化拡張を使用して、既存のパーティションを追加、削除、再定義、マージまたは分割できます。

```sql
mysql> CREATE TABLE trb3 (id INT, name VARCHAR(50), purchased DATE)
    ->     PARTITION BY RANGE( YEAR(purchased) ) (
    ->         PARTITION p0 VALUES LESS THAN (1990),
    ->         PARTITION p1 VALUES LESS THAN (1995),
    ->         PARTITION p2 VALUES LESS THAN (2000),
    ->         PARTITION p3 VALUES LESS THAN (2005)
    ->     );
Query OK, 0 rows affected (0.06 sec)

mysql> ALTER TABLE trb3 PARTITION BY KEY(id) PARTITIONS 2;
Query OK, 0 rows affected (0.12 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

### 24.3.1 RANGE および LIST パーティションの管理

レンジでパーティション化され、次の CREATE TABLE および INSERT ステートメントを使用して 10 個のレコードが移入されるテーブルを作成したとします

```Sql
mysql>  CREATE TABLE tr (id INT, name VARCHAR(50), purchased DATE)
    ->          PARTITION BY RANGE( YEAR(purchased) ) (
    ->              PARTITION p0 VALUES LESS THAN (1990),
    ->              PARTITION p1 VALUES LESS THAN (1995),
    ->              PARTITION p2 VALUES LESS THAN (2000),
    ->              PARTITION p3 VALUES LESS THAN (2005),
    ->              PARTITION p4 VALUES LESS THAN (2010),
    ->              PARTITION p5 VALUES LESS THAN (2015)
    ->          );
Query OK, 0 rows affected (0.12 sec)

mysql> INSERT INTO tr VALUES
    ->          (1, 'desk organiser', '2003-10-15'),
    ->          (2, 'alarm clock', '1997-11-05'),
    ->          (3, 'chair', '2009-03-10'),
    ->          (4, 'bookcase', '1989-01-10'),
    ->          (5, 'exercise bike', '2014-05-09'),
    ->          (6, 'sofa', '1987-06-05'),
    ->          (7, 'espresso maker', '2011-11-22'),
    ->          (8, 'aquarium', '1992-08-04'),
    ->          (9, 'study desk', '2006-09-16'),
    ->          (10, 'lava lamp', '1998-12-25');
Query OK, 10 rows affected (0.01 sec)
Records: 10  Duplicates: 0  Warnings: 0
```

パーティション p2 に挿入されているはずの項目を以下のように確認できます

```sql
mysql> SELECT * FROM tr
    ->          WHERE purchased BETWEEN '1995-01-01' AND '1999-12-31';
+------+-------------+------------+
| id   | name        | purchased  |
+------+-------------+------------+
|    2 | alarm clock | 1997-11-05 |
|   10 | lava lamp   | 1998-12-25 |
+------+-------------+------------+
2 rows in set (0.00 sec)
```

次に示すように、パーティション選択を使用してこの情報を取得することもできます

```sql
mysql> SELECT * FROM tr PARTITION (p2);
+------+-------------+------------+
| id   | name        | purchased  |
+------+-------------+------------+
|    2 | alarm clock | 1997-11-05 |
|   10 | lava lamp   | 1998-12-25 |
+------+-------------+------------+
2 rows in set (0.00 sec)
```

p2 という名前のパーティションを削除するには、次のコマンドを実行します

```sql
mysql> ALTER TABLE tr DROP PARTITION p2;
Query OK, 0 rows affected (0.03 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> SELECT * FROM tr WHERE purchased
    ->      BETWEEN '1995-01-01' AND '1999-12-31';
Empty set (0.00 sec)
```

```sql
mysql>  INSERT INTO tr VALUES (11, 'pencil holder', '1995-07-12');
Query OK, 1 row affected (0.00 sec)

mysql> SELECT * FROM tr WHERE purchased
    ->      BETWEEN '1995-01-01' AND '2004-12-31';
+------+----------------+------------+
| id   | name           | purchased  |
+------+----------------+------------+
|    1 | desk organiser | 2003-10-15 |
|   11 | pencil holder  | 1995-07-12 |
+------+----------------+------------+
2 rows in set (0.00 sec)

mysql> ALTER TABLE tr DROP PARTITION p3;
Query OK, 0 rows affected (0.03 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> SELECT * FROM tr WHERE purchased
    ->      BETWEEN '1995-01-01' AND '2004-12-31';
Empty set (0.00 sec)
```

次のように定義された、組織のメンバーシップデータが含まれるパーティション化されたテーブルがあるとします

```sql
mysql> CREATE TABLE members (
    ->     id INT,
    ->     fname VARCHAR(25),
    ->     lname VARCHAR(25),
    ->     dob DATE
    -> )
    -> PARTITION BY RANGE( YEAR(dob) ) (
    ->     PARTITION p0 VALUES LESS THAN (1980),
    ->     PARTITION p1 VALUES LESS THAN (1990),
    ->     PARTITION p2 VALUES LESS THAN (2000)
    -> );
Query OK, 0 rows affected (0.05 sec)
```

次のように members テーブルを変更することで、2000 年から 2010 年までに生まれた新しいメンバーを受け入れることができます。

```sql
mysql> ALTER TABLE members ADD PARTITION (PARTITION p3 VALUES LESS THAN (2010));
Query OK, 0 rows affected (0.04 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

範囲によってパーティション化されたテーブルで ADD PARTITION を使用するときは、パーティションリストの上端にのみ新しいパーティションを追加できます。 この方法で新しいパーティションを既存のパーティションの間または前に追加しようとすると、次のようにエラーになります。

````Sql
mysql> ALTER TABLE members
    ->           ADD PARTITION (
    ->           PARTITION n VALUES LESS THAN (1970));
ERROR 1493 (HY000): VALUES LESS THAN value must be strictly increasing for each partition```
````

この問題は、次のように最初のパーティションを 2 つに再編成し、それらの間の範囲を分割することで回避できます

```sql
mysql> ALTER TABLE members
    ->     REORGANIZE PARTITION p0 INTO (
    ->         PARTITION n0 VALUES LESS THAN (1970),
    ->         PARTITION n1 VALUES LESS THAN (1980)
    -> );
Query OK, 0 rows affected (0.05 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

```sql
mysql> SHOW CREATE TABLE members\G
*************************** 1. row ***************************
       Table: members
Create Table: CREATE TABLE `members` (
  `id` int DEFAULT NULL,
  `fname` varchar(25) DEFAULT NULL,
  `lname` varchar(25) DEFAULT NULL,
  `dob` date DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
/*!50100 PARTITION BY RANGE (year(`dob`))
(PARTITION n0 VALUES LESS THAN (1970) ENGINE = InnoDB,
 PARTITION n1 VALUES LESS THAN (1980) ENGINE = InnoDB,
 PARTITION p1 VALUES LESS THAN (1990) ENGINE = InnoDB,
 PARTITION p2 VALUES LESS THAN (2000) ENGINE = InnoDB,
 PARTITION p3 VALUES LESS THAN (2010) ENGINE = InnoDB) */
1 row in set (0.00 sec)

```

次の CREATE TABLE ステートメントを使用してテーブル tt が定義されているとします

```sql
mysql> CREATE TABLE tt (
    ->     id INT,
    ->     data INT
    -> )
    -> PARTITION BY LIST(data) (
    ->     PARTITION p0 VALUES IN (5, 10, 15),
    ->     PARTITION p1 VALUES IN (6, 12, 18)
    -> );
Query OK, 0 rows affected (0.06 sec)
```

data カラム値が 7、14、および 21 である行を格納する新しいパーティションを次のように追加できます。

```Sql
mysql> ALTER TABLE tt ADD PARTITION (PARTITION p2 VALUES IN (7, 14, 21));
Query OK, 0 rows affected (0.03 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

```sql
mysql> ALTER TABLE tt ADD PARTITION
    ->           (PARTITION np VALUES IN (4, 8, 12));
ERROR 1495 (HY000): Multiple definition of same constant in list partitioning
```

次のように単一 ALTER TABLE ... ADD PARTITION ステートメントで複数のパーティションを追加できます

```sql
mysql> CREATE TABLE employees (
    ->   id INT NOT NULL,
    ->   fname VARCHAR(50) NOT NULL,
    ->   lname VARCHAR(50) NOT NULL,
    ->   hired DATE NOT NULL
    -> )
    -> PARTITION BY RANGE( YEAR(hired) ) (
    ->   PARTITION p1 VALUES LESS THAN (1991),
    ->   PARTITION p2 VALUES LESS THAN (1996),
    ->   PARTITION p3 VALUES LESS THAN (2001),
    ->   PARTITION p4 VALUES LESS THAN (2005)
    -> );
Query OK, 0 rows affected (0.06 sec)

mysql>
mysql> ALTER TABLE employees ADD PARTITION (
    ->     PARTITION p5 VALUES LESS THAN (2010),
    ->     PARTITION p6 VALUES LESS THAN MAXVALUE
    -> );
Query OK, 0 rows affected (0.05 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

ALTER TABLE への別のパーティション関連拡張を使用して、これを行うことができます

```sql
mysql> ALTER TABLE members REORGANIZE PARTITION n0 INTO (
    ->     PARTITION s0 VALUES LESS THAN (1960),
    ->     PARTITION s1 VALUES LESS THAN (1970)
    -> );
Query OK, 0 rows affected (0.06 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

REORGANIZE PARTITION 句を使用して、隣接するパーティションをマージすることもできます。 次に示すように、前のステートメントの members テーブルへの影響を元に戻すことができます

```sql
mysql> ALTER TABLE members REORGANIZE PARTITION s0,s1 INTO (
    ->     PARTITION p0 VALUES LESS THAN (1970)
    -> );
Query OK, 0 rows affected (0.12 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

### 24.3.2 HASH および KEY パーティションの管理

ハッシュまたはキーによってパーティション化されたテーブルは、パーティショニングセットアップで変更に関して互いによく似ていますが、範囲またはリストによってパーティション化されたテーブルとはいくつかの点で異なります

```sql
mysql> CREATE TABLE clients (
    ->     id INT,
    ->     fname VARCHAR(30),
    ->     lname VARCHAR(30),
    ->     signed DATE
    -> )
    -> PARTITION BY HASH( MONTH(signed) )
    -> PARTITIONS 12;
Query OK, 0 rows affected (0.16 sec)
```

パーティション数を 12 から 8 に減らすには、次の ALTER TABLE ステートメントを実行します

```sql
mysql> ALTER TABLE clients COALESCE PARTITION 4;
Query OK, 0 rows affected (0.34 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

次の例は前の例と似ていますが、テーブルが LINEAR KEY によってパーティション化されている点のみが異なります

```sql
mysql> CREATE TABLE clients_lk (
    ->          id INT,
    ->          fname VARCHAR(30),
    ->          lname VARCHAR(30),
    ->          signed DATE
    ->      )
    ->      PARTITION BY LINEAR KEY(signed)
    ->      PARTITIONS 12;
Query OK, 0 rows affected (0.15 sec)

mysql> ALTER TABLE clients_lk COALESCE PARTITION 4;
Query OK, 0 rows affected (0.19 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

### 24.3.3 パーティションとサブパーティションをテーブルと交換する

```sql
mysql> CREATE TABLE e (
    ->     id INT NOT NULL,
    ->     fname VARCHAR(30),
    ->     lname VARCHAR(30)
    -> )
    ->     PARTITION BY RANGE (id) (
    ->         PARTITION p0 VALUES LESS THAN (50),
    ->         PARTITION p1 VALUES LESS THAN (100),
    ->         PARTITION p2 VALUES LESS THAN (150),
    ->         PARTITION p3 VALUES LESS THAN (MAXVALUE)
    -> );
Query OK, 0 rows affected (0.06 sec)

mysql>
mysql> INSERT INTO e VALUES
    ->     (1669, "Jim", "Smith"),
    ->     (337, "Mary", "Jones"),
    ->     (16, "Frank", "White"),
    ->     (2005, "Linda", "Black");
Query OK, 4 rows affected (0.01 sec)
Records: 4  Duplicates: 0  Warnings: 0
```

ここで、e2 という名前の、e のパーティション化されていないコピーを作成します

```sql
mysql> CREATE TABLE e2 LIKE e;
Query OK, 0 rows affected (0.06 sec)

mysql> ALTER TABLE e2 REMOVE PARTITIONING;
Query OK, 0 rows affected (0.12 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

テーブル e のどのパーティションに行が含まれるかは、次のように INFORMATION_SCHEMA.PARTITIONS テーブルを照会することで確認できます

```sql
mysql> SELECT PARTITION_NAME, TABLE_ROWS
    ->            FROM INFORMATION_SCHEMA.PARTITIONS
    ->            WHERE TABLE_NAME = 'e';
+----------------+------------+
| PARTITION_NAME | TABLE_ROWS |
+----------------+------------+
| p0             |          1 |
| p1             |          0 |
| p2             |          0 |
| p3             |          3 |
+----------------+------------+
4 rows in set (0.00 sec)
```

テーブル e のパーティション p0 をテーブル e2 と交換するには、次に示すように ALTER TABLE を使用できます

```sql
mysql> ALTER TABLE e EXCHANGE PARTITION p0 WITH TABLE e2;
Query OK, 0 rows affected (0.03 sec)
```

```sql
mysql> SELECT PARTITION_NAME, TABLE_ROWS
    ->            FROM INFORMATION_SCHEMA.PARTITIONS
    ->            WHERE TABLE_NAME = 'e';
+----------------+------------+
| PARTITION_NAME | TABLE_ROWS |
+----------------+------------+
| p0             |          0 |
| p1             |          0 |
| p2             |          0 |
| p3             |          3 |
+----------------+------------+
4 rows in set (0.01 sec)

mysql> SELECT * FROM e2;
+----+-------+-------+
| id | fname | lname |
+----+-------+-------+
| 16 | Frank | White |
+----+-------+-------+
1 row in set (0.00 sec)
```

パーティションと交換されるテーブルは、必ずしも空である必要はありません。 これを示すために、まずテーブル e に新しい行を挿入し、50 未満の id カラム値を選択してこの行がパーティション p0 に格納されていることを確認し、PARTITIONS テーブルをクエリーしてこれを後で検証します

````sql
mysql> INSERT INTO e VALUES (41, "Michael", "Green");
Query OK, 1 row affected (0.00 sec)

mysql> SELECT PARTITION_NAME, TABLE_ROWS
    ->            FROM INFORMATION_SCHEMA.PARTITIONS
    ->            WHERE TABLE_NAME = 'e';
+----------------+------------+
| PARTITION_NAME | TABLE_ROWS |
+----------------+------------+
| p0             |          1 |
| p1             |          0 |
| p2             |          0 |
| p3             |          3 |
+----------------+------------+
4 rows in set (0.00 sec)```
````

ここで、前と同じ ALTER TABLE ステートメントを使用して、ふたたびパーティション p0 をテーブル e2 と交換します

```sql
mysql> ALTER TABLE e EXCHANGE PARTITION p0 WITH TABLE e2;
Query OK, 0 rows affected (0.04 sec)
```

次のクエリーの出力は、ALTER TABLE ステートメントを発行する前に、パーティション p0 に格納されていたテーブル行およびテーブル e2 に格納されていたテーブル行の配置が切り替わったことを示しています

```sql
mysql> SELECT * FROM e;
+------+-------+-------+
| id   | fname | lname |
+------+-------+-------+
|   16 | Frank | White |
| 1669 | Jim   | Smith |
|  337 | Mary  | Jones |
| 2005 | Linda | Black |
+------+-------+-------+
4 rows in set (0.00 sec)

mysql> SELECT PARTITION_NAME, TABLE_ROWS
    ->            FROM INFORMATION_SCHEMA.PARTITIONS
    ->            WHERE TABLE_NAME = 'e';
+----------------+------------+
| PARTITION_NAME | TABLE_ROWS |
+----------------+------------+
| p0             |          0 |
| p1             |          0 |
| p2             |          0 |
| p3             |          3 |
+----------------+------------+
4 rows in set (0.00 sec)

mysql> SELECT * FROM e2;
+----+---------+-------+
| id | fname   | lname |
+----+---------+-------+
| 41 | Michael | Green |
+----+---------+-------+
1 row in set (0.00 sec)
```

ALTER TABLE ... EXCHANGE PARTITION ステートメントを発行する前にパーティション化されていないテーブルで見つかる行は、それらがターゲットパーティションに格納されるために必要な条件を満たしている必要があり、そうでない場合はステートメントが失敗する

```sql
mysql> INSERT INTO e2 VALUES (51, "Ellen", "McDonald");
Query OK, 1 row affected (0.00 sec)

mysql> ALTER TABLE e EXCHANGE PARTITION p0 WITH TABLE e2;
ERROR 1737 (HY000): Found a row that does not match the partition

mysql> ALTER TABLE e EXCHANGE PARTITION p0 WITH TABLE e2 WITHOUT VALIDATION;
Query OK, 0 rows affected (0.03 sec)
```

行ごとの検証なしでのパーティションの交換

```sql
mysql> CREATE TABLE e (
    ->     id INT NOT NULL,
    ->     fname VARCHAR(30),
    ->     lname VARCHAR(30)
    -> )
    ->     PARTITION BY RANGE (id) (
    ->         PARTITION p0 VALUES LESS THAN (1000001),
    ->         PARTITION p1 VALUES LESS THAN (2000001)
    -> );
Query OK, 0 rows affected (0.04 sec)

mysql> SELECT PARTITION_NAME, TABLE_ROWS FROM INFORMATION_SCHEMA.PARTITIONS WHERE TABLE_NAME = 'e';
+----------------+------------+
| PARTITION_NAME | TABLE_ROWS |
+----------------+------------+
| p0             |          0 |
| p1             |          0 |
+----------------+------------+
2 rows in set (0.00 sec)

mysql> CREATE TABLE e2 (
    ->     id INT NOT NULL,
    ->     fname VARCHAR(30),
    ->     lname VARCHAR(30)
    -> );
Query OK, 0 rows affected (0.02 sec)

mysql> CREATE TABLE e3 (
    ->     id INT NOT NULL,
    ->     fname VARCHAR(30),
    ->     lname VARCHAR(30)
    -> );
Query OK, 0 rows affected (0.02 sec)

mysql> ALTER TABLE e EXCHANGE PARTITION p0 WITH TABLE e2 WITH VALIDATION;
Query OK, 0 rows affected (0.03 sec)

mysql> ALTER TABLE e EXCHANGE PARTITION p0 WITH TABLE e3 WITHOUT VALIDATION;
Query OK, 0 rows affected (0.03 sec)

mysql> SELECT PARTITION_NAME, TABLE_ROWS FROM INFORMATION_SCHEMA.PARTITIONS WHERE TABLE_NAME = 'e';
+----------------+------------+
| PARTITION_NAME | TABLE_ROWS |
+----------------+------------+
| p0             |          0 |
| p1             |          0 |
+----------------+------------+
2 rows in set (0.00 sec)
```

サブパーティションをパーティション化されていないテーブルと交換する

```sql
mysql> CREATE TABLE es (
    ->          id INT NOT NULL,
    ->          fname VARCHAR(30),
    ->          lname VARCHAR(30)
    ->      )
    ->          PARTITION BY RANGE (id)
    ->          SUBPARTITION BY KEY (lname)
    ->          SUBPARTITIONS 2 (
    ->              PARTITION p0 VALUES LESS THAN (50),
    ->              PARTITION p1 VALUES LESS THAN (100),
    ->              PARTITION p2 VALUES LESS THAN (150),
    ->              PARTITION p3 VALUES LESS THAN (MAXVALUE)
    ->          );
Query OK, 0 rows affected (0.11 sec)

mysql> INSERT INTO es VALUES
    ->          (1669, "Jim", "Smith"),
    ->          (337, "Mary", "Jones"),
    ->          (16, "Frank", "White"),
    ->          (2005, "Linda", "Black");
Query OK, 4 rows affected (0.01 sec)
Records: 4  Duplicates: 0  Warnings: 0

mysql> CREATE TABLE es2 LIKE es;
Query OK, 0 rows affected (0.12 sec)

mysql> ALTER TABLE es2 REMOVE PARTITIONING;
Query OK, 0 rows affected (0.15 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> SELECT PARTITION_NAME, SUBPARTITION_NAME, TABLE_ROWS
    ->          FROM INFORMATION_SCHEMA.PARTITIONS
    ->          WHERE TABLE_NAME = 'es';
+----------------+-------------------+------------+
| PARTITION_NAME | SUBPARTITION_NAME | TABLE_ROWS |
+----------------+-------------------+------------+
| p0             | p0sp0             |          1 |
| p0             | p0sp1             |          0 |
| p1             | p1sp0             |          0 |
| p1             | p1sp1             |          0 |
| p2             | p2sp0             |          0 |
| p2             | p2sp1             |          0 |
| p3             | p3sp0             |          1 |
| p3             | p3sp1             |          2 |
+----------------+-------------------+------------+
8 rows in set (0.00 sec)

mysql> ALTER TABLE es EXCHANGE PARTITION p3sp0 WITH TABLE es2;
Query OK, 0 rows affected (0.03 sec)

mysql> SELECT PARTITION_NAME, SUBPARTITION_NAME, TABLE_ROWS
    ->          FROM INFORMATION_SCHEMA.PARTITIONS
    ->          WHERE TABLE_NAME = 'es';
+----------------+-------------------+------------+
| PARTITION_NAME | SUBPARTITION_NAME | TABLE_ROWS |
+----------------+-------------------+------------+
| p0             | p0sp0             |          0 |
| p0             | p0sp1             |          0 |
| p1             | p1sp0             |          0 |
| p1             | p1sp1             |          0 |
| p2             | p2sp0             |          0 |
| p2             | p2sp1             |          0 |
| p3             | p3sp0             |          0 |
| p3             | p3sp1             |          2 |
+----------------+-------------------+------------+
8 rows in set (0.01 sec)

mysql> CREATE TABLE es3 LIKE e;
Query OK, 0 rows affected (0.03 sec)

mysql> ALTER TABLE es3 REMOVE PARTITIONING;
Query OK, 0 rows affected (0.07 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> SHOW CREATE TABLE es3\G
*************************** 1. row ***************************
       Table: es3
Create Table: CREATE TABLE `es3` (
  `id` int NOT NULL,
  `fname` varchar(30) DEFAULT NULL,
  `lname` varchar(30) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
1 row in set (0.00 sec)

mysql>  ALTER TABLE es3 ENGINE = MyISAM;
Query OK, 0 rows affected (0.04 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE es EXCHANGE PARTITION p3sp0 WITH TABLE es3;
ERROR 1497 (HY000): The mix of handlers in the partitions is not allowed in this version of MySQL

```

24.3.5 パーティションに関する情報を取得する

```sql
mysql> CREATE TABLE trb1 (id INT, name VARCHAR(50), purchased DATE)
    ->     PARTITION BY RANGE(id)
    ->     (
    ->         PARTITION p0 VALUES LESS THAN (3),
    ->         PARTITION p1 VALUES LESS THAN (7),
    ->         PARTITION p2 VALUES LESS THAN (9),
    ->         PARTITION p3 VALUES LESS THAN (11)
    ->     );
Query OK, 0 rows affected (0.06 sec)

mysql>
mysql> INSERT INTO trb1 VALUES
    ->     (1, 'desk organiser', '2003-10-15'),
    ->     (2, 'CD player', '1993-11-05'),
    ->     (3, 'TV set', '1996-03-10'),
    ->     (4, 'bookcase', '1982-01-10'),
    ->     (5, 'exercise bike', '2004-05-09'),
    ->     (6, 'sofa', '1987-06-05'),
    ->     (7, 'popcorn maker', '2001-11-22'),
    ->     (8, 'aquarium', '1992-08-04'),
    ->     (9, 'study desk', '1984-09-16'),
    ->     (10, 'lava lamp', '1998-12-25');
Query OK, 10 rows affected (0.01 sec)
Records: 10  Duplicates: 0  Warnings: 0

mysql>  EXPLAIN SELECT * FROM trb1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: trb1
   partitions: p0,p1,p2,p3
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 10
     filtered: 100.00
        Extra: NULL
1 row in set, 1 warning (0.00 sec)

mysql> EXPLAIN SELECT * FROM trb1 WHERE id < 5\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: trb1
   partitions: p0,p1
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 6
     filtered: 33.33
        Extra: Using where
1 row in set, 1 warning (0.00 sec)

mysql> ALTER TABLE trb1 ADD PRIMARY KEY (id);
Query OK, 0 rows affected (0.22 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> EXPLAIN SELECT * FROM trb1 WHERE id < 5\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: trb1
   partitions: p0,p1
         type: range
possible_keys: PRIMARY
          key: PRIMARY
      key_len: 4
          ref: NULL
         rows: 4
     filtered: 100.00
        Extra: Using where
1 row in set, 1 warning (0.00 sec)
```

### 24.5 パーティション選択

```sql
mysql> SET @@SQL_MODE = '';
Query OK, 0 rows affected (0.00 sec)

mysql>
mysql> CREATE TABLE employees  (
    ->     id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    ->     fname VARCHAR(25) NOT NULL,
    ->     lname VARCHAR(25) NOT NULL,
    ->     store_id INT NOT NULL,
    ->     department_id INT NOT NULL
    -> )
    ->     PARTITION BY RANGE(id)  (
    ->         PARTITION p0 VALUES LESS THAN (5),
    ->         PARTITION p1 VALUES LESS THAN (10),
    ->         PARTITION p2 VALUES LESS THAN (15),
    ->         PARTITION p3 VALUES LESS THAN MAXVALUE
    -> );
Query OK, 0 rows affected (0.07 sec)

mysql>
mysql> INSERT INTO employees VALUES
    ->     ('', 'Bob', 'Taylor', 3, 2), ('', 'Frank', 'Williams', 1, 2),
    ->     ('', 'Ellen', 'Johnson', 3, 4), ('', 'Jim', 'Smith', 2, 4),
    ->     ('', 'Mary', 'Jones', 1, 1), ('', 'Linda', 'Black', 2, 3),
    ->     ('', 'Ed', 'Jones', 2, 1), ('', 'June', 'Wilson', 3, 1),
    ->     ('', 'Andy', 'Smith', 1, 3), ('', 'Lou', 'Waters', 2, 4),
    ->     ('', 'Jill', 'Stone', 1, 4), ('', 'Roger', 'White', 3, 2),
    ->     ('', 'Howard', 'Andrews', 1, 2), ('', 'Fred', 'Goldberg', 3, 3),
    ->     ('', 'Barbara', 'Brown', 2, 3), ('', 'Alice', 'Rogers', 2, 2),
    ->     ('', 'Mark', 'Morgan', 3, 3), ('', 'Karen', 'Cole', 3, 2);
Query OK, 18 rows affected, 18 warnings (0.01 sec)
Records: 18  Duplicates: 0  Warnings: 18

mysql> SELECT * FROM employees PARTITION (p1);
+----+-------+--------+----------+---------------+
| id | fname | lname  | store_id | department_id |
+----+-------+--------+----------+---------------+
|  5 | Mary  | Jones  |        1 |             1 |
|  6 | Linda | Black  |        2 |             3 |
|  7 | Ed    | Jones  |        2 |             1 |
|  8 | June  | Wilson |        3 |             1 |
|  9 | Andy  | Smith  |        1 |             3 |
+----+-------+--------+----------+---------------+
5 rows in set (0.00 sec)

mysql> SELECT * FROM employees PARTITION (p0, p2)
    ->          WHERE lname LIKE 'S%';
+----+-------+-------+----------+---------------+
| id | fname | lname | store_id | department_id |
+----+-------+-------+----------+---------------+
|  4 | Jim   | Smith |        2 |             4 |
| 11 | Jill  | Stone |        1 |             4 |
+----+-------+-------+----------+---------------+
2 rows in set (0.00 sec)


mysql> SELECT id, CONCAT(fname, ' ', lname) AS name
    ->          FROM employees PARTITION (p0) ORDER BY lname;
+----+----------------+
| id | name           |
+----+----------------+
|  3 | Ellen Johnson  |
|  4 | Jim Smith      |
|  1 | Bob Taylor     |
|  2 | Frank Williams |
+----+----------------+
4 rows in set (0.01 sec)

mysql> SELECT store_id, COUNT(department_id) AS c
    ->          FROM employees PARTITION (p1,p2,p3)
    ->          GROUP BY store_id HAVING c > 4;
+----------+---+
| store_id | c |
+----------+---+
|        2 | 5 |
|        3 | 5 |
+----------+---+
2 rows in set (0.01 sec)

mysql> CREATE TABLE employees_sub  (
    ->          id INT NOT NULL AUTO_INCREMENT,
    ->          fname VARCHAR(25) NOT NULL,
    ->          lname VARCHAR(25) NOT NULL,
    ->          store_id INT NOT NULL,
    ->          department_id INT NOT NULL,
    ->          PRIMARY KEY pk (id, lname)
    ->      )
    ->          PARTITION BY RANGE(id)
    ->          SUBPARTITION BY KEY (lname)
    ->          SUBPARTITIONS 2 (
    ->              PARTITION p0 VALUES LESS THAN (5),
    ->              PARTITION p1 VALUES LESS THAN (10),
    ->              PARTITION p2 VALUES LESS THAN (15),
    ->              PARTITION p3 VALUES LESS THAN MAXVALUE
    ->      );
Query OK, 0 rows affected (0.13 sec)

mysql> INSERT INTO employees_sub   # reuse data in employees table
    ->          SELECT * FROM employees;
Query OK, 18 rows affected (0.01 sec)
Records: 18  Duplicates: 0  Warnings: 0

mysql> SELECT id, CONCAT(fname, ' ', lname) AS name
    ->          FROM employees_sub PARTITION (p2sp1);
+----+------------+
| id | name       |
+----+------------+
| 10 | Lou Waters |
+----+------------+
1 row in set (0.00 sec)
```

パーティション選択は結合と一緒に使用することもできます。 次のステートメントを使用して 2 つのテーブルを作成して移入するとします

```sql
mysql> CREATE TABLE stores (
    ->     id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    ->     city VARCHAR(30) NOT NULL
    -> )
    ->     PARTITION BY HASH(id)
    ->     PARTITIONS 2;
Query OK, 0 rows affected (0.04 sec)

mysql>
mysql> INSERT INTO stores VALUES
    ->     ('', 'Nambucca'), ('', 'Uranga'),
    ->     ('', 'Bellingen'), ('', 'Grafton');
Query OK, 4 rows affected, 4 warnings (0.01 sec)
Records: 4  Duplicates: 0  Warnings: 4

mysql>
mysql> CREATE TABLE departments  (
    ->     id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    ->     name VARCHAR(30) NOT NULL
    -> )
    ->     PARTITION BY KEY(id)
    ->     PARTITIONS 2;
Query OK, 0 rows affected (0.04 sec)

mysql>
mysql> INSERT INTO departments VALUES
    ->     ('', 'Sales'), ('', 'Customer Service'),
    ->     ('', 'Delivery'), ('', 'Accounting');
Query OK, 4 rows affected, 4 warnings (0.01 sec)
Records: 4  Duplicates: 0  Warnings: 4

mysql> SELECT
    ->          e.id AS 'Employee ID', CONCAT(e.fname, ' ', e.lname) AS Name,
    ->          s.city AS City, d.name AS department
    ->      FROM employees AS e
    ->          JOIN stores PARTITION (p1) AS s ON e.store_id=s.id
    ->          JOIN departments PARTITION (p0) AS d ON e.department_id=d.id
    ->      ORDER BY e.lname;
+-------------+---------------+-----------+------------+
| Employee ID | Name          | City      | department |
+-------------+---------------+-----------+------------+
|          14 | Fred Goldberg | Bellingen | Delivery   |
|           5 | Mary Jones    | Nambucca  | Sales      |
|          17 | Mark Morgan   | Bellingen | Delivery   |
|           9 | Andy Smith    | Nambucca  | Delivery   |
|           8 | June Wilson   | Bellingen | Sales      |
+-------------+---------------+-----------+------------+
5 rows in set (0.00 sec)

mysql> SELECT * FROM employees WHERE fname LIKE 'j%';
+----+-------+--------+----------+---------------+
| id | fname | lname  | store_id | department_id |
+----+-------+--------+----------+---------------+
|  4 | Jim   | Smith  |        2 |             4 |
|  8 | June  | Wilson |        3 |             1 |
| 11 | Jill  | Stone  |        1 |             4 |
+----+-------+--------+----------+---------------+
3 rows in set (0.00 sec)

mysql> DELETE FROM employees PARTITION (p0, p1)
    ->          WHERE fname LIKE 'j%';
Query OK, 2 rows affected (0.00 sec)

mysql> SELECT * FROM employees WHERE fname LIKE 'j%';
+----+-------+-------+----------+---------------+
| id | fname | lname | store_id | department_id |
+----+-------+-------+----------+---------------+
| 11 | Jill  | Stone |        1 |             4 |
+----+-------+-------+----------+---------------+
1 row in set (0.00 sec)

mysql> UPDATE employees PARTITION (p0)
    ->          SET store_id = 2 WHERE fname = 'Jill';
Query OK, 0 rows affected (0.00 sec)
Rows matched: 0  Changed: 0  Warnings: 0

mysql> SELECT * FROM employees WHERE fname = 'Jill';
+----+-------+-------+----------+---------------+
| id | fname | lname | store_id | department_id |
+----+-------+-------+----------+---------------+
| 11 | Jill  | Stone |        1 |             4 |
+----+-------+-------+----------+---------------+
1 row in set (0.00 sec)

mysql> UPDATE employees PARTITION (p2)
    ->         SET store_id = 2 WHERE fname = 'Jill';
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> SELECT * FROM employees WHERE fname = 'Jill';
+----+-------+-------+----------+---------------+
| id | fname | lname | store_id | department_id |
+----+-------+-------+----------+---------------+
| 11 | Jill  | Stone |        2 |             4 |
+----+-------+-------+----------+---------------+
1 row in set (0.00 sec)
```

### 24.6 パーティショニングの制約と制限

```Sql
mysql> SELECT @@sql_mode;
+------------+
| @@sql_mode |
+------------+
|            |
+------------+
1 row in set (0.00 sec)

mysql> CREATE TABLE tu (c1 BIGINT UNSIGNED)
    ->        PARTITION BY RANGE(c1 - 10) (
    ->          PARTITION p0 VALUES LESS THAN (-5),
    ->          PARTITION p1 VALUES LESS THAN (0),
    ->          PARTITION p2 VALUES LESS THAN (5),
    ->          PARTITION p3 VALUES LESS THAN (10),
    ->          PARTITION p4 VALUES LESS THAN (MAXVALUE)
    ->      );
ERROR 1563 (HY000): Partition constant is out of partition function domain
mysql> SET sql_mode='NO_UNSIGNED_SUBTRACTION';
Query OK, 0 rows affected (0.00 sec)

mysql> CREATE TABLE tu (c1 BIGINT UNSIGNED)
    ->        PARTITION BY RANGE(c1 - 10) (
    ->          PARTITION p0 VALUES LESS THAN (-5),
    ->          PARTITION p1 VALUES LESS THAN (0),
    ->          PARTITION p2 VALUES LESS THAN (5),
    ->          PARTITION p3 VALUES LESS THAN (10),
    ->          PARTITION p4 VALUES LESS THAN (MAXVALUE)
    ->      );
Query OK, 0 rows affected (0.07 sec)
```

tu を作成したあとに NO_UNSIGNED_SUBTRACTION サーバー SQL モードを削除すると、このテーブルにアクセスできなくなる可能性があります

```Sql
mysql> SET sql_mode='';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT * FROM tu;
ERROR 1563 (HY000): Partition constant is out of partition function domain
mysql> INSERT INTO tu VALUES (20);
ERROR 1563 (HY000): Partition constant is out of partition function domain
```

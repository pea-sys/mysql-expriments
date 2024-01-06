### 13.1.20 CREATE TABLE ステートメント
* LIKE
```sql
mysql> create table t1(a int);
Query OK, 0 rows affected (0.02 sec)

mysql> CREATE TABLE new_tbl LIKE t1;
Query OK, 0 rows affected (0.03 sec)
```
* [AS] query_expression
```sql
mysql> CREATE TABLE new_tbl_2 AS SELECT * FROM t1;
Query OK, 0 rows affected (0.03 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
* data_type
```sql
mysql> CREATE TABLE t (c CHAR(20) CHARACTER SET utf8 COLLATE utf8_bin);
Query OK, 0 rows affected, 2 warnings (0.02 sec)
```
* index_type
```sql
ERROR 1286 (42000): Unknown storage engine 'NDB'
mysql> CREATE TABLE lookup
    ->   (id INT, INDEX USING BTREE (id))
    ->   ENGINE = MEMORY;
Query OK, 0 rows affected (0.01 sec)
```
* HASH(expr)
```sql
mysql> CREATE TABLE t1 (col1 INT, col2 CHAR(5))
    ->     PARTITION BY HASH(col1);
Query OK, 0 rows affected (0.03 sec)
```
* KEY(column_list)
```sql
mysql> CREATE TABLE tk (col1 INT, col2 CHAR(5), col3 DATE)
    ->     PARTITION BY KEY(col3)
    ->     PARTITIONS 4;
Query OK, 0 rows affected (0.06 sec)
```
* RANGE(expr)
```sql
mysql> CREATE TABLE t1 (
    ->     year_col  INT,
    ->     some_data INT
    -> )
    -> PARTITION BY RANGE (year_col) (
    ->     PARTITION p0 VALUES LESS THAN (1991),
    ->     PARTITION p1 VALUES LESS THAN (1995),
    ->     PARTITION p2 VALUES LESS THAN (1999),
    ->     PARTITION p3 VALUES LESS THAN (2002),
    ->     PARTITION p4 VALUES LESS THAN (2006),
    ->     PARTITION p5 VALUES LESS THAN MAXVALUE
    -> );
Query OK, 0 rows affected (0.09 sec)
```
* RANGE COLUMNS(column_list)
```sql
mysql> CREATE TABLE rc (
    ->     a INT NOT NULL,
    ->     b INT NOT NULL
    -> )
    -> PARTITION BY RANGE COLUMNS(a,b) (
    ->     PARTITION p0 VALUES LESS THAN (10,5),
    ->     PARTITION p1 VALUES LESS THAN (20,10),
    ->     PARTITION p2 VALUES LESS THAN (50,MAXVALUE),
    ->     PARTITION p3 VALUES LESS THAN (65,MAXVALUE),
    ->     PARTITION p4 VALUES LESS THAN (MAXVALUE,MAXVALUE)
    -> );
Query OK, 0 rows affected (0.08 sec)
```
* LIST(expr)
```sql
mysql> CREATE TABLE client_firms (
    ->     id   INT,
    ->     name VARCHAR(35)
    -> )
    -> PARTITION BY LIST (id) (
    ->     PARTITION r0 VALUES IN (1, 5, 9, 13, 17, 21),
    ->     PARTITION r1 VALUES IN (2, 6, 10, 14, 18, 22),
    ->     PARTITION r2 VALUES IN (3, 7, 11, 15, 19, 23),
    ->     PARTITION r3 VALUES IN (4, 8, 12, 16, 20, 24)
    -> );
Query OK, 0 rows affected (0.07 sec)
```
* LIST COLUMNS(column_list)
```sql
mysql> CREATE TABLE lc (
    ->     a INT NULL,
    ->     b INT NULL
    -> )
    -> PARTITION BY LIST COLUMNS(a,b) (
    ->     PARTITION p0 VALUES IN( (0,0), (NULL,NULL) ),
    ->     PARTITION p1 VALUES IN( (0,1), (0,2), (0,3), (1,1), (1,2) ),
    ->     PARTITION p2 VALUES IN( (1,0), (2,0), (2,1), (3,0), (3,1) ),
    ->     PARTITION p3 VALUES IN( (1,3), (2,2), (2,3), (3,2), (3,3) )
    -> );
Query OK, 0 rows affected (0.06 sec)
```
生成されたカラムによるパーティション化
```sql
mysql> CREATE TABLE t1 (
    ->   s1 INT,
    ->   s2 INT AS (EXP(s1)) STORED
    -> )
    -> PARTITION BY LIST (s2) (
    ->   PARTITION p1 VALUES IN (1)
    -> );
Query OK, 0 rows affected (0.03 sec)
```
### 13.1.20.4 CREATE TABLE ... SELECT ステートメント
```sql
mysql> CREATE TABLE tv1
    ->           SELECT * FROM (VALUES ROW(1,3,5), ROW(2,4,6)) AS v;
Query OK, 2 rows affected (0.03 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql> TABLE tv1;
+----------+----------+----------+
| column_0 | column_1 | column_2 |
+----------+----------+----------+
|        1 |        3 |        5 |
|        2 |        4 |        6 |
+----------+----------+----------+
2 rows in set (0.00 sec)

mysql> CREATE TABLE tv2
    ->           SELECT * FROM (VALUES ROW(1,3,5), ROW(2,4,6)) AS v(x,y,z);
Query OK, 2 rows affected (0.02 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql> TABLE tv2;
+---+---+---+
| x | y | z |
+---+---+---+
| 1 | 3 | 5 |
| 2 | 4 | 6 |
+---+---+---+
2 rows in set (0.00 sec)

mysql> CREATE TABLE tv3 (a INT, b INT, c INT)
    ->           SELECT * FROM (VALUES ROW(1,3,5), ROW(2,4,6)) AS v(x,y,z);
Query OK, 2 rows affected (0.02 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql> TABLE tv3;
+------+------+------+---+---+---+
| a    | b    | c    | x | y | z |
+------+------+------+---+---+---+
| NULL | NULL | NULL | 1 | 3 | 5 |
| NULL | NULL | NULL | 2 | 4 | 6 |
+------+------+------+---+---+---+
2 rows in set (0.00 sec)

mysql> CREATE TABLE tv4 (a INT, b INT, c INT)
    ->           SELECT * FROM (VALUES ROW(1,3,5), ROW(2,4,6)) AS v(x,y,z);
Query OK, 2 rows affected (0.02 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql> TABLE tv4;
+------+------+------+---+---+---+
| a    | b    | c    | x | y | z |
+------+------+------+---+---+---+
| NULL | NULL | NULL | 1 | 3 | 5 |
| NULL | NULL | NULL | 2 | 4 | 6 |
+------+------+------+---+---+---+
2 rows in set (0.00 sec)

mysql> CREATE TABLE tv5 (a INT, b INT, c INT)
    ->           SELECT * FROM (VALUES ROW(1,3,5), ROW(2,4,6)) AS v(a,b,c);
Query OK, 2 rows affected (0.02 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql> TABLE tv5;
+------+------+------+
| a    | b    | c    |
+------+------+------+
|    1 |    3 |    5 |
|    2 |    4 |    6 |
+------+------+------+
2 rows in set (0.00 sec)
```
### 13.1.20.5 FOREIGN KEY の制約
外部キー制約の例
```sql
mysql> CREATE TABLE parent (
    ->     id INT NOT NULL,
    ->     PRIMARY KEY (id)
    -> ) ENGINE=INNODB;
Query OK, 0 rows affected (0.05 sec)

mysql>
mysql> CREATE TABLE child (
    ->     id INT,
    ->     parent_id INT,
    ->     INDEX par_ind (parent_id),
    ->     FOREIGN KEY (parent_id)
    ->         REFERENCES parent(id)
    ->         ON DELETE CASCADE
    -> ) ENGINE=INNODB;
Query OK, 0 rows affected (0.05 sec)
```

```sql
mysql> CREATE TABLE product (
    ->     category INT NOT NULL, id INT NOT NULL,
    ->     price DECIMAL,
    ->     PRIMARY KEY(category, id)
    -> )   ENGINE=INNODB;
Query OK, 0 rows affected (0.02 sec)

mysql>
mysql> CREATE TABLE customer (
    ->     id INT NOT NULL,
    ->     PRIMARY KEY (id)
    -> )   ENGINE=INNODB;
Query OK, 0 rows affected (0.02 sec)

mysql>
mysql> CREATE TABLE product_order (
    ->     no INT NOT NULL AUTO_INCREMENT,
    ->     product_category INT NOT NULL,
    ->     product_id INT NOT NULL,
    ->     customer_id INT NOT NULL,
    ->
    ->     PRIMARY KEY(no),
    ->     INDEX (product_category, product_id),
    ->     INDEX (customer_id),
    ->
    ->     FOREIGN KEY (product_category, product_id)
    ->       REFERENCES product(category, id)
    ->       ON UPDATE CASCADE ON DELETE RESTRICT,
    ->
    ->     FOREIGN KEY (customer_id)
    ->       REFERENCES customer(id)
    -> )   ENGINE=INNODB;
Query OK, 0 rows affected (0.04 sec)

```
外部キー制約名の確認
```sql
mysql> SHOW CREATE TABLE child\G
*************************** 1. row ***************************
       Table: child
Create Table: CREATE TABLE `child` (
  `id` int DEFAULT NULL,
  `parent_id` int DEFAULT NULL,
  KEY `par_ind` (`parent_id`),
  CONSTRAINT `child_ibfk_1` FOREIGN KEY (`parent_id`) REFERENCES `parent` (`id`) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci
1 row in set (0.01 sec)
```
### 13.1.20.6 CHECK 制約
```Sql
mysql> CREATE TABLE t1
    -> (
    ->   CHECK (c1 <> c2),
    ->   c1 INT CHECK (c1 > 10),
    ->   c2 INT CONSTRAINT c2_positive CHECK (c2 > 0),
    ->   c3 INT CHECK (c3 < 100),
    ->   CONSTRAINT c1_nonzero CHECK (c1 <> 0),
    ->   CHECK (c1 > c3)
    -> );
Query OK, 0 rows affected (0.02 sec)
```
前述のテーブル定義に対して生成された名前を確認するには、SHOW CREATE TABLE を使用します
```sql
mysql> SHOW CREATE TABLE t1\G
*************************** 1. row ***************************
       Table: t1
Create Table: CREATE TABLE `t1` (
  `c1` int DEFAULT NULL,
  `c2` int DEFAULT NULL,
  `c3` int DEFAULT NULL,
  CONSTRAINT `c1_nonzero` CHECK ((`c1` <> 0)),
  CONSTRAINT `c2_positive` CHECK ((`c2` > 0)),
  CONSTRAINT `t1_chk_1` CHECK ((`c1` <> `c2`)),
  CONSTRAINT `t1_chk_2` CHECK ((`c1` > 10)),
  CONSTRAINT `t1_chk_3` CHECK ((`c3` < 100)),
  CONSTRAINT `t1_chk_4` CHECK ((`c1` > `c3`))
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci
1 row in set (0.00 sec)

```

### 13.1.20.7 暗黙のカラム指定の変更
MySQL は場合によって、カラム指定を CREATE TABLE または ALTER TABLE ステートメントで指定されたものから暗黙のうちに変更することがあります。
```sql
mysql> CREATE TABLE t
    -> (
    ->   c1 VARCHAR(10) CHARACTER SET binary,
    ->   c2 TEXT CHARACTER SET binary,
    ->   c3 ENUM('a','b','c') CHARACTER SET binary
    -> );
Query OK, 0 rows affected (0.02 sec)
```
結果のテーブルには、この定義が含まれています
```sql
CREATE TABLE t
(
  c1 VARBINARY(10),
  c2 BLOB,
  c3 ENUM('a','b','c') CHARACTER SET binary
);
```
### 13.1.20.8 CREATE TABLE および生成されるカラム
```sql
mysql> CREATE TABLE triangle (
    ->   sidea DOUBLE,
    ->   sideb DOUBLE,
    ->   sidec DOUBLE AS (SQRT(sidea * sidea + sideb * sideb))
    -> );
Query OK, 0 rows affected (0.03 sec)

mysql> INSERT INTO triangle (sidea, sideb) VALUES(1,1),(3,4),(6,8);
Query OK, 3 rows affected (0.01 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql> SELECT * FROM triangle;
+-------+-------+--------------------+
| sidea | sideb | sidec              |
+-------+-------+--------------------+
|     1 |     1 | 1.4142135623730951 |
|     3 |     4 |                  5 |
|     6 |     8 |                 10 |
+-------+-------+--------------------+
3 rows in set (0.00 sec)
```

```sql
mysql> CREATE TABLE t1 (
    ->   first_name VARCHAR(10),
    ->   last_name VARCHAR(10),
    ->   full_name VARCHAR(255) AS (CONCAT(first_name,' ',last_name))
    -> );
Query OK, 0 rows affected (0.02 sec)
```
### 13.1.20.9 セカンダリインデックスと生成されたカラム
InnoDB では、仮想生成カラムのセカンダリインデックスがサポートされます。
```sql
mysql> CREATE TABLE jemp (
    ->          c JSON,
    ->          g INT GENERATED ALWAYS AS (c->"$.id"),
    ->          INDEX i (g)
    ->      );
Query OK, 0 rows affected (0.05 sec)

mysql> INSERT INTO jemp (c) VALUES
    ->         ('{"id": "1", "name": "Fred"}'), ('{"id": "2", "name": "Wilma"}'),

    ->         ('{"id": "3", "name": "Barney"}'), ('{"id": "4", "name": "Betty"}');
Query OK, 4 rows affected (0.01 sec)
Records: 4  Duplicates: 0  Warnings: 0

mysql> SELECT c->>"$.name" AS name FROM jemp WHERE g > 2;
+--------+
| name   |
+--------+
| Barney |
| Betty  |
+--------+
2 rows in set (0.00 sec)

mysql> EXPLAIN SELECT c->>"$.name" AS name FROM jemp WHERE g > 2\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: jemp
   partitions: NULL
         type: range
possible_keys: i
          key: i
      key_len: 5
          ref: NULL
         rows: 2
     filtered: 100.00
        Extra: Using where
1 row in set, 1 warning (0.00 sec)

mysql> SHOW WARNINGS\G
*************************** 1. row ***************************
  Level: Note
   Code: 1003
Message: /* select#1 */ select json_unquote(json_extract(`sample`.`jemp`.`c`,'$.name')) AS `name` from `sample`.`jemp` where (`sample`.`jemp`.`g` > 2)
1 row in set (0.00 sec)
```

### 13.1.20.10 非表示カラム
```sql
mysql> CREATE TABLE t1 (
    ->   i INT,
    ->   j DATE INVISIBLE
    -> ) ENGINE = InnoDB;
Query OK, 0 rows affected (0.02 sec)

mysql> ALTER TABLE t1 ADD COLUMN k INT INVISIBLE;
Query OK, 0 rows affected (0.02 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE t1 CHANGE COLUMN j j DATE VISIBLE;
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE t1 MODIFY COLUMN j DATE INVISIBLE;
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE t1 ALTER COLUMN j SET VISIBLE;
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
CREATE TABLE ... SELECT には、SELECT 部分で明示的に参照されないかぎり、不可視のカラムは含まれません
```Sql
mysql> CREATE TABLE t1 (col1 INT, col2 INT INVISIBLE);
Query OK, 0 rows affected (0.02 sec)

mysql> CREATE TABLE t2 AS SELECT col1, col2 FROM t1;
Query OK, 0 rows affected (0.02 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> SHOW CREATE TABLE t2\G
*************************** 1. row ***************************
       Table: t2
Create Table: CREATE TABLE `t2` (
  `col1` int DEFAULT NULL,
  `col2` int DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci
1 row in set (0.00 sec)
```
DML ステートメントと非表示カラム
```sql
mysql> CREATE TABLE t1 (col1 INT, col2 INT INVISIBLE);
Query OK, 0 rows affected (0.02 sec)

mysql> INSERT INTO t1 (col1, col2) VALUES(1, 2), (3, 4);
Query OK, 2 rows affected (0.01 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql> SELECT * FROM t1;
+------+
| col1 |
+------+
|    1 |
|    3 |
+------+
2 rows in set (0.00 sec)

mysql> SELECT col1, col2 FROM t1;
+------+------+
| col1 | col2 |
+------+------+
|    1 |    2 |
|    3 |    4 |
+------+------+
2 rows in set (0.00 sec)

mysql> SELECT TABLE_NAME, COLUMN_NAME, EXTRA
    ->        FROM INFORMATION_SCHEMA.COLUMNS
    ->        WHERE TABLE_SCHEMA = 'sample' AND TABLE_NAME = 't1';
+------------+-------------+-----------+
| TABLE_NAME | COLUMN_NAME | EXTRA     |
+------------+-------------+-----------+
| t1         | col1        |           |
| t1         | col2        | INVISIBLE |
+------------+-------------+-----------+
2 rows in set (0.00 sec)
```
### 13.1.21 CREATE TABLESPACE ステートメント

```sql
mysql> CREATE TABLESPACE `ts1` ADD DATAFILE 'ts1.ibd' ENGINE=INNODB;
Query OK, 0 rows affected (0.01 sec)

mysql> CREATE TABLE t1 (c1 INT PRIMARY KEY) TABLESPACE ts1 ROW_FORMAT=REDUNDANT;
Query OK, 0 rows affected (0.01 sec)

mysql> CREATE TABLE t2 (c1 INT PRIMARY KEY) TABLESPACE ts1 ROW_FORMAT=COMPACT;
Query OK, 0 rows affected (0.01 sec)

mysql>  CREATE TABLE t3 (c1 INT PRIMARY KEY) TABLESPACE ts1 ROW_FORMAT=DYNAMIC;
Query OK, 0 rows affected (0.01 sec)

mysql> CREATE TABLESPACE `ts2` ADD DATAFILE 'ts2.ibd' FILE_BLOCK_SIZE = 8192 Engine=InnoDB;
Query OK, 0 rows affected (0.01 sec)

mysql> CREATE TABLE t4 (c1 INT PRIMARY KEY) TABLESPACE ts2 ROW_FORMAT=COMPRESSED KEY_BLOCK_SIZE=8;
Query OK, 0 rows affected (0.01 sec)

mysql> CREATE TABLESPACE `ts3` ENGINE=INNODB;
Query OK, 0 rows affected (0.02 sec)

mysql> CREATE UNDO TABLESPACE undo_003 ADD DATAFILE 'undo_003.ibu';
Query OK, 0 rows affected (0.34 sec)
```

### 13.1.23 CREATE VIEW ステートメント
```sql
mysql> CREATE TABLE t (qty INT, price INT);
Query OK, 0 rows affected (0.02 sec)

mysql> INSERT INTO t VALUES(3, 50);
Query OK, 1 row affected (0.00 sec)

mysql> CREATE VIEW v AS SELECT qty, price, qty*price AS value FROM t;
Query OK, 0 rows affected (0.01 sec)

mysql> SELECT * FROM v;
+------+-------+-------+
| qty  | price | value |
+------+-------+-------+
|    3 |    50 |   150 |
+------+-------+-------+
1 row in set (0.00 sec)
```

```sql
mysql> CREATE VIEW v (mycol) AS SELECT 'abc';
Query OK, 0 rows affected (0.00 sec)

mysql> SET sql_mode = '';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT "mycol" FROM v;
+-------+
| mycol |
+-------+
| mycol |
+-------+
1 row in set (0.00 sec)

mysql> SET sql_mode = 'ANSI_QUOTES';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT "mycol" FROM v;
+-------+
| mycol |
+-------+
| abc   |
+-------+
1 row in set (0.00 sec)
```

### 13.1.36 RENAME TABLE ステートメント
```sql
mysql> create table old_table(a int);
Query OK, 0 rows affected (0.02 sec)

mysql> RENAME TABLE old_table TO new_table;
Query OK, 0 rows affected (0.02 sec)
```
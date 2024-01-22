### 13.2.11.1 スカラーオペランドとしてのサブクエリー
```sql
mysql> CREATE TABLE t1 (s1 INT, s2 CHAR(5) NOT NULL);
Query OK, 0 rows affected (0.02 sec)

mysql> INSERT INTO t1 VALUES(100, 'abcde');
Query OK, 1 row affected (0.00 sec)

mysql> SELECT (SELECT s2 FROM t1);
+---------------------+
| (SELECT s2 FROM t1) |
+---------------------+
| abcde               |
+---------------------+
1 row in set (0.01 sec)
```

```sql
mysql> CREATE TABLE t1 (s1 INT);
Query OK, 0 rows affected (0.03 sec)

mysql> INSERT INTO t1 VALUES (1);
Query OK, 1 row affected (0.01 sec)

mysql> CREATE TABLE t2 (s1 INT);
Query OK, 0 rows affected (0.02 sec)

mysql> INSERT INTO t2 VALUES (2);
Query OK, 1 row affected (0.00 sec)

mysql> SELECT (SELECT s1 FROM t2) FROM t1;
+---------------------+
| (SELECT s1 FROM t2) |
+---------------------+
|                   2 |
+---------------------+
1 row in set (0.00 sec)

mysql> SELECT UPPER((SELECT s1 FROM t1)) FROM t2;
+----------------------------+
| UPPER((SELECT s1 FROM t1)) |
+----------------------------+
| 1                          |
+----------------------------+
1 row in set (0.00 sec)
```
### 13.2.11.2 サブクエリーを使用した比較
```sql
mysql> SELECT * FROM t1
    ->   WHERE s1 = (SELECT MAX(s1)-1 FROM t2);
+------+
| s1   |
+------+
|    1 |
+------+
1 row in set (0.00 sec)
```
### 13.2.11.3 ANY、IN、または SOME を使用したサブクエリー
```sql
mysql> SELECT s1 FROM t1 WHERE s1 = ANY (SELECT s1 FROM t2);
+------+
| s1   |
+------+
|    1 |
+------+
1 row in set (0.00 sec)
mysql> SELECT s1 FROM t1 WHERE s1 <> SOME (SELECT s1 FROM t2);
+------+
| s1   |
+------+
|    1 |
+------+
1 row in set (0.00 sec)
```

### 13.2.11.4 ALL を使用したサブクエリー
```sql
mysql> SELECT * FROM t1 WHERE 1 > ALL (SELECT s1 FROM t2);
Empty set (0.00 sec)

mysql> truncate table t2;
Query OK, 0 rows affected (0.03 sec)

mysql> SELECT * FROM t1 WHERE 1 > ALL (SELECT s1 FROM t2);
+------+
| s1   |
+------+
|    3 |
+------+
1 row in set (0.00 sec)
```
### 13.2.11.5 行サブクエリー
```sql
mysql> SELECT * FROM t1
    ->   WHERE (s1) = (SELECT s1 FROM t2 WHERE s1 = 3);
+------+
| s1   |
+------+
|    3 |
+------+
1 row in set (0.00 sec)
```
```sql
mysql> create table t1 (a int, b int);
Query OK, 0 rows affected (0.03 sec)

mysql> insert into t1 values (1,2);
Query OK, 1 row affected (0.00 sec)

mysql> SELECT * FROM t1 WHERE (a,b) = (1,2);
+------+------+
| a    | b    |
+------+------+
|    1 |    2 |
+------+------+
1 row in set (0.00 sec)

mysql> SELECT * FROM t1 WHERE a = 1 AND b = 2;
+------+------+
| a    | b    |
+------+------+
|    1 |    2 |
+------+------+
1 row in set (0.00 sec)

```
### 13.2.11.6 EXISTS または NOT EXISTS を使用したサブクエリー
```sql
mysql> SELECT a FROM t1 WHERE EXISTS (SELECT * FROM t2);
+------+
| a    |
+------+
|    1 |
+------+
1 row in set (0.00 sec)

mysql> SELECT b FROM t1 WHERE EXISTS (TABLE t2);
+------+
| b    |
+------+
|    2 |
+------+
1 row in set (0.00 sec)
```
### 13.2.11.7 相関サブクエリー
```sql
mysql> SELECT * FROM t1
    ->   WHERE a = ANY (SELECT s1 FROM t2 WHERE t2.s1 = t1.b);
Empty set (0.00 sec)
```
### 13.2.11.8 導出テーブル
```sql
mysql> CREATE TABLE t1 (s1 INT, s2 CHAR(5), s3 FLOAT);
Query OK, 0 rows affected (0.02 sec)

mysql> INSERT INTO t1 VALUES (1,'1',1.0);
Query OK, 1 row affected (0.01 sec)

mysql> INSERT INTO t1 VALUES (2,'2',2.0);
Query OK, 1 row affected (0.00 sec)

mysql> SELECT sb1,sb2,sb3
    ->   FROM (SELECT s1 AS sb1, s2 AS sb2, s3*2 AS sb3 FROM t1) AS sb
    ->   WHERE sb1 > 1;
+------+------+------+
| sb1  | sb2  | sb3  |
+------+------+------+
|    2 | 2    |    4 |
+------+------+------+
1 row in set (0.00 sec)

mysql> SELECT * FROM (SELECT 1, 2, 3, 4) AS dt;
+---+---+---+---+
| 1 | 2 | 3 | 4 |
+---+---+---+---+
| 1 | 2 | 3 | 4 |
+---+---+---+---+
1 row in set (0.00 sec)

mysql> SELECT * FROM (SELECT 1, 2, 3, 4) AS dt (a, b, c, d);
+---+---+---+---+
| a | b | c | d |
+---+---+---+---+
| 1 | 2 | 3 | 4 |
+---+---+---+---+
1 row in set (0.00 sec)
```
### 13.2.12 TABLE ステートメント
```sql
mysql> TABLE t;
+------+------+------+
| i    | d    | f    |
+------+------+------+
|    1 |    1 |    1 |
+------+------+------+
1 row in set (0.02 sec)

mysql> TABLE t order by i;
+------+------+------+
| i    | d    | f    |
+------+------+------+
|    1 |    1 |    1 |
+------+------+------+
1 row in set (0.00 sec)

mysql> TABLE t order by i limit 3;
+------+------+------+
| i    | d    | f    |
+------+------+------+
|    1 |    1 |    1 |
+------+------+------+
1 row in set (0.00 sec)

mysql> TABLE city UNION TABLE actor limit 5;
+---------+----------------------+------------+---------------------+
| city_id | city                 | country_id | last_update         |
+---------+----------------------+------------+---------------------+
|       1 | A Coru?a (La Coru?a) | 87         | 2006-02-15 04:45:25 |
|       2 | Abha                 | 82         | 2006-02-15 04:45:25 |
|       3 | Abu Dhabi            | 101        | 2006-02-15 04:45:25 |
|       4 | Acu?a                | 60         | 2006-02-15 04:45:25 |
|       5 | Adana                | 97         | 2006-02-15 04:45:25 |
+---------+----------------------+------------+---------------------+
5 rows in set (0.00 sec)
```
### 13.2.13 UPDATE ステートメント
```sql
mysql> UPDATE t SET i = i + 1;
Query OK, 1 row affected (0.01 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> UPDATE t SET i = d + 1, d = i;
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> table t;
+------+------+------+
| i    | d    | f    |
+------+------+------+
|    2 |    2 |    1 |
+------+------+------+
1 row in set (0.00 sec)
```

```sql
mysql> CREATE TABLE items (
    ->     id BIGINT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    ->     wholesale DECIMAL(6,2) NOT NULL DEFAULT 0.00,
    ->     retail DECIMAL(6,2) NOT NULL DEFAULT 0.00,
    ->     quantity BIGINT NOT NULL DEFAULT 0
    -> );
Query OK, 0 rows affected (0.03 sec)

mysql> UPDATE items,
    ->        (SELECT id FROM items
    ->         WHERE id IN
    ->             (SELECT id FROM items
    ->              WHERE retail / wholesale >= 1.3 AND quantity < 100))
    ->         AS discounted
    -> SET items.retail = items.retail * 0.9
    -> WHERE items.id = discounted.id;
ERROR 1093 (HY000): You can't specify target table 'items' for update in FROM clause
mysql> UPDATE /*+ NO_MERGE(discounted) */ items,
    ->        (SELECT id FROM items
    ->         WHERE retail / wholesale >= 1.3 AND quantity < 100)
    ->         AS discounted
    ->     SET items.retail = items.retail * 0.9
    ->     WHERE items.id = discounted.id;
Query OK, 0 rows affected (0.00 sec)
Rows matched: 0  Changed: 0  Warnings: 0
```

### 13.2.14 VALUES ステートメント
```sql
mysql> VALUES ROW(1,-2,3), ROW(5,7,9), ROW(4,6,8);
+----------+----------+----------+
| column_0 | column_1 | column_2 |
+----------+----------+----------+
|        1 |       -2 |        3 |
|        5 |        7 |        9 |
|        4 |        6 |        8 |
+----------+----------+----------+
3 rows in set (0.00 sec)

mysql> VALUES ROW(1,-2,3), ROW(5,7,9), ROW(4,6,8) ORDER BY column_2;
+----------+----------+----------+
| column_0 | column_1 | column_2 |
+----------+----------+----------+
|        1 |       -2 |        3 |
|        5 |        7 |        9 |
|        4 |        6 |        8 |
+----------+----------+----------+
3 rows in set (0.00 sec)


mysql> VALUES ROW("q", 42, '2019-12-18'),
    ->          ROW(23, "abc", 98.6),
    ->          ROW(27.0002, "Mary Smith", '{"a": 10, "b": 25}');
+----------+------------+--------------------+
| column_0 | column_1   | column_2           |
+----------+------------+--------------------+
| q        | 42         | 2019-12-18         |
| 23       | abc        | 98.6               |
| 27.0002  | Mary Smith | {"a": 10, "b": 25} |
+----------+------------+--------------------+
3 rows in set (0.00 sec)

mysql> VALUES ROW(1,2) UNION VALUES ROW(10,15);
+----------+----------+
| column_0 | column_1 |
+----------+----------+
|        1 |        2 |
|       10 |       15 |
+----------+----------+
2 rows in set (0.00 sec)
```
### 13.2.15 WITH (共通テーブル式)
```sql
mysql> WITH cte (col1, col2) AS
    -> (
    ->   SELECT 1, 2
    ->   UNION ALL
    ->   SELECT 3, 4
    -> )
    -> SELECT col1, col2 FROM cte;
+------+------+
| col1 | col2 |
+------+------+
|    1 |    2 |
|    3 |    4 |
+------+------+
2 rows in set (0.00 sec)

mysql> WITH cte AS
    -> (
    ->   SELECT 1 AS col1, 2 AS col2
    ->   UNION ALL
    ->   SELECT 3, 4
    -> )
    -> SELECT col1, col2 FROM cte;
+------+------+
| col1 | col2 |
+------+------+
|    1 |    2 |
|    3 |    4 |
+------+------+
2 rows in set (0.00 sec)

mysql> WITH cte1 AS (SELECT 1)
    -> SELECT * FROM (WITH cte2 AS (SELECT 2) SELECT * FROM cte2 JOIN cte1) AS dt;
+---+---+
| 2 | 1 |
+---+---+
| 2 | 1 |
+---+---+
1 row in set (0.00 sec)
```
再帰的な共通テーブル式
```sql
mysql> WITH RECURSIVE cte (n) AS
    -> (
    ->   SELECT 1
    ->   UNION ALL
    ->   SELECT n + 1 FROM cte WHERE n < 5
    -> )
    -> SELECT * FROM cte;
+------+
| n    |
+------+
|    1 |
|    2 |
|    3 |
|    4 |
|    5 |
+------+
5 rows in set (0.00 sec)

mysql> WITH RECURSIVE cte AS
    -> (
    ->   SELECT 1 AS n, CAST('abc' AS CHAR(20)) AS str
    ->   UNION ALL
    ->   SELECT n + 1, CONCAT(str, str) FROM cte WHERE n < 3
    -> )
    -> SELECT * FROM cte;
+------+--------------+
| n    | str          |
+------+--------------+
|    1 | abc          |
|    2 | abcabc       |
|    3 | abcabcabcabc |
+------+--------------+
3 rows in set (0.00 sec)

mysql> WITH RECURSIVE cte AS
    -> (
    ->   SELECT 1 AS n, 1 AS p, -1 AS q
    ->   UNION ALL
    ->   SELECT n + 1, q * 2, p * 2 FROM cte WHERE n < 5
    -> )
    -> SELECT * FROM cte;
+------+------+------+
| n    | p    | q    |
+------+------+------+
|    1 |    1 |   -1 |
|    2 |   -2 |    2 |
|    3 |    4 |   -4 |
|    4 |   -8 |    8 |
|    5 |   16 |  -16 |
+------+------+------+
5 rows in set (0.01 sec)
```
共通テーブル式の再帰の制限
```sql
mysql> WITH RECURSIVE cte (n) AS
    -> (
    ->   SELECT 1
    ->   UNION ALL
    ->   SELECT n + 1 FROM cte
    -> )
    -> SELECT * FROM cte;
ERROR 3636 (HY000): Recursive query aborted after 1001 iterations. Try increasing @@cte_max_recursion_depth to a larger value.

mysql> SET SESSION cte_max_recursion_depth = 10;
Query OK, 0 rows affected (0.00 sec)

mysql> WITH RECURSIVE cte (n) AS
    -> (
    ->   SELECT 1
    ->   UNION ALL
    ->   SELECT n + 1 FROM cte
    -> )
    -> SELECT * FROM cte;
ERROR 3636 (HY000): Recursive query aborted after 11 iterations. Try increasing @@cte_max_recursion_depth to a larger value.

mysql> WITH RECURSIVE cte (n) AS
    -> (
    ->   SELECT 1
    ->   UNION ALL
    ->   SELECT n + 1 FROM cte
    -> )
    -> SELECT /*+ SET_VAR(cte_max_recursion_depth = 1M) */ * FROM cte;
ERROR 3636 (HY000): Recursive query aborted after 1048577 iterations. Try increasing @@cte_max_recursion_depth to a larger value.
```

```sql
mysql> WITH RECURSIVE cte (n) AS
    -> (
    ->   SELECT 1
    ->   UNION ALL
    ->   SELECT n + 1 FROM cte LIMIT 10
    -> )
    -> SELECT * FROM cte;
+------+
| n    |
+------+
|    1 |
|    2 |
|    3 |
|    4 |
|    5 |
|    6 |
|    7 |
|    8 |
|    9 |
|   10 |
+------+
10 rows in set (0.00 sec)
```
フィボナッチシリーズ世代
```sql
mysql> WITH RECURSIVE fibonacci (n, fib_n, next_fib_n) AS
    -> (
    ->   SELECT 1, 0, 1
    ->   UNION ALL
    ->   SELECT n + 1, next_fib_n, fib_n + next_fib_n
    ->     FROM fibonacci WHERE n < 10
    -> )
    -> SELECT * FROM fibonacci;
+------+-------+------------+
| n    | fib_n | next_fib_n |
+------+-------+------------+
|    1 |     0 |          1 |
|    2 |     1 |          1 |
|    3 |     1 |          2 |
|    4 |     2 |          3 |
|    5 |     3 |          5 |
|    6 |     5 |          8 |
|    7 |     8 |         13 |
|    8 |    13 |         21 |
|    9 |    21 |         34 |
|   10 |    34 |         55 |
+------+-------+------------+
10 rows in set (0.00 sec)
```
階層データトラバース
```sql
mysql> CREATE TABLE employees (
    ->   id         INT PRIMARY KEY NOT NULL,
    ->   name       VARCHAR(100) NOT NULL,
    ->   manager_id INT NULL,
    ->   INDEX (manager_id),
    -> FOREIGN KEY (manager_id) REFERENCES employees (id)
    -> );
Query OK, 0 rows affected (0.07 sec)

mysql> INSERT INTO employees VALUES
    -> (333, "Yasmina", NULL),  # Yasmina is the CEO (manager_id is NULL)
    -> (198, "John", 333),      # John has ID 198 and reports to 333 (Yasmina)
    -> (692, "Tarek", 333),
    -> (29, "Pedro", 198),
    -> (4610, "Sarah", 29),
    -> (72, "Pierre", 29),
    -> (123, "Adil", 692);
Query OK, 7 rows affected (0.01 sec)
Records: 7  Duplicates: 0  Warnings: 0

mysql> SELECT * FROM employees ORDER BY id;
+------+---------+------------+
| id   | name    | manager_id |
+------+---------+------------+
|   29 | Pedro   |        198 |
|   72 | Pierre  |         29 |
|  123 | Adil    |        692 |
|  198 | John    |        333 |
|  333 | Yasmina |       NULL |
|  692 | Tarek   |        333 |
| 4610 | Sarah   |         29 |
+------+---------+------------+
7 rows in set (0.00 sec)

mysql> WITH RECURSIVE employee_paths (id, name, path) AS
    -> (
    ->   SELECT id, name, CAST(id AS CHAR(200))
    ->     FROM employees
    ->     WHERE manager_id IS NULL
    ->   UNION ALL
    ->   SELECT e.id, e.name, CONCAT(ep.path, ',', e.id)
    ->     FROM employee_paths AS ep JOIN employees AS e
    ->       ON ep.id = e.manager_id
    -> )
    -> SELECT * FROM employee_paths ORDER BY path;
+------+---------+-----------------+
| id   | name    | path            |
+------+---------+-----------------+
|  333 | Yasmina | 333             |
|  198 | John    | 333,198         |
|   29 | Pedro   | 333,198,29      |
| 4610 | Sarah   | 333,198,29,4610 |
|   72 | Pierre  | 333,198,29,72   |
|  692 | Tarek   | 333,692         |
|  123 | Adil    | 333,692,123     |
+------+---------+-----------------+
7 rows in set (0.01 sec)
```
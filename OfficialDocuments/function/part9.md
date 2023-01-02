### 12.19 グローバルトランザクション識別子 (GTID) で使用される機能
 GTID ベースのレプリケーションで使用されます。
```sql
mysql> CREATE FUNCTION GTID_UNION(g1 TEXT, g2 TEXT)
    ->     RETURNS TEXT DETERMINISTIC
    ->     RETURN CONCAT(g1,',',g2);
ERROR 2013 (HY000): Lost connection to MySQL server during query
No connection. Trying to reconnect...
Connection id:    9
Current database: sakila
```
* GTID_SUBSET(set1,set2)
```sql
mysql> SELECT GTID_SUBSET('3E11FA47-71CA-11E1-9E33-C80AA9429562:23',
    ->          '3E11FA47-71CA-11E1-9E33-C80AA9429562:21-57')\G
*************************** 1. row ***************************
GTID_SUBSET('3E11FA47-71CA-11E1-9E33-C80AA9429562:23',
         '3E11FA47-71CA-11E1-9E33-C80AA9429562:21-57'): 1
1 row in set (0.00 sec)

mysql> SELECT GTID_SUBSET('3E11FA47-71CA-11E1-9E33-C80AA9429562:23-25',
    ->          '3E11FA47-71CA-11E1-9E33-C80AA9429562:21-57')\G
*************************** 1. row ***************************
GTID_SUBSET('3E11FA47-71CA-11E1-9E33-C80AA9429562:23-25',
         '3E11FA47-71CA-11E1-9E33-C80AA9429562:21-57'): 1
1 row in set (0.00 sec)

mysql> SELECT GTID_SUBSET('3E11FA47-71CA-11E1-9E33-C80AA9429562:20-25',
    ->          '3E11FA47-71CA-11E1-9E33-C80AA9429562:21-57')\G
*************************** 1. row ***************************
GTID_SUBSET('3E11FA47-71CA-11E1-9E33-C80AA9429562:20-25',
         '3E11FA47-71CA-11E1-9E33-C80AA9429562:21-57'): 0
1 row in set (0.00 sec)
```
* GTID_SUBTRACT(set1,set2)
```Sql
mysql> SELECT GTID_SUBTRACT('3E11FA47-71CA-11E1-9E33-C80AA9429562:21-57',
    ->          '3E11FA47-71CA-11E1-9E33-C80AA9429562:21')\G
*************************** 1. row ***************************
GTID_SUBTRACT('3E11FA47-71CA-11E1-9E33-C80AA9429562:21-57',
         '3E11FA47-71CA-11E1-9E33-C80AA9429562:21'): 3e11fa47-71ca-11e1-9e33-c80aa9429562:22-57
1 row in set (0.00 sec)

mysql> SELECT GTID_SUBTRACT('3E11FA47-71CA-11E1-9E33-C80AA9429562:21-57',
    ->          '3E11FA47-71CA-11E1-9E33-C80AA9429562:20-25')\G
*************************** 1. row ***************************
GTID_SUBTRACT('3E11FA47-71CA-11E1-9E33-C80AA9429562:21-57',
         '3E11FA47-71CA-11E1-9E33-C80AA9429562:20-25'): 3e11fa47-71ca-11e1-9e33-c80aa9429562:26-57
1 row in set (0.00 sec)

mysql> SELECT GTID_SUBTRACT('3E11FA47-71CA-11E1-9E33-C80AA9429562:21-57',
    ->          '3E11FA47-71CA-11E1-9E33-C80AA9429562:23-24')\G
*************************** 1. row ***************************
GTID_SUBTRACT('3E11FA47-71CA-11E1-9E33-C80AA9429562:21-57',
         '3E11FA47-71CA-11E1-9E33-C80AA9429562:23-24'): 3e11fa47-71ca-11e1-9e33-c80aa9429562:21-22:25-57
1 row in set (0.00 sec)
```
### 12.20 集計関数
### 12.20.3 MySQL での GROUP BY の処理
```sql
mysql> CREATE TABLE mytable (
    ->         id INT UNSIGNED NOT NULL PRIMARY KEY,
    ->         a VARCHAR(10),
    ->         b INT
    ->      );
Query OK, 0 rows affected (0.03 sec)

mysql>
mysql> INSERT INTO mytable
    ->      VALUES (1, 'abc', 1000),
    ->             (2, 'abc', 2000),
    ->             (3, 'def', 4000);
Query OK, 3 rows affected (0.01 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql> SET SESSION sql_mode = sys.list_add(@@session.sql_mode, 'ONLY_FULL_GROUP_BY');
Query OK, 0 rows affected (0.01 sec)

mysql> SELECT a, SUM(b) FROM mytable WHERE a = 'abc';
+------+--------+
| a    | SUM(b) |
+------+--------+
| abc  |   3000 |
+------+--------+
1 row in set (0.01 sec)

mysql> CREATE TABLE mytable (
    ->         id INT UNSIGNED NOT NULL PRIMARY KEY,
    ->         a VARCHAR(10),
    ->         b VARCHAR(10),
    ->         c INT
    ->      );
Query OK, 0 rows affected (0.03 sec)

mysql> INSERT INTO mytable
    ->      VALUES (1, 'abc', 'qrs', 1000),
    ->             (2, 'abc', 'tuv', 2000),
    ->             (3, 'def', 'qrs', 4000),
    ->             (4, 'def', 'tuv', 8000),
    ->             (5, 'abc', 'qrs', 16000),
    ->             (6, 'def', 'tuv', 32000);
Query OK, 6 rows affected (0.01 sec)
Records: 6  Duplicates: 0  Warnings: 0

mysql> SELECT @@session.sql_mode;
+-----------------------------------------------------------------------------------------------------------------------+
| @@session.sql_mode
|
+-----------------------------------------------------------------------------------------------------------------------+
| ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION |
+-----------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT a, b, SUM(c) FROM mytable
    ->          WHERE a = 'abc' AND b = 'qrs';
+------+------+--------+
| a    | b    | SUM(c) |
+------+------+--------+
| abc  | qrs  |  17000 |
+------+------+--------+
1 row in set (0.00 sec)
```
### 12.22 パフォーマンススキーマ関数
* FORMAT_BYTES(count)
```sql
mysql> SELECT FORMAT_BYTES(512), FORMAT_BYTES(18446644073709551615);
+-------------------+------------------------------------+
| FORMAT_BYTES(512) | FORMAT_BYTES(18446644073709551615) |
+-------------------+------------------------------------+
|  512 bytes        | 16.00 EiB                          |
+-------------------+------------------------------------+
1 row in set (0.00 sec)
```
* FORMAT_PICO_TIME(time_val)
```sql
mysql> SELECT FORMAT_PICO_TIME(3501), FORMAT_PICO_TIME(188732396662000);
+------------------------+-----------------------------------+
| FORMAT_PICO_TIME(3501) | FORMAT_PICO_TIME(188732396662000) |
+------------------------+-----------------------------------+
| 3.50 ns                | 3.15 min                          |
+------------------------+-----------------------------------+
1 row in set (0.00 sec)
```
* PS_CURRENT_THREAD_ID()
```sql
mysql> SELECT PS_CURRENT_THREAD_ID();
+------------------------+
| PS_CURRENT_THREAD_ID() |
+------------------------+
|                     51 |
+------------------------+
1 row in set (0.00 sec)

mysql> SELECT PS_THREAD_ID(CONNECTION_ID());
+-------------------------------+
| PS_THREAD_ID(CONNECTION_ID()) |
+-------------------------------+
|                            51 |
+-------------------------------+
1 row in set (0.00 sec)
```
* PS_THREAD_ID(connection_id)
```sql
mysql> SELECT PS_THREAD_ID(6);
+-----------------+
| PS_THREAD_ID(6) |
+-----------------+
|            NULL |
+-----------------+
1 row in set (0.00 sec)
```

### 12.24 その他の関数
* INET6_NTOA(expr)
```sql
mysql> SELECT INET6_NTOA(INET6_ATON('fdfe::5a55:caff:fefa:9089'));
+-----------------------------------------------------+
| INET6_NTOA(INET6_ATON('fdfe::5a55:caff:fefa:9089')) |
+-----------------------------------------------------+
| fdfe::5a55:caff:fefa:9089                           |
+-----------------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT INET6_NTOA(INET6_ATON('10.0.5.9'));
+------------------------------------+
| INET6_NTOA(INET6_ATON('10.0.5.9')) |
+------------------------------------+
| 10.0.5.9                           |
+------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT INET6_NTOA(UNHEX('FDFE0000000000005A55CAFFFEFA9089'));
+-------------------------------------------------------+
| INET6_NTOA(UNHEX('FDFE0000000000005A55CAFFFEFA9089')) |
+-------------------------------------------------------+
| fdfe::5a55:caff:fefa:9089                             |
+-------------------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT INET6_NTOA(UNHEX('0A000509'));
+-------------------------------+
| INET6_NTOA(UNHEX('0A000509')) |
+-------------------------------+
| 10.0.5.9                      |
+-------------------------------+
1 row in set (0.00 sec)
```
* IS_IPV4(expr)
```Sql
mysql> SELECT IS_IPV4('10.0.5.9'), IS_IPV4('10.0.5.256');
+---------------------+-----------------------+
| IS_IPV4('10.0.5.9') | IS_IPV4('10.0.5.256') |
+---------------------+-----------------------+
|                   1 |                     0 |
+---------------------+-----------------------+
1 row in set (0.00 sec)

```
* IS_IPV4_COMPAT(expr)
```Sql
mysql> SELECT IS_IPV4_COMPAT(INET6_ATON('::10.0.5.9'));
+------------------------------------------+
| IS_IPV4_COMPAT(INET6_ATON('::10.0.5.9')) |
+------------------------------------------+
|                                        1 |
+------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT IS_IPV4_COMPAT(INET6_ATON('::ffff:10.0.5.9'));
+-----------------------------------------------+
| IS_IPV4_COMPAT(INET6_ATON('::ffff:10.0.5.9')) |
+-----------------------------------------------+
|                                             0 |
+-----------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT HEX(INET6_ATON('198.51.100.1'));
+---------------------------------+
| HEX(INET6_ATON('198.51.100.1')) |
+---------------------------------+
| C6336401                        |
+---------------------------------+
1 row in set (0.00 sec)
```
* IS_IPV4_MAPPED(expr)
```Sql
mysql> SELECT IS_IPV4_MAPPED(INET6_ATON('::10.0.5.9'));
+------------------------------------------+
| IS_IPV4_MAPPED(INET6_ATON('::10.0.5.9')) |
+------------------------------------------+
|                                        0 |
+------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT IS_IPV4_MAPPED(INET6_ATON('::ffff:10.0.5.9'));
+-----------------------------------------------+
| IS_IPV4_MAPPED(INET6_ATON('::ffff:10.0.5.9')) |
+-----------------------------------------------+
|                                             1 |
+-----------------------------------------------+
1 row in set (0.00 sec)
```
* IS_IPV6(expr)
```sql
mysql> SELECT IS_IPV6('10.0.5.9'), IS_IPV6('::1');
+---------------------+----------------+
| IS_IPV6('10.0.5.9') | IS_IPV6('::1') |
+---------------------+----------------+
|                   0 |              1 |
+---------------------+----------------+
1 row in set (0.00 sec)
```
* IS_UUID(string_uuid)
```sql
mysql> SELECT IS_UUID('6ccd780c-baba-1026-9564-5b8c656024db');
+-------------------------------------------------+
| IS_UUID('6ccd780c-baba-1026-9564-5b8c656024db') |
+-------------------------------------------------+
|                                               1 |
+-------------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT IS_UUID('6CCD780C-BABA-1026-9564-5B8C656024DB');
+-------------------------------------------------+
| IS_UUID('6CCD780C-BABA-1026-9564-5B8C656024DB') |
+-------------------------------------------------+
|                                               1 |
+-------------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT IS_UUID('6ccd780c-baba-1026-9564-5b8c6560');
+---------------------------------------------+
| IS_UUID('6ccd780c-baba-1026-9564-5b8c6560') |
+---------------------------------------------+
|                                           0 |
+---------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT IS_UUID(RAND());
+-----------------+
| IS_UUID(RAND()) |
+-----------------+
|               0 |
+-----------------+
1 row in set (0.00 sec)
```
* NAME_CONST(name,value)
```sql
mysql> SELECT NAME_CONST('myname', 14);
+--------+
| myname |
+--------+
|     14 |
+--------+
1 row in set (0.00 sec)
```
* SLEEP(duration)
```Sql
mysql> SELECT SLEEP(1000);
+-------------+
| SLEEP(1000) |
+-------------+
|           0 |
+-------------+
1 row in set (16 min 40.00 sec)

mysql> SELECT /*+ MAX_EXECUTION_TIME(100) */ 1 FROM actor WHERE SLEEP(100);
ERROR 3024 (HY000): Query execution was interrupted, maximum statement execution time exceeded
```
* UUID()
```Sql
mysql> SELECT UUID();
+--------------------------------------+
| UUID()                               |
+--------------------------------------+
| d02cb059-9f2f-11ee-a517-e8e0b71f826a |
+--------------------------------------+
1 row in set (0.01 sec)
```
* UUID_SHORT()
```sql
mysql> SELECT UUID_SHORT();
+--------------------+
| UUID_SHORT()       |
+--------------------+
| 100629564132163584 |
+--------------------+
1 row in set (0.00 sec)
```
* UUID_TO_BIN(string_uuid), UUID_TO_BIN(string_uuid, swap_flag)
```sql
mysql> SET @uuid = '6ccd780c-baba-1026-9564-5b8c656024db';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT HEX(UUID_TO_BIN(@uuid));
+----------------------------------+
| HEX(UUID_TO_BIN(@uuid))          |
+----------------------------------+
| 6CCD780CBABA102695645B8C656024DB |
+----------------------------------+
1 row in set (0.00 sec)

mysql> SELECT HEX(UUID_TO_BIN(@uuid, 0));
+----------------------------------+
| HEX(UUID_TO_BIN(@uuid, 0))       |
+----------------------------------+
| 6CCD780CBABA102695645B8C656024DB |
+----------------------------------+
1 row in set (0.00 sec)
```
### 12.25.5 高精度計算の例
```sql
mysql> SELECT (.1 + .2) = .3;
+----------------+
| (.1 + .2) = .3 |
+----------------+
|              1 |
+----------------+
1 row in set (0.00 sec)

mysql> SELECT (.1E0 + .2E0) = .3E0;
+----------------------+
| (.1E0 + .2E0) = .3E0 |
+----------------------+
|                    0 |
+----------------------+
1 row in set (0.00 sec)

mysql> SELECT .01 * .01;
+-----------+
| .01 * .01 |
+-----------+
|    0.0001 |
+-----------+
1 row in set (0.00 sec)

mysql> SELECT ROUND(2.5), ROUND(-2.5);
+------------+-------------+
| ROUND(2.5) | ROUND(-2.5) |
+------------+-------------+
|          3 |          -3 |
+------------+-------------+
1 row in set (0.00 sec)

mysql> SELECT ROUND(2.5E0), ROUND(-2.5E0);
+--------------+---------------+
| ROUND(2.5E0) | ROUND(-2.5E0) |
+--------------+---------------+
|            2 |            -2 |
+--------------+---------------+
1 row in set (0.00 sec)

mysql> CREATE TABLE t (i TINYINT);
Query OK, 0 rows affected (0.02 sec)

mysql> INSERT INTO t SET i = 128;
Query OK, 1 row affected, 1 warning (0.00 sec)

mysql> SELECT i FROM t;
+------+
| i    |
+------+
|  127 |
+------+
1 row in set (0.00 sec)

mysql> SET sql_mode='STRICT_ALL_TABLES';
Query OK, 0 rows affected, 1 warning (0.00 sec)


mysql> CREATE TABLE t (i TINYINT);
Query OK, 0 rows affected (0.05 sec)


mysql> INSERT INTO t SET i = 128;
ERROR 1264 (22003): Out of range value for column 'i' at row 1
mysql> SELECT i FROM t;
Empty set (0.00 sec)

mysql> SET sql_mode='';
Query OK, 0 rows affected (0.00 sec)

mysql> INSERT INTO t SET i = 1 / 0;
Query OK, 1 row affected (0.00 sec)

mysql> SELECT i FROM t;
+------+
| i    |
+------+
| NULL |
+------+
1 row in set (0.00 sec)

mysql> SET sql_mode='STRICT_ALL_TABLES,ERROR_FOR_DIVISION_BY_ZERO';
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql> INSERT INTO t SET i = 1 / 0;
ERROR 1365 (22012): Division by 0
```

```sql
mysql> CREATE TABLE t (i INT, d DECIMAL, f FLOAT);
Query OK, 0 rows affected (0.02 sec)

mysql> INSERT INTO t VALUES(1,1,1);
Query OK, 1 row affected (0.00 sec)

mysql> CREATE TABLE y SELECT AVG(i), AVG(d), AVG(f) FROM t;
Query OK, 1 row affected (0.02 sec)
Records: 1  Duplicates: 0  Warnings: 0

mysql> DESCRIBE y;
+--------+---------------+------+-----+---------+-------+
| Field  | Type          | Null | Key | Default | Extra |
+--------+---------------+------+-----+---------+-------+
| AVG(i) | decimal(14,4) | YES  |     | NULL    |       |
| AVG(d) | decimal(14,4) | YES  |     | NULL    |       |
| AVG(f) | double        | YES  |     | NULL    |       |
+--------+---------------+------+-----+---------+-------+
3 rows in set (0.03 sec)
```
### 12.11 キャスト関数と演算子
```sql
mysql> SELECT CONVERT('test' USING utf8mb4);
+-------------------------------+
| CONVERT('test' USING utf8mb4) |
+-------------------------------+
| test                          |
+-------------------------------+
1 row in set (0.00 sec)

mysql> SELECT CONVERT(_latin1'Müller' USING utf8mb4);
+----------------------------------------+
| CONVERT(_latin1'M?ller' USING utf8mb4) |
+----------------------------------------+
| M?ller                                 |
+----------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT CONVERT('test', CHAR CHARACTER SET utf8mb4);
+---------------------------------------------+
| CONVERT('test', CHAR CHARACTER SET utf8mb4) |
+---------------------------------------------+
| test                                        |
+---------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT CAST('test' AS CHAR CHARACTER SET utf8mb4);
+--------------------------------------------+
| CAST('test' AS CHAR CHARACTER SET utf8mb4) |
+--------------------------------------------+
| test                                       |
+--------------------------------------------+
1 row in set (0.00 sec)
```
COLLATE 句は、CONVERT() または CAST() コール内では許可されませんが、関数の結果に適用できます。
```sql
mysql> SELECT CONVERT('test' USING utf8mb4) COLLATE utf8mb4_bin;
+---------------------------------------------------+
| CONVERT('test' USING utf8mb4) COLLATE utf8mb4_bin |
+---------------------------------------------------+
| test                                              |
+---------------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT CONVERT('test', CHAR CHARACTER SET utf8mb4) COLLATE utf8mb4_bin;
+-----------------------------------------------------------------+
| CONVERT('test', CHAR CHARACTER SET utf8mb4) COLLATE utf8mb4_bin |
+-----------------------------------------------------------------+
| test                                                            |
+-----------------------------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT CAST('test' AS CHAR CHARACTER SET utf8mb4) COLLATE utf8mb4_bin;
+----------------------------------------------------------------+
| CAST('test' AS CHAR CHARACTER SET utf8mb4) COLLATE utf8mb4_bin |
+----------------------------------------------------------------+
| test                                                           |
+----------------------------------------------------------------+
1 row in set (0.00 sec)
```
キャスト関数は、CREATE TABLE ... SELECT ステートメントで特定の型のカラムを作成する場合に役立ちます
```sql
mysql> CREATE TABLE new_table SELECT CAST('2000-01-01' AS DATE) AS c1;
Query OK, 1 row affected (0.04 sec)
Records: 1  Duplicates: 0  Warnings: 0

mysql> SHOW CREATE TABLE new_table\G
*************************** 1. row ***************************
       Table: new_table
Create Table: CREATE TABLE `new_table` (
  `c1` date DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
1 row in set (0.00 sec)
```
* BINARY expr


```sql
mysql> SELECT 'a' = 'A';
+-----------+
| 'a' = 'A' |
+-----------+
|         1 |
+-----------+
1 row in set (0.00 sec)

mysql> SELECT BINARY 'a' = 'A';
+------------------+
| BINARY 'a' = 'A' |
+------------------+
|                0 |
+------------------+
1 row in set, 1 warning (0.00 sec)

mysql> SELECT 'a' = 'a ';
+------------+
| 'a' = 'a ' |
+------------+
|          0 |
+------------+
1 row in set (0.00 sec)

mysql> SELECT BINARY 'a' = 'a ';
+-------------------+
| BINARY 'a' = 'a ' |
+-------------------+
|                 0 |
+-------------------+
1 row in set, 1 warning (0.00 sec)
```
* CAST(expr AS type [ARRAY])
```sql
mysql> SELECT @@system_time_zone;
+--------------------+
| @@system_time_zone |
+--------------------+
| ???? (?W????)      |
+--------------------+
1 row in set (0.00 sec)

mysql> INSERT INTO tz VALUES
    ->           ROW(CURRENT_TIMESTAMP),
    ->           ROW('2020-07-28 14:50:15');
Query OK, 2 rows affected (0.01 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql> TABLE tz;
+---------------------+
| c                   |
+---------------------+
| 2023-12-17 16:49:29 |
| 2020-07-28 14:50:15 |
+---------------------+
2 rows in set (0.00 sec)

mysql> SELECT CAST(c AT TIME ZONE '+00:00' AS DATETIME) AS u FROM tz;
+---------------------+
| u                   |
+---------------------+
| 2023-12-17 07:49:29 |
| 2020-07-28 05:50:15 |
+---------------------+
2 rows in set (0.00 sec)
```
* CONVERT(expr USING transcoding_name), CONVERT(expr,type)
```sql
mysql> SELECT CONVERT('abc' USING utf8mb4);
+------------------------------+
| CONVERT('abc' USING utf8mb4) |
+------------------------------+
| abc                          |
+------------------------------+
1 row in set (0.00 sec)
```

### 12.12 XML 関数
```sql
mysql>  SET @xml = '<a><b>X</b><b>Y</b></a>';
Query OK, 0 rows affected (0.00 sec)

mysql> SET @i =1, @j = 2;
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT @i, ExtractValue(@xml, '//b[$@i]');
+------+--------------------------------+
| @i   | ExtractValue(@xml, '//b[$@i]') |
+------+--------------------------------+
|    1 | X                              |
+------+--------------------------------+
1 row in set (0.00 sec)
```

```sql
mysql> DELIMITER |
mysql> CREATE PROCEDURE myproc ()
    ->      BEGIN
    ->        DECLARE i INT DEFAULT 1;
    ->        DECLARE xml VARCHAR(25) DEFAULT '<a>X</a><a>Y</a><a>Z</a>';
    ->
    ->        WHILE i < 4 DO
    ->          SELECT xml, i, ExtractValue(xml, '//a[$i]');
    ->          SET i = i+1;
    ->        END WHILE;
    ->      END |
Query OK, 0 rows affected (0.02 sec)

mysql> DELIMITER ;
mysql> CALL myproc();
+--------------------------+------+------------------------------+
| xml                      | i    | ExtractValue(xml, '//a[$i]') |
+--------------------------+------+------------------------------+
| <a>X</a><a>Y</a><a>Z</a> |    1 | X                            |
+--------------------------+------+------------------------------+
1 row in set (0.00 sec)

+--------------------------+------+------------------------------+
| xml                      | i    | ExtractValue(xml, '//a[$i]') |
+--------------------------+------+------------------------------+
| <a>X</a><a>Y</a><a>Z</a> |    2 | Y                            |
+--------------------------+------+------------------------------+
1 row in set (0.01 sec)

+--------------------------+------+------------------------------+
| xml                      | i    | ExtractValue(xml, '//a[$i]') |
+--------------------------+------+------------------------------+
| <a>X</a><a>Y</a><a>Z</a> |    3 | Z                            |
+--------------------------+------+------------------------------+
1 row in set (0.01 sec)

Query OK, 0 rows affected (0.01 sec)
```
* ExtractValue(xml_frag, xpath_expr)
```sql
mysql> SELECT ExtractValue('<a><b/></a>', '/a/b');
+-------------------------------------+
| ExtractValue('<a><b/></a>', '/a/b') |
+-------------------------------------+
|                                     |
+-------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT ExtractValue('<a><b/></a>', 'count(/a/b)');
+--------------------------------------------+
| ExtractValue('<a><b/></a>', 'count(/a/b)') |
+--------------------------------------------+
| 1                                          |
+--------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT ExtractValue('<a><c/></a>', 'count(/a/b)');
+--------------------------------------------+
| ExtractValue('<a><c/></a>', 'count(/a/b)') |
+--------------------------------------------+
| 0                                          |
+--------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT
    ->        ExtractValue('<a>ccc<b>ddd</b></a>', '/a') AS val1,
    ->        ExtractValue('<a>ccc<b>ddd</b></a>', '/a/b') AS val2,
    ->        ExtractValue('<a>ccc<b>ddd</b></a>', '//b') AS val3,
    ->        ExtractValue('<a>ccc<b>ddd</b></a>', '/b') AS val4,
    ->        ExtractValue('<a>ccc<b>ddd</b><b>eee</b></a>', '//b') AS val5;
+------+------+------+------+---------+
| val1 | val2 | val3 | val4 | val5    |
+------+------+------+------+---------+
| ccc  | ddd  | ddd  |      | ddd eee |
+------+------+------+------+---------+
1 row in set (0.00 sec)
```
* UpdateXML(xml_target, xpath_expr, new_xml)
```sql
mysql> SELECT
    ->        UpdateXML('<a><b>ccc</b><d></d></a>', '/a', '<e>fff</e>') AS val1,
    ->        UpdateXML('<a><b>ccc</b><d></d></a>', '/b', '<e>fff</e>') AS val2,
    ->        UpdateXML('<a><b>ccc</b><d></d></a>', '//b', '<e>fff</e>') AS val3,
    ->        UpdateXML('<a><b>ccc</b><d></d></a>', '/a/d', '<e>fff</e>') AS val4,
    ->        UpdateXML('<a><d></d><b>ccc</b><d></d></a>', '/a/d', '<e>fff</e>') AS val5
    ->      \G
*************************** 1. row ***************************
val1: <e>fff</e>
val2: <a><b>ccc</b><d></d></a>
val3: <a><e>fff</e><d></d></a>
val4: <a><b>ccc</b><e>fff</e></a>
val5: <a><d></d><b>ccc</b><d></d></a>
1 row in set (0.00 sec)
```

### 12.13 ビット関数と演算子
```sql
mysql> SELECT HEX(UUID_TO_BIN('6ccd780c-baba-1026-9564-5b8c656024db'));
+----------------------------------------------------------+
| HEX(UUID_TO_BIN('6ccd780c-baba-1026-9564-5b8c656024db')) |
+----------------------------------------------------------+
| 6CCD780CBABA102695645B8C656024DB                         |
+----------------------------------------------------------+
1 row in set (0.01 sec)

mysql> SELECT HEX(INET6_ATON('fe80::219:d1ff:fe91:1a72'));
+---------------------------------------------+
| HEX(INET6_ATON('fe80::219:d1ff:fe91:1a72')) |
+---------------------------------------------+
| FE800000000000000219D1FFFE911A72            |
+---------------------------------------------+
1 row in set (0.00 sec)
```
バイナリ文字列のビット操作の例
```sql
mysql> SET @uuid = UUID_TO_BIN('6ccd780c-baba-1026-9564-5b8c656024db');
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT HEX(@uuid);
+----------------------------------+
| HEX(@uuid)                       |
+----------------------------------+
| 6CCD780CBABA102695645B8C656024DB |
+----------------------------------+
1 row in set (0.00 sec)
```
ビット単位の AND、OR および XOR 操作
```sql
mysql> SELECT 64 | 1, X'40' | X'01';
+--------+---------------+
| 64 | 1 | X'40' | X'01' |
+--------+---------------+
|     65 |            65 |
+--------+---------------+
1 row in set (0.00 sec)

mysql> SELECT _binary X'40' | X'01';
+----------------------------------------------+
| _binary X'40' | X'01'                        |
+----------------------------------------------+
| 0x41                                         |
+----------------------------------------------+
1 row in set (0.00 sec)

mysql> SET @var1 = X'40', @var2 = X'01';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT @var1 | @var2;
+------------------------------+
| @var1 | @var2                |
+------------------------------+
| 0x41                         |
+------------------------------+
1 row in set (0.00 sec)
```
ビット単位の補完およびシフト操作
```sql
mysql> SELECT ~0, 64 << 2, X'40' << 2;
+----------------------+---------+------------+
| ~0                   | 64 << 2 | X'40' << 2 |
+----------------------+---------+------------+
| 18446744073709551615 |     256 |        256 |
+----------------------+---------+------------+
1 row in set (0.00 sec)

mysql> SELECT HEX(_binary X'1111000022220000' >> 16);
+----------------------------------------+
| HEX(_binary X'1111000022220000' >> 16) |
+----------------------------------------+
| 0000111100002222                       |
+----------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT HEX(_binary X'1111000022220000' << 16);
+----------------------------------------+
| HEX(_binary X'1111000022220000' << 16) |
+----------------------------------------+
| 0000222200000000                       |
+----------------------------------------+
1 row in set (0.00 sec)

mysql> SET @var1 = X'F0F0F0F0';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT HEX(~@var1);
+-------------+
| HEX(~@var1) |
+-------------+
| 0F0F0F0F    |
+-------------+
1 row in set (0.00 sec)
```
BIT_COUNT() の操作
```sql
mysql> SELECT BIT_COUNT(127);
+----------------+
| BIT_COUNT(127) |
+----------------+
|              7 |
+----------------+
1 row in set (0.00 sec)

mysql> SELECT BIT_COUNT(b'010101'), BIT_COUNT(_binary b'010101');
+----------------------+------------------------------+
| BIT_COUNT(b'010101') | BIT_COUNT(_binary b'010101') |
+----------------------+------------------------------+
|                    3 |                            3 |
+----------------------+------------------------------+
1 row in set (0.00 sec)
```
BIT_AND()、BIT_OR() および BIT_XOR() の操作
```sql
mysql> CREATE TABLE t (group_id INT, a VARBINARY(6));
Query OK, 0 rows affected (0.03 sec)

mysql> INSERT INTO t VALUES (1, NULL);
Query OK, 1 row affected (0.01 sec)

mysql> INSERT INTO t VALUES (1, NULL);
Query OK, 1 row affected (0.00 sec)

mysql> INSERT INTO t VALUES (2, NULL);
Query OK, 1 row affected (0.00 sec)

mysql> INSERT INTO t VALUES (2, X'1234');
Query OK, 1 row affected (0.00 sec)

mysql> INSERT INTO t VALUES (2, X'FF34');
Query OK, 1 row affected (0.00 sec)

mysql> SELECT HEX(BIT_AND(a)), HEX(BIT_OR(a)), HEX(BIT_XOR(a))
    ->        FROM t GROUP BY group_id;
+-----------------+----------------+-----------------+
| HEX(BIT_AND(a)) | HEX(BIT_OR(a)) | HEX(BIT_XOR(a)) |
+-----------------+----------------+-----------------+
| FFFFFFFFFFFF    | 000000000000   | 000000000000    |
| 1234            | FF34           | ED00            |
+-----------------+----------------+-----------------+
2 rows in set (0.01 sec)
```
* |
```sql
mysql> SELECT 29 | 15;
+---------+
| 29 | 15 |
+---------+
|      31 |
+---------+
1 row in set (0.00 sec)

mysql> SELECT _binary X'40404040' | X'01020304';
+----------------------------------------------------------------------+
| _binary X'40404040' | X'01020304'                                    |
+----------------------------------------------------------------------+
| 0x41424344                                                           |
+----------------------------------------------------------------------+
1 row in set (0.00 sec)
```
* &
```sql
mysql> SELECT 29 & 15;
+---------+
| 29 & 15 |
+---------+
|      13 |
+---------+
1 row in set (0.00 sec)

mysql> SELECT HEX(_binary X'FF' & b'11110000');
+----------------------------------+
| HEX(_binary X'FF' & b'11110000') |
+----------------------------------+
| F0                               |
+----------------------------------+
1 row in set (0.00 sec)
```
* ^
```sql
mysql> SELECT 1 ^ 1;
+-------+
| 1 ^ 1 |
+-------+
|     0 |
+-------+
1 row in set (0.00 sec)

mysql> SELECT 1 ^ 0;
+-------+
| 1 ^ 0 |
+-------+
|     1 |
+-------+
1 row in set (0.00 sec)

mysql> SELECT 11 ^ 3;
+--------+
| 11 ^ 3 |
+--------+
|      8 |
+--------+
1 row in set (0.00 sec)

mysql> SELECT HEX(_binary X'FEDC' ^ X'1111');
+--------------------------------+
| HEX(_binary X'FEDC' ^ X'1111') |
+--------------------------------+
| EFCD                           |
+--------------------------------+
1 row in set (0.00 sec)
```
* <<
```sql
mysql> SELECT 1 << 2;
+--------+
| 1 << 2 |
+--------+
|      4 |
+--------+
1 row in set (0.00 sec)

mysql> SELECT HEX(_binary X'00FF00FF00FF' << 8);
+-----------------------------------+
| HEX(_binary X'00FF00FF00FF' << 8) |
+-----------------------------------+
| FF00FF00FF00                      |
+-----------------------------------+
1 row in set (0.00 sec)
```
* 　>>
```sql
mysql> SELECT 4 >> 2;
+--------+
| 4 >> 2 |
+--------+
|      1 |
+--------+
1 row in set (0.00 sec)

mysql> SELECT HEX(_binary X'00FF00FF00FF' >> 8);
+-----------------------------------+
| HEX(_binary X'00FF00FF00FF' >> 8) |
+-----------------------------------+
| 0000FF00FF00                      |
+-----------------------------------+
1 row in set (0.00 sec)
```
* ~
```sql
mysql> SELECT 5 & ~1;
+--------+
| 5 & ~1 |
+--------+
|      4 |
+--------+
1 row in set (0.00 sec)

mysql> SELECT HEX(~X'0000FFFF1111EEEE');
+---------------------------+
| HEX(~X'0000FFFF1111EEEE') |
+---------------------------+
| FFFF0000EEEE1111          |
+---------------------------+
1 row in set (0.00 sec)
```
* BIT_COUNT(N)
```sql
mysql> SELECT BIT_COUNT(64), BIT_COUNT(BINARY 64);
+---------------+----------------------+
| BIT_COUNT(64) | BIT_COUNT(BINARY 64) |
+---------------+----------------------+
|             1 |                    7 |
+---------------+----------------------+
1 row in set, 1 warning (0.00 sec)

mysql> SELECT BIT_COUNT('64'), BIT_COUNT(_binary '64');
+-----------------+-------------------------+
| BIT_COUNT('64') | BIT_COUNT(_binary '64') |
+-----------------+-------------------------+
|               1 |                       7 |
+-----------------+-------------------------+
1 row in set (0.00 sec)

mysql> SELECT BIT_COUNT(X'40'), BIT_COUNT(_binary X'40');
+------------------+--------------------------+
| BIT_COUNT(X'40') | BIT_COUNT(_binary X'40') |
+------------------+--------------------------+
|                1 |                        1 |
+------------------+--------------------------+
1 row in set (0.00 sec)
```
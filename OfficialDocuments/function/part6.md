### 12.14 暗号化関数と圧縮関数
```sql
mysql> CREATE TABLE md5_tbl (md5_val CHAR(32));
Query OK, 0 rows affected (0.03 sec)

mysql> INSERT INTO md5_tbl (md5_val) VALUES(MD5('abcdef'));
Query OK, 1 row affected (0.01 sec)
```
* AES_ENCRYPT(str,key_str[,init_vector])
```sql
mysql> SET block_encryption_mode = 'aes-256-cbc';
Query OK, 0 rows affected (0.00 sec)

mysql> SET @key_str = SHA2('My secret passphrase',512);
Query OK, 0 rows affected (0.00 sec)

mysql> SET @init_vector = RANDOM_BYTES(16);
Query OK, 0 rows affected (0.00 sec)

mysql> SET @crypt_str = AES_ENCRYPT('text',@key_str,@init_vector);
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql> SELECT AES_DECRYPT(@crypt_str,@key_str,@init_vector);
+----------------------------------------------------------------------------------------------+
| AES_DECRYPT(@crypt_str,@key_str,@init_vector)                                                |
+----------------------------------------------------------------------------------------------+
| 0x74657874                                                                                   |
+----------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)
```
* COMPRESS(string_to_compress)
```sql
mysql> SELECT LENGTH(COMPRESS(REPEAT('a',1000)));
+------------------------------------+
| LENGTH(COMPRESS(REPEAT('a',1000))) |
+------------------------------------+
|                                 21 |
+------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT LENGTH(COMPRESS(''));
+----------------------+
| LENGTH(COMPRESS('')) |
+----------------------+
|                    0 |
+----------------------+
1 row in set (0.00 sec)

mysql> SELECT LENGTH(COMPRESS('a'));
+-----------------------+
| LENGTH(COMPRESS('a')) |
+-----------------------+
|                    13 |
+-----------------------+
1 row in set (0.00 sec)

mysql> SELECT LENGTH(COMPRESS(REPEAT('a',16)));
+----------------------------------+
| LENGTH(COMPRESS(REPEAT('a',16))) |
+----------------------------------+
|                               15 |
+----------------------------------+
1 row in set (0.00 sec)
```
* MD5(str)
```sql
mysql> SELECT MD5('testing');
+----------------------------------+
| MD5('testing')                   |
+----------------------------------+
| ae2b1fca515949e5d54fb22b8ed95575 |
+----------------------------------+
1 row in set (0.00 sec)
```
* SHA1(str), SHA(str)
```sql
mysql> SELECT SHA1('abc');
+------------------------------------------+
| SHA1('abc')                              |
+------------------------------------------+
| a9993e364706816aba3e25717850c26c9cd0d89d |
+------------------------------------------+
1 row in set (0.00 sec)
```
* SHA2(str, hash_length)
```sql
mysql> SELECT SHA2('abc', 224);
+----------------------------------------------------------+
| SHA2('abc', 224)                                         |
+----------------------------------------------------------+
| 23097d223405d8228642a477bda255b32aadbce4bda0b3f7e36c9da7 |
+----------------------------------------------------------+
1 row in set (0.00 sec)
```
* STATEMENT_DIGEST(statement)
```sql
mysql> SET @stmt = 'SELECT * FROM mytable WHERE cola = 10 AND colb = 20';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT STATEMENT_DIGEST(@stmt);
+------------------------------------------------------------------+
| STATEMENT_DIGEST(@stmt)                                          |
+------------------------------------------------------------------+
| 3bb95eeade896657c4526e74ff2a2862039d0a0fe8a9e7155b5fe492cbd78387 |
+------------------------------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT STATEMENT_DIGEST_TEXT(@stmt);
+---------------------------------------------------------+
| STATEMENT_DIGEST_TEXT(@stmt)                            |
+---------------------------------------------------------+
| SELECT * FROM `mytable` WHERE `cola` = ? AND `colb` = ? |
+---------------------------------------------------------+
1 row in set (0.00 sec)
```
* UNCOMPRESS(string_to_uncompress)
```sql
mysql> SELECT UNCOMPRESS(COMPRESS('any string'));
+------------------------------------------------------------------------+
| UNCOMPRESS(COMPRESS('any string'))                                     |
+------------------------------------------------------------------------+
| 0x616E7920737472696E67                                                 |
+------------------------------------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT UNCOMPRESS('any string');
+----------------------------------------------------+
| UNCOMPRESS('any string')                           |
+----------------------------------------------------+
| NULL                                               |
+----------------------------------------------------+
1 row in set, 1 warning (0.00 sec)
```
* UNCOMPRESSED_LENGTH(compressed_string)
```sql
mysql> SELECT UNCOMPRESSED_LENGTH(COMPRESS(REPEAT('a',30)));
+-----------------------------------------------+
| UNCOMPRESSED_LENGTH(COMPRESS(REPEAT('a',30))) |
+-----------------------------------------------+
|                                            30 |
+-----------------------------------------------+
1 row in set (0.00 sec)
```
### 12.16 情報関数
* BENCHMARK(count,expr)
```sql
mysql> SET @@session.block_encryption_mode = 'aes-256-ecb';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT BENCHMARK(1000000,AES_ENCRYPT('hello','goodbye'));
+---------------------------------------------------+
| BENCHMARK(1000000,AES_ENCRYPT('hello','goodbye')) |
+---------------------------------------------------+
|                                                 0 |
+---------------------------------------------------+
1 row in set (2.48 sec)
```
* CHARSET(str)
```sql
mysql> SELECT CHARSET('abc');
+----------------+
| CHARSET('abc') |
+----------------+
| cp932          |
+----------------+
1 row in set (0.00 sec)

mysql> SELECT CHARSET(CONVERT('abc' USING latin1));
+--------------------------------------+
| CHARSET(CONVERT('abc' USING latin1)) |
+--------------------------------------+
| latin1                               |
+--------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT CHARSET(USER());
+-----------------+
| CHARSET(USER()) |
+-----------------+
| utf8mb3         |
+-----------------+
1 row in set (0.00 sec)
```
* COERCIBILITY(str)
```sql
mysql> SELECT COERCIBILITY(USER());
+----------------------+
| COERCIBILITY(USER()) |
+----------------------+
|                    3 |
+----------------------+
1 row in set (0.00 sec)

mysql> SELECT COERCIBILITY('abc');
+---------------------+
| COERCIBILITY('abc') |
+---------------------+
|                   4 |
+---------------------+
1 row in set (0.00 sec)

mysql> SELECT COERCIBILITY(1000);
+--------------------+
| COERCIBILITY(1000) |
+--------------------+
|                  5 |
+--------------------+
1 row in set (0.00 sec)
```
* COLLATION(str)
```sql
mysql> SELECT COLLATION('abc');
+-------------------+
| COLLATION('abc')  |
+-------------------+
| cp932_japanese_ci |
+-------------------+
1 row in set (0.00 sec)

mysql> SELECT COLLATION(_utf8mb4'abc');
+--------------------------+
| COLLATION(_utf8mb4'abc') |
+--------------------------+
| utf8mb4_0900_ai_ci       |
+--------------------------+
1 row in set (0.00 sec)

mysql> SELECT COLLATION(_latin1'abc');
+-------------------------+
| COLLATION(_latin1'abc') |
+-------------------------+
| latin1_swedish_ci       |
+-------------------------+
1 row in set (0.00 sec)
```
* CONNECTION_ID()
```sql
mysql> SELECT CONNECTION_ID();
+-----------------+
| CONNECTION_ID() |
+-----------------+
|              12 |
+-----------------+
1 row in set (0.00 sec)
```
* CURRENT_ROLE()
```sql
mysql> SELECT CURRENT_ROLE();
+----------------+
| CURRENT_ROLE() |
+----------------+
| NONE           |
+----------------+
1 row in set (0.00 sec)
```
* CURRENT_USER, CURRENT_USER()
```sql
mysql> SELECT CURRENT_USER();
+----------------+
| CURRENT_USER() |
+----------------+
| root@localhost |
+----------------+
1 row in set (0.00 sec)
```
* DATABASE()
```sql
mysql> SELECT DATABASE();
+------------+
| DATABASE() |
+------------+
| sakila     |
+------------+
1 row in set (0.00 sec)
```
* FOUND_ROWS()
```sql
mysql>  SELECT FOUND_ROWS();
+--------------+
| FOUND_ROWS() |
+--------------+
|            1 |
+--------------+
1 row in set, 1 warning (0.00 sec)
```
* LAST_INSERT_ID(), LAST_INSERT_ID(expr)
```sql
mysql> CREATE TABLE t (
    ->        id INT AUTO_INCREMENT NOT NULL PRIMARY KEY,
    ->        name VARCHAR(10) NOT NULL
    ->        );
Query OK, 0 rows affected (0.02 sec)

mysql> INSERT INTO t VALUES (NULL, 'Bob');
Query OK, 1 row affected (0.01 sec)

mysql> SELECT * FROM t;
+----+------+
| id | name |
+----+------+
|  1 | Bob  |
+----+------+
1 row in set (0.00 sec)

mysql> SELECT LAST_INSERT_ID();
+------------------+
| LAST_INSERT_ID() |
+------------------+
|                1 |
+------------------+
1 row in set (0.00 sec)

mysql> INSERT INTO t VALUES
    ->        (NULL, 'Mary'), (NULL, 'Jane'), (NULL, 'Lisa');
Query OK, 3 rows affected (0.00 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql> SELECT * FROM t;
+----+------+
| id | name |
+----+------+
|  1 | Bob  |
|  2 | Mary |
|  3 | Jane |
|  4 | Lisa |
+----+------+
4 rows in set (0.00 sec)

mysql> SELECT LAST_INSERT_ID();
+------------------+
| LAST_INSERT_ID() |
+------------------+
|                2 |
+------------------+
1 row in set (0.00 sec)
```
* ROLES_GRAPHML()
```sql
mysql> SELECT ROLES_GRAPHML();
+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ROLES_GRAPHML()






                            |
+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| <?xml version="1.0" encoding="UTF-8"?>
<graphml xmlns="http://graphml.graphdrawing.org/xmlns" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://graphml.graphdrawing.org/xmlns http://graphml.graphdrawing.org/xmlns/1.0/graphml.xsd">
  <key id="key0" for="edge" attr.name="color" attr.type="int" />
  <key id="key1" for="node" attr.name="name" attr.type="string" />
  <graph id="G" edgedefault="directed" parse.nodeids="canonical" parse.edgeids="canonical" parse.order="nodesfirst">
    <node id="n0">
      <data key="key1">`mysql.infoschema`@`localhost`</data>
    </node>
    <node id="n1">
      <data key="key1">`mysql.session`@`localhost`</data>
    </node>
    <node id="n2">
      <data key="key1">`mysql.sys`@`localhost`</data>
    </node>
    <node id="n3">
      <data key="key1">`root`@`localhost`</data>
    </node>
  </graph>
</graphml>
 |
+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)
```
* ROW_COUNT()
```sql
mysql> create table t(a int);
Query OK, 0 rows affected (0.02 sec)

mysql>  INSERT INTO t VALUES(1),(2),(3);
Query OK, 3 rows affected (0.01 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql> SELECT ROW_COUNT();
+-------------+
| ROW_COUNT() |
+-------------+
|           3 |
+-------------+
1 row in set (0.00 sec)

mysql> DELETE FROM t WHERE a IN(1,2);
Query OK, 2 rows affected (0.00 sec)

mysql> SELECT ROW_COUNT();
+-------------+
| ROW_COUNT() |
+-------------+
|           2 |
+-------------+
1 row in set (0.00 sec)
```
* USER()
```sql
mysql> SELECT USER();
+----------------+
| USER()         |
+----------------+
| root@localhost |
+----------------+
1 row in set (0.00 sec)
```
* VERSION()
```sql
mysql> SELECT VERSION();
+-----------+
| VERSION() |
+-----------+
| 8.2.0     |
+-----------+
1 row in set (0.00 sec)
```
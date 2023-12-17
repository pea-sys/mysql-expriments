### 9.1.1 文字列リテラル

文字列リテラルには、特定の文字セットおよび照合順序を使用する文字列として指定するために、オプションの文字セットイントロデューサおよび COLLATE 句を含めることができます
```sql
mysql> SELECT _latin1'string';
+--------+
| string |
+--------+
| string |
+--------+
1 row in set (0.00 sec)

mysql> SELECT _binary'string';
+----------------+
| string         |
+----------------+
| 0x737472696E67 |
+----------------+
1 row in set (0.00 sec)

mysql> SELECT _utf8'string' COLLATE utf8_danish_ci;
+--------------------------------------+
| _utf8'string' COLLATE utf8_danish_ci |
+--------------------------------------+
| string                               |
+--------------------------------------+
1 row in set, 1 warning (0.01 sec)
```
N'literal' (または n'literal') を使用すると、各国文字セットの文字列を作成できます。
```sql
mysql> SELECT N'some text';
+-----------+
| some text |
+-----------+
| some text |
+-----------+
1 row in set, 1 warning (0.00 sec)

mysql> SELECT n'some text';
+-----------+
| some text |
+-----------+
| some text |
+-----------+
1 row in set, 1 warning (0.00 sec)

mysql> SELECT _utf8'some text';
+-----------+
| some text |
+-----------+
| some text |
+-----------+
1 row in set, 1 warning (0.00 sec)
```

次の SELECT ステートメントは、引用符を使用した場合とエスケープを使用した場合にどのような効果があるかを示しています。
```sql
mysql> SELECT 'hello', '"hello"', '""hello""', 'hel''lo', '\'hello';
+-------+---------+-----------+--------+--------+
| hello | "hello" | ""hello"" | hel'lo | 'hello |
+-------+---------+-----------+--------+--------+
| hello | "hello" | ""hello"" | hel'lo | 'hello |
+-------+---------+-----------+--------+--------+
1 row in set (0.00 sec)

mysql> SELECT "hello", "'hello'", "''hello''", "hel""lo", "\"hello";
+-------+---------+-----------+--------+--------+
| hello | 'hello' | ''hello'' | hel"lo | "hello |
+-------+---------+-----------+--------+--------+
| hello | 'hello' | ''hello'' | hel"lo | "hello |
+-------+---------+-----------+--------+--------+
1 row in set (0.00 sec)

mysql> SELECT 'This\nIs\nFour\nLines';
+--------------------+
| This
Is
Four
Lines |
+--------------------+
| This
Is
Four
Lines |
+--------------------+
1 row in set (0.00 sec)

mysql> SELECT 'disappearing\ backslash';
+------------------------+
| disappearing backslash |
+------------------------+
| disappearing backslash |
+------------------------+
1 row in set (0.00 sec)
```

### 9.1.2 数値リテラル
数値リテラルには、正確値 (整数と DECIMAL) リテラルと近似値 (浮動小数点) リテラルが含まれます。
```sql
mysql> select 1,.2,3.4,-5,-6.78,+9.10;
+---+-----+-----+----+-------+------+
| 1 | .2  | 3.4 | -5 | -6.78 | 9.10 |
+---+-----+-----+----+-------+------+
| 1 | 0.2 | 3.4 | -5 | -6.78 | 9.10 |
+---+-----+-----+----+-------+------+
1 row in set (0.00 sec)

mysql> select 1.2E-3,-1.2E3,-1.2E-3;
+--------+--------+---------+
| 1.2E-3 | -1.2E3 | -1.2E-3 |
+--------+--------+---------+
| 0.0012 |  -1200 | -0.0012 |
+--------+--------+---------+
1 row in set (0.00 sec)
```

### 9.1.4 16 進数リテラル
16 進数リテラル値は、X'val'または 0xval 表記を使用して書き込まれます。
```sql
mysql> select X'01AF',X'01af',x'01AF',x'01af',0x01AF,0x01af;
+------------------+------------------+------------------+------------------+----------------+----------------+
| X'01AF'          | X'01af'          | x'01AF'          | x'01af'          | 0x01AF         | 0x01af         |
+------------------+------------------+------------------+------------------+----------------+----------------+
| 0x01AF           | 0x01AF           | 0x01AF           | 0x01AF           | 0x01AF         | 0x01AF         |
+------------------+------------------+------------------+------------------+----------------+----------------+
1 row in set (0.00 sec)
```
不正なケース
```sql
mysql> select X'0G';
ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'X'0G'' at line 1
mysql> select 0X01AF;
ERROR 1054 (42S22): Unknown column '0X01AF' in 'field list'
```
X'val'表記法を使用して記述された値には偶数の桁数が含まれている必要がある
```sql
mysql> SET @s = X'FFF';
ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'X'FFF'' at line 1
mysql> SET @s = X'0FFF';
Query OK, 0 rows affected (0.00 sec)
```
デフォルトでは、16 進数リテラルはバイナリ文字列で、16 進数の各ペアは文字を表します
```sql
mysql> SELECT X'4D7953514C', CHARSET(X'4D7953514C');
+------------------------------+------------------------+
| X'4D7953514C'                | CHARSET(X'4D7953514C') |
+------------------------------+------------------------+
| 0x4D7953514C                 | binary                 |
+------------------------------+------------------------+
1 row in set (0.00 sec)

mysql> SELECT 0x5461626c65, CHARSET(0x5461626c65);
+----------------------------+-----------------------+
| 0x5461626c65               | CHARSET(0x5461626c65) |
+----------------------------+-----------------------+
| 0x5461626C65               | binary                |
+----------------------------+-----------------------+
1 row in set (0.00 sec)
```
16 進リテラルには、特定の文字セットおよび照合順序を使用する文字列として指定するために、オプションの文字セットイントロデューサおよび COLLATE 句を含めることができます
```sql
mysql> SELECT _latin1 X'4D7953514C';
+-----------------------+
| _latin1 X'4D7953514C' |
+-----------------------+
| MySQL                 |
+-----------------------+
1 row in set (0.00 sec)

mysql> SELECT _utf8 0x4D7953514C COLLATE utf8_danish_ci;
+-------------------------------------------+
| _utf8 0x4D7953514C COLLATE utf8_danish_ci |
+-------------------------------------------+
| MySQL                                     |
+-------------------------------------------+
1 row in set, 1 warning (0.00 sec)
```
 16 進リテラルの数値処理を保証するには、数値コンテキストで使用します。
 ```sql
 mysql> SET @v1 = X'41';
Query OK, 0 rows affected (0.00 sec)

mysql> SET @v2 = X'41'+0;
Query OK, 0 rows affected (0.00 sec)

mysql> SET @v3 = CAST(X'41' AS UNSIGNED);
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT @v1, @v2, @v3;
+------------+------+------+
| @v1        | @v2  | @v3  |
+------------+------+------+
| 0x41       |   65 |   65 |
+------------+------+------+
1 row in set (0.00 sec)
 ```
 空の 16 進数値 (X'') は、長さゼロのバイナリ文字列に評価されます。
 ```sql
 mysql> SELECT CHARSET(X''), LENGTH(X'');
+--------------+-------------+
| CHARSET(X'') | LENGTH(X'') |
+--------------+-------------+
| binary       |           0 |
+--------------+-------------+
1 row in set (0.00 sec)

mysql> SELECT X''+0;
+-------+
| X''+0 |
+-------+
|     0 |
+-------+
1 row in set (0.00 sec)
```
文字列または数値を 16 進数形式の文字列に変換するには、HEX() 関数を使用します
```sql
mysql> SELECT HEX('cat');
+------------+
| HEX('cat') |
+------------+
| 636174     |
+------------+
1 row in set (0.00 sec)

mysql> SELECT X'636174';
+----------------------+
| X'636174'            |
+----------------------+
| 0x636174             |
+----------------------+
1 row in set (0.00 sec)
```

### 9.1.5 ビット値リテラル
デフォルトでは、ビット値リテラルはバイナリ文字列です
```sql
mysql> SELECT b'1000001', CHARSET(b'1000001');
+------------------------+---------------------+
| b'1000001'             | CHARSET(b'1000001') |
+------------------------+---------------------+
| 0x41                   | binary              |
+------------------------+---------------------+
1 row in set (0.00 sec)
mysql> SELECT 0b1100001, CHARSET(0b1100001);
+----------------------+--------------------+
| 0b1100001            | CHARSET(0b1100001) |
+----------------------+--------------------+
| 0x61                 | binary             |
+----------------------+--------------------+
1 row in set (0.00 sec)
```
ビット値リテラルには、オプションの文字セットイントロデューサおよび COLLATE 句を指定して、特定の文字セットおよび照合順序を使用する文字列として指定できます
```sql
mysql> SELECT _latin1 b'1000001';
+--------------------+
| _latin1 b'1000001' |
+--------------------+
| A                  |
+--------------------+
1 row in set (0.00 sec)

mysql> SELECT _utf8 0b1000001 COLLATE utf8_danish_ci;
+----------------------------------------+
| _utf8 0b1000001 COLLATE utf8_danish_ci |
+----------------------------------------+
| A                                      |
+----------------------------------------+
1 row in set, 1 warning (0.00 sec)
```
数値コンテキストでは、MySQL はビットリテラルを整数として扱います。
```sql
mysql> SET @v1 = b'1100001';
Query OK, 0 rows affected (0.00 sec)

mysql> SET @v2 = b'1100001'+0;
Query OK, 0 rows affected (0.00 sec)

mysql> SET @v3 = CAST(b'1100001' AS UNSIGNED);
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT @v1, @v2, @v3;
+------------+------+------+
| @v1        | @v2  | @v3  |
+------------+------+------+
| 0x61       |   97 |   97 |
+------------+------+------+
1 row in set (0.00 sec)
```
空のビット値 (b'') は、長さゼロのバイナリ文字列に評価されます。
```sql
mysql> CREATE TABLE t (b BIT(8));
Query OK, 0 rows affected (0.05 sec)

mysql> INSERT INTO t SET b = b'11111111';
Query OK, 1 row affected (0.02 sec)

mysql> INSERT INTO t SET b = b'1010';
Query OK, 1 row affected (0.00 sec)

mysql> INSERT INTO t SET b = b'0101';
Query OK, 1 row affected (0.00 sec)
```
結果セットのビット値はバイナリ値として返されますが、適切に表示されない場合があります。
```sql
mysql> SELECT b+0, BIN(b), OCT(b), HEX(b) FROM t;
+------+----------+--------+--------+
| b+0  | BIN(b)   | OCT(b) | HEX(b) |
+------+----------+--------+--------+
|  255 | 11111111 | 377    | FF     |
|   10 | 1010     | 12     | A      |
|    5 | 101      | 5      | 5      |
+------+----------+--------+--------+
3 rows in set (0.02 sec)
```
ビットリテラルの場合、ビット操作は数値コンテキストとみなされますが、ビット操作では MySQL 8.0 以上で数値またはバイナリ文字列引数が許可されます。
```sql
mysql> SET @v1 = b'000010101' | b'000101010';
Query OK, 0 rows affected (0.00 sec)

mysql> SET @v2 = _binary b'000010101' | _binary b'000101010';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT HEX(@v1), HEX(@v2);
+----------+----------+
| HEX(@v1) | HEX(@v2) |
+----------+----------+
| 3F       | 003F     |
+----------+----------+
1 row in set (0.00 sec)
```

### 9.1.6 boolean リテラル
```sql
mysql> SELECT TRUE, true, FALSE, false;
+------+------+-------+-------+
| TRUE | true | FALSE | false |
+------+------+-------+-------+
|    1 |    1 |     0 |     0 |
+------+------+-------+-------+
1 row in set (0.01 sec)
```
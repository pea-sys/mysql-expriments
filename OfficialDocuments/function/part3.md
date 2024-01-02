### 12.8 文字列関数および演算子
* ASCII(str)
```sql
mysql> SELECT ASCII('2');
+------------+
| ASCII('2') |
+------------+
|         50 |
+------------+
1 row in set (0.00 sec)
```
* BIN(N)
```sql
mysql> SELECT BIN(12);
+---------+
| BIN(12) |
+---------+
| 1100    |
+---------+
1 row in set (0.00 sec)
```
* BIT_LENGTH(str)
```Sql
mysql> SELECT BIT_LENGTH('text');
+--------------------+
| BIT_LENGTH('text') |
+--------------------+
|                 32 |
+--------------------+
1 row in set (0.00 sec)
```
* CHAR(N,... [USING charset_name])
```sql
mysql> SELECT CHAR(77,121,83,81,'76');
+--------------------------------------------------+
| CHAR(77,121,83,81,'76')                          |
+--------------------------------------------------+
| 0x4D7953514C                                     |
+--------------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT CHAR(77,77.3,'77.3');
+--------------------------------------------+
| CHAR(77,77.3,'77.3')                       |
+--------------------------------------------+
| 0x4D4D4D                                   |
+--------------------------------------------+
1 row in set, 1 warning (0.00 sec)
```
* CONCAT(str1,str2,...)
```sql
mysql> SELECT CONCAT('My', 'S', 'QL');
+-------------------------+
| CONCAT('My', 'S', 'QL') |
+-------------------------+
| MySQL                   |
+-------------------------+
1 row in set (0.00 sec)

mysql> SELECT CONCAT('My', NULL, 'QL');
+--------------------------+
| CONCAT('My', NULL, 'QL') |
+--------------------------+
| NULL                     |
+--------------------------+
1 row in set (0.00 sec)
```
* CONCAT_WS(separator,str1,str2,...)
```sql
mysql> SELECT CONCAT_WS(',','First name','Second name','Last Name');
+-------------------------------------------------------+
| CONCAT_WS(',','First name','Second name','Last Name') |
+-------------------------------------------------------+
| First name,Second name,Last Name                      |
+-------------------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT CONCAT_WS(',','First name',NULL,'Last Name');
+----------------------------------------------+
| CONCAT_WS(',','First name',NULL,'Last Name') |
+----------------------------------------------+
| First name,Last Name                         |
+----------------------------------------------+
1 row in set (0.00 sec)
```
* ELT(N,str1,str2,str3,...)
```sql
mysql> SELECT ELT(1, 'Aa', 'Bb', 'Cc', 'Dd');
+--------------------------------+
| ELT(1, 'Aa', 'Bb', 'Cc', 'Dd') |
+--------------------------------+
| Aa                             |
+--------------------------------+
1 row in set (0.00 sec)

mysql> SELECT ELT(4, 'Aa', 'Bb', 'Cc', 'Dd');
+--------------------------------+
| ELT(4, 'Aa', 'Bb', 'Cc', 'Dd') |
+--------------------------------+
| Dd                             |
+--------------------------------+
1 row in set (0.00 sec)
```
* EXPORT_SET(bits,on,off[,separator[,number_of_bits]])
```sql
mysql> SELECT EXPORT_SET(5,'Y','N',',',4);
+-----------------------------+
| EXPORT_SET(5,'Y','N',',',4) |
+-----------------------------+
| Y,N,Y,N                     |
+-----------------------------+
1 row in set (0.00 sec)

mysql> SELECT EXPORT_SET(6,'1','0',',',10);
+------------------------------+
| EXPORT_SET(6,'1','0',',',10) |
+------------------------------+
| 0,1,1,0,0,0,0,0,0,0          |
+------------------------------+
1 row in set (0.00 sec)
```
* FIELD(str,str1,str2,str3,...)
```sql
mysql> SELECT FIELD('Bb', 'Aa', 'Bb', 'Cc', 'Dd', 'Ff');
+-------------------------------------------+
| FIELD('Bb', 'Aa', 'Bb', 'Cc', 'Dd', 'Ff') |
+-------------------------------------------+
|                                         2 |
+-------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT FIELD('Gg', 'Aa', 'Bb', 'Cc', 'Dd', 'Ff');
+-------------------------------------------+
| FIELD('Gg', 'Aa', 'Bb', 'Cc', 'Dd', 'Ff') |
+-------------------------------------------+
|                                         0 |
+-------------------------------------------+
1 row in set (0.00 sec)
```
* FIND_IN_SET(str,strlist)
```sql
mysql> SELECT FIND_IN_SET('b','a,b,c,d');
+----------------------------+
| FIND_IN_SET('b','a,b,c,d') |
+----------------------------+
|                          2 |
+----------------------------+
1 row in set (0.00 sec)
```
* FORMAT(X,D[,locale])
```sql
mysql> SELECT FORMAT(12332.123456, 4);
+-------------------------+
| FORMAT(12332.123456, 4) |
+-------------------------+
| 12,332.1235             |
+-------------------------+
1 row in set (0.00 sec)

mysql> SELECT FORMAT(12332.1,4);
+-------------------+
| FORMAT(12332.1,4) |
+-------------------+
| 12,332.1000       |
+-------------------+
1 row in set (0.00 sec)

mysql> SELECT FORMAT(12332.2,0);
+-------------------+
| FORMAT(12332.2,0) |
+-------------------+
| 12,332            |
+-------------------+
1 row in set (0.00 sec)
```
* FROM_BASE64(str)
```sql
mysql> SELECT TO_BASE64('abc'), FROM_BASE64(TO_BASE64('abc'));
+------------------+--------------------------------------------------------------+
| TO_BASE64('abc') | FROM_BASE64(TO_BASE64('abc'))                                |
+------------------+--------------------------------------------------------------+
| YWJj             | 0x616263                                                     |
+------------------+--------------------------------------------------------------+
1 row in set (0.00 sec)
```
* HEX(str), HEX(N)
```sql
mysql> SELECT X'616263', HEX('abc'), UNHEX(HEX('abc'));
+----------------------+------------+--------------------------------------+
| X'616263'            | HEX('abc') | UNHEX(HEX('abc'))                    |
+----------------------+------------+--------------------------------------+
| 0x616263             | 616263     | 0x616263                             |
+----------------------+------------+--------------------------------------+
1 row in set (0.00 sec)
```
* INSERT(str,pos,len,newstr)
```sql
mysql> SELECT INSERT('Quadratic', 3, 4, 'What');
+-----------------------------------+
| INSERT('Quadratic', 3, 4, 'What') |
+-----------------------------------+
| QuWhattic                         |
+-----------------------------------+
1 row in set (0.00 sec)
```
* INSTR(str,substr)
```sql
mysql> SELECT INSTR('foobarbar', 'bar');
+---------------------------+
| INSTR('foobarbar', 'bar') |
+---------------------------+
|                         4 |
+---------------------------+
1 row in set (0.00 sec)
```
* LEFT(str,len)
```sql
mysql> SELECT LEFT('foobarbar', 5);
+----------------------+
| LEFT('foobarbar', 5) |
+----------------------+
| fooba                |
+----------------------+
1 row in set (0.00 sec)
```
* LENGTH(str)
```sql
mysql> SELECT LENGTH('text');
+----------------+
| LENGTH('text') |
+----------------+
|              4 |
+----------------+
1 row in set (0.00 sec)
```
* LOCATE(substr,str), LOCATE(substr,str,pos)
```sql
mysql> SELECT LOCATE('bar', 'foobarbar');
+----------------------------+
| LOCATE('bar', 'foobarbar') |
+----------------------------+
|                          4 |
+----------------------------+
1 row in set (0.00 sec)
```
* LOWER(str)
```sql
mysql> SELECT LOWER('QUADRATICALLY');
+------------------------+
| LOWER('QUADRATICALLY') |
+------------------------+
| quadratically          |
+------------------------+
1 row in set (0.00 sec)
```
* LPAD(str,len,padstr)
```sql
mysql> SELECT LPAD('hi',4,'??');
+-------------------+
| LPAD('hi',4,'??') |
+-------------------+
| ??hi              |
+-------------------+
1 row in set (0.00 sec)
```
* LTRIM(str)
```sql
mysql> SELECT LTRIM('  barbar');
+-------------------+
| LTRIM('  barbar') |
+-------------------+
| barbar            |
+-------------------+
1 row in set (0.00 sec)
```
* MAKE_SET(bits,str1,str2,...)
```sql
mysql> SELECT MAKE_SET(1,'a','b','c');
+-------------------------+
| MAKE_SET(1,'a','b','c') |
+-------------------------+
| a                       |
+-------------------------+
1 row in set (0.00 sec)

mysql> SELECT MAKE_SET(1 | 4,'hello','nice','world');
+----------------------------------------+
| MAKE_SET(1 | 4,'hello','nice','world') |
+----------------------------------------+
| hello,world                            |
+----------------------------------------+
1 row in set (0.00 sec)
```
* OCT(N)
```sql
mysql> SELECT OCT(12);
+---------+
| OCT(12) |
+---------+
| 14      |
+---------+
1 row in set (0.00 sec)
```
* QUOTE(str)
```sql
mysql> SELECT QUOTE('Don\'t!');
+------------------+
| QUOTE('Don\'t!') |
+------------------+
| 'Don\'t!'        |
+------------------+
1 row in set (0.00 sec)
```
* REPEAT(str,count)
```sql
mysql> SELECT REPEAT('MySQL', 3);
+--------------------+
| REPEAT('MySQL', 3) |
+--------------------+
| MySQLMySQLMySQL    |
+--------------------+
1 row in set (0.00 sec)
```
* REPLACE(str,from_str,to_str)
```sql
mysql> SELECT REPLACE('www.mysql.com', 'w', 'Ww');
+-------------------------------------+
| REPLACE('www.mysql.com', 'w', 'Ww') |
+-------------------------------------+
| WwWwWw.mysql.com                    |
+-------------------------------------+
1 row in set (0.00 sec)
```
* REVERSE(str)
```sql
mysql> SELECT REVERSE('abc');
+----------------+
| REVERSE('abc') |
+----------------+
| cba            |
+----------------+
1 row in set (0.00 sec)
```
* RIGHT(str,len)
```sql
mysql> SELECT RIGHT('foobarbar', 4);
+-----------------------+
| RIGHT('foobarbar', 4) |
+-----------------------+
| rbar                  |
+-----------------------+
1 row in set (0.00 sec)
```
* RPAD(str,len,padstr)
```sql
mysql> SELECT RPAD('hi',5,'?');
+------------------+
| RPAD('hi',5,'?') |
+------------------+
| hi???            |
+------------------+
1 row in set (0.00 sec)
```
* RTRIM(str)
```sql
mysql> SELECT RTRIM('barbar   ');
+--------------------+
| RTRIM('barbar   ') |
+--------------------+
| barbar             |
+--------------------+
1 row in set (0.00 sec)
```
* SOUNDEX(str)
```sql
mysql> SELECT SOUNDEX('Hello');
+------------------+
| SOUNDEX('Hello') |
+------------------+
| H400             |
+------------------+
1 row in set (0.00 sec)
```
* SPACE(N)
```sql
mysql> SELECT SPACE(6);
+----------+
| SPACE(6) |
+----------+
|          |
+----------+
1 row in set (0.00 sec)
```
* SUBSTRING(str,pos), SUBSTRING(str FROM pos), SUBSTRING(str,pos,len), SUBSTRING(str FROM pos FOR len)
```sql
mysql> SELECT SUBSTRING('Quadratically',5);
+------------------------------+
| SUBSTRING('Quadratically',5) |
+------------------------------+
| ratically                    |
+------------------------------+
1 row in set (0.00 sec)

mysql> SELECT SUBSTRING('foobarbar' FROM 4);
+-------------------------------+
| SUBSTRING('foobarbar' FROM 4) |
+-------------------------------+
| barbar                        |
+-------------------------------+
1 row in set (0.00 sec)

mysql> SELECT SUBSTRING('Quadratically',5,6);
+--------------------------------+
| SUBSTRING('Quadratically',5,6) |
+--------------------------------+
| ratica                         |
+--------------------------------+
1 row in set (0.00 sec)
```
* SUBSTRING_INDEX(str,delim,count)
```sql
mysql> SELECT SUBSTRING_INDEX('www.mysql.com', '.', 2);
+------------------------------------------+
| SUBSTRING_INDEX('www.mysql.com', '.', 2) |
+------------------------------------------+
| www.mysql                                |
+------------------------------------------+
1 row in set (0.00 sec)
```
* TO_BASE64(str)
```sql
mysql> SELECT TO_BASE64('abc'), FROM_BASE64(TO_BASE64('abc'));
+------------------+--------------------------------------------------------------+
| TO_BASE64('abc') | FROM_BASE64(TO_BASE64('abc'))                                |
+------------------+--------------------------------------------------------------+
| YWJj             | 0x616263                                                     |
+------------------+--------------------------------------------------------------+
1 row in set (0.00 sec)
```
* TRIM([{BOTH | LEADING | TRAILING} [remstr] FROM] str), TRIM([remstr FROM] str)
```sql
mysql> SELECT TRIM('  bar   ');
+------------------+
| TRIM('  bar   ') |
+------------------+
| bar              |
+------------------+
1 row in set (0.00 sec)
```
* UNHEX(str)
```Sql
mysql> SELECT UNHEX('4D7953514C');
+------------------------------------------+
| UNHEX('4D7953514C')                      |
+------------------------------------------+
| 0x4D7953514C                             |
+------------------------------------------+
1 row in set (0.00 sec)
```
* UPPER(str)
```sql
mysql> SELECT UPPER('Hej');
+--------------+
| UPPER('Hej') |
+--------------+
| HEJ          |
+--------------+
1 row in set (0.00 sec)
```
### 12.8.1 文字列比較関数および演算子
* expr LIKE pat [ESCAPE 'escape_char']
```sql
mysql> SELECT 'David!' LIKE 'David_';
+------------------------+
| 'David!' LIKE 'David_' |
+------------------------+
|                      1 |
+------------------------+
1 row in set (0.00 sec)

mysql> SELECT 'David!' LIKE '%D%v%';
+-----------------------+
| 'David!' LIKE '%D%v%' |
+-----------------------+
|                     1 |
+-----------------------+
1 row in set (0.00 sec)

mysql> SELECT 'David_' LIKE 'David\_';
+-------------------------+
| 'David_' LIKE 'David\_' |
+-------------------------+
|                       1 |
+-------------------------+
1 row in set (0.00 sec)
```
* STRCMP(expr1,expr2)
```Sql
mysql> SELECT STRCMP('text', 'text2');
+-------------------------+
| STRCMP('text', 'text2') |
+-------------------------+
|                      -1 |
+-------------------------+
1 row in set (0.00 sec)

mysql> SELECT STRCMP('text2', 'text');
+-------------------------+
| STRCMP('text2', 'text') |
+-------------------------+
|                       1 |
+-------------------------+
1 row in set (0.00 sec)

mysql> SELECT STRCMP('text', 'text');
+------------------------+
| STRCMP('text', 'text') |
+------------------------+
|                      0 |
+------------------------+
1 row in set (0.00 sec)
```
### 12.8.2 正規表現
* expr REGEXP pat, expr RLIKE pat
```sql
mysql> SELECT 'new*\n*line' REGEXP 'new\\*.\\*line';
+---------------------------------------+
| 'new*\n*line' REGEXP 'new\\*.\\*line' |
+---------------------------------------+
|                                     0 |
+---------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT 'a' REGEXP '^[a-d]';
+---------------------+
| 'a' REGEXP '^[a-d]' |
+---------------------+
|                   1 |
+---------------------+
1 row in set (0.00 sec)

mysql> SELECT 'new*\n*line' REGEXP 'new\\*.\\*line';
+---------------------------------------+
| 'new*\n*line' REGEXP 'new\\*.\\*line' |
+---------------------------------------+
|                                     0 |
+---------------------------------------+
1 row in set (0.00 sec)
```
* REGEXP_INSTR(expr, pat[, pos[, occurrence[, return_option[, match_type]]]])
```sql
mysql> SELECT REGEXP_INSTR('dog cat dog', 'dog');
+------------------------------------+
| REGEXP_INSTR('dog cat dog', 'dog') |
+------------------------------------+
|                                  1 |
+------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT REGEXP_INSTR('dog cat dog', 'dog', 2);
+---------------------------------------+
| REGEXP_INSTR('dog cat dog', 'dog', 2) |
+---------------------------------------+
|                                     9 |
+---------------------------------------+
1 row in set (0.00 sec)
```
* REGEXP_LIKE(expr, pat[, match_type])
```sql
mysql> SELECT REGEXP_LIKE('CamelCase', 'CAMELCASE');
+---------------------------------------+
| REGEXP_LIKE('CamelCase', 'CAMELCASE') |
+---------------------------------------+
|                                     1 |
+---------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT REGEXP_LIKE('new*\n*line', 'new\\*.\\*line');
+----------------------------------------------+
| REGEXP_LIKE('new*\n*line', 'new\\*.\\*line') |
+----------------------------------------------+
|                                            0 |
+----------------------------------------------+
1 row in set (0.00 sec)
```
* REGEXP_REPLACE(expr, pat, repl[, pos[, occurrence[, match_type]]])
```Sql
mysql> SELECT REGEXP_REPLACE('a b c', 'b', 'X');
+-----------------------------------+
| REGEXP_REPLACE('a b c', 'b', 'X') |
+-----------------------------------+
| a X c                             |
+-----------------------------------+
1 row in set (0.00 sec)

mysql> SELECT REGEXP_REPLACE('abc def ghi', '[a-z]+', 'X', 1, 3);
+----------------------------------------------------+
| REGEXP_REPLACE('abc def ghi', '[a-z]+', 'X', 1, 3) |
+----------------------------------------------------+
| abc def X                                          |
+----------------------------------------------------+
1 row in set (0.00 sec)
```
* REGEXP_SUBSTR(expr, pat[, pos[, occurrence[, match_type]]])
```sql
mysql> SELECT REGEXP_SUBSTR('abc def ghi', '[a-z]+');
+----------------------------------------+
| REGEXP_SUBSTR('abc def ghi', '[a-z]+') |
+----------------------------------------+
| abc                                    |
+----------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT REGEXP_SUBSTR('abc def ghi', '[a-z]+', 1, 3);
+----------------------------------------------+
| REGEXP_SUBSTR('abc def ghi', '[a-z]+', 1, 3) |
+----------------------------------------------+
| ghi                                          |
+----------------------------------------------+
1 row in set (0.00 sec)
```
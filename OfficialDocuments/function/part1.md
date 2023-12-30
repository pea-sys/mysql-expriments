### 12.3 式評価での型変換
演算子が別の型のオペランドとともに使用されると、オペランドの互換性を保つために型変換が発生します。

```sql
mysql> SELECT 1+'1';
+-------+
| 1+'1' |
+-------+
|     2 |
+-------+
1 row in set (0.00 sec)

mysql> SELECT CONCAT(2,' test');
+-------------------+
| CONCAT(2,' test') |
+-------------------+
| 2 test            |
+-------------------+
1 row in set (0.00 sec)
```
CAST() 関数を明示的に使用して、数字を文字列に変換することもできます。
```sql
mysql> SELECT 38.8, CAST(38.8 AS CHAR);
+------+--------------------+
| 38.8 | CAST(38.8 AS CHAR) |
+------+--------------------+
| 38.8 | 38.8               |
+------+--------------------+
1 row in set (0.00 sec)

mysql> SELECT 38.8, CONCAT(38.8);
+------+--------------+
| 38.8 | CONCAT(38.8) |
+------+--------------+
| 38.8 | 38.8         |
+------+--------------+
1 row in set (0.00 sec)
```
比較演算での文字列から数字への変換を示しています
```sql
mysql> SELECT 1 > '6x';
+----------+
| 1 > '6x' |
+----------+
|        0 |
+----------+
1 row in set, 1 warning (0.00 sec)
mysql> SELECT 7 > '6x';
+----------+
| 7 > '6x' |
+----------+
|        1 |
+----------+
1 row in set, 1 warning (0.00 sec)
mysql> SELECT 0 > 'x6';
+----------+
| 0 > 'x6' |
+----------+
|        0 |
+----------+
1 row in set, 1 warning (0.00 sec)
mysql> SELECT 0 = 'x6';
+----------+
| 0 = 'x6' |
+----------+
|        1 |
+----------+
1 row in set, 1 warning (0.00 sec)
```
### 12.4.1 演算子の優先順位
```sql
mysql> SELECT 1+2*3;
+-------+
| 1+2*3 |
+-------+
|     7 |
+-------+
1 row in set (0.00 sec)

mysql> SELECT (1+2)*3;
+---------+
| (1+2)*3 |
+---------+
|       9 |
+---------+
1 row in set (0.00 sec)
```

### 12.4.2 比較関数と演算子
* =

等価

```Sql
mysql> SELECT 1 = 0;
+-------+
| 1 = 0 |
+-------+
|     0 |
+-------+
1 row in set (0.00 sec)

mysql> SELECT '0' = 0;
+---------+
| '0' = 0 |
+---------+
|       1 |
+---------+
1 row in set (0.00 sec)

mysql> SELECT '0.0' = 0;
+-----------+
| '0.0' = 0 |
+-----------+
|         1 |
+-----------+
1 row in set (0.00 sec)

mysql> SELECT '0.01' = 0;
+------------+
| '0.01' = 0 |
+------------+
|          0 |
+------------+
1 row in set (0.00 sec)

mysql> SELECT '.01' = 0.01;
+--------------+
| '.01' = 0.01 |
+--------------+
|            1 |
+--------------+
1 row in set (0.00 sec)
```
* <=>

NULL - 安全等価
```sql
mysql> SELECT 1 <=> 1, NULL <=> NULL, 1 <=> NULL;
+---------+---------------+------------+
| 1 <=> 1 | NULL <=> NULL | 1 <=> NULL |
+---------+---------------+------------+
|       1 |             1 |          0 |
+---------+---------------+------------+
1 row in set (0.00 sec)

mysql> SELECT 1 = 1, NULL = NULL, 1 = NULL;
+-------+-------------+----------+
| 1 = 1 | NULL = NULL | 1 = NULL |
+-------+-------------+----------+
|     1 |        NULL |     NULL |
+-------+-------------+----------+
1 row in set (0.00 sec)
```

* <>, !=
```sql
mysql> SELECT '.01' <> '0.01';
+-----------------+
| '.01' <> '0.01' |
+-----------------+
|               1 |
+-----------------+
1 row in set (0.00 sec)

mysql> SELECT .01 <> '0.01';
+---------------+
| .01 <> '0.01' |
+---------------+
|             0 |
+---------------+
1 row in set (0.00 sec)

mysql> SELECT 'zapp' <> 'zappp';
+-------------------+
| 'zapp' <> 'zappp' |
+-------------------+
|                 1 |
+-------------------+
1 row in set (0.00 sec)
```
* <=

```sql
mysql> SELECT 0.1 <= 2;
+----------+
| 0.1 <= 2 |
+----------+
|        1 |
+----------+
1 row in set (0.00 sec)
```

* <

```sql
mysql> SELECT 2 < 2;
+-------+
| 2 < 2 |
+-------+
|     0 |
+-------+
1 row in set (0.00 sec)
```

* 　>=

```sql
mysql> SELECT 2 >= 2;
+--------+
| 2 >= 2 |
+--------+
|      1 |
+--------+
1 row in set (0.00 sec)
```

* 　>


```sql
mysql> SELECT 2 > 2;
+-------+
| 2 > 2 |
+-------+
|     0 |
+-------+
1 row in set (0.00 sec)
```

* expr BETWEEN min AND max

```sql
mysql> SELECT 2 BETWEEN 1 AND 3, 2 BETWEEN 3 and 1;
+-------------------+-------------------+
| 2 BETWEEN 1 AND 3 | 2 BETWEEN 3 and 1 |
+-------------------+-------------------+
|                 1 |                 0 |
+-------------------+-------------------+
1 row in set (0.00 sec)

mysql> SELECT 1 BETWEEN 2 AND 3;
+-------------------+
| 1 BETWEEN 2 AND 3 |
+-------------------+
|                 0 |
+-------------------+
1 row in set (0.00 sec)

mysql> SELECT 'b' BETWEEN 'a' AND 'c';
+-------------------------+
| 'b' BETWEEN 'a' AND 'c' |
+-------------------------+
|                       1 |
+-------------------------+
1 row in set (0.00 sec)

mysql> SELECT 2 BETWEEN 2 AND '3';
+---------------------+
| 2 BETWEEN 2 AND '3' |
+---------------------+
|                   1 |
+---------------------+
1 row in set (0.00 sec)
```

* COALESCE(value,...)

リストの最初の非 NULL 値を返します。
```sql
mysql> SELECT COALESCE(NULL,1);
+------------------+
| COALESCE(NULL,1) |
+------------------+
|                1 |
+------------------+
1 row in set (0.00 sec)

mysql> SELECT COALESCE(NULL,NULL,NULL);
+--------------------------+
| COALESCE(NULL,NULL,NULL) |
+--------------------------+
|                     NULL |
+--------------------------+
1 row in set (0.00 sec)
```
* GREATEST(value1,value2,...)
```sql
mysql> SELECT GREATEST(2,0);
+---------------+
| GREATEST(2,0) |
+---------------+
|             2 |
+---------------+
1 row in set (0.00 sec)

mysql> SELECT GREATEST(34.0,3.0,5.0,767.0);
+------------------------------+
| GREATEST(34.0,3.0,5.0,767.0) |
+------------------------------+
|                        767.0 |
+------------------------------+
1 row in set (0.00 sec)

mysql> SELECT GREATEST('B','A','C');
+-----------------------+
| GREATEST('B','A','C') |
+-----------------------+
| C                     |
+-----------------------+
1 row in set (0.00 sec)

```

* expr IN (value,...)
```sql
mysql> SELECT 2 IN (0,3,5,7);
+----------------+
| 2 IN (0,3,5,7) |
+----------------+
|              0 |
+----------------+
1 row in set (0.00 sec)

mysql> SELECT 'wefwf' IN ('wee','wefwf','weg');
+----------------------------------+
| 'wefwf' IN ('wee','wefwf','weg') |
+----------------------------------+
|                                1 |
+----------------------------------+
1 row in set (0.00 sec)
```
* INTERVAL(N,N1,N2,N3,...)
```sql
mysql> SELECT INTERVAL(23, 1, 15, 17, 30, 44, 200);
+--------------------------------------+
| INTERVAL(23, 1, 15, 17, 30, 44, 200) |
+--------------------------------------+
|                                    3 |
+--------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT INTERVAL(10, 1, 10, 100, 1000);
+--------------------------------+
| INTERVAL(10, 1, 10, 100, 1000) |
+--------------------------------+
|                              2 |
+--------------------------------+
1 row in set (0.00 sec)

mysql> SELECT INTERVAL(22, 23, 30, 44, 200);
+-------------------------------+
| INTERVAL(22, 23, 30, 44, 200) |
+-------------------------------+
|                             0 |
+-------------------------------+
1 row in set (0.00 sec)
```
* IS boolean_value
```sql
mysql> SELECT 1 IS TRUE, 0 IS FALSE, NULL IS UNKNOWN;
+-----------+------------+-----------------+
| 1 IS TRUE | 0 IS FALSE | NULL IS UNKNOWN |
+-----------+------------+-----------------+
|         1 |          1 |               1 |
+-----------+------------+-----------------+
1 row in set (0.00 sec)
```
* IS NOT boolean_value
```sql
mysql> SELECT 1 IS NOT UNKNOWN, 0 IS NOT UNKNOWN, NULL IS NOT UNKNOWN;
+------------------+------------------+---------------------+
| 1 IS NOT UNKNOWN | 0 IS NOT UNKNOWN | NULL IS NOT UNKNOWN |
+------------------+------------------+---------------------+
|                1 |                1 |                   0 |
+------------------+------------------+---------------------+
1 row in set (0.00 sec)
```
* IS NULL
```sql
mysql> SELECT 1 IS NULL, 0 IS NULL, NULL IS NULL;
+-----------+-----------+--------------+
| 1 IS NULL | 0 IS NULL | NULL IS NULL |
+-----------+-----------+--------------+
|         0 |         0 |            1 |
+-----------+-----------+--------------+
1 row in set (0.00 sec)
```
* IS NOT NULL
```sql
mysql> SELECT 1 IS NOT NULL, 0 IS NOT NULL, NULL IS NOT NULL;
+---------------+---------------+------------------+
| 1 IS NOT NULL | 0 IS NOT NULL | NULL IS NOT NULL |
+---------------+---------------+------------------+
|             1 |             1 |                0 |
+---------------+---------------+------------------+
1 row in set (0.00 sec)
```
* ISNULL(expr)
```sql
mysql> SELECT ISNULL(1+1);
+-------------+
| ISNULL(1+1) |
+-------------+
|           0 |
+-------------+
1 row in set (0.00 sec)

mysql> SELECT ISNULL(1/0);
+-------------+
| ISNULL(1/0) |
+-------------+
|           1 |
+-------------+
1 row in set, 1 warning (0.00 sec)
```
* LEAST(value1,value2,...)
```sql
mysql> SELECT LEAST(2,0);
+------------+
| LEAST(2,0) |
+------------+
|          0 |
+------------+
1 row in set (0.00 sec)

mysql> SELECT LEAST(34.0,3.0,5.0,767.0);
+---------------------------+
| LEAST(34.0,3.0,5.0,767.0) |
+---------------------------+
|                       3.0 |
+---------------------------+
1 row in set (0.00 sec)

mysql> SELECT LEAST('B','A','C');
+--------------------+
| LEAST('B','A','C') |
+--------------------+
| A                  |
+--------------------+
1 row in set (0.00 sec)
```
### 12.4.3 論理演算子
* NOT, !
```sql
mysql> SELECT NOT 10;
+--------+
| NOT 10 |
+--------+
|      0 |
+--------+
1 row in set (0.00 sec)

mysql> SELECT NOT 0;
+-------+
| NOT 0 |
+-------+
|     1 |
+-------+
1 row in set (0.00 sec)

mysql> SELECT NOT NULL;
+----------+
| NOT NULL |
+----------+
|     NULL |
+----------+
1 row in set (0.00 sec)

mysql> SELECT ! (1+1);
+---------+
| ! (1+1) |
+---------+
|       0 |
+---------+
1 row in set, 1 warning (0.00 sec)

mysql> SELECT ! 1+1;
+-------+
| ! 1+1 |
+-------+
|     1 |
+-------+
1 row in set, 1 warning (0.00 sec)
```
* AND, &&
```sql
mysql> SELECT 1 AND 1;
+---------+
| 1 AND 1 |
+---------+
|       1 |
+---------+
1 row in set (0.00 sec)

mysql> SELECT 1 AND 0;
+---------+
| 1 AND 0 |
+---------+
|       0 |
+---------+
1 row in set (0.00 sec)

mysql>  SELECT 1 AND NULL;
+------------+
| 1 AND NULL |
+------------+
|       NULL |
+------------+
1 row in set (0.00 sec)

mysql> SELECT 0 AND NULL;
+------------+
| 0 AND NULL |
+------------+
|          0 |
+------------+
1 row in set (0.00 sec)

mysql> SELECT NULL AND 0;
+------------+
| NULL AND 0 |
+------------+
|          0 |
+------------+
1 row in set (0.00 sec)
```

* OR, ||
```sql
mysql> SELECT 1 OR 1;
+--------+
| 1 OR 1 |
+--------+
|      1 |
+--------+
1 row in set (0.00 sec)

mysql> SELECT 1 OR 0;
+--------+
| 1 OR 0 |
+--------+
|      1 |
+--------+
1 row in set (0.00 sec)

mysql> SELECT 0 OR 0;
+--------+
| 0 OR 0 |
+--------+
|      0 |
+--------+
1 row in set (0.00 sec)

mysql> SELECT 0 OR NULL;
+-----------+
| 0 OR NULL |
+-----------+
|      NULL |
+-----------+
1 row in set (0.00 sec)

mysql> SELECT 1 OR NULL;
+-----------+
| 1 OR NULL |
+-----------+
|         1 |
+-----------+
1 row in set (0.00 sec)
```
* XOR
```sql
mysql> SELECT 1 XOR 1;
+---------+
| 1 XOR 1 |
+---------+
|       0 |
+---------+
1 row in set (0.00 sec)

mysql> SELECT 1 XOR 0;
+---------+
| 1 XOR 0 |
+---------+
|       1 |
+---------+
1 row in set (0.00 sec)

mysql> SELECT 1 XOR NULL;
+------------+
| 1 XOR NULL |
+------------+
|       NULL |
+------------+
1 row in set (0.00 sec)

mysql> SELECT 1 XOR 1 XOR 1;
+---------------+
| 1 XOR 1 XOR 1 |
+---------------+
|             1 |
+---------------+
1 row in set (0.00 sec)
```
### 12.4.4 割り当て演算子
* :=
```sql
mysql> SELECT @var1, @var2;
+--------------+--------------+
| @var1        | @var2        |
+--------------+--------------+
| NULL         | NULL         |
+--------------+--------------+
1 row in set (0.00 sec)

mysql> SELECT @var1 := 1, @var2;
+------------+--------------+
| @var1 := 1 | @var2        |
+------------+--------------+
|          1 | NULL         |
+------------+--------------+
1 row in set, 1 warning (0.00 sec)

mysql> SELECT @var1, @var2;
+-------+--------------+
| @var1 | @var2        |
+-------+--------------+
|     1 | NULL         |
+-------+--------------+
1 row in set (0.00 sec)

mysql> SELECT @var1, @var2 := @var1;
+-------+----------------+
| @var1 | @var2 := @var1 |
+-------+----------------+
|     1 |              1 |
+-------+----------------+
1 row in set, 1 warning (0.00 sec)

mysql> SELECT @var1, @var2;
+-------+-------+
| @var1 | @var2 |
+-------+-------+
|     1 |     1 |
+-------+-------+
1 row in set (0.00 sec)

mysql> SELECT @var1:=COUNT(*) FROM t1;
+-----------------+
| @var1:=COUNT(*) |
+-----------------+
|               0 |
+-----------------+
1 row in set, 1 warning (0.05 sec)
```
* =
```sql
mysql> SELECT @var1 := 1, @var2;
+------------+--------------+
| @var1 := 1 | @var2        |
+------------+--------------+
|          1 | NULL         |
+------------+--------------+
1 row in set, 1 warning (0.00 sec)

mysql> SELECT @var1, @var2 := @var1;
+-------+----------------+
| @var1 | @var2 := @var1 |
+-------+----------------+
|     1 |              1 |
+-------+----------------+
1 row in set, 1 warning (0.00 sec)

mysql> SELECT @var1, @var2;
+-------+-------+
| @var1 | @var2 |
+-------+-------+
|     1 |     1 |
+-------+-------+
1 row in set (0.00 sec)
```
### 12.5 フロー制御関数
* CASE value WHEN compare_value THEN result [WHEN compare_value THEN result ...] [ELSE result] END
```sql
mysql> SELECT CASE 1 WHEN 1 THEN 'one'
    ->          WHEN 2 THEN 'two' ELSE 'more' END;
+---------------------------------------------------------------------+
| CASE 1 WHEN 1 THEN 'one'
         WHEN 2 THEN 'two' ELSE 'more' END |
+---------------------------------------------------------------------+
| one                                                                 |
+---------------------------------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT CASE WHEN 1>0 THEN 'true' ELSE 'false' END;
+--------------------------------------------+
| CASE WHEN 1>0 THEN 'true' ELSE 'false' END |
+--------------------------------------------+
| true                                       |
+--------------------------------------------+
1 row in set (0.00 sec)
```
* IF(expr1,expr2,expr3)
```sql
mysql> SELECT IF(1>2,2,3);
+-------------+
| IF(1>2,2,3) |
+-------------+
|           3 |
+-------------+
1 row in set (0.00 sec)

mysql> SELECT IF(1<2,'yes','no');
+--------------------+
| IF(1<2,'yes','no') |
+--------------------+
| yes                |
+--------------------+
1 row in set (0.00 sec)

mysql> SELECT IF(STRCMP('test','test1'),'no','yes');
+---------------------------------------+
| IF(STRCMP('test','test1'),'no','yes') |
+---------------------------------------+
| no                                    |
+---------------------------------------+
1 row in set (0.00 sec)
```
* IFNULL(expr1,expr2)
```sql
mysql> SELECT IFNULL(1,0);
+-------------+
| IFNULL(1,0) |
+-------------+
|           1 |
+-------------+
1 row in set (0.00 sec)

mysql> SELECT IFNULL(NULL,10);
+-----------------+
| IFNULL(NULL,10) |
+-----------------+
|              10 |
+-----------------+
1 row in set (0.00 sec)

mysql> SELECT IFNULL(1/0,10);
+----------------+
| IFNULL(1/0,10) |
+----------------+
|        10.0000 |
+----------------+
1 row in set, 1 warning (0.00 sec)

mysql> SELECT IFNULL(1/0,'yes');
+-------------------+
| IFNULL(1/0,'yes') |
+-------------------+
| yes               |
+-------------------+
1 row in set, 1 warning (0.00 sec)
```
* NULLIF(expr1,expr2)
```sql
mysql> SELECT NULLIF(1,1);
+-------------+
| NULLIF(1,1) |
+-------------+
|        NULL |
+-------------+
1 row in set (0.00 sec)

mysql> SELECT NULLIF(1,2);
+-------------+
| NULLIF(1,2) |
+-------------+
|           1 |
+-------------+
1 row in set (0.00 sec)
```
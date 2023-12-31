### 9.5 式
このセクションでは、式が MySQL で従う必要がある文法ルールをリストし、式に使用できる用語のタイプに関する追加情報を提供します。
```sql
mysql> SELECT DATE_ADD('2018-05-01',INTERVAL 1 DAY);
+---------------------------------------+
| DATE_ADD('2018-05-01',INTERVAL 1 DAY) |
+---------------------------------------+
| 2018-05-02                            |
+---------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT DATE_SUB('2018-05-01',INTERVAL 1 YEAR);
+----------------------------------------+
| DATE_SUB('2018-05-01',INTERVAL 1 YEAR) |
+----------------------------------------+
| 2017-05-01                             |
+----------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT DATE_ADD('2020-12-31 23:59:59',INTERVAL 1 SECOND);
+---------------------------------------------------+
| DATE_ADD('2020-12-31 23:59:59',INTERVAL 1 SECOND) |
+---------------------------------------------------+
| 2021-01-01 00:00:00                               |
+---------------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT DATE_ADD('2018-12-31 23:59:59',INTERVAL 1 DAY);
+------------------------------------------------+
| DATE_ADD('2018-12-31 23:59:59',INTERVAL 1 DAY) |
+------------------------------------------------+
| 2019-01-01 23:59:59                            |
+------------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT DATE_ADD('2100-12-31 23:59:59',INTERVAL '1:1' MINUTE_SECOND);
+--------------------------------------------------------------+
| DATE_ADD('2100-12-31 23:59:59',INTERVAL '1:1' MINUTE_SECOND) |
+--------------------------------------------------------------+
| 2101-01-01 00:01:00                                          |
+--------------------------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT DATE_SUB('2025-01-01 00:00:00',INTERVAL '1 1:1:1' DAY_SECOND);
+---------------------------------------------------------------+
| DATE_SUB('2025-01-01 00:00:00',INTERVAL '1 1:1:1' DAY_SECOND) |
+---------------------------------------------------------------+
| 2024-12-30 22:58:59                                           |
+---------------------------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT DATE_ADD('1900-01-01 00:00:00',INTERVAL '-1 10' DAY_HOUR);
+-----------------------------------------------------------+
| DATE_ADD('1900-01-01 00:00:00',INTERVAL '-1 10' DAY_HOUR) |
+-----------------------------------------------------------+
| 1899-12-30 14:00:00                                       |
+-----------------------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT DATE_SUB('1998-01-02', INTERVAL 31 DAY);
+-----------------------------------------+
| DATE_SUB('1998-01-02', INTERVAL 31 DAY) |
+-----------------------------------------+
| 1997-12-02                              |
+-----------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT DATE_ADD('1992-12-31 23:59:59.000002',INTERVAL '1.999999' SECOND_MICROSECOND);
+-------------------------------------------------------------------------------+
| DATE_ADD('1992-12-31 23:59:59.000002',INTERVAL '1.999999' SECOND_MICROSECOND) |
+-------------------------------------------------------------------------------+
| 1993-01-01 00:00:01.000001                                                    |
+-------------------------------------------------------------------------------+
1 row in set (0.00 sec)
```
- 演算子では、間隔から日付または日付間値を抽出しても意味がないため、INTERVAL expr unit は右側でのみ許可されます。
```sql
mysql> SELECT '2018-12-31 23:59:59' + INTERVAL 1 SECOND;
+-------------------------------------------+
| '2018-12-31 23:59:59' + INTERVAL 1 SECOND |
+-------------------------------------------+
| 2019-01-01 00:00:00                       |
+-------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT INTERVAL 1 DAY + '2018-12-31';
+-------------------------------+
| INTERVAL 1 DAY + '2018-12-31' |
+-------------------------------+
| 2019-01-01                    |
+-------------------------------+
1 row in set (0.00 sec)

mysql> SELECT '2025-01-01' - INTERVAL 1 SECOND;
+----------------------------------+
| '2025-01-01' - INTERVAL 1 SECOND |
+----------------------------------+
| 2024-12-31 23:59:59              |
+----------------------------------+
1 row in set (0.00 sec)

```
EXTRACT() 関数は、DATE_ADD() または DATE_SUB() と同じ種類の unit 指定子を使用しますが、日付演算を実行するのではなく、日付から部分を抽出します
```sql
mysql> SELECT EXTRACT(YEAR FROM '2019-07-02');
+---------------------------------+
| EXTRACT(YEAR FROM '2019-07-02') |
+---------------------------------+
|                            2019 |
+---------------------------------+
1 row in set (0.00 sec)

mysql> SELECT EXTRACT(YEAR_MONTH FROM '2019-07-02 01:02:03');
+------------------------------------------------+
| EXTRACT(YEAR_MONTH FROM '2019-07-02 01:02:03') |
+------------------------------------------------+
|                                         201907 |
+------------------------------------------------+
1 row in set (0.00 sec)
```
INTERVAL で文字列以外の値を指定する場合は注意してください。 たとえば、間隔指定子が HOUR_MINUTE の場合、6/4 は 6 時間 4 分として扱われますが、6/4 は 1.5000 と評価され、1 時間 5000 分として扱われます
```sql
mysql> SELECT '6/4', 6/4;
+-----+--------+
| 6/4 | 6/4    |
+-----+--------+
| 6/4 | 1.5000 |
+-----+--------+
1 row in set (0.00 sec)

mysql> SELECT DATE_ADD('2019-01-01', INTERVAL '6/4' HOUR_MINUTE);
+----------------------------------------------------+
| DATE_ADD('2019-01-01', INTERVAL '6/4' HOUR_MINUTE) |
+----------------------------------------------------+
| 2019-01-01 06:04:00                                |
+----------------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT DATE_ADD('2019-01-01', INTERVAL 6/4 HOUR_MINUTE);
+--------------------------------------------------+
| DATE_ADD('2019-01-01', INTERVAL 6/4 HOUR_MINUTE) |
+--------------------------------------------------+
| 2019-01-04 12:20:00                              |
+--------------------------------------------------+
1 row in set (0.00 sec)
```

```sql
mysql> SELECT CAST(6/4 AS DECIMAL(3,1));
+---------------------------+
| CAST(6/4 AS DECIMAL(3,1)) |
+---------------------------+
|                       1.5 |
+---------------------------+
1 row in set (0.00 sec)

mysql> SELECT DATE_ADD('1970-01-01 12:00:00', INTERVAL CAST(6/4 AS DECIMAL(3,1)) HOUR_MINUTE);
+---------------------------------------------------------------------------------+
| DATE_ADD('1970-01-01 12:00:00', INTERVAL CAST(6/4 AS DECIMAL(3,1)) HOUR_MINUTE) |
+---------------------------------------------------------------------------------+
| 1970-01-01 13:05:00                                                             |
+---------------------------------------------------------------------------------+
1 row in set (0.00 sec)
```
時間部分が含まれるものを日付値に加算したり、日付値から減算したりすると、自動的に結果が日付時間値に変換されます
```sql
mysql> SELECT DATE_ADD('2023-01-01', INTERVAL 1 DAY);
+----------------------------------------+
| DATE_ADD('2023-01-01', INTERVAL 1 DAY) |
+----------------------------------------+
| 2023-01-02                             |
+----------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT DATE_ADD('2023-01-01', INTERVAL 1 HOUR);
+-----------------------------------------+
| DATE_ADD('2023-01-01', INTERVAL 1 HOUR) |
+-----------------------------------------+
| 2023-01-01 01:00:00                     |
+-----------------------------------------+
1 row in set (0.00 sec)
```
MONTH、YEAR_MONTH、または YEAR を加算した結果の日付に、新しい月の最大日数よりも大きな日が含まれる場合は、その日が新しい月の最大日数に調整されます。
```sql
mysql> SELECT DATE_ADD('2019-01-30', INTERVAL 1 MONTH);
+------------------------------------------+
| DATE_ADD('2019-01-30', INTERVAL 1 MONTH) |
+------------------------------------------+
| 2019-02-28                               |
+------------------------------------------+
1 row in set (0.00 sec)
```
### 11.1.7 範囲外およびオーバーフローの処理
```sql
mysql> CREATE TABLE t1 (i1 TINYINT, i2 TINYINT UNSIGNED);
Query OK, 0 rows affected (0.02 sec)

mysql> SET sql_mode = 'TRADITIONAL';
Query OK, 0 rows affected (0.00 sec)

mysql> INSERT INTO t1 (i1, i2) VALUES(256, 256);
ERROR 1264 (22003): Out of range value for column 'i1' at row 1
mysql> SELECT * FROM t1;
Empty set (0.00 sec)
```
厳密な SQL モードが有効になっていない場合、警告付きでクリッピングが発生します
```sql
mysql> SET sql_mode = '';
Query OK, 0 rows affected (0.00 sec)

mysql> INSERT INTO t1 (i1, i2) VALUES(256, 256);
Query OK, 1 row affected, 2 warnings (0.01 sec)

mysql> SELECT * FROM t1;
+------+------+
| i1   | i2   |
+------+------+
|  127 |  255 |
+------+------+
1 row in set (0.00 sec)

```

```sql
mysql> SELECT 9223372036854775807 + 1;
ERROR 1690 (22003): BIGINT value is out of range in '(9223372036854775807 + 1)'
mysql> SELECT CAST(9223372036854775807 AS UNSIGNED) + 1;
+-------------------------------------------+
| CAST(9223372036854775807 AS UNSIGNED) + 1 |
+-------------------------------------------+
|                       9223372036854775808 |
+-------------------------------------------+
1 row in set (0.00 sec)
mysql> SELECT 9223372036854775807.0 + 1;
+---------------------------+
| 9223372036854775807.0 + 1 |
+---------------------------+
|     9223372036854775808.0 |
+---------------------------+
1 row in set (0.00 sec)

mysql> SELECT CAST(0 AS UNSIGNED) - 1;
ERROR 1690 (22003): BIGINT UNSIGNED value is out of range in '(cast(0 as unsigned) - 1)'
```

```sql
mysql> SET sql_mode = 'NO_UNSIGNED_SUBTRACTION';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT CAST(0 AS UNSIGNED) - 1;
+-------------------------+
| CAST(0 AS UNSIGNED) - 1 |
+-------------------------+
|                      -1 |
+-------------------------+
1 row in set (0.00 sec)
```

### 11.2.1 日時データ型の構文
```sql
mysql> CREATE TABLE t1 (t TIME(3), dt DATETIME(6), ts TIMESTAMP(0));
Query OK, 0 rows affected (0.03 sec)

mysql> SELECT SEC_TO_TIME(SUM(TIME_TO_SEC(dt))) FROM t1;
+-----------------------------------+
| SEC_TO_TIME(SUM(TIME_TO_SEC(dt))) |
+-----------------------------------+
| NULL                              |
+-----------------------------------+
1 row in set (0.01 sec)

mysql> SELECT FROM_DAYS(SUM(TO_DAYS(dt))) FROM t1;
+-----------------------------+
| FROM_DAYS(SUM(TO_DAYS(dt))) |
+-----------------------------+
| NULL                        |
+-----------------------------+
1 row in set (0.00 sec)
```

### 11.2.2 DATE、DATETIME、および TIMESTAMP 型
DATE、DATETIME、および TIMESTAMP 型は関連しています。
```sql
mysql>  CREATE TABLE ts (
    ->          id INTEGER NOT NULL AUTO_INCREMENT PRIMARY KEY,
    ->          col TIMESTAMP NOT NULL
    ->      ) AUTO_INCREMENT = 1;
Query OK, 0 rows affected (0.05 sec)

mysql> CREATE TABLE dt (
    ->          id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    ->          col DATETIME NOT NULL
    ->      ) AUTO_INCREMENT = 1;
Query OK, 0 rows affected (0.02 sec)

mysql> SET @@time_zone = '+00:00';
Query OK, 0 rows affected (0.00 sec)

mysql> INSERT INTO ts (col) VALUES ('2020-01-01 10:10:10'),
    ->          ('2020-01-01 10:10:10+05:30'), ('2020-01-01 10:10:10-08:00');
Query OK, 3 rows affected (0.00 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql> SET @@time_zone = 'SYSTEM';
Query OK, 0 rows affected (0.00 sec)

mysql> INSERT INTO dt (col) VALUES ('2020-01-01 10:10:10'),
    ->          ('2020-01-01 10:10:10+05:30'), ('2020-01-01 10:10:10-08:00');
Query OK, 3 rows affected (0.01 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql> SET @@time_zone = '+00:00';
Query OK, 0 rows affected (0.00 sec)

mysql> INSERT INTO dt (col) VALUES ('2020-01-01 10:10:10'),
    ->          ('2020-01-01 10:10:10+05:30'), ('2020-01-01 10:10:10-08:00');
Query OK, 3 rows affected (0.00 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql> SET @@time_zone = 'SYSTEM';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT @@system_time_zone;
+--------------------+
| @@system_time_zone |
+--------------------+
| ???? (?W????)      |
+--------------------+
1 row in set (0.00 sec)

mysql> SELECT col, UNIX_TIMESTAMP(col) FROM dt ORDER BY id;
+---------------------+---------------------+
| col                 | UNIX_TIMESTAMP(col) |
+---------------------+---------------------+
| 2020-01-01 10:10:10 |          1577841010 |
| 2020-01-01 13:40:10 |          1577853610 |
| 2020-01-02 03:10:10 |          1577902210 |
| 2020-01-01 10:10:10 |          1577841010 |
| 2020-01-01 04:40:10 |          1577821210 |
| 2020-01-01 18:10:10 |          1577869810 |
+---------------------+---------------------+
6 rows in set (0.01 sec)

mysql> SELECT col, UNIX_TIMESTAMP(col) FROM ts ORDER BY id;
+---------------------+---------------------+
| col                 | UNIX_TIMESTAMP(col) |
+---------------------+---------------------+
| 2020-01-01 10:10:10 |          1577841010 |
| 2020-01-01 13:40:10 |          1577853610 |
| 2020-01-02 03:10:10 |          1577902210 |
| 2020-01-01 19:10:10 |          1577873410 |
| 2020-01-01 13:40:10 |          1577853610 |
| 2020-01-02 03:10:10 |          1577902210 |
+---------------------+---------------------+
6 rows in set (0.00 sec)

mysql> SELECT col,
    ->           CAST(col AT TIME ZONE INTERVAL '+00:00' AS DATETIME) AS ut
    ->           FROM ts ORDER BY id;
+---------------------+---------------------+
| col                 | ut                  |
+---------------------+---------------------+
| 2020-01-01 10:10:10 | 2020-01-01 01:10:10 |
| 2020-01-01 13:40:10 | 2020-01-01 04:40:10 |
| 2020-01-02 03:10:10 | 2020-01-01 18:10:10 |
| 2020-01-01 19:10:10 | 2020-01-01 10:10:10 |
| 2020-01-01 13:40:10 | 2020-01-01 04:40:10 |
| 2020-01-02 03:10:10 | 2020-01-01 18:10:10 |
+---------------------+---------------------+
6 rows in set (0.00 sec)
```

### 11.2.5 TIMESTAMP および DATETIME の自動初期化および更新機能
DEFAULT CURRENT_TIMESTAMP と ON UPDATE CURRENT_TIMESTAMP の両方を使用した場合、カラムは、デフォルト値が現在のタイムスタンプになり、現在のタイムスタンプに自動的に更新されます。

```sql
mysql> CREATE TABLE t1 (
    ->   ts TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    ->   dt DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
    -> );
Query OK, 0 rows affected (0.02 sec)
```
DEFAULT 句を使用するが ON UPDATE CURRENT_TIMESTAMP 句を使用しない場合、カラムには所定のデフォルト値が設定され、現在のタイムスタンプに自動的に更新されません。
```sql
mysql> CREATE TABLE t1 (
    ->   ts TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    ->   dt DATETIME DEFAULT CURRENT_TIMESTAMP
    -> );
Query OK, 0 rows affected (0.02 sec)
```
ON UPDATE CURRENT_TIMESTAMP 句と定数の DEFAULT 句を使用した場合、カラムは、現在のタイムスタンプに自動的に更新され、所定の定数のデフォルト値があります。
```sql
mysql> SET sql_mode = '';
Query OK, 0 rows affected (0.00 sec)

mysql> CREATE TABLE t1 (
    ->   ts TIMESTAMP DEFAULT 0 ON UPDATE CURRENT_TIMESTAMP,
    ->   dt DATETIME DEFAULT 0 ON UPDATE CURRENT_TIMESTAMP
    -> );
Query OK, 0 rows affected (0.02 sec)
```
#### TIMESTAMP の初期化と NULL 属性
explicit_defaults_for_timestamp システム変数が無効になっている場合、TIMESTAMP カラムはデフォルトで NOT NULL であり、NULL 値を含めることはできず、NULL を割り当てると現在のタイムスタンプが割り当てられます。 NULL を含めるように TIMESTAMP カラムを許可するには、NULL 属性で明示的に宣言します。 
```sql
mysql> CREATE TABLE t
    -> (
    ->   ts1 TIMESTAMP NULL DEFAULT NULL,
    ->   ts2 TIMESTAMP NULL DEFAULT 0,
    ->   ts3 TIMESTAMP NULL DEFAULT CURRENT_TIMESTAMP
    -> );
Query OK, 0 rows affected (0.02 sec)
```

### 11.2.6 時間値での小数秒
MySQL では、マイクロ秒 (6 桁) までの精度で、TIME、DATETIME および TIMESTAMP 値の小数秒がサポートされています


小数秒部を含むカラムを定義するには、type_name(fsp) の構文を使用します。ここで、type_name は TIME、DATETIME、または TIMESTAMP であり、fsp は小数秒の精度です
```sql
mysql> CREATE TABLE t1 (t TIME(3), dt DATETIME(6));
ERROR 1046 (3D000): No database selected
mysql> use sakila;
Database changed
mysql> CREATE TABLE t1 (t TIME(3), dt DATETIME(6));
Query OK, 0 rows affected (0.04 sec)
```
小数秒の部分を含む TIME、DATE または TIMESTAMP 値を同じタイプのカラムに挿入すると、小数点以下の桁数が少なくなります。
```sql
mysql> CREATE TABLE fractest( c1 TIME(2), c2 DATETIME(2), c3 TIMESTAMP(2) );
Query OK, 0 rows affected (0.03 sec)

mysql> INSERT INTO fractest VALUES
    -> ('17:51:04.777', '2018-09-08 17:51:04.777', '2018-09-08 17:51:04.777');
Query OK, 1 row affected (0.01 sec)

mysql> SELECT * FROM fractest;
+-------------+------------------------+------------------------+
| c1          | c2                     | c3                     |
+-------------+------------------------+------------------------+
| 17:51:04.78 | 2018-09-08 17:51:04.78 | 2018-09-08 17:51:04.78 |
+-------------+------------------------+------------------------+
1 row in set (0.00 sec)
```

 SQL モードを有効にすると、時間値は切捨てとともに挿入されます
```sql
mysql> SET @@sql_mode = sys.list_add(@@sql_mode, 'TIME_TRUNCATE_FRACTIONAL');
Query OK, 0 rows affected (0.03 sec)

mysql> SELECT * FROM fractest;
+-------------+------------------------+------------------------+
| c1          | c2                     | c3                     |
+-------------+------------------------+------------------------+
| 17:51:04.78 | 2018-09-08 17:51:04.78 | 2018-09-08 17:51:04.78 |
+-------------+------------------------+------------------------+
1 row in set (0.00 sec)
```

### 11.2.7 日付と時間型間での変換
ある程度まで、ある時間型から別の時間型に値を変換できます。
```sql
mysql> SELECT CURTIME(), CURTIME()+0, CURTIME(3)+0;
+-----------+-------------+--------------+
| CURTIME() | CURTIME()+0 | CURTIME(3)+0 |
+-----------+-------------+--------------+
| 07:45:26  |       74526 |    74526.330 |
+-----------+-------------+--------------+
1 row in set (0.01 sec)

mysql> SELECT NOW(), NOW()+0, NOW(3)+0;
+---------------------+----------------+--------------------+
| NOW()               | NOW()+0        | NOW(3)+0           |
+---------------------+----------------+--------------------+
| 2023-12-16 07:45:31 | 20231216074531 | 20231216074531.999 |
+---------------------+----------------+--------------------+
1 row in set (0.00 sec)
```

### 11.3.1 文字列データ型の構文
文字列データ型は、CHAR, VARCHAR, BINARY, VARBINARY, BLOB, TEXT, ENUM および SET です。

CHARACTER SET では、文字セットを指定します。
```sql
mysql> CREATE TABLE t
    -> (
    ->     c1 VARCHAR(20) CHARACTER SET utf8,
    ->     c2 TEXT CHARACTER SET latin1 COLLATE latin1_general_cs
    -> );
Query OK, 0 rows affected, 1 warning (0.02 sec)
```
文字列データ型に CHARACTER SET binary 属性を指定すると、対応するバイナリ文字列データ型としてカラムが作成されます
```sql
mysql> CREATE TABLE t
    -> (
    ->   c1 VARCHAR(10) CHARACTER SET binary,
    ->   c2 TEXT CHARACTER SET binary,
    ->   c3 ENUM('a','b','c') CHARACTER SET binary
    -> );
Query OK, 0 rows affected (0.02 sec)
```

### 11.3.2 CHAR および VARCHAR 型
CHAR 型と VARCHAR 型は似ていますが、格納および取得方法が異なります。 また、最大長と、末尾のスペースが保持されるかどうかという点でも異なります。

所定の値が CHAR(4) および VARCHAR(4) カラムに格納されると、取り出しのときに末尾のスペースが CHAR カラムから削除されるので、カラムから取り出された値は必ずしも同じではありません。
```sql
mysql> CREATE TABLE vc (v VARCHAR(4), c CHAR(4));
Query OK, 0 rows affected (0.02 sec)

mysql> INSERT INTO vc VALUES ('ab  ', 'ab  ');
Query OK, 1 row affected (0.01 sec)

mysql> SELECT CONCAT('(', v, ')'), CONCAT('(', c, ')') FROM vc;
+---------------------+---------------------+
| CONCAT('(', v, ')') | CONCAT('(', c, ')') |
+---------------------+---------------------+
| (ab  )              | (ab)                |
+---------------------+---------------------+
1 row in set (0.00 sec)
```

### 11.3.3 BINARY および VARBINARY 型
BINARY および VARBINARY 型は、非バイナリ文字列ではなくバイナリ文字列を格納する点を除き、CHAR および VARCHAR と似ています。
つまり、文字列ではなくバイト文字列が格納されます
```sql
mysql> CREATE TABLE t (c BINARY(3));
Query OK, 0 rows affected (0.02 sec)

mysql> INSERT INTO t SET c = 'a';
Query OK, 1 row affected (0.00 sec)

mysql> SELECT HEX(c), c = 'a', c = 'a\0\0' from t;
+--------+---------+-------------+
| HEX(c) | c = 'a' | c = 'a\0\0' |
+--------+---------+-------------+
| 610000 |       0 |           1 |
+--------+---------+-------------+
1 row in set (0.00 sec)
```
### 11.3.5 ENUM 型
ENUM は、テーブル作成時にカラム仕様に明示的に列挙された、許可されている値のリストから選択された値を持つ文字列オブジェクトです。
```sql
mysql> CREATE TABLE shirts (
    ->     name VARCHAR(40),
    ->     size ENUM('x-small', 'small', 'medium', 'large', 'x-large')
    -> );
Query OK, 0 rows affected (0.02 sec)

mysql> INSERT INTO shirts (name, size) VALUES ('dress shirt','large'), ('t-shirt','medium'),
    ->   ('polo shirt','small');
Query OK, 3 rows affected (0.01 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql> SELECT name, size FROM shirts WHERE size = 'medium';
+---------+--------+
| name    | size   |
+---------+--------+
| t-shirt | medium |
+---------+--------+
1 row in set (0.00 sec)

mysql> UPDATE shirts SET size = 'small' WHERE size = 'large';
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0
``` 

### 11.3.6 SET 型
SET は、ゼロ以上の値を取ることができる文字列オブジェクトであり、それぞれの値は、テーブルの作成時に指定された許可される値のリストから選択する必要があります。 
SET カラムには最大 64 個の個別のメンバーを含めることができます。
```sql
mysql> CREATE TABLE myset (col SET('a', 'b', 'c', 'd'));
Query OK, 0 rows affected (0.02 sec)

mysql> INSERT INTO myset (col) VALUES ('a,d'), ('d,a'), ('a,d,a'), ('a,d,d'), ('d,a,d');
Query OK, 5 rows affected (0.00 sec)
Records: 5  Duplicates: 0  Warnings: 0

mysql> SELECT col FROM myset;
+------+
| col  |
+------+
| a,d  |
| a,d  |
| a,d  |
| a,d  |
| a,d  |
+------+
5 rows in set (0.00 sec)
```
サポートされていない値に SET カラムを設定すると、その値は無視され警告が表示されます。
```sql
mysql> INSERT INTO myset (col) VALUES ('a,d,d,s');
ERROR 1265 (01000): Data truncated for column 'col' at row 1
```
通常は、FIND_IN_SET() 関数か LIKE 演算子を使用して SET 値を検索します
```sql
mysql> SELECT * FROM myset WHERE FIND_IN_SET('a',col)>0;
+------+
| col  |
+------+
| a,d  |
| a,d  |
| a,d  |
| a,d  |
| a,d  |
+------+
5 rows in set (0.00 sec)
```

### 11.4.1 空間データ型
MySQL には、OpenGIS クラスに対応する空間データ型があります。
```sql
mysql> CREATE TABLE geom (g GEOMETRY);
Query OK, 0 rows affected (0.03 sec)
```
```sql
CREATE TABLE geom (
    p POINT SRID 0,
    g GEOMETRY NOT NULL SRID 4326
);
```

### 11.4.5 空間参照システムのサポート
空間データの空間参照システム (SRS) は、地理的位置の調整ベースのシステムです。

SRS エントリの例を次に示します
```sql
mysql> SELECT *
    ->        FROM INFORMATION_SCHEMA.ST_SPATIAL_REFERENCE_SYSTEMS
    ->        WHERE SRS_ID = 4326\G
*************************** 1. row ***************************
                SRS_NAME: WGS 84
                  SRS_ID: 4326
            ORGANIZATION: EPSG
ORGANIZATION_COORDSYS_ID: 4326
              DEFINITION: GEOGCS["WGS 84",DATUM["World Geodetic System 1984",SPHEROID["WGS 84",6378137,298.257223563,AUTHORITY["EPSG","7030"]],AUTHORITY["EPSG","6326"]],PRIMEM["Greenwich",0,AUTHORITY["EPSG","8901"]],UNIT["degree",0.017453292519943278,AUTHORITY["EPSG","9122"]],AXIS["Lat",NORTH],AXIS["Lon",EAST],AUTHORITY["EPSG","4326"]]
             DESCRIPTION: NULL
1 row in set (0.01 sec)
```

### 11.4.6 空間カラムの作成
MySQL には、CREATE TABLE や ALTER TABLE を使用する方法など、ジオメトリ型の空間カラムを作成するための標準的な方法が用意されています。
```sql
mysql> CREATE TABLE geom (g GEOMETRY);
Query OK, 0 rows affected (0.02 sec)
```

### 11.4.7 空間カラムへのデータ移入
空間カラムを作成し終わったら、空間データを移入できます。
```Sql
mysql> INSERT INTO geom VALUES (ST_GeomFromText('POINT(1 1)'));
Query OK, 1 row affected (0.01 sec)

mysql> SET @g = 'POINT(1 1)';
Query OK, 0 rows affected (0.00 sec)

mysql> INSERT INTO geom VALUES (ST_GeomFromText(@g));
Query OK, 1 row affected (0.00 sec)

mysql> SET @g = 'LINESTRING(0 0,1 1,2 2)';
Query OK, 0 rows affected (0.00 sec)

mysql> INSERT INTO geom VALUES (ST_GeomFromText(@g));
Query OK, 1 row affected (0.00 sec)

mysql>
mysql> SET @g = 'POLYGON((0 0,10 0,10 10,0 10,0 0),(5 5,7 5,7 7,5 7, 5 5))';
Query OK, 0 rows affected (0.00 sec)

mysql> INSERT INTO geom VALUES (ST_GeomFromText(@g));
Query OK, 1 row affected (0.00 sec)

mysql>
mysql> SET @g =
    -> 'GEOMETRYCOLLECTION(POINT(1 1),LINESTRING(0 0,1 1,2 2,3 3,4 4))';
Query OK, 0 rows affected (0.00 sec)

mysql> INSERT INTO geom VALUES (ST_GeomFromText(@g));
Query OK, 1 row affected (0.00 sec)

mysql> INSERT INTO geom VALUES (ST_LineStringFromText(@g));
Query OK, 1 row affected (0.00 sec)

mysql>
mysql> SET @g = 'POLYGON((0 0,10 0,10 10,0 10,0 0),(5 5,7 5,7 7,5 7, 5 5))';
Query OK, 0 rows affected (0.00 sec)

mysql> INSERT INTO geom VALUES (ST_PolygonFromText(@g));
Query OK, 1 row affected (0.00 sec)

mysql>
mysql> SET @g =
    -> 'GEOMETRYCOLLECTION(POINT(1 1),LINESTRING(0 0,1 1,2 2,3 3,4 4))';
Query OK, 0 rows affected (0.00 sec)

mysql> INSERT INTO geom VALUES (ST_GeomCollFromText(@g));
Query OK, 1 row affected (0.00 sec)

```

### 11.4.8 空間データのフェッチ
テーブルに格納されたジオメトリ値は内部形式でフェッチできます。 WKT 形式から WKB 形式に変換することもできます。
```sql
mysql> CREATE TABLE geom2 (g GEOMETRY) SELECT g FROM geom;
Query OK, 9 rows affected (0.02 sec)
Records: 9  Duplicates: 0  Warnings: 0

mysql> SELECT ST_AsText(g) FROM geom;
+----------------------------------------------------------------+
| ST_AsText(g)                                                   |
+----------------------------------------------------------------+
| POINT(1 1)                                                     |
| POINT(1 1)                                                     |
| LINESTRING(0 0,1 1,2 2)                                        |
| POLYGON((0 0,10 0,10 10,0 10,0 0),(5 5,7 5,7 7,5 7,5 5))       |
| GEOMETRYCOLLECTION(POINT(1 1),LINESTRING(0 0,1 1,2 2,3 3,4 4)) |
| POINT(1 1)                                                     |
| LINESTRING(0 0,1 1,2 2)                                        |
| POLYGON((0 0,10 0,10 10,0 10,0 0),(5 5,7 5,7 7,5 7,5 5))       |
| GEOMETRYCOLLECTION(POINT(1 1),LINESTRING(0 0,1 1,2 2,3 3,4 4)) |
+----------------------------------------------------------------+
9 rows in set (0.00 sec)

mysql> SELECT ST_AsBinary(g) FROM geom;
+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ST_AsBinary(g)

                                                                                                                       |
+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| 0x0101000000000000000000F03F000000000000F03F

                                                                                                                       |
| 0x0101000000000000000000F03F000000000000F03F

                                                                                                                       |
| 0x01020000000300000000000000000000000000000000000000000000000000F03F000000000000F03F00000000000000400000000000000040  
                                                                                                                       |
| 0x01030000000200000005000000000000000000000000000000000000000000000000002440000000000000000000000000000024400000000000002440000000000000000000000000000024400000000000000000000000000000000005000000000000000000144000000000000014400000000000001C4000000000000014400000000000001C400000000000001C4000000000000014400000000000001C4000000000000014400000000000001440 |
| 0x0107000000020000000101000000000000000000F03F000000000000F03F01020000000500000000000000000000000000000000000000000000000000F03F000000000000F03F000000000000004000000000000000400000000000000840000000000000084000000000000010400000000000001040                                                                                                                     |
| 0x0101000000000000000000F03F000000000000F03F

                                                                                                                       |
| 0x01020000000300000000000000000000000000000000000000000000000000F03F000000000000F03F00000000000000400000000000000040  
                                                                                                                       |
| 0x01030000000200000005000000000000000000000000000000000000000000000000002440000000000000000000000000000024400000000000002440000000000000000000000000000024400000000000000000000000000000000005000000000000000000144000000000000014400000000000001C4000000000000014400000000000001C400000000000001C4000000000000014400000000000001C4000000000000014400000000000001440 |
| 0x0107000000020000000101000000000000000000F03F000000000000F03F01020000000500000000000000000000000000000000000000000000000000F03F000000000000F03F000000000000004000000000000000400000000000000840000000000000084000000000000010400000000000001040                                                                                                                     |
+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
9 rows in set (0.00 sec)
```

### 11.4.10 空間インデックスの作成
InnoDB および MyISAM テーブルの場合、MySQL では、通常のインデックスを作成する場合と同様の構文を使用して空間インデックスを作成できますが、SPATIAL キーワードを使用します。

CREATE TABLE を使用する場合
```sql
mysql> CREATE TABLE geom (g GEOMETRY NOT NULL SRID 4326, SPATIAL INDEX(g));
Query OK, 0 rows affected (0.10 sec)
```
ALTER TABLE を使用する場合
```sql
mysql> CREATE TABLE geom (g GEOMETRY NOT NULL SRID 4326);
Query OK, 0 rows affected (0.03 sec)

mysql> ALTER TABLE geom ADD SPATIAL INDEX(g);
Query OK, 0 rows affected (0.03 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
CREATE INDEX を使用する場合
```sql
mysql> CREATE TABLE geom (g GEOMETRY NOT NULL SRID 4326);
Query OK, 0 rows affected (0.03 sec)

mysql> CREATE SPATIAL INDEX g ON geom (g);
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
空間インデックスを削除するには、次のように ALTER TABLE または DROP INDEX を使用します
```sql
mysql> ALTER TABLE geom DROP INDEX g;
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
DROP INDEX を使用する場合:
```sql
mysql> DROP INDEX g ON geom;
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

### 11.5 JSON データ型
値を JSON カラムに挿入しようとすると、その値が有効な JSON 値である場合は成功しますが、そうでない場合は失敗します
```sql
mysql> CREATE TABLE t1 (jdoc JSON);
Query OK, 0 rows affected (0.02 sec)

mysql> INSERT INTO t1 VALUES('{"key1": "value1", "key2": "value2"}');
Query OK, 1 row affected (0.01 sec)

mysql> INSERT INTO t1 VALUES('[1, 2,');
ERROR 3140 (22032): Invalid JSON text: "Invalid value." at position 6 in value for column 't1.jdoc'.
```
JSON_TYPE() 関数は、JSON 引数を想定し、JSON 値に解析しようとします。 値 JSON 型が有効な場合はそれを返し、それ以外の場合はエラーを生成します
```sql
mysql> SELECT JSON_TYPE('["a", "b", 1]');
+----------------------------+
| JSON_TYPE('["a", "b", 1]') |
+----------------------------+
| ARRAY                      |
+----------------------------+
1 row in set (0.00 sec)

mysql> SELECT JSON_TYPE('"hello"');
+----------------------+
| JSON_TYPE('"hello"') |
+----------------------+
| STRING               |
+----------------------+
1 row in set (0.00 sec)

mysql> SELECT JSON_TYPE('hello');
ERROR 3141 (22032): Invalid JSON text in argument 1 to function json_type: "Invalid value." at position 0.
```
JSON_ARRAY() は、(空の可能性がある) 値リストを取得し、これらの値を含む JSON 配列を返します
```sql
mysql> SELECT JSON_ARRAY('a', 1, NOW());
+----------------------------------------+
| JSON_ARRAY('a', 1, NOW())              |
+----------------------------------------+
| ["a", 1, "2023-12-16 10:21:55.000000"] |
+----------------------------------------+
1 row in set (0.00 sec)
```
JSON_OBJECT() は、キーと値のペアの (空の可能性がある) リストを取得し、それらのペアを含む JSON オブジェクトを返します
```sql
mysql> SELECT JSON_OBJECT('key1', 1, 'key2', 'abc');
+---------------------------------------+
| JSON_OBJECT('key1', 1, 'key2', 'abc') |
+---------------------------------------+
| {"key1": 1, "key2": "abc"}            |
+---------------------------------------+
1 row in set (0.00 sec)
```
JSON_MERGE_PRESERVE() は、複数の JSON ドキュメントを取得し、結合された結果を返します
```sql
mysql> SELECT JSON_MERGE_PRESERVE('["a", 1]', '{"key": "value"}');
+-----------------------------------------------------+
| JSON_MERGE_PRESERVE('["a", 1]', '{"key": "value"}') |
+-----------------------------------------------------+
| ["a", 1, {"key": "value"}]                          |
+-----------------------------------------------------+
1 row in set (0.00 sec)

```

```sql
mysql> SET @j = JSON_OBJECT('key', 'value');
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT @j;
+------------------+
| @j               |
+------------------+
| {"key": "value"} |
+------------------+
1 row in set (0.00 sec)

```
JSON ドキュメントに引用符文字 ("または') を挿入する必要がある場合や望ましい場合があります。
```sql
mysql> CREATE TABLE facts (sentence JSON);
Query OK, 0 rows affected (0.02 sec)

mysql> INSERT INTO facts VALUES
    ->         (JSON_OBJECT("mascot", "Our mascot is a dolphin named \"Sakila\"."));
Query OK, 1 row affected (0.00 sec)
mysql> SELECT sentence FROM facts;
+---------------------------------------------------------+
| sentence                                                |
+---------------------------------------------------------+
| {"mascot": "Our mascot is a dolphin named \"Sakila\"."} |
+---------------------------------------------------------+
1 row in set (0.00 sec)
```
JSON 値の正規化、マージおよび自動ラップ
```sql
mysql> SELECT JSON_OBJECT('key1', 1, 'key2', 'abc', 'key1', 'def');
+------------------------------------------------------+
| JSON_OBJECT('key1', 1, 'key2', 'abc', 'key1', 'def') |
+------------------------------------------------------+
| {"key1": "def", "key2": "abc"}                       |
+------------------------------------------------------+
1 row in set (0.00 sec)


mysql> SELECT
    ->        JSON_MERGE_PRESERVE('[1, 2]', '["a", "b", "c"]', '[true, false]') AS Preserve,
    ->        JSON_MERGE_PATCH('[1, 2]', '["a", "b", "c"]', '[true, false]') AS Patch\G
*************************** 1. row ***************************
Preserve: [1, 2, "a", "b", "c", true, false]
   Patch: [true, false]
1 row in set (0.00 sec)
```

JSON 値の検索および変更  
JSON パス式は、JSON ドキュメント内の値を選択します。
```sql
mysql> SELECT JSON_EXTRACT('{"id": 14, "name": "Aztalan"}', '$.name');
+---------------------------------------------------------+
| JSON_EXTRACT('{"id": 14, "name": "Aztalan"}', '$.name') |
+---------------------------------------------------------+
| "Aztalan"                                               |
+---------------------------------------------------------+
1 row in set (0.00 sec)
mysql> SELECT JSON_SET('"x"', '$[0]', 'a');
+------------------------------+
| JSON_SET('"x"', '$[0]', 'a') |
+------------------------------+
| "a"                          |
+------------------------------+
1 row in set (0.00 sec)

mysql> SELECT JSON_EXTRACT('{"a": 1, "b": 2, "c": [3, 4, 5]}', '$.*');
+---------------------------------------------------------+
| JSON_EXTRACT('{"a": 1, "b": 2, "c": [3, 4, 5]}', '$.*') |
+---------------------------------------------------------+
| [1, 2, [3, 4, 5]]                                       |
+---------------------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT JSON_EXTRACT('{"a": 1, "b": 2, "c": [3, 4, 5]}', '$.c[*]');
+------------------------------------------------------------+
| JSON_EXTRACT('{"a": 1, "b": 2, "c": [3, 4, 5]}', '$.c[*]') |
+------------------------------------------------------------+
| [3, 4, 5]                                                  |
+------------------------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT JSON_EXTRACT('{"a": {"b": 1}, "c": {"b": 2}}', '$**.b');
+---------------------------------------------------------+
| JSON_EXTRACT('{"a": {"b": 1}, "c": {"b": 2}}', '$**.b') |
+---------------------------------------------------------+
| [1, 2]                                                  |
+---------------------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT JSON_EXTRACT('[1, 2, 3, 4, 5]', '$[1 to 3]');
+----------------------------------------------+
| JSON_EXTRACT('[1, 2, 3, 4, 5]', '$[1 to 3]') |
+----------------------------------------------+
| [2, 3, 4]                                    |
+----------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT JSON_EXTRACT('[1, 2, 3, 4, 5]', '$[last-3 to last-1]');
+--------------------------------------------------------+
| JSON_EXTRACT('[1, 2, 3, 4, 5]', '$[last-3 to last-1]') |
+--------------------------------------------------------+
| [2, 3, 4]                                              |
+--------------------------------------------------------+
1 row in set (0.00 sec)
```

### 11.6 データ型デフォルト値
データ型指定には、明示的または暗黙的なデフォルト値を指定できます
```sql
mysql> CREATE TABLE t1 (
    ->   i     INT DEFAULT -1,
    ->   c     VARCHAR(10) DEFAULT '',
    ->   price DOUBLE(16,2) DEFAULT 0.00
    -> );
Query OK, 0 rows affected, 1 warning (0.02 sec)

mysql> CREATE TABLE t1 (
    ->   -- literal defaults
    ->   i INT         DEFAULT 0,
    ->   c VARCHAR(10) DEFAULT '',
    ->   -- expression defaults
    ->   f FLOAT       DEFAULT (RAND() * RAND()),
    ->   b BINARY(16)  DEFAULT (UUID_TO_BIN(UUID())),
    ->   d DATE        DEFAULT (CURRENT_DATE + INTERVAL 1 YEAR),
    ->   p POINT       DEFAULT (Point(0,0)),
    ->   j JSON        DEFAULT (JSON_ARRAY())
    -> );
Query OK, 0 rows affected (0.03 sec)
```

```sql
mysql> CREATE TABLE t4 (uid BINARY(16) DEFAULT (UUID_TO_BIN(UUID())));
ERROR 1050 (42S01): Table 't4' already exists
mysql> drop table t4;
Query OK, 0 rows affected (0.01 sec)

mysql> CREATE TABLE t4 (uid BINARY(16) DEFAULT (UUID_TO_BIN(UUID())));
Query OK, 0 rows affected (0.02 sec)

mysql> INSERT INTO t4 () VALUES();
Query OK, 1 row affected (0.02 sec)

mysql> INSERT INTO t4 () VALUES(DEFAULT);
Query OK, 1 row affected (0.00 sec)

mysql> SELECT BIN_TO_UUID(uid) AS uid FROM t4;
+--------------------------------------+
| uid                                  |
+--------------------------------------+
| 5f9fd611-9bc4-11ee-91ce-e8e0b71f826a |
| 6288c89b-9bc4-11ee-91ce-e8e0b71f826a |
+--------------------------------------+
2 rows in set (0.00 sec)
```

### 10.3.3 データベース文字セットおよび照合順序
各データベースにはデータベース文字セットとデータベース照合順序があります。 
```sql
mysql> CREATE DATABASE db_name CHARACTER SET latin1 COLLATE latin1_swedish_ci;
Query OK, 1 row affected (0.01 sec)
```
特定のデータベースのデフォルト文字セットおよび照合順序を確認するには、次のステートメントを使用します。

```sql
mysql> USE db_name;
Database changed
mysql> SELECT @@character_set_database, @@collation_database;
+--------------------------+----------------------+
| @@character_set_database | @@collation_database |
+--------------------------+----------------------+
| latin1                   | latin1_swedish_ci    |
+--------------------------+----------------------+
1 row in set (0.00 sec)
```
デフォルトのデータベースを変更しないで値を表示するには、次のステートメントを使用します。
```sql
mysql> SELECT DEFAULT_CHARACTER_SET_NAME, DEFAULT_COLLATION_NAME
    -> FROM INFORMATION_SCHEMA.SCHEMATA WHERE SCHEMA_NAME = 'db_name';
+----------------------------+------------------------+
| DEFAULT_CHARACTER_SET_NAME | DEFAULT_COLLATION_NAME |
+----------------------------+------------------------+
| latin1                     | latin1_swedish_ci      |
+----------------------------+------------------------+
1 row in set (0.04 sec)
```

### 10.3.4 テーブル文字セットおよび照合順序
各テーブルにはテーブル文字セットとテーブル照合順序があります。 
```sql
mysql> CREATE TABLE t1 (c1 int)
    -> CHARACTER SET latin1 COLLATE latin1_danish_ci;
Query OK, 0 rows affected (0.04 sec)
```

### 10.3.5 カラム文字セットおよび照合順序
すべての「「文字型」」カラム (CHAR、VARCHAR、TEXT 型またはシノニム型のカラム) には、カラム文字セットおよびカラム照合順序があります。
```sql
mysql> CREATE TABLE t1
    -> (
    ->     col1 VARCHAR(5)
    ->       CHARACTER SET latin1
    ->       COLLATE latin1_german1_ci
    -> );
Query OK, 0 rows affected (0.02 sec)

mysql>
mysql> ALTER TABLE t1 MODIFY
    ->     col1 VARCHAR(5)
    ->       CHARACTER SET latin1
    ->       COLLATE latin1_swedish_ci;
Query OK, 0 rows affected (0.02 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
CHARACTER SET charset_name と COLLATE collation_name の両方が指定されている場合、文字セット charset_name と照合順序 collation_name が使用されます。
```sql
mysql> CREATE TABLE t1
    -> (
    ->     col1 CHAR(10) CHARACTER SET utf8 COLLATE utf8_unicode_ci
    -> ) CHARACTER SET latin1 COLLATE latin1_bin;
Query OK, 0 rows affected, 2 warnings (0.02 sec)
```
CHARACTER SET charset_name が COLLATE なしで指定されている場合、文字セット charset_name とそのデフォルトの照合順序が使用されます。
```sql
mysql> CREATE TABLE t1
    -> (
    ->     col1 CHAR(10) CHARACTER SET utf8
    -> ) CHARACTER SET latin1 COLLATE latin1_bin;
Query OK, 0 rows affected, 1 warning (0.03 sec)
```
COLLATE collation_name が CHARACTER SET なしで指定されている場合は、collation_name および照合 collation_name に関連付けられた文字セットが使用されます
```sql
mysql> CREATE TABLE t1
    -> (
    ->     col1 CHAR(10) COLLATE utf8_polish_ci
    -> ) CHARACTER SET latin1 COLLATE latin1_bin;
Query OK, 0 rows affected, 1 warning (0.02 sec)
```
### 10.3.6 文字列リテラルの文字セットおよび照合順序
各文字列リテラルには文字セットと照合順序があります。
```sql
mysql> SELECT 'abc';
+-----+
| abc |
+-----+
| abc |
+-----+
1 row in set (0.00 sec)

mysql> SELECT _latin1'abc';
+-----+
| abc |
+-----+
| abc |
+-----+
1 row in set (0.00 sec)

mysql> SELECT _binary'abc';
+----------+
| abc      |
+----------+
| 0x616263 |
+----------+
1 row in set (0.00 sec)

mysql> SELECT _utf8mb4'abc' COLLATE utf8mb4_danish_ci;
+-----------------------------------------+
| _utf8mb4'abc' COLLATE utf8mb4_danish_ci |
+-----------------------------------------+
| abc                                     |
+-----------------------------------------+
1 row in set (0.00 sec)
```
latin1 文字セットと latin1_german1_ci 照合順序を持つ非バイナリ文字列
```sql
mysql> SELECT _latin1'Müller' COLLATE latin1_german1_ci;
+-------------------------------------------+
| _latin1'M?ller' COLLATE latin1_german1_ci |
+-------------------------------------------+
| M?ller                                    |
+-------------------------------------------+
1 row in set (0.00 sec)
```
utf8mb4 文字セットとそのデフォルトの照合順序
```sql
mysql> SELECT _utf8mb4'Müller';
+--------+
| M?ller |
+--------+
| M?ller |
+--------+
1 row in set (0.00 sec)
```
binary 文字セットとそのデフォルト照合順序 (binary) を持つバイナリ文字列
```sql
mysql> SELECT _binary'Müller';
+----------------+
| M?ller         |
+----------------+
| 0x4D3F6C6C6572 |
+----------------+
1 row in set (0.00 sec)
```
接続のデフォルト文字セットおよび utf8mb4_general_ci 照合順序を持つ非バイナリ文字列(接続文字セットが utf8mb4 でない場合は失敗します)
```sql
mysql> SELECT 'Müller' COLLATE utf8mb4_general_ci;
ERROR 1253 (42000): COLLATION 'utf8mb4_general_ci' is not valid for CHARACTER SET 'cp932'
```
### 10.3.7 各国語文字セット
標準 SQL では、NCHAR または NATIONAL CHAR は、CHAR カラムで事前定義された文字セットを使用するように指定する方法として定義されています。
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

### 10.3.8 文字セットイントロデューサ
文字列リテラル、16 進リテラルまたはビット値リテラルには、特定の文字セットおよび照合順序を使用する文字列として指定するために、オプションの文字セットイントロデューサおよび COLLATE 句を指定できます
```sql
mysql> SELECT 'abc';
+-----+
| abc |
+-----+
| abc |
+-----+
1 row in set (0.00 sec)

mysql> SELECT _latin1'abc';
+-----+
| abc |
+-----+
| abc |
+-----+
1 row in set (0.00 sec)

mysql> SELECT _binary'abc';
+----------+
| abc      |
+----------+
| 0x616263 |
+----------+
1 row in set (0.00 sec)

mysql> SELECT _utf8mb4'abc' COLLATE utf8mb4_danish_ci;
+-----------------------------------------+
| _utf8mb4'abc' COLLATE utf8mb4_danish_ci |
+-----------------------------------------+
| abc                                     |
+-----------------------------------------+
1 row in set (0.00 sec)

mysql>
mysql> SELECT _latin1 X'4D7953514C';
+-----------------------+
| _latin1 X'4D7953514C' |
+-----------------------+
| MySQL                 |
+-----------------------+
1 row in set (0.00 sec)

mysql> SELECT _utf8mb4 0x4D7953514C COLLATE utf8mb4_danish_ci;
+-------------------------------------------------+
| _utf8mb4 0x4D7953514C COLLATE utf8mb4_danish_ci |
+-------------------------------------------------+
| MySQL                                           |
+-------------------------------------------------+
1 row in set (0.00 sec)

mysql>
mysql> SELECT _latin1 b'1000001';
+--------------------+
| _latin1 b'1000001' |
+--------------------+
| A                  |
+--------------------+
1 row in set (0.00 sec)

mysql> SELECT _utf8mb4 0b1000001 COLLATE utf8mb4_danish_ci;
+----------------------------------------------+
| _utf8mb4 0b1000001 COLLATE utf8mb4_danish_ci |
+----------------------------------------------+
| A                                            |
+----------------------------------------------+
1 row in set (0.00 sec)
```
文字列リテラルは、_binary イントロデューサを使用してバイナリ文字列として指定できます。 
```sql
mysql> SET @v1 = X'000D' | X'0BC0';
Query OK, 0 rows affected (0.00 sec)

mysql> SET @v2 = _binary X'000D' | X'0BC0';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT HEX(@v1), HEX(@v2);
+----------+----------+
| HEX(@v1) | HEX(@v2) |
+----------+----------+
| BCD      | 0BCD     |
+----------+----------+
1 row in set (0.00 sec)
```

### 10.3.9 文字セットと照合順序の割り当ての例
MySQL でどのようにしてデフォルトの文字セットおよび照合順序の値が決定されるかを、次の例で示します。

```sql
mysql> CREATE TABLE t1
    -> (
    ->     c1 CHAR(10) CHARACTER SET latin1 COLLATE latin1_german1_ci
    -> ) DEFAULT CHARACTER SET latin2 COLLATE latin2_bin;
Query OK, 0 rows affected (0.02 sec)

mysql> CREATE TABLE t1
    -> (
    ->     c1 CHAR(10)
    -> ) DEFAULT CHARACTER SET latin1 COLLATE latin1_danish_ci;
Query OK, 0 rows affected (0.02 sec)
```

### 10.4 接続文字セットおよび照合順序
結果セットまたはエラーメッセージの変換を実行しないようにサーバーに指示するには、character_set_results を NULL または binary に設定します
```sql
mysql> SET character_set_results = NULL;
Query OK, 0 rows affected (0.00 sec)

mysql> SET character_set_results = binary;
Query OK, 0 rows affected (0.00 sec)
```
現在のセッションに適用される文字セットおよび照合順序システム変数の値を確認するには、次のステートメントを使用します
```sql
mysql> SELECT * FROM performance_schema.session_variables
    -> WHERE VARIABLE_NAME IN (
    -> 'character_set_client', 'character_set_connection',
    -> 'character_set_results', 'collation_connection'
    -> ) ORDER BY VARIABLE_NAME;
+----------------------------------------------------+--------------------------------------+
| VARIABLE_NAME                                      | VARIABLE_VALUE                       |
+----------------------------------------------------+--------------------------------------+
| 0x6368617261637465725F7365745F636C69656E74         | 0x6370393332                         |
| 0x6368617261637465725F7365745F636F6E6E656374696F6E | 0x6370393332                         |
| 0x6368617261637465725F7365745F726573756C7473       | 0x62696E617279                       |
| 0x636F6C6C6174696F6E5F636F6E6E656374696F6E         | 0x63703933325F6A6170616E6573655F6369 |
+----------------------------------------------------+--------------------------------------+
4 rows in set (0.02 sec)
```

自動再接続を有効にして mysql クライアントを使用している場合は (推奨しません)、SET NAMES ではなく charset コマンドを使用することをお勧めします
```sql
mysql> charset koi8r
Charset changed
```

### 接続時のエラー処理
一部の文字セットは、クライアント文字セットとして使用できません。許可されていないクライアント文字セット を参照してください。 有効だがクライアント文字セットとして許可されていない文字セットを指定すると、サーバーはエラーを返します
```
C:\Users\masami>mysql --default-character-set=ucs2 -u root -p
ERROR 2074 (HY000): 'ucs2' character set is having more than 1 byte minimum character length, which cannot be used as a client character set. Please use any of the single byte minimum ones, e.g. utf8mb4, latin1 etc.

```

```sql
mysql> SHOW SESSION VARIABLES LIKE 'character\_set\_%';
+--------------------------+---------+
| Variable_name            | Value   |
+--------------------------+---------+
| character_set_client     | cp932   |
| character_set_connection | cp932   |
| character_set_database   | utf8mb4 |
| character_set_filesystem | binary  |
| character_set_results    | cp932   |
| character_set_server     | utf8mb4 |
| character_set_system     | utf8mb3 |
+--------------------------+---------+
7 rows in set, 1 warning (0.03 sec)

mysql> SHOW SESSION VARIABLES LIKE 'collation_connection';
+----------------------+-------------------+
| Variable_name        | Value             |
+----------------------+-------------------+
| collation_connection | cp932_japanese_ci |
+----------------------+-------------------+
1 row in set, 1 warning (0.00 sec)
```
### ランタイムエラー処理
```sql
mysql>  SET NAMES 'ucs2';
ERROR 1231 (42000): Variable 'character_set_client' can't be set to the value of 'ucs2'
mysql> SET NAMES 'bogus';
ERROR 1115 (42000): Unknown character set: 'bogus'
mysql> SET NAMES 'utf8mb4' COLLATE 'bogus';
ERROR 1273 (HY000): Unknown collation: 'bogus'
```

### 10.7 カラム文字セットの変換
特定の文字セットを使用するようにバイナリ文字列または非バイナリ文字列カラムを変換するには、ALTER TABLE を使用します。
```sql
mysql> ALTER TABLE t MODIFY b VARCHAR(50) CHARACTER SET greek;
Query OK, 3 rows affected (0.07 sec)
Records: 3  Duplicates: 0  Warnings: 0

```
元のカラムに BINARY(50) のタイプがある場合は、それを CHAR(50) に変換できますが、結果の値の末尾に 0x00 バイトが埋め込まれるため、望ましくない場合があります。 これらのバイトを削除するには、TRIM() 関数を使用します
```sql
mysql> UPDATE t SET b = TRIM(TRAILING 0x00 FROM b);
Query OK, 0 rows affected (0.01 sec)
Rows matched: 3  Changed: 0  Warnings: 0

```
テーブル t に CHAR(50) CHARACTER SET latin1 として定義された col1 という名前の非バイナリ列があるが、utf8 を使用するようにこれを変換し、多くの言語の値を格納できるようにするとします。 次のステートメントでこれを実行できます。
```sql
mysql> ALTER TABLE t MODIFY b CHAR(50) CHARACTER SET utf8;
Query OK, 3 rows affected, 1 warning (0.05 sec)
Records: 3  Duplicates: 0  Warnings: 1
```
MySQL 4.1 より前の古いテーブルがある場合、実際にはサーバーのデフォルトの文字セットと異なる文字セットでエンコードされた値が、非バイナリカラムに含まれるという特殊な状況が起こります。 たとえば、アプリケーションでは、MySQL のデフォルトの文字セットが異なる場合でも、カラムに sjis 値が格納されていることがあります。 適切な文字セットを使用するために、カラムを変換することは可能ですが、追加ステップが必要になります。 たとえば、サーバーのデフォルト文字セットが latin1 で、col1 は CHAR(50) と定義されているにもかかわらず、内容は sjis 値であるとします。 最初のステップでは、バイナリデータ型にカラムを変換することで、文字変換を実行しないで既存の文字セット情報を取り除きます。
```sql

mysql> ALTER TABLE t MODIFY b BLOB;
Query OK, 3 rows affected (0.06 sec)
Records: 3  Duplicates: 0  Warnings: 0
mysql> ALTER TABLE t MODIFY b CHAR(50) CHARACTER SET sjis;
Query OK, 3 rows affected (0.05 sec)
Records: 3  Duplicates: 0  Warnings: 0
```
### 10.8.7 INFORMATION_SCHEMA 検索での照合の使用
INFORMATION_SCHEMA テーブルの文字列カラムには utf8_general_ci の照合順序があり、大/小文字は区別されません。

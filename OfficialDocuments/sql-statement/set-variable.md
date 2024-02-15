### 13.7.6.1 変数代入の SET 構文
変数割当ての SET 構文を使用すると、サーバーまたはクライアントの操作に影響する様々なタイプの変数に値を割り当てることができます

```sql
mysql> SET @name = 43;
Query OK, 0 rows affected (0.00 sec)

mysql> SET @total_tax = (SELECT SUM(a) FROM t1);
Query OK, 0 rows affected (0.02 sec)
```

パラメータおよびローカル変数の割当て
```sql
mysql> delimiter //
mysql> CREATE PROCEDURE p(increment INT)
    -> BEGIN
    ->   DECLARE counter INT DEFAULT 0;
    ->   WHILE counter < 10 DO
    ->     -- ... do work ...
    ->     SET counter = counter + increment;
    ->   END WHILE;
    -> END;
    -> //
Query OK, 0 rows affected (0.03 sec)
mysql> call p(10);
Query OK, 0 rows affected (0.00 sec)
```
システム変数の割当て

グローバルシステム変数に値を割り当てるには、変数名の前に GLOBAL キーワードまたは@@GLOBAL. 修飾子を付けます
```sql
mysql> SET GLOBAL max_connections = 1000;
Query OK, 0 rows affected (0.00 sec)

mysql> SET @@GLOBAL.max_connections = 1000;
Query OK, 0 rows affected (0.00 sec)
```
セッションシステム変数に値を割り当てるには、変数名の前に SESSION または LOCAL キーワード、@@SESSION.、@@LOCAL. または@@修飾子、あるいはキーワードなしまたは修飾子なしを付けます
```sql
mysql> SET SESSION sql_mode = 'TRADITIONAL';
Query OK, 0 rows affected (0.00 sec)

mysql> SET LOCAL sql_mode = 'TRADITIONAL';
Query OK, 0 rows affected (0.00 sec)

mysql> SET @@SESSION.sql_mode = 'TRADITIONAL';
Query OK, 0 rows affected (0.00 sec)

mysql> SET @@LOCAL.sql_mode = 'TRADITIONAL';
Query OK, 0 rows affected (0.00 sec)

mysql> SET @@sql_mode = 'TRADITIONAL';
Query OK, 0 rows affected (0.00 sec)

mysql> SET sql_mode = 'TRADITIONAL';
Query OK, 0 rows affected (0.00 sec)
```
グローバルシステム変数をデータディレクトリ内の mysqld-auto.cnf オプションファイルに永続化するには、変数名の前に PERSIST キーワードまたは@@PERSIST. 修飾子を付けます
```Sql

mysql> SET PERSIST max_connections = 1000;
Query OK, 0 rows affected (0.01 sec)

mysql> SET @@PERSIST.max_connections = 1000;
Query OK, 0 rows affected (0.00 sec)
```
グローバル変数のランタイム値を設定せずにグローバルシステム変数を mysqld-auto.cnf ファイルに永続化するには、変数名の前に PERSIST_ONLY キーワードまたは@@PERSIST_ONLY. 修飾子を付けます
```sql
mysql> SET PERSIST_ONLY back_log = 100;
Query OK, 0 rows affected (0.00 sec)

mysql> SET @@PERSIST_ONLY.back_log = 100;
Query OK, 0 rows affected (0.00 sec)
```
グローバルシステム変数値をコンパイル済の MySQL デフォルト値に設定するか、セッションシステム変数を現在対応するグローバル値に設定するには、変数を値 DEFAULT に設定します
```Sql
mysql> SET @@SESSION.max_join_size = DEFAULT;
Query OK, 0 rows affected (0.00 sec)

mysql> SET @@SESSION.max_join_size = @@GLOBAL.max_join_size;
Query OK, 0 rows affected (0.00 sec)
```
複数変数の割当て
```Sql
mysql> SET @x = 1, SESSION sql_mode = '';
Query OK, 0 rows affected (0.00 sec)
```
式でのシステム変数参照
```Sql
mysql> SELECT @@GLOBAL.sql_mode, @@SESSION.sql_mode, @@sql_mode;
+-----------------------------------------------------------------------------------------------------------------------+--------------------+------------+
| @@GLOBAL.sql_mode
| @@SESSION.sql_mode | @@sql_mode |
+-----------------------------------------------------------------------------------------------------------------------+--------------------+------------+
| ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION |                    |            |
+-----------------------------------------------------------------------------------------------------------------------+--------------------+------------+
1 row in set (0.00 sec)
```
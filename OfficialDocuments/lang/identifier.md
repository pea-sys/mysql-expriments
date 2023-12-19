### 9.2 スキーマオブジェクト名
識別子引用符文字は逆引用符 (`) です
```sql
mysql> SELECT * FROM `actor` WHERE `actor`.actor_id < 10;
+----------+------------+--------------+---------------------+
| actor_id | first_name | last_name    | last_update         |
+----------+------------+--------------+---------------------+
|        1 | PENELOPE   | GUINESS      | 2006-02-15 04:34:33 |
|        2 | NICK       | WAHLBERG     | 2006-02-15 04:34:33 |
|        3 | ED         | CHASE        | 2006-02-15 04:34:33 |
|        4 | JENNIFER   | DAVIS        | 2006-02-15 04:34:33 |
|        5 | JOHNNY     | LOLLOBRIGIDA | 2006-02-15 04:34:33 |
|        6 | BETTE      | NICHOLSON    | 2006-02-15 04:34:33 |
|        7 | GRACE      | MOSTEL       | 2006-02-15 04:34:33 |
|        8 | MATTHEW    | JOHANSSON    | 2006-02-15 04:34:33 |
|        9 | JOE        | SWANK        | 2006-02-15 04:34:33 |
+----------+------------+--------------+---------------------+
9 rows in set (0.00 sec)
```
ANSI_QUOTES SQL モードが有効になっている場合、二重引用符内で識別子を引用符で囲むことも許可されています。
```sql
mysql> CREATE TABLE "test" (col INT);
ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near '"test" (col INT)' at line 1
mysql> SET sql_mode='ANSI_QUOTES';
Query OK, 0 rows affected (0.00 sec)

mysql> CREATE TABLE "test" (col INT);
Query OK, 0 rows affected (0.02 sec)
```
識別子が引用符で囲まれていれば、識別子引用符文字を識別子内に含めることができます。 識別子内に含める文字が識別子自体を囲むのに使用している引用符と同じ場合、文字を二重にする必要があります。 次のステートメントは、c"d という名前のカラムを含んだ a`b という名前のテーブルを作成します。
```sql
mysql> CREATE TABLE `a``b` (`c"d` INT);
Query OK, 0 rows affected (0.02 sec)
```
クエリーの選択リストで、引用したカラムエイリアスを指定するには、識別子または文字列引用文字を使用します
```sql
mysql> SELECT 1 AS `one`, 2 AS 'two';
+-----+-----+
| one | two |
+-----+-----+
|   1 |   2 |
+-----+-----+
1 row in set (0.00 sec)
```

### 9.2.2 識別子の修飾子
次のステートメントは、修飾されていない名前 t1 を使用してテーブルを作成します
```sql
mysql> CREATE TABLE t1 (i INT);
Query OK, 0 rows affected (0.02 sec)
```
次のステートメントは、修飾名 db1.t1 を使用してテーブルを作成します
```sql
mysql> CREATE TABLE sakila.t1 (i INT);
Query OK, 0 rows affected (0.02 sec)

mysql> SELECT sakila.actor.actor_id FROM sakila.actor limit 1;
+----------+
| actor_id |
+----------+
|       58 |
+----------+
1 row in set (0.00 sec)
```

### 9.4 ユーザー定義変数
あるステートメントのユーザー定義変数に値を格納し、後で別のステートメントで参照できます。
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

mysql> SET @v1 = b'1000001';
Query OK, 0 rows affected (0.00 sec)

mysql> SET @v2 = b'1000001'+0;
Query OK, 0 rows affected (0.00 sec)

mysql> SET @v3 = CAST(b'1000001' AS UNSIGNED);
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT @v1, @v2, @v3;
+------------+------+------+
| @v1        | @v2  | @v3  |
+------------+------+------+
| 0x41       |   65 |   65 |
+------------+------+------+
1 row in set (0.00 sec)
```

ユーザー変数はステートメントの一部を提供するために使用できます。
```sql
mysql> SET @c = "b";
Query OK, 0 rows affected (0.00 sec)

mysql> SET @s = CONCAT("SELECT ", @c, " FROM t");
Query OK, 0 rows affected (0.00 sec)

mysql> PREPARE stmt FROM @s;
Query OK, 0 rows affected (0.00 sec)
Statement prepared

mysql> EXECUTE stmt;
+------------+
| b          |
+------------+
| 0xFF       |
| 0x0A       |
| 0x05       |
+------------+
3 rows in set (0.00 sec)
```
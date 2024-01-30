### 13.5 プリペアドステートメント
文字列リテラルを使用してステートメントのテキストを指定することによって準備済みステートメントを作成する方法を示しています
```Sql
mysql> PREPARE stmt1 FROM 'SELECT SQRT(POW(?,2) + POW(?,2)) AS hypotenuse';
Query OK, 0 rows affected (0.01 sec)
Statement prepared

mysql> SET @a = 3;
Query OK, 0 rows affected (0.00 sec)

mysql> SET @b = 4;
Query OK, 0 rows affected (0.00 sec)

mysql> EXECUTE stmt1 USING @a, @b;
+------------+
| hypotenuse |
+------------+
|          5 |
+------------+
1 row in set (0.00 sec)

mysql> DEALLOCATE PREPARE stmt1;
Query OK, 0 rows affected (0.00 sec)
```
ステートメントのテキストをユーザー変数として指定します
```sql
mysql> SET @s = 'SELECT SQRT(POW(?,2) + POW(?,2)) AS hypotenuse';
Query OK, 0 rows affected (0.00 sec)

mysql> PREPARE stmt2 FROM @s;
Query OK, 0 rows affected (0.00 sec)
Statement prepared

mysql> SET @a = 6;
Query OK, 0 rows affected (0.00 sec)

mysql> SET @b = 8;
Query OK, 0 rows affected (0.00 sec)

mysql> EXECUTE stmt2 USING @a, @b;
+------------+
| hypotenuse |
+------------+
|         10 |
+------------+
1 row in set (0.00 sec)

mysql> DEALLOCATE PREPARE stmt2;
Query OK, 0 rows affected (0.00 sec)
```
次の追加の例は、クエリーを実行する対象となるテーブルの名前をユーザー変数として格納することによって、実行時にそのテーブルを選択する方法を示しています
```Sql
mysql> CREATE TABLE t1 (a INT NOT NULL);
Query OK, 0 rows affected (0.03 sec)

mysql> INSERT INTO t1 VALUES (4), (8), (11), (32), (80);
Query OK, 5 rows affected (0.01 sec)
Records: 5  Duplicates: 0  Warnings: 0

mysql> SET @table = 't1';
Query OK, 0 rows affected (0.00 sec)

mysql> SET @s = CONCAT('SELECT * FROM ', @table);
Query OK, 0 rows affected (0.00 sec)

mysql> PREPARE stmt3 FROM @s;
Query OK, 0 rows affected (0.00 sec)
Statement prepared

mysql> EXECUTE stmt3;
+----+
| a  |
+----+
|  4 |
|  8 |
| 11 |
| 32 |
| 80 |
+----+
5 rows in set (0.00 sec)

mysql> DEALLOCATE PREPARE stmt3;
Query OK, 0 rows affected (0.00 sec)
```
### 13.6 複合ステートメントの構文
### 13.6.2 ステートメントラベル

```sql
mysql> CREATE PROCEDURE doiterate(p1 INT)
    -> BEGIN
    ->   label1: LOOP
    ->     SET p1 = p1 + 1;
    ->     IF p1 < 10 THEN ITERATE label1; END IF;
    ->     LEAVE label1;
    ->   END LOOP label1;
    -> END;
    -> DELIMITER;
    -> ;
    -> //
Query OK, 0 rows affected (0.01 sec)
```
### 13.6.4.2 ローカル変数のスコープと解決
ローカル変数のスコープは、それが宣言されている BEGIN ... END ブロックです
```Sql
mysql> CREATE PROCEDURE sp1 (x VARCHAR(5))
    -> BEGIN
    ->   DECLARE xname VARCHAR(5) DEFAULT 'bob';
    ->   DECLARE newname VARCHAR(5);
    ->   DECLARE xid INT;
    ->
    ->   SELECT xname, id INTO newname, xid
    ->     FROM table1 WHERE xname = xname;
    ->   SELECT newname;
    -> END;//
Query OK, 0 rows affected (0.01 sec)

mysql> delimiter ;
```

```sql
mysql> delimiter //
mysql> CREATE PROCEDURE sp2 (x VARCHAR(5))
    -> BEGIN
    ->   DECLARE xname VARCHAR(5) DEFAULT 'bob';
    ->   DECLARE newname VARCHAR(5);
    ->   DECLARE xid INT;
    ->   DECLARE done TINYINT DEFAULT 0;
    ->   DECLARE cur1 CURSOR FOR SELECT xname, id FROM table1;
    ->   DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = 1;
    ->
    ->   OPEN cur1;
    ->   read_loop: LOOP
    ->     FETCH FROM cur1 INTO newname, xid;
    ->     IF done THEN LEAVE read_loop; END IF;
    ->     SELECT newname;
    ->   END LOOP;
    ->   CLOSE cur1;
    -> END;//
Query OK, 0 rows affected (0.01 sec)
```

### 13.6.5.1 CASE ステートメント

```Sql
mysql> DELIMITER |
mysql>
mysql> CREATE PROCEDURE p()
    ->   BEGIN
    ->     DECLARE v INT DEFAULT 1;
    ->
    ->     CASE v
    ->       WHEN 2 THEN SELECT v;
    ->       WHEN 3 THEN SELECT 0;
    ->       ELSE
    ->         BEGIN
    ->         END;
    ->     END CASE;
    ->   END;
    ->   |
Query OK, 0 rows affected (0.00 sec)
```

### 13.6.5.6 REPEAT ステートメント
```Sql
mysql>  delimiter //
mysql> CREATE PROCEDURE dorepeat(p1 INT)
    ->        BEGIN
    ->          SET @x = 0;
    ->          REPEAT
    ->            SET @x = @x + 1;
    ->          UNTIL @x > p1 END REPEAT;
    ->        END
    ->        //
Query OK, 0 rows affected (0.00 sec)

mysql> CALL dorepeat(1000)//
Query OK, 0 rows affected (0.02 sec)

mysql> SELECT @x//
+------+
| @x   |
+------+
| 1001 |
+------+
1 row in set (0.00 sec)

```
### 13.6.6 カーソル
```sql
mysql> delimiter //
mysql> CREATE PROCEDURE curdemo()
    -> BEGIN
    ->   DECLARE done INT DEFAULT FALSE;
    ->   DECLARE a CHAR(16);
    ->   DECLARE b, c INT;
    ->   DECLARE cur1 CURSOR FOR SELECT id,data FROM test.t1;
    ->   DECLARE cur2 CURSOR FOR SELECT i FROM test.t2;
    ->   DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = TRUE;
    ->
    ->   OPEN cur1;
    ->   OPEN cur2;
    ->
    ->   read_loop: LOOP
    ->     FETCH cur1 INTO a, b;
    ->     FETCH cur2 INTO c;
    ->     IF done THEN
    ->       LEAVE read_loop;
    ->     END IF;
    ->     IF b < c THEN
    ->       INSERT INTO test.t3 VALUES (a,b);
    ->     ELSE
    ->       INSERT INTO test.t3 VALUES (a,c);
    ->     END IF;
    ->   END LOOP;
    ->
    ->   CLOSE cur1;
    ->   CLOSE cur2;
    -> END;
    -> //
Query OK, 0 rows affected (0.01 sec)

mysql> call curdemo//
ERROR 1049 (42000): Unknown database 'test'
```

### 13.6.7.2 DECLARE ... HANDLER ステートメント
DECLARE ... HANDLER ステートメントは、1 つ以上の条件を処理するハンドラを指定します。 これらの条件のいずれかが発生した場合は、指定された statement が実行されます。
```sql
mysql> delimiter //
mysql> CREATE PROCEDURE handlerdemo ()
    ->        BEGIN
    ->          DECLARE CONTINUE HANDLER FOR SQLSTATE '23000' SET @x2 = 1;
    ->          SET @x = 1;
    ->          INSERT INTO test.t VALUES (1);
    ->          SET @x = 2;
    ->          INSERT INTO test.t VALUES (1);
    ->          SET @x = 3;
    ->        END;
    ->        //
Query OK, 0 rows affected (0.01 sec)

mysql> CALL handlerdemo()//
Query OK, 0 rows affected (0.01 sec)

mysql> SELECT @x//
+------+
| @x   |
+------+
|    3 |
+------+
1 row in set (0.00 sec)
```
### 13.6.7.7 MySQL の診断領域
次の SET ステートメントはエラーを生成するため、診断領域をクリアして移入します
```sql
mysql> DROP TABLE test.no_such_table;
ERROR 1051 (42S02): Unknown table 'test.no_such_table'
mysql> SHOW WARNINGS;
+-------+------+------------------------------------+
| Level | Code | Message                            |
+-------+------+------------------------------------+
| Error | 1051 | Unknown table 'test.no_such_table' |
+-------+------+------------------------------------+
1 row in set (0.00 sec)
```
 次のステートメントは 2 の条件番号を使用しています。これにより、診断領域にその領域をクリアすることなく追加される警告が生成されます
 ```sql
 mysql> GET DIAGNOSTICS CONDITION 2 @p = MESSAGE_TEXT;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql> SHOW WARNINGS;
+-------+------+------------------------------------+
| Level | Code | Message                            |
+-------+------+------------------------------------+
| Error | 1051 | Unknown table 'test.no_such_table' |
| Error | 1758 | Invalid condition number           |
+-------+------+------------------------------------+
2 rows in set (0.00 sec)
```
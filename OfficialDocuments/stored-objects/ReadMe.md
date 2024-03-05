### 25.1 ストアドプログラムの定義

```sql
mysql> delimiter //
mysql> CREATE PROCEDURE dorepeat(p1 INT)
    ->      BEGIN
    ->        SET @x = 0;
    ->        REPEAT SET @x = @x + 1; UNTIL @x > p1 END REPEAT;
    ->      END
    ->      //
Query OK, 0 rows affected (0.05 sec)

mysql> delimiter ;

mysql> CALL dorepeat(1000);
Query OK, 0 rows affected (0.03 sec)

mysql> SELECT @x;
+------+
| @x   |
+------+
| 1001 |
+------+
1 row in set (0.00 sec)

```

```sql
mysql> CREATE FUNCTION hello (s CHAR(20))
    -> RETURNS CHAR(50) DETERMINISTIC
    -> RETURN CONCAT('Hello, ',s,'!');
Query OK, 0 rows affected (0.01 sec)

mysql> SELECT hello('world');
+----------------+
| hello('world') |
+----------------+
| Hello, world!  |
+----------------+
1 row in set (0.00 sec)
```

### 25.3.1 トリガーの構文と例

```sql
mysql> CREATE TABLE account (acct_num INT, amount DECIMAL(10,2));
Query OK, 0 rows affected (0.05 sec)

mysql> CREATE TRIGGER ins_sum BEFORE INSERT ON account
    ->        FOR EACH ROW SET @sum = @sum + NEW.amount;
Query OK, 0 rows affected (0.02 sec)

mysql> SET @sum = 0;
Query OK, 0 rows affected (0.00 sec)

mysql> INSERT INTO account VALUES(137,14.98),(141,1937.50),(97,-100.00);
Query OK, 3 rows affected (0.01 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql> SELECT @sum AS 'Total amount inserted';
+-----------------------+
| Total amount inserted |
+-----------------------+
|               1852.48 |
+-----------------------+
1 row in set (0.00 sec)

mysql> DROP TRIGGER test.ins_sum;
Query OK, 0 rows affected (0.01 sec)
```

次のトリガー定義では、account テーブルに対して別の BEFORE INSERT トリガーを定義します

```Sql
mysql> CREATE TRIGGER ins_sum BEFORE INSERT ON account
    ->        FOR EACH ROW SET @sum = @sum + NEW.amount;
Query OK, 0 rows affected (0.04 sec)

mysql> CREATE TRIGGER ins_transaction BEFORE INSERT ON account
    ->        FOR EACH ROW PRECEDES ins_sum
    ->        SET
    ->        @deposits = @deposits + IF(NEW.amount>0,NEW.amount,0),
    ->        @withdrawals = @withdrawals + IF(NEW.amount<0,-NEW.amount,0);
Query OK, 0 rows affected (0.01 sec)
```

各行の更新に使用する新しい値をチェックし、0 から 100 の範囲に収まるように値を変更する UPDATE トリガーを定義しています。 行の更新に使用される前に値をチェックする必要があるので、これは BEFORE トリガーにする必要があります

```Sql
mysql> delimiter //
mysql> CREATE TRIGGER upd_check BEFORE UPDATE ON account
    ->        FOR EACH ROW
    ->        BEGIN
    ->            IF NEW.amount < 0 THEN
    ->                SET NEW.amount = 0;
    ->            ELSEIF NEW.amount > 100 THEN
    ->                SET NEW.amount = 100;
    ->            END IF;
    ->        END;//
Query OK, 0 rows affected (0.01 sec)

mysql> delimiter ;
```

トリガーには、名前によるテーブルへの直接の参照を含めることができます。

```Sql
mysql> CREATE TABLE test1(a1 INT);
Query OK, 0 rows affected (0.03 sec)

mysql> CREATE TABLE test2(a2 INT);
Query OK, 0 rows affected (0.02 sec)

mysql> CREATE TABLE test3(a3 INT NOT NULL AUTO_INCREMENT PRIMARY KEY);
Query OK, 0 rows affected (0.02 sec)

mysql> CREATE TABLE test4(
    ->   a4 INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    ->   b4 INT DEFAULT 0
    -> );
Query OK, 0 rows affected (0.02 sec)

mysql>
mysql> delimiter |
mysql>
mysql> CREATE TRIGGER testref BEFORE INSERT ON test1
    ->   FOR EACH ROW
    ->   BEGIN
    ->     INSERT INTO test2 SET a2 = NEW.a1;
    ->     DELETE FROM test3 WHERE a3 = NEW.a1;
    ->     UPDATE test4 SET b4 = b4 + 1 WHERE a4 = NEW.a1;
    ->   END;
    -> |
Query OK, 0 rows affected (0.01 sec)

mysql>
mysql> delimiter ;
mysql>
mysql> INSERT INTO test3 (a3) VALUES
    ->   (NULL), (NULL), (NULL), (NULL), (NULL),
    ->   (NULL), (NULL), (NULL), (NULL), (NULL);
Query OK, 10 rows affected (0.01 sec)
Records: 10  Duplicates: 0  Warnings: 0

mysql>
mysql> INSERT INTO test4 (a4) VALUES
    ->   (0), (0), (0), (0), (0), (0), (0), (0), (0), (0);
Query OK, 10 rows affected (0.00 sec)
Records: 10  Duplicates: 0  Warnings: 0

mysql> INSERT INTO test1 VALUES
    ->        (1), (3), (1), (7), (1), (8), (4), (4);
Query OK, 8 rows affected (0.02 sec)
Records: 8  Duplicates: 0  Warnings: 0

mysql> SELECT * FROM test1;
+------+
| a1   |
+------+
|    1 |
|    3 |
|    1 |
|    7 |
|    1 |
|    8 |
|    4 |
|    4 |
+------+
8 rows in set (0.00 sec)

mysql> SELECT * FROM test2;
+------+
| a2   |
+------+
|    1 |
|    3 |
|    1 |
|    7 |
|    1 |
|    8 |
|    4 |
|    4 |
+------+
8 rows in set (0.00 sec)

mysql> SELECT * FROM test3;
+----+
| a3 |
+----+
|  2 |
|  5 |
|  6 |
|  9 |
| 10 |
+----+
5 rows in set (0.00 sec)

mysql> SELECT * FROM test4;
+----+------+
| a4 | b4   |
+----+------+
|  1 |    3 |
|  2 |    0 |
|  3 |    1 |
|  4 |    2 |
|  5 |    0 |
|  6 |    0 |
|  7 |    1 |
|  8 |    1 |
|  9 |    0 |
| 10 |    0 |
+----+------+
10 rows in set (0.00 sec)
```

### 25.4.2 イベントスケジューラの構成

イベントスケジューラが ON の場合、イベントスケジューラスレッドは、デーモンプロセスとして SHOW PROCESSLIST の出力に一覧表示され、その状態は次に示すように表示されます

```sql
mysql> SHOW PROCESSLIST\G
*************************** 1. row ***************************
     Id: 5
   User: event_scheduler
   Host: localhost
     db: NULL
Command: Daemon
   Time: 10862
  State: Waiting on empty queue
   Info: NULL
*************************** 2. row ***************************
     Id: 8
   User: root
   Host: localhost
     db: test
Command: Query
   Time: 0
  State: init
   Info: SHOW PROCESSLIST
2 rows in set, 1 warning (0.00 sec)
```

mysql クライアントで次の 4 つのどのステートメントを使用しても、イベントスケジューラをオンにできます

```Sql
mysql> SET GLOBAL event_scheduler = ON;
Query OK, 0 rows affected (0.00 sec)

mysql> SET @@GLOBAL.event_scheduler = ON;
Query OK, 0 rows affected (0.00 sec)

mysql> SET GLOBAL event_scheduler = 1;
Query OK, 0 rows affected (0.00 sec)

mysql> SET @@GLOBAL.event_scheduler = 1;
Query OK, 0 rows affected (0.00 sec)
```

同様に、次の 4 つのどのステートメントを使用してもイベントスケジューラをオフにできます

```Sql
mysql> SET GLOBAL event_scheduler = OFF;
Query OK, 0 rows affected (0.00 sec)

mysql> SET @@GLOBAL.event_scheduler = OFF;
Query OK, 0 rows affected (0.00 sec)

mysql> SET GLOBAL event_scheduler = 0;
Query OK, 0 rows affected (0.00 sec)

mysql> SET @@GLOBAL.event_scheduler = 0;
Query OK, 0 rows affected (0.00 sec)

```

### 25.5 ビューの使用

### 25.5.1 ビューの構文

```Sql
mysql> CREATE TABLE t (qty INT, price INT);
Query OK, 0 rows affected (0.02 sec)

mysql> INSERT INTO t VALUES(3, 50), (5, 60);
Query OK, 2 rows affected (0.00 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql> CREATE VIEW v AS SELECT qty, price, qty*price AS value FROM t;
Query OK, 0 rows affected (0.01 sec)

mysql> SELECT * FROM v;
+------+-------+-------+
| qty  | price | value |
+------+-------+-------+
|    3 |    50 |   150 |
|    5 |    60 |   300 |
+------+-------+-------+
2 rows in set (0.00 sec)

mysql> SELECT * FROM v WHERE qty = 5;
+------+-------+-------+
| qty  | price | value |
+------+-------+-------+
|    5 |    60 |   300 |
+------+-------+-------+
1 row in set (0.00 sec)
```

### 25.5.3 更新可能および挿入可能なビュー

```sql
mysql> CREATE TABLE t1 (x INTEGER);
Query OK, 0 rows affected (0.02 sec)

mysql> CREATE TABLE t2 (c INTEGER);
Query OK, 0 rows affected (0.02 sec)

mysql> CREATE VIEW vmat AS SELECT SUM(x) AS s FROM t1;
Query OK, 0 rows affected (0.01 sec)

mysql> CREATE VIEW vup AS SELECT * FROM t2;
Query OK, 0 rows affected (0.00 sec)

mysql> CREATE VIEW vjoin AS SELECT * FROM vmat JOIN vup ON vmat.s=vup.c;
Query OK, 0 rows affected (0.01 sec)
```

結合ビューのいずれかのコンポーネントが更新不可であるため、このステートメントは無効です

```sql
mysql> INSERT INTO vjoin (c) VALUES (1);
ERROR 1471 (HY000): The target table vjoin of the INSERT is not insertable-into
```

このステートメントは有効です。ビューには実体化コンポーネントが含まれていません

```sql
mysql> INSERT INTO vup (c) VALUES (1);
Query OK, 1 row affected (0.00 sec)
```

次のステートメントは有効です。カラム c は結合ビューの更新可能な部分のものです

```sql
mysql> UPDATE vjoin SET c=c+1;
Query OK, 0 rows affected (0.01 sec)
Rows matched: 0  Changed: 0  Warnings: 0
```

このステートメントは無効です。カラム x は更新不可能な部分のものです

```sql
mysql> UPDATE vjoin SET x=x+1;
ERROR 1054 (42S22): Unknown column 'x' in 'field list'
```

### 25.5.4 WITH CHECK OPTION 句の表示

```Sql
mysql> CREATE TABLE t1 (a INT);
Query OK, 0 rows affected (0.03 sec)

mysql> CREATE VIEW v1 AS SELECT * FROM t1 WHERE a < 2
    -> WITH CHECK OPTION;
Query OK, 0 rows affected (0.01 sec)

mysql> CREATE VIEW v2 AS SELECT * FROM v1 WHERE a > 0
    -> WITH LOCAL CHECK OPTION;
Query OK, 0 rows affected (0.01 sec)

mysql> CREATE VIEW v3 AS SELECT * FROM v1 WHERE a > 0
    -> WITH CASCADED CHECK OPTION;
Query OK, 0 rows affected (0.01 sec)

mysql> INSERT INTO v2 VALUES (2);
ERROR 1369 (HY000): CHECK OPTION failed 'test.v2'
mysql> INSERT INTO v3 VALUES (2);
ERROR 1369 (HY000): CHECK OPTION failed 'test.v3'
```

### 25.6 ストアドオブジェクトのアクセス制御

p1 に対する EXECUTE 権限を持つどのユーザーでも、CALL ステートメントを使用してこれを呼び出すことができます。 ただし、p1 を実行すると、定義者セキュリティコンテキストで実行されるため、DEFINER 属性として指定されたアカウントである'admin'@'localhost'の権限で実行されます

```Sql
mysql> delimiter //
mysql> CREATE DEFINER = 'admin'@'localhost' PROCEDURE p1()
    -> SQL SECURITY DEFINER
    -> BEGIN
    ->   UPDATE t1 SET counter = counter + 1;
    -> END;
    -> //
Query OK, 0 rows affected, 1 warning (0.01 sec)

mysql> delimiter ;
```

、p2 は起動側セキュリティコンテキストで実行されるため、DEFINER 属性値に関係なく、起動側ユーザーの権限を使用します。実行者に p2 に対する EXECUTE 権限または t1 テーブルに対する UPDATE 権限がない場合、p2 は失敗します

```sql
mysql> delimiter //
mysql> CREATE DEFINER = 'admin'@'localhost' PROCEDURE p2()
    -> SQL SECURITY INVOKER
    -> BEGIN
    ->   UPDATE t1 SET counter = counter + 1;
    -> END;
    -> //
Query OK, 0 rows affected, 1 warning (0.01 sec)

mysql> delimiter ;
```

このクエリーは、DEFINER 属性を持つオブジェクトを記述する INFORMATION_SCHEMA テーブルを識別します

```Sql
mysql> SELECT TABLE_SCHEMA, TABLE_NAME FROM INFORMATION_SCHEMA.COLUMNS
    ->        WHERE COLUMN_NAME = 'DEFINER';
+--------------------+------------+
| TABLE_SCHEMA       | TABLE_NAME |
+--------------------+------------+
| information_schema | EVENTS     |
| information_schema | ROUTINES   |
| information_schema | TRIGGERS   |
| information_schema | VIEWS      |
+--------------------+------------+
4 rows in set, 2 warnings (0.03 sec)
```

## 3.1 サーバーへの接続とサーバーからの切断

* 以下でmysqlサーバーにアクセス出来る
```sql
mysql -u root -h localhost -p
Enter password: **********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 28
Server version: 8.2.0 MySQL Community Server - GPL

Copyright (c) 2000, 2023, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
```
* ログイン済みの場合はホストパラメータは省略可能
```sql
mysql -u root -p
Enter password: **********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 29
Server version: 8.2.0 MySQL Community Server - GPL

Copyright (c) 2000, 2023, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
```
* Sqlサーバーが起動していない状態でログインした場合は、次のようなエラーになる
```sql
net stop mysql82
mysql -u root -p
Enter password: **********
ERROR 2003 (HY000): Can't connect to MySQL server on 'localhost:3306' (10061)
```
* コネクションを切断する場合
```
mysql> quit
Bye
```
## 3.2 クエリーの入力
```
mysql> SELECT VERSION(), CURRENT_DATE;
+-----------+--------------+
| VERSION() | CURRENT_DATE |
+-----------+--------------+
| 8.2.0     | 2023-11-18   |
+-----------+--------------+
1 row in set (0.01 sec)
```
大文字小文字の区別はなし
```
mysql> SeLeCt vErSiOn(), current_DATE;
+-----------+--------------+
| vErSiOn() | current_DATE |
+-----------+--------------+
| 8.2.0     | 2023-11-18   |
+-----------+--------------+
1 row in set (0.00 sec)
```
計算機としても使用可能
```
mysql> SELECT SIN(PI()/4), (4+1)*5;
+--------------------+---------+
| SIN(PI()/4)        | (4+1)*5 |
+--------------------+---------+
| 0.7071067811865476 |      25 |
+--------------------+---------+
1 row in set (0.01 sec)
```
1行に複数ステートメントを含める
```
mysql> SELECT VERSION(); SELECT NOW();
+-----------+
| VERSION() |
+-----------+
| 8.2.0     |
+-----------+
1 row in set (0.00 sec)

+---------------------+
| NOW()               |
+---------------------+
| 2023-11-18 11:28:49 |
+---------------------+
1 row in set (0.00 sec)
```
複数行で1ステートメントを表現できる
```
mysql> select
    -> user()
    -> ,
    -> current_date;
+----------------+--------------+
| user()         | current_date |
+----------------+--------------+
| root@localhost | 2023-11-18   |
+----------------+--------------+
1 row in set (0.00 sec)
```

## 3.3 データベースの作成と使用
データベース存在確認
```
mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sakila             |
| sys                |
+--------------------+
5 rows in set (0.06 sec)
```
データベースの接続変更
```
mysql> use sakila
Database changed
```

アクセス権がない場合はエラーになる

```
mysql> CREATE USER masami IDENTIFIED BY '********';
Query OK, 0 rows affected (0.05 sec)
mysql> quit
Bye
mysql -u masami -p
Enter password: **********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 10
Server version: 8.2.0 MySQL Community Server - GPL

Copyright (c) 2000, 2023, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> use sakila
ERROR 1044 (42000): Access denied for user masami to database 'sakila'
```
権限を与える
```
mysql -u root -p
mysql> GRANT ALL ON sakila.* TO masami;
mysql> exit
Bye

mysql -u masami -p
Enter password: **********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 23
Server version: 8.2.0 MySQL Community Server - GPL

Copyright (c) 2000, 2023, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> use sakila
Database changed
```
ここで１つ躓いたことをメモしておく
mysqlは存在しないリレーションに権限を与えられるため誤字があってもコマンドが通る
■mysqlの場合
```
mysql> GRANT ALL ON sggggakila.* TO masami;
Query OK, 0 rows affected (0.01 sec)
```
■postgresの場合
```
postgres=# grant all on ttt to masami;
ERROR:  リレーション"ttt"は存在しません
```

## 3.3.1 データベースの作成と選択
データベース作成
```
mysql> CREATE DATABASE menagerie;
Query OK, 1 row affected (0.01 sec)
```
接続先切り替え
```
mysql> USE menagerie
Database changed
```
初回アクセス時に接続先DBを指定
```
mysql -u root -p menagerie
Enter password: **********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 26
Server version: 8.2.0 MySQL Community Server - GPL

Copyright (c) 2000, 2023, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
```

## 3.3.2 テーブルの作成
テーブル確認
```
mysql> SHOW TABLES;
Empty set (0.02 sec)
```
テーブル作成
```
CREATE TABLE pet (name VARCHAR(20), owner VARCHAR(20),
       species VARCHAR(20), sex CHAR(1), birth DATE, death DATE);
```
テーブル確認
```

mysql> SHOW TABLES;
+---------------------+
| Tables_in_menagerie |
+---------------------+
| pet                 |
+---------------------+
1 row in set (0.00 sec)
mysql> DESCRIBE pet;
+---------+-------------+------+-----+---------+-------+
| Field   | Type        | Null | Key | Default | Extra |
+---------+-------------+------+-----+---------+-------+
| name    | varchar(20) | YES  |     | NULL    |       |
| owner   | varchar(20) | YES  |     | NULL    |       |
| species | varchar(20) | YES  |     | NULL    |       |
| sex     | char(1)     | YES  |     | NULL    |       |
| birth   | date        | YES  |     | NULL    |       |
| death   | date        | YES  |     | NULL    |       |
+---------+-------------+------+-----+---------+-------+
6 rows in set (0.01 sec)
```
### 3.3.3 テーブルへのデータのロード

```
mysql> LOAD DATA LOCAL INFILE 'C:\Users\masami\source\repos\mysql-expriments\OfficialDocuments\Tutorial\pet.txt' INTO TABLE pet;
ERROR 3948 (42000): Loading local data is disabled; this must be enabled on both the client and server sides
```
クライアントとサーバーでローカルデータをロードできる設定に変えるよう怒られる
```
mysql -u root -p --local_infile=on
Enter password: **********
mysql> SET GLOBAL local_infile=on;
Query OK, 0 rows affected (0.00 sec)
```
ロードしてみる。ちなみに円マークは/に置き換える必要あり。
```
mysql> LOAD DATA LOCAL INFILE 'C:/Users/masami/source/repos/mysql-expriments/OfficialDocuments/Tutorial/pet.txt' INTO TABLE pet;
Query OK, 1 row affected, 6 warnings (0.01 sec)
Records: 1  Deleted: 0  Skipped: 0  Warnings: 6
```
挿入
```
mysql> INSERT INTO pet
    ->        VALUES ('Puffball','Diane','hamster','f','1999-03-30',NULL);
Query OK, 1 row affected (0.01 sec)

```
### 3.3.4 テーブルからの情報の取り出し
### 3.3.4.1 すべてのデータの選択
```
mysql> select * from pet;
+----------------------+-------+---------+------+------------+-------+
| name                 | owner | species | sex  | birth      | death |
+----------------------+-------+---------+------+------------+-------+
| Whistler        Gwen | NULL  | NULL    | NULL | NULL       | NULL  |
| Puffball             | Diane | hamster | f    | 1999-03-30 | NULL  |
+----------------------+-------+---------+------+------------+-------+
2 rows in set (0.00 sec)
```

テーブル削除
```
mysql> DELETE FROM pet;
mysql> LOAD DATA LOCAL INFILE 'C:/Users/masami/source/repos/mysql-expriments/OfficialDocuments/Tutorial/pet.txt' INTO TABLE pet;
Query OK, 8 rows affected, 7 warnings (0.01 sec)
Records: 8  Deleted: 0  Skipped: 0  Warnings: 7
```
テーブル更新
```
mysql> UPDATE pet SET birth = '1989-08-31' WHERE name = 'Bowser';
Query OK, 1 row affected (0.01 sec)
Rows matched: 1  Changed: 1  Warnings: 0
```

### 3.3.4.2 特定の行の選択

```
mysql> SELECT * FROM pet WHERE name = 'Bowser';
+--------+-------+---------+------+------------+------------+
| name   | owner | species | sex  | birth      | death      |
+--------+-------+---------+------+------------+------------+
| Bowser | Diane | dog     | m    | 1989-08-31 | 1995-07-29 |
+--------+-------+---------+------+------------+------------+
1 row in set (0.00 sec)

mysql> SELECT * FROM pet WHERE birth >= '1998-1-1';
+--------+-------+---------+------+------------+------------+
| name   | owner | species | sex  | birth      | death      |
+--------+-------+---------+------+------------+------------+
| Chirpy | Gwen  | bird    | f    | 1998-09-11 | 0000-00-00 |
+--------+-------+---------+------+------------+------------+
1 row in set (0.00 sec)
```
AND 検索
```
mysql> SELECT * FROM pet WHERE species = 'dog' AND sex = 'f';
+-------+--------+---------+------+------------+------------+
| name  | owner  | species | sex  | birth      | death      |
+-------+--------+---------+------+------------+------------+
| Buffy | Harold | dog     | f    | 1989-05-13 | 0000-00-00 |
+-------+--------+---------+------+------------+------------+
1 row in set (0.00 sec)
```
OR 検索
```
mysql> SELECT * FROM pet WHERE species = 'snake' OR species = 'bird';
+----------+-------+---------+------+------------+------------+
| name     | owner | species | sex  | birth      | death      |
+----------+-------+---------+------+------------+------------+
| Chirpy   | Gwen  | bird    | f    | 1998-09-11 | 0000-00-00 |
| Whistler | Gwen  | bird    | NULL | 1997-12-09 | 0000-00-00 |
| Slim     | Benny | snake   | m    | 1996-04-29 | NULL       |
+----------+-------+---------+------+------------+------------+
3 rows in set (0.00 sec)
```
AND + OR 検索
```
mysql> SELECT * FROM pet WHERE (species = 'cat' AND sex = 'm')
    ->        OR (species = 'dog' AND sex = 'f');
+-------+--------+---------+------+------------+------------+
| name  | owner  | species | sex  | birth      | death      |
+-------+--------+---------+------+------------+------------+
| Claws | Gwen   | cat     | m    | 1994-03-17 | 0000-00-00 |
| Buffy | Harold | dog     | f    | 1989-05-13 | 0000-00-00 |
+-------+--------+---------+------+------------+------------+
2 rows in set (0.00 sec)
```

### 3.3.4.3 特定のカラムの選択
```
mysql> SELECT name, birth FROM pet;
+----------------------+------------+
| name                 | birth      |
+----------------------+------------+
| Fluffy               | NULL       |
| Claws     ,Gwen    , | NULL       |
| Buffy     ,Harold  , | NULL       |
| Fang      ,Benny   , | NULL       |
| Bowser    ,Diane   , | NULL       |
| Chirpy    ,Gwen    , | NULL       |
| Whistler  ,Gwen    , | NULL       |
| Slim      ,Benny   , | NULL       |
| Puffball  ,Diane   , | NULL       |
| Fluffy               | 1993-02-04 |
| Claws                | 1994-03-17 |
| Buffy                | 1989-05-13 |
| Fang                 | 1990-08-27 |
| Bowser               | 1989-08-31 |
| Chirpy               | 1998-09-11 |
| Whistler             | 1997-12-09 |
| Slim                 | 1996-04-29 |
+----------------------+------------+
17 rows in set (0.00 sec)

mysql> SELECT DISTINCT owner FROM pet;
+----------------------+
| owner                |
+----------------------+
|   ,Harold  ,cat      |
| NULL                 |
| Harold               |
| Gwen                 |
| Benny                |
| Diane                |
+----------------------+
6 rows in set (0.00 sec)

mysql> SELECT name, species, birth FROM pet
    ->        WHERE species = 'dog' OR species = 'cat';
+--------+---------+------------+
| name   | species | birth      |
+--------+---------+------------+
| Fluffy | cat     | 1993-02-04 |
| Claws  | cat     | 1994-03-17 |
| Buffy  | dog     | 1989-05-13 |
| Fang   | dog     | 1990-08-27 |
| Bowser | dog     | 1989-08-31 |
+--------+---------+------------+
5 rows in set (0.00 sec)
```
### 3.3.4.4 行のソート
```
mysql> SELECT name, birth FROM pet ORDER BY birth;
+----------------------+------------+
| name                 | birth      |
+----------------------+------------+
| Fluffy               | NULL       |
| Claws     ,Gwen    , | NULL       |
| Buffy     ,Harold  , | NULL       |
| Fang      ,Benny   , | NULL       |
| Bowser    ,Diane   , | NULL       |
| Chirpy    ,Gwen    , | NULL       |
| Whistler  ,Gwen    , | NULL       |
| Slim      ,Benny   , | NULL       |
| Puffball  ,Diane   , | NULL       |
| Buffy                | 1989-05-13 |
| Bowser               | 1989-08-31 |
| Fang                 | 1990-08-27 |
| Fluffy               | 1993-02-04 |
| Claws                | 1994-03-17 |
| Slim                 | 1996-04-29 |
| Whistler             | 1997-12-09 |
| Chirpy               | 1998-09-11 |
+----------------------+------------+
17 rows in set (0.00 sec)

mysql> SELECT name, birth FROM pet ORDER BY birth DESC;
+----------------------+------------+
| name                 | birth      |
+----------------------+------------+
| Chirpy               | 1998-09-11 |
| Whistler             | 1997-12-09 |
| Slim                 | 1996-04-29 |
| Claws                | 1994-03-17 |
| Fluffy               | 1993-02-04 |
| Fang                 | 1990-08-27 |
| Bowser               | 1989-08-31 |
| Buffy                | 1989-05-13 |
| Fluffy               | NULL       |
| Claws     ,Gwen    , | NULL       |
| Buffy     ,Harold  , | NULL       |
| Fang      ,Benny   , | NULL       |
| Bowser    ,Diane   , | NULL       |
| Chirpy    ,Gwen    , | NULL       |
| Whistler  ,Gwen    , | NULL       |
| Slim      ,Benny   , | NULL       |
| Puffball  ,Diane   , | NULL       |
+----------------------+------------+
17 rows in set (0.00 sec)

mysql> SELECT name, species, birth FROM pet
    ->        ORDER BY species, birth DESC;
+----------------------+---------+------------+
| name                 | species | birth      |
+----------------------+---------+------------+
| Fluffy               | NULL    | NULL       |
| Claws     ,Gwen    , | NULL    | NULL       |
| Buffy     ,Harold  , | NULL    | NULL       |
| Fang      ,Benny   , | NULL    | NULL       |
| Bowser    ,Diane   , | NULL    | NULL       |
| Chirpy    ,Gwen    , | NULL    | NULL       |
| Whistler  ,Gwen    , | NULL    | NULL       |
| Slim      ,Benny   , | NULL    | NULL       |
| Puffball  ,Diane   , | NULL    | NULL       |
| Chirpy               | bird    | 1998-09-11 |
| Whistler             | bird    | 1997-12-09 |
| Claws                | cat     | 1994-03-17 |
| Fluffy               | cat     | 1993-02-04 |
| Fang                 | dog     | 1990-08-27 |
| Bowser               | dog     | 1989-08-31 |
| Buffy                | dog     | 1989-05-13 |
| Slim                 | snake   | 1996-04-29 |
+----------------------+---------+------------+
17 rows in set (0.00 sec)
```

## 3.3.4.5 日付の計算
```
mysql> SELECT name, birth, CURDATE(),
    ->        TIMESTAMPDIFF(YEAR,birth,CURDATE()) AS age
    ->        FROM pet;
+----------------------+------------+------------+------+
| name                 | birth      | CURDATE()  | age  |
+----------------------+------------+------------+------+
| Fluffy               | NULL       | 2023-11-18 | NULL |
| Claws     ,Gwen    , | NULL       | 2023-11-18 | NULL |
| Buffy     ,Harold  , | NULL       | 2023-11-18 | NULL |
| Fang      ,Benny   , | NULL       | 2023-11-18 | NULL |
| Bowser    ,Diane   , | NULL       | 2023-11-18 | NULL |
| Chirpy    ,Gwen    , | NULL       | 2023-11-18 | NULL |
| Whistler  ,Gwen    , | NULL       | 2023-11-18 | NULL |
| Slim      ,Benny   , | NULL       | 2023-11-18 | NULL |
| Puffball  ,Diane   , | NULL       | 2023-11-18 | NULL |
| Fluffy               | 1993-02-04 | 2023-11-18 |   30 |
| Claws                | 1994-03-17 | 2023-11-18 |   29 |
| Buffy                | 1989-05-13 | 2023-11-18 |   34 |
| Fang                 | 1990-08-27 | 2023-11-18 |   33 |
| Bowser               | 1989-08-31 | 2023-11-18 |   34 |
| Chirpy               | 1998-09-11 | 2023-11-18 |   25 |
| Whistler             | 1997-12-09 | 2023-11-18 |   25 |
| Slim                 | 1996-04-29 | 2023-11-18 |   27 |
+----------------------+------------+------------+------+
17 rows in set (0.00 sec)

mysql> SELECT name, birth, death,
    ->        TIMESTAMPDIFF(YEAR,birth,death) AS age
    ->        FROM pet WHERE death IS NOT NULL ORDER BY age;
+----------+------------+------------+------+
| name     | birth      | death      | age  |
+----------+------------+------------+------+
| Fluffy   | 1993-02-04 | 0000-00-00 | NULL |
| Claws    | 1994-03-17 | 0000-00-00 | NULL |
| Buffy    | 1989-05-13 | 0000-00-00 | NULL |
| Fang     | 1990-08-27 | 0000-00-00 | NULL |
| Chirpy   | 1998-09-11 | 0000-00-00 | NULL |
| Whistler | 1997-12-09 | 0000-00-00 | NULL |
| Bowser   | 1989-08-31 | 1995-07-29 |    5 |
+----------+------------+----------

mysql> SELECT name, birth FROM pet WHERE MONTH(birth) = 5;
+-------+------------+
| name  | birth      |
+-------+------------+
| Buffy | 1989-05-13 |
+-------+------------+
1 row in set (0.00 sec)

mysql> SELECT name, birth FROM pet
    ->        WHERE MONTH(birth) = MONTH(DATE_ADD(CURDATE(),INTERVAL 1 MONTH));
+----------+------------+
| name     | birth      |
+----------+------------+
| Whistler | 1997-12-09 |
+----------+------------+
1 row in set (0.00 sec)

mysql> SELECT name, birth FROM pet
    ->        WHERE MONTH(birth) = MOD(MONTH(CURDATE()), 12) + 1;
+----------+------------+
| name     | birth      |
+----------+------------+
| Whistler | 1997-12-09 |
+----------+------------+
1 row in set (0.00 sec)

mysql> SELECT '2018-10-31' + INTERVAL 1 DAY;
+-------------------------------+
| '2018-10-31' + INTERVAL 1 DAY |
+-------------------------------+
| 2018-11-01                    |
+-------------------------------+
1 row in set (0.00 sec)

mysql> SELECT '2018-10-32' + INTERVAL 1 DAY;
+-------------------------------+
| '2018-10-32' + INTERVAL 1 DAY |
+-------------------------------+
| NULL                          |
+-------------------------------+
1 row in set, 1 warning (0.00 sec)

mysql> SHOW WARNINGS;
+---------+------+----------------------------------------+
| Level   | Code | Message                                |
+---------+------+----------------------------------------+
| Warning | 1292 | Incorrect datetime value: '2018-10-32' |
+---------+------+----------------------------------------+
1 row in set (0.00 sec)
```

### 3.3.4.6 NULL 値の操作
```
mysql> SELECT 1 = NULL, 1 <> NULL, 1 < NULL, 1 > NULL;
+----------+-----------+----------+----------+
| 1 = NULL | 1 <> NULL | 1 < NULL | 1 > NULL |
+----------+-----------+----------+----------+
|     NULL |      NULL |     NULL |     NULL |
+----------+-----------+----------+----------+
1 row in set (0.00 sec)

mysql> SELECT 0 IS NULL, 0 IS NOT NULL, '' IS NULL, '' IS NOT NULL;
+-----------+---------------+------------+----------------+
| 0 IS NULL | 0 IS NOT NULL | '' IS NULL | '' IS NOT NULL |
+-----------+---------------+------------+----------------+
|         0 |             1 |          0 |              1 |
+-----------+---------------+------------+----------------+
1 row in set (0.00 sec)
```

### 3.3.4.7 パターンマッチング
```
mysql> SELECT * FROM pet WHERE name LIKE 'b%';
+----------------------+--------+---------+------+------------+------------+
| name                 | owner  | species | sex  | birth      | death      |
+----------------------+--------+---------+------+------------+------------+
| Buffy     ,Harold  , | NULL   | NULL    | NULL | NULL       | NULL       |
| Bowser    ,Diane   , | NULL   | NULL    | NULL | NULL       | NULL       |
| Buffy                | Harold | dog     | f    | 1989-05-13 | 0000-00-00 |
| Bowser               | Diane  | dog     | m    | 1989-08-31 | 1995-07-29 |
+----------------------+--------+---------+------+------------+------------+
4 rows in set (0.00 sec)

mysql> SELECT * FROM pet WHERE name LIKE '%fy';
+--------+----------------------+---------+------+------------+------------+
| name   | owner                | species | sex  | birth      | death      |
+--------+----------------------+---------+------+------------+------------+
| Fluffy |   ,Harold  ,cat      | NULL    | NULL | NULL       | NULL       |
| Fluffy | Harold               | cat     | f    | 1993-02-04 | 0000-00-00 |
| Buffy  | Harold               | dog     | f    | 1989-05-13 | 0000-00-00 |
+--------+----------------------+---------+------+------------+------------+
3 rows in set (0.00 sec)

mysql> SELECT * FROM pet WHERE name LIKE '%w%';
+----------------------+-------+---------+------+------------+------------+
| name                 | owner | species | sex  | birth      | death      |
+----------------------+-------+---------+------+------------+------------+
| Claws     ,Gwen    , | NULL  | NULL    | NULL | NULL       | NULL       |
| Bowser    ,Diane   , | NULL  | NULL    | NULL | NULL       | NULL       |
| Chirpy    ,Gwen    , | NULL  | NULL    | NULL | NULL       | NULL       |
| Whistler  ,Gwen    , | NULL  | NULL    | NULL | NULL       | NULL       |
| Claws                | Gwen  | cat     | m    | 1994-03-17 | 0000-00-00 |
| Bowser               | Diane | dog     | m    | 1989-08-31 | 1995-07-29 |
| Whistler             | Gwen  | bird    | NULL | 1997-12-09 | 0000-00-00 |
+----------------------+-------+---------+------+------------+------------+
7 rows in set (0.00 sec)

mysql> SELECT * FROM pet WHERE name LIKE '_____';
+-------+--------+---------+------+------------+------------+
| name  | owner  | species | sex  | birth      | death      |
+-------+--------+---------+------+------------+------------+
| Claws | Gwen   | cat     | m    | 1994-03-17 | 0000-00-00 |
| Buffy | Harold | dog     | f    | 1989-05-13 | 0000-00-00 |
+-------+--------+---------+------+------------+------------+
2 rows in set (0.00 sec)

mysql> SELECT * FROM pet WHERE REGEXP_LIKE(name, '^b');
+----------------------+--------+---------+------+------------+------------+
| name                 | owner  | species | sex  | birth      | death      |
+----------------------+--------+---------+------+------------+------------+
| Buffy     ,Harold  , | NULL   | NULL    | NULL | NULL       | NULL       |
| Bowser    ,Diane   , | NULL   | NULL    | NULL | NULL       | NULL       |
| Buffy                | Harold | dog     | f    | 1989-05-13 | 0000-00-00 |
| Bowser               | Diane  | dog     | m    | 1989-08-31 | 1995-07-29 |
+----------------------+--------+---------+------+------------+------------+
4 rows in set (0.02 sec)

mysql> SELECT * FROM pet WHERE REGEXP_LIKE(name, 'fy$');
+--------+----------------------+---------+------+------------+------------+
| name   | owner                | species | sex  | birth      | death      |
+--------+----------------------+---------+------+------------+------------+
| Fluffy |   ,Harold  ,cat      | NULL    | NULL | NULL       | NULL       |
| Fluffy | Harold               | cat     | f    | 1993-02-04 | 0000-00-00 |
| Buffy  | Harold               | dog     | f    | 1989-05-13 | 0000-00-00 |
+--------+----------------------+---------+------+------------+------------+
3 rows in set (0.00 sec)

mysql> SELECT * FROM pet WHERE REGEXP_LIKE(name, 'w');
+----------------------+-------+---------+------+------------+------------+
| name                 | owner | species | sex  | birth      | death      |
+----------------------+-------+---------+------+------------+------------+
| Claws     ,Gwen    , | NULL  | NULL    | NULL | NULL       | NULL       |
| Bowser    ,Diane   , | NULL  | NULL    | NULL | NULL       | NULL       |
| Chirpy    ,Gwen    , | NULL  | NULL    | NULL | NULL       | NULL       |
| Whistler  ,Gwen    , | NULL  | NULL    | NULL | NULL       | NULL       |
| Claws                | Gwen  | cat     | m    | 1994-03-17 | 0000-00-00 |
| Bowser               | Diane | dog     | m    | 1989-08-31 | 1995-07-29 |
| Whistler             | Gwen  | bird    | NULL | 1997-12-09 | 0000-00-00 |
+----------------------+-------+---------+------+------------+------------+
7 rows in set (0.00 sec)

mysql> SELECT * FROM pet WHERE REGEXP_LIKE(name, '^.....$');
+-------+--------+---------+------+------------+------------+
| name  | owner  | species | sex  | birth      | death      |
+-------+--------+---------+------+------------+------------+
| Claws | Gwen   | cat     | m    | 1994-03-17 | 0000-00-00 |
| Buffy | Harold | dog     | f    | 1989-05-13 | 0000-00-00 |
+-------+--------+---------+------+------------+------------+
2 rows in set (0.00 sec)

mysql> SELECT * FROM pet WHERE REGEXP_LIKE(name, '^.{5}$');
+-------+--------+---------+------+------------+------------+
| name  | owner  | species | sex  | birth      | death      |
+-------+--------+---------+------+------------+------------+
| Claws | Gwen   | cat     | m    | 1994-03-17 | 0000-00-00 |
| Buffy | Harold | dog     | f    | 1989-05-13 | 0000-00-00 |
+-------+--------+---------+------+------------+------------+
2 rows in set (0.00 sec)
```

### 3.3.4.8 行のカウント

```
mysql> SELECT COUNT(*) FROM pet;
+----------+
| COUNT(*) |
+----------+
|       17 |
+----------+
1 row in set (0.01 sec)

mysql> SELECT owner, COUNT(*) FROM pet GROUP BY owner;
+----------------------+----------+
| owner                | COUNT(*) |
+----------------------+----------+
|   ,Harold  ,cat      |        1 |
| NULL                 |        8 |
| Harold               |        2 |
| Gwen                 |        3 |
| Benny                |        2 |
| Diane                |        1 |
+----------------------+----------+
6 rows in set (0.00 sec)

mysql> SELECT species, COUNT(*) FROM pet GROUP BY species;
+---------+----------+
| species | COUNT(*) |
+---------+----------+
| NULL    |        9 |
| cat     |        2 |
| dog     |        3 |
| bird    |        2 |
| snake   |        1 |
+---------+----------+
5 rows in set (0.00 sec)

mysql> SELECT sex, COUNT(*) FROM pet GROUP BY sex;
+------+----------+
| sex  | COUNT(*) |
+------+----------+
| NULL |       10 |
| f    |        3 |
| m    |        4 |
+------+----------+
3 rows in set (0.00 sec)

mysql> SELECT species, sex, COUNT(*) FROM pet GROUP BY species, sex;
+---------+------+----------+
| species | sex  | COUNT(*) |
+---------+------+----------+
| NULL    | NULL |        9 |
| cat     | f    |        1 |
| cat     | m    |        1 |
| dog     | f    |        1 |
| dog     | m    |        2 |
| bird    | f    |        1 |
| bird    | NULL |        1 |
| snake   | m    |        1 |
+---------+------+----------+
8 rows in set (0.00 sec)

mysql> SELECT species, sex, COUNT(*) FROM pet
    ->        WHERE species = 'dog' OR species = 'cat'
    ->        GROUP BY species, sex;
+---------+------+----------+
| species | sex  | COUNT(*) |
+---------+------+----------+
| cat     | f    |        1 |
| cat     | m    |        1 |
| dog     | f    |        1 |
| dog     | m    |        2 |
+---------+------+----------+
4 rows in set (0.00 sec)

mysql> SELECT species, sex, COUNT(*) FROM pet
    ->        WHERE sex IS NOT NULL
    ->        GROUP BY species, sex;
+---------+------+----------+
| species | sex  | COUNT(*) |
+---------+------+----------+
| cat     | f    |        1 |
| cat     | m    |        1 |
| dog     | f    |        1 |
| dog     | m    |        2 |
| bird    | f    |        1 |
| snake   | m    |        1 |
+---------+------+----------+
6 rows in set (0.00 sec)
```
sql_mode = 'ONLY_FULL_GROUP_BY'にすると、集約されていない全選択が出来ない
```
mysql>  SET sql_mode = 'ONLY_FULL_GROUP_BY';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT owner, COUNT(*) FROM pet;
ERROR 1140 (42000): In aggregated query without GROUP BY, expression #1 of SELECT list contains nonaggregated column 'menagerie.pet.owner'; this is incompatible with sql_mode=only_full_group_by
mysql> SET sql_mode = '';
Query OK, 0 rows affected (0.00 sec)

mysql>  SELECT owner, COUNT(*) FROM pet;
+----------------------+----------+
| owner                | COUNT(*) |
+----------------------+----------+
|   ,Harold  ,cat      |       17 |
+----------------------+----------+
1 row in set (0.00 sec)
```
### 3.3.4.9 複数のテーブルの使用
```
mysql> CREATE TABLE event (name VARCHAR(20), date DATE,
    ->        type VARCHAR(15), remark VARCHAR(255));
Query OK, 0 rows affected (0.03 sec)

mysql> LOAD DATA LOCAL INFILE 'C:/Users/masami/source/repos/mysql-expriments/OfficialDocuments/Tutorial/event.txt' INTO TABLE event;
Query OK, 10 rows affected, 2 warnings (0.01 sec)
Records: 10  Deleted: 0  Skipped: 0  Warnings: 2
```

```
mysql> SELECT pet.name,
    ->        TIMESTAMPDIFF(YEAR,birth,date) AS age,
    ->        remark
    ->        FROM pet INNER JOIN event
    ->          ON pet.name = event.name
    ->        WHERE event.type = 'litter';
+--------+------+------------------------------+
| name   | age  | remark                       |
+--------+------+------------------------------+
 |Fluffy | NULL | 4 kittens, 3 female, 1 male
 |Fluffy |    2 | 4 kittens, 3 female, 1 male
         |    5 | 3 puppies, 3 female
 |Buffy  |    4 | 5 puppies, 2 female, 3 male
+--------+------+------------------------------+
4 rows in set (0.00 sec)

mysql> SELECT p1.name, p1.sex, p2.name, p2.sex, p1.species
    ->        FROM pet AS p1 INNER JOIN pet AS p2
    ->          ON p1.species = p2.species
    ->          AND p1.sex = 'f' AND p1.death IS NULL
    ->          AND p2.sex = 'm' AND p2.death IS NULL;
Empty set (0.00 sec)
```

### 3.4 データベースとテーブルに関する情報の取得
```
mysql> SELECT DATABASE();
+------------+
| DATABASE() |
+------------+
| menagerie  |
+------------+
1 row in set (0.00 sec)

mysql> SHOW TABLES;
+---------------------+
| Tables_in_menagerie |
+---------------------+
| event               |
| pet                 |
+---------------------+
2 rows in set (0.01 sec)

mysql> DESCRIBE pet;
+---------+-------------+------+-----+---------+-------+
| Field   | Type        | Null | Key | Default | Extra |
+---------+-------------+------+-----+---------+-------+
| name    | varchar(20) | YES  |     | NULL    |       |
| owner   | varchar(20) | YES  |     | NULL    |       |
| species | varchar(20) | YES  |     | NULL    |       |
| sex     | char(1)     | YES  |     | NULL    |       |
| birth   | date        | YES  |     | NULL    |       |
| death   | date        | YES  |     | NULL    |       |
+---------+-------------+------+-----+---------+-------+
6 rows in set (0.00 sec)
```

### 3.6 一般的なクエリーの例

```
mysql> CREATE TABLE shop (
    ->     article INT UNSIGNED  DEFAULT '0000' NOT NULL,
    ->     dealer  CHAR(20)      DEFAULT ''     NOT NULL,
    ->     price   DECIMAL(16,2) DEFAULT '0.00' NOT NULL,
    ->     PRIMARY KEY(article, dealer));
Query OK, 0 rows affected (0.05 sec)

mysql> INSERT INTO shop VALUES
    ->     (1,'A',3.45),(1,'B',3.99),(2,'A',10.99),(3,'B',1.45),
    ->     (3,'C',1.69),(3,'D',1.25),(4,'D',19.95);
Query OK, 7 rows affected (0.01 sec)
Records: 7  Duplicates: 0  Warnings: 0

mysql> SELECT * FROM shop ORDER BY article;
+---------+--------+-------+
| article | dealer | price |
+---------+--------+-------+
|       1 | A      |  3.45 |
|       1 | B      |  3.99 |
|       2 | A      | 10.99 |
|       3 | B      |  1.45 |
|       3 | C      |  1.69 |
|       3 | D      |  1.25 |
|       4 | D      | 19.95 |
+---------+--------+-------+
7 rows in set (0.00 sec)
```

### 3.6.1 カラムの最大値
```
mysql> SELECT MAX(article) AS article FROM shop;
+---------+
| article |
+---------+
|       4 |
+---------+
1 row in set (0.00 sec)

mysql> SELECT article, dealer, price
    -> FROM   shop
    -> WHERE  price=(SELECT MAX(price) FROM shop);
+---------+--------+-------+
| article | dealer | price |
+---------+--------+-------+
|       4 | D      | 19.95 |
+---------+--------+-------+
1 row in set (0.00 sec)

```
### 3.6.2 特定のカラムの最大値が格納されている行
```
mysql> SELECT s1.article, s1.dealer, s1.price
    -> FROM shop s1
    -> LEFT JOIN shop s2 ON s1.price < s2.price
    -> WHERE s2.article IS NULL;
+---------+--------+-------+
| article | dealer | price |
+---------+--------+-------+
|       4 | D      | 19.95 |
+---------+--------+-------+
1 row in set (0.00 sec)

mysql> SELECT article, dealer, price
    -> FROM shop
    -> ORDER BY price DESC
    -> LIMIT 1;
+---------+--------+-------+
| article | dealer | price |
+---------+--------+-------+
|       4 | D      | 19.95 |
+---------+--------+-------+
1 row in set (0.00 sec)
```

### 3.6.3 グループごとのカラムの最大値
```
mysql> SELECT article, MAX(price) AS price
    -> FROM   shop
    -> GROUP BY article
    -> ORDER BY article;
+---------+-------+
| article | price |
+---------+-------+
|       1 |  3.99 |
|       2 | 10.99 |
|       3 |  1.69 |
|       4 | 19.95 |
+---------+-------+
4 rows in set (0.00 sec)
```

### 3.6.4 特定のカラムのグループごとの最大値が格納されている行
```
mysql> SELECT article, dealer, price
    -> FROM   shop s1
    -> WHERE  price=(SELECT MAX(s2.price)
    ->               FROM shop s2
    ->               WHERE s1.article = s2.article)
    -> ORDER BY article;
+---------+--------+-------+
| article | dealer | price |
+---------+--------+-------+
|       1 | B      |  3.99 |
|       2 | A      | 10.99 |
|       3 | C      |  1.69 |
|       4 | D      | 19.95 |
+---------+--------+-------+
4 rows in set (0.00 sec)

mysql> SELECT s1.article, s1.dealer, s1.price
    -> FROM shop s1
    -> LEFT JOIN shop s2 ON s1.article = s2.article AND s1.price < s2.price
    -> WHERE s2.article IS NULL
    -> ORDER BY s1.article;
+---------+--------+-------+
| article | dealer | price |
+---------+--------+-------+
|       1 | B      |  3.99 |
|       2 | A      | 10.99 |
|       3 | C      |  1.69 |
|       4 | D      | 19.95 |
+---------+--------+-------+
4 rows in set (0.00 sec)

mysql> WITH s1 AS (
    ->    SELECT article, dealer, price,
    ->           RANK() OVER (PARTITION BY article
    ->                            ORDER BY price DESC
    ->                       ) AS `Rank`
    ->      FROM shop
    -> )
    -> SELECT article, dealer, price
    ->   FROM s1
    ->   WHERE `Rank` = 1
    -> ORDER BY article;
+---------+--------+-------+
| article | dealer | price |
+---------+--------+-------+
|       1 | B      |  3.99 |
|       2 | A      | 10.99 |
|       3 | C      |  1.69 |
|       4 | D      | 19.95 |
+---------+--------+-------+
4 rows in set (0.00 sec)

```

### 3.6.5 ユーザー定義の変数の使用
```
mysql> SELECT @min_price:=MIN(price),@max_price:=MAX(price) FROM shop;
+------------------------+------------------------+
| @min_price:=MIN(price) | @max_price:=MAX(price) |
+------------------------+------------------------+
|                   1.25 |                  19.95 |
+------------------------+------------------------+
1 row in set, 2 warnings (0.00 sec)

mysql> SELECT * FROM shop WHERE price=@min_price OR price=@max_price;
+---------+--------+-------+
| article | dealer | price |
+---------+--------+-------+
|       3 | D      |  1.25 |
|       4 | D      | 19.95 |
+---------+--------+-------+
2 rows in set (0.00 sec)
```

### 3.6.6 外部キーの使用
```
mysql> CREATE TABLE person (
    ->     id SMALLINT UNSIGNED NOT NULL AUTO_INCREMENT,
    ->     name CHAR(60) NOT NULL,
    ->     PRIMARY KEY (id)
    -> );
Query OK, 0 rows affected (0.04 sec)

mysql>
mysql> CREATE TABLE shirt (
    ->     id SMALLINT UNSIGNED NOT NULL AUTO_INCREMENT,
    ->     style ENUM('t-shirt', 'polo', 'dress') NOT NULL,
    ->     color ENUM('red', 'blue', 'orange', 'white', 'black') NOT NULL,
    ->     owner SMALLINT UNSIGNED NOT NULL REFERENCES person(id),
    ->     PRIMARY KEY (id)
    -> );
Query OK, 0 rows affected (0.02 sec)

mysql>
mysql> INSERT INTO person VALUES (NULL, 'Antonio Paz');
Query OK, 1 row affected (0.01 sec)

mysql>
mysql> SELECT @last := LAST_INSERT_ID();
+---------------------------+
| @last := LAST_INSERT_ID() |
+---------------------------+
|                         1 |
+---------------------------+
1 row in set, 1 warning (0.00 sec)

mysql> INSERT INTO shirt VALUES
    -> (NULL, 'polo', 'blue', @last),
    -> (NULL, 'dress', 'white', @last),
    -> (NULL, 't-shirt', 'blue', @last);
Query OK, 3 rows affected (0.01 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql> INSERT INTO person VALUES (NULL, 'Lilliana Angelovska');
Query OK, 1 row affected (0.00 sec)

mysql>
mysql> SELECT @last := LAST_INSERT_ID();
+---------------------------+
| @last := LAST_INSERT_ID() |
+---------------------------+
|                         2 |
+---------------------------+
1 row in set, 1 warning (0.00 sec)

mysql> INSERT INTO shirt VALUES
    -> (NULL, 'dress', 'orange', @last),
    -> (NULL, 'polo', 'red', @last),
    -> (NULL, 'dress', 'blue', @last),
    -> (NULL, 't-shirt', 'white', @last);
Query OK, 4 rows affected (0.00 sec)
Records: 4  Duplicates: 0  Warnings: 0

mysql> SELECT * FROM person;
+----+---------------------+
| id | name                |
+----+---------------------+
|  1 | Antonio Paz         |
|  2 | Lilliana Angelovska |
+----+---------------------+
2 rows in set (0.01 sec)

mysql> SELECT * FROM shirt;
+----+---------+--------+-------+
| id | style   | color  | owner |
+----+---------+--------+-------+
|  1 | polo    | blue   |     1 |
|  2 | dress   | white  |     1 |
|  3 | t-shirt | blue   |     1 |
|  4 | dress   | orange |     2 |
|  5 | polo    | red    |     2 |
|  6 | dress   | blue   |     2 |
|  7 | t-shirt | white  |     2 |
+----+---------+--------+-------+
7 rows in set (0.00 sec)

mysql> SELECT s.* FROM person p INNER JOIN shirt s
    ->    ON s.owner = p.id
    ->  WHERE p.name LIKE 'Lilliana%'
    ->    AND s.color <> 'white';
+----+-------+--------+-------+
| id | style | color  | owner |
+----+-------+--------+-------+
|  4 | dress | orange |     2 |
|  5 | polo  | red    |     2 |
|  6 | dress | blue   |     2 |
+----+-------+--------+-------+
3 rows in set (0.02 sec)
```

### 3.6.8 日ごとの訪問数の計算
```
mysql> CREATE TABLE t1 (year YEAR, month INT UNSIGNED,
    ->              day INT UNSIGNED);
Query OK, 0 rows affected (0.04 sec)

mysql> INSERT INTO t1 VALUES(2000,1,1),(2000,1,20),(2000,1,30),(2000,2,2),
    ->             (2000,2,23),(2000,2,23);
Query OK, 6 rows affected (0.01 sec)
Records: 6  Duplicates: 0  Warnings: 0

mysql> SELECT year,month,BIT_COUNT(BIT_OR(1<<day)) AS days FROM t1
    ->        GROUP BY year,month;
+------+-------+------+
| year | month | days |
+------+-------+------+
| 2000 |     1 |    3 |
| 2000 |     2 |    2 |
+------+-------+------+
2 rows in set (0.01 sec)
```

### 3.6.9 AUTO_INCREMENT の使用
```
mysql> CREATE TABLE animals (
    ->      id MEDIUMINT NOT NULL AUTO_INCREMENT,
    ->      name CHAR(30) NOT NULL,
    ->      PRIMARY KEY (id)
    -> );
Query OK, 0 rows affected (0.03 sec)

mysql>
mysql> INSERT INTO animals (name) VALUES
    ->     ('dog'),('cat'),('penguin'),
    ->     ('lax'),('whale'),('ostrich');
Query OK, 6 rows affected (0.01 sec)
Records: 6  Duplicates: 0  Warnings: 0

mysql>
mysql> SELECT * FROM animals;
+----+---------+
| id | name    |
+----+---------+
|  1 | dog     |
|  2 | cat     |
|  3 | penguin |
|  4 | lax     |
|  5 | whale   |
|  6 | ostrich |
+----+---------+
6 rows in set (0.00 sec)

```
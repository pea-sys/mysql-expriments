### 13.7.1.6 GRANT ステートメント
GRANT ステートメントを使用すると、システム管理者は、ユーザーアカウントおよびロールに付与できる権限およびロールを付与できます
```sql
mysql> GRANT ALL ON world.* TO 'jeffrey'@'localhost';
Query OK, 0 rows affected (0.01 sec)

mysql> GRANT SELECT ON sakila.t1 TO 'jeffrey'@'localhost';
Query OK, 0 rows affected (0.01 sec)

mysql> ALTER USER 'jeffrey'@'localhost' WITH MAX_QUERIES_PER_HOUR 90;
Query OK, 0 rows affected (0.01 sec)
```
グローバル権限
```sql
mysql> GRANT ALL ON *.* TO  'jeffrey'@'localhost';
Query OK, 0 rows affected (0.02 sec)

mysql> GRANT SELECT, INSERT ON *.* TO 'jeffrey'@'localhost';
Query OK, 0 rows affected (0.00 sec)
```
データベース権限
```sql
mysql> GRANT ALL ON sakila.* TO 'jeffrey'@'localhost';
Query OK, 0 rows affected (0.00 sec)

mysql> GRANT SELECT, INSERT ON sakila.* TO 'jeffrey'@'localhost';
Query OK, 0 rows affected (0.00 sec)
```
テーブル権限
```sql
mysql> GRANT ALL ON sakila.t1 TO 'jeffrey'@'localhost';
Query OK, 0 rows affected (0.00 sec)

mysql> GRANT SELECT, INSERT ON sakila.t1 TO 'jeffrey'@'localhost';
Query OK, 0 rows affected (0.00 sec)
```
カラム権限
```sql
mysql> GRANT SELECT (a), INSERT (a) ON sakila.t1 TO 'jeffrey'@'localhost';
Query OK, 0 rows affected (0.01 sec)
```
ストアドルーチン権限
```sql
mysql> GRANT CREATE ROUTINE ON sakila.* TO 'jeffrey'@'localhost';
Query OK, 0 rows affected (0.00 sec)

mysql> GRANT EXECUTE ON PROCEDURE sakila.t1 TO 'jeffrey'@'localhost';
ERROR 1305 (42000): PROCEDURE t1 does not exist
```
プロキシユーザー権限
```sql
mysql> GRANT PROXY ON  'jeffrey'@'localhost' TO 'root'@'localhost';
Query OK, 0 rows affected (0.00 sec)
```
ロールの付与
```sql
mysql> CREATE USER 'u2'@'localhost';
Query OK, 0 rows affected (0.00 sec)

mysql> GRANT SELECT, INSERT, UPDATE ON *.* TO 'u2'@'localhost';
Query OK, 0 rows affected (0.00 sec)

mysql> SHOW GRANTS FOR 'u2'@'localhost';
+---------------------------------------------------------+
| Grants for u2@localhost                                 |
+---------------------------------------------------------+
| GRANT SELECT, INSERT, UPDATE ON *.* TO `u2`@`localhost` |
+---------------------------------------------------------+
1 row in set (0.00 sec)
```
その他のアカウント特性
```sql
mysql> GRANT USAGE ON *.* TO 'u2'@'localhost' WITH GRANT OPTION;
Query OK, 0 rows affected (0.00 sec)
```
### 13.7.1.7 RENAME USER ステートメント
RENAME USER ステートメントは、既存の MySQL アカウントの名前を変更します。
```Sql
mysql> RENAME USER 'jeffrey'@'localhost' TO 'jeff'@'127.0.0.1';
Query OK, 0 rows affected (0.01 sec)
```
### 13.7.1.8 REVOKE ステートメント
REVOKE ステートメントを使用すると、システム管理者は、ユーザーアカウントおよびロールから取り消すことができる権限およびロールを取り消すことができます。
```Sql
mysql> REVOKE INSERT ON *.* FROM 'jeff'@'127.0.0.1';
Query OK, 0 rows affected (0.00 sec)
```

### 13.7.1.9 SET DEFAULT ROLE ステートメント
TO キーワードの直後に指定された user ごとに、ユーザーがサーバーに接続して認証したとき、またはユーザーがセッション中に SET ROLE DEFAULT ステートメントを実行したときにアクティブになる役割を定義します。

### 13.7.1.10 SET PASSWORD ステートメント
SET PASSWORD ステートメントは、MySQL ユーザーアカウントにパスワードを割り当てます。 
```sql
mysql> SET PASSWORD FOR 'jeff'@'127.0.0.1' = 'auth_string';
Query OK, 0 rows affected (0.01 sec)
```

### 13.7.1.11 SET ROLE ステートメント
SET ROLE では、付与されたロールのうちアクティブなロールを指定することで、現行セッション内の現行ユーザーの有効な権限が変更されます。 
```Sql
mysql> SET ROLE DEFAULT;
Query OK, 0 rows affected (0.00 sec)

mysql> SET ROLE 'role1', 'role2';
ERROR 3530 (HY000): `role1`@`%` is not granted to `root`@`localhost`
mysql> SET ROLE ALL;
Query OK, 0 rows affected (0.00 sec)

mysql> SET ROLE ALL EXCEPT 'role1', 'role2';
Query OK, 0 rows affected (0.00 sec)

mysql> create role1, role2;
ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'role1, role2' at line 1
mysql> create role role1,role2;
Query OK, 0 rows affected (0.01 sec)
```
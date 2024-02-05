### 13.7.1.1 ALTER USER ステートメント
サーバーが認証したアカウントを確認するには、CURRENT_USER() 関数を呼び出します
```Sql
mysql> SELECT CURRENT_USER();
+----------------+
| CURRENT_USER() |
+----------------+
| root@localhost |
+----------------+
1 row in set (0.00 sec)
```

アカウントに文字どおりの名前を付けずに自分のパスワードを変更できます。
```Sql
mysql> ALTER USER USER() IDENTIFIED BY 'auth_string';
Query OK, 0 rows affected (0.02 sec)
```
ユーザーコメントおよびユーザー属性は、JSON オブジェクトとして INFORMATION_SCHEMA.USER_ATTRIBUTES テーブルに格納されます。
```Sql
mysql> SELECT * FROM INFORMATION_SCHEMA.USER_ATTRIBUTES
    ->          WHERE USER='root' AND HOST='localhost';
+------+-----------+-----------+
| USER | HOST      | ATTRIBUTE |
+------+-----------+-----------+
| root | localhost | NULL      |
+------+-----------+-----------+
1 row in set (0.00 sec)

mysql> SELECT * FROM INFORMATION_SCHEMA.USER_ATTRIBUTES
    ->          WHERE USER='root' AND HOST='localhost';
+------+-----------+-----------+
| USER | HOST      | ATTRIBUTE |
+------+-----------+-----------+
| root | localhost | NULL      |
+------+-----------+-----------+
1 row in set (0.00 sec)

mysql> ALTER USER 'root'@'localhost' ATTRIBUTE '{"baz": "faz", "foo": "moo"}';
Query OK, 0 rows affected (0.01 sec)

mysql> SELECT * FROM INFORMATION_SCHEMA.USER_ATTRIBUTES
    ->          WHERE USER='root' AND HOST='localhost';
+------+-----------+------------------------------+
| USER | HOST      | ATTRIBUTE                    |
+------+-----------+------------------------------+
| root | localhost | {"baz": "faz", "foo": "moo"} |
+------+-----------+------------------------------+
1 row in set (0.00 sec)

mysql> ALTER USER 'root'@'localhost' COMMENT 'Hello';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT * FROM INFORMATION_SCHEMA.USER_ATTRIBUTES
    ->          WHERE USER='root' AND HOST='localhost';
+------+-----------+--------------------------------------------------+
| USER | HOST      | ATTRIBUTE                                        |
+------+-----------+--------------------------------------------------+
| root | localhost | {"baz": "faz", "foo": "moo", "comment": "Hello"} |
+------+-----------+--------------------------------------------------+
1 row in set (0.00 sec)

mysql> ALTER USER 'root'@'localhost' ATTRIBUTE '{"baz": null, "foo": null , "comment": null}';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT * FROM INFORMATION_SCHEMA.USER_ATTRIBUTES
    ->          WHERE USER='root' AND HOST='localhost';
+------+-----------+-----------+
| USER | HOST      | ATTRIBUTE |
+------+-----------+-----------+
| root | localhost | {}        |
+------+-----------+-----------+
1 row in set (0.00 sec)
```

```Sql
mysql> CREATE USER 'jeffrey'@'localhost' IDENTIFIED BY 'password';
Query OK, 0 rows affected (0.01 sec)

mysql> ALTER USER 'jeffrey'@'localhost'
    ->   IDENTIFIED BY 'new_password' PASSWORD EXPIRE;
Query OK, 0 rows affected (0.01 sec)

mysql> ALTER USER 'jeffrey'@'localhost'
    ->   IDENTIFIED WITH caching_sha2_password BY 'new_password'
    ->   PASSWORD EXPIRE INTERVAL 180 DAY
    ->   FAILED_LOGIN_ATTEMPTS 3 PASSWORD_LOCK_TIME 2;
Query OK, 0 rows affected (0.01 sec)
```
ロックとロック解除
```sql
mysql> ALTER USER 'jeffrey'@'localhost' ACCOUNT LOCK;
Query OK, 0 rows affected (0.00 sec)

mysql> ALTER USER 'jeffrey'@'localhost' ACCOUNT UNLOCK;
Query OK, 0 rows affected (0.00 sec)
```
SSL を使用して接続し、1 時間あたり 20 の接続を確立するためのアカウントが必要です
```sql
mysql> ALTER USER 'jeffrey'@'localhost'
    ->   REQUIRE SSL WITH MAX_CONNECTIONS_PER_HOUR 20;
Query OK, 0 rows affected (0.01 sec)
```
アカウントにプライマリパスワードのみを残して、josh のセカンダリパスワードを破棄します
```sql
mysql> ALTER USER 'jeffrey'@'localhost' DISCARD OLD PASSWORD;
Query OK, 0 rows affected (0.00 sec)
```
例: 認証プラグインをクリアテキストのパスワード値とともに指定します
```sql
mysql> ALTER USER 'jeffrey'@'localhost'
    ->   IDENTIFIED WITH mysql_native_password
    ->              BY 'password';
Query OK, 0 rows affected (0.01 sec)
```
例: 新しいプライマリパスワードを設定し、既存のパスワードをセカンダリパスワードとして保持します
```Sql
mysql> ALTER USER 'jeffrey'@'localhost'
    ->   IDENTIFIED BY 'new_password'
    ->   RETAIN CURRENT PASSWORD;
Query OK, 0 rows affected (0.01 sec)
```
例: ハッシュされたパスワード値とともに認証プラグインを指定します
```Sql
mysql> ALTER USER 'jeffrey'@'localhost'
    ->   IDENTIFIED WITH mysql_native_password
    ->              AS '*6C8989366EAF75BB670AD8EA7A7FC1176A95CEF4';
Query OK, 0 rows affected (0.00 sec)
```
ALTER USER SSL/TLS オプション
```Sql
mysql> ALTER USER 'jeffrey'@'localhost' REQUIRE NONE;
Query OK, 0 rows affected (0.00 sec)

mysql> ALTER USER 'jeffrey'@'localhost' REQUIRE SSL;
Query OK, 0 rows affected (0.00 sec)

mysql> ALTER USER 'jeffrey'@'localhost' REQUIRE X509;
Query OK, 0 rows affected (0.00 sec)

mysql> ALTER USER 'jeffrey'@'localhost'
    ->   REQUIRE ISSUER '/C=SE/ST=Stockholm/L=Stockholm/
    '>     O=MySQL/CN=CA/emailAddress=ca@example.com';
Query OK, 0 rows affected (0.00 sec)

mysql> ALTER USER 'jeffrey'@'localhost'
    ->   REQUIRE SUBJECT '/C=SE/ST=Stockholm/L=Stockholm/
    '>     O=MySQL demo client certificate/
    '>     CN=client/emailAddress=client@example.com';
Query OK, 0 rows affected (0.00 sec)

mysql> ALTER USER 'jeffrey'@'localhost'
    ->   REQUIRE CIPHER 'EDH-RSA-DES-CBC3-SHA';
Query OK, 0 rows affected (0.00 sec)
```

ALTER USER リソース制限オプション
* MAX_USER_CONNECTIONS count
```sql
mysql> ALTER USER 'jeffrey'@'localhost'
    ->   WITH MAX_QUERIES_PER_HOUR 500 MAX_UPDATES_PER_HOUR 100;
Query OK, 0 rows affected (0.00 sec)
```
ALTER USER のパスワード管理オプション

ステートメントで指定されたすべてのアカウントのパスワードをすぐに期限切れとしてマークします
```sql
mysql> ALTER USER 'jeffrey'@'localhost' PASSWORD EXPIRE;
Query OK, 0 rows affected (0.00 sec)
```
default_password_lifetime システム変数で指定されたグローバル有効期限ポリシーが適用されるように、ステートメントで指定されたすべてのアカウントを設定します
```sql
mysql> ALTER USER 'jeffrey'@'localhost' PASSWORD EXPIRE DEFAULT;
Query OK, 0 rows affected (0.00 sec)
```
ステートメントで指定されたすべてのアカウントのグローバルポリシーをオーバーライドします。
```Sql
mysql> ALTER USER 'jeffrey'@'localhost' PASSWORD EXPIRE NEVER;
Query OK, 0 rows affected (0.00 sec)

mysql> ALTER USER 'jeffrey'@'localhost' PASSWORD EXPIRE INTERVAL 180 DAY;
Query OK, 0 rows affected (0.00 sec)
```
履歴長オプション
```sql
mysql> ALTER USER 'jeffrey'@'localhost' PASSWORD HISTORY 6;
Query OK, 0 rows affected (0.00 sec)
```
password_reuse_interval システム変数で指定された日数よりも新しいパスワードの再利用が禁止されるように、アカウントで指定されたすべてのステートメントを設定します
```sql
mysql> ALTER USER 'jeffrey'@'localhost' PASSWORD REUSE INTERVAL DEFAULT;
Query OK, 0 rows affected (0.00 sec)
```
パスワードの再利用間隔を N 日に設定して、その日数より新しいパスワードの再利用を禁止します
```Sql
mysql> ALTER USER 'jeffrey'@'localhost' PASSWORD REUSE INTERVAL 360 DAY;
Query OK, 0 rows affected (0.00 sec)
```
パスワードの変更で現在のパスワードを指定させる
```sql
mysql> ALTER USER 'jeffrey'@'localhost' PASSWORD REQUIRE CURRENT;
Query OK, 0 rows affected (0.00 sec)
```
パスワードの変更で現在のパスワードを指定させない
```sql
mysql> ALTER USER 'jeffrey'@'localhost' PASSWORD REQUIRE CURRENT OPTIONAL;
Query OK, 0 rows affected (0.00 sec)
```
パスワード試行回数とアカウントロック時間の設定
```sql
mysql> ALTER USER 'jeffrey'@'localhost'
    ->   FAILED_LOGIN_ATTEMPTS 4 PASSWORD_LOCK_TIME 2;
Query OK, 0 rows affected (0.00 sec)
```
### 13.7.1.2 CREATE ROLE ステートメント
```Sql
mysql> CREATE ROLE 'administrator', 'developer';
Query OK, 0 rows affected (0.03 sec)

mysql> CREATE ROLE 'webapp'@'localhost';
Query OK, 0 rows affected (0.00 sec)
```
### 13.7.1.3 CREATE USER ステートメント
```sql
mysql> CREATE USER 'jeffrey'@'localhost' IDENTIFIED BY 'password';
Query OK, 0 rows affected (0.01 sec)
```
デフォルトの認証プラグインと指定されたパスワードを使用するアカウントを作成します。 ユーザーがサーバーへの最初の接続時に新しいパスワードを選択する必要があるように、パスワードを期限切れとしてマークします
```sql
mysql> CREATE USER 'jeffrey'@'localhost'
    ->   IDENTIFIED BY 'new_password' PASSWORD EXPIRE;
Query OK, 0 rows affected (0.01 sec)
```
caching_sha2_password 認証プラグインと指定されたパスワードを使用するアカウントを作成します。 180 日ごとに新しいパスワードを選択し、ログイン失敗トラッキングを有効にする必要があります。これにより、次のパスワードが 3連続して正しくないと、一時的なアカウントのロックが 2 日間発生します:
```sql
mysql> CREATE USER 'jeffrey'@'localhost'
    ->   IDENTIFIED WITH caching_sha2_password BY 'new_password'
    ->   PASSWORD EXPIRE INTERVAL 180 DAY
    ->   FAILED_LOGIN_ATTEMPTS 3 PASSWORD_LOCK_TIME 2;
Query OK, 0 rows affected (0.01 sec)
```
アカウントごとのプロパティとグローバルプロパティを指定して、複数のアカウントを作成します
```Sql
mysql> CREATE USER
    ->   'jeffrey'@'localhost' IDENTIFIED WITH mysql_native_password
    ->                                    BY 'new_password1',
    ->   'jeanne'@'localhost' IDENTIFIED WITH caching_sha2_password
    ->                                   BY 'new_password2'
    ->   REQUIRE X509 WITH MAX_QUERIES_PER_HOUR 60
    ->   PASSWORD HISTORY 5
    ->   ACCOUNT LOCK;
Query OK, 0 rows affected (0.02 sec)
```
* ユーザーコメント
```sql
mysql> CREATE USER 'jon'@'localhost' COMMENT 'Some information about Jon';
Query OK, 0 rows affected (0.01 sec)
```
* ユーザー属性
```Sql
mysql> CREATE USER 'jim'@'localhost'
    ->     ATTRIBUTE '{"fname": "James", "lname": "Scott", "phone": "123-456-7890"}';
Query OK, 0 rows affected (0.01 sec)
```
ユーザーコメントとユーザー属性は、INFORMATION_SCHEMA.USER_ATTRIBUTES テーブルの ATTRIBUTE カラムにまとめて格納されます。
```sql
mysql> SELECT * FROM INFORMATION_SCHEMA.USER_ATTRIBUTES
    ->         WHERE USER = 'jim' AND HOST = 'localhost'\G
*************************** 1. row ***************************
     USER: jim
     HOST: localhost
ATTRIBUTE: {"fname": "James", "lname": "Scott", "phone": "123-456-7890"}
1 row in set (0.03 sec)

mysql> SELECT * FROM INFORMATION_SCHEMA.USER_ATTRIBUTES
    ->         WHERE USER = 'jon' AND HOST = 'localhost';
+------+-----------+-------------------------------------------+
| USER | HOST      | ATTRIBUTE                                 |
+------+-----------+-------------------------------------------+
| jon  | localhost | {"comment": "Some information about Jon"} |
+------+-----------+-------------------------------------------+
1 row in set (0.00 sec)

```
ATTRIBUTE 行のコンテンツは JSON オブジェクトであるため、次に示すように、適切な MySQL JSON 関数または演算子を使用して操作できます
```Sql

mysql>  SELECT
    ->        USER AS User,
    ->        HOST AS Host,
    ->        CONCAT(ATTRIBUTE->>"$.fname"," ",ATTRIBUTE->>"$.lname") AS 'Full Name',
    ->        ATTRIBUTE->>"$.comment" AS Comment
    ->      FROM INFORMATION_SCHEMA.USER_ATTRIBUTES
    ->      WHERE USER='jon' AND HOST='localhost';
+------+-----------+-----------+----------------------------+
| User | Host      | Full Name | Comment                    |
+------+-----------+-----------+----------------------------+
| jon  | localhost | NULL      | Some information about Jon |
+------+-----------+-----------+----------------------------+
1 row in set (0.00 sec)

```
* IDENTIFIED WITH auth_plugin AS 'auth_string'

パスワードをクリアテキストで指定します。デフォルトのプラグインが使用されます

```sql
mysql> CREATE USER 'jeffrey'@'localhost'
    ->   IDENTIFIED BY 'password';
Query OK, 0 rows affected (0.01 sec)
```
 認証プラグインをクリアテキストのパスワード値とともに指定します
 ```Sql
 mysql> CREATE USER 'jeffrey'@'localhost'
    ->   IDENTIFIED WITH mysql_native_password BY 'password';
Query OK, 0 rows affected (0.01 sec)
```
CREATE USER ロールのオプション
```sql
mysql> CREATE USER 'joe'@'10.0.0.1' DEFAULT ROLE administrator, developer;
Query OK, 0 rows affected (0.00 sec)
```
CREATE USER SSL/TLS オプション
* NONE
```sql
mysql> CREATE USER 'jeffrey'@'localhost' REQUIRE NONE;
Query OK, 0 rows affected (0.01 sec)
```
* SSL
```sql
mysql> CREATE USER 'jeffrey'@'localhost' REQUIRE SSL;
Query OK, 0 rows affected (0.00 sec)
```
* X509
```Sql
mysql> CREATE USER 'jeffrey'@'localhost' REQUIRE X509;
Query OK, 0 rows affected (0.00 sec)
```
* ISSUER 'issuer'
```sql
mysql> CREATE USER 'jeffrey'@'localhost'
    ->   REQUIRE ISSUER '/C=SE/ST=Stockholm/L=Stockholm/
    '>     O=MySQL/CN=CA/emailAddress=ca@example.com';
Query OK, 0 rows affected (0.00 sec)
```
* SUBJECT 'subject'
```sql
mysql> CREATE USER 'jeffrey'@'localhost'
    ->   REQUIRE SUBJECT '/C=SE/ST=Stockholm/L=Stockholm/
    '>     O=MySQL demo client certificate/
    '>     CN=client/emailAddress=client@example.com';
Query OK, 0 rows affected (0.00 sec)
```
* CIPHER 'cipher'
```Sql
mysql> CREATE USER 'jeffrey'@'localhost'
    ->   REQUIRE CIPHER 'EDH-RSA-DES-CBC3-SHA';
Query OK, 0 rows affected (0.00 sec)
```
CREATE USER リソース制限オプション

* MAX_USER_CONNECTIONS count
```Sql
mysql> CREATE USER 'jeffrey'@'localhost'
    ->   WITH MAX_QUERIES_PER_HOUR 500 MAX_UPDATES_PER_HOUR 100;
Query OK, 0 rows affected (0.01 sec)
```
CREATE USER のパスワード管理オプション
* PASSWORD EXPIRE
```sql
mysql> CREATE USER 'jeffrey'@'localhost' PASSWORD EXPIRE;
Query OK, 0 rows affected (0.00 sec)
```

* PASSWORD EXPIRE DEFAULT
```sql
mysql> CREATE USER 'jeffrey'@'localhost' PASSWORD EXPIRE DEFAULT;
Query OK, 0 rows affected (0.00 sec)
```
* PASSWORD EXPIRE NEVER
```sql
mysql> CREATE USER 'jeffrey'@'localhost' PASSWORD EXPIRE NEVER;
Query OK, 0 rows affected (0.00 sec)
```
* PASSWORD EXPIRE INTERVAL N DAY
```sql
mysql> CREATE USER 'jeffrey'@'localhost' PASSWORD EXPIRE INTERVAL 180 DAY;
Query OK, 0 rows affected (0.00 sec)
```
* PASSWORD HISTORY DEFAULT
```sql
mysql> CREATE USER 'jeffrey'@'localhost' PASSWORD HISTORY DEFAULT;
Query OK, 0 rows affected (0.00 sec)
```
* PASSWORD HISTORY N
```sql
mysql> CREATE USER 'jeffrey'@'localhost' PASSWORD HISTORY 6;
Query OK, 0 rows affected (0.00 sec)
```
* PASSWORD REUSE INTERVAL DEFAULT
```sql
mysql> CREATE USER 'jeffrey'@'localhost' PASSWORD REUSE INTERVAL DEFAULT;
Query OK, 0 rows affected (0.00 sec)
```
* PASSWORD REUSE INTERVAL N DAY
```sql
mysql> CREATE USER 'jeffrey'@'localhost' PASSWORD REUSE INTERVAL 360 DAY;
Query OK, 0 rows affected (0.00 sec)
```
* PASSWORD REQUIRE CURRENT
```Sql
mysql> CREATE USER 'jeffrey'@'localhost' PASSWORD REQUIRE CURRENT;
Query OK, 0 rows affected (0.00 sec)
```
* PASSWORD REQUIRE CURRENT OPTIONAL
```sql
mysql> CREATE USER 'jeffrey'@'localhost' PASSWORD REQUIRE CURRENT OPTIONAL;
Query OK, 0 rows affected (0.00 sec)
```
* PASSWORD REQUIRE CURRENT DEFAULT
```sql
mysql> CREATE USER 'jeffrey'@'localhost' PASSWORD REQUIRE CURRENT DEFAULT;
Query OK, 0 rows affected (0.00 sec)
```
* PASSWORD_LOCK_TIME {N | UNBOUNDED}
```sql
mysql> CREATE USER 'jeffrey'@'localhost'
    ->   FAILED_LOGIN_ATTEMPTS 4 PASSWORD_LOCK_TIME 2;
Query OK, 0 rows affected (0.01 sec)
```
## 6.2.3 付与テーブル

アカウントに付与された権限を調べるには、次のステートメントを使用します。
```
mysql> SHOW GRANTS FOR root@localhost\G
*************************** 1. row ***************************
Grants for root@localhost: GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, SHUTDOWN, PROCESS, FILE, REFERENCES, INDEX, ALTER, SHOW DATABASES, SUPER, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER, CREATE TABLESPACE, CREATE ROLE, DROP ROLE ON *.* TO `root`@`localhost` WITH GRANT OPTION
*************************** 2. row ***************************
Grants for root@localhost: GRANT ALLOW_NONEXISTENT_DEFINER,APPLICATION_PASSWORD_ADMIN,AUDIT_ABORT_EXEMPT,AUDIT_ADMIN,AUTHENTICATION_POLICY_ADMIN,BACKUP_ADMIN,BINLOG_ADMIN,BINLOG_ENCRYPTION_ADMIN,CLONE_ADMIN,CONNECTION_ADMIN,ENCRYPTION_KEY_ADMIN,FIREWALL_EXEMPT,FLUSH_OPTIMIZER_COSTS,FLUSH_STATUS,FLUSH_TABLES,FLUSH_USER_RESOURCES,GROUP_REPLICATION_ADMIN,GROUP_REPLICATION_STREAM,INNODB_REDO_LOG_ARCHIVE,INNODB_REDO_LOG_ENABLE,PASSWORDLESS_USER_ADMIN,PERSIST_RO_VARIABLES_ADMIN,REPLICATION_APPLIER,REPLICATION_SLAVE_ADMIN,RESOURCE_GROUP_ADMIN,RESOURCE_GROUP_USER,ROLE_ADMIN,SENSITIVE_VARIABLES_OBSERVER,SERVICE_CONNECTION_ADMIN,SESSION_VARIABLES_ADMIN,SET_ANY_DEFINER,SHOW_ROUTINE,SYSTEM_USER,SYSTEM_VARIABLES_ADMIN,TABLE_ENCRYPTION_ADMIN,TELEMETRY_LOG_ADMIN,XA_RECOVER_ADMIN ON *.* TO `root`@`localhost` WITH GRANT OPTION
*************************** 3. row ***************************
Grants for root@localhost: GRANT PROXY ON ``@`` TO `root`@`localhost` WITH GRANT OPTION
3 rows in set (0.00 sec)
```
アカウントの非特権プロパティーを表示する
```
mysql> SHOW CREATE USER newuser@localhost\G
*************************** 1. row ***************************
CREATE USER for newuser@localhost: CREATE USER `newuser`@`localhost` IDENTIFIED WITH 'caching_sha2_password' REQUIRE NONE PASSWORD EXPIRE DEFAULT ACCOUNT UNLOCK PASSWORD HISTORY DEFAULT PASSWORD REUSE INTERVAL DEFAULT PASSWORD REQUIRE CURRENT DEFAULT
1 row in set (0.00 sec)
```
## 6.2.4 アカウント名の指定
* ユーザー名およびホスト名の部分が引用符で囲まれる場合、別々に囲んでください。 つまり、'me@localhost'ではなく'me'@'localhost'と記述します。 後者は、実際には'me@localhost'@'%'と同等です。

IPv4 アドレスとして指定されたホスト値の場合、ネットマスクを指定して、ネットワーク番号に使用するアドレスビット数を示すことができます。 ネットマスク表記は IPv6 アドレスについては使用できません。
```
mysql> CREATE USER 'david'@'198.51.100.0/255.255.255.0';
Query OK, 0 rows affected (0.01 sec)
```

## 6.2.5 ロール名の指定
ロール名は、次の点でアカウント名と異なります:

* ロール名のユーザー部分は空白にできません。 したがって、「「匿名ユーザー」」の概念に類似した「「匿名ロール」」はありません。

* アカウント名の場合、ロール名のホスト部分を省略すると、'%'のホスト部分になります。 ただし、アカウント名の'%'とは異なり、ロール名の'%'のホスト部分にはワイルドカードプロパティはありません。 たとえば、ロール名として使用される名前'me'@'%'の場合、ホスト部分 ('%') はリテラル値であり、「「任意のホスト」」一致プロパティはありません。

* ロール名のホスト部分のネットマスク表記に意味はありません。

* アカウント名は、複数のコンテキストで CURRENT_USER() にすることができます。 ロール名がではありません。

## 6.2.6 アクセス制御、ステージ 1: 接続の検証
このようなユーザーを作成すると、誰でも接続可能
```
mysql> CREATE USER ''@'%';
Query OK, 0 rows affected (0.00 sec)
```
```
C:\Users\masami>mysql -u aiueo -p
mysql> SELECT CURRENT_USER();
+----------------+
| CURRENT_USER() |
+----------------+
| @%             |
+----------------+
1 row in set (0.00 sec)
```
IPv4 アドレス . . . . . . . . . . . .: 192.168.0.123  
サブネット マスク . . . . . . . . . .: 255.255.255.0  
から匿名ユーザーのログインを許す場合は
```
mysql> CREATE USER ''@'192.168.0.%/255.255.255.0';
Query OK, 0 rows affected (0.01 sec)
```   

## 6.2.7 アクセス制御、ステージ 2: リクエストの確認
サーバーは接続を受け入れると、アクセス制御のステージ 2 に入ります。 接続を介して発行するリクエストごとに、実行する操作がサーバーによって決定され、権限が十分かどうかがチェックされます。

## 6.2.8 アカウントの追加、権限の割当ておよびアカウントの削除
4つのアカウントを登録
```
mysql> CREATE USER 'finley'@'localhost'
    ->   IDENTIFIED BY 'password';
Query OK, 0 rows affected (0.03 sec)

mysql> GRANT ALL
    ->   ON *.*
    ->   TO 'finley'@'localhost'
    ->   WITH GRANT OPTION;
Query OK, 0 rows affected (0.01 sec)

mysql>
mysql> CREATE USER 'finley'@'%.example.com'
    ->   IDENTIFIED BY 'password';
Query OK, 0 rows affected (0.01 sec)

mysql> GRANT ALL
    ->   ON *.*
    ->   TO 'finley'@'%.example.com'
    ->   WITH GRANT OPTION;
Query OK, 0 rows affected (0.01 sec)

mysql>
mysql> CREATE USER 'admin'@'localhost'
    ->   IDENTIFIED BY 'password';
Query OK, 0 rows affected (0.01 sec)

mysql> GRANT RELOAD,PROCESS
    ->   ON *.*
    ->   TO 'admin'@'localhost';
Query OK, 0 rows affected (0.00 sec)

mysql>
mysql> CREATE USER 'dummy'@'localhost';
Query OK, 0 rows affected (0.00 sec)
```
これらのステートメントによって作成されるアカウントには、次のプロパティがあります:

* 2 つのアカウントのユーザー名は finley です。 どちらも、すべてを実行するための完全なグローバル権限を持つスーパーユーザーアカウントです。 'finley'@'localhost'アカウントは、ローカルホストから接続する場合にのみ使用できます。 'finley'@'%.example.com'アカウントは、ホスト部分で'%'ワイルドカードを使用するため、example.com ドメイン内の任意のホストからの接続に使用できます。

* 'finley'@'localhost'アカウントは、localhost の匿名ユーザーアカウントがある場合に必要です。 'finley'@'localhost'アカウントがない場合、その匿名ユーザーアカウントは、finley がローカルホストから接続し、finley が匿名ユーザーとして処理されるときに優先されます。 これは、匿名ユーザーアカウントには'finley'@'%'アカウントよりも具体的な Host カラム値があるため、user テーブルのソート順が早いためです。 (user テーブルのソートの詳細は、セクション6.2.6「アクセス制御、ステージ 1: 接続の検証」 を参照してください。)

* 'admin'@'localhost'アカウントは、ローカルホストから接続するために admin でのみ使用できます。 RELOAD および PROCESS のグローバル管理権限が付与されます。 これらの権限を持つ admin ユーザーは、mysqladmin reload、mysqladmin refresh、mysqladmin flush-xxx コマンド、および mysqladmin processlist を実行できます。 任意のデータベースにアクセスするための権限は付与されません。 このような権限は、GRANT ステートメントを使用して追加できます。

* 'dummy'@'localhost'アカウントにパスワードがありません (セキュアでないため、お薦めしません)。 このアカウントは、ローカルホストから接続する際にのみ使用できます。 権限は付与されません。 GRANT ステートメントを使用してアカウントに特定の権限を付与することを前提としています。

 次の例では、3 つのアカウントを作成し、下位レベル、つまりデータベース内の特定のデータベースまたはオブジェクトへのアクセス権を付与します。
 ```
 mysql> CREATE USER 'custom'@'localhost'
    ->   IDENTIFIED BY 'password';
Query OK, 0 rows affected (0.02 sec)

mysql> GRANT ALL
    ->   ON bankaccount.*
    ->   TO 'custom'@'localhost';
Query OK, 0 rows affected (0.00 sec)

mysql>
mysql> CREATE USER 'custom'@'host47.example.com'
    ->   IDENTIFIED BY 'password';
Query OK, 0 rows affected (0.01 sec)

mysql> GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP
    ->   ON expenses.*
    ->   TO 'custom'@'host47.example.com';
Query OK, 0 rows affected (0.00 sec)

mysql>
mysql> CREATE USER 'custom'@'%.example.com'
    ->   IDENTIFIED BY 'password';
Query OK, 0 rows affected (0.01 sec)

mysql> GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP
    ->   ON customer.addresses
    ->   TO 'custom'@'%.example.com';
Query OK, 0 rows affected (0.01 sec)
```
3 つのアカウントは、次のように使用できます。

* 'custom'@'localhost'アカウントには、bankaccount データベースにアクセスするためのすべてのデータベースレベルの権限があります。 アカウントを使用してサーバーに接続できるのは、ローカルホストからのみです。

* 'custom'@'host47.example.com'アカウントには、expenses データベースにアクセスするための特定のデータベースレベル権限があります。 このアカウントは、ホスト host47.example.com からのみサーバーに接続するために使用できます。

* 'custom'@'%.example.com'アカウントには、example.com ドメイン内の任意のホストから customer データベース内の addresses テーブルにアクセスするための特定のテーブルレベルの権限があります。 アカウント名のホスト部分に % ワイルドカード文字が使用されているため、アカウントを使用してドメイン内のすべてのマシンからサーバーに接続できます。

権限確認
```
mysql> SHOW GRANTS FOR 'admin'@'localhost';
+-----------------------------------------------------+
| Grants for admin@localhost                          |
+-----------------------------------------------------+
| GRANT RELOAD, PROCESS ON *.* TO `admin`@`localhost` |
+-----------------------------------------------------+
1 row in set (0.00 sec)
```
アカウントの非特権プロパティーを表示

```
mysql> SET print_identified_with_as_hex = ON;
Query OK, 0 rows affected (0.00 sec)

mysql> SHOW CREATE USER 'admin'@'localhost'\G
*************************** 1. row ***************************
CREATE USER for admin@localhost: CREATE USER `admin`@`localhost` IDENTIFIED WITH 'caching_sha2_password' AS 0x244124303035242038133C277C1A7638414863586057206B47695F7456556B656455443264626D447738436A714249384D396857326C6D5A4F68447776734F61657639617A42 REQUIRE NONE PASSWORD EXPIRE DEFAULT ACCOUNT UNLOCK PASSWORD HISTORY DEFAULT PASSWORD REUSE INTERVAL DEFAULT PASSWORD REQUIRE CURRENT DEFAULT
1 row in set (0.00 sec)
```
権限取り消し
```
mysql> REVOKE ALL
    ->   ON *.*
    ->   FROM 'finley'@'%.example.com';
Query OK, 0 rows affected (0.01 sec)

mysql>
mysql> REVOKE RELOAD
    ->   ON *.*
    ->   FROM 'admin'@'localhost';
Query OK, 0 rows affected (0.00 sec)
```
データベースレベルの権限取り消し
```
mysql> REVOKE CREATE,DROP
    ->   ON expenses.*
    ->   FROM 'custom'@'host47.example.com';
Query OK, 0 rows affected (0.00 sec)
```
テーブルレベルの権限取り消し
```

mysql> REVOKE INSERT,UPDATE,DELETE
    ->   ON customer.addresses
    ->   FROM 'custom'@'%.example.com';
Query OK, 0 rows affected (0.00 sec)
```
アカウントの削除
```
mysql> DROP USER 'finley'@'localhost';
Query OK, 0 rows affected (0.01 sec)

mysql> DROP USER 'finley'@'%.example.com';
Query OK, 0 rows affected (0.00 sec)

mysql> DROP USER 'admin'@'localhost';
Query OK, 0 rows affected (0.00 sec)

mysql> DROP USER 'dummy'@'localhost';
Query OK, 0 rows affected (0.00 sec)
```

## 6.2.10 ロールの使用
MySQL ロールは、権限の名前付きコレクションです。 ユーザーアカウントと同様に、ロールには付与された権限と取り消された権限があります。

次のシナリオを考えてみます:

* アプリケーションは、app_db という名前のデータベースを使用します。

* アプリケーションに関連付けられている場合、アプリケーションを作成および保守する開発者、およびアプリケーションと対話するユーザーのアカウントが存在する可能性があります。

* 開発者には、データベースへの完全なアクセス権が必要です。 一部のユーザーには読取りアクセスのみが必要で、その他のユーザーには読取り/書込みアクセスが必要です。

ロール作成
```
mysql> CREATE ROLE 'app_developer', 'app_read', 'app_write';
Query OK, 0 rows affected (0.01 sec)
```
ロールに権限を割り当て
```
mysql> GRANT ALL ON app_db.* TO 'app_developer';
Query OK, 0 rows affected (0.01 sec)

mysql> GRANT SELECT ON app_db.* TO 'app_read';
Query OK, 0 rows affected (0.00 sec)

mysql> GRANT INSERT, UPDATE, DELETE ON app_db.* TO 'app_write';
Query OK, 0 rows affected (0.00 sec)
```
アカウント作成
```
mysql> CREATE USER 'dev1'@'localhost' IDENTIFIED BY 'dev1pass';
Query OK, 0 rows affected (0.02 sec)

mysql> CREATE USER 'read_user1'@'localhost' IDENTIFIED BY 'read_user1pass';
Query OK, 0 rows affected (0.01 sec)

mysql> CREATE USER 'read_user2'@'localhost' IDENTIFIED BY 'read_user2pass';
Query OK, 0 rows affected (0.01 sec)

mysql> CREATE USER 'rw_user1'@'localhost' IDENTIFIED BY 'rw_user1pass';
Query OK, 0 rows affected (0.02 sec)
```
ロールに権限付与
```

mysql> GRANT 'app_developer' TO 'dev1'@'localhost';
Query OK, 0 rows affected (0.01 sec)

mysql> GRANT 'app_read' TO 'read_user1'@'localhost', 'read_user2'@'localhost';
Query OK, 0 rows affected (0.00 sec)

mysql> GRANT 'app_read', 'app_write' TO 'rw_user1'@'localhost';
Query OK, 0 rows affected (0.00 sec)
```

必須ロールの定義  
mandatory_roles システム変数の値でロールに名前を付けることで、ロールを必須として指定できます。サーバーは必須ロールをすべてのユーザーに付与されたものとして処理するため、アカウントに明示的に付与する必要はありません。

ロール権限の確認  
アカウントに割り当てられた権限
```
mysql>  SHOW GRANTS FOR 'dev1'@'localhost';
+-------------------------------------------------+
| Grants for dev1@localhost                       |
+-------------------------------------------------+
| GRANT USAGE ON *.* TO `dev1`@`localhost`        |
| GRANT `app_developer`@`%` TO `dev1`@`localhost` |
+-------------------------------------------------+
2 rows in set (0.00 sec)

mysql> SHOW GRANTS FOR 'dev1'@'localhost' USING 'app_developer';
+----------------------------------------------------------+
| Grants for dev1@localhost                                |
+----------------------------------------------------------+
| GRANT USAGE ON *.* TO `dev1`@`localhost`                 |
| GRANT ALL PRIVILEGES ON `app_db`.* TO `dev1`@`localhost` |
| GRANT `app_developer`@`%` TO `dev1`@`localhost`          |
+----------------------------------------------------------+
3 rows in set (0.00 sec)
mysql> SHOW GRANTS FOR 'read_user1'@'localhost' USING 'app_read';
+--------------------------------------------------------+
| Grants for read_user1@localhost                        |
+--------------------------------------------------------+
| GRANT USAGE ON *.* TO `read_user1`@`localhost`         |
| GRANT SELECT ON `app_db`.* TO `read_user1`@`localhost` |
| GRANT `app_read`@`%` TO `read_user1`@`localhost`       |
+--------------------------------------------------------+
3 rows in set (0.00 sec)

mysql> SHOW GRANTS FOR 'rw_user1'@'localhost' USING 'app_read', 'app_write';
+------------------------------------------------------------------------------+
| Grants for rw_user1@localhost                                                |
+------------------------------------------------------------------------------+
| GRANT USAGE ON *.* TO `rw_user1`@`localhost`                                 |
| GRANT SELECT, INSERT, UPDATE, DELETE ON `app_db`.* TO `rw_user1`@`localhost` |
| GRANT `app_read`@`%`,`app_write`@`%` TO `rw_user1`@`localhost`               |
+------------------------------------------------------------------------------+
3 rows in set (0.00 sec)

```
 ロールのアクティブ化  
ユーザーアカウントに付与されるロールは、アカウントセッション内でアクティブまたは非アクティブにできます。 
```
mysql>  SELECT CURRENT_ROLE();
+----------------+
| CURRENT_ROLE() |
+----------------+
| NONE           |
+----------------+
1 row in set (0.00 sec)
```
アクティブロールの設定
```
mysql> SET DEFAULT ROLE ALL TO
    ->   'dev1'@'localhost',
    ->   'read_user1'@'localhost',
    ->   'read_user2'@'localhost',
    ->   'rw_user1'@'localhost';
Query OK, 0 rows affected (0.01 sec)
```
該当ロールでログイン後、アクティブロールの確認
```
C:\Users\masami>mysql -u 'dev1' -p
Enter password: ********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 29
Server version: 8.2.0 MySQL Community Server - GPL

Copyright (c) 2000, 2023, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> SELECT CURRENT_ROLE();
+---------------------+
| CURRENT_ROLE()      |
+---------------------+
| `app_developer`@`%` |
+---------------------+
1 row in set (0.00 sec)
```
セッション内で、ユーザーは SET ROLE を実行してアクティブなロールのセットを変更できます。
```
mysql> SET ROLE NONE; SELECT CURRENT_ROLE();
Query OK, 0 rows affected (0.00 sec)

+----------------+
| CURRENT_ROLE() |
+----------------+
| NONE           |
+----------------+
1 row in set (0.00 sec)

mysql>  SET ROLE ALL EXCEPT 'app_write'; SELECT CURRENT_ROLE();
Query OK, 0 rows affected (0.00 sec)

+---------------------+
| CURRENT_ROLE()      |
+---------------------+
| `app_developer`@`%` |
+---------------------+
1 row in set (0.00 sec)

mysql> SET ROLE DEFAULT; SELECT CURRENT_ROLE();
Query OK, 0 rows affected (0.00 sec)

+---------------------+
| CURRENT_ROLE()      |
+---------------------+
| `app_developer`@`%` |
+---------------------+
1 row in set (0.00 sec)
```
ロールまたはロール権限の取消し
```
mysql> REVOKE INSERT, UPDATE, DELETE ON app_db.* FROM 'app_write';
Query OK, 0 rows affected (0.01 sec)

mysql> SHOW GRANTS FOR 'app_write';
+---------------------------------------+
| Grants for app_write@%                |
+---------------------------------------+
| GRANT USAGE ON *.* TO `app_write`@`%` |
+---------------------------------------+
1 row in set (0.00 sec)

mysql> SHOW GRANTS FOR 'rw_user1'@'localhost'
    ->        USING 'app_read', 'app_write';
+----------------------------------------------------------------+
| Grants for rw_user1@localhost                                  |
+----------------------------------------------------------------+
| GRANT USAGE ON *.* TO `rw_user1`@`localhost`                   |
| GRANT SELECT ON `app_db`.* TO `rw_user1`@`localhost`           |
| GRANT `app_read`@`%`,`app_write`@`%` TO `rw_user1`@`localhost` |
+----------------------------------------------------------------+
3 rows in set (0.00 sec)
```
ロールの削除
```
mysql> DROP ROLE 'app_read', 'app_write';
Query OK, 0 rows affected (0.01 sec)

```

ユーザーとロールの互換性  
ロールとユーザーの違いの 1 つは、CREATE ROLE ではデフォルトでロックされる認可識別子が作成されるのに対し、CREATE USER ではデフォルトでロック解除される認可識別子が作成されることです。

```
mysql> CREATE USER 'u1';
Query OK, 0 rows affected (0.01 sec)

mysql> CREATE ROLE 'r1';
Query OK, 0 rows affected (0.00 sec)

mysql> GRANT SELECT ON db1.* TO 'u1';
Query OK, 0 rows affected (0.00 sec)

mysql> GRANT SELECT ON db2.* TO 'r1';
Query OK, 0 rows affected (0.00 sec)

mysql> CREATE USER 'u2';
Query OK, 0 rows affected (0.01 sec)

mysql> CREATE ROLE 'r2';
Query OK, 0 rows affected (0.00 sec)

mysql> GRANT 'u1', 'r1' TO 'u2';
Query OK, 0 rows affected (0.00 sec)

mysql> GRANT 'u1', 'r1' TO 'r2';
Query OK, 0 rows affected (0.00 sec)
```
権限は個人ではなく役割につくものなので、基本的には
ユーザーではなくロールで権限コントロールをした方が良いと思う。

### 6.2.20 アカウントリソース制限の設定
MySQL では、次のサーバーリソースの使用に関する個々のアカウントの制限を許可しています:

* アカウントが発行できるクエリーの数/時間
* アカウントが発行できる更新の数/時間
* アカウントが 1 時間ごとにサーバーに接続できる回数
* アカウントによるサーバーへの同時接続の数

新規登録
```sql
mysql> CREATE USER 'francis'@'localhost' IDENTIFIED BY 'frank'
    ->          WITH MAX_QUERIES_PER_HOUR 20
    ->               MAX_UPDATES_PER_HOUR 10
    ->               MAX_CONNECTIONS_PER_HOUR 5
    ->               MAX_USER_CONNECTIONS 2;
Query OK, 0 rows affected (0.01 sec)
```
更新
```sql
mysql>  ALTER USER 'francis'@'localhost' WITH MAX_QUERIES_PER_HOUR 100;
Query OK, 0 rows affected (0.00 sec)
```
制限削除
```sql
mysql> ALTER USER 'francis'@'localhost' WITH MAX_CONNECTIONS_PER_HOUR 0;
Query OK, 0 rows affected (0.00 sec)
```
max_user_connections システム変数によって制限を受ける例
```sql
mysql> ALTER USER 'francis'@'localhost' WITH MAX_USER_CONNECTIONS 0;
Query OK, 0 rows affected (0.00 sec)
```
この場合、max_user_connectionsが10なら10で制限される


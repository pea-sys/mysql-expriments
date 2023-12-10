## 6.2.11 アカウントカテゴリ
MySQL 8.0.16 では、MySQL に、SYSTEM_USER 権限に基づいてユーザーアカウントカテゴリの概念が組み込まれています


### システムアカウントと通常アカウント
* SYSTEM_USER 権限を持つユーザーはシステムユーザーです。
* SYSTEM_USER 権限を持たないユーザーは通常のユーザーです。

### システムおよび通常のセッション
サーバー内で実行されているセッションは、システムユーザーと通常のユーザーの区別と同様に、システムセッションまたは通常のセッションと区別されます:

* SYSTEM_USER 権限を持つセッションはシステムセッションです。
* SYSTEM_USER 権限を持たないセッションは通常のセッションです。

通常のセッションは、通常のユーザーに許可されている操作のみを実行できます。 システムセッションは、システムユーザーにのみ許可される操作を追加で実行できます。

### 通常アカウントによる操作からのシステムアカウントの保護
アカウント操作には、アカウントの作成と削除、権限の付与と取消し、資格証明や認証プラグインなどのアカウント認証特性の変更、およびパスワード有効期限ポリシーなどの他のアカウント特性の変更が含まれます。


すべてのスキーマに対するすべての権限を持つユーザー u1 を作成するとします。

```sql
mysql> CREATE USER u1 IDENTIFIED BY 'password';
Query OK, 0 rows affected (0.01 sec)

mysql> GRANT ALL ON *.* TO u1 WITH GRANT OPTION;
Query OK, 0 rows affected (0.01 sec)

mysql> REVOKE SYSTEM_USER ON *.* FROM u1;
Query OK, 0 rows affected (0.00 sec)

mysql> REVOKE ALL ON mysql.* FROM u1;
ERROR 1141 (42000): There is no such grant defined for user 'u1' on host '%'
```
現在部分取り消し無効なので最後のクエリは失敗。

アカウントによるすべての mysql システムスキーマへのアクセスを防止するには、次に示すように、mysql スキーマに対するすべての権限を取り消します。
```sql
mysql> CREATE USER u2 IDENTIFIED BY 'password';
ERROR 1396 (HY000): Operation CREATE USER failed for 'u2'@'%'
mysql> GRANT SELECT, INSERT, UPDATE, DELETE ON *.* TO u2;
Query OK, 0 rows affected (0.00 sec)

mysql> REVOKE INSERT, UPDATE, DELETE ON mysql.* FROM u2;
ERROR 1141 (42000): There is no such grant defined for user 'u2' on host '%'
```
すべての mysql スキーマ権限を取り消し、特定の mysql テーブルまたはカラムへのアクセス権を付与することもできます。 
```sql
mysql> CREATE USER u3 IDENTIFIED BY 'password';
Query OK, 0 rows affected (0.01 sec)

mysql> GRANT ALL ON *.* TO u3;
Query OK, 0 rows affected (0.01 sec)

mysql> REVOKE ALL ON mysql.* FROM u3;
ERROR 1141 (42000): There is no such grant defined for user 'u3' on host '%'
mysql> GRANT SELECT ON mysql.db TO u3;
Query OK, 0 rows affected (0.00 sec)

mysql> GRANT SELECT(Host,User) ON mysql.user TO u3;
Query OK, 0 rows affected (0.00 sec)
```

### 6.2.12 部分取消しを使用した権限の制限
部分失効の使用  
partial_revokes システム変数は、アカウントに権限制限を設定できるかどうかを制御します

```sql
mysql> CREATE USER u1;
Query OK, 0 rows affected (0.01 sec)

mysql> GRANT SELECT, INSERT ON *.* TO u1;
Query OK, 0 rows affected (0.00 sec)

mysql> REVOKE INSERT ON world.* FROM u1;
ERROR 1141 (42000): There is no such grant defined for user 'u1' on host '%'
mysql> SET PERSIST partial_revokes = ON;
Query OK, 0 rows affected (0.00 sec)

mysql> REVOKE INSERT ON world.* FROM u1;
Query OK, 0 rows affected (0.00 sec)

mysql> SHOW GRANTS FOR u1;
+------------------------------------------+
| Grants for u1@%                          |
+------------------------------------------+
| GRANT SELECT, INSERT ON *.* TO `u1`@`%`  |
| REVOKE INSERT ON `world`.* FROM `u1`@`%` |
+------------------------------------------+
2 rows in set (0.00 sec)
```

```sql
mysql> SELECT User, Host, User_attributes->>'$.Restrictions'
    ->        FROM mysql.user WHERE User_attributes->>'$.Restrictions' <> '';
+------+------+---------------------------------------------------+
| User | Host | User_attributes->>'$.Restrictions'                |
+------+------+---------------------------------------------------+
| u1   | %    | [{"Database": "world", "Privileges": ["INSERT"]}] |
+------+------+---------------------------------------------------+
1 row in set (0.00 sec)
```
スキーマレベルの権限の部分的な取消しは、SHOW GRANTS 出力に REVOKE ステートメントとして表示されます
```sql
mysql> DROP USER u1;
Query OK, 0 rows affected (0.01 sec)

mysql> CREATE USER u1;
Query OK, 0 rows affected (0.00 sec)

mysql> GRANT UPDATE ON mysql.* TO u1;
Query OK, 0 rows affected (0.00 sec)

mysql> GRANT DELETE ON world.* TO u1;
Query OK, 0 rows affected (0.00 sec)

mysql> SHOW GRANTS FOR u1;
+---------------------------------------+
| Grants for u1@%                       |
+---------------------------------------+
| GRANT USAGE ON *.* TO `u1`@`%`        |
| GRANT UPDATE ON `mysql`.* TO `u1`@`%` |
| GRANT DELETE ON `world`.* TO `u1`@`%` |
+---------------------------------------+
3 rows in set (0.00 sec)
```
取り消すと、スキーマレベルの権限は単に出力から消えます。
```sql
mysql>  REVOKE UPDATE ON mysql.* FROM u1;
Query OK, 0 rows affected (0.00 sec)

mysql> REVOKE DELETE ON world.* FROM u1;
Query OK, 0 rows affected (0.00 sec)

mysql> SHOW GRANTS FOR u1;
+--------------------------------+
| Grants for u1@%                |
+--------------------------------+
| GRANT USAGE ON *.* TO `u1`@`%` |
+--------------------------------+
1 row in set (0.00 sec)
```
ユーザーが権限を付与すると、権限受領者がその権限を持たない権限をすでに持っていないかぎり、権限受領者はその権限を継承します。
```sql
mysql> DROP USER u1;
Query OK, 0 rows affected (0.00 sec)

mysql> CREATE USER u1, u2;
Query OK, 0 rows affected (0.01 sec)

mysql> GRANT SELECT ON *.* TO u2;
Query OK, 0 rows affected (0.00 sec)
```
管理ユーザー admin に、グローバルだが部分的に取り消された SELECT 権限があるとします
```sql
mysql> CREATE USER admin;
Query OK, 0 rows affected (0.00 sec)

mysql> GRANT SELECT ON *.* TO admin WITH GRANT OPTION;
Query OK, 0 rows affected (0.00 sec)

mysql> REVOKE SELECT ON mysql.* FROM admin;
Query OK, 0 rows affected (0.03 sec)

mysql> SHOW GRANTS FOR admin;
+------------------------------------------------------+
| Grants for admin@%                                   |
+------------------------------------------------------+
| GRANT SELECT ON *.* TO `admin`@`%` WITH GRANT OPTION |
| REVOKE SELECT ON `mysql`.* FROM `admin`@`%`          |
+------------------------------------------------------+
2 rows in set (0.00 sec)
```
* admin が SELECT 権限を持たない u1 に SELECT をグローバルに付与する場合、u1 は admin 権限制限を継承します:
```sql
mysql> GRANT SELECT ON *.* TO u1;
Query OK, 0 rows affected (0.00 sec)

mysql> SHOW GRANTS FOR u1;
+------------------------------------------+
| Grants for u1@%                          |
+------------------------------------------+
| GRANT SELECT ON *.* TO `u1`@`%`          |
| REVOKE SELECT ON `mysql`.* FROM `u1`@`%` |
+------------------------------------------+
2 row in set (0.00 sec)
```
* u2 は制限なしでグローバル SELECT 権限をすでに保持しています。
```sql
mysql> GRANT SELECT ON *.* TO u2;
Query OK, 0 rows affected (0.00 sec)

mysql> SHOW GRANTS FOR u2;
+---------------------------------+
| Grants for u2@%                 |
+---------------------------------+
| GRANT SELECT ON *.* TO `u2`@`%` |
+---------------------------------+
1 row in set (0.00 sec)
```
 AS user 句が含まれている場合、適用される権限制限は、ステートメントを実行するユーザーではなく、句で指定されたユーザー/ロールの組合せに対する権限制限
 ```sql
 mysql> CREATE USER u1;
Query OK, 0 rows affected (0.01 sec)

mysql> GRANT SELECT, INSERT, UPDATE, DELETE ON *.* TO u1;
Query OK, 0 rows affected (0.00 sec)

mysql> REVOKE INSERT ON mysql.* FROM u1;
Query OK, 0 rows affected (0.00 sec)

mysql> SHOW GRANTS FOR u1;
+---------------------------------------------------------+
| Grants for u1@%                                         |
+---------------------------------------------------------+
| GRANT SELECT, INSERT, UPDATE, DELETE ON *.* TO `u1`@`%` |
| REVOKE INSERT ON `mysql`.* FROM `u1`@`%`                |
+---------------------------------------------------------+
2 rows in set (0.00 sec)

mysql> REVOKE DELETE, UPDATE ON db2.* FROM u1;
Query OK, 0 rows affected (0.03 sec)

mysql> SHOW GRANTS FOR u1;
+---------------------------------------------------------+
| Grants for u1@%                                         |
+---------------------------------------------------------+
| GRANT SELECT, INSERT, UPDATE, DELETE ON *.* TO `u1`@`%` |
| REVOKE UPDATE, DELETE ON `db2`.* FROM `u1`@`%`          |
| REVOKE INSERT ON `mysql`.* FROM `u1`@`%`                |
+---------------------------------------------------------+
3 rows in set (0.00 sec)
```
制限のない別のアカウントは、制限付きスキーマまたはその中のオブジェクトの制限付きアカウントに権限を付与できます
```sql
mysql> CREATE USER u1;
Query OK, 0 rows affected (0.00 sec)

mysql> GRANT SELECT, INSERT, UPDATE ON *.* TO u1;
Query OK, 0 rows affected (0.00 sec)

mysql> REVOKE SELECT, INSERT, UPDATE ON mysql.* FROM u1;
Query OK, 0 rows affected (0.01 sec)

mysql> GRANT SELECT ON mysql.user TO u1;          -- grant table privilege
Query OK, 0 rows affected (0.00 sec)

mysql> GRANT SELECT(Host,User) ON mysql.db TO u1; -- grant column privileges
Query OK, 0 rows affected (0.00 sec)

mysql> SHOW GRANTS FOR u1;
+-----------------------------------------------------------+
| Grants for u1@%                                           |
+-----------------------------------------------------------+
| GRANT SELECT, INSERT, UPDATE ON *.* TO `u1`@`%`           |
| REVOKE SELECT, INSERT, UPDATE ON `mysql`.* FROM `u1`@`%`  |
| GRANT SELECT (`Host`, `User`) ON `mysql`.`db` TO `u1`@`%` |
| GRANT SELECT ON `mysql`.`user` TO `u1`@`%`                |
+-----------------------------------------------------------+
4 rows in set (0.00 sec)
```
複数の権限をグローバルに保持しているが、INSERT、UPDATE および DELETE に制限があるユーザー u1 
```sql
mysql> CREATE USER u1;
Query OK, 0 rows affected (0.00 sec)

mysql>  GRANT SELECT, INSERT, UPDATE, DELETE ON *.* TO u1;
Query OK, 0 rows affected (0.00 sec)

mysql> REVOKE INSERT, UPDATE, DELETE ON mysql.* FROM u1;
Query OK, 0 rows affected (0.00 sec)

mysql> SHOW GRANTS FOR u1;
+----------------------------------------------------------+
| Grants for u1@%                                          |
+----------------------------------------------------------+
| GRANT SELECT, INSERT, UPDATE, DELETE ON *.* TO `u1`@`%`  |
| REVOKE INSERT, UPDATE, DELETE ON `mysql`.* FROM `u1`@`%` |
+----------------------------------------------------------+
2 rows in set (0.00 sec)
```
制限のないアカウントから u1 にグローバルに権限を付与すると、権限制限が削除
```sql
mysql> GRANT INSERT ON *.* TO u1;
Query OK, 0 rows affected (0.00 sec)

mysql> SHOW GRANTS FOR u1;
+---------------------------------------------------------+
| Grants for u1@%                                         |
+---------------------------------------------------------+
| GRANT SELECT, INSERT, UPDATE, DELETE ON *.* TO `u1`@`%` |
| REVOKE UPDATE, DELETE ON `mysql`.* FROM `u1`@`%`        |
+---------------------------------------------------------+
2 rows in set (0.00 sec)
```
スキーマレベルで u1 に権限を付与すると、権限の制限がなくなります。
```sql
mysql> GRANT UPDATE ON mysql.* TO u1;
Query OK, 0 rows affected (0.00 sec)

mysql> SHOW GRANTS FOR u1;
+---------------------------------------------------------+
| Grants for u1@%                                         |
+---------------------------------------------------------+
| GRANT SELECT, INSERT, UPDATE, DELETE ON *.* TO `u1`@`%` |
| REVOKE DELETE ON `mysql`.* FROM `u1`@`%`                |
+---------------------------------------------------------+
2 rows in set (0.00 sec)
```
グローバル権限を取り消すと、その権限に対する制限も含めて権限が削除されます。
```sql
mysql> REVOKE DELETE ON *.* FROM u1;
Query OK, 0 rows affected (0.01 sec)

mysql> SHOW GRANTS FOR u1;
+-------------------------------------------------+
| Grants for u1@%                                 |
+-------------------------------------------------+
| GRANT SELECT, INSERT, UPDATE ON *.* TO `u1`@`%` |
+-------------------------------------------------+
1 row in set (0.00 sec)
```
アカウントにグローバルレベルとスキーマレベルの両方の権限がある場合、部分的な取消しを有効にするには、スキーマレベルで 2 回取り消す必要があります。
```sql
mysql> CREATE USER u1;
Query OK, 0 rows affected (0.00 sec)

mysql> GRANT SELECT, INSERT ON *.* TO u1;
Query OK, 0 rows affected (0.01 sec)

mysql> GRANT INSERT ON world.* TO u1;
Query OK, 0 rows affected (0.00 sec)

mysql> SHOW GRANTS FOR u1;
+-----------------------------------------+
| Grants for u1@%                         |
+-----------------------------------------+
| GRANT SELECT, INSERT ON *.* TO `u1`@`%` |
| GRANT INSERT ON `world`.* TO `u1`@`%`   |
+-----------------------------------------+
2 rows in set (0.00 sec)
```
world で INSERT を取り消すと、スキーマレベルの権限が取り消されます
```sql
mysql> REVOKE INSERT ON world.* FROM u1;
Query OK, 0 rows affected (0.00 sec)

mysql> SHOW GRANTS FOR u1;
+-----------------------------------------+
| Grants for u1@%                         |
+-----------------------------------------+
| GRANT SELECT, INSERT ON *.* TO `u1`@`%` |
+-----------------------------------------+
1 row in set (0.00 sec)
```
world で INSERT を再度取り消すと、グローバル権限の部分的な取消しが実行されます 
```sql
mysql> REVOKE INSERT ON world.* FROM u1;
Query OK, 0 rows affected (0.00 sec)

mysql> SHOW GRANTS FOR u1;
+------------------------------------------+
| Grants for u1@%                          |
+------------------------------------------+
| GRANT SELECT, INSERT ON *.* TO `u1`@`%`  |
| REVOKE INSERT ON `world`.* FROM `u1`@`%` |
+------------------------------------------+
2 rows in set (0.00 sec)
```
部分失効の無効化  
部分失効アカウントの確認
```sql
mysql> SELECT User, Host, User_attributes->>'$.Restrictions'
    -> FROM mysql.user WHERE User_attributes->>'$.Restrictions' <> '';
+-------+------+---------------------------------------------------+
| User  | Host | User_attributes->>'$.Restrictions'                |
+-------+------+---------------------------------------------------+
| admin | %    | [{"Database": "mysql", "Privileges": ["SELECT"]}] |
| u1    | %    | [{"Database": "world", "Privileges": ["INSERT"]}] |
+-------+------+---------------------------------------------------+
2 rows in set (0.00 sec)
```
部分失効アカウントがあるうちは部分失効設定をOFFにできない
```sql
mysql> SET PERSIST partial_revokes = OFF;
ERROR 3896 (HY000): At least one partial revoke exists on a database. The system variable '@@partial_revokes' must be set to ON.
```
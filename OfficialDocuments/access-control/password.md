### 6.2.14 アカウントパスワードの割り当て
MySQL サーバーに接続するクライアントに必要な証明書には、パスワードを含めることができます。

新しいアカウントの作成時にパスワードを割り当てる
```sql
mysql> CREATE USER 'jeffrey'@'localhost' IDENTIFIED BY 'password';
Query OK, 0 rows affected (0.03 sec)
```
既存のアカウントのパスワードを割り当てたり変更する
```sql
mysql> ALTER USER 'jeffrey'@'localhost' IDENTIFIED BY 'password';
Query OK, 0 rows affected (0.01 sec)
```
自分のアカウントに文字どおりに名前を付けずに自分のパスワードを変更できます
```
mysql> ALTER USER USER() IDENTIFIED BY 'password';
Query OK, 0 rows affected (0.01 sec)
```

### 6.2.15 パスワード管理
MySQL は、次のパスワード管理機能をサポートしています:

* パスワードの有効期限。パスワードを定期的に変更する必要があります。

* 古いパスワードが再度選択されないようにするためのパスワード再利用の制限。

* パスワードの検証。パスワードの変更を要求するには、置換する現在のパスワードも指定します。

* デュアルパスワード。クライアントがプライマリパスワードまたはセカンダリパスワードを使用して接続できるようにします。

* 強力なパスワードを要求するためのパスワード強度評価。

* ランダムパスワード生成。明示的な管理者指定のリテラルパスワードを要求するかわりに使用します。

* パスワードの失敗を追跡して、連続するパスワードログインの失敗が多すぎる場合に一時アカウントロックを有効にします。


### パスワード有効期限ポリシー
MySQL を使用すると、データベース管理者はアカウントパスワードを手動で期限切れにしたり、自動パスワード期限切れのポリシーを設定できます。 

アカウントパスワードを手動で期限切れにする
```sql
mysql> ALTER USER 'jeffrey'@'localhost' PASSWORD EXPIRE;
Query OK, 0 rows affected (0.02 sec)
```
パスワード有効期限の設定を6カ月にする場合
```
[mysqld]
default_password_lifetime=180
```
パスワード無期限の設定
```
[mysqld]
default_password_lifetime=0
```
動的に設定
```sql
mysql> SET PERSIST default_password_lifetime = 180;
```
設定をオーバーライドする  
90 日ごとにパスワードを変更する
```sql
mysql> CREATE USER 'jeffrey'@'localhost' PASSWORD EXPIRE INTERVAL 90 DAY;
Query OK, 0 rows affected (0.01 sec)

mysql> ALTER USER 'jeffrey'@'localhost' PASSWORD EXPIRE INTERVAL 90 DAY;
Query OK, 0 rows affected (0.00 sec)
```
パスワードの有効期限の無効化
```sql
mysql> CREATE USER 'jeffrey'@'localhost' PASSWORD EXPIRE NEVER;
Query OK, 0 rows affected (0.00 sec)

mysql> ALTER USER 'jeffrey'@'localhost' PASSWORD EXPIRE NEVER;
Query OK, 0 rows affected (0.00 sec)
```
グローバルな有効期限
```sql
mysql> CREATE USER 'jeffrey'@'localhost' PASSWORD EXPIRE DEFAULT;
ERROR 1396 (HY000): Operation CREATE USER failed for 'jeffrey'@'localhost'
mysql> ALTER USER 'jeffrey'@'localhost' PASSWORD EXPIRE DEFAULT;
Query OK, 0 rows affected (0.00 sec)
```

パスワード再利用ポリシー  
MySQL では、以前のパスワードの再利用に制限を適用できます。  
パスワード再利用ポリシーをグローバルに確立するには、password_history および password_reuse_interval システム変数を使用します。  
設定をオーバーライドする 

再利用を許可する前に、少なくとも 5 つのパスワード変更が必要
```sql
mysql> CREATE USER 'jeffrey'@'localhost' PASSWORD HISTORY 5;
Query OK, 0 rows affected (0.00 sec)

mysql> ALTER USER 'jeffrey'@'localhost' PASSWORD HISTORY 5;
Query OK, 0 rows affected (0.00 sec)
```
再利用を許可する前に 365 日以上経過
```sql
mysql> CREATE USER 'jeffrey'@'localhost' PASSWORD REUSE INTERVAL 365 DAY;
Query OK, 0 rows affected (0.00 sec)

mysql> ALTER USER 'jeffrey'@'localhost' PASSWORD REUSE INTERVAL 365 DAY;
Query OK, 0 rows affected (0.00 sec)
```
組み合わせ
```sql
mysql> CREATE USER 'jeffrey'@'localhost'
    ->   PASSWORD HISTORY 5
    ->   PASSWORD REUSE INTERVAL 365 DAY;
Query OK, 0 rows affected (0.00 sec)

mysql> ALTER USER 'jeffrey'@'localhost'
    ->   PASSWORD HISTORY 5
    ->   PASSWORD REUSE INTERVAL 365 DAY;
Query OK, 0 rows affected (0.00 sec)
```
デフォルト組み合わせ
```sql
mysql> CREATE USER 'jeffrey'@'localhost'
    ->   PASSWORD HISTORY DEFAULT
    ->   PASSWORD REUSE INTERVAL DEFAULT;
Query OK, 0 rows affected (0.00 sec)

mysql> ALTER USER 'jeffrey'@'localhost'
    ->   PASSWORD HISTORY DEFAULT
    ->   PASSWORD REUSE INTERVAL DEFAULT;
Query OK, 0 rows affected (0.00 sec)
```
### パスワード検証必須ポリシー
* パスワード変更で現在のパスワードを指定する必要があるグローバルポリシーを確立するにはconfを編集
```
[mysqld]
password_require_current=ON
```
* 実行時に password_require_current を設定および永続化する
```sql
mysql> SET PERSIST password_require_current = ON;
Query OK, 0 rows affected (0.01 sec)

mysql> SET PERSIST password_require_current = OFF;
Query OK, 0 rows affected (0.00 sec)
```
オーバーライド  
パスワードの変更で現在のパスワードを指定する
```sql
mysql> CREATE USER 'jeffrey'@'localhost' PASSWORD REQUIRE CURRENT;
Query OK, 0 rows affected (0.00 sec)

mysql> ALTER USER 'jeffrey'@'localhost' PASSWORD REQUIRE CURRENT;
Query OK, 0 rows affected (0.00 sec)
```
パスワードの変更で現在のパスワードを指定する必要はありません (現在のパスワードを指定する必要はあります)
```sql
mysql> CREATE USER 'jeffrey'@'localhost' IDENTIFIED BY 'password';
Query OK, 0 rows affected (0.01 sec)
```
現在のユーザーパスワードを変更
```sql
mysql> ALTER USER USER() IDENTIFIED BY 'auth_string' REPLACE 'password';
Query OK, 0 rows affected (0.02 sec)
```
名前付きユーザーのパスワードを変更
```sql
mysql> ALTER USER 'jeffrey'@'localhost' IDENTIFIED BY 'auth_string' REPLACE 'auth_string';
Query OK, 0 rows affected (0.03 sec)
```
デュアルパスワードのサポート  
MySQL 8.0.14 の時点では、ユーザーアカウントは、プライマリパスワードおよびセカンダリパスワードとして指定されたデュアルパスワードを持つことができます。
```sql
mysql> CREATE USER 'appuser1'@'host1.example.com' IDENTIFIED BY 'password_b';
Query OK, 0 rows affected (0.01 sec)

mysql> ALTER USER 'appuser1'@'host1.example.com'
    ->   IDENTIFIED BY 'password_b'
    ->   RETAIN CURRENT PASSWORD;
Query OK, 0 rows affected (0.01 sec)
```
セカンダリパスワードは不要にする
```sql
mysql> ALTER USER 'appuser1'@'host1.example.com'
    ->   DISCARD OLD PASSWORD;
Query OK, 0 rows affected (0.00 sec)
```

ランダムパスワード生成  
MySQL 8.0.18 では、CREATE USER、ALTER USER および SET PASSWORD ステートメントに、明示的に管理者指定のリテラルパスワードを要求するかわりに、ユーザーアカウントのランダムパスワードを生成する機能があります。
```sql
mysql> CREATE USER
    ->        'u1'@'localhost' IDENTIFIED BY RANDOM PASSWORD,
    ->        'u2'@'%.example.com' IDENTIFIED BY RANDOM PASSWORD,
    ->        'u3'@'%.org' IDENTIFIED BY RANDOM PASSWORD;
+------+---------------+----------------------+-------------+
| user | host          | generated password   | auth_factor |
+------+---------------+----------------------+-------------+
| u1   | localhost     | jSpmZ(c!BrUkVwFAU0VG |           1 |
| u2   | %.example.com | fdVikSnf6ATc*Inq(X,N |           1 |
| u3   | %.org         | }_7FOXjpkZ8I4O(cod/6 |           1 |
+------+---------------+----------------------+-------------+
3 rows in set (0.03 sec)

mysql> ALTER USER
    ->        'u1'@'localhost' IDENTIFIED BY RANDOM PASSWORD,
    ->        'u2'@'%.example.com' IDENTIFIED BY RANDOM PASSWORD;
+------+---------------+----------------------+-------------+
| user | host          | generated password   | auth_factor |
+------+---------------+----------------------+-------------+
| u1   | localhost     | Pr{w{.(P;,Da_/r8;k!w |           1 |
| u2   | %.example.com | /:8T/TC78:[)dzj*!S}8 |           1 |
+------+---------------+----------------------+-------------+
2 rows in set (0.02 sec)

mysql> SET PASSWORD FOR 'u3'@'%.org' TO RANDOM;
+------+-------+----------------------+-------------+
| user | host  | generated password   | auth_factor |
+------+-------+----------------------+-------------+
| u3   | %.org | <eeNHLPeTij2+{t;-02m |           1 |
+------+-------+----------------------+-------------+
1 row in set (0.01 sec)
```
失敗したログイントラッキングと一時アカウントロック  

MySQL 8.0.19 の時点では、管理者は、連続するログイン失敗が多すぎると一時アカウントロックが発生するようにユーザーアカウントを構成できます。

```sql
mysql> CREATE USER 'u1'@'localhost' IDENTIFIED BY 'password'
    ->   FAILED_LOGIN_ATTEMPTS 3 PASSWORD_LOCK_TIME 3;
Query OK, 0 rows affected (0.02 sec)
```
ロック動作確認
```
C:\Users\masami>mysql -u u1 -p
Enter password: ***
ERROR 1045 (28000): Access denied for user 'u1'@'localhost' (using password: YES)

C:\Users\masami>mysql -u u1 -p
Enter password: ***
ERROR 1045 (28000): Access denied for user 'u1'@'localhost' (using password: YES)

C:\Users\masami>mysql -u u1 -p
Enter password: *****
ERROR 3955 (HY000): Access denied for user 'u1'@'localhost'. Account is blocked for 3 day(s) (3 day(s) remaining) due to 3 consecutive failed logins.

```
### 6.2.16 期限切れパスワードのサーバー処理
MySQL にはパスワードの有効期限機能が用意されており、データベース管理者はパスワードのリセットをユーザーに要求できます。

```sql
mysql> CREATE USER 'myuser'@'localhost';
Query OK, 0 rows affected (0.00 sec)

mysql> ALTER USER 'myuser'@'localhost' PASSWORD EXPIRE;
Query OK, 0 rows affected (0.00 sec)
```
セッション内で許可されていない操作の場合、サーバーは ER_MUST_CHANGE_PASSWORD エラーを返します
```sql
mysql> USE performance_schema;
No connection. Trying to reconnect...
Connection id:    12
Current database: *** NONE ***

ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement.

mysql> SELECT 1;
ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement.
```

### 6.4.3 パスワード検証コンポーネント
validate_password コンポーネントは、アカウントパスワードを要求し、潜在的なパスワードの強度テストを有効にすることで、セキュリティを向上させます。
### 6.4.3.1 パスワード検証コンポーネントのインストールおよびアンインストール
validate_password コンポーネントをインストール
```sql
mysql> INSTALL COMPONENT 'file://component_validate_password';
Query OK, 0 rows affected (0.04 sec)
```
validate_password コンポーネントをアンインストール

### 6.4.3.2 パスワード検証オプションおよび変数
```
mysql> SHOW VARIABLES LIKE 'validate_password.%';
+-------------------------------------------------+--------+
| Variable_name                                   | Value  |
+-------------------------------------------------+--------+
| validate_password.changed_characters_percentage | 0      |
| validate_password.check_user_name               | ON     |
| validate_password.dictionary_file               |        |
| validate_password.length                        | 8      |
| validate_password.mixed_case_count              | 1      |
| validate_password.number_count                  | 1      |
| validate_password.policy                        | MEDIUM |
| validate_password.special_char_count            | 1      |
+-------------------------------------------------+--------+
```
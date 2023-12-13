### 6.3.1 暗号化接続を使用するための MySQL の構成
暗号化された接続を使用するかどうかを示し、適切な証明書およびキーファイルを指定するために、いくつかの構成パラメータを使用できます。 このセクションでは、暗号化された接続のためのサーバーおよびクライアントの構成に関する一般的なガイダンスを示します


SSLはデフォルト有効のネット記事をいくつか見かけましたが、WindowsではSSLはデフォルト無効のようでした。

```sql
mysql> \s
--------------
mysql  Ver 8.2.0 for Win64 on x86_64 (MySQL Community Server - GPL)

Connection id:          13
Current database:
Current user:           root@localhost
SSL:                    Not in use
Using delimiter:        ;
Server version:         8.2.0 MySQL Community Server - GPL
Protocol version:       10
Connection:             Shared memory: MYSQL
Server characterset:    utf8mb4
Db     characterset:    utf8mb4
Client characterset:    cp932
Conn.  characterset:    cp932
TCP port:               0
Binary data as:         Hexadecimal
Uptime:                 51 sec

Threads: 2  Questions: 6498  Slow queries: 0  Opens: 257  Flush tables: 3  Open tables: 165  Queries per second avg: 127.411
--------------

mysql> show session status like 'ssl_cipher';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| Ssl_cipher    |       |
+---------------+-------+
1 row in set (0.00 sec)
```

設定ファイルを更新する  
例はmysqlインストール時に一緒にインストールされているものを使用  
C:\ProgramData\MySQL\MySQL Server 8.2\Data
```
[mysqld]
ssl_ca="C:\ProgramData\MySQL\MySQL Server 8.2\Data\ca.pem"
ssl_cert="C:\ProgramData\MySQL\MySQL Server 8.2\Data\server-cert.pem"
ssl_key=server-"C:\ProgramData\MySQL\MySQL Server 8.2\Data\server-key.pem"
```
mysqlサービスを再起動  
クライアントからアクセスしようとすると
```
C:\Windows\System32>mysql --ssl-ca="C:\ProgramData\MySQL\MySQL Server 8.2\Data\ca.pem"  --ssl-cert="C:\ProgramData\MySQL\MySQL Server 8.2\Data\client-cert.pem" --ssl-key="C:\ProgramData\MySQL\MySQL Server 8.2\Data\client-key.pem" -u root -p
Enter password: **********
ERROR 2026 (HY000): SSL connection error: error:00000000:lib(0)::reason(0)
```
なぜか失敗する  
下記のバグ報告があり、7年の時を経て閉じられているが
まだ役に立たないエラーは健在の模様
https://bugs.mysql.com/bug.php?id=75311

一応、キーの確認をしてみたが問題なさそう
```
openssl verify -CAfile "C:\ProgramData\MySQL\MySQL Server 8.2\Data\ca.pem" "C:\ProgramData\MySQL\MySQL Server 8.2\Data\server-cert.pem" "C:\ProgramData\MySQL\MySQL Server 8.2\Data\client-cert.pem"
C:\ProgramData\MySQL\MySQL Server 8.2\Data\server-cert.pem: OK
C:\ProgramData\MySQL\MySQL Server 8.2\Data\client-cert.pem: OK
```

Windowsで試していたが、Ubuntuでも同様  
取り合えず本項目はスキップ

### 6.3.2 暗号化された接続 TLS プロトコルおよび暗号
MySQL では、複数の TLS プロトコルおよび暗号がサポートされており、暗号化された接続を許可するプロトコルおよび暗号を構成できます。 
```sql
mysql> SHOW GLOBAL VARIABLES LIKE 'tls_version';
+---------------+-----------------+
| Variable_name | Value           |
+---------------+-----------------+
| tls_version   | TLSv1.2,TLSv1.3 |
+---------------+-----------------+
1 row in set, 1 warning (0.01 sec)
```
設定ファイルでtlsバージョンを制限
```
[mysqld]
tls_version=TLSv1.2
```
サーバ再起動後は次のようになる
```sql
mysql> SHOW GLOBAL VARIABLES LIKE 'tls_version';
+---------------+---------+
| Variable_name | Value   |
+---------------+---------+
| tls_version   | TLSv1.2 |
+---------------+---------+
1 row in set, 1 warning (0.01 sec)
```

以下の値は穴があるため非推奨とされている
```
tls_version=TLSv1,TLSv1.2       (TLSv1.1 is missing)
tls_version=TLSv1.1,TLSv1.3     (TLSv1.2 is missing)
```
特定のサーバーがサポートする暗号を判別するには、Ssl_cipher_list ステータス変数のセッション値を確認
```sql
mysql> SHOW SESSION STATUS LIKE 'Ssl_cipher_list';
+-----------------+-------+
| Variable_name   | Value |
+-----------------+-------+
| Ssl_cipher_list |       |
+-----------------+-------+
1 row in set (0.00 sec)
```
### 現在のクライアントセッション TLS プロトコルおよび暗号のモニタリング
```sql
mysql> SELECT * FROM performance_schema.session_status
    ->        WHERE VARIABLE_NAME IN ('Ssl_version','Ssl_cipher');
+---------------+----------------+
| VARIABLE_NAME | VARIABLE_VALUE |
+---------------+----------------+
| Ssl_cipher    |                |
| Ssl_version   |                |
+---------------+----------------+
2 rows in set (0.00 sec)
```

### 6.3.3.1 MySQL を使用した SSL および RSA 証明書とキーの作成
SSL および RSA ファイルを生成するために手動で起動できる mysql_ssl_rsa_setup ユーティリティが含まれています。 

OpenSSLを使うため、Windowsは以下からOpenSSLをインストールして、環境変数でopensslコマンドが通るようにします。
https://slproweb.com/products/Win32OpenSSL.html
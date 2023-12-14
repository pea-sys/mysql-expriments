### 6.4.4 MySQL キーリング
MySQL Server は、内部サーバーコンポーネントおよびプラグインが機密情報を安全に格納して後で取得できるようにするキーリングをサポートしています。 

Windowsでのインストール  
* フォルダ作成("C:\ProgramData\lib\mysql-keyring")
* iniファイル編集
```
[mysqld]
early-plugin-load=keyring_file.dll
keyring_file_data=C:/ProgramData/lib/mysql-keyring/keyring
```
* MySQLサービス再起動
* プラグイン状態確認
```sql
mysql> SELECT PLUGIN_NAME, PLUGIN_STATUS
    ->        FROM INFORMATION_SCHEMA.PLUGINS
    ->        WHERE PLUGIN_NAME LIKE 'keyring%';
ERROR 2013 (HY000): Lost connection to MySQL server during query
No connection. Trying to reconnect...
Connection id:    8
Current database: *** NONE ***

+--------------+---------------+
| PLUGIN_NAME  | PLUGIN_STATUS |
+--------------+---------------+
| keyring_file | ACTIVE        |
+--------------+---------------+
1 row in set (0.01 sec)
```
汎用キーリング関数のインストール
```sql
mysql> INSTALL PLUGIN keyring_udf SONAME 'keyring_udf.dll';
Query OK, 0 rows affected (0.08 sec)
mysql> CREATE FUNCTION keyring_key_generate RETURNS INTEGER
    ->   SONAME 'keyring_udf.dll';
Query OK, 0 rows affected (0.01 sec)

mysql> CREATE FUNCTION keyring_key_fetch RETURNS STRING
    ->   SONAME 'keyring_udf.dll';
Query OK, 0 rows affected (0.00 sec)

mysql> CREATE FUNCTION keyring_key_length_fetch RETURNS INTEGER
    ->   SONAME 'keyring_udf.dll';
Query OK, 0 rows affected (0.00 sec)

mysql> CREATE FUNCTION keyring_key_type_fetch RETURNS STRING
    ->   SONAME 'keyring_udf.dll';
Query OK, 0 rows affected (0.00 sec)

mysql> CREATE FUNCTION keyring_key_store RETURNS INTEGER
    ->   SONAME 'keyring_udf.dll';
Query OK, 0 rows affected (0.00 sec)

mysql> CREATE FUNCTION keyring_key_remove RETURNS INTEGER
    ->   SONAME 'keyring_udf.dll';
Query OK, 0 rows affected (0.01 sec)
```
SECRET キータイプを含むキーリング操作の例:
```sql
mysql> SELECT keyring_key_generate('MySecret1', 'SECRET', 20);
+-------------------------------------------------+
| keyring_key_generate('MySecret1', 'SECRET', 20) |
+-------------------------------------------------+
|                                               1 |
+-------------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT keyring_key_remove('MySecret1');
+---------------------------------+
| keyring_key_remove('MySecret1') |
+---------------------------------+
|                               1 |
+---------------------------------+
1 row in set (0.00 sec)

mysql> SELECT keyring_key_store('MySecret2', 'SECRET', 'MySecretData');
+----------------------------------------------------------+
| keyring_key_store('MySecret2', 'SECRET', 'MySecretData') |
+----------------------------------------------------------+
|                                                        1 |
+----------------------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT keyring_key_fetch('MySecret2');
+----------------------------------------------------------------+
| keyring_key_fetch('MySecret2')                                 |
+----------------------------------------------------------------+
| 0x4D7953656372657444617461                                     |
+----------------------------------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT keyring_key_length_fetch('MySecret2');
+---------------------------------------+
| keyring_key_length_fetch('MySecret2') |
+---------------------------------------+
|                                    12 |
+---------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT keyring_key_type_fetch('MySecret2');
+-------------------------------------+
| keyring_key_type_fetch('MySecret2') |
+-------------------------------------+
| SECRET                              |
+-------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT keyring_key_remove('MySecret2');
+---------------------------------+
| keyring_key_remove('MySecret2') |
+---------------------------------+
|                               1 |
+---------------------------------+
1 row in set (0.00 sec)
```

次の例は、DBA が所有する SharedKey という名前の共有キーと、現在のキー値へのアクセスを提供する get_shared_key() ストアドファンクションの設定方法を示しています。 この値は、key_schema スキーマに作成された、その関数に対する EXECUTE 権限を持つすべてのユーザーが取得できます。

```sql
mysql> CREATE SCHEMA key_schema;
Query OK, 1 row affected (0.01 sec)

mysql> CREATE DEFINER = 'root'@'localhost'
    ->        FUNCTION key_schema.get_shared_key()
    ->        RETURNS BLOB READS SQL DATA
    ->        RETURN keyring_key_fetch('SharedKey');
Query OK, 0 rows affected (0.02 sec)
```
管理アカウントから、共有キーが存在することを確認します:
```sql
mysql> SELECT keyring_key_generate('SharedKey', 'DSA', 8);
+---------------------------------------------+
| keyring_key_generate('SharedKey', 'DSA', 8) |
+---------------------------------------------+
|                                           1 |
+---------------------------------------------+
1 row in set (0.00 sec)

```
管理アカウントから、キーアクセス権を付与する通常のユーザーアカウントを作成します
```sql
mysql> CREATE USER 'key_user'@'localhost'
    ->        IDENTIFIED BY 'key_user_pwd';
Query OK, 0 rows affected (0.01 sec)
```
key_userでアクセスしてもキーにアクセスできない
```sql
mysql> SELECT HEX(key_schema.get_shared_key());
ERROR 1370 (42000): execute command denied to user 'key_user'@'localhost' for routine 'key_schema.get_shared_key'
```
ルートユーザーでアクセス権限を与える
```sql
mysql> GRANT EXECUTE ON FUNCTION key_schema.get_shared_key
    ->        TO 'key_user'@'localhost';
Query OK, 0 rows affected (0.01 sec)
```
アクセスできるようになったことを確認
```sql
C:\Users\masami>mysql -u key_user -p
Enter password: ************

mysql> SELECT HEX(key_schema.get_shared_key());
+----------------------------------+
| HEX(key_schema.get_shared_key()) |
+----------------------------------+
| E77B07E45C150C3D                 |
+----------------------------------+
1 row in set (0.00 sec)
```
### 汎用キーリング関数リファレンス
* keyring_key_fetch(key_id)

キー ID を指定すると、キー値を不明瞭化して戻します。
```sql
mysql> SELECT keyring_key_generate('RSA_key', 'RSA', 16);
+--------------------------------------------+
| keyring_key_generate('RSA_key', 'RSA', 16) |
+--------------------------------------------+
|                                          1 |
+--------------------------------------------+
1 row in set (0.00 sec)
mysql> SELECT HEX(keyring_key_fetch('RSA_key'));
+-----------------------------------+
| HEX(keyring_key_fetch('RSA_key')) |
+-----------------------------------+
| C34329A3283495684AFDC652DC1CB6AE  |
+-----------------------------------+
1 row in set (0.00 sec)
mysql> SELECT keyring_key_type_fetch('RSA_key');
+-----------------------------------+
| keyring_key_type_fetch('RSA_key') |
+-----------------------------------+
| RSA                               |
+-----------------------------------+
1 row in set (0.00 sec)
mysql> SELECT keyring_key_length_fetch('RSA_key');
+-------------------------------------+
| keyring_key_length_fetch('RSA_key') |
+-------------------------------------+
|                                  16 |
+-------------------------------------+
1 row in set (0.00 sec)
```
刷できない文字が含まれている可能性があるため、HEX() を使用してキー値を表示します。
* keyring_key_generate(key_id, key_type, key_length)

指定された ID、タイプおよび長さの新しいランダムキーを生成し、キーリングに格納します。
```sql
mysql> SELECT keyring_key_generate('RSA_key', 'RSA', 384);
+---------------------------------------------+
| keyring_key_generate('RSA_key', 'RSA', 384) |
+---------------------------------------------+
|                                           1 |
+---------------------------------------------+
1 row in set (0.00 sec)
```
* keyring_key_store(key_id, key_type, key)

キーリングにキーを不明瞭化して格納します。
```sql
mysql> SELECT keyring_key_store('new key', 'DSA', 'My key value');
+-----------------------------------------------------+
| keyring_key_store('new key', 'DSA', 'My key value') |
+-----------------------------------------------------+
|                                                   1 |
+-----------------------------------------------------+
1 row in set (0.00 sec)
```

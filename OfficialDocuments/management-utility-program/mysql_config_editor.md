## 4.6.7 mysql_config_editor — MySQL 構成ユーティリティー
mysql_config_editor ユーティリティでは、.mylogin.cnf という名前の不明瞭化されたログインパスファイルに認証資格証明を格納できます。 

* print [options]:パスワードが ***** として表示されることを除き、ログインパスファイルの内容を不明瞭化された形式で出力します。
```
C:\Users\masami>mysql_config_editor print
[client]

C:\Users\masami>mysql_config_editor print --login-path=client
[client]

C:\Users\masami>mysql_config_editor print --login-path=sample
```
* set [options]:ログインパスファイルにログインパスを書き込みます。
```
C:\Users\masami>mysql_config_editor set --login-path=sample --user=user001 --host=localhost

C:\Users\masami>mysql_config_editor print --login-path=sample
[sample]
user = "user001"
host = "localhost"
```

* remove [options]:
ログインパスファイルからログインパスを削除するか、ログインパスからオプションを削除してログインパスを変更します。
```
C:\Users\masami>mysql_config_editor remove --login-path=sample --host

C:\Users\masami>mysql_config_editor print --login-path=sample
[sample]
user = "user001"
```
* reset [options]:ログインパスファイルの内容を空にします。
```
C:\Users\masami>mysql_config_editor reset

C:\Users\masami>mysql_config_editor print
```
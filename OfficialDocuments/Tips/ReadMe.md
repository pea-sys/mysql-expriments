- ログローテーション設定しないと SSH ログインが遅くなる

  > [!NOTE]  
  > https://tech.repro.io/entry/2023/11/30/181742

- incorrect timezone が発生する

```sql
mysql> SELECT CAST(c AT TIME ZONE 'UTC' AS DATETIME(2)) AS u FROM tz;
ERROR 1298 (HY000): Unknown or incorrect time zone: 'UTC'
```

ユーティリティを使用してタイムゾーンをインストールする。Windowsno 場合は以下。

```sql
C:\Users\masami>mysql_tzinfo_to_sql "C:/Program Files/MySQL/MySQL Workbench 8.0 CE/python/lib/zoneinfo"
```

- まだ MyISAM をお使いですか? 今こそ InnoDB に切り替える時です!
  https://blogs.oracle.com/mysql-jp/post/still-using-myisam-it-is-time-to-switch-to-innodb-jp

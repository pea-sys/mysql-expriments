## 4.6.3 myisam_ftdump — 全文インデックス情報の表示
myisam_ftdump は MyISAM テーブル内の FULLTEXT インデックスに関する情報を表示します。 
※myisamはinnodbに劣っているため、使うなと公式ブログにお達しあり

### Setup
```
C:\Users\masami>mysql -u root -p test
Enter password: **********

mysql> CREATE TABLE mytexttable
    -> (
    ->   id   INT NOT NULL,
    ->   txt  TEXT NOT NULL,
    ->   PRIMARY KEY (id),
    ->   FULLTEXT (txt)
    -> ) ENGINE=MyISAM;
Query OK, 0 rows affected (0.01 sec)
```

```
C:\Users\masami>myisam_ftdump "C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI" 1
Total rows: 0
Total words: 0
Unique words: 0
Longest word: 0 chars (P棡ﾁ)
Median length: 0
Average global weight: -nan(ind)
Most common word: 0 times, weight: 0.000000 ()
```

* --count, -c:1 語当たりの統計 (カウントとグローバルな重み) を計算します。
```
C:\Users\masami>mysql -u root -p test
mysql> insert into mytexttable values (1, "I know Nothing is possible");
Query OK, 1 row affected (0.01 sec)
mysql> insert into mytexttable values (2, "We know nothing is impossible");
Query OK, 1 row affected (0.00 sec)

C:\Users\masami>myisam_ftdump --count "C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI" 1
        1            0.0000000 impossible
```
* --dump, -d:データオフセットや語の重みを含むインデックスをダンプします。
```
C:\Users\masami>myisam_ftdump --dump "C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI" 1
       24            0.9886308 impossible
```
* --length, -l:長さの分布をレポートします。
```
C:\Users\masami>myisam_ftdump --length "C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\mytexttable.MYI" 1
 10:          1 100.00%                    1 100.0%
```
### 8.4.7 テーブルカラム数と行サイズの制限
行サイズ制限の例

```sql
mysql> CREATE TABLE t (a VARCHAR(10000), b VARCHAR(10000),
    ->        c VARCHAR(10000), d VARCHAR(10000), e VARCHAR(10000),
    ->        f VARCHAR(10000), g VARCHAR(6000)) ENGINE=InnoDB CHARACTER SET latin1;
ERROR 1118 (42000): Row size too large. The maximum row size for the used table type, not counting BLOBs, is 65535. This includes storage overhead, check the manual. You have to change some columns to TEXT or BLOBs

mysql> CREATE TABLE t (a VARCHAR(10000), b VARCHAR(10000),
    ->        c VARCHAR(10000), d VARCHAR(10000), e VARCHAR(10000),
    ->        f VARCHAR(10000), g VARCHAR(6000)) ENGINE=MyISAM CHARACTER SET latin1;
ERROR 1118 (42000): Row size too large. The maximum row size for the used table type, not counting BLOBs, is 65535. This includes storage overhead, check the manual. You have to change some columns to TEXT or BLOBs
```
次の MyISAM の例では、カラムを TEXT に変更すると、65,535-byte の行サイズ制限が回避され、BLOB および TEXT のカラムは行サイズに 9 から 12 バイトしか寄与しないため、操作を成功させることができます。
```sql
mysql> CREATE TABLE t (a VARCHAR(10000), b VARCHAR(10000),
    ->        c VARCHAR(10000), d VARCHAR(10000), e VARCHAR(10000),
    ->        f VARCHAR(10000), g TEXT(6000)) ENGINE=MyISAM CHARACTER SET latin1;
Query OK, 0 rows affected (0.01 sec)
```
カラムを TEXT に変更すると MySQL 65,535-byte の行サイズ制限が回避され、可変長カラムの InnoDB オフページストレージによって InnoDB の行サイズ制限が回避されるため、InnoDB テーブルに対する操作は成功します。
```sql
mysql> CREATE TABLE t (a VARCHAR(10000), b VARCHAR(10000),
    ->        c VARCHAR(10000), d VARCHAR(10000), e VARCHAR(10000),
    ->        f VARCHAR(10000), g TEXT(6000)) ENGINE=InnoDB CHARACTER SET latin1;
Query OK, 0 rows affected (0.03 sec)
```
カラムに 32,765 + 2 バイトおよび 32,766 + 2 バイトが必要で、これは最大行サイズ 65,535 バイト内にあるため、テーブル t1 を作成するステートメントは成功します:
```sql
mysql> CREATE TABLE t1
    ->        (c1 VARCHAR(32765) NOT NULL, c2 VARCHAR(32766) NOT NULL)
    ->        ENGINE = InnoDB CHARACTER SET latin1;
Query OK, 0 rows affected (0.03 sec)
```
カラムの長さが 65,535 バイトの最大長内にあるにもかかわらず、行サイズが 65,535 バイトを超えるため、テーブル t2 を作成するステートメントは失敗します:
```sql
mysql> CREATE TABLE t2
    ->        (c1 VARCHAR(65535) NOT NULL)
    ->        ENGINE = InnoDB CHARACTER SET latin1;
ERROR 1118 (42000): Row size too large. The maximum row size for the used table type, not counting BLOBs, is 65535. This includes storage overhead, check the manual. You have to change some columns to TEXT or BLOBs
```
カラム長を 65,533 以下に減らすと、ステートメントは成功します
```sql
mysql> CREATE TABLE t2
    ->        (c1 VARCHAR(65533) NOT NULL)
    ->        ENGINE = InnoDB CHARACTER SET latin1;
Query OK, 0 rows affected (0.03 sec)
```
MyISAM テーブルの場合、NULL カラムは、値が NULL であるかどうかを記録するための追加領域を行内に必要とします。 各 NULL カラムは 1 ビット余分に占め、もっとも近いバイトまで丸められます。
```sql
mysql> CREATE TABLE t3
    ->        (c1 VARCHAR(32765) NULL, c2 VARCHAR(32766) NULL)
    ->        ENGINE = MyISAM CHARACTER SET latin1;
ERROR 1118 (42000): Row size too large. The maximum row size for the used table type, not counting BLOBs, is 65535. This includes storage overhead, check the manual. You have to change some columns to TEXT or BLOBs
```
InnoDB では、行サイズ (データベースページ内にローカルに格納されるデータの場合) は、4KB、8KB、16KB および 32KB の innodb_page_size 設定ではデータベースページの半分未満に制限され、64KB ページでは 16KB 未満に制限されます。

定義されたカラムが 16KB の InnoDB ページの行サイズ制限を超えているため、テーブル t4 を作成するステートメントは失敗します。

```sql
mysql> CREATE TABLE t4 (
    ->        c1 CHAR(255),c2 CHAR(255),c3 CHAR(255),
    ->        c4 CHAR(255),c5 CHAR(255),c6 CHAR(255),
    ->        c7 CHAR(255),c8 CHAR(255),c9 CHAR(255),
    ->        c10 CHAR(255),c11 CHAR(255),c12 CHAR(255),
    ->        c13 CHAR(255),c14 CHAR(255),c15 CHAR(255),
    ->        c16 CHAR(255),c17 CHAR(255),c18 CHAR(255),
    ->        c19 CHAR(255),c20 CHAR(255),c21 CHAR(255),
    ->        c22 CHAR(255),c23 CHAR(255),c24 CHAR(255),
    ->        c25 CHAR(255),c26 CHAR(255),c27 CHAR(255),
    ->        c28 CHAR(255),c29 CHAR(255),c30 CHAR(255),
    ->        c31 CHAR(255),c32 CHAR(255),c33 CHAR(255)
    ->        ) ENGINE=InnoDB ROW_FORMAT=DYNAMIC DEFAULT CHARSET latin1;
ERROR 1118 (42000): Row size too large (> 8126). Changing some columns to TEXT or BLOB may help. In current row format, BLOB prefix of 0 bytes is stored inline.
```
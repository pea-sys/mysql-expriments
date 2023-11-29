# 4.6.2 innochecksum — オフライン InnoDB ファイルチェックサムユーティリティー
innochecksum は、InnoDB ファイルのチェックサムを出力します。 このツールは InnoDB テーブルスペースファイルを読み取って各ページのチェックサムを計算し、計算されたチェックサムを保存されているチェックサムと比較して不一致をレポートします。不一致はページが破損していることを示します。

```
C:\Users\masami> innochecksum --verbose --log="C:\ProgramData\MySQL\MySQL Server 8.2\Data\test.log" "C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\imptest.ibd"

Variables (--variable-name=value)
and boolean options {FALSE|TRUE}  Value (after reading options)
--------------------------------- ----------------------------------------
verbose                           TRUE
count                             FALSE
start-page                        0
end-page                          0
page                              0
strict-check                      crc32
no-check                          FALSE
allow-mismatches                  0
write                             crc32
page-type-summary                 FALSE
page-type-dump                    (No default value)
log                               C:\ProgramData\MySQL\MySQL Server 8.2\Data\test.log
```
ログの中身
```
InnoDB File Checksum Utility.
Filename = C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\imptest.ibd
file C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\imptest.ibd = 114688 bytes (7 pages)
page::0; log sequence number:first = 60352736; second = 60352736
page::0; old style: calculated = 1346100831; recorded = 3048595722
page::0; new style: calculated = 1560558999; crc32 = 3048595722; recorded = 3048595722
page::1; log sequence number:first = 60348883; second = 60348883
page::1; old style: calculated = 4142901009; recorded = 1858165813
page::1; new style: calculated = 3274320469; crc32 = 1858165813; recorded = 1858165813
page::2; log sequence number:first = 60352736; second = 60352736
page::2; old style: calculated = 28530244; recorded = 290872088
page::2; new style: calculated = 2991392283; crc32 = 290872088; recorded = 290872088
page::3; log sequence number:first = 60358746; second = 60358746
page::3; old style: calculated = 2184064252; recorded = 1178047202
page::3; new style: calculated = 3359887474; crc32 = 1178047202; recorded = 1178047202
page::4; log sequence number:first = 60368488; second = 60368488
page::4; old style: calculated = 1992569175; recorded = 2995009462
page::4; new style: calculated = 188311923; crc32 = 2995009462; recorded = 2995009462
page::5; log sequence number:first = 0; second = 0
Page::0 is empty and uncorrupted
page::6; log sequence number:first = 0; second = 0
Page::0 is empty and uncorrupted

```

* --count, -c:ファイル内のページ数を出力して終了します。
```
C:\Users\masami> innochecksum --count "C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\imptest.ibd"
Number of pages:7
```
* --start-page=num, -s num:このページ番号から開始します。
```
C:\Users\masami> innochecksum --start-page=6 "C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\imptest.ibd"
```
* --end-page=num, -e num:このページ番号で終了します。
```
C:\Users\masami> innochecksum --end-page=6 "C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\imptest.ibd"
```
* --page=num, -p num:このページ番号のみをチェックします。
```
C:\Users\masami> innochecksum --page=6 "C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\imptest.ibd"
```
* --strict-check, -C:厳密なチェックサムアルゴリズムを指定します。 
```
C:\Users\masami> innochecksum --strict-check=innodb "C:\ProgramData\MySQL\MySQL Server 8.2\Data\test\imptest.ibd"
[WARNING] innochecksum: innodb_checksum_algorithm is set to "strict_innodb" but the page [page id: space=239, page number=0] contains a valid checksum "crc32".  Accepting the page as valid. Change innodb_checksum_algorithm to "innodb" to silently accept such pages or rewrite all pages so that they contain "innodb" checksum.
[WARNING] innochecksum: innodb_checksum_algorithm is set to "strict_innodb" but the page [page id: space=239, page number=1] contains a valid checksum "crc32".  Accepting the page as valid. Change innodb_checksum_algorithm to "innodb" to silently accept such pages or rewrite all pages so that they contain "innodb" checksum.
[WARNING] innochecksum: innodb_checksum_algorithm is set to "strict_innodb" but the page [page id: space=239, page number=2] contains a valid checksum "crc32".  Accepting the page as valid. Change innodb_checksum_algorithm to "innodb" to silently accept such pages or rewrite all pages so that they contain "innodb" checksum.
[WARNING] innochecksum: innodb_checksum_algorithm is set to "strict_innodb" but the page [page id: space=239, page number=3] contains a valid checksum "crc32".  Accepting the page as valid. Change innodb_checksum_algorithm to "innodb" to silently accept such pages or rewrite all pages so that they contain "innodb" checksum.
[WARNING] innochecksum: innodb_checksum_algorithm is set to "strict_innodb" but the page [page id: space=239, page number=4] contains a valid checksum "crc32".  Accepting the page as valid. Change innodb_checksum_algorithm to "innodb" to silently accept such pages or rewrite all pages so that they contain "innodb" checksum.
```
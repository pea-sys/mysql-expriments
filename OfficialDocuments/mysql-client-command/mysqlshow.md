# 4.5.7 mysqlshow — データベース、テーブル、およびカラム情報の表示
mysqlshow クライアントは、どのデータベース、そのテーブル、またはテーブルのカラムまたはインデックスが存在するかを迅速に確認するために使用できます。

```
C:\Users\masami>mysqlshow -u root -p
Enter password: **********
+--------------------+
|     Databases      |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sakila             |
| sample             |
| sys                |
| test               |
| world              |
+--------------------+

C:\Users\masami>mysqlshow test -u root -p
Enter password: **********
Database: test
+---------+
| Tables  |
+---------+
| imptest |
+---------+

C:\Users\masami>mysqlshow test imptest -u root -p
Enter password: **********
Database: test  Table: imptest
+-------+-------------+--------------------+------+-----+---------+-------+---------------------------------+---------+
| Field | Type        | Collation          | Null | Key | Default | Extra | Privileges                      | Comment |
+-------+-------------+--------------------+------+-----+---------+-------+---------------------------------+---------+
| id    | int         |                    | YES  |     |         |       | select,insert,update,references |         |
| n     | varchar(30) | utf8mb4_0900_ai_ci | YES  |     |         |       | select,insert,update,references |         |
+-------+-------------+--------------------+------+-----+---------+-------+---------------------------------+---------+

C:\Users\masami>mysqlshow test imptest id -u root -p
Enter password: **********
Database: test  Table: imptest  Wildcard: id
+-------+------+-----------+------+-----+---------+-------+---------------------------------+---------+
| Field | Type | Collation | Null | Key | Default | Extra | Privileges                      | Comment |
+-------+------+-----------+------+-----+---------+-------+---------------------------------+---------+
| id    | int  |           | YES  |     |         |       | select,insert,update,references |         |
+-------+------+-----------+------+-----+---------+-------+---------------------------------+---------+
```

* --count:テーブルごとの行の数を表示
```
C:\Users\masami>mysqlshow --count test -u root -p
Enter password: **********
Database: test
+---------+----------+------------+
| Tables  | Columns  | Total Rows |
+---------+----------+------------+
| imptest |        2 |          4 |
+---------+----------+------------+
1 row in set.
```
* --show-table-type:テーブルのタイプを示すカラムを表示
```
C:\Users\masami>mysqlshow --show-table-type test -u root -p
Enter password: **********
Database: test
+---------+------------+
| Tables  | table_type |
+---------+------------+
| imptest | BASE TABLE |
+---------+------------+
```

* --status:各テーブルの追加情報を表示
```
C:\Users\masami>mysqlshow --status sakila -u root -p
Enter password: **********
Database: sakila
+----------------------------+--------+---------+------------+-------+----------------+-------------+-----------------+--------------+-----------+----------------+---------------------+-------------+------------+--------------------+----------+----------------+---------+
| Name                       | Engine | Version | Row_format | Rows  | Avg_row_length | Data_length | Max_data_length | Index_length | Data_free | Auto_increment | Create_time         | Update_time | Check_time | Collation          | Checksum | Create_options | Comment |
+----------------------------+--------+---------+------------+-------+----------------+-------------+-----------------+--------------+-----------+----------------+---------------------+-------------+------------+--------------------+----------+----------------+---------+
| actor                      | InnoDB | 10      | Dynamic    | 200   | 81             | 16384       | 0               | 16384        | 0         | 201            | 2023-11-23 20:12:15 |             |            | utf8mb4_0900_ai_ci |          |                |         |
| actor_info                 |        |         |            |       |                |             |                 |              |           |                | 2023-11-23 08:13:15 |             |            |                    |          |                | VIEW    |
| address                    | InnoDB | 10      | Dynamic    | 603   | 163            | 98304       | 0               | 16384        | 0         |                | 2023-11-23 20:12:15 |             |            | utf8mb4_0900_ai_ci |          |                |         |
| category                   | InnoDB | 10      | Dynamic    | 16    | 1024           | 16384       | 0               | 0            | 0         |                | 2023-11-23 20:12:16 |             |            | utf8mb4_0900_ai_ci |          |                |         |
| city                       | InnoDB | 10      | Dynamic    | 600   | 81             | 49152       | 0               | 16384        | 0         |                | 2023-11-23 20:12:16 |             |            | utf8mb4_0900_ai_ci |          |                |         |
| country                    | InnoDB | 10      | Dynamic    | 109   | 150            | 16384       | 0               | 0            | 0         |                | 2023-11-23 20:12:16 |             |            | utf8mb4_0900_ai_ci |          |                |         |
| customer                   | InnoDB | 10      | Dynamic    | 599   | 136            | 81920       | 0               | 49152        | 0         |                | 2023-11-23 20:12:16 |             |            | utf8mb4_0900_ai_ci |          |                |         |
| customer_list              |        |         |            |       |                |             |                 |              |           |                | 2023-11-23 08:13:15 |             |            |                    |          |                | VIEW    |
| film                       | InnoDB | 10      | Dynamic    | 1000  | 180            | 180224      | 0               | 81920        | 0         |                | 2023-11-23 20:12:16 |             |            | utf8mb4_0900_ai_ci |          |                |         |
| film_actor                 | InnoDB | 10      | Dynamic    | 5462  | 32             | 180224      | 0               | 81920        | 0         |                | 2023-11-23 20:12:16 |             |            | utf8mb4_0900_ai_ci |          |                |         |
| film_category              | InnoDB | 10      | Dynamic    | 1000  | 49             | 49152       | 0               | 16384        | 0         |                | 2023-11-23 20:12:16 |             |            | utf8mb4_0900_ai_ci |          |                |         |
| film_list                  |        |         |            |       |                |             |                 |              |           |                | 2023-11-23 08:13:15 |             |            |                    |          |                | VIEW    |
| film_text                  | InnoDB | 10      | Dynamic    | 1000  | 180            | 180224      | 0               | 16384        | 0         |                | 2023-11-23 20:12:16 |             |            | utf8mb4_0900_ai_ci |          |                |         |
| inventory                  | InnoDB | 10      | Dynamic    | 4581  | 35             | 163840      | 0               | 147456       | 0         |                | 2023-11-23 20:12:16 |             |            | utf8mb4_0900_ai_ci |          |                |         |
| language                   | InnoDB | 10      | Dynamic    | 6     | 2730           | 16384       | 0               | 0            | 0         |                | 2023-11-23 20:12:17 |             |            | utf8mb4_0900_ai_ci |          |                |         |
| nicer_but_slower_film_list |        |         |            |       |                |             |                 |              |           |                | 2023-11-23 08:13:15 |             |            |                    |          |                | VIEW    |
| payment                    | InnoDB | 10      | Dynamic    | 16125 | 98             | 1589248     | 0               | 573440       | 0         |                | 2023-11-23 20:12:17 |             |            | utf8mb4_0900_ai_ci |          |                |         |
| rental                     | InnoDB | 10      | Dynamic    | 15902 | 99             | 1589248     | 0               | 983040       | 0         |                | 2023-11-23 20:12:17 |             |            | utf8mb4_0900_ai_ci |          |                |         |
| sales_by_film_category     |        |         |            |       |                |             |                 |              |           |                | 2023-11-23 08:13:15 |             |            |                    |          |                | VIEW    |
| sales_by_store             |        |         |            |       |                |             |                 |              |           |                | 2023-11-23 08:13:15 |             |            |                    |          |                | VIEW    |
| staff                      | InnoDB | 10      | Dynamic    | 5     | 13107          | 65536       | 0               | 32768        | 0         |                | 2023-11-23 20:12:17 |             |            | utf8mb4_0900_ai_ci |          |                |         |
| staff_list                 |        |         |            |       |                |             |                 |              |           |                | 2023-11-23 08:13:15 |             |            |                    |          |                | VIEW    |
| store                      | InnoDB | 10      | Dynamic    | 2     | 8192           | 16384       | 0               | 32768        | 0         |                | 2023-11-23 20:12:17 |             |            | utf8mb4_0900_ai_ci |          |                |         |
+----------------------------+--------+---------+------------+-------+----------------+-------------+-----------------+--------------+-----------+----------------+---------------------+-------------+------------+--------------------+----------+----------------+---------+
```
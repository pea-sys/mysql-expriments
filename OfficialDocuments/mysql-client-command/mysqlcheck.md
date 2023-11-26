## 4.5.3 mysqlcheck — テーブル保守プログラム

mysqlcheck クライアントでは、テーブルの保守 (テーブルの検査、修復、最適化、分析) を実行します。


* --all-databases:データベース内のテーブルをすべて確認
```
C:\Users\masami>mysqlcheck --all-databases -u root -p
Enter password: **********
mysql.columns_priv                                 OK
mysql.component                                    OK
mysql.db                                           OK
mysql.default_roles                                OK
mysql.engine_cost                                  OK
mysql.func                                         OK
mysql.general_log                                  OK
mysql.global_grants                                OK
mysql.gtid_executed                                OK
mysql.help_category                                OK
mysql.help_keyword                                 OK
mysql.help_relation                                OK
mysql.help_topic                                   OK
mysql.innodb_index_stats                           OK
mysql.innodb_table_stats                           OK
mysql.ndb_binlog_index                             OK
mysql.password_history                             OK
mysql.plugin                                       OK
mysql.procs_priv                                   OK
mysql.proxies_priv                                 OK
mysql.replication_asynchronous_connection_failover OK
mysql.replication_asynchronous_connection_failover_managed OK
mysql.replication_group_configuration_version      OK
mysql.replication_group_member_actions             OK
mysql.role_edges                                   OK
mysql.server_cost                                  OK
mysql.servers                                      OK
mysql.slave_master_info                            OK
mysql.slave_relay_log_info                         OK
mysql.slave_worker_info                            OK
mysql.slow_log                                     OK
mysql.tables_priv                                  OK
mysql.time_zone                                    OK
mysql.time_zone_leap_second                        OK
mysql.time_zone_name                               OK
mysql.time_zone_transition                         OK
mysql.time_zone_transition_type                    OK
mysql.user                                         OK
sakila.actor                                       OK
sakila.address                                     OK
sakila.category                                    OK
sakila.city                                        OK
sakila.country                                     OK
sakila.customer                                    OK
sakila.film                                        OK
sakila.film_actor                                  OK
sakila.film_category                               OK
sakila.film_text                                   OK
sakila.inventory                                   OK
sakila.language                                    OK
sakila.payment                                     OK
sakila.rental                                      OK
sakila.staff                                       OK
sakila.store                                       OK
sys.sys_config                                     OK
world.city                                         OK
world.country                                      OK
world.countrylanguage                              OK
```
* --all-in-1:各データベースに対して、そのデータベースのすべてのテーブルを指定する単一のステートメントを実行
```
C:\Users\masami>mysqlcheck --all-in-1 sakila -u root -p
Enter password: **********
sakila.actor                                       OK
sakila.address                                     OK
sakila.category                                    OK
sakila.city                                        OK
sakila.country                                     OK
sakila.customer                                    OK
sakila.film                                        OK
sakila.film_actor                                  OK
sakila.film_category                               OK
sakila.film_text                                   OK
sakila.inventory                                   OK
sakila.language                                    OK
sakila.payment                                     OK
sakila.rental                                      OK
sakila.staff                                       OK
sakila.store                                       OK
```
* --analyze:テーブルを分析します
```
C:\Users\masami>mysqlcheck --analyze sakila -u root -p
Enter password: **********
sakila.actor                                       OK
sakila.address                                     OK
sakila.category                                    OK
sakila.city                                        OK
sakila.country                                     OK
sakila.customer                                    OK
sakila.film                                        OK
sakila.film_actor                                  OK
sakila.film_category                               OK
sakila.film_text                                   OK
sakila.inventory                                   OK
sakila.language                                    OK
sakila.payment                                     OK
sakila.rental                                      OK
sakila.staff                                       OK
sakila.store                                       OK
```
* --auto-repair:確認されたテーブルが破損していた場合、自動的に修復
```
C:\Users\masami>mysqlcheck --auto-repair sakila -u root -p
Enter password: **********
sakila.actor                                       OK
sakila.address                                     OK
sakila.category                                    OK
sakila.city                                        OK
sakila.country                                     OK
sakila.customer                                    OK
sakila.film                                        OK
sakila.film_actor                                  OK
sakila.film_category                               OK
sakila.film_text                                   OK
sakila.inventory                                   OK
sakila.language                                    OK
sakila.payment                                     OK
sakila.rental                                      OK
sakila.staff                                       OK
```
* --check:テーブルにエラーがないか確認
```
C:\Users\masami>mysqlcheck --check sakila -u root -p
Enter password: **********
sakila.actor                                       OK
sakila.address                                     OK
sakila.category                                    OK
sakila.city                                        OK
sakila.country                                     OK
sakila.customer                                    OK
sakila.film                                        OK
sakila.film_actor                                  OK
sakila.film_category                               OK
sakila.film_text                                   OK
sakila.inventory                                   OK
sakila.language                                    OK
sakila.payment                                     OK
sakila.rental                                      OK
sakila.staff                                       OK
sakila.store                                       OK
```
* --check-only-changed:最後に行われた検査以降に変更されたテーブルのみをチェック
```
C:\Users\masami>mysqlcheck --check-only-changed sakila -u root -p
Enter password: **********
sakila.actor                                       OK
sakila.address                                     OK
sakila.category                                    OK
sakila.city                                        OK
sakila.country                                     OK
sakila.customer                                    OK
sakila.film                                        OK
sakila.film_actor                                  OK
sakila.film_category                               OK
sakila.film_text                                   OK
sakila.inventory                                   OK
sakila.language                                    OK
sakila.payment                                     OK
sakila.rental                                      OK
sakila.staff                                       OK
sakila.store                                       OK
```
* --check-upgrade:CHECK TABLE を FOR UPGRADE オプションで呼び出し
```
C:\Users\masami>mysqlcheck --check-upgrade sakila -u root -p
Enter password: **********
sakila.actor                                       OK
sakila.address                                     OK
sakila.category                                    OK
sakila.city                                        OK
sakila.country                                     OK
sakila.customer                                    OK
sakila.film                                        OK
sakila.film_actor                                  OK
sakila.film_category                               OK
sakila.film_text                                   OK
sakila.inventory                                   OK
sakila.language                                    OK
sakila.payment                                     OK
sakila.rental                                      OK
sakila.staff                                       OK
sakila.store                                       OK
```

* --extended:テーブルをチェックして修復
```
C:\Users\masami>mysqlcheck --extended sakila -u root -p
Enter password: **********
sakila.actor                                       OK
sakila.address                                     OK
sakila.category                                    OK
sakila.city                                        OK
sakila.country                                     OK
sakila.customer                                    OK
sakila.film                                        OK
sakila.film_actor                                  OK
sakila.film_category                               OK
sakila.film_text                                   OK
sakila.inventory                                   OK
sakila.language                                    OK
sakila.payment                                     OK
sakila.rental                                      OK
sakila.staff                                       OK
sakila.store                                       OK
```
* --fast:正しく閉じられていないテーブルのみを確認
```
C:\Users\masami>mysqlcheck --fast sakila -u root -p
Enter password: **********
sakila.actor                                       OK
sakila.address                                     OK
sakila.category                                    OK
sakila.city                                        OK
sakila.country                                     OK
sakila.customer                                    OK
sakila.film                                        OK
sakila.film_actor                                  OK
sakila.film_category                               OK
sakila.film_text                                   OK
sakila.inventory                                   OK
sakila.language                                    OK
sakila.payment                                     OK
sakila.rental                                      OK
sakila.staff                                       OK
sakila.store                                       OK
```

* --medium-check:--extended 操作よりも速いチェックを実行
```
C:\Users\masami>mysqlcheck --medium-check sakila -u root -p
Enter password: **********
sakila.actor                                       OK
sakila.address                                     OK
sakila.category                                    OK
sakila.city                                        OK
sakila.country                                     OK
sakila.customer                                    OK
sakila.film                                        OK
sakila.film_actor                                  OK
sakila.film_category                               OK
sakila.film_text                                   OK
sakila.inventory                                   OK
sakila.language                                    OK
sakila.payment                                     OK
sakila.rental                                      OK
sakila.staff                                       OK
sakila.store                                       OK
```
* --optimize:テーブルを最適化します
```
C:\Users\masami>mysqlcheck --optimize sakila -u root -p
Enter password: **********
sakila.actor
note     : Table does not support optimize, doing recreate + analyze instead
status   : OK
sakila.address
note     : Table does not support optimize, doing recreate + analyze instead
status   : OK
sakila.category
note     : Table does not support optimize, doing recreate + analyze instead
status   : OK
sakila.city
note     : Table does not support optimize, doing recreate + analyze instead
status   : OK
sakila.country
note     : Table does not support optimize, doing recreate + analyze instead
status   : OK
sakila.customer
note     : Table does not support optimize, doing recreate + analyze instead
status   : OK
sakila.film
note     : Table does not support optimize, doing recreate + analyze instead
status   : OK
sakila.film_actor
note     : Table does not support optimize, doing recreate + analyze instead
status   : OK
sakila.film_category
note     : Table does not support optimize, doing recreate + analyze instead
status   : OK
sakila.film_text
note     : Table does not support optimize, doing recreate + analyze instead
status   : OK
sakila.inventory
note     : Table does not support optimize, doing recreate + analyze instead
status   : OK
sakila.language
note     : Table does not support optimize, doing recreate + analyze instead
status   : OK
sakila.payment
note     : Table does not support optimize, doing recreate + analyze instead
status   : OK
sakila.rental
note     : Table does not support optimize, doing recreate + analyze instead
status   : OK
sakila.staff
note     : Table does not support optimize, doing recreate + analyze instead
status   : OK
sakila.store
note     : Table does not support optimize, doing recreate + analyze instead
status   : OK
```

* --quick:最速のチェック方法
```
C:\Users\masami>mysqlcheck --quick sakila -u root -p
Enter password: **********
sakila.actor                                       OK
sakila.address                                     OK
sakila.category                                    OK
sakila.city                                        OK
sakila.country                                     OK
sakila.customer                                    OK
sakila.film                                        OK
sakila.film_actor                                  OK
sakila.film_category                               OK
sakila.film_text                                   OK
sakila.inventory                                   OK
sakila.language                                    OK
sakila.payment                                     OK
sakila.rental                                      OK
sakila.staff                                       OK
sakila.store                                       OK
```
* --repair:一意ではではない一意なキー以外のほぼすべてを修正できる修復を実行
```
C:\Users\masami>mysqlcheck --repair sakila -u root -p
Enter password: **********
sakila.actor
note     : The storage engine for the table doesn't support repair
sakila.address
note     : The storage engine for the table doesn't support repair
sakila.category
note     : The storage engine for the table doesn't support repair
sakila.city
note     : The storage engine for the table doesn't support repair
sakila.country
note     : The storage engine for the table doesn't support repair
sakila.customer
note     : The storage engine for the table doesn't support repair
sakila.film
note     : The storage engine for the table doesn't support repair
sakila.film_actor
note     : The storage engine for the table doesn't support repair
sakila.film_category
note     : The storage engine for the table doesn't support repair
sakila.film_text
note     : The storage engine for the table doesn't support repair
sakila.inventory
note     : The storage engine for the table doesn't support repair
sakila.language
note     : The storage engine for the table doesn't support repair
sakila.payment
note     : The storage engine for the table doesn't support repair
sakila.rental
note     : The storage engine for the table doesn't support repair
sakila.staff
note     : The storage engine for the table doesn't support repair
sakila.store
note     : The storage engine for the table doesn't support repair
```
* --use-frm	MyISAM:テーブルの修復操作用
```
C:\Users\masami>mysqlcheck --use-frm sakila -u root -p
Enter password: **********
sakila.actor                                       OK
sakila.address                                     OK
sakila.category                                    OK
sakila.city                                        OK
sakila.country                                     OK
sakila.customer                                    OK
sakila.film                                        OK
sakila.film_actor                                  OK
sakila.film_category                               OK
sakila.film_text                                   OK
sakila.inventory                                   OK
sakila.language                                    OK
sakila.payment                                     OK
sakila.rental                                      OK
sakila.staff                                       OK
sakila.store                                       OK
```
### 20.5.1 X プラグイン のインストールの確認

X プラグイン は MySQL 8 ではデフォルトで有効になっているため、MySQL 8 をインストールまたはアップグレードするとプラグインが使用可能になります

```
C:\Users\masami>mysqlsh -u root --sqlc -P 3306 -e "SHOW plugins"
Please provide the password for 'root@localhost:3306': **********
Save password for 'root@localhost:3306'? [Y]es/[N]o/Ne[v]er (default No): y
Name    Status  Type    Library License
binlog  ACTIVE  STORAGE ENGINE  NULL    GPL
sha256_password ACTIVE  AUTHENTICATION  NULL    GPL
caching_sha2_password   ACTIVE  AUTHENTICATION  NULL    GPL
sha2_cache_cleaner      ACTIVE  AUDIT   NULL    GPL
daemon_keyring_proxy_plugin     ACTIVE  DAEMON  NULL    GPL
CSV     ACTIVE  STORAGE ENGINE  NULL    GPL
MEMORY  ACTIVE  STORAGE ENGINE  NULL    GPL
InnoDB  ACTIVE  STORAGE ENGINE  NULL    GPL
INNODB_TRX      ACTIVE  INFORMATION SCHEMA      NULL    GPL
INNODB_CMP      ACTIVE  INFORMATION SCHEMA      NULL    GPL
INNODB_CMP_RESET        ACTIVE  INFORMATION SCHEMA      NULL    GPL
INNODB_CMPMEM   ACTIVE  INFORMATION SCHEMA      NULL    GPL
INNODB_CMPMEM_RESET     ACTIVE  INFORMATION SCHEMA      NULL    GPL
INNODB_CMP_PER_INDEX    ACTIVE  INFORMATION SCHEMA      NULL    GPL
INNODB_CMP_PER_INDEX_RESET      ACTIVE  INFORMATION SCHEMA      NULL    GPL
INNODB_BUFFER_PAGE      ACTIVE  INFORMATION SCHEMA      NULL    GPL
INNODB_BUFFER_PAGE_LRU  ACTIVE  INFORMATION SCHEMA      NULL    GPL
INNODB_BUFFER_POOL_STATS        ACTIVE  INFORMATION SCHEMA      NULL    GPL
INNODB_TEMP_TABLE_INFO  ACTIVE  INFORMATION SCHEMA      NULL    GPL
INNODB_METRICS  ACTIVE  INFORMATION SCHEMA      NULL    GPL
INNODB_FT_DEFAULT_STOPWORD      ACTIVE  INFORMATION SCHEMA      NULL    GPL
INNODB_FT_DELETED       ACTIVE  INFORMATION SCHEMA      NULL    GPL
INNODB_FT_BEING_DELETED ACTIVE  INFORMATION SCHEMA      NULL    GPL
INNODB_FT_CONFIG        ACTIVE  INFORMATION SCHEMA      NULL    GPL
INNODB_FT_INDEX_CACHE   ACTIVE  INFORMATION SCHEMA      NULL    GPL
INNODB_FT_INDEX_TABLE   ACTIVE  INFORMATION SCHEMA      NULL    GPL
INNODB_TABLES   ACTIVE  INFORMATION SCHEMA      NULL    GPL
INNODB_TABLESTATS       ACTIVE  INFORMATION SCHEMA      NULL    GPL
INNODB_INDEXES  ACTIVE  INFORMATION SCHEMA      NULL    GPL
INNODB_TABLESPACES      ACTIVE  INFORMATION SCHEMA      NULL    GPL
INNODB_COLUMNS  ACTIVE  INFORMATION SCHEMA      NULL    GPL
INNODB_VIRTUAL  ACTIVE  INFORMATION SCHEMA      NULL    GPL
INNODB_CACHED_INDEXES   ACTIVE  INFORMATION SCHEMA      NULL    GPL
INNODB_SESSION_TEMP_TABLESPACES ACTIVE  INFORMATION SCHEMA      NULL    GPL
MyISAM  ACTIVE  STORAGE ENGINE  NULL    GPL
MRG_MYISAM      ACTIVE  STORAGE ENGINE  NULL    GPL
PERFORMANCE_SCHEMA      ACTIVE  STORAGE ENGINE  NULL    GPL
TempTable       ACTIVE  STORAGE ENGINE  NULL    GPL
ARCHIVE ACTIVE  STORAGE ENGINE  NULL    GPL
BLACKHOLE       ACTIVE  STORAGE ENGINE  NULL    GPL
FEDERATED       DISABLED        STORAGE ENGINE  NULL    GPL
ndbcluster      DISABLED        STORAGE ENGINE  NULL    GPL
ndbinfo DISABLED        STORAGE ENGINE  NULL    GPL
ndb_transid_mysql_connection_map        DISABLED        INFORMATION SCHEMA      NULL    GPL
ngram   ACTIVE  FTPARSER        NULL    GPL
mysqlx_cache_cleaner    ACTIVE  AUDIT   NULL    GPL
mysqlx  ACTIVE  DAEMON  NULL    GPL
mysql_native_password   ACTIVE  AUTHENTICATION  NULL    GPL
```

### 20.5.7 X プラグイン の監視

events_statements_history コンシューマが有効かどうかを確認します。 次のコマンドを発行します

```
 MySQL  127.0.0.1:33060+ ssl  world_x  SQL > SELECT enabled FROM performance_schema.setup_consumers WHERE NAME = 'events_statements_history';
+---------+
| enabled |
+---------+
| YES     |
+---------+
1 row in set (0.0006 sec)
```

すべてのインストゥルメントがコンシューマにデータをレポートするかどうかを確認します

```
 MySQL  127.0.0.1:33060+ ssl  world_x  SQL > SELECT NAME, ENABLED, TIMED FROM performance_schema.setup_instruments WHERE NAME LIKE 'statement/%' AND NOT (ENABLED and TIMED);
Empty set (0.0038 sec)
```

現在の接続のスレッド ID を取得します

```
 MySQL  127.0.0.1:33060+ ssl  world_x  SQL > SELECT thread_id INTO @id FROM performance_schema.threads WHERE processlist_id=connection_id();
Query OK, 1 row affected (0.0057 sec)
```

生成された SQL を表示する X DevAPI CRUD 操作を実行します

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > db.countryinfo.find("Name = :country").bind("country", "Italy")
{
    "GNP": 1161755,
    "_id": "00005de917d8000000000000006a",
    "Code": "ITA",
    "Name": "Italy",
    "Airports": [],
    "IndepYear": 1861,
    "geography": {
        "Region": "Southern Europe",
        "Continent": "Europe",
        "SurfaceArea": 301316
    },
    "government": {
        "HeadOfState": "Carlo Azeglio Ciampi",
        "GovernmentForm": "Republic"
    },
    "demographics": {
        "Population": 57680000,
        "LifeExpectancy": 79
    }
}
1 document in set (0.0108 sec)
```

このスレッド ID によって行われた最後の SQL クエリーを表示します

```
 MySQL  127.0.0.1:33060+ ssl  world_x  Py > \sql SELECT THREAD_ID, MYSQL_ERRNO,SQL_TEXT FROM performance_schema.events_statements_history WHERE THREAD_ID=@id ORDER BY TIMER_START DESC LIMIT 1;
+-----------+-------------+--------------------------------------------------------------------------------------+
| THREAD_ID | MYSQL_ERRNO | SQL_TEXT                                                                             |
+-----------+-------------+--------------------------------------------------------------------------------------+
|        31 |           0 | SELECT doc FROM `world_x`.`countryinfo` WHERE (JSON_EXTRACT(doc,'$.Name') = 'Italy') |
+-----------+-------------+--------------------------------------------------------------------------------------+
1 row in set (0.0082 sec)
```

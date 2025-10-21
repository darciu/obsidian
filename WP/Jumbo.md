listowanie directories

```
hdfs dfs -ls hdfs://jumbo/
```

tworzy katalog:

```
hdfs dfs -mkdir hdfs://jumbo/warehouse/tablespace/external/private_bibd.db/tatabela
```

konfiguracja tabeli
```
hdfs ec -setPolicy -policy RS-6-3-1024k -path hdfs://jumbo/warehouse/tablespace/external/prj_bdc.db/moderacja_daily_summary
```

usuwanie zawartości folderu bez samego folderu
```
hdfs dfs -rm /path/to/hive/folder/*
```

usuwanie całego folderu z zawartością rekurencyjnie
```
hdfs dfs -rm -r /path/to/hive/folder/
```

migracja danych
```
hadoop distcp -Dmapreduce.job.hdfs-servers.token-renewal.exclude=nameservice1 -pb -m 50 -skipcrccheck hdfs://nameservice1/user/hive/warehouse/private_bibd.db/moderacja_opinie_daily/log_date=2024-0[3-6]-* hdfs://jumbo/warehouse/tablespace/external/private_bibd.db/moderacja_opinie_daily/
```

naprawa tabeli po migracji
```
msck REPAIR TABLE private_bibd.moderacja_opinie_daily
```

kopiowanie danych

```
hdfs dfs -cp hdfs://jumbo/warehouse/tablespace/external/public_bibd.db/gemius_traffic/stat_type=H/log_date=2024-10-22 hdfs://jumbo/warehouse/tablespace/external/public_bibd.db/gemius_traffic/stat_type=H/log_date=2024-10-23
```

pobieranie danych
```

```
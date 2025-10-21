Pobieranie danych z Clickhouse

```
clickhouse-client \
--host clickhouse.analitics-ac.dc-2.lb.dcwp.pl \
--user dgiemza \
--password  \
--multiquery \
--query "" \
--format Parquet > data.parquet
```

Multiquery
```
clickhouse-client \
  --host clickhouse.analitics-ac.dc-2.lb.dcwp.pl \
  --user dgiemza \
  --password  \
  --multiquery \
  --queries-file all.sql 
```

Hosty:
Clickhouse Analitics
```
clickhouse.analitics-ac.dc-2.lb.dcwp.pl
```

Clickhouse MALogger
```
clickhouse.malogger.dc-2.lb.dcwp.pl
```



Dokumentacja dużej ćmy (Clickhouse Analitics: moth.contentData_all)
https://confluence.grupawp.pl/display/CMA/moth.contentData_all

Dokumentacja małej ćmy (Clickhouse MAlogger: higlogger.rcBidData_all)
https://confluence.grupawp.pl/display/CMA/higlogger.rcBidData_all

Różnica pomiędzy klastrami Clickhouse Analitics i MAlogger (moth.contentData_all) - Analitics ma inne ogranicznia i tam jest tylko reprezentacja danych. Dane fizycznie są na MAlogger - więc produkcyjnie należy uderzać do tej tabeli, gdyż jest szybszy dostęp do danych.

Dane z kafki:
https://confluence.grupawp.pl/display/AC/ac.kafka_logs


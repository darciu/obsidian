Komunikaty kafkowe to pary key - value.



Aby korzystać z kafkat, trzeba wejść na r-server
```
ssh rserver.dc-2.tools.dcwp.pl
```

jako prj_bdc
```
sudo su - prj_bdc
```

**Zmienne środowiskowe**

(prod)
```
export KAFKAT_SERVER=node-1.kafka-2.dc-2.tools.dcwp.pl:9092,node-2.kafka-2.dc-2.tools.dcwp.pl:9092,node-3.kafka-2.dc-2.tools.dcwp.pl:9092,node-4.kafka-2.dc-2.tools.dcwp.pl:9092,node-5.kafka-2.dc-2.tools.dcwp.pl:9092
```

(dev)
```
export KAFKAT_SERVER=node-1.kafka-1.dc-2.dev.dcwp.pl:9092,node-2.kafka-1.dc-2.dev.dcwp.pl:9092,node-3.kafka-1.dc-2.dev.dcwp.pl:9092
```

**Działania w Kafkat**

wyświetlanie topic'ów zawierających w nazwie ...
```
kafkat -l | cut -d " " -f 1 |sort -u | grep wave
```

czytanie topicu
```
kafkat dot.i-json-wave
```

czytanie ostatnich 5 wierszy z topicu
```
kafkat dot.i-json-wave::e-5:e | jq . -c
```
(-c wyświetla każdy json w nowej linii)

zapis streamu do pliku
```
kafkat dot.i-json-wave::e-20:e | jq . -c > kafkat.txt
```

czytanie pola user_actions dla mojego statidu
```
kafkat dot.i-json-wave-user-actions | jq 'select(.cookie.statid=="a87ded9286ab2317bef06daa6708cd41:f5dce6:1613861108:v3").user_actions'
```

aby zobaczyć klucze komunikatów należy podać parametr
```
kafkat --show-keys dot.i-json-wave:0:e:e
```


**Aplikacja Kafki**

Wejść w lokalizację
```
/opt/tech/prj_bdc/work/kafka_2.12-2.3.1
```

Eksportować lokalizację nodów kafki deweloperskiej
```
export KAFKAT_SERVER=node-1.kafka-1.dc-2.dev.dcwp.pl:9092,node-2.kafka-1.dc-2.dev.dcwp.pl:9092,node-3.kafka-1.dc-2.dev.dcwp.pl:9092
```

i wpisać
```
./bin/kafka-topics.sh --create --bootstrap-server $KAFKA_SERVERS --replication-factor 1 --partitions 1 --topic nazwa-topiku-test
```

tą aplikacją można też czytać topiki za pomocą consumera kafki (dla nowszych topików)
```
./bin/kafka-console-consumer.sh --bootstrap-server $KAFKAT_SERVER --topic dgiemza-slot-status-dev --max-messages 10 | jq .
```


**Pythonowy klient Kafki**

oraz jego użycie
```
from kafka import KafkaConsumer


consumer2 = KafkaConsumer('topik',
                         bootstrap_servers=['
                         node-1.kafka-1.dc-2.tools.dcwp.pl:9092'
                         ,'node-2.kafka-1.dc-2.tools.dcwp.pl:9092'
                         ,'node-3.kafka-1.dc-2.tools.dcwp.pl:9092'
                         ,'node-4.kafka-1.dc-2.tools.dcwp.pl:9092'
                         ,'node-5.kafka-1.dc-2.tools.dcwp.pl:9092'],
                         api_version_auto_timeout_ms=100000,
                         value_deserializer=lambda m: json.loads(m.decode('utf-8')),
                         consumer_timeout_ms=28000
                         
pool1 = [message.value for message in consumer2]


pool1_df = pd.DataFrame([{'id':it[0]['id']
                          , 'datetime':it[0]['datetime']                   
                          , 'shows':it[0]['data'][5]['aggregates']['shows']
                          , 'shows_mobile': it[0]['data'][5]['aggregates']['shows_mobile']
                          , 'clicks':it[0]['data'][5]['aggregates']['clicks']
                          , 'clicks_mobile':it[0]['data'][5]['aggregates']['clicks_mobile']
                          , 'ucb' :it[0]['data'][5]['decisions']['ucb']
                          , 'weightedClicks' :it[0]['data'][5]['aggregates']['weightedClicks']
                          , 'weightedClicks_mobile' :it[0]['data'][5]['aggregates']['weightedClicks_mobile']
                          , 'weightedCtr':it[0]['data'][5]['decisions']['weightedCtr']
                          , 'weightedUcb' :it[0]['data'][5]['decisions']['weightedUcb']
                          , 'weightedUcb_mobile' :it[0]['data'][5]['decisions']['weightedUcb_mobile']
                          
                         } for it in pool1 if it[0]['data'][5]['clusteringKey'] == 'default'])
                 
```

**Task do zakładania topiku Kafki**
[https://jira.grupawp.pl/browse/OPAK-310](https://jira.grupawp.pl/browse/OPAK-310 "https://jira.grupawp.pl/browse/opak-310")

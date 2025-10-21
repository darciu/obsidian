Airflow dla FS
[https://airflow2-ac.grupawp.pl/home?search=aip](https://airflow2-ac.grupawp.pl/home?search=aip "https://airflow2-ac.grupawp.pl/home?search=aip")

Repo
https://git.partnerzy.dcwp.pl/marketingcloud/high-cpm-airflow-dags

W go.mod znajdują się wszelkie zależności i ich wersje. Jeśli chcemy podbić wersję jakiejś zależności, to właśnie tam. W go.mod określa się też nazwę modułu. Dzięki niemu importy pojawiają się automatycznie w poszczególnych plikach .go.

Folder vendor w projekcie zawiera lokalne kopie zewnętrznych zależności (bibliotek i pakietów) używanych w danym projekcie. Podobna funkcjonalność do venv - izolacja i ujednolicenie używanych w projekcie zależności.

Ficzer ma za zadanie zwracać dwie kolumny: klucz oraz wartość

pola w funkcji ficzera:
- name - jak nazywa się DAG na Airflow
- aliasName - to w jaki sposób po przetworzeniu przez FS ta kolumna będzie się nazywać
- columnName - nazwa kolumny, którą w query SQL zwraca ficzer. Dzięki temu oznaczeniu FS wie jaką kolumnę brać z SQL do ficzera
- entity - wiele ficzerów może korzystać z jednej encji;
	- joinable_entity - do datasetów; zalecany wybór  
	- not_joinable_entity - takiego ficzera nie będzie można joinować do datasetu; może służyć jako pewna miara w konkretnej logice innego procesu  
- entitySql - deifniowane klucza
- clickhouseHopping - obiekt zbierający wszystkie wcześniej zdefiniowane zmienne i inne parametry 
- backfill - ficzery do datasetu na true
- IsValidatable - walidacja ficzera, która można napisać
- DateColumnName i TimestampColumnName - należy wskazać jak nazywają się kolumny date timestamp  


w definicji encji:
	KeyHandler -
	KeyTranslator - 
te pola finalnie tworzą XXHash32

funkcja GetSql - jest wstrzykiwana do query SQL; używany w kilku miejscach (ficzer, dataset) stąd potrzeba istnienia funkcji. GetSql powinno być zdefinowane w dp_entity, więc nie trzeba go pisać

FeatureTypes:

MapType - mapa, czyli słownik klucz - wartość
ArraySparseType - array stringów
SparseType - stringi
DenseType - numeryczne
StructType - nieregularnie ustrukturyzowane dane


Inne informacje:
$timeCondition jest warunkiem czasowym, który odpowiada za okno ficzera. Wystarczy wstawić w składnię zaraz po WHERE

Funkcja UnmarshallRow - rezultat zapytania SQL jest zapisywany w pliku avro, który następnie jest ładowany do pamięci;
brany jest klucz (np. w formie stringu);
chcemy dla tego klucza pobrać wartość ficzera z pliku avro
na podstawie encji wiadomo jaki jest klucz joinujący (zahashowany klucz), funkcja szuka wartości zahashowanej w pliku;
klucz w odpowiedniej formie jest przetrzymywany w zmiennej key, wartość dla klucza jest przekształcana w odpowiedni sposób i ficzer jest update’owany po kluczu o tą wartość

**Test ficzera**

generate_test.go - funkcja printuje query, które mogę wysłać do clickhouse’a aby dostać plik mock avro

Odpala się do komendą: 
```
go test -v -run TestGenerateExampleDataset
```


Aby mieć oryginalną wartość klucza originalSnKey (przed hashowaniem) należy puścić w metabase szukanie danego hashu i tą wartość wkleić w plik testowy


**Dalsze informacje**
Feature należy zarejestrować w pkg/data_products/features/hoppings_registry.go

Ekstraktory generuje się po to aby ficzer obsługiwał wszystkie wyjątki, takie jak brak danych.
Tworzenie ekstraktorów (w root projektu)
```
make run-extractors-generator-runner
```

Jeśli uda się poprawnie wytworzyć ekstraktory, należy wejść na GitLab i uruchomić step delopy-airflow. DAG należy ręcznie uruchomić.

https://airflow2-ac.grupawp.pl/home

Lokalizacja na s3 - aip/features_hopping/
Używać s3fs (przykład w dgiemza/2025/s3/)
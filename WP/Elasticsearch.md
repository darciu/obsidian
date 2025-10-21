
**Szukanie za pomocą URL**


Parametry w URL
```
_search?q=log_date:"2024-09-16"&size=40
```

Szukanie za pomocą tekstu (we wszystkich polach)
```
_search?q=poczta.o2.pl
```

Warunki logiczne (AND, OR, NOT)
```
_search?q=log_date:"2024-09-18"%20AND%20sitename:poczta.o2.pl
```

Zakres dat
```
_search?q=@timestamp:[2024-01-01%20TO%202024-12-31]
```

Sortowanie
```
_search?q=log_date:"2024-09-16"&sort=@timestamp:desc
```

Wybór wyszukiwanych pól
```
_search?q=log_date:"2024-09-16"&_source=diff,pv_dot
```

Dane pole istnieje
```
_search?q=_exists_:diff
```

Zawiera Elasticsearch, ale nie zawiera wprowadzenie
```
_search?q=tytuł:(+Elasticsearch -"wprowadzenie")
```

Szukanie w wielu indeksach jednocześnie
```
/indeks1,indeks2/_search?q=szukany_tekst
```

Dla bardziej zaawansowanych kwerend stosuje się ciało żądania
```
GET /indeks/_search

{
"query": {
"bool": {
"must": [
{ "match": { "tytuł": "Elasticsearch" } },
{ "range": { "data": { "gte": "2022-01-01", "lte": "2022-12-31" } } }
]
}
}
}
```

**CURL**
```
curl http://user:password@es5-int.dc-2.lb.dcwp.pl:8080
```
user i hasło są w definicjach external tables


**Teoria**

Jest to open source'owy silnik (engine) wyszukiwania full-textowowego (ale nie tylko - ES pozwala na indeksowanie dowolnie ustrukturyzowanych typów danych).

Wyszukiwarki full-text pozwalają na bardzo zoptymalizowany sposób wyszukiwania danych, jeśli podajemy tylko ich fragment.

Jest on napisany w Javie na bazie Apache Lucene. Jest bardzo skalowalny.

ES może też służyć jako narzędzie analityczne, a także pozwala przyśpieszać wyszukiwanie w relacyjnych bazach danych.

  
**Wyjaśnienie full-text search**

Z całego dokumentu tesktowego tworzony jest indeks, który wskazuje występowanie poszczególnych słów w dokumencie.

Indeks (search index) jest to uporządkowana lista występowania poszczególnych fragmentów tesktów w całym dokumencie tekstowym.

Dzięki temu wpisując w wyszukiwarkę słowo, lub jego fragment, dostajemy listę pozycji, gdzie takie słowo się znajduje.

Dodatkowo stosuje się **scoring**, który określa jak podobne jest słowo (lub jego część) wyszukiwane do znalezionych wyników. Przykładowo wpisujemy domo i większy score będzie miało domowy niż domownik.

Słowa w indeksie są tokenizowane. Czyli właśnie w ten sposób możliwe jest połączenie domo z domowym oraz domo z domownikiem.

**Inverted Index** to (przykładowo) lista wyrażeń z określeniem w jakich dokumentach się one znajdują.

![[Pasted image 20240507110810.png|200]]

W rzeczywistości oprócz id dokumentu zwracana jest też pozycja na jakiej to słowo się znajduje. ES stosuje algorytm TF-IDF aby określić istotność rzadkich/częstych słów w dokumentach.

**Elastic Search**

**Document, Field**

W ES dane są przechowywane jako dokumenty (documents). Jest to odpowiednik wiersza tabeli (RDBMS) i jest to w zasadzie obiekt typu JSON, który może mieć różnorodną wewnętrzną strukturę. Każdy dokument ma swój unikalny ID i typ.

Pola (field) w takim dokumencie są odpowiednikiem kolumn w tabeli.
![[Pasted image 20240507111107.png|500]]

**Type, Index**

Dokumenty mogą być zorganizowane w typy, czyli jakiś jeden wspólny mianownik (taki jak Autor, Komentarz itp). Indeks jest odpowiednikiem bazy danych. Zawiera on wiele typów dokumentów.

Typ definiuje schemat (kształt JSONa) oraz mapowanie (rodzaje danych w polach) dokumentu.

Od ES 6 będzie dozwolony tylko jeden typ per indeks.

Indeks jest najwyższym rodzajem bytu jaki można wyszukać w ES.

  

**Shard, Replica, Cluster**

Każdy indeks jest podzielony na wiele shardów. Jest to czynione w celu rozbicia zbyt dużych porcji danych (dane z jednego indeksu mogą nie mieścić się na jednym nodzie), przyśpieszenia wyszukiwania (zrównoleglenie obliczeń na wielu nodach). Shardy to logiczny podział indeksów w celu przyśpieszonego przetwarzania danych. Shardy mogą być na jednej fizycznej maszynie lub większej ich ilości.

Każdy shard ma swoją replikę, która stanowi redundancję dla sharda. Jeśli shard padnie, replika przejmuje jego obowiązki. Elastic Search ustala repliki na innych serwerach niż oryginalny shard. Przy zapisie na oryginalny shard, zmiany są zwyczajnie replikowane na repliki. Repliki przyśpieszają także odczyt danych. Liczbę shardów i replik na indeksie można ustalić tylko przy zakładaniu indeksu. Później ta zmiana jest niemożliwa.

ES w całości zarządza shardami i replikami na nodach.

![[Pasted image 20240507111159.png|400]]

W takim przypadku będziemy mieli 3 oryginalne shardy i na każdy z nich będzie przypadała jedna replika. W efekcie będzie 6 shardów. 

Każdy shard jest w zasadzie osobną instancją Lucene. Można przez to powiedzieć, że ES jest to skalowalne Lucene.

**Cluster, Node**

Węzeł (node) to pojedynczy serwer, który jest częścią klastra i bierze udział on w operacjach przechowywania danych i wyszukiwania ich. Węzły identyfikowane są przez ich nazwy (domyślnie UUID - Universally Unique ID). 

  

Shardy są automatycznie rozdzielane przez ES na podłączone węzły. Wszystkie węzły serwerów podłączone w jedną sieć ES stanowią klaster.

Klaster jest zbiorem jednego lub więcej nodów, które zawierają w sobie całość danych i umożliwia indeksowanie po tych nodach. Klaster dostaje unikalną nazwę (domyślna to elasticsearch).

Węzeł serwera może być przypisany do klastra właśnie po jego nazwie. Dlatego trzeba uważać na nazewnictwo klastrów w rożnych środowiskach, tak by węzły nie połączyły się przypadkowo z niewłaściwym klastrem.
  

**Korzystanie z zasobów**

Sposobem na przeszukiwanie indeksów jest stosowanie REST API. Jednak ES oferuje, w celu ułatwienia korzystania, poziom abstrakcji wyżej, czyli API klienta. Najwyższym poziomem abstrakcji korzystania z indeksów są narzędzia analityczne, tj. Kibana.

Klastry:
- [http://back-1.cr.dc-2.tools.dcwp.pl:9200/](http://back-1.cr.dc-2.tools.dcwp.pl:9200/) zawiera indeksy adm_wpteasers i adm_o2teasers, na których znajdują się informacje o materiałach WP i O2.
- [https://es-cr.dc-2.lb.dcwp.pl/](https://es-cr.dc-2.lb.dcwp.pl/) i [https://es-cr-2.dc-1.lb.dcwp.pl/](https://es-cr-2.dc-1.lb.dcwp.pl/) 
- http://es5-int.dc-2.lb.dcwp.pl:8080/





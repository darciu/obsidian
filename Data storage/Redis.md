(**RE**mote **DI**ctionary **S**erver)Jest to bardzo szybka w czasie dostępu (mikrosekundy opóźnienia odpowiedzi) baza danych (typu klucz-wartość), głównie w pamięci RAM jako cache. Może przechowywać dane tymczasowo (z zadanym TTL) lub trwale. 

**Typy danych (oraz najczęstsze operacje)** 
- string - domyślny typ danych; jeśli nie sprecyzujemy, to wartości będą zapisane właśnie jako string
	- SET key val- ustawia wartość pod zadany klucz (MSET to multiset)
	- GET key - pobiera wartość z klucza (MGET key1 key2)
	- GETRANGE key n m - wartość ze stringa od indeksu n do m
- interger
	- INRC(BY), DECR(BY) key wartość - zwiększanie i zmniejszanie o jakąś wartość
- float
	- INCRBYFLOAT key wartość float
- listy - można dodawać/usuwać elementy z lewej/prawej, pobierać po odpowiednich indeksach


EXPIRE key sek - wygaszanie klucza za ileś sekund
KEYS * - wypisze wszystkie klucze
FLUSHALL - usuwa wszystkie klucze
	


jak, gdzie, kiedy oraz przez kogo został stworzony Redis
Jakie sposoby używania cache (cache-aside, pre-warming, write-through, write-behind)
redis działa w kontenerze dockera/alternatywnie można działać w usłudze chmurowej/


negative caching
cache worker
Odróżnia go od SQL:
brak zapytań sql, są inne polecenia
brak joinów, brak złożonych indeksów po kilku kolumnach


co to ACID

REDIS modules:
RediSearch
RedisGraph
RedisTimeSeries
RedisJSON
i inne

Data Persistence:
- snapshoty bazy danych zapisywane na dysku twardym. Odbywają się co jakiś czas
- AOF (Append only file) - zapisywana jest w czasie rzeczywistym każda operacja z Redis

można używać kombinacji obu podejść
Najlepiej oddzielić usługę Redisa od persistent storage na osobne serwery, tak by w przypadku awarii nie stracić danych

Redis on Flash - częściej używane dane są w pamięci RAM, ale można rozszerzyć pamięć także o SSD, gdzie trzyma się mniej używane dane


Jakie typy danych?

sharding w Redis

Czy pola w redis są zagłębione, podobnie jak w json?


Redis w defaultcie zapisuje wartości jako string

wartość TTL -1 to brak TTL; -2 oznacza, że klucz został usunięty

co to są hashes

Architektura: Redis Console Client - Redis Server

cluster bus

sloty w redis
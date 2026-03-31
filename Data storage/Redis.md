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
- sety - nieuporządkowana kolekcja unikalnych stringów. Operacje:
	- SADD key member - dodaje element
	- SREM key member - usuwa element
	- SISMEMBER key member - sprawdza czy element jest w secie
- sorted set - jest to zbiór unikalnych stringów, ale uporządkowanych rosnąco wg przypisanej liczby zmiennoprzecinkowej (score), która musi być ustalona przez użytkownika. Najczęstsze operacje:
	- ZADD key score member - dodaje elementy
	- ZSCORE key member - pobieranie score elementu
	- ZREM - key member - usuwanie elementu
- HyperLogLog - jest to struktura (licznik), która pozwala szacować liczbę unikalnych elementów. Używana zamiast seta aby nie zajmować pamięci. Pozwala określić liczbę zliczeń z pewnym minimalnym błędem. Niemożna w tej strunkturze podejrzeć tych elementów. Najczęstsze operacje:
	- PFADD key element - dodaje do klucza zliczanie tego elementu
	- PFCOUNT key - zwraca wartość licznika
	- PFMERGE key1 key2 ... - połączenie kilku hyperlogów
- HASH - struktura danych, która przechowuje wiele kluczy i ich wartości. Podobne do słowników. Jeśli pola są ze sobą związane, to lepiej mieć jeden hash niż wiele kluczy (także pod względem efektywności i zarządania TTLem). Nie należy przekraczać ilości 100 pól w hashu. Najczęstsze operacje:
	- HSET key field value
	- HGET key field - pobiera wartość dla danego pola i klucza
	- HGETALL key - pobiera cały hash
- transakcje - jest to grupa sekwencyjnych poleceń w Redis w lokalnej bazie danych, które są wykonywane razem, jedno po drugim. Jeśli choć jedno z nich się nie powiedzie, to żadne inne nie będzie wykonane. Dodatkowo wszystkie struktury danych w bazie Redis, których dotyczy transakcja są na jej czas zabezpieczone przed zmianami (np. innych użytkowników) za pomocą polecenia WATCH key. Jeśli coś miałoby się zmienić w tych kluczach, transakcja jest odwołana. Transakcje oznacza się blokiem MULTI ...komendy... EXEC. Warto używać transakcji przy aktualizacji wielu kluczy, które muszą być ze sobą spójne.

przykład:
```
WATCH balance:42
val = GET balance:42  # załóżmy: 100

MULTI
SET balance:42 90     # np. odejmujemy 10
EXEC     # Zadziała tylko, jeśli balance:42 nie zmienił się od WATCH
```

- PubSub - system komunikacji w czasie rzeczywistym pomiędzy różnymi klientami Redisa. Jedni klienci publikują wiadomości na konkretne kanały, inni subskrybują je w tym samym momencie. Wiadomości w kanałach nie są buforowane (aby odczytać je później) ani w żaden inny sposób nie są zapisywane. Jest to komunikacja jednostronna, gdyż subskrybent nie może odpowiadać na komunikaty. Klient subskrybujący nie może wykonywać innych poleceń dopóki nie opuści trybu subskrybcji - aby zapisywać dane, czy robić cokolwiek, należy utworzyć drugiego klienta. Najczęstsze komendy:
	- SUBSCRIBE channel - subskrybcja kanału
	- UNSUBSCRIBE channel - rezygnacja z subskrybcji
	- PUBLISH channel - publikowanie na kanał
	- PUBSUB - pokazuje informacje systemowa
- Skrypty Lua - można wykonywać bardziej skomplikowane skrypty w Redisie, które mają warunki if, pętle for, while, funkcje itp. Dodatkowo istnieją komendy do komunikacji z Redisem, takie jak: 
	- redis.call() - wykonywanie zwykłych komend Redisa
	- KEYS[n] - dostęp do kluczy przekazanych do skryptu
	- ARGV[n] - dostęp do argumentów
Podstawowe komendy w Redis dla tego języka:
	- EVAL - uruchamianie skryptu od nowa
	- SCRIPT LOAD - wczytywanie skryptu i zwracanie SHA1 aby móc z niego korzystać wielokrotnie
	- EVALSHA - uruchamianie wcześniej załadowanego skryptu po jego SHA1
	- SCRIPT EXISTS - sprawdza czy dany SHA1 skryptu istnieje
	- SCRIPT FLUSH - usuwa wszystkie zapisane skrypty
	- SCRIPT KILL - przerywa działający skrypt
Podczas wykonywania skryptów Lua, blokowana jest even loop Redisa, więc nikt nie może wykonać żadnej komendy w danej bazie

- Geospatial - dane geo przechowywane są w danych typu sorted set. Można przypisywać nazwanym punktom (np. jako miasto) współrzędne geograficzne, aby później robić na nich obliczenia adekwatne do typu geo. Najczęstsze komendy:
	- GEOADD key longitude latitude member - dodaje do danego klucza współrzędne jakiegoś obiektu
	- GEOPOS key member - zwraca longitude i latitude punktu
	- GEODIST key member1 member2 - zwraca odległość pomiędzy punktami
	  GEORADIUS key longitude latitude radius - znajduje w promieniu danego punktu inne punkty
	- GEOHASH key member - zwraca hash w celu stosowania go w słowniku (indeksowania)
- Benchmark - redis-benchmark to narzędzie do testowania wydajności Redisa poprzez symulację wielu użytkowników stosujących dane komendy. Domyślnie wykonuje on 50k komend przy 50 symulowanych użytkownikach i wyświetla statystyki dla różnych komend.





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

transakcje ACID w SQL - co to jest

wszystkie przykłady użycia typów danych w kliencie pythona

event loop w Redis - działanie jednordzeniowo
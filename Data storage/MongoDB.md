Pojedynczym wpisem w bazie jest dokument, czyli obiekt JSON. Przechowywany jest on w formacie BSOJ (binary JSON). Zbiorem dokumentów jest kolekcja (odpowiednik tabeli), a zbiorem kolekcji jest nasza baza danych.

Uruchomienie demona serwera z podaną ścieżką do lokacji do zapisu danych
```
mongod --dbpath=/Users/dgiemza/data/db
```

Komendy mognosh:
- show dbs/collections - pokazuje wszystkie bazy danych/kolekcje
- use database_name - przełączenie się na bazę danych
- db - pokazuje aktualną bazę danych
- db.createCollection("name", {capped: true, size: 1000\*1024, max: 100}) - dodaje kolekcję, drugi argument daje ograniczenie na wielkość kolekcji do 10MB oraz maksymalnie 100 dokumentów;
- db.nazwa_kolekcji.drop() - usuwa kolekcję
- db.dropDatabase()

Komendy na dokumentach:
- db.students.insertOne({name:"Spongebob", age:30, gpa:3.2}) - dodaje jeden dokument
- ...insertMany({}, {}, {}) - dodaje wiele dokumentów
- db.students.find().sort({name:1}) - sortowanie po wskazanym polu w dokumencie (1 to rosnąco, -1 malejąco). Pole z null ma większą wartość niż brak tego pola;
- find({filter}, {projection}) - pierwszy parametr działa jak filtr, gdzie podaje się warunki, np find({name:"Darek", age:{\$lt:30}}); odpowiednik WHERE w SQL; drugi człon projection decyduje które pola zwracać z dokumentów, np. find({}, {name:true, \_id:false}) - odpowiednik SELECT z SQL; nazwa_pola:{$exists:true} pozwala w filtrować pola, które istnieją w dokumentach
- update({filter}, {update}) - na podstawie filtra wybiera się rekordy do update'u; przykład: db.students.updateOne({name:"Darius"}, {$set:{age:35}}); $set - ustawia pole, $unset - usuwa pole
- delete({filter}) - usuwa rekordy na podstawie filtra
- 
Operatory porównań:
nazwa_pola:{$ne:"Nazwa"} - not equal
nazwa_pola:{$lt:20} - liczba mniejsza niż 20; inne to lte, gt, gte
nazwa_pola:{$in:["Darek","Marek"]} - pole ma wartości z podanej listy; odwrotność nin (not in)

Operatory logiczne:
$and: [{name:"Darek"}, {age:{\$lt:28}}] - warunki na imię i odpowiedni wiek połączone operatorem and
$or - podobnie jak wyżej
$nor - podobnie jak wyżej
$not - podobna składnia jak wyżej

Indeksowanie
Bez stosowania indeksowania każde zapytanie musi przejrzeć wszystkie dokumenty (COLLSCAN). Jednak można to przyspieszyć poprzez trzymanie indeksów w zewnętrznej strukturze danych (B-TREE). Wtedy serwer MongoDB robi tylko skan po indeksie (IXSCAN). Kosztem takiego rozwiązania jest to, że trzyma się w pamięci dodatkowe dane oraz operacje takie jak update, insert, delete dłużej trwają.

Dobrymi praktykami jest wybieranie pól do indeksowania, które mają dużo różnorodnych wartości i często się po nich filtruje.

db.students.createIndex({nazwa_pola: 1}) - tworzy indeks z pola rosnąco
db.students.getIndexes() - zwraca wszystkie indeksy w kolekcji
db.students.dropIndex("nazwa_indeksu") - usuwa indeks




explain
konstruktory danych
skąd nazwa, dlaczego i gdzie powstał i kiedy zaczął być popularny
jak ustawiać typy danych w dokumentach
time series database

czy można łączyć się z MongoDB za pomocą curl?
jaki jest klient pythonowy do MongoDB
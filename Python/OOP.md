
**REPL** - Read Edit Print Loop, czyli inaczej interaktywna konsola do pisania o wykonywania kodu. Przykładowo Python w terminalu lub bardziej zaawansowana forma to Jupyter


**OOP**


Co robi **new**, a co **init**

- **new**(cls, …) – tworzy (alokuje) nowy obiekt. Wywoływana jako pierwsza. Musi zwrócić obiekt (najczęściej instancję cls).
- **init**(self, …) – inicjalizuje już utworzoną instancję. Wywoływana tylko wtedy, gdy **new** zwróci instancję tej klasy (lub jej podklasy). Zawsze zwraca None.
**new** tworzy, **init** konfiguruje.

Wywoływanie klasy. Podczas x=MyClass() działa type.call(). Jak Python naprawdę wywołuje konstruktor.
```python
obj = cls.__new__(cls, *args, **kwargs)
if isinstance(obj, cls): 
	cls.**init**(obj, *args, **kwargs)
return obj
```

To powoduje:

- Jeśli **new** zwróci coś, co nie jest instancją cls (np. inną klasę lub prymityw), **init** zostanie pominięte.
- Te same argumenty trafiają do **new** i **init** (zadbaj, by ich sygnatury były kompatybilne albo użyj *args, **kwargs).

Kiedy nadpisywać **new**, a kiedy **init**
- Zwykle nadpisujesz tylko **init** – do walidacji, ustawiania pól, logiki inicjalizacji.
- **new** nadpisuj głównie gdy:
    - Tworzysz/zmieniasz zachowanie obiektów niemutowalnych (int, str, tuple, bytes, frozenset).
    - Chcesz kontrolować caching/singleton/flyweight (zwracać istniejącą instancję).
    - Chcesz czasem zwrócić inny typ niż cls.
    - Chcesz wpłynąć na sam proces tworzenia (np. odmówić utworzenia instancji).

Przykłady:
```python
class UpperTuple(tuple):
    def __new__(cls, iterable):
        # Tworzymy “treść” już teraz, bo tuple jest niemutowalne
        normalized = tuple(str(x).upper() for x in iterable)
        return super().__new__(cls, normalized)

t = UpperTuple(["a", "B", "c"])  # ('A', 'B', 'C')
```
Niemutowalne obiekty. Możliwe jest stworzenie tupleta z tylko wielkimi literami na bazie istniejącego, ale to wymaga lokowania pamieci dwukrotnie. W powyższy sposób taki tuplet powstaje raz.

Walidacja w new
```python
class HexString(str):
    def __new__(cls, value):
        s = str(value)
        if any(ch not in "0123456789abcdefABCDEF" for ch in s):
            raise ValueError("Not a hex string")
        return super().__new__(cls, s.lower())

h = HexString("A0Ff")  # "a0ff"
```

super().metoda() - szuka metody wyżej w hierarchii MRO. Nie musi być to zawsze bezpośredni rodzic danej klasy.

**MRO - Method Resolution Order**
Sposób ustalania kolejności klas skąd dziedziczyć daną metodę/atrybuty. 

W starszych wersjach Pythona (DLR): najpierw szukanie wgłąb (depth-first), a później od lewej do prawej

W nowszych: C3 Linearization Algorithm, tutaj opisany https://medium.com/@ruitcatarino/understanding-pythons-method-resolution-order-mro-f7cbcec36993

**Klasa object**
Jest to blueprint dla wszystkich innych klas, gdyż każda klasa w Pythonie po niej dziedziczy. Klasa ta posiada minimalny zestaw zachowań nadawany automatycznie wszystkim innym klasom (o ile tego sami nie zdefiniujemy inaczej), nazywanych metodami specjalnymi, takie jak: new, init, str, repr, eq, format, hash, getattribute,setattr, delattr, dir, reduce, reduce_ex, sizeof, init_subclass.
Nie definiuje ona operatorów porządkowych (lt, le, gt, ge), bool, getattr, class_getitem.

 Klasa ta nie posiada atrybutów (nie ma dict)
 ```
o = object()
o.x1 = 'a'
 ```
 daje: AttributeError: 'object' object has no attribute 'x1'



**Metody specjalne (dunder)**

- repr - zwraca string z tekstową reprezentacją danego obiektu, techniczny zapis aby programista dokładnie wiedział czym jest dany obiekt. Przykładowo, dla obiektu datetime, tak wygląda repr
```
datetime.datetime(2025, 11, 27, 9, 50, 37, 429078)
```
- str - reprezentacja tekstowa, ale dla użytkowników końcowych. Ładna forma tekstowa danego obiektu. Przykładowo str dla datetime.now wygląda tak:
```
2025-11-27 23:22:54.359826
```

Jeśli klasa nie ma zdefiniowanego str(), to jest fallback do repr(), a jeśli nie ma zdefiniowanego repr(), to jest wersja dziedziczona z object, przykładowo
```
<__main__.A object at 0x7f8b8c0d4f40>
```
str i repr można wywoływać bezpośrednio na obiektach
```
repr(obiekt)
str(obiekt)
```
* eq - decyduje ona co oznacza operacja przyrównania dwóch obiektów. Nie musimy ograniczać się wyłącznie do instancji jednej klasy, ale w przypadku dwóch różnych klas takie przyrównanie może nie być symetryczne. Istnieje różnica pomiędzy a == b i a.eq(b).
Jeśli zostanie wywołane a == b, to najpierw wywoływane jest a.eq(b). W przypadku True, zwracane jest True, w przypadku False, zwracane jest False (bez wywołania b.eq(a)), w przypadku NotImplemented wywoływane jest b.eq(a). Defaultowo w klasie object sprawdzane jest czy instancje tej samej klasy mają dokładne taki sam adres w pamięci (tylko te same instancje dają True).

Bliźniacze metody specjalne komparacyjne to le,lt, ge, gt. Można je implementować w np. taki sposób, że porównuje się zbiory, bada porządek w jakimś szeregu (np. leksykalnym).

- getitem i settiem - metody określające zachowanie obiektu podczas używania operatora [], czyli podczas wybierania na podstawie jakiegoś indeksu albo ustalania wartości w obiekcie na podstawie indeksu. Przydatne, jeśli operuje się na niestandardowych obiektach lub jest potencjał na skrócenie dłuższego fragmentu kodu w szczególnych złożonych typach danych.
- iter i next - iter zwraca iterator, czyli obiekt po którym można iterować i ta iteracja zaczyna się od zerowego indeksu. Jeśli metoda iter zwraca self, to obiekt sam jest iteratorem. Next zwraca wartość kolejnego elementu danego iteratora (albo StopIteration).
- hash - w słownikach/setach aby można było szybko wyszukiwać klucze, tworzony jest hash na ich wartościach (wartość int). Dlatego kluczami słownika mogą być wyłącznie hashowalne obiekty, mające stały hash w trakcie sesji Pythona (bool, float, int, str...). Ta metoda jest nadpisywana, jeśli istnieje potrzeba aby obiekty klasy mogły być kluczami w słowniku.
- getattr, hasattr, setattr, delattr - służą do manipulowania atrybutami w klasie
- dir - zwraca wszystkie magiczne metody w danej klasie
- del - destruktor, który jest wywoływany jeśli wszystkie odniesienia do danego obiektu przestają istnieć
- sizeof - podaje ile zajmuje dany obiekt w pamięci (w bajtach); sys.getsizeof() wywołuje tą metodę ale dodaje jeszcze pamięć zajętą przez obiekt w garbage collectorze



**Builtins**  - funkcje, wyjątki, stałe oraz typy danych które są wbudowane w Pythona i zawsze są dostępne, bez importów (https://docs.python.org/3/library/builtins.html). Aby coś się stało builtinem, musi przejść przez proces standaryzacji PEP. Dzieje się to bardzo rzadko, gdyż nie można czegoś usunąć z builtins bez psucia kompatybilności wstecznej.





type (metaklasa) - co to jest

builtins (nazwy wbudowane), LEGB, słowa kluczowe (if, for)

dataclass
Dekorator zmieniający charakter klasy w  budowaniu struktur danych. Przydatne, kiedy klasa jest głównie nośnikiem pól a nie funkcjonalności. Pozwala pisać minimum kodu aby mieć pojemnik na dane.

co to jest singleton

callable
PEP

asynchroniczna iteracja
iteracja
ellipsis

hash table


closure (funkcja w funkcji)
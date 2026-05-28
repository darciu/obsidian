
**Mutowalne vs niemutowalne**

Niemutowalne (int, str, tuple, bool) - kiedy chcemy coś zmienić w obiekcie, to python nie dokłada czy nie modyfikuje jego zawartości ale tworzy nowy obiekt w pamięci. Raz utworzone w pamięci. Można ich używać jako kluczy w słownikach (są pod tym względem bezpieczne), mogą być elementami setów.
Mutowalne (list, dict, set) - można zmieniać ich wartość w miejscu.

*Czy krotka z wewnętrzną listą jest mutowalna?*
Jest niemutowalna, ale już sama lista, przez sam fakt istnienia tego kontenera, jest mutowalna.

**Stos (Stack) vs sterta (Heap)**
- Heap - prywatna przestrzeń w pamięci operacyjnej przeznaczona dla Pythona, gdzie przechowywane są wszystkie obiekty i funkcje do której do pamięci użytkownik ma dostęp tylko pośrednio. Stertą zarządza Python Memory Manager, który prosi system operacyjny o duże bloki pamięci i aby ciągle nie zawracać głowy OS, zarządza już tak wydzieloną pamięcią lokując samodzielnie tam obiekty lub je zwalniając, co jest znacznie bardziej optymalne. Każdy obiekt na stercie ma licznik referencji, który wskazuje ile jest odniesień do obiektu w kodzie Pythona. Jeśli licznik spada do zera (obj = None), to pamięć obiektu zwalniana jest natychmiast. Innym sposobem zwalniania pamięci jest Garbage Collector, który zwalnia pamięć jeśli puste obiekty, jeśli te wskazują tylko na siebie same.
- Stack - znacznie mniejsza pamięć gdzie przechowywane są tylko referencje do obiektów w stercie.

**Areny vs Bloki vs Poole**
- Bloki - najmniejsze jednostki pamięci od 8 do 512 bajtów przechowujące dokładnie jeden obiekt
- Poole - zbiory bloków uporządkowane wg takich samych rozmiarów. Dzięki takiemu podziałowi unika się fragmentacji pamięci.
- Arena - największy podział pamięci zwykle 256 kb i składać się z różnorodnych pul.

**Small Object Allocator** posługuje się wyżej wymienionymi arenami i pulami. Jeśli obiekt przekracza 512 bajtów omija ten system i uderza od razu do alokatora pamięci C malloc lub do systemu operacyjnego.

**Wyciek pamięci**
Program tworzy obiekty w pamięci RAM, ale nigdy ich nie używa, co powoduje w końcu zapchanie RAMu i OOM. W Pythonie przeciwdziała im Garbage Collector, ale czasem użytkownik tworzy globalne listy czy słowniki, których nie ogranicza. Innym powodem wycieków jest niezamykanie połączeń zewnętrznych (do baz danych itp). Dla tego celu używa się context managera.

**Context manager**
Mechanizm, który automatycznie zarządza przydzielaniem i zwalnianiem zasobów. Ma on tą zaletę, że połączenie z zasobem zawsze zostanie poprawnie zamknięte niezależnie od tego co się może zadziać w kodzie. Aby go zastosować używać się składni *with*. Musi mieć zdefiniowane on dwie dunder methods: enter i exit. W definicji exit są trzy argumenty:
``` python
__exit__(self, exc_type, exc_val, exc_tb)
```
są to typ błędu, wartość błędu i traceback, które przy poprawnym wykonaniu wynoszą None.


**Deep i shallow copy** - w Pythonie rozróżniamy przypisanie i kopiowanie płytkie oraz głębokie. 

**Kontener** to opakowanie na inne obiekty/elementy

**Przypisanie (`=`)**: Nie tworzy żadnej kopii obiektu. Tworzy jedynie nową nazwę (referencję), która wskazuje na ten sam adres w pamięci, co nazwa oryginalna. Zmiana jakiegokolwiek elementu przez jedną nazwę jest widoczna przez drugą. Jest to wyłącznie nowa etykieta z tą samą zawartością.

**Płytka kopia (shallow copy)**: Tworzy nowy obiekt kontenera (kontenerem jest lista), ale wypełnia go referencjami do tych samych obiektów, które znajdują się w oryginale.
- Jeśli **podmienisz** element na poziomie kontenera (np. zmienisz liczbę lub string pod indeksem `[0]`), zmiana nie wpłynie na oryginał, ponieważ w kopii wstawiasz nową referencję.
- Jeśli jednak **zmodyfikujesz** istniejący obiekt mutowalny wewnątrz kontenera (np. dodasz element do listy będącej elementem listy głównej), zmiana zajdzie w obu miejscach, bo obie kopie współdzielą ten sam obiekt wewnętrzny. Dla obiektów niemutowalnych (int, str, tuple)
- płytką kopią jest też takie wyrażenie: 
	- nowa_lista = stara_lista[:10];
	- nowy_dict = stary_dict.copy();

**Głęboka kopia (deep copy)**: Tworzy całkiem niezależny obiekt, rekurencyjnie kopiując wszystkie obiekty znalezione wewnątrz oryginału. Tworzy nowe miejsca w pamięci dla każdego poziomu zagnieżdżenia. Modyfikacje kopii w żaden sposób nie wpływają na pierwowzór.

Deep copy należy używać gdy mamy zagnieżdżone struktury, ale ona wolniejsza niż shallow copy.
W pandas .copy() ma domyślny parametr deep=True

**Różnica pomiędzy is a \=\=**
is sprawdza czy dwa obiekty to ten sam kontener, a == sprawdza je wyłącznie pod względm wartości. Za operator == odpowiada metoda eq, a is nie ma odpowiadającej metody, gdyż nie odpytywany jest obiekt, ale sprawdzana jest referencja w pamięci. a is b jest równoważny do id(a) == id(b).

**val is None vs val == None**
Istnieje tylko jeden singleton None w pamięci środowiska Pythona. Kiedy używa się is, to interpreter sprawdza dokładnie jeden adres w tej pamięci. Kiedy używa się == to Python sprawdza czy obiekt ma zdefiniowaną metodę eq, wywołuję jej logikę i sprawdza wynik.

**Dlaczego krotka (tuple) jest niemutowalna, a lista już tak?**
Ponieważ krotka ma być lekkim obiektem, którego rozmiar zakładamy już przy tworzeniu obiektu. Lista to pojemnik na wiele elementów, które mogą się zmieniać w czasie.
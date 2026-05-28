Chodzi tutaj o najmniejszą logiczną jednostkę pracy z bazą SQL, na którą może składać się kilka operacji, ale traktuje się je jako jedną nierozerwalną całość. Albo wszystkie zapytania w SQL zostaną wykonane poprawnie, albo żadne z nich nie będzie. Jest to konieczne w rozwiązaniach bankowych, kiedy jedna operacja jest wymogiem do zaistnienia drugiej, np. przy przelewach z konta na konto.

Na transakcję składają się części:
BEGIN - otwiera transakcję; baza danych notuje sobie wszystkie wyniki operacji na boku, ale jeszcze ich nie zapisuje
COMMIT - jeśli wszystko pójdzie zgodnie z planem, to wyniki są zapisywane do bazy danych
ROLLBACK - jeśli coś poszło nie tak, to należy usunąć wszystko co było robione od momentu BEGIN

**ACID**
A jak Atomicity - wszystko albo nic, transakcja nie może wykonać się w połowie
C jak Consistency - transakcja musi przeprowadzić bazę z jednego poprawnego stanu do drugiego. Muszą być spełnione wszystkie założenia w kolumnach tej bazy (jak dodatnie saldo).
I jak Isolation - jeśli jednocześnie trwają dwie transakcje na jednym polu bazy danych, to nie mogą one sobie wchodzić w drogę. Transakcja musi rezerwować sobie konieczne pola. Są zresztą różne poziomy izolacji w zależności od wymogów.
D jak Durability - po COMMITcie zmiana jest już nieodwracalna i na stałe wpisana w bazę danych
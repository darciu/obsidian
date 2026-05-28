**Iterable (obiekt iterowalny)** – jakikolwiek obiekt (listy, krotki, stringi, słowniki), z którego można wyciągnąć iterator. Obiekt ten musi mieć zaimplementowaną metodę `__iter__()`.

**Iterator** – obiekt zwracany przez _iterable_ (nie jest to "instancja iterable"), który reprezentuje konkretny strumień danych. Zna swój obecny stan (wskaźnik), a aby przejść do następnego elementu, należy zastosować funkcję `next()` (która wymaga w tym obiekcie zaimplementowanej metody `__next__()`). Iterator ma też metodę iter, która zwraca samego siebie (self).

**Pętla for** działa tak, że najpierw wyciąga iterator (wywołując funkcję `iter()`), a następnie w pętli wywołuje funkcję `next()`, aż nie napotka wyjątku `StopIteration`.

**Iteratory są jednorazowego użytku** – po przejściu przez element, iterator bezpowrotnie przesuwa swój stan do przodu (niekoniecznie "pozbywa się" elementu z pamięci, jeśli opiera się np. na liście). Iteratory wykorzystują **leniwe wartościowanie (lazy evaluation)**, co oznacza, że nie trzymają wygenerowanych wyników w pamięci RAM naraz, a obliczają/pobierają tylko ten jeden element wywołany funkcją `next()`.

**Wszystkie iteratory to obiekty iterowalne** (mają metodę `__iter__()`, która zwraca je same), ale **nie wszystkie obiekty iterowalne to iteratory** (np. listy mają `__iter__()`, ale nie mają metody `__next__()`). Przykładowo aby iterować po liście najpierw działa metoda iter, która zwraca list_iterator, na której można stosować metodę next.

**Generatory** - jest to najszybszy sposób na stworzenie iteratora: zamiast pisania metod next i iter, po prostu w funkcji (w dowolnym miejscu) zwracam yield zamiast return. Wniosek z tego taki, że każdy generator jest iteratorem, ale nie każdy iterator jest generatorem (bo iteratory mogą mieć własne implementacje tych funkcji). Jeśli funkcja kończy się return, to do pamięci RAM ładowana jest cała zawartość zwracanego obiektu i zapominany jest stan wewnętrzny funkcji, jeśli yield, to ładowany jest zawsze jeden element, po którym następuje kolejny itp. a sama funkcja jest zamrażana w tym momencie, aby móc ją wznowić.

Podobnie jak iteratory, generatory są wyczerpywalne.

Generator expression:
``` python
generator = (x**2 for x in range(1000000))
```


Generator z iteratora:
``` python
def moj_generator():
	for i in range(3):
		yield i

```
zwraca on element po elemencie

yield from
``` python
def generator_zewnetrzny():
	yield from [1, 2, 3]
```
wyczerpuje on oryginalny generator

dowiedzieć się więcej o yield from
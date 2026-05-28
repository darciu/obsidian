Są to wrappery na funkcje, które wzbogacają jej nowymi zachowaniami przed uruchomieniem albo po, bez modyfikowania oryginalnej funkcji.

Działa to w ten sposób, że dekorator jest skrótem od podania funkcji jako argument dla funkcji dekorującej. Więc dekorator sam jest funkcją
``` python
@moj_dekorator
def zwykla_funkcja():
	print("Działam!")

# jest tożsame z:
zwykla_funkcja = moj_dekorator(zwykla_funkcja)
```

Szablon pisania dekoratora
``` python
def loguj_wywolanie(oryginalna_funkcja):
    # To jest nasz wrapper, który zastąpi oryginalną funkcję.
    # Musi przyjmować *args i **kwargs, aby działać dla KAŻDEJ funkcji!
    def wrapper(*args, **kwargs):
        print(f"--- LOG: Uruchamiam funkcję: {oryginalna_funkcja.__name__} ---")
        
        # Wywołujemy oryginalną funkcję i zapisujemy jej wynik
        wynik = oryginalna_funkcja(*args, **kwargs) 
        
        print(f"--- LOG: Funkcja zakończyła działanie ---")
        
        # Zwracamy wynik, aby nie zepsuć zachowania oryginalnej funkcji
        return wynik 

    # Dekorator ZWRACA samą funkcję wrapper (bez nawiasów, nie wywołuje jej!)
    return wrapper

# Użycie:
@loguj_wywolanie
def dodaj(a, b):
    return a + b

print(dodaj(2, 3))

```

dekoratorów używa się kiedy chcemy mieć trochę kodu przed i po funkcji, ale nie chcemy pisać zbyt dużej ilości kodu, który niepotrzebnie mógłby się powtarzać. Najpopularniejsze zastosowanie to mierzenie czasu funkcji.

- **Problem utraty metadanych i `functools.wraps`**: W Twoim szablonie, nowa funkcja nazywa się `wrapper`. Jeśli na udekorowanej funkcji `dodaj` wywołasz `print(dodaj.__name__)`, zamiast `"dodaj"` otrzymasz `"wrapper"`. Tracisz też np. oryginalny docstring. Aby temu zapobiec, wewnątrz dekoratora używa się wbudowanego modułu `functools` i deklaruje `@wraps(oryginalna_funkcja)` tuż nad `def wrapper(...)`.
    
- **Dekoratory przyjmujące argumenty**: Czasem chcemy przekazać parametr, np. `@powtorz(3)`. Aby to osiągnąć, w kodzie potrzebujesz aż **trzech** zagnieżdżonych funkcji (zewnętrzna przyjmuje argumenty dekoratora, środkowa jest właściwym dekoratorem, a wewnętrzna to wrapper).
    
- **Dekoratory oparte na klasach**: Mimo że w notatkach masz funkcje, dekoratory można też tworzyć przy użyciu klas, pod warunkiem zaimplementowania w nich "magicznej" metody `__call__`.
    
- **Kolejność stosowania**: Można nałożyć wiele dekoratorów na jedną funkcję (jeden pod drugim). Wykonują się one kaskadowo – najbliższy funkcji (ten na samym dole) owija ją jako pierwszy.
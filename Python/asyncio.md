### Podstawy i terminologia

- **Współbieżność (Concurrency):** Funkcje zazwyczaj działają na jednym rdzeniu CPU. Polega to na przełączaniu się między zadaniami w momentach oczekiwania (głównie na operacje Wejścia/Wyjścia – I/O), co pozwala efektywnie wykorzystać czas procesora.
    
- **Równoległość (Parallelism):** Funkcje działają na wielu rdzeniach CPU jednocześnie (np. trenowanie modeli). W Pythonie odpowiada za to moduł `multiprocessing`.
    
- **Zastosowanie:** Wykorzystanie faktu, że operacje sieciowe lub dyskowe trwają długo. Zamiast blokować program, `asyncio` pozwala w tym czasie wykonywać inne obliczenia.
    

### Event Loop (Pętla zdarzeń)

Jest to serce biblioteki. Nieskończona pętla, która monitoruje zadania wstrzymane przez `await`.

- Czeka na powiadomienia od systemu operacyjnego o zakończeniu operacji I/O.
- Wznawia zadania, które otrzymały wyniki.
- Zarządza trzema typami obiektów: **Taski, Coroutines, Futures**.
- Jeśli wszystkie taski wykonują tylko ciężkie obliczenia CPU (brak `await`), pętla zostaje zablokowana i traci swój sens.
### Coroutine (Korutyna)

Funkcja zdefiniowana przez `async def`, która może zawieszać swoje działanie.
- Wywołanie jej bezpośrednio zwraca **obiekt coroutine**, a nie wynik funkcji.
- Zostaje wykonana dopiero po włączeniu do event loop (np. przez `await` lub `asyncio.run()`).

``` python
async def main():
    print('Hello')

# Wywołanie: asyncio.run(main())
```

### Task (Zadanie)

Coroutine zarejestrowana w event loop za pomocą `asyncio.create_task(coro())`. Task uruchamia się automatycznie, gdy pętla zdarzeń ma wolne zasoby.

**Co programista może zrobić z obiektem Task?**

- `await task`: Wstrzymuje obecną funkcję do czasu zakończenia taska i pobiera jego wynik.
- `task.cancel()`: Przerywa zadanie (rzuca wewnątrz `CancelledError`).
- `task.done()`: Zwraca `True`, jeśli zadanie się zakończyło (sukcesem lub błędem).
- `task.result()`: Pobiera wynik (używać tylko, gdy `done()` jest True, inaczej rzuci błąd).
- `task.add_done_callback(fn)`: Rejestruje funkcję synchroniczną, która wykona się od razu po zakończeniu taska.
    
Jeśli konkretny task nigdy nie napotka await lub nie zostanie użyte task.result(), to wynik taska nie zostanie zwrócony.

**Cykl życia Taska:** `pending` -> `running` -> `awaiting` -> `finished/failed` lub `cancelled`.

### Kluczowe funkcje sterujące

- **`asyncio.run()`:** Tworzy nową pętlę zdarzeń, uruchamia korutynę i zamyka pętlę. Używać tylko raz w punkcie wejścia do programu.
- **`asyncio.create_task()`:** Planuje wykonanie korutyny w tle. Nie blokuje dalszego wykonywania kodu.
- **`asyncio.gather(*aws)`:** Przyjmuje wiele obiektów (korutyny/taski) i czeka na wszystkie, zwracając listę wyników.
- **`asyncio.TaskGroup()`:** Nowoczesny sposób (Python 3.11+) na zarządzanie grupą zadań. Jeśli jedno zadanie w grupie padnie, reszta zostaje automatycznie anulowana.
    

### Słowo kluczowe `await`

Oddaje kontrolę do pętli zdarzeń.

- `await asyncio.sleep(1)`: Synchroniczna funkcja "idzie spać", pętla wykonuje w tym czasie inne zadania przez 1 sekundę.
- `await task`: Czeka na konkretny wynik zadania. Jeśli zadanie skończyło się wcześniej, wynik jest zwracany natychmiast.


### Future

Niskopoziomowy obiekt – "pudełko na dane". Nie ma logiki wykonawczej. Czeka, aż zewnętrzny kod (np. callback z innej biblioteki) wypełni go wartością przez `set_result(value)`. Służy do łączenia świata asynchronicznego z kodem niskopoziomowym lub synchronicznym.

---

### Synchronizacja

Używana, gdy wiele korutyn chce uzyskać dostęp do tego samego zasobu w tym samym czasie.

**1. `asyncio.Lock`** (Tylko jedna korutyna na raz):

Python

```
lock = asyncio.Lock()
async with lock:
    # Sekcja krytyczna - tylko jeden task tu wejdzie
    shared_resource += 1
```

**2. `asyncio.Semaphore`** (Limitowana liczba korutyn na raz):

Python

```
import asyncio

async def limit_access(semafor, task_id):
    async with semafor:
        print(f"Task {task_id} pracuje...")
        await asyncio.sleep(1)
    print(f"Task {task_id} zwolnił miejsce.")

async def main():
    semafor = asyncio.Semaphore(2) # Tylko 2 taski na raz
    await asyncio.gather(*(limit_access(semafor, i) for i in range(5)))

# asyncio.run(main())
```

**3. `asyncio.Event`** (Synchronizacja sygnałem):

Python

```
import asyncio

async def waiter(event):
    print("Czekam na sygnał...")
    await event.wait() # Blokada do czasu event.set()
    print("Sygnał odebrany! Ruszam.")

async def setter(event):
    await asyncio.sleep(2)
    print("Wysyłam sygnał...")
    event.set() # Odblokowuje wszystkie funkcje czekające na .wait()

async def main():
    event = asyncio.Event()
    await asyncio.gather(waiter(event), setter(event))

# asyncio.run(main())
```

### asyncio w JupyterLab / IPython

W środowisku interaktywnym pętla zdarzeń działa domyślnie w tle, dlatego:

1. **Nigdy nie używamy `asyncio.run()`** (rzuci błąd `RuntimeError`).
    
2. Możemy używać **`await` bezpośrednio w komórkach** (Top-level await).
    
    - Przykład: `await moja_funkcja_async()` zamiast `asyncio.run(...)`.
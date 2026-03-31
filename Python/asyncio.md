Funkcje asynchroniczne w Pythonie są zazwyczaj uruchamiane na jednym rdzeniu CPU, ale ten rdzeń jest w stanie dzielić swoje zasoby na wiele współbieżnych zadań. Równoległość polega już na tym, że funkcje działają na wielu CPU (np. trenowanie modeli). Równoległością w Pythonie zajmuje się moduł multiprocessing.
Zastosowaniem współbieżności jest wykorzystywanie faktu, że funkcje pythonowe zazwyczaj oczekują na I/O (stąd nazwa asyncIO), przez to można ten czas wykorzystać na inne obliczenia.

**Event loop**
Jest to nieskończona pętla, która ma za zadanie monitorować co się dzieje z zadaniami wstrzymanymi przez await, czeka na powiadomienia od systemu operacyjnego czy zadanie dokonało już obliczeń i ma wyniki aby następnie wznowić te zadania, które zostały w kodzie wstrzymane przez await.
Zarządza ona trzema typami obiektów: Taski, Coroutines, Futures.
To właśnie dzięki pętli zdarzeń, jeśli jeden task czeka, to reszta może działać współbieżnie. Jeśli wszystkie taski nie oczekują na nic, tylko zajmują się obliczeniami, to sens pętli zdarzeń zanika.

**Coroutine**
Jest to funkcja, która ma zdolność do zawieszania swojego działania na await. Wywołanie jej bezpośrednio zwraca nie jej wynik, ale obiekt coroutine.
``` python
async def main():
    print('Hello')
    
main()

<coroutine object main at 0x7f0f6443f9c0>
```
Taka funkcja może mieć wiele await w swojej treści i na końcu może zwracać return. Zostaje ona wykonana dopiero kiedy zostaje ona włączona do event loop:
```python
asyncio.run(main())
```

**Task**
Jest to coroutine zarejestrowane w event loop za pomocą funkcji asyncio.create_task(funkcja), tak by pętla zdarzeń mogła nim zarządzać. Task ma pewien cykl życia:
- pending - task został dodany do event loop
- running - task się wykonuje na CPU
- awaiting - task trafił w swoim kodzie na await i oczekuje
- failed/finished - task został ukończony i ma wynik
- cancelled - anulowany

asyncio.run vs asyncio.create_task vs asyncio.gather

- run powinno się używać tylko raz w głównej funkcji, gdyż tworzona jest nowa pętla zdarzeń. Całość kodu jest blokowana aż nie wykona się run.
- create_task - służy do rejestrowania coroutine w event loop,  oraz zwraca obiekt task. Task uruchamia się przy await lub gather. await może dotyczyć czegokolwiek i wtedy ta funkcja się zacznie wykonywać, o ile będzie dla niej kolejka
- gather - przyjmuje wiele coroutine lub tasks i oczekuje na zwrócenie wyników przez wszystkie z nich w formie listy

await
To oddaje kontrolę pętli, tak by taski w niej zarejestrowane mogły się wykonywać. Jeśli mamy sytuację:
- await asyncio.sleep(1) - zarejestrowane funkcje wykonują się przez czas jednej sekundy. Jeśli task nie zdąży się wykonać, to będzie on kontynuowany przy następnym await, chyba że ustalimy jakiś jego timeout lub zakończy się event loop.
- await task - uruchamia task (jeśli wcześniej nie było await po rejestracji taska) ale lub kontynuuje jego działanie aż do upewnienia się, że dany task zwraca wyniki. Jeśli task się zakończy przed await task, to tylko zwracane są jego wyniki. Jeśli wielokrotnie podamy kod await task, to tyle razy zostanie zwrócony jego rezultat (po zakończeniu taska przechowywany jest w jego obiekcie wynik).

async with asyncio.TaskGroup()

**Future**
Future nie zawiera w sobie żadnej logiki wykonawczej, ale jest to pudełko na dane, które oczekuje aż zewnętrzny kod wypełni go wartością (set_result(value)) lub zwróci wyjątek. I właśnie tym brakiem wewnętrznej logiki to się różni od taska. Służy do integracji z zewnętrznymi bibliotekami, gdzie nie działa kod pythona.

Synchronizacja
Jeśli problem jest, że wiele coroutines mogłoby mieć dostęp jednocześnie do tego samego zasobu (zapis do bazy danych), można ustalić kolejność w jakiej te coroutines będą ją miały.

- asyncio.Lock - tylko jedna coroutine ma dostęp do zasobu w danej chwili
``` python
import asyncio

lock = asyncio.Lock()
shared_resource = 0

async def update_resource():
    global shared_resource
    async with lock:  # Automatycznie acquire() i release()
        temp = shared_resource
        await asyncio.sleep(0.1) # Symulacja I/O
        shared_resource = temp + 1
```

- asyncio.Semaphore - pozwala na dostęp do zasobu maksymalnie (zależy od parametru) kilku coroutines
``` python

```
- asyncio.Event - jest to flaga typu boolean (True/False). Jeśli zostaje spełniony warunek await event.wait() na True, to kod idzie dalej. Taki warunek może być spełniony np. w innym coroutine, które działa w pętli i tam jest ustawiane event.set().






Korzystanie a asyncio w Jupyterze

czy można odpalać asynchroniczne funkcje w normalnej
* Modele LLM vs Chat - dwie osobne klasy, które różni sposób komunikacji. Modele LLM dostają an wejściu tekst i zwracają tekst (modele completion). Chat modele otrzymują ustrukturyzowane wiadomości (z klasy BaseMessage) i zwracają AIMessage. Dla modeli LLM używa się szablony typu PromptTemaplate, dla Chat modeli ChatPromptTemplate, gdzie podaje się jakie pole jest czym
``` python
chat_prompt = ChatPromptTemplate.from_messages([ ("system", "Jesteś tłumaczem z angielskiego na francuski."), ("human", "Przetłumacz: {word}") ])
```
Chat modele wspierają bind_tools(), a modele LLM nie. Obecnie raczej używa się wyłącznie Chat modeli.

* LCEL (LangChain Expression Language) - sposób na łączenie komponentów LangChain za pomocą pipe |. Wszystkie te komponenty muszą dziedziczyć po klasie Runnable. Runnable ma interface:
	* invoke - wywołuje łańcuch dla pojedynczego wejścia
	* batch - wywołuje łańcuch dla listy wejść (zoptymalizowane działanie),
	* stream - zwraca chunki odpowiedzi w czasie ich generowania.
Ten interface mają wszystkie obiekty Runnable, włącznie z np. PromptTemplate.
Istnieją też zoptymalizowane asynchroniczne wersje. Runnable pozwala też określać fallbacki (jakiś model przestaje działać i jest przekierowanie na inny), co też jest częścią LCEL.
Inne elementy LCEL:
	- RunnableParallel lub zapis słownikowy {} - pozwala na uruchamianie wielu zadań jednocześnie
	- RunnableLambda - pozwala uruchamiać kod Pythona w łańcuchu LCEL
	- RunnableBranch - pozwala modelowi decydować w jaką stronę ma pójść proces (odpowiednik if/else)
	- .with_structured_output() - gwarantuje output danej klasy, którą się podaje,
	- RunnableConfig - wszelkie metaobiekty, które krążą po aplikacji
	- .with_retry() - pozwala powtórzyć zapytanie ale do innego modelu, ileś razy.

* Prompt Templates - jest to interface API do modelu językowego, korzystające z jinja2, dziedziczą po Runnable. Szablony od Langchain, w przeciwieństwie od f-stringów, automatycznie walidują czy podane wszystkie wymagane zmienne. Pozwalają one wstrzykiwać nie tylko tekst ale inne obiekty z wiadomościami (MesagesPlaceholder). Dzięki partial można wstrzykiwać do templatki nie wszystkie zmienne od razu, tylko wygodnych dla tego momentach. Przez to nie trzeba przenosić słownika ze zmiennymi po całej aplikacji, aby później jest wstrzyknąć. Prompt Template w odróżnieniu od f-stringów potrafi automatycznie parsować zmienne po ich nazwie.
* Output Parsers - obecnie są one już mniej używane, gdyż modele potrafią zwracać JSONy (obsługują Tool Calling i JSON Mode). Teraz używa się metody .with_structured_output(), ale jeśli pojawi się jakiś starszy model , to można użyć StrOutputParser, PydanticOutputParser lub JsonOutputParser, które są obiektami Runnable (with_structured_output to metoda na obiekcie).
* Document Loaders - mają pobierać dane z dowolnego, nieustrukturyzowanego źródła, takiego jak PDF, kanały Slacka, Notion, strony internetowej do ustandaryzowanego formatu typu Document. Document ma atrybuty page_content - czyli plain text, metadata - wszelkie metadane. Można używać metody lazy_load(), która zwraca iterator, dzięki któremu dane są pobierane jeden po drugim (lub w małych batchach), tak by nie zapchać pamięci RAM. Nie są one jednak najbardziej jakościowe i dla trudnych przypadków mogą istnieć lepsze rozwiązania, w tym customowe.
* Retrievers - dziedziczy po BaseRetriever, jest typem Runnable. Na podstawie zapytania tekstowego zwraca listę Documents. Jest to sposób odpytywania bazy wektorowej ale nie tylko, bo można np. odpytywać Wikipedię. Retrievery najpierw szukają w bazie najbardziej podobnych rekordów, ale później zwracają takie, która są jak najbardziej różnorodne. Mogą też parafrazować pytanie do bazy wektorowej, aby wyniki wyszukiwania pokryły temat szerzej. Jednak główną zasadą jest to, że Retriever dostaje na wejściu string i zwraca listę dokumentów.
	* wyszukiwanie MMR (maximum marginal relevance) - najpierw algorytm wyszuka rekordy najbardziej podobne, a później wybierze te, które zapewniają różnorodność.
	* wyszukiwanie hybrydowe przydaje się, jeśli szukamy charakterystycznego tekstu.
	* ContextualCompressionRetriever - wyciąganie z bazy dokumentów oraz odrzucenie tych fragmentów, gdzie nie znajduje się odpowiedź.
* Tools - pozwalają na wszelkie interakcje ze światem zewnętrznym. Umożliwia to LLMowi np. korzystanie z API, połączenie z bazą danych, wykonywanie innych funkcji itp. Model wywołuje dany tool na podstawie nazwy funkcji, docstring (precyzyjny opis), argumenty oraz ich typowanie. Nad funkcją należy dodać dekorator @tool. Model musi jednak posiadać metodę .bind_tools(). Model zwraca AIMessage z tool_calls, kod pythonowy z wybranej funkcji wykonuje się i zwraca wynik, następuje zakończenie iteracji pętli i z tego powodu zwracany jest ToolMessage. Wszystko to (Messages) leci do LLMa, który zwraca odpowiedź w języku naturalnym.
* Tool loop - w momencie uruchomienia invoke modelu, który ma dodane .bind_tools() proces odpytywania LLM przestaje być liniowy, a staje się cykliczny:
	* model otrzymuje polecenie i decyduje czy użyć narzędzia z dostępnych,
	* model zwraca AIMessage, w którym znajduje się pole tool_calls z nazwą narzędzia oraz promptem co ma się wydarzyć
	* wykonanie funkcji pythonowej (oraz zwrócenie wyniku),
	* wynik pakowany jest w ToolMessage i całość dopisywana jest na koniec konwersacji
	* to wszystko (AIMessage i ToolMessage) wysyłane jest do modelu LLM, który podejmuje decyzję czy wyjść z pętli czy zwrócić wynik.
Jest to zoptymalizowana forma pętli while, tak by nie pisać zbędnego kodu.
*  ReAct (Reason & Act) - jest to wzorzec promptowania gdzie model najpierw wypisuje jawnie swoje przemyślenia na dany temat, następnie na podstawie tego promptu pisze prompt jakiego narzędzia należy użyć, jaką akcję podjąć albo co zrobić. Jest to skuteczne dlatego, że daje to trochę więcej czasu modelowi na myślenie (model nie ma takiego czasu realnie i od razu generuje tokeny), rozbija zadanie na dwa kroki, wręcz analogia do myślenia czy postępowania krok po kroku. Natomiast minusem jest to, że jeśli model pomyli się w pierwszym kroku, to kolejny też będzie źle podjęty. Wtedy ważna jest obserwacja, czyli wynik z akcji i weryfikacja czy model postępuje właściwie. Należy ustawić maksymalną liczbę kroków, aby model nie wpadł w nieskończoną pętlę i nie zużył zbyt dużo tokenów.
* Memory - same modele LLM są stateless. W takim razie aby model miał ciągłość konwersacji konieczne jest wstrzykiwanie mu zapisanych wcześniejszych konwersacji. Ale są różne strategie ile takiej historii model powinien dostawać. Może on dostać wszystko (buffer), okno czasowe (k ostatnich), streszczenie konwersacji (streszcza prosty model LLM), może zapisywać wiadomości w bazie wektorowej i przy każdym odpytaniu będą wyciągnięte wiadomości, których wektory są podobne. W LangGraphie pamięcią zarządza Checkpointer: może on zapisywać do RAMu, do bazy danych.
* Callbacks - zamiast używać logging, można w kontekście LangChaina stosować Callbacks, które monitorują wewnętrzne stany, ale też mogą reagować w razie problemów.
* Human in the loop - jeśli agent miałby dostęp do potencjalnie niebezpiecznych narzędzi, należy poprosić użytkownika o potwierdzenie czy się zgadza na dalsze kroki. Służą temu interrupt_before oraz interrupt_after, którymi wskazuje się na węzły gdzie ma dojść do przerwania. Człowiek może wtedy zatwierdzić dalszą akcję, przerwać ją lub zmodyfikować.
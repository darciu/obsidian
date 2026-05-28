**Optymalizacja wyszukań RAG** - można optymalizować wyszukiwania w trzech krokach:
- Pre-retrieval
	- query rewriting/paraphasing/expansion - chodzi o poszerzenie pola wyszukiwania poprzez stworzenie kilku alternatywnych, ale też poprawnych językowo zapytań
	- hypotetical document embedding - LLM generuje hipotetyczną odpowiedź na zapytanie użytkownika, po czym wyszukiwane są w bazie dokumenty, które najbardziej pasują do tej odpowiedzi.
	- routing - przekierowanie zapytania do adekwatnego indeksu w bazie RAG, gdzie najprawdopodobniej znajduje się odpowiedź
- Retrieval - tutaj najważniejszym elementem do optymalizacji jest wyszukiwanie hybrydowe i jego parametry. Można też na poziomie ładowania danych do bazy ustalić odpowiedni chunking strategy, czyli dzielenie na chunki.
- post-retrieval - najważniejszym elementem optymalizacyjnym jest reranking za pomocą CrossEncodera, który ocenia jak znalezione dokumenty czy chunki są adekwatne do początkowego zapytania.

**Metryki oceny RAG**
Standardem jest framework ewaluacyjny RAGAS, który działa w oparciu o koncepcję LLM-as-a-judge. Ten model powinien być dobrym jakościowo modelem, aby ocena była jak najbardziej trafna. Oceny wyników wyszukiwania są wystawiane jako score od 0.0 do 1.0. Może on służyć do przeszukiwania przestrzeni parametrów.
Istnieje coś takiego jak triada RAGAS i są to trzy pytania co do problemu zadania:
- Czy znalazłem odpowiednie dokumenty? czy w zwróconych fragmentach znajduje się odpowiedź.
- Czy opieram się wyłącznie na nich? czy model halucynował odpowiadając na pytania, albo dodawał coś od siebie?
- Czy odpowiadam zadane pytanie? czy model rozwiązuje problem użytkownika, jednocześnie nie lejąc wody.

Hiperparametry powinny być przeszukiwane jednocześnie, gdyż są one ze sobą w wyszukiwaniu RAG silnie sprzężone. Można jednak podzielić to na etap retrievera i generatora, tak by nie płacić kolosalnych pieniędzy za LLM jako sędziego. Należy wybrać ogólny model, nie reasoning czy planujący.

Na podstawie triady budowane są cztery główne mierzalne metryki:
	*Ewaluacja retrievera*:
- context precision - trafność sortowania wyników: czy najbardziej przydatne wyniki są na topie listy wysyłanej do LLM.
- context recall - czy znaleziono wszystkie potrzebne dokumenty z RAG do udzielenia odpowiedzi.
	*Ewaluacja Generatora*:
- faithfullness (wierność) - jest to zabezpieczenie przed halucynacjami. Brana jest odpowiedź i sprawdzane jest w niej każde stwierdzenie pod kątem tego czy pojawiają się informacje, których nie było w odpowiedzi z bazy wektorowej,
- asnwer relevance (trafność odpowiedzi) - czy LLM odpowiada na temat i rozwiązuje problem użytkownika. Model na bazie odpowiedzi stara się przewidzieć jakie było pytanie na początku.


Score dla tych metryk oblicza się poprzez stosunek odpowiedzi poprawnych tak/nie (binarne odpowiedzi) jak zadecydował sędzia.
Zbiór benchmarkowy musi zawierać rzeczywiście to co będzie adekwatne do środowiska produkcyjnego. Więc powinny się tam znaleźć problemy różnej trudności, przykłady negatywne (brak odpowiedzi), błędy w pisowni, skróty myślowe.


**Precision vs recall w strategii chunkowania** - duże chunki to duże prawdopodobieństwo trafienia poprawniej odpowiedzi, więc to zwiększa recall. Wysokie precision to raczej małe i dobrze trafione chunki, czyli mała ilość szumu oprócz nich. Rozwiązaniem w przypadku RAG tego optymalizacyjnego problemu może być połączenie chunków child-parent, tak by zaczynając od małych chunków można było znaleźć i podawać ich szersze wersje, czyli parents.
Jest to open-source baza wektorowa służąca do przeszukiwania wektorów, które są bliskie wybranemu wektorowi. Każdy obiekt danych musi należeć do jakiejś kolekcji. Obiekt danych, oprócz wektora i indeksu, może zawierać również inne dane takie jak: tekst, audio, obraz, wideo oraz properties (dodatkowe pola). Choć poręcznie dla większych plików mieć referencję do zewnętrznego storage gdzie takie dane lepiej przechowywać. Indeksy służą przyspieszeniu przeszukiwania danych.


Indeksy w Weaviate
Służą one do przeszukiwania danych w kolekcji (indeksowanie działa wyłącznie w obrębie jednej kolekcji), tak by nie przeszukiwać ich wszystkich jeden po drugim, ale raczej skupić się na najbardziej trafnych wynikach. Są dwa rodzaje indeksów:
	* wektorowy HNSW (Hierarchical Navigable Small World) - wyszukiwanie od ogółu do szczegółu. Wszystkie wektory (punkty w przestrzeni) leżą początkowo w warstwie 0, a wraz z coraz wyższą hierarchią warstw zostają wybrane tylko punkty najbardziej charakterystyczne, które pomiędzy warstwami są połączone grafem. Punkty mają sąsiedztwo w obrębie danej warstwy (o liczbie sąsiadów decyduje parametr maxConnections). W ostatniej warstwie znajduje się kilka punktów z którego wybierany jest entry point, od którego zawsze zaczyna się przeszukiwanie. To nie jest struktura drzewiasta, gdyż do końcowych punktów mogą prowadzić różne ścieżki (w grafie mogą istnieć pętle). Jest to zaletą, gdyż jeśli algorytm się pogubi, to możliwe jest powrócenie do dobrego rejonu grafu. Algorytm przeszukuje wszystkich sąsiadów pod względem bliskości z danym wektorem. W najwyższych warstwach dzięki temu wykonuje się duże skoki w przestrzeni danych. Jeśli uda się znaleźć najlepszy punkt danych, to schodzi się warstwę niżej i zaczyna od tego samego co wybrany punkt. Small world oznacza małą ilość przeskoków w grafie pomiędzy dowolnymi dwoma punktami (tak jak dowolnych ludzi dzieli na świecie średnio 6 przeskoków). Istnieje jeszcze parametr ef (exploration factor), który oznacza równoległe poszukiwania w grafie - nie jest wybierany tylko najlepszy punkt, ale też sprawdzane jest jego otoczenie (jednocześnie przeszukiwane są alternatywne trasy w grafie). Ma to na celu uniknięcia sytuacji gdy algorytm natrafi na lokalne (a nie globalne) minimum.
	* BM25 (Best Matching 25) - rozwinięcie metody TF-IDF. System punktowy oparty na trzech filarach:
		* częstość słów TF - zamiast jak w starych systemach zliczać częstość słów, to BM25 wprowadza ich nasycenie. Początkowo zliczanie danego słowa powoduje duży przyrost score, ale później ten score rośnie logarytmicznie
		* rzadkość słowa IDF - ważenie jak często rzadkie słowa występują w jednym dokumencie i w całym zbiorze danych
		* normalizacja długości - w zależności od długości tekstu, ważony jest TF.
		W tym algorytmie istnieją dwa główne parametry:
			- k1, który decyduje jak szybko występuje nasycenie
			- b, waga długości tekstu, która wpływa na TF. Zwykle ma wartość 0.75
		Możliwe jest jednoczesne wykorzystanie obu tych algorytmów w sposób Hybrydowy.
	* Reciprocal Rank Fusion - sposób rankingowania wyników wyszukiwania w sposobie hybrydowym. Tworzona jest lista wyników z BM25 i HVSM, każdy dokument dostaje punkty na podstawie swojego miejsca w rankingu, pozycje są sumowane specjalnym wzorem, gdzie wagi dla obu rankingów ustalane są parametrem alpha.
API
Weaviate posiada trzy rodzaje API:
- REST - komunikacja w celu zarządzania kolekcjami, bazami danych, diagnostyka, proste zapytania CRUD
- gRPC - bardzo wydajne i szybkie API do importowania danych, zaawansowane queries, małe zużycie procesora i sieci przez mały overhead zapytania, używane w SDK Pythona
- GraphQL - złożone zapytania w formacie json, bardziej naturalny dla Weaviate niż gRPC


**Semantic search** - wyszukiwanie nie na podstawie słów kluczowych, ale na podstawie kontekstu, czyli podobieństwa embeddingów.
``` python
movies.query.near_text(
	query="old movie",
	distance=0.1,
	limit=5,
	return_metadata=MetadataQuery(distance=True)
)
```
gdzie distance = True zwracanie score wyszukiwania, a distance=0.1 to threshold wyszukiwania

To czy decydujemy się na odległość cosinusową czy l2-squared (dobry do obrazów) ustala się przy inicjalizacji kolekcji.

**Keyword search** - wyszukiwanie BM25 z wszystkich wskazanych properties

``` python
response = movies.query.bm25(
	query="history", 
	limit=5,
	query_properties=["title^3", "description"],
	return_metadata=MetadataQuery(score=True)
)
```
query_properties pozwala podbić priorytet danego pola przy wyszukiwaniu - tutaj title trzykrotnie

sposób ten obsługuje też wildcardy, filtrowanie, operatory logiczne

**Hybrid search** - tak jak wyżej opisane
``` python
response = movies.query.hybrid(
	query="history",
	limit=5,
	alpha=0.6,
	return_metadata=MetadataQuery(score=True)
)
```

W wyszukiwaniach można stosować filtry, wildcardy, operatory logiczne, np.
``` python
filters=Filter.by_property("release_date").greater_than(datetime(2020, 1, 1))
```

**Single prompt vs grouped task** - tak jak nazwa wskazuje pierwszy sposób to pojedyncze pytanie do LLM przy otrzymaniu wyników z wyszukiwarki weaviate per każdy wynik wyszukiwania, a drugi polega na wysłaniu wszystkiego jako jeden tekst, tak by na bazie tych tekstów LLM opracował odpowiedź.

``` python
response = movies.generate.near_text(
	query="dystopian future",
	limit=5,
	single_prompt="Describe in two sentences mood of this movie: {title}"
)

response = movies.generate.near_text(
query="dystopian future",
limit=5,
grouped_task="What do these movies have in common?",
# grouped_properties=["title", "overview"] # Optional parameter; for reducing prompt length
)

```


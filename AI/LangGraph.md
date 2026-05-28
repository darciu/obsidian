
**Pojęcia**
Stan - współdzielona część aplikacji, która trzyma pamięć
Węzeł - funkcje albo operacje w aplikacji. Mają input i output
Graf - sposób w jaki różne węzły są ze sobą skomunikowane. Można go przedstawiać w formie graficznej.
Krawędzie - połączenia pomiędzy węzłami. Mogą być też warunkowe.
Aplikacja ma też entry point oraz końce.
ToolNode - Wykonuje realne operacje na komputerze (sprawdzanie maili, zapis plików itp.). Ręce aplikacji LangGraph.
StateGraph - Plan działania grafu, klasa która kompiluje graf. Zarządza wszystkimi elementami. Mózg aplikacji LangGraph.
Runnable - ustandaryzowany protokół zasad (element), który jest podstawową jednostką budulcową w LangGraph. Te elementy można łączyć w Chain.
Messages - human (wiadomości od człowieka), system (ustalanie kontekstu i instrukcji), function (wynik wywoływanej funkcji), AI (z modeli AI), tool (w zależności od użytego narzędzia).

W funkcjach użytych jako tool nodes LLM czyta też docstring, więc należy podawać tam sensowne opisy. 

**TypedDict vs BaseModel**
W LangGraph dla głównego stanu najlepiej używać TypedDict, a dla wszystkich innych biznesowych pól BaseModel. Ten pierwszy jest bardziej elastyczny, a BaseModel wymusza zwracanie do pól konkretnych danych.
TypedDict podpowiada typy w IDE (zwykły dictionary), a Pydantic (BaseModel) plinuje typów danych, także podczas działania programu (walidacja w locie). Pydantic jest z tego powodu obciążeniem.

Wyświetlanie grafu
```python
from IPython.display import Image, display
display(Image(app.get_graph().draw_mermaid_png()))
```

Istnieje coś takiego jak thread_id, który przenoszony jest pomiędzy kolejnymi uruchomieniami grafu, tak by checkpointer LangGraphu mógł odczytać z bazy danych wcześniejsze wiadomości/stan grafu.
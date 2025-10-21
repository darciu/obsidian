
Instalacja Python'a w odpowiedniej wersji za pomocą brew
```
brew install python@3.8
```


**virtualenv**
```
virtualenv -p /opt/homebrew/bin/python3.10 venv
```

**VENV**
```
python -m venv venv
```


**aktywacja venv**
```
cd venv && source bin/activate
```
komenda *source* wykonuje skrypt
activate to skrypt odpalający virtual enva


**instalacja requirements.txt**
```
pip install -r requirements.txt
```


**Ładowanie zmiennych środowiskowych z .env**
```
source .env && export $(cut -d= -f1 .env)
```


**Uruchamianie aplikacji (na serwerze uvicorn)**
```
uvicorn main:app --app-dir src/
```


**Czyszczenie portu localhost 8000**
```
sudo lsof -i:8000
```

```
kill -9 PID
```

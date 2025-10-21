Git najprostszym ujęciu tworzy obiekty zwane commitami, tj. zestawy zmian w plikach przypisane do danego autora, daty i innych informacji, posiadające swój hash. Commity można znaleźć po hashu w folderze .git/objects, gdzie są one grupowane po dwóch pierwszych znakach tego hashu. Comitty wewnątrz tych plików są w formacie bajtowym (skompresowane). 

Repo na GitHubie nie jest centralnym repo, ale po prostu kolejną wersją repozytorium, nad którym pracujemy. Remote to nazwa na inne repo niż nasze. Te remote mają swoje nazwy konfiguracyjne (aliasy adresów internetowych) zapisane w .git/config:
- Origin - z naszego punktu widzenia "repo prawdy" i ta etykieta jest dodawana automatycznie przy klonowaniu;
- Upstream - inny alias nadawany gdy lokalne repo musi się kontaktować z dwom repozytoriami. Nazwa może być dowolna

Przy pierwszym pushu nowoutworzonej gałęzi, należy wskazać ten alias
```
git push -u origin feature
```
flaga -u to --set-upstream; ta opcja która każe śledzić wybraną gałąź na wskazanym repo jako relację z danym branchem.



**Konfiguracja Git**
Wypisze wszystkie elementy configu
```
git config --list
```

Git sprawdza setup kolejno w takich lokalizacjach:
- katalog_git/etc/gitconfig - dla wszystkich użytkowników (flaga --system)
- ~/.gitconfig - dla poszczególnych userów (flaga --global)
- .git/config - dla danego repo (flaga --local)
- jest jeszcze worktree, gdzie config działa tylko dla części projektu

Każdy kolejny poziom nadpisuje poprzedni


**Repozytorium**

Nowoutworzony plik ma status untracked. Jeśli użyje się komendy git add na nim, to ma wtedy status staged (jest przygotowany do commita). Staged area (index) to jest to co trafi do commita. Commit jest snapshotem stanu plików w czasie, a nie tylko zmian w plikach - natomiast istnieją procesy optymalizacyjne, gdyż commity nie zawierają plików ale wskaźniki do nich (w formie blob z hashem). Po commicie HEAD i index są zgodne.

HEAD - jest jeden na dane worktree i wskazuje na ostatni snapshot w branchu. Untracked files się do niego nie zaliczają. Pliki staged nie są jeszcze w HEAD, bo brakuje im commita.

Historia wszystkich commitów aż do stanu HEAD na danym branchu ale w kolejności od HEAD do przodków. Każdy commit ma parent commit, o ile nie był pierwszym.




Log Pokazuje różnice jakie zaszły w commitach od ostatniego 
```
git log --oneline --graph --decorate --parents
```
- --oneline - skraca zapis każdego commita do pojedynczego wiersza
- --graph - rysuje graf z gałęziami i commitami
- --decorate - dla każdego commita jest jeszcze dołączona lista referencji takich jak tagi
- --parents - wypisuje także SHA commitów parents (w sumie 3 hashe parents + własny)


Można sprawdzać detale o commicie za pomocą komendy

```
cat-file -p hash_commita
```

W Gicie tree jest sposobem przechowywania folderów, a blob to sposób przechowywania plików w commitach. Tree oraz blob również mają swoje hash. Kiedy wejdziemy w hash tree, to zobaczymy jakie obiekty były edytowane

**Merge**
W przypadku merge szukany jest wspólny poprzednik dwóch gałęzi (common ancestor) a następnie obie gałęzie są łączone ze sobą. Taki commit ma dwoje parent commits.

Fast forward merge - możliwy jest tylko wtedy kiedy na mainie (gałąź na którą chce się mergować inną) nie posiada własnych commitów. Wtedy dla podstawowej gałęzi (main) przesuwany jest HEAD tam gdzie jest ostatni commit dla gałęzi mergowanej na nią. Nie jest też tworzony commit merge, więc w logach to wygląda jak praca na głównej gałęzi.
```
Git spróbuje fast-forward, ale jeśli się nie uda, to zrobi commita merge
git merge --ff

Git zawsze tworzy commit merge
git merge --not-ff

Git nie wykona merge, jeśli nie uda się fast-forward
git merge --ff-only
```



**Rebase** 
Pozwala na przeniesienie zman z jednej gałęzi na inną gałąź, jeśli na tej drugiej gałęzi zaszły jakieś zmiany. Wszystkie commity z gałęzi main po rebase są na początku brancha, przed commitami właśnie tego brancha. Rebase nie dodaje z tego powodu żadnego dodatkowego commita, oprócz tych które istnieją.


Usuwanie wszystkich branchy lokalnych oprócz main
```
git branch | grep -v "main" | xargs git branch -D
```

Usuwanie wszystkich untracked plików
```
git clean -fd
```

Reset
Usuwanie ostatnie zmiany w commitach albo zmiany w indexie oraz wszystkie zmiany unstaged.
Opcja soft usuwa tylko ostatni commit, ale nie usuwa zmian w plikach. Te zmiany stają się uncommited (staged)
```
git reset --soft SHA
```

Opcja hard usuwa commity i workingtree (pliki nad którymi pracuję). Można podać SHA danego commita do usunięcia
```
git reset --hard SHA
```

Usunięcie wszystkich nieistniejących zdalnych branchy
```
git fetch --prune
```

Rollback do danego commitu
```
git reset --hard SHA_commita

oraz 

git push --force
```

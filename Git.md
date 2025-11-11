Git najprostszym ujęciu tworzy obiekty zwane commitami, tj. zestawy zmian w plikach przypisane do danego autora, daty i innych informacji, posiadające swój hash. Zmiany te są zmianami w liniach plików, więc jeśli tylko pewne linie uległy zmianie, to Git to odnotowuje, a reszta pozostaje bez zmian. Commity można znaleźć po hashu w folderze .git/objects, gdzie są one grupowane po dwóch pierwszych znakach tego hashu. Comitty wewnątrz tych plików są w formacie bajtowym (skompresowane). 

Repo na GitHubie nie jest centralnym repo, ale po prostu kolejną wersją repozytorium, nad którym pracujemy. Remote to nazwa na inne repo niż nasze (może też być lokalne ale również zdalne). Te remote mają swoje nazwy konfiguracyjne (aliasy adresów internetowych) zapisane w .git/config:
- Origin - z naszego punktu widzenia "repo prawdy" i ta etykieta jest dodawana automatycznie przy klonowaniu. To z tego repo automatycznie są pullowane i pushowane zmiany.
- Upstream - inny alias nadawany gdy lokalne repo musi się kontaktować z dwom repozytoriami. Nazwa może być dowolna
Komenda ustawiająca te aliasy w configu
```
git remote add alias https://github.com/nazwa/repo.git
```

Przy pierwszym pushu nowoutworzonej gałęzi feature, należy wskazać ten alias
```
git push -u origin feature
```
flaga -u to --set-upstream; ta opcja która każe śledzić wybraną gałąź na wskazanym repo jako relację z danym branchem.


**.gitignore**
Plik .gitignore może być w podfolderach projektu, i tylko na tym poziomie będzie on oddziaływał.
W tym pliku można zawrzeć wykluczenie z ignorowania
```
*.txt
!text.txt
```
W pliku .git/info/exclude można zawrzeć lokalne pliki i foldery do ignorowania, które nie będą ignorowane w innych repo (np w zespole).

```
git fetch
```
fetch różni się tym od pull, że pobiera zmiany z wybranego remote repo, ale ich nie integruje z daną gałęzią. Te zmiany są w pliku FETCH_HEAD. Zmiany te można później merge'ować albo cherry pickować
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






Usuwanie wszystkich branchy lokalnych oprócz main
```
git branch | grep -v "main" | xargs git branch -D
```

Usuwanie wszystkich untracked plików
```
git clean -fd
```

**Reset**
Usuwanie ostatnie zmiany w commitach albo zmiany w indexie oraz wszystkie zmiany unstaged.
Opcja soft usuwa tylko ostatni commit, ale nie usuwa zmian w plikach. Te zmiany stają się uncommited (staged)
```
git reset --soft SHA
```

Usuwanie ostatniego commita
```
git reset --soft HEAD~1
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


**Fork**
Akcja nie w Git, ale na platformach hostujących kod, która to tworzy kopię danego repozytorium pod nowym kontem. Ze swojej wersji kodu można robić Pull Request na oryginał i dopiero maintainerzy tamtego repo mogą merge'ować.

Dobrą praktyką jest dodanie do configu upstream oryginalnego repo, aby zaciągać zachodzące tam zmiany na swoją kopię.


**Reflog**
Zapisywane są wszystkie lokalne zmiany czym był HEAD. Przez to możliwe jest cofanie się do wybranego HEAD podając jego numer. Reflog składa się z:
```
41fdeeb (origin/main, origin/HEAD, main) HEAD@{6}: pull: Fast-forward
```
- 41fdeeb - skrócony SHA commita na który przesunął się HEAD
- (origin/main, origin/HEAD, main) - dekoracje, lista referencji wskazująca w obecnej chwili na dany commit, czyli te referencje się zmieniają. Można dzięki temu szybko odnaleźć w reflogu co w przeszłości było np. obecnym HEADem
- HEAD@{6} - kolejność gdzie był HEAD, gdzie 0 to najnowsza pozycja
- pull - rodzaj akcji
- : Fast-forward - message commita albo opis operacji

Za pomocą refloga można cofnąć usunięty branch, jeśli zna się hash
```
git checkout -b branch_name hash
```

Wpisy w reflogu wygasają po 90 dniach.

**Merge conflicts**
Jeśli na dwóch różnych branchach zmodyfikowano te same linie lub ten sam plik, a następnie zachodzi merge, Git nie może sam zdecydować którą ze zmian wybrać. Wtedy należy to zrobić manualnie.

mergowanie z inną gałęzią
```
git merge other_branch
```

```
<<<<<<<< HEAD - nasza sekcja
nasze zmiany
============ - koniec sekcji
ich zmiany
>>>>>>>>>> main - drugi branch
```
Aby merge się udał, należy usunąć ręcznie lub w IDE markery merge conflictu i wybrać jakie zmiany zostają. Następnie należy commitować zmiany.
Podczas konfliktu pomocna może być komenda
```
git diff
```

**Rebase** 
Pozwala na przeniesienie zman z jednej gałęzi na inną gałąź, jeśli na tej drugiej gałęzi zaszły jakieś zmiany. Wszystkie commity z gałęzi main po rebase są na początku brancha, przed commitami właśnie tego brancha. Rebase nie dodaje z tego powodu żadnego dodatkowego commita, oprócz tych które istnieją.

**Squash**
Jest to prasowanie wielu commitów w jeden. Jest to potrzebne kiedy robimy merge i nie chcemy później mieć na głównej gałęzi przesadnie dużo nowych commitów, które ciężko będzie kontrolować. Często używany z merge albo rebase.

Squashowanie w rebase na aktualnej gałęzi
```
git rebase -i HEAD~n
```
ta komenda otwiera edytor, gdzie podaje się commity (p)ickowany czyli bazowy oraz (s)quashowane, czyli te które się zleją z bazowym; n to liczba ostatnich commitów do rebase. Następnie należy, po rozwiązaniu konflików, należy podać
```
git rebase --continue
```

**Stash**
Służy do tymczasowego zapisania aktualnego stanu worktree bez robienia commita, aby móc przejść do pracy nad np. innym branchem lub kiedy trzeba zrobic pull/rebase. Stash ląduje na stosie commitów i zawierają się w nim pliki staged (index) i unstaged (worktree) oraz opcjonalnie nieśledzone i ignorowane. Zalecane odkładać do stash małe zmiany i na krótki termin. Przechowywanie danych jest w konwencji First In Last Out, czyli bierzemy zapisy z góry stosu.
```
git stash
```

wyświetlanie listy stashy (flaga -p pokazuje zmiany jako diff)
```
git stash list -p
```

przywrócenie ostatniego zapisu ze stash listy (nazywany stack)
```
git stash pop
```

**Revert**

Działa on w ten sposób, że tworzy nowy commit, który jest odwrotnością ostatniego commita. Bierze diff pomiędzy danym commitem i jego parentem. Odwraca ten diff, ustawia HEAD w tym miejscu i commituje.

revertowanie trzech ostatnich commitów
```
git revert --no-commit HEAD~3..HEAD && git commit -m "Revert last 3 commits"
```

**Cherry pick**
Wyciąganie jednego commita z całej gałęzi i przenoszenie go na inny jako np. hot-fix. Pozwala to uniknąć robienia merge'a i wyselekcjonowanie potrzebnych na teraz zmian.

Cherry pick jednego commita
```
git cherry-pick sha
```

cherry pick kilku wybranych commitów
```
git cherry-pick sha1 sha2 sha3
```

można też podać zakres commitów
```
git cherry-pick A..B
```

Jeśli poda się nazwę brancha, to jest brany pod uwagę ostatni commit na tym branchu. Kolejność commitów dodanych do głównego brancha jest taka jak kolejność commitów po cherry-pick.

**Bisect**
W poszukiwaniu interesującego commita (zawierającego cokolwiek ale przykładowo bug w kodzie), nie przegląda się wszystkich commitów, ale dzieli się ich historię zawsze na pół i spogląda po której stronie jest, a po której nie interesujący kod. Można taki proces wykonywać ręcznie ale też automatyzować. Commity mogę być w porządku ich występowania ale też po timestampie.

Komendy to
```
git bisect start
git bisect good
git bisect bad
git bisect reset
git bisect run script.sh arguments - automatyzacja w bash
```

**Worktree**
- Co to jest: osobny folder z tym samym repozytorium (katalog roboczy). W głównym repo masz katalog .git; w dodatkowych worktrees .git to tylko mały plik wskazujący na główne repo.
- Po co: żeby mieć dwa (lub więcej) foldery z kodem naraz, np. różne gałęzie w dwóch oknach IDE, bez przełączania i bez stasha.
- Jak to działa: do nowego folderu „wypakowują się” zwykłe pliki projektu, ale historia (commity, obiekty) jest współdzielona z repo głównym. Dzięki temu worktree jest lekkie i tworzy się szybko.
- Ważne zasady:
    - Ta sama gałąź nie może być jednocześnie otwarta w dwóch worktrees tego samego repo.
    - Usunięcie worktree nie usuwa commitów ani gałęzi, ale niezacommitowane zmiany istnieją tylko w tym folderze — zcommiuj lub zrób stash, zanim coś usuniesz.
- Mini-komendy (dla przypomnienia):
    - Utwórz: git worktree add ../wt-feature -b feature (albo na istniejącą: git worktree add ../wt-main main)
    - Usuń wpis: git worktree remove ../wt-feature

Podsumowanie: worktree to „drugi folder” tego samego repo — idealny, lekki sposób, by pracować równolegle na różnych gałęziach w dwóch oknach IDE, bez duplikowania historii i bez żonglowania stashem.

**Tagi**
Tagi w Git służą do trwałego oznaczania konkretnych punktów w historii repozytorium (zwykle commitów). Najczęściej używa się ich do oznaczania wydań wersji (np. v1.0.0) które idą wraz konkretnymi commitami, aby łatwo wracać do dokładnego stanu kodu. Nie służą do pracy nad kodem, ale jako punkty referencyjne. Tagi są immutable.

tworzenie
```
git tag -a "nazwa tagu" -m "message tagu"
```

Numer tagu powinien podlegać wersjonowaniu semantycznemu:
https://semver.org/lang/pl/

Dla numeru wersji MAJOR.MINOR.PATCH, zwiększaj:

1. wersję MAJOR, gdy dokonujesz zmian niekompatybilnych z API,
2. wersję MINOR, gdy dodajesz nową funkcjonalność, która jest kompatybilna z poprzednimi wersjami,
3. wersję PATCH, gdy naprawiasz błąd nie zrywając kompatybilności z poprzednimi wersjami.

HTTP vs SSH


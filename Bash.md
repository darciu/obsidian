Bourne Again Shell


vim
cat
bash file.sh
echo $SHELL
w pliku 

file.sh
``` sh
#!/bin/bash
echo Hello!
```
zapewnia, że zostanie użyta wskazana powłoka

chmod
chown

read ZMIENNA - ładuje wartość do zmiennej

positional arguments - są one wstrzykiwane z zewnątrz skryptu w miejsca numerowane w ten sposób $1 $2 itp. Należy je podać przy uruchamianiu skryptu 
```
./skrypt.sh pierwszy drugi
```
Wtedy pierwszy i drugi zostaną wstrzyknięte w miejsca, gdzie w skrypcie jest $1 i $2

pisanie do pliku (nadpisywanie jeśli istnieje)
``` bash
echo Hello! > hello.txt
```

dopisywanie do pliku (na jego końcu)
``` bash
echo Hello! >> hello.txt
```

Zliczanie słów w pliku
``` bash
wc -w < file.txt
```
wc -w to zliczanie elementów w tekście, z parametrem -w oznaczającym zliczanie słów. Inne flagi:
-l - liczba linii
-m - liczba znaków
-c - liczba bajtów

heredoc, czyli wprowadzanie do programu wielolinijkowego tekstu jako wejścia. Wprowadzany tekst musi kończyć się tym samym znakiem końca (EOF) w nowej linii
``` bash
cat << EOF
```

Test operator
Porównywanie dwóch wartości zwracanie 0 jeśli prawda (sukces), a 1 jeśli niepowodzenie. Jest to związane z tym, że w bashu 0 oznacza właśnie poprawnie wykonaną operację.

Aby wyświetlić wynik z ostatniego testu, należy wpisać:
``` echo
echo $?
```

[ hey = hey] - porównuje dwa napisy
[ yo != hey ]
[ 1 -eq 1 ] - porównuje liczby

Inne operatory, to: -ne, -lt, -ge 


If Elif Else
``` bash
if [ ${1,,} = darek ]; then

echo "lost again"

elif [ ${1,,} = help]; then

echo "Enter username"

else

echo "Else statement"

fi
```
${1,,} - pierwszy argument podany do skryptu
${var,} - zamienia na małą literę tylko pierwszy znak zmiennej var
${var,,} - zamienia na małą literę wszystkie znaki
${var^} - zamienia na dużą literę pierwszy znak
${var^^} - zamienia na dużą literę wszystkie znaki
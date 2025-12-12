Dobre zaplanowanie testu A/B powinno obejmować: postawienie prawidłowej hipotezy, przewidzenie jakie parametry należy badać i jak wielka ma być próba, uruchomienie testu i zbieranie wyników, a następnie ich analiza.

W testowaniu AB nie wystarczy porównywać średnich obu grup kontrolnych, nawet jeśli mają one bardzo liczne próby. Należy też uwzględnić wariancję oraz istotność tej różnicy, a także czy efekt jest istotny biznesowo.

Primary metric - powinna istnieć tylko jedna na test i jest to miara, którą mierzymy w grupach eksperymentalnej i kontrolnej aby stwierdzić czy zachodzi istotna statystycznie różnica pomiędzy tymi grupami.

Stawianie hipotezy:
- H0 - null hypotesis, czyli zaprzeczenie naszego założenia
- H1 - alternative hypotesis, czyli akceptacja naszego założenia

Projekt testu A/B:
- Power analysis - określenie wszystkich parametrów testu przy zadanej istotności:
	- moc testu (power of the test)
	- poziom istotności (significance level) 
	- minimalny wykrywalny efekt testu (minimum detectable effect MDE) - jaką minimalną zmianę $delta$ chcemy widzieć w proponowanej zmianie; poniżej takiej zmiany nie widzimy sensu we wdrażaniu zmiany; do ustalenia z biznesem
- ustalenie minimalnej wielkości próby - na podstawie poprzednio ustalonych parametrów
- czas trwania testu - liczba próby/ilość zdarzeń w danej jednostce czasu; trzeba też uwzględniać cykliczność danych procesów, ale zbyt długie trzymanie testu może spowodować, że stare dane są już nieaktualne

Celem testu AB jest albo: decyzja czy wyliczone p-value jest mniejsze niż zakładany poziom istotności, czy też przedziały ufności nie zachodzą na siebie.


**p-value**
Ta wartość wynika z danych empirycznych jakie mamy po zmierzeniu testów. Przed samymi testami ustala się jedynie poziom istotności α (zwykle 0.05) jako limit na częstość fałszywych alarmów (błąd I typu) a p-value wynika już z danych  zdobytych podczas testów. 
Im mniejsze wyliczone p-value przy jednoczesnym wniosku, że średnie z obu grup testu A/B się od siebie różnią, to możemy być bardziej pewni (wciąż jest to prawdopodobieństwo a nie pewność), że tak w rzeczywistości jest co skutkuje odrzuceniem H0. Jeśli p-value jest większe od α, to nie mamy podstaw do odrzucenia H0. 
P-value to procentowa wartość tego, że chociaż wyniki z obu grup od siebie są różne, to jednak mamy do czynienia z tym samym rozkładem danych.

Wzory dla p-value zależą od przyjętej metryki:

- konwersja (wartości 0 lub 1)
Najpierw obliczamy statystykę testową Z-score:
$$Z = \frac{\hat{p}_B - \hat{p}_A}{\sqrt{\hat{p}_{\text{pool}} (1 - \hat{p}_{\text{pool}}) \left( \frac{1}{n_A} + \frac{1}{n_B} \right)}}$$

$\hat{p}_A = \frac{x_A}{n_A}$ - konwersje w grupie Axa przez liczbę użytkowników A

$$\hat{p}_{\text{pool}} = \frac{x_A + x_B}{n_A + n_B}$$
spoolowana konwersja obu grup

$${\sqrt{\hat{p}_{\text{pool}} (1 - \hat{p}_{\text{pool}}) \left( \frac{1}{n_A} + \frac{1}{n_B} \right)}}$$
Błąd standardowy spoolowany (SE), który mówi jak duże wahania są w wynikach

Wartość Z jest odległością od środka rozkładu normalnego w jednostkach odchyleń standardowych. P-value to pole pod krzywą rozkładu normalnego dalej od środka tego rozkładu niż obliczona wartość Z. Tą wartość odczytuje się z tablic statystycznych w zależności od tego czy test jest jednostronny czy dwustronny.

Przykładowo Z = 1.96 w tabeli Z daje wartość 0.4750 dla jednej strony. Pole całej jednej strony rozkładu normalnego to 0.5

p-value = 0.4750 - 0.5 = 0.025

Dla dwustronnego testu p-value to 0.05.

- wartości ciągłe (np. CTR)

Zamiast Z-score stosujemy test T Studenta dla dwóch niezależnych prób.

$$T = \frac{(\bar{X}_B - \bar{X}_A)}{\sqrt{s_p^2 \left( \frac{1}{n_A} + \frac{1}{n_B} \right)}}$$

**$\bar{X}_A$** i **$\bar{X}_B$**  - obserwowane średnie wartości w Wariancie A (kontrola) i Wariancie B (eksperyment)
**$n_A$** i **$n_B$** -  liczność próby (liczba użytkowników/zdarzeń) w Wariancie A i B

$$\sqrt{s_p^2 \left( \frac{1}{n_A} + \frac{1}{n_B} \right)}$$
błąd standardowy różnicy

$$s_p^2 = \frac{(n_A - 1)s_A^2 + (n_B - 1)s_B^2}{n_A + n_B - 2}$$
wariancja spoolowana.

P-value to pole pod krzywą Rozkładu T Studenta, które znajduje się dalej od środka niż obliczona wartość T, analogicznie do Z score.

Innym wariantem rozkładu Testu T Studenta jest Test Welcha, który jest bardziej odporny na różne wariancje obu grup kontrolnych oraz na nierówne licznościowo grupy. Zalecane rozwiązanie.

**Alfa α**

Jest to próg istotności, wartość ustalana przy projektowaniu testu A/B aby decydować czy obliczone p-value jest wystarczająco małe by odrzucić H0. Maksymalne ryzyko jakie jesteśmy zaakceptować by popełnić błąd I rodzaju (False Positive), czyli niesłusznie uznać wariant eksperymentalny za lepszy od kontrolnego.
Standardem wartość 0.05, gdyż nie opłaca się ponosić dodatkowych kosztów testowania, ale też 1/20 pomyłek FP jest akceptowalne. Oczywiście można zmienić ten próg do nawet 0.01 i mniej, jeśli zmiana jest krytyczna.

**Moc testu i Beta β**
Parametr ten kontroluje błąd II rodzaju (False Negative), czyli prawdopodobieństwo błędnego przyjęcia H0, podczas gdy należało ją odrzucić na korzyść H1.

Moc testu to prawdopodobieństwo przeciwne do beta:
1 - β
i mówi jakie jest prawdopodobieństwo wykrycia różnic między grupami kontrolnymi i odrzucenie H0, kiedy jest to zasadne. Moc testu zwykle jest ustalana na poziomie 80% i nie musimy obliczać tego parametru. Ten parametr służy właściwie do obliczania minimalnej wielkości próby.


**Minimalna wielkość próby (n)**

Warto za to ustalić, znając inne parametry, jaka powinna być minimalna wielkość próby. Jest to miara dla jednej grupy i najwłaściwiej aby obie próby dla grup były robie równe.


$$n \approx \frac{(Z_{1-\alpha/2} + Z_{1-\beta})^2 \cdot [p_A(1-p_A) + p_B(1-p_B)]}{(\text{MDE})^2}$$
$Z_{1-\alpha/2}$ i $Z_{1-\beta}$ - wartości odczytane z tablic Z
$p_A$ i $p_B$ - obliczone oczekiwane konwersje; komponent ten odpowiada za wariancję danych. Im bardziej rozstrzelone wyniki ($p_A$ zbliża się do wartości 0.5), tym wariancja jest większa. Dla metryki typu średnia część wzoru $[p_A(1-p_A) + p_B(1-p_B)]$ zamieniana jest na $2\sigma^2$.

**Przedziały ufności (CI)**
Jeśli badamy jakąś próbę z zadanym poziomem ufności (1-$\alpha$), możemy podać zakres prawidłowych wyników wokół wyliczonej średniej, gdzie z takim prawdopodobieństwem jakie wynosi poziom ufności (np. 95%) próbkując dane z całej populacji nasza średnia wpadnie w ten przedział ufności. Czyli im mniejszy poziom ufności, tym mniejszy margines błędu (przedział ufności), bo szansa na trafienie w ten przedział maleje.

Dla konwersji:
$$\text{CI} = \hat{p} \pm Z_{\alpha/2} \times \sqrt{\frac{\hat{p}(1-\hat{p})}{n}}$$
$\hat{p}$ - wyliczona (obserwowana) konwersja 
$Z_{\alpha/2}$ - wartość krytyczna 

W teście AB można odrzucić H0, jeśli przedziały ufności obu grup na siebie nie nachodzą.
Zmniejszając $\alpha$ zwiększane są przedziały ufności CI, przez co szansa na odrzucenie H0 maleje.


**Standard Error vs Standard Deviation**

SD ($\sigma$) - mierzy rozrzut danych w wylosowanej próbie wokół jakiejś średniej. Dla rozkładu normalnego jedna $\sigma$ to 68%, 2$\sigma$ to 95%, 3$\sigma$ to 99,7% wszystkich danych z populacji od średniej.

$$\text{Odchylenie Standardowe} (\sigma) = \sqrt{\frac{\sum(x_i - \bar{x})^2}{n-1}}$$

SE - mierzy błąd estymatora z danej próby - jak bardzo jest bliski rzeczywistej średniej. Im mniejsza wartość, tym lepiej

$$\text{Błąd Standardowy} (\text{SE}) = \frac{\sigma}{\sqrt{n}}$$
Z tego wniosek taki, że zbieranie coraz to większej ilości danych nie zmienia odchylenia standardowego, bo natura danych jest w miarę taka sama, ale zmniejsza to błąd standardowy.


**Wartość krytyczna vs pomiar z tablicy Z (Z-score)**

Pierwszy termin to próg decyzyjny, a drugi to wartość obliczana jako statystyka testowa. Jeśli Z-score przekracza Z krytyczne, to odrzucamy H0.


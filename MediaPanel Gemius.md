Zakładanie konta na e.gemius.com (Gemius Audience)
https://confluence.grupawp.pl/pages/viewpage.action?pageId=83051360
Istnieje też możliwość dostępu po API (w odpowiednim pakiecie dostępu).

MediaPanel
https://e.gemius.com/login

Pomiary Cross-Audience, czyli online i offline (internet, telewizja i radio). Gemius prowadzi te badania z PBI oraz KBR. Gradacja danych to jeden dzień. Badani są lokalni oraz globalni wydawcy.

Pojęcia:
- Walled gardens - witryny nie pozwalające na skryptowanie zewnętrznymi kodami (np. facebook).
- Site-centric - metoda badania części internetowej oparta na skryptach, które wywołują się na wejściu do witryny/aplikacji. Tracking po stronie serwera, mierzący dostępy użytkowników z mobile/desktop.
- User-centric - aktywność użytkowników mierzona jest na nieoskryptowanych aplikacjach, telewizji, radiu. Dane zbierane są dzięki oprogramowaniu raportującemu. Panelista może posiadać zmodyfikowany smartfon, który jest mu przekazywany podczas rekrutacji lub też uwzględniane są cookies z witryn.
- Real users - wskaźnik jaka liczba osób odwiedza wybrany kanał mediowy (co najmniej jedna odsłona w zdefiniowanym zakresie czasu). W analizie cross-mediowej, osoba korzystająca z radia, telewizji i internetu walled gardens i open web, będzie postrzegana jako jeden użytkownik (wspólny zasięg wszystkich kanałów mediowych).
- Pasywny pomiar konsumpcji mediów - paneliści, którzy mają miernik (dedykowany smartfon) nie muszą nic robić, aby takie pomiary były dokonywane. Gemius ogranicza kontakt z panelistami tylko do ostatecznych przypadków (przykładowo, jeśli smartfon z miernikiem został przekazany innej osobie i zmienił się profil geolokalizacji/zachowań, następuje kontakt z panelistą w celu wyjaśnienia sytuacji). Taki smartfon nasłuchuje jakie stacje radiowe/telewizyjne są w pobliżu panelisty. Ale też zbierane są dane z wizyt mobile na witrynach.
- Pomiar materiałów strumieniowych w internecie - pomiar tego co dzieje się na player'ach internetowych. Nowość w Gemius.
- Kontakty out-of-home - pomiar konsumpcji mediów poza miejscem zamieszkania. Na podstawie zachowań panelisty ustalane są dwa punkty: statyczny i dynamiczny. Statyczny to adres zamieszkania, który został podany w podpisanej umowie. Dynamiczny ustalany jest z zachowania panelisty (ostatnie 56 dni), gdzie najczęściej przebywa w godzinach 22:00 - 6:00 (pora snu). Aby określić kiedy następuje kontakt z out-of-home, stosuje się dokładność pomiaru GPS. Jeśli panelista znajduje się od punktu domu 100m + dokładność pomiaru, uznaje się, że jest out-of-home. Konsumpcja telewizja jest podzielona na konsumpcję domową i poza domem, słuchanie radia jest zebrane w całość. Dane te, w ramach współpracy, trafiają też do Nielsena.
- Paneliści desktop - użytkownicy otrzymują ankietę, w której przekazują swój profil demograficzny. Po wypełnieniu tej ankiety, panelista staje się cookie-panelistą - pierwszy poziom panelisty. Kolejnym, drugim poziomem, jest instalacja software, rozszerzenia w przeglądarce. Rozszerzenie to potrafi rozpoznawać reklamy (ad-drill), jakie strony odwiedził użytkownik (dane również dla stron nieoskryptowanych).
- Paneliści hardware - paneliści otrzymują od Gemius smartfony, które są modyfikowane na poziomie systemu operacyjnego. Dzięki temu widać jakie aplikacje są otwierane, jakie są odwiedzane strony, dostęp również do lokalizacji i mikrofonu. Mikrofon ciągle nasłuchuje otoczenie, jednak dane audio nie są zbierane. Zamiast tego zbierane i wysyłane są sygnatury dźwięku, które są porównywane z bazą sygnału referencyjnego. Dzięki temu wiadomo czy panelista słucha radia, czy ogląda telewizję. Utrzymanie tego panelu jest drogie, więc jest w nim utrzymywana reprezentacja płci, wieku oraz miejsc zamieszkania. Mierzonych jest 80 stacji TV i 23 radiowe. Paneliści hardware mają umowy na 2 lata, która zwykle jest przedłużana.


Liczby poszczególnych panelistów
![[Pasted image 20240215140107.png]]

Panel hardware liczy 2500, jednak rozdanych smartfonów to około 3000 (część z nich jest wyłączona, w serwisie itp.).

* Pomiar sonarem - sonar, to rodzaj skryptu, który wywołuje się co sekundę z prawdopodobieństwem 1/40. Wtedy wywoływany jest na zakładce event (i dokonywany pomiar). Można w ten sposób określić jaki czas użytkownik spędzał na stronie, jednocześnie nie obciążając pracy strony.
* Wizyta - ciąg kolejnych odsłon wygenerowanych przez jedną osobę na stronie, gdzie przerwa pomiędzy odsłonami nie przekracza 30 minut.
* Constant Panel - każdy panelista w nim ma identyczną wagę (mini populacja wirtualna). Stała waga pozwala pokazywać dane z dowolnego okresu czasu. Rotacja użytkowników jest niska.
* Struktura populacji - Polacy w wieku 7 - 75. Ta populacja ustalona jest na 33 mln osób. Dla osób nie korzystających z internetu, struktura dopełnienia populacji jest z danych z GUS.
* Próg dla witryn - witryna musi przekroczyć liczbę 100 panelistów, aby była prezentowana. Inną opcją jest oskryptowanie witryny (site-centric).





Algorytm stosowany do problemów gdzie występuje wiele opcji (ramion bandyty) do wyboru, jednak nie wiemy jakie wygrane dają te opcje. Poprzez korzystanie z tych opcji możemy określić średnie wygrane, a także jaki zakres odchylenia jest od tej średniej.

Podstawowy wzór:
![[Pasted image 20250608224921.png]]
Ramię i zostało mało razy przetestowane, to mianownik sprawia, że wartość ze wzoru rośnie. Wybierane jest ramię, które ma najwyższą wartość UCB. Przez to w jakimś okresie czasu eksplorowane są wszystkie ramiona, nawet te z małą średnią nagrody.

Zawsze przy rozpoczęciu działania algorytmu należy kilkukrotnie (przynajmniej jeden raz) użyć każdego z ramion. Algorytm UCB nie może utknąć w optimum lokalnym, gdyż dla nieużywanych ramion rośnie wartość UCB.

Nazwa Upper Confidence Bound oznacza górny przedział ufności. Chodzi o drugą część wzoru, gdzie z przedziału ufności bierze się tylko jego górną granicę i dodaje się ją do średniej wygranej. Ta górna granica przedziału oznacza najlepszą możliwą nagrodę, jaka może się wydarzyć w danym przedziale ufności.

Czy UCB działa również wtedy kiedy nie ma nagród, lub zdarzają się bardzo rzadko?

**Warianty UCB**

- Epsilon-greedy - Nie jest to UCB, ale podobny algorytm. Greedy oznacza, że istnieje tylko pierwsza składowa wzoru i wybierane jest ramię z największą średnią. Jednak epsilon to prawdopodobieństwo wybierania danego ramienia.
- UCB-tuned - element eksploracji nie wynika wyłącznie z ilości razy pociągnięcia za to ramię, ale wynika z wariancji wartości jakie były otrzymywane. Czyli drugi składnik wzoru, a przez to górny przedział ufności, obliczany jest na podstawie wartości nagród. Usuwany jest przez to problem, gdzie UCB wybiera ramię o dużej średniej, ale również o dużej wariancji wygranych, przez co zdarzają się tam również małe wygrane. Jeśli wariancja jest duża, to takie ramię jest częściej eksplorowane.
- UCB2 - różni się tym od UCB w wersji podstawowej, że gdy wybierzemy dane ramię, to pociągamy je wtedy kilkukrotnie. Im więcej razy testowaliśmy dane ramię, tym te serie pociągnięć są dłuższe. Algorytm polecany wtedy gdy przełączanie się z ramienia na inne ramię niesie za sobą jakiś koszt.
- Sliding Window UCB - brane pod uwagę są tylko najnowsze dane o wielkościach nagród. Dobre rozwiązane, jeśli chcemy by algorytm adaptował się szybciej do zmieniającej się rzeczywistości.
- Discounted UCB - wersja podobna do powyższej, jednak im starsze pomiary, tym mają mniejszą wagę.
- LinUCB - Linear UCB, inaczej Contextual Bandits. Każde ramię, oprócz średniej, ma jeszcze wektor cech, które wynikają z kontektsu (liczbowe wartości określające kontekst danego ramienia). Cechy te odnoszą się do użytkownika, który ma przed sobą dylemat, jakie ramię pociągnąć aby zyskać największą nagrodę. Oczekiwana nagroda danego ramienia to liniowa zależność pomiędzy wektorem cech a wektorem wag, który jest oszacowywany.
![[Pasted image 20250609003830.png]]

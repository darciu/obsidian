Na początku każda z opcji ma taki sam rozkład Beta(1,1), gdzie dla każdej opcji z osobna losuje się wartości z tego rozkładu. Algorytm wybiera opcję, która ma największą wartość. Jeśli udało się przewidzieć w ten sposób kliknięcie, to zwiększany jest parametr rozkładu alfa + 1. W przeciwnym przypadku zwiększa się parametr beta + 1. Ten algorytm w swojej prostocie zawiera eksplorację (wciąż możliwe jest wybranie każdej opcji, gdyż wartości z rozkładu Beta są wybierane losowo), a także eksploatację (najlepsze opcje będą wybierane najczęściej).

**Rozkład Beta**

Jeśli chodzi o sam rozkład Beta, to charakteryzuje się on dwoma parametrami: alfa i beta (wartości powyżej zera) i jest w zakresie wartości [0,1]. Jeśli są one sobie równe, to rozkład jest symetryczny względem punktu 0.5. W szczególnym przypadku, gdy alfa = beta =1, to jest to rozkład jednostajny:

![[Pasted image 20250615225942.png]]

Dla wartości powyżej 1, taki rozkład nabiera kształt dzwonu:
![[Pasted image 20250615230131.png]]

Przy zwiększaniu alfa, ta górka idzie w prawą stronę, beta cofa ją w lewą.
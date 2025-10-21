Kompilowanie pliku
```
go build file.go
```

Uruchamianie skompilowanego pliku
```
./file
```


Explicit variable declaration - dokładnie i odgórne podanie jakie typu jest zmienna
```
var number uint8 = 200
```

Implicit variable declaration - Golang sam zgaduje jakiego typu jest zmienna po przypisaniu do niej wartości
```
number := 6
```

Formatowanie i określanie typu zmiennej
```
fmt.Printf("%T", number)
```

Formatowanie i wkładanie wartości zmiennej
```
fmt.Printf("%v", number)
```


Formatowanie i zapis do zmiennej
```
var text string = fmt.Sprintf("Przykład formatowania %05d", 45)
fmt.Printf(text)
```

Przykład scannera, i konwersji tekstu na int
```
package main

import (

"bufio"
"fmt"
"os"
"strconv"

)

func main() {
scanner := bufio.NewScanner(os.Stdin)
scanner.Scan()
input, _ := strconv.ParseInt(scanner.Text(), 10, 64)
fmt.Printf("100 minus podana liczba to %d \n", 100-input)

}
```

Konwersja typów numerycznych
```
uint8(number)
```

Operatory logiczne
! - NOT
&& - AND
|| - OR


IF
```
func main() {

if 12 < 11 {
	fmt.Println("Opcja 1")
} else if 11 < 10 {
	fmt.Println("Opcja 2")
} else {
	fmt.Println("Opcja 3")
}

}
```

FOR
```
x := 1
for x < 10 {
fmt.Println(x)
x++
}
```

Inny zapis
```
for x := 1; x < 10; x++ {
fmt.Println(x)
}
```

SWITCH
```
ans := 1

switch ans {
case 1, -1:
fmt.Println("1")
case 2:
fmt.Println("2")
default:
fmt.Println("3")
}
```

ARRAY - mają stałą długość i zadeklarowany typ danych; indeksy zaczynają się od zera
```
var arr [5]int
arr[1] = 200
fmt.Println(arr)
```
lub
```
arr := [4]int{1, 2, 3, 5}
fmt.Println(arr)
```


SLICE - to wycinek arraya; ma length oraz capacity, które oznacza ile pozostało arraya od lewego pointera slice'a

Przykład re-slicowania
```
var x [5]int = [5]int{2, 3, 4, 5, 6}
var sl []int = x[1:3]
fmt.Println(sl[:cap(sl)])
```

RANGE - iteracja po indeksach i wartościach
```
for i, element := range a {
	fmt.Printf("%d: %d", i, element)
}
```

MAP
Explicite; poniżej sposób na sprawdzenie czy dany klucz istnieje (jeśli nie to zwraca false)
```
var mp map[string]int = map[string]int{
	"one": 1,
	"two": 2,
	"three": 3,
}
fmt.Println(mp["two"])

val, ok := mp["tonny"]
fmt.Println(val, ok)
```

FUNKCJE
```
func algebra(x1, x2 int) (z1, z2 int) {
	defer fmt.Println("after return")
	z1 = x1 + x2
	z2 = x1 * x2
	fmt.Println("before return")
	return
}

func main() {
	fmt.Println(algebra(3, 7))
}
```
defer wykonuje się po returnie

przypisanie i wykonanie funkcji dla jakiegoś parametru
```
test := func(x int) int {
	return x * -1
	}
fmt.Println(test(8))
```

podawanie funkcji do innej funkcji
```
func test2(otherFunc func(int) int) {
	fmt.Println(otherFunc(12))
}

func main() {
	test := func(x int) int {
	return x * -1
}
test2(test)
}
```
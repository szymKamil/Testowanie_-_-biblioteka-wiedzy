# functions

# Funkcje

1. Function<T, R>
   Opis: Interfejs, który reprezentuje funkcję, która przyjmuje jeden argument typu T i zwraca wynik typu R.

Parametry:

T – typ wejściowy (argument).
R – typ wynikowy (wartość zwracana).
Metody:

R apply(T t) – wykonuje funkcję na podanym argumencie.
default Function<T, V> andThen(Function<? super R, ? extends V> after) – zwraca nową funkcję, która najpierw wywołuje tę funkcję, a potem wywołuje inną funkcję after na wyniku.
default Function<V, R> compose(Function<? super V, ? extends T> before) – zwraca nową funkcję, która najpierw wywołuje funkcję before, a potem tę funkcję.
Przykład użycia:

java
Skopiuj kod
Function<Integer, String> intToString = i -> “Liczba:” + i;
System.out.println(intToString.apply(5)); // Wypisze: Liczba: 5 2. UnaryOperator
Opis: Rozszerza Function<T, T> – reprezentuje funkcję, która przyjmuje jeden argument typu T i zwraca wartość tego samego typu T. Jest używane, gdy wejście i wynik są tego samego typu.

Parametry:

T – typ wejściowy i wynikowy.
Metody:

T apply(T t) – wykonuje funkcję na podanym argumencie.
default UnaryOperator andThen(UnaryOperator<? super T> after) – zwraca nową funkcję, która najpierw wywołuje tę funkcję, a potem wywołuje funkcję after.
Przykład użycia:

java
Skopiuj kod
UnaryOperator increment = i -> i + 1;
System.out.println(increment.apply(5)); // Wypisze: 6 3. BiFunction<T, U, R>
Opis: Interfejs, który reprezentuje funkcję, która przyjmuje dwa argumenty (T i U) i zwraca wynik typu R.

Parametry:

T – pierwszy typ wejściowy.
U – drugi typ wejściowy.
R – typ wynikowy.
Metody:

R apply(T t, U u) – wykonuje funkcję na dwóch podanych argumentach.
default BiFunction<T, U, V> andThen(Function<? super R, ? extends V> after) – zwraca nową funkcję, która wywołuje tę funkcję na dwóch argumentach, a potem wywołuje funkcję after na wyniku.
Przykład użycia:

java
Skopiuj kod
BiFunction<Integer, Integer, Integer> add = (a, b) -> a + b;
System.out.println(add.apply(3, 4)); // Wypisze: 7

1. BiPredicate<T, U>
   Opis: Interfejs, który reprezentuje predykat (funkcję zwracającą boolean), który przyjmuje dwa argumenty typu T i U.

Parametry:

T – pierwszy typ wejściowy.
U – drugi typ wejściowy.
Metody:

boolean test(T t, U u) – sprawdza, czy oba argumenty spełniają warunek.

Przykład użycia:

````java
BiPredicate<Integer, Integer> isSumGreaterThanTen = (a, b) -> (a + b) > 10;
System.out.println(isSumGreaterThanTen.test(5, 7));  // Wypisze: true
// ```

5. Predicate<T>
   Opis: Interfejs reprezentujący predykat (funkcję, która zwraca boolean), który przyjmuje jeden argument typu T.

Parametry:

T – typ wejściowy.
Metody:

boolean test(T t) – sprawdza, czy argument spełnia warunek.
default Predicate<T> and(Predicate<? super T> other) – zwraca nowy predykat, który łączy obecny predykat z innym, zwracając true tylko, jeśli oba warunki są spełnione.
default Predicate<T> or(Predicate<? super T> other) – zwraca nowy predykat, który zwraca true, jeśli przynajmniej jeden z warunków jest spełniony.
Przykład użycia:

```java
Predicate<Integer> isEven = x -> x % 2 == 0;
System.out.println(isEven.test(4));  // Wypisze: true
// ```

6. Consumer<T>
   Opis: Reprezentuje funkcję, która przyjmuje jeden argument typu T i nic nie zwraca (efekt uboczny).

Parametry:

T – typ wejściowy.
Metody:

void accept(T t) – wykonuje operację na podanym argumencie.
Przykład użycia:

java
Skopiuj kod
```java
Consumer<String> print = s -> System.out.println(s);
print.accept("Hello, Java!");  // Wypisze: Hello, Java!
````

Każdy z tych interfejsów jest szeroko stosowany w Javie, szczególnie w połączeniu z metodami strumieni, które umożliwiają funkcjonalne podejście do przetwarzania danych.

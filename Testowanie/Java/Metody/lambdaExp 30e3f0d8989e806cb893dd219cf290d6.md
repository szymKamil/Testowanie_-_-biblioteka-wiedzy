# lambdaExp

# Lambda

W Javie wyrażenie lambda (ang. lambda expression) to sposób definiowania krótkiego, jednorazowego fragmentu kodu, który może być przekazany jako parametr metody lub przypisany do zmiennej. Wyrażenia lambda wprowadzono w Java 8, aby ułatwić pracę z kodem funkcyjnym, co pozwala na bardziej zwięzłe i czytelne kodowanie – zwłaszcza przy użyciu interfejsów funkcyjnych.

> Lambda uważane są za wyrafinowane klasy anonimowe.
> 

JAVA *WNIOSKUJE* o wyrażeniu lambda na podstawie użytej metody i zawartej w nim lambra expression.

Składnia wyrażeń lambda
Lambda składa się z:

Listy parametrów (w nawiasach).
Strzałki -> (arrow), która rozdziela parametry i ciało wyrażenia.
Ciała wyrażenia, które może być pojedynczym wyrażeniem lub blokiem kodu.
Przykładowa lambda:

```java
(parametry) -> wyrażenie
// lub
(parametry) -> { blok kodu }
```

Przykład zastosowania
Dla uproszczenia, załóżmy, że mamy interfejs funkcyjny Runnable:

```java
Runnable runnable = () -> System.out.println("Hello, lambda!");
runnable.run();
```

Powyżej widzimy wyrażenie lambda, które implementuje metodę run interfejsu Runnable. Wyrażenie to nie przyjmuje parametrów (()), a jego ciało jedynie wypisuje tekst na konsolę.

Lambda a interfejsy funkcyjne
Lambda działa z interfejsami funkcyjnymi, czyli interfejsami posiadającymi tylko jedną metodę abstrakcyjną (np. Runnable, Callable, Comparator, Function, Consumer). Interfejsy te mogą być oznaczone adnotacją @FunctionalInterface, choć nie jest to konieczne.

# Zalety lambd

- Zwięzłość: Pozwala na bardziej kompaktowy zapis kodu, zwłaszcza w przypadku krótkich operacji.
- Czytelność: Dzięki krótszemu zapisowi, kod staje się bardziej przejrzysty.
- Łatwiejsze programowanie funkcyjne: Umożliwia bardziej funkcyjne podejście do pisania kodu, szczególnie w kontekście operacji na kolekcjach (np. filtrowania, mapowania, redukcji).
Przykład z interfejsem Comparator

```java
Skopiuj kod
List<String> names = Arrays.asList("Anna", "Tom", "John");
names.sort((a, b) -> a.compareTo(b));
System.out.println(names);
```

Lambda (a, b) -> a.compareTo(b) jest krótką implementacją porównywania dwóch łańcuchów znaków w celu posortowania listy.
Lambda expressions w Javie stanowią fundament nowoczesnego podejścia do pracy z kolekcjami oraz umożliwiają bardziej zwięzłe i funkcyjne podejście do pisania kodu.

> Wyrażenia lambda w Javie mogą być używane tylko z interfejsami funkcyjnymi.
> 

> *!Uwaga!* Zmienne użyte w wyrażeniach zmienna są traktowane jako `final`, tj. nie można ich zmienić w kodzie, nawet po wykonaniu.
> 

Czym jest interfejs funkcyjny?
Interfejs funkcyjny (Functional Interface) to interfejs, który posiada dokładnie jedną metodę abstrakcyjną. Można go wykorzystać do reprezentowania funkcji lub operacji, którą można przekazać jako argument do innej metody lub przypisać do zmiennej. W praktyce interfejsy funkcyjne stanowią podstawę wyrażeń lambda w Javie.
Inaczej SAM - Single Abstract Method.
Interfejsy funkcyjne są oznaczane za pomocą adnotacji @FunctionalInterface, chociaż ta adnotacja nie jest wymagana. Dodanie jej pomaga jednak kompilatorowi wykryć błędy, jeśli interfejs będzie miał więcej niż jedną metodę abstrakcyjną.

```java
@FunctionalInterface
interface Greeting {
    void sayHello(String name);
}
```

Interfejs Greeting posiada tylko jedną metodę abstrakcyjną – sayHello, co czyni go interfejsem funkcyjnym. Teraz możemy go użyć z wyrażeniem lambda:

```java
Greeting greeting = (name) -> System.out.println("Hello, " + name);
greeting.sayHello("John");
```

Dlaczego interfejsy funkcyjne są wymagane do lambd?
Java wymaga interfejsów funkcyjnych do wyrażeń lambda, ponieważ lambda musi mieć jednoznaczną sygnaturę – sposób, w jaki będzie wywołana. Interfejsy funkcyjne pozwalają przekazać lambdę jako implementację jednej konkretnej metody, co eliminuje wieloznaczność.

Typowe interfejsy funkcyjne w Javie
Java posiada wiele wbudowanych interfejsów funkcyjnych, które mogą być używane z wyrażeniami lambda, np.:

```java
Runnable – metoda run()
Callable<T> – metoda call()
Comparator<T> – metoda compare()
Consumer<T> – metoda accept()
Supplier<T> – metoda get()
Function<T, R> – metoda apply()
```

| Wyrażenie Lambda | Opis |
| --- | --- |
| `element -> System.out.println(element);` | Pojedynczy parametr bez typu może nie wymagać nawiasów. |
| `(element) -> System.out.println(element);` | Nawiasy są opcjonalne. |
| `(String element) -> System.out.println(element);` | Nawiasy są wymagane, jeśli podano typ referencyjny. |
| `(var element) -> System.out.println(element);` | Typ referencyjny może być `var`. |

| Wyrażenie Lambda | Opis |
| --- | --- |
| `(element) -> System.out.println(element);` | Wyrażenie może być pojedynczym poleceniem. |
|  | Podobnie jak w przypadku `switch` z jednym wynikiem, `return` nie jest potrzebne i jego użycie wywołałoby błąd kompilacji. |
| `(var element) -> {` | Wyrażenie może być blokiem kodu. |
| `char first = element.charAt(0);` | Podobnie jak w `switch` z wymaganym `yield`, lambda zwracająca wartość wymagałaby na końcu instrukcji `return`. |
| `System.out.println(element + " means " + first);` | Wszystkie instrukcje w bloku muszą kończyć się średnikami. |
| `};` |  |

## Jak stworzyć włąsny interfejs używający lambda

```java
@FunctionalInterface
public interface Operation<T>{

    T operate(T value1, T value2);

}
```

```java
 * Deklaracja typu ogólnego (<T>): W części <T> po static, typ T jest definiowany jako typ ogólny, co pozwala użyć tego samego typu we wszystkich parametrach i zwracanej wartości tej metody. Dzięki temu calculator może pracować z różnymi typami danych, takimi jak Integer, Double, String, itp., bez konieczności przeciążania metody.
     *
     * Zwracany typ i typy parametrów: Zwracany typ T oraz typy parametrów T value1 i T value2 oznaczają, że wszystkie trzy muszą być tego samego typu. calculator będzie działać poprawnie tylko wtedy, gdy wszystkie parametry przekazane do metody będą tego samego typu, zgodnie z deklaracją typu ogólnego T.
     */

public static <T> T calculator(Operation<T> function, T value1, T value2) {
    T result = function.operate(value1, value2);
    System.out.println("Result of operation: " + result);
    return result;
}

int result = calculator((a, b) -> a + b, 5, 6);
var result2 = calculator((a, b) -> { return  a + b; } , 5, 6);

var rezultat = calculator((a, b) -> a + b, "Hello ", "World");
```

Deklaracja typu ogólnego () w metodzie jest konieczna, ponieważ to ona informuje kompilator, że T jest typem parametryzowanym (czyli generics). Bez tej deklaracji kompilator nie wie, czym jest T i zgłosi błąd.
W powyższym kodzie kompilator nie wie, czym jest T, ponieważ brakuje deklaracji  przed zwracanym typem. W efekcie kompilator nie ma punktu odniesienia i nie może dopasować T do konkretnego typu w trakcie wywołania metody.
Deklaracja typu ogólnego () jest wymagana, aby jednoznacznie wskazać, że metoda ma działać z typem ogólnym, a T nie jest zwykłą klasą czy typem. Gdybyśmy pominęli , kompilator potraktowałby T jako nieznany symbol lub typ, a nie jako placeholder, który zostanie zastąpiony przez rzeczywisty typ w czasie kompilacji.

| Wyrażenie lambda | Opis |
| --- | --- |
| (a, b) -> a + b; | Nawiasy są zawsze wymagane. Jawne typy nie są wymagane. |
| (Integer a, Integer b) -> a + b; | Jeśli używasz jawnego typu dla jednego parametru, musisz użyć jawnych typów dla wszystkich. |
| (var a, var b) -> a + b; | Jeśli używasz `var` dla jednego parametru, musisz użyć `var` dla wszystkich parametrów. |

| Opis wyrażenia lambda | Opis wyrażenia lambda po polsku |
| --- | --- |
| `(a, b) -> a + b` | Gdy nie używasz klamr `{}`, słowo kluczowe `return` jest niepotrzebne i spowoduje błąd kompilacji. |
| `(a, b) -> { var c = a + b; return c; }` | Jeśli używasz bloku instrukcji (czyli klamr `{}`), `return` jest wymagane. |

| Interfejs | Podstawowa sygnatura metody | Cel |
| --- | --- | --- |
| `Predicate<T>` | `boolean test(T t)` | Umożliwia testowanie obiektu pod kątem warunku (true/false). |
| `Function<T, R>` | `R apply(T t)` | Przekształca jeden obiekt w inny, zwracając wynik typu `R`. |
| `Consumer<T>` | `void accept(T t)` | Wykonuje akcję na obiekcie typu `T`, nie zwraca wartości. |
| `Supplier<T>` | `T get()` | Dostarcza obiekt typu `T`, nie przyjmuje żadnych argumentów. |
| `BiConsumer<T, U>` | `void accept(T t, U u)` | Wykonuje akcję na dwóch obiektach typu `T` i `U`, nie zwraca wartości. |

# BiConsumer

Interfejs Funkcji

`BinaryOperator` jest interfejsem `BiFunction`, gdzie oba argumenty mają ten sam typ, podobnie jak wynik, dlatego wynik jest pokazany jako `T`, a nie `R`.

To przypomina nam, że wynik będzie miał ten sam typ co argumenty metod.

Uwzględniłem również parametry typu z każdą nazwą interfejsu na tym slajdzie, ponieważ chciałem, abyś zobaczył, że wynik dla `Function` lub `BiFunction` jest deklarowany jako ostatni argument typu.

Dla `UnaryOperator` i `BinaryOperator` jest deklarowany tylko jeden argument typu, ponieważ typy argumentów i wyników będą takie same.

| Nazwa Interfejsu | Sygnatura Metody | Nazwa Interfejsu | Sygnatura Metody |
| --- | --- | --- | --- |
| `Function<T,R>` | `R apply(T t)` | `UnaryOperator<T>` | `T apply(T t)` |
| `BiFunction<T,U,R>` | `R apply(T t, U u)` | `BinaryOperator<T>` | `T apply(T t1, T t2)` |

## Poprawne Deklaracje Lambda dla Różnej Liczby Argumentów

- Kiedy używasz `var` jako typu, każdy argument musi używać `var`.
- Kiedy określasz jawne typy, każdy argument musi zawierać określony typ.

| Liczba Argumentów w Metodzie Funkcyjnej | Poprawna składnia lambda |
| --- | --- |
| Brak | `() -> instrukcja` |
| Jeden | `s -> instrukcja` |
|  | `(s) -> instrukcja` |
|  | `(var s) -> instrukcja` |
|  | `(String s) -> instrukcja` |
| Dwa | `(s, t) -> instrukcja` |
|  | `(var s, var t) -> instrukcja` |
|  | `(String s, List t) -> instrukcja` |

### Uwagi

- Kiedy używasz `var`, **wszystkie** argumenty muszą używać `var`.
- Kiedy określasz jawne typy, **wszystkie** argumenty muszą zawierać jawne typy.
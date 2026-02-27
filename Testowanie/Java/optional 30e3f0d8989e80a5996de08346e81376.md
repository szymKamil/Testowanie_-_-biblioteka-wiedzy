# optional

# Optional

Optional to klasa wprowadzona w JDK 8 jako część pakietu java.util. Jest używana do reprezentowania wartości, która może być obecna lub nie, zamiast używania null, co zmniejsza ryzyko wystąpienia błędów NullPointerException.

Kluczowe cechy Optional:
Uniknięcie null: Zamiast przekazywać null, można zwrócić obiekt Optional reprezentujący obecność lub brak wartości.
Immutability: Obiekty Optional są niemodyfikowalne.
Zgodność z programowaniem funkcyjnym: Metody pozwalają na przetwarzanie wartości w stylu funkcyjnym.

| Metoda | Opis |
| --- | --- |
| `empty()` | Tworzy pusty obiekt `Optional`. |
| `of(value)` | Tworzy `Optional` z wartością, która nie może być `null`. |
| `ofNullable(value)` | Tworzy `Optional` z wartością, która może być `null`. |
| `isPresent()` | Sprawdza, czy wartość jest obecna. |
| `ifPresent(Consumer)` | Wykonuje akcję, jeśli wartość jest obecna. |
| `orElse(value)` | Zwraca wartość lub podaną domyślną wartość, jeśli brak wartości. |
| `orElseGet(Supplier)` | Zwraca wartość lub wynik funkcji dostarczającej domyślną wartość. |
| `orElseThrow(Supplier<Exception>)` | Zwraca wartość lub rzuca wyjątek, jeśli wartość nie jest obecna. |
| `map(Function)` | Przekształca wartość, jeśli jest obecna. |
| `flatMap(Function)` | Przekształca wartość, zwracając `Optional`, aby uniknąć zagnieżdżonych obiektów `Optional`. |
| `filter(Predicate)` | Zwraca `Optional`, jeśli wartość spełnia podany warunek. |
- Zawijanie elementów w Optional zużywa więcej pamięci i może spowolnić wykonywanie kodu.
- Zawijanie elementów w Optional zwiększa złożoność i zmniejsza czytelność kodu.
- Optional nie jest serializowalny.
- Używanie Optional dla pól lub parametrów metod nie jest zalecane.

Przykład z Optional w strumieniach

```java
import java.util.Optional;
import java.util.stream.Stream;

public class OptionalInStreams {
    public static void main(String[] args) {
        Stream<Optional<Integer>> stream = Stream.of(
            Optional.of(1),
            Optional.empty(),
            Optional.of(3)
        );

        // Rozwinięcie i filtrowanie wartości obecnych w Optional
        stream.flatMap(opt -> opt.stream()) // Rozwija Optional do wartości w strumieniu
              .filter(i -> i > 1)
              .forEach(System.out::println); // Wynik: 3
    }
}
```

```java
import java.util.Optional;
import java.util.stream.Stream;

public class OptionalExample {
    public static void main(String[] args) {
        Optional<String> result = Stream.of("Java", "Scala", "Kotlin")
                                        .filter(lang -> lang.startsWith("S"))
                                        .findFirst();

        result.ifPresent(System.out::println); // Wynik: Scala
    }
}
```

Optional to narzędzie ułatwiające zarządzanie wartościami potencjalnie nieobecnymi i zmniejszające ryzyko błędów związanych z null. W strumieniach jest szczególnie przydatne w połączeniu z metodami jak flatMap, filter, czy w przypadku operacji wyszukiwania (findFirst, findAny).

Metoda Optional.empty() jest używana do stworzenia pustego obiektu Optional, który reprezentuje brak wartości. Można jej używać w sytuacjach, gdy chcesz jasno określić, że wartość jest nieobecna, zamiast używać null.
Przykłady użycia Optional.empty()
1. Zwracanie pustego Optional z metody
Jeśli metoda nie może zwrócić żadnej wartości, możesz użyć Optional.empty():

```java
import java.util.Optional;

public class Example {
    public static Optional<String> findNameById(int id) {
        if (id == 1) {
            return Optional.of("Alice");
        }
        return Optional.empty(); // Brak wartości
    }

    public static void main(String[] args) {
        Optional<String> name = findNameById(2);
        name.ifPresentOrElse(
                System.out::println,
                () -> System.out.println("Nie znaleziono imienia")
        );
    }
}
```

| Metoda fabryczna | Kiedy używać | Najlepsze praktyki |
| --- | --- | --- |
| `Optional<T> empty()` | Użyj tej metody, aby utworzyć `Optional`, o którym wiesz, że nie ma wartości. | Nigdy nie zwracaj `null` z metody, która ma zwracać `Optional`. |
| `Optional<T> of(T value)` | Użyj tej metody, aby utworzyć `Optional` z wartością, o której wiesz, że istnieje. | Przekazanie `null` do tej metody spowoduje rzucenie `NullPointerException`. Użyj `ofNullable`, jeśli wartość może być `null`. |
| `Optional<T> ofNullable(T value)` | Użyj tej metody, aby utworzyć `Optional`, jeśli nie jesteś pewien, czy wartość jest `null`, czy nie. |  |

Użycie Optional.empty() pozwala wyraźnie wskazać brak wartości, co jest bardziej jednoznaczne niż Optional.ofNullable(null):

```java
Optional<String> emptyOptional = Optional.empty();
Optional<String> nullableOptional = Optional.ofNullable(null);

System.out.println(emptyOptional.isPresent()); // false
System.out.println(nullableOptional.isPresent()); // false
```

Filtrowanie z użyciem Optional.empty()
Jeśli filtr w metodzie filter nie zostanie spełniony, wynikowy obiekt będzie pusty (Optional.empty()):

```java
Optional<String> name = Optional.of("Alice")
                                .filter(n -> n.startsWith("B"));

System.out.println(name.isPresent()); // false
```
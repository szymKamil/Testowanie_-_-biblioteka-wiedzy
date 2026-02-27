# stream

# Stream

W Javie Stream to klasa należąca do pakietu java.util.stream, wprowadzona w Java 8, która umożliwia przetwarzanie danych w sposób deklaratywny, oparty na funkcjonalnym podejściu do programowania.

Stream nie jest strukturą danych — nie przechowuje danych, ale działa jako potok, przez który przepływają elementy kolekcji lub innych źródeł danych, umożliwiając ich przetwarzanie.

W Javie Stream to klasa należąca do pakietu java.util.stream, wprowadzona w Java 8, która umożliwia przetwarzanie danych w sposób deklaratywny, oparty na funkcjonalnym podejściu do programowania.

Stream nie jest strukturą danych — nie przechowuje danych, ale działa jako potok, przez który przepływają elementy kolekcji lub innych źródeł danych, umożliwiając ich przetwarzanie.

Kluczowe cechy Stream:
Deklaratywność: Operacje na Stream są wyrażone za pomocą metody łańcuchowej, co sprawia, że kod staje się czytelniejszy i bardziej zwięzły.

Przetwarzanie leniwe: Operacje pośrednie (np. filter(), map()) są wykonywane tylko wtedy, gdy konieczne jest uzyskanie wyniku (np. collect(), forEach()).

Nie modyfikuje danych źródłowych: Stream działa na danych, ale ich nie zmienia; generuje nowe strumienie lub wyniki.

Możliwość przetwarzania równoległego: Streamy mogą być przetwarzane sekwencyjnie (stream()) lub równolegle (parallelStream()), co zwiększa wydajność przy dużych zbiorach danych.

Tworzenie Stream:
Stream można utworzyć z różnych źródeł, takich jak:

```java
List<String> list = List.of("Java", "Python", "C++");
Stream<String> stream = list.stream();
```

```java
String[] array = {"a", "b", "c"};
Stream<String> stream = Arrays.stream(array);
```

```java
Stream<Integer> stream = Stream.of(1, 2, 3, 4, 5);
```

```java
Stream<Double> randomNumbers = Stream.generate(Math::random).limit(5);
```

W Javie Stream to klasa należąca do pakietu java.util.stream, wprowadzona w Java 8, która umożliwia przetwarzanie danych w sposób deklaratywny, oparty na funkcjonalnym podejściu do programowania.

Stream nie jest strukturą danych — nie przechowuje danych, ale działa jako potok, przez który przepływają elementy kolekcji lub innych źródeł danych, umożliwiając ich przetwarzanie.

Kluczowe cechy Stream:
Deklaratywność: Operacje na Stream są wyrażone za pomocą metody łańcuchowej, co sprawia, że kod staje się czytelniejszy i bardziej zwięzły.

Przetwarzanie leniwe: Operacje pośrednie (np. filter(), map()) są wykonywane tylko wtedy, gdy konieczne jest uzyskanie wyniku końcowego (np. collect(), forEach()).

Nie modyfikuje danych źródłowych: Stream działa na danych, ale ich nie zmienia; generuje nowe strumienie lub wyniki.

Możliwość przetwarzania równoległego: Streamy mogą być przetwarzane sekwencyjnie (stream()) lub równolegle (parallelStream()), co zwiększa wydajność przy dużych zbiorach danych.

Tworzenie Stream:
Stream można utworzyć z różnych źródeł, takich jak:

Kolekcje:

java
Skopiuj kod
List list = List.of(“Java”, “Python”, “C++”);
Stream stream = list.stream();
Tablice:

java
Skopiuj kod
String[] array = {“a”, “b”, “c”};
Stream stream = Arrays.stream(array);
Metoda Stream.of():

java
Skopiuj kod
Stream stream = Stream.of(1, 2, 3, 4, 5);
Generowanie danych:

java
Skopiuj kod
Stream randomNumbers = Stream.generate(Math::random).limit(5);
Operacje na Stream:
Stream składa się z dwóch rodzajów operacji:

1. Operacje pośrednie (intermediate):
Zwracają nowy Stream (leniwe przetwarzanie).
Przykłady:
filter(Predicate
    
    ) – filtruje elementy na podstawie warunku.
    map(Function<T, R>) – przekształca elementy.
    distinct() – usuwa duplikaty.
    sorted() – sortuje elementy. — sorted(Comparator.comparing(Obj::variable).
    thenComparing() - sortuje elementy poprzez 2 kryterium.
    mapToObject() - przekszatłca element na inny obiekt.
    boxed() - zwraca strumień składający się z elementów tego strumienia, każdy zamknięty w polu typu Integer.
    peak() - podgląd elementów - służy do debugowania.
    

```java
List<String> names = List.of("Alice", "Bob", "Charlie");
Stream<String> filteredStream = names.stream()
                                     .filter(name -> name.startsWith("A"));
```

1. Operacje końcowe (terminal):
Kończą przetwarzanie Stream.
Przykłady:
collect(Collector) – przekształca Stream w kolekcję.
forEach(Consumer) – wykonuje operację dla każdego elementu.
reduce(BinaryOperator) – redukuje dane do jednego wyniku.
count() – liczy elementy w Stream.

```java
List<String> names = List.of("Alice", "Bob", "Charlie");
long count = names.stream()
                  .filter(name -> name.length() > 3)
                  .count(); // Zwraca 2
```

### Collect

```java
public class StreamCollectExample {
    public static void main(String[] args) {
        List<User> users = Arrays.asList(
            new User("Anna", 25),
            new User("Marek", 30),
            new User("Julia", 25),
            new User("Piotr", 35)
        );

        // Grupowanie użytkowników według wieku
        Map<Integer, List<User>> usersByAge = users.stream()
            .collect(Collectors.groupingBy(user -> user.age));

        // Wyświetlenie wyników
        usersByAge.forEach((age, userList) ->
            System.out.println("Wiek: " + age + ", Użytkownicy: " + userList)
        );
    }
```

| Kolektor | Opis |
| --- | --- |
| `toList` | Zbiera elementy strumienia do listy (`List`). |
| `toSet` | Zbiera elementy strumienia do zbioru (`Set`). |
| `toMap` | Zbiera elementy strumienia do mapy (`Map`). |
| `joining` | Łączy elementy strumienia w jeden ciąg znaków. |
| `groupingBy` | Grupuje elementy według klucza. |
| `partitioningBy` | Dzieli elementy na dwie grupy (`true/false`) na podstawie predykatu. |
| `counting` | Liczy elementy w strumieniu. |
| `summarizingInt` | Oblicza podsumowanie statystyczne dla wartości `int`. |
| `summarizingDouble` | Oblicza podsumowanie statystyczne dla wartości `double`. |
| `summarizingLong` | Oblicza podsumowanie statystyczne dla wartości `long`. |
| `reducing` | Umożliwia dostosowane agregowanie elementów. |
| `mapping` | Mapuje elementy przed ich zebraniem do innej struktury. |
| `collectingAndThen` | Przekształca wynik kolektora po zakończeniu zbierania. |
| `maxBy` | Znajduje maksymalny element według komparatora. |
| `minBy` | Znajduje minimalny element według komparatora. |
| `filtering` | Filtruje elementy przed ich zebraniem. |
| `flatMapping` | Spłaszcza i mapuje elementy przed ich zebraniem. |

## Zastosowania Stream:

Filtrowanie danych (filter)
Transformacja danych (map)
Agregacja i redukcja (reduce, collect)
Sortowanie (sorted)
Usuwanie duplikatów (distinct)

# Operacje Terminalne

Niektóre służą do przekształcania danych strumieniowych w kolekcje lub inny typ referencyjny.

Inne agregują informacje, liczą elementy, znajdują minimalną lub maksymalną wartość i nie przyjmują argumentów.

| Dopasowywanie i Szukanie | Transformacje i Redukcje Typu | Statystyczne (Numeryczne) Redukcje | Przetwarzanie |
| --- | --- | --- | --- |
| allMatch | collect | average2 | forEach |
| anyMatch | reduce | count | forEachOrdered |
| findAny1 | toArray | max1 |  |
| findFirst1 | toList | min1 |  |
| noneMatch |  | sum2 |  |
|  |  | summaryStatistics2 |  |

1 Zwraca instancję typu `Optional`.

2 Dostępne dla `DoubleStream`, `IntStream`, `LongStream`.

Collector w collect:

```java
Collectors.toSet()
Collectors.toList()
Collectors.toMap()
Collectors.toCollection()
```

```java
import java.util.Comparator;
import java.util.TreeSet;

// Niestandardowy kolektor
Set<String> customSet = stream.collect(Collector.of(
        HashSet::new,             // dostawca nowej kolekcji
        Set::add,                 // akumulator
        (set1, set2) -> {         // łącznik (dla równoległych strumieni)
           set1.addAll(set2);
           return set1;
        }));

        // lub
        TreeSet::new,TreeSet::add,TreeSet::addAll;
        // z komparatorem
        ()->new TreeSet<>(Comparator.comparing(Object obj1::var)),TreeSet::add,TreeSet::addAll;
```

## Stream a lambda:

| **Cecha** | **Stream** | **Lambda** |
| --- | --- | --- |
| **Definicja** | Abstrakcja przetwarzania danych, pozwalająca na operacje takie jak filtrowanie, mapowanie i redukcja. | Wyrażenie funkcyjne, czyli sposób definiowania funkcji anonimowych. |
| **Cel** | Uproszczenie operacji na danych w sposób deklaratywny i wydajny. | Definiowanie logiki przetwarzania, np. w operacjach funkcyjnych lub w konstrukcjach API. |
| **Zastosowanie** | Operacje na danych pochodzących z kolekcji, tablic, strumieni wejściowych itp. | Używane wszędzie tam, gdzie wymagany jest interfejs funkcyjny (np. `Predicate`, `Runnable`). |
| **Leniwość** | Operacje pośrednie w Stream (np. `filter()`, `map()`) są wykonywane leniwie, dopiero przy operacji końcowej. | Lambda jest wykonywana natychmiast w swoim kontekście. |
| **Przykład użycia** | `List.of(1, 2, 3).stream().filter(x -> x > 1).toList();` | `Runnable task = () -> System.out.println("Hello!"); task.run();` |
| **Powiązanie** | Streamy korzystają z lambd do definiowania logiki operacji (np. w `filter()` lub `map()`). | Lambdy mogą być używane zarówno w Streamach, jak i poza nimi, np. do obsługi zdarzeń w GUI. |
| **Poziom abstrakcji** | Wyższy poziom — operacje na danych są traktowane jako przetwarzanie “przepływu” elementów. | Niższy poziom — Lambda jest podstawową konstrukcją do definiowania funkcji anonimowych. |
| **Funkcja w Javie** | Kluczowa część `java.util.stream` wprowadzona w Java 8, dedykowana do pracy z danymi. | Wprowadzone w Java 8 jako element umożliwiający programowanie funkcyjne. |
| **Czy może działać niezależnie?** | Nie, wymaga źródła danych, np. kolekcji, oraz często lambdy jako argumentu dla operacji. | Tak, może być używana w dowolnym kontekście, gdzie oczekiwany jest interfejs funkcyjny. |
| **Podstawowe operacje** | Filtrowanie (`filter`), mapowanie (`map`), redukcja (`reduce`), kolekcjonowanie (`collect`). | Definiowanie logiki dla interfejsów funkcyjnych, np. `Function<T, R>` czy `Consumer<T>`. |

## Pipeline (ciąg operacji)

Stream składa się z potoku operacji, które można podzielić na dwa rodzaje:

1. Operacje pośrednie (intermediate)
Są to operacje, które przekształcają lub filtrują strumień danych, zwracając nowy Stream. Operacje te są “leniwe”, tzn. wykonują się dopiero w momencie zastosowania operacji terminalnej.

Przykłady:
filter(Predicate) - filtruje elementy na podstawie predykatu.
map(Function<T, R>) - przekształca elementy strumienia.
flatMap(Function<T, Stream>) - spłaszcza zagnieżdżone struktury.
sorted(Comparator) - sortuje dane.
distinct() - usuwa duplikaty.
limit(long n) - ogranicza liczbę elementów.
skip(long n) - pomija określoną liczbę elementów.

1. Operacje terminalne (terminal)
Kończą przetwarzanie strumienia i zwracają wynik. Operacje te uruchamiają całą sekwencję operacji.
Gdy użyta zostanie operacja terminalna, nie da się odwołać ponownie do tego pipieline, nawet gdy został dodany do zmiennej.

Przykłady:
Redukcja: reduce(BinaryOperator), collect(Collectors.toList()).
Iteracja: forEach(Consumer).
Agregacja: count(), min(Comparator), max(Comparator).
Sprawdzenie: anyMatch(Predicate), allMatch(Predicate), noneMatch(Predicate).

Stateless vs Stateful
Stateless: Operacje, które nie przechowują żadnego stanu między kolejnymi elementami (np. filter, map).
Stateful: Operacje, które muszą przechować stan, aby móc zwrócić wyniki (np. distinct, sorted, limit).

Lazy Evaluation
Streamy są obliczane “leniwe”. Oznacza to, że operacje pośrednie nie są wykonywane od razu, lecz są zapisywane jako opis operacji do wykonania, który uruchamia się dopiero przy operacji terminalnej.

```java
List<String> names = Arrays.asList("Anna", "Bartek", "Adam", "Beata");

// Przetwarzanie strumieniowe
List<String> filteredNames = names.stream()
    .filter(name -> name.startsWith("A"))  // Operacja pośrednia
    .sorted()                              // Operacja pośrednia
    .collect(Collectors.toList());         // Operacja terminalna

System.out.println(filteredNames); // [Adam, Anna]
```

# FlatMap

Metoda flatMap() w Java Stream API służy do przekształcania strumienia elementów w strumień wielu elementów, a następnie ich “spłaszczenia”. Oznacza to, że zamiast pracy z listą list, strumień pozwala na uzyskanie pojedynczego strumienia z elementów znajdujących się wewnątrz tych list.

Jak działa flatMap()?
Wejście: Każdy element z oryginalnego strumienia jest przekształcany w nowy strumień (np. listę, tablicę lub inny strumień).
Spłaszczenie: flatMap() scala te nowe strumienie w jeden ciągły strumień elementów.

Różnica między map() a flatMap()
map(): Przekształca elementy strumienia jeden do jednego, czyli zwraca strumień strumieni, np. Stream<Stream>.
flatMap(): Przekształca elementy i “spłaszcza” wynik, zwracając jeden strumień, np. Stream.

```java
import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

public class FlatMapExample {
    public static void main(String[] args) {
        List<List<String>> nestedList = Arrays.asList(
            Arrays.asList("a", "b", "c"),
            Arrays.asList("d", "e"),
            Arrays.asList("f", "g", "h")
        );

        List<String> flattenedList = nestedList.stream()
            .flatMap(List::stream) // Spłaszczenie strumienia
            .collect(Collectors.toList());

        System.out.println(flattenedList); // [a, b, c, d, e, f, g, h]
    }
}
```
# interface

# Interface

W Javie interfejs (ang. interface) to rodzaj abstrakcyjnego typu, który pozwala na definiowanie zbioru metod bez ich implementacji. Klasa, która implementuje dany interfejs, zobowiązuje się do dostarczenia implementacji wszystkich metod zadeklarowanych w interfejsie. Interfejsy są często używane do osiągania wielodziedziczenia oraz do definiowania zachowań, które mają być wspólne dla różnych klas.
Kluczowe cechy interfejsów w Javie:
- Abstrakcyjność: Metody w interfejsie domyślnie nie mają implementacji (choć od Javy 8 można używać metod z domyślną implementacją - default methods).
- Zbiór metod: Interfejs określa, jakie metody klasa musi zaimplementować, aby “spełniać” ten interfejs.
- Stałe: Interfejs może zawierać stałe (public static final), które są dostępne dla każdej klasy implementującej ten interfejs.
- Wielodziedziczenie: Klasa może implementować wiele interfejsów (w przeciwieństwie do dziedziczenia klas), co pozwala na definiowanie różnych zachowań niezależnie od struktury dziedziczenia.

```java
public interface Drawable {
    void draw(); // deklaracja metody bez implementacji
}
```

Jesli interface nie ma modyfikatorów dostępu, domyślnie posiada modyfikator implicity public.
Tylko konkretne (z blokiem kodu) metody mogą mieć prywatny (private) dostęp.

Dana klasa może przyjmować wiele interfejsów.
Interfejsy mogą być rozszerzane przez inne (wiele) interfejsów.

# Interfejsy w Javie

## Podstawy Deklarowania Interfejsów

- **Deklaracja interfejsu**:
    - Podobna do deklaracji klasy, ale zamiast słowa kluczowego `class` używa się `interface`.
    - Przykład deklaracji:
    `java public interface FlightEnabled {}`
- **Nazewnictwo**:
    - Nazwa interfejsu zazwyczaj odnosi się do zestawu zachowań, które opisuje.
    - Wiele interfejsów kończy się na ‘able’, jak `Comparable` i `Iterable`, co wskazuje, że obiekt jest zdolny do wykonywania określonych działań.

## Implementacja Interfejsów

- **Associacja klasy z interfejsem**:
    - Aby klasa implementowała interfejs, używa się klauzuli `implements`.
    - Przykład:
    `java public class Bird implements FlightEnabled {}`
- **Tworzenie obiektu i przypisanie do zmiennej typu interfejsu**:
    - Można stworzyć nowy obiekt klasy implementującej interfejs i przypisać go do zmiennej interfejsu.
    - Przykład kodu:
    `java FlightEnabled flier = new Bird();`

## Tworzenie obiektu i przypisanie do zmiennej typu interfejsu

Przypisanie obiektu klasy implementującej interfejs do zmiennej typu tego interfejsu pozwala na tworzenie bardziej elastycznego i modularnego kodu. Dzięki temu możemy operować na obiekcie przy użyciu ogólnego typu interfejsu, bez względu na konkretną klasę, która go implementuje.

### Przykład

Załóżmy, że mamy interfejs `FlightEnabled` oraz klasę `Bird`, która ten interfejs implementuje:

```java
public interface FlightEnabled {
    void fly();
}

public class Bird implements FlightEnabled {
    @Override
    public void fly() {
        System.out.println("Ptak leci");
    }
}
```

Jeżeli utworzymy obiekt klasy Bird i przypiszemy go do zmiennej typu FlightEnabled:

```java
FlightEnabled flier = new Bird();
```

Oznacza to, że teraz możemy używać flier, aby wywołać dowolną metodę zdefiniowaną w FlightEnabled, np.:

```java
flier.fly(); // Wynik: "Ptak leci"
```

Zastosowania
Elastyczność i Polimorfizm: Możemy przypisać obiekt dowolnej klasy implementującej FlightEnabled do zmiennej typu FlightEnabled. Na przykład, jeśli mamy inną klasę Airplane, która również implementuje FlightEnabled, możemy używać tej samej zmiennej do przechowywania obiektów Bird i Airplane:

```java
FlightEnabled flier = new Bird();
flier.fly(); // "Ptak leci"

flier = new Airplane();
flier.fly(); // "Samolot leci"
```

Interfejs jako typ ogólny: Używanie zmiennej typu interfejsu pozwala na manipulowanie obiektami różnych klas w jednolity sposób, co jest szczególnie przydatne przy tworzeniu list lub kolekcji obiektów implementujących wspólny interfejs.
Dekouplowanie kodu: Kod operujący na obiektach typu interfejsu nie musi znać szczegółów implementacyjnych klasy. To pozwala na łatwiejszą wymianę implementacji lub rozszerzenie kodu w przyszłości, bez konieczności zmieniania zależnych klas.

- **Rozszerzanie klasy i implementacja interfejsów**:
    - Klasa może jednocześnie dziedziczyć po innej klasie oraz implementować jeden lub więcej interfejsów.
    - Przykład:
        
        ```java
        package dev.lpa;
        
        public class Bird extends Animal implements FlightEnabled, Trackable {}
        ```
        

## Elastyczność i Modularność

- Klasa może implementować wiele interfejsów, co zwiększa elastyczność i modularność kodu.
- Przykład:
    - Klasa `Bird` dziedziczy po klasie `Animal` oraz implementuje interfejsy `FlightEnabled` i `Trackable`.

## Dodatkowe informacje o interfejsach

1. **Domyślne metody (default methods)**:
    - Od Javy 8 interfejsy mogą zawierać metody z domyślną implementacją, które są oznaczone słowem kluczowym `default`.
    - Dzięki temu można dodawać nowe metody do interfejsu bez konieczności modyfikowania wszystkich klas, które go implementują.
    - Przykład:
        
        ```java
        public interface FlightEnabled {
            default void fly() {
                System.out.println("Lecę!");
            }
        }
        ```
        
2. **Metody statyczne**:
    - Interfejsy mogą także zawierać metody statyczne, które są wywoływane bezpośrednio na interfejsie.
    - Przykład:
        
        ```java
        public interface FlightEnabled {
            static void checkFlightStatus() {
                System.out.println("Sprawdzanie statusu lotu.");
            }
        }
        ```
        
3. **Interfejsy funkcyjne**:
    - Interfejsy z dokładnie jedną metodą abstrakcyjną (np. `Runnable`) nazywają się interfejsami funkcyjnymi.
    - Używa się ich często z wyrażeniami lambda i metodami referencyjnymi, co jest przydatne w programowaniu funkcyjnym.
    - Można oznaczyć je adnotacją `@FunctionalInterface` dla czytelności kodu.
4. **Dziedziczenie interfejsów**:
    - Jeden interfejs może rozszerzać inny interfejs, co pozwala na tworzenie bardziej złożonych hierarchii interfejsów.
    - Przykład:
        
        ```java
        public interface Flyable extends FlightEnabled {}
        ```
        
5. **Różnice między klasami abstrakcyjnymi a interfejsami**:
    - Kiedy klasa jest tylko częściowo abstrakcyjna i ma wspólne zachowania dla innych klas, bardziej odpowiednia jest klasa abstrakcyjna.
    - Gdy potrzeba tylko zdefiniować kontrakt (zbiór metod do zaimplementowania), lepszym wyborem jest interfejs.

## Default

Słowo kluczowe default przy metodzie interfejsu w Javie pozwala na dostarczenie domyślnej implementacji tej metody. Wprowadzone w Javie 8, default umożliwia dodanie nowej funkcjonalności do interfejsów bez konieczności zmieniania wszystkich klas, które już implementują ten interfejs. Przed Javą 8 wszystkie metody w interfejsach musiały być abstrakcyjne (czyli bez implementacji).

Dlaczego używać default w interfejsach?
Zachowanie zgodności wstecznej:
Gdy chcemy dodać nową metodę do istniejącego interfejsu, użycie default pozwala uniknąć konieczności implementacji tej metody we wszystkich klasach, które już implementują interfejs.
Rozszerzanie interfejsów:
Można wzbogacać istniejące interfejsy o nowe metody, które mają domyślne zachowanie, pozostawiając klasom implementującym interfejs możliwość ich nadpisania.

```java
public interface FlightEnabled {
    void fly();

    // Metoda z domyślną implementacją
    default void land() {
        System.out.println("Lądowanie...");
    }
}

public class Bird implements FlightEnabled {
    @Override
    public void fly() {
        System.out.println("Ptak leci");
    }
}
```

W powyższym przykładzie klasa Bird implementuje interfejs FlightEnabled, ale dostarcza tylko implementację metody fly. Metoda land, mająca domyślną implementację w interfejsie, jest dostępna dla wszystkich obiektów typu Bird, nawet jeśli nie została jawnie zaimplementowana w tej klasie.

```java
FlightEnabled bird = new Bird();
bird.fly();  // Wynik: "Ptak leci"
bird.land(); // Wynik: "Lądowanie..."
```

Metody oznaczone jako default mogą, ale nie muszą być nadpisywane w klasach implementujących interfejs.

Podobnie jak w przypadku nadpisywania metody na klasie, masz trzy możliwości, gdy nadpisujesz domyślną metodę na interfejsie.
- Możesz nie nadpisywać jej wcale.
- Możesz nadpisać metodę i napisać dla niej kod, tak aby metoda interfejsu nie była wykonywana.
- Możesz też napisać własny kod i wywołać metodę interfejsu jako część swojej implementacji.

## Metoty statyczne
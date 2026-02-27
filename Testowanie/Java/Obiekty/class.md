# class

# Class (klasa)

Klasa może stworzyć grupę obiektów, które mają wspólne właściwości. Jest to szablon lub plan, na podstawie którego tworzone są obiekty. Jest to byt logiczny. Nie może być fizyczna.

W Javie klasa nie jest fizycznym bytem (w przeciwieństwie do obiektu) – służy raczej jako schemat lub logiczna struktura, na której bazują obiekty.

Klasa w Javie może zawierać:
- Pola
- Metody
- Konstruktory
- Bloki
- Zagnieżdżone klasy i interfejsy

Deklaracja klasy:

```java
    class <class_name>{
        field;
        method;
    }
```

### Zmienna instancji:

Zmienna, która jest tworzona wewnątrz klasy, ale poza metodą, jest znana jako zmienna instancji. Zmienna instancji nie otrzymuje pamięci w czasie kompilacji. Otrzymuje pamięć w czasie wykonywania, gdy tworzony jest obiekt lub instancja. Dlatego jest znana jako zmienna instancji.
### Metoda w języku Java
W języku Java metoda jest jak funkcja, która służy do prezentacji zachowania obiektu.
Zalety metod:
- Możliwość ponownego użycia kodu
- Optymalizacja kodu

### Słowo kluczowe new w języku Java

Słowo kluczowe new służy do alokacji pamięci w czasie wykonywania. Wszystkie obiekty otrzymują pamięć w obszarze pamięci Heap.

## Casting klas

Casting klas w Javie to proces przekształcania jednej referencji obiektowej na inną, gdy obiekt jest zgodny z typem docelowym. W Javie można wykonywać dwa rodzaje castingu dla klas: upcasting i downcasting.

1. Upcasting (rzutowanie w górę)
Upcasting to rzutowanie obiektu klasy pochodnej na typ klasy bazowej.
Jest bezpieczne i automatyczne, ponieważ obiekt klasy pochodnej zawsze jest zgodny z typem klasy bazowej.
Używa się go często, aby ograniczyć dostęp do specyficznych metod klasy pochodnej i stosować obiekt jako typ bardziej ogólny (np. interfejs lub klasa bazowa).

```java
class Zwierze {
    void dzwiek() {
        System.out.println("Zwierzę wydaje dźwięk");
    }
}

class Pies extends Zwierze {
    void szczekaj() {
        System.out.println("Pies szczeka");
    }
}

public class Main {
    public static void main(String[] args) {
        Zwierze zwierze = new Pies(); // Upcasting
        zwierze.dzwiek(); // wywołuje metodę z klasy Zwierze
    }
}
```

1. Downcasting (rzutowanie w dół)
Downcasting to rzutowanie obiektu klasy bazowej na typ klasy pochodnej.
Jest ryzykowne i musi być jawne, ponieważ może zakończyć się błędem w czasie wykonywania, jeśli obiekt nie jest rzeczywiście instancją klasy pochodnej.
Stosuje się go, gdy jesteśmy pewni, że obiekt referencyjny wskazuje na rzeczywistą instancję klasy pochodnej i chcemy uzyskać dostęp do jej specyficznych metod lub pól.

```java
public class Main {
    public static void main(String[] args) {
        Zwierze zwierze = new Pies(); // Upcasting

        if (zwierze instanceof Pies) { // Sprawdzenie bezpieczeństwa przed downcastingiem
            Pies pies = (Pies) zwierze; // Downcasting
            pies.szczekaj(); // Teraz można wywołać metodę specyficzną dla Pies
        }
    }
}
```

# Zasady tworzenia klas tworzących niemutowalne (immutable) obiekty

| **Zasada** | **Opis** | **Przykład** |
| --- | --- | --- |
| **1. Oznacz klasę jako `final`.** | Klasa `final` uniemożliwia dziedziczenie, dzięki czemu nie można zmienić zachowania klasy w podklasach. | `final class ImmutableClass { ... }` |
| **2. Oznacz wszystkie pola jako `private` i `final`.** | Pola powinny być niewidoczne dla innych klas i mieć stałe wartości po przypisaniu. | `private final String name;` |
| **3. Nie udostępniaj setterów.** | Nie twórz metod, które pozwalałyby modyfikować wartości pól po utworzeniu obiektu. | `public void setName(String name) { ... } // Nie tworzymy takiej metody!` |
| **4. Zainicjuj wszystkie pola w konstruktorze.** | Wszystkie pola klasy muszą być ustawiane wyłącznie w konstruktorze i nigdy później. | `public ImmutableClass(String name, int age) { this.name = name; this.age = age; }` |
| **5. Unikaj zwracania referencji do obiektów mutowalnych.** | Jeśli klasa posiada pola, które są obiektami mutowalnymi, udostępniaj tylko ich kopie (ang. *defensive copy*). | `public Date getDate() { return new Date(this.date.getTime()); }` |
| **6. Zadbaj o brak metod zmieniających stan obiektu.** | Nie twórz metod, które mogłyby zmieniać wartości pól lub działać na nich w sposób destrukcyjny. | `// Metody zmieniające stan obiektu są niedozwolone.` |

Bezpieczeństwo wątków: Immutable obiekty są naturalnie bezpieczne w środowisku wielowątkowym, ponieważ ich stan nie może się zmieniać.
Łatwość użytkowania: Brak nieoczekiwanych modyfikacji obiektów upraszcza ich użycie w aplikacjach.
Przykłady wbudowane w Javę: Klasy takie jak String, Integer czy LocalDate są immutable i stosują te zasady.

Strategie tworzenia klasy, która podczas użycia produkuje obiekty niemutowalne:

Ustaw pola instancji jako prywatne i oznacz je jako final.
Nie definiuj żadnych metod typu setter.
Twórz kopie obronne (defensive copies) w getterach:

```java
  public List<BankAccount> getAccounts() {
        return new ArrayList<>(accounts);
    }
```

Użyj konstruktora lub metody fabrycznej do ustawienia danych, tworząc kopie mutowalnych danych referencyjnych.
Oznacz klasę jako final lub wszystkie jej konstruktory jako prywatne.

# Private constructor

Tworząc w klasach zmienne (values) oznaczone jako final, trzeba je od razu zainicjować. Nie można nadać im wartości poprzez instancjonowanie.
do zmiennych możemy przypisywać użycie metod generujących dane, np.: `private final String name = getName()`

## Instance Initializer w Javie

Instance Initializer to blok kodu w klasie, który jest wykonywany podczas inicjalizacji obiektu klasy, zanim zakończy się konstruktor. Jest to blok kodu umieszczony bezpośrednio w ciele klasy, nie w metodzie ani konstruktorze.

```java
class MyClass {
    // Instance initializer block
    {
        System.out.println("Instance initializer is called");
    }

    // Konstruktor
    MyClass() {
        System.out.println("Constructor is called");
    }

    public static void main(String[] args) {
        new MyClass(); // Wywołuje zarówno blok inicjalizujący, jak i konstruktor
    }
}
```

Instance Initializer w Javie
Instance Initializer to blok kodu w klasie, który jest wykonywany podczas inicjalizacji obiektu klasy, zanim zakończy się konstruktor. Jest to blok kodu umieszczony bezpośrednio w ciele klasy, nie w metodzie ani konstruktorze.
W bloku kodu możemy budować złożone metody.
Składnia

```java
Skopiuj kod
class MyClass {
// Instance initializer block
{
System.out.println("Instance initializer is called");
}

    // Konstruktor
    MyClass() {
        System.out.println("Constructor is called");
    }

    public static void main(String[] args) {
        new MyClass(); // Wywołuje zarówno blok inicjalizujący, jak i konstruktor
    }
}
```

| Cecha | Instance Initializer | Konstruktor |
| --- | --- | --- |
| Położenie w kodzie | W ciele klasy, poza metodami i konstruktorami | W ciele klasy, jako specjalna metoda |
| Wywoływanie | Wywoływany automatycznie przed konstruktorem | Wywoływany jawnie lub automatycznie |
| Przeznaczenie | Inicjalizacja wspólnego kodu dla wszystkich konstruktorów | Specyficzne działanie dla danej wersji konstruktora |

Jak to działa?
Kiedy jest wywoływany?
Blok inicjalizacyjny jest wykonywany za każdym razem, gdy tworzony jest obiekt klasy, przed wykonaniem ciała konstruktora.
Dlaczego używamy?
Służy do inicjalizacji współdzielonego kodu, który musi być wykonany przed wykonaniem konstruktora.
Można używać go do inicjalizacji pól instancji, zwłaszcza gdy ich inicjalizacja jest bardziej skomplikowana.

## Static Initializer w Javie

Static Initializer, znany także jako static block, to blok kodu używany do inicjalizacji statycznych pól lub wykonania kodu, który powinien być uruchomiony tylko raz, w momencie ładowania klasy do pamięci.

```java
class MyClass {
    // Static initializer
    static {
        System.out.println("Static initializer is executed");
    }
    //Instance initializer
   {
      System.out.println("Instance initializer is called");
   }

    public static void main(String[] args) {
        System.out.println("Main method is executed");
    }
}
```

Jak działa Static Initializer?
Kiedy jest wykonywany?

Blok statyczny jest wykonywany raz dla danej klasy, w momencie ładowania klasy przez JVM.
Wywoływany zanim zostanie wykonana jakakolwiek metoda statyczna lub utworzony jakikolwiek obiekt tej klasy.
Dlaczego używamy?

Służy do inicjalizacji statycznych pól lub konfiguracji, która dotyczy całej klasy, a nie konkretnego obiektu.
Przydatny np. w ustawianiu początkowych wartości dla pól statycznych lub wczytywaniu danych konfiguracyjnych.
Jeśli w klasie zdefiniujesz więcej niż jeden static initializer, zostaną one wykonane w kolejności ich występowania w kodzie.

| Cecha | Instance Initializer | Static Initializer |
| --- | --- | --- |
| Wykonanie | Przy każdej inicjalizacji obiektu | Tylko raz, przy ładowaniu klasy |
| Modyfikator | Nie używa static | Musi być oznaczone static |
| Powiązanie | Związane z obiektem | Związane z klasą |
| Przeznaczenie | Inicjalizacja pól instancji | Inicjalizacja pól statycznych |

Wykonanie Przy każdej inicjalizacji obiektu Tylko raz, przy ładowaniu klasy
Powiązanie Związane z obiektem Związane z klasą
Modyfikator Nie używa static Musi być oznaczone static
Przeznaczenie Inicjalizacja pól instancji Inicjalizacja pól statycznych

# Final Class

Użycie final w klasie oznacza, że nie może być ona rozszerzona. Żadna subklasa nie może się do niej odwoływać. Enums i Records są klasami finalnymi.

W Javie, klasa oznaczona jako final ma specyficzną właściwość: nie można jej rozszerzyć (czyli nie można stworzyć z niej klasy dziedziczącej). Oznacza to, że struktura i zachowanie takiej klasy są ostateczne i nie mogą być zmienione przez dziedziczenie.

Dlaczego warto używać final w klasach?
Zapewnienie bezpieczeństwa: Gdy nie chcesz, aby ktoś zmodyfikował zachowanie Twojej klasy poprzez dziedziczenie.
Optymalizacja wydajności: Kompilator i JVM mogą potencjalnie dokonywać optymalizacji kodu, ponieważ wiedzą, że klasa nie będzie modyfikowana.
Bezpieczeństwo w bibliotekach: Często w bibliotekach klasy są oznaczane jako final, aby użytkownicy nie mogli ich zmieniać i potencjalnie wprowadzać błędów.

| **Operations** | **final class** | **abstract class** | **private constructors only** | **protected constructors only** |
| --- | --- | --- | --- | --- |
| **Instantiate a new instance** | **yes**: Można tworzyć nowe obiekty klasy `final`. | **no**: Klasy abstrakcyjne nie mogą być instancjonowane, ponieważ są przeznaczone do dziedziczenia. | **no**: Prywatny konstruktor uniemożliwia instancjonowanie z zewnątrz (np. wzorce Singleton). | **yes, but only subclasses, and classes in same package**: Konstruktor ogranicza dostęp. |
| **A subclass can be declared successfully** | **no**: Klasa `final` nie może być dziedziczona – zapewnia to bezpieczeństwo i spójność implementacji. | **yes**: Klasy abstrakcyjne muszą być dziedziczone, ponieważ zazwyczaj definiują metody abstrakcyjne do zaimplementowania. | **no**: Klasa z prywatnym konstruktorem również nie może być dziedziczona, ponieważ brak dostępu do konstruktora. | **yes**: Dziedziczenie jest możliwe tylko w tej samej paczce lub z podklasami w innych paczkach. |

---

### Wyjaśnienia kluczowych punktów:

1. **Final class**:
    - Klasa oznaczona jako `final` ma “ostateczny” charakter, czyli jej struktura nie może być zmieniana przez dziedziczenie.
    - Typowe zastosowanie: zapewnienie bezpieczeństwa i zapobieganie niepożądanym modyfikacjom w klasach krytycznych, np. `java.lang.String`.
2. **Abstract class**:
    - Klasa abstrakcyjna działa jak szablon – zawiera metody, które muszą być zaimplementowane w podklasach.
    - Nie można utworzyć instancji klasy abstrakcyjnej, ponieważ jej przeznaczeniem jest bycie rozszerzoną przez inne klasy.
    - Używana, gdy istnieje wspólna logika dla podklas, ale wymagane są również specyficzne implementacje w tych podklasach.
3. **Private constructors only**:
    - Klasa z prywatnym konstruktorem uniemożliwia tworzenie obiektów z zewnątrz. Jest to powszechne w przypadku wzorców projektowych, takich jak **Singleton**.
    - Zastosowanie: ograniczenie kontroli nad instancjonowaniem lub zapewnienie, że obiekt może być utworzony tylko przez metody fabryczne (np. `Factory Pattern`).
4. **Protected constructors only**:
    - Konstruktor `protected` ogranicza tworzenie obiektów do klas dziedziczących i klas w tej samej paczce.
    - Typowe zastosowanie: klasy bazowe w hierarchii dziedziczenia, w których kontroluje się, kto może tworzyć ich obiekty (np. klasy w bibliotekach frameworków).

---

### Kluczowe różnice między `final`, `abstract` i innymi podejściami:

- `final` i `abstract` są **wykluczające się**: klasa `final` nie może być abstrakcyjna, ponieważ nie można jednocześnie uniemożliwiać dziedziczenia (`final`) i wymagać implementacji metod w podklasach (`abstract`).
- Klasa z **prywatnym konstruktorem** pełni rolę pomocniczą, podczas gdy klasa z **protected konstruktorem** jest otwarta dla specyficznych zastosowań w hierarchii dziedziczenia.

---

# Sealed class

Sealed classes w Javie zostały wprowadzone w wersji Java 15 (w ramach funkcji eksperymentalnej) i w pełni ustabilizowane w Java 17. Są to klasy, które pozwalają kontrolować, jakie inne klasy mogą je rozszerzać. Mechanizm ten służy do zwiększenia bezpieczeństwa i lepszej kontroli nad hierarchią klas.

Kluczowe cechy sealed classes:
Deklaracja ograniczeń dziedziczenia: Klasa oznaczona jako sealed wymaga jawnego określenia, które klasy mogą ją dziedziczyć. Robi się to za pomocą słowa kluczowego permits.

```java
public sealed class Shape permits Circle, Rectangle {
    // kod klasy
}
```

Podklasy muszą być final, sealed, lub non-sealed: Wszystkie klasy dziedziczące po klasie sealed muszą być jedną z trzech kategorii:

final: Nie można ich dalej rozszerzać.
sealed: Mogą być dziedziczone, ale tylko przez określone klasy.
non-sealed: Otwierają hierarchię dziedziczenia, pozwalając na dowolne rozszerzenie.

```java
public final class Circle extends Shape {
    // kod klasy
}

public sealed class Rectangle extends Shape permits Square {
    // kod klasy
}

public non-sealed class Square extends Rectangle {
    // kod klasy
}
```

Ograniczenie w obrębie jednego modułu lub pakietu: Wszystkie klasy, które dziedziczą po klasie sealed, muszą znajdować się w tym samym module (lub pakiecie, jeśli moduły nie są używane).

Zastosowania
Kontrola dziedziczenia:

Użycie sealed classes umożliwia twórcom API precyzyjne określenie, jak może być rozszerzana dana hierarchia, co zmniejsza ryzyko błędów.
Zwiększenie bezpieczeństwa typów:

W połączeniu z switch wyrażeniami (wprowadzonymi w Java 14+), sealed classes pozwalają kompilatorowi sprawdzić, czy wszystkie przypadki są obsługiwane.

```java
public static void printShapeDetails(Shape shape) {
    switch (shape) {
        case Circle c -> System.out.println("Circle with radius " + c.getRadius());
        case Rectangle r -> System.out.println("Rectangle with dimensions " + r.getWidth() + "x" + r.getHeight());
    }
}
```

Przykład:

```java
public sealed class Vehicle permits Car, Truck {
    // Kod wspólny dla wszystkich pojazdów
}

public final class Car extends Vehicle {
    // Samochód
}

public final class Truck extends Vehicle {
    // Ciężarówka
}
```
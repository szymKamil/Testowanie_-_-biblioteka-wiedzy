# deepAndShallowCopy

# Deep and shallow copy

W Javie, deep copy i shallow copy odnoszą się do sposobu kopiowania obiektów, szczególnie tych, które zawierają referencje do innych obiektów.

1. Shallow Copy (Płytka kopia):
Shallow copy oznacza, że kopia obiektu zawiera tylko referencje do tych samych obiektów, które były referencjami w oryginale. Innymi słowy, kopiuje strukturę na “pierwszym poziomie”, ale nie klonuje obiektów referencyjnych.

```java
class Person implements Cloneable {
    String name;
    Address address;

    public Person(String name, Address address) {
        this.name = name;
        this.address = address;
    }

    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone(); // Shallow copy
    }
}

class Address {
    String city;
    Address(String city) {
        this.city = city;
    }
}

public class Main {
    public static void main(String[] args) throws CloneNotSupportedException {
        Address address = new Address("Warsaw");
        Person original = new Person("John", address);
        Person shallowCopy = (Person) original.clone();

        // Zmiana miasta w oryginalnym adresie wpłynie na oba obiekty
        shallowCopy.address.city = "Krakow";

        System.out.println(original.address.city); // Krakow
        System.out.println(shallowCopy.address.city); // Krakow
    }
}
```

Cechy:
Zmiany w obiektach referencyjnych w jednym obiekcie (np. Address) będą widoczne w obu kopiach.
Domyślne implementacje kopiowania (np. Object.clone()) zazwyczaj wykonują płytką kopię.

1. Deep Copy (Głęboka kopia):
Deep copy tworzy nową instancję obiektu wraz z kopiami wszystkich obiektów, do których referencje zawiera oryginalny obiekt. W efekcie zmiany w jednej kopii nie wpływają na drugą.

```java
class Person implements Cloneable {
    String name;
    Address address;

    public Person(String name, Address address) {
        this.name = name;
        this.address = address;
    }

    @Override
    protected Object clone() throws CloneNotSupportedException {
        Person cloned = (Person) super.clone();
        cloned.address = new Address(this.address.city); // Deep copy
        return cloned;
    }
}

class Address {
    String city;
    Address(String city) {
        this.city = city;
    }
}

public class Main {
    public static void main(String[] args) throws CloneNotSupportedException {
        Address address = new Address("Warsaw");
        Person original = new Person("John", address);
        Person deepCopy = (Person) original.clone();

        // Zmiana miasta w adresie kopii NIE wpływa na oryginał
        deepCopy.address.city = "Krakow";

        System.out.println(original.address.city); // Warsaw
        System.out.println(deepCopy.address.city); // Krakow
    }
}
```

Cechy:
Każdy obiekt w hierarchii jest kopiowany.
Wymaga więcej zasobów i dodatkowego kodu (np. jawne klonowanie zagnieżdżonych obiektów).

| **Cecha** | **Shallow Copy** | **Deep Copy** |
| --- | --- | --- |
| **Referencje** | Kopiuje tylko referencje | Tworzy nowe obiekty dla wszystkich referencji |
| **Zmiany** | Zmiany w obiektach referencyjnych są widoczne | Zmiany w kopii nie wpływają na oryginał |
| **Wydajność** | Szybsza i wymaga mniej zasobów | Wolniejsza i bardziej zasobożerna |
| **Kiedy używać?** | Gdy dzielenie referencji jest akceptowalne | Gdy wymagane jest pełne oddzielenie danych |

## Tabela

Zabezpieczenie tabeli (tablicy) lub kolekcji w Javie może być interpretowane na różne sposoby w zależności od tego, co dokładnie chcesz osiągnąć. Poniżej przedstawiam kilka podejść w kontekście ochrony danych przed niepożądanymi zmianami:

1. Kopia defensywna (Defensive Copy)
Najczęściej stosowanym mechanizmem zabezpieczenia kolekcji lub tablic jest tworzenie kopii danych podczas przekazywania ich do innej metody lub klasy. Dzięki temu oryginalne dane pozostają nietknięte.

```java
public class SafeArray {
    private final int[] array;

    public SafeArray(int[] array) {
        // Tworzymy kopię, by chronić oryginalną tablicę
        this.array = array.clone();
    }

    public int[] getArray() {
        // Zwracamy kopię, by chronić wewnętrzną tablicę
        return array.clone();
    }
}
```

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class SafeCollection {
    private final List<String> list;

    public SafeCollection(List<String> list) {
        // Tworzymy nową, niemodyfikowalną listę
        this.list = new ArrayList<>(list);
    }

    public List<String> getList() {
        // Zwracamy niemodyfikowalną kopię
        return Collections.unmodifiableList(list);
    }
}
```

1. Użycie kolekcji niemodyfikowalnych
Java dostarcza gotowe metody, które tworzą niemodyfikowalne wersje kolekcji. Dzięki temu nie można dodawać, usuwać ani zmieniać elementów.

```java
import java.util.Collections;
import java.util.List;

public class Main {
    public static void main(String[] args) {
        List<String> modifiableList = List.of("A", "B", "C");
        List<String> unmodifiableList = Collections.unmodifiableList(modifiableList);

        System.out.println(unmodifiableList); // [A, B, C]

        // Próba modyfikacji spowoduje wyjątek UnsupportedOperationException
        // unmodifiableList.add("D"); // Błąd w czasie działania
    }
}
```

Od Javy 9 można tworzyć niemodyfikowalne kolekcje za pomocą metod fabrycznych:

```java
List<String> immutableList = List.of("A", "B", "C");
Set<String> immutableSet = Set.of("X", "Y", "Z");
```

1. Synchronizacja (dla bezpieczeństwa wątków)
Jeśli tabela lub kolekcja jest używana w środowisku wielowątkowym, należy zastosować synchronizację, aby zapobiec jednoczesnym modyfikacjom.

```java
import java.util.Collections;
import java.util.List;
import java.util.ArrayList;

public class Main {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        List<String> synchronizedList = Collections.synchronizedList(list);

        synchronized (synchronizedList) {
            synchronizedList.add("A");
        }
    }
}
```

1. Tworzenie głębokiej kopii (Deep Copy)
Jeśli kolekcja zawiera obiekty referencyjne, należy utworzyć kopię każdego z tych obiektów, aby w pełni zabezpieczyć dane.

```java
import java.util.ArrayList;
import java.util.List;

public class DeepCopyExample {
    public static List<Address> deepCopy(List<Address> originalList) {
        List<Address> copiedList = new ArrayList<>();
        for (Address address : originalList) {
            copiedList.add(new Address(address.getCity())); // Tworzymy nowy obiekt
        }
        return copiedList;
    }
}

class Address {
    private String city;

    public Address(String city) {
        this.city = city;
    }

    public String getCity() {
        return city;
    }
}
```

1. Przechowywanie w zmiennej final
Użycie modyfikatora final dla zmiennej kolekcji lub tablicy zapobiega zmianie referencji, ale nie zabezpiecza samej zawartości.

```java
public class Main {
    private final int[] array = {1, 2, 3};

    public void modifyArray() {
        // array = new int[]{4, 5, 6}; // Błąd kompilacji
        array[0] = 42; // Możliwa modyfikacja zawartości
    }
}
```

| **Metoda zabezpieczenia** | **Opis** |
| --- | --- |
| **Kopia defensywna** | Tworzy kopie danych przy przekazywaniu lub zwracaniu. |
| **Kolekcje niemodyfikowalne** | Uniemożliwia modyfikację za pomocą `Collections.unmodifiableList()` lub `List.of()`. |
| **Synchronizacja** | Zapobiega jednoczesnym modyfikacjom w środowisku wielowątkowym. |
| **Głęboka kopia** | Tworzy niezależne kopie wszystkich elementów kolekcji lub tablicy. |
| **Modyfikator `final`** | Zapobiega zmianie referencji, ale nie zabezpiecza zawartości kolekcji lub tablicy. |

List.copyOf() w Javie zadziała jako sposób na stworzenie niemodyfikowalnej kopii listy. Jednak jej funkcjonalność różni się od Collections.unmodifiableList() i trzeba rozważyć, w jakim kontekście jest używana.

Jak działa List.copyOf()?
Tworzy nową, niemodyfikowalną listę:

Lista zwrócona przez List.copyOf() nie pozwala na dodawanie, usuwanie ani modyfikowanie elementów.
Próba modyfikacji skutkuje wyjątkiem UnsupportedOperationException.
Nie tworzy głębokiej kopii:

List.copyOf() kopiuje tylko referencje do obiektów zawartych w liście źródłowej, co oznacza, że zmiany w obiektach referencyjnych są nadal możliwe.
Eliminuje elementy null:

W przeciwieństwie do Collections.unmodifiableList(), List.copyOf() rzuci NullPointerException, jeśli lista źródłowa zawiera element null.

```java
import java.util.List;

public class Main {
    public static void main(String[] args) {
        // Lista źródłowa
        List<String> originalList = List.of("A", "B", "C");

        // Tworzymy niemodyfikowalną kopię
        List<String> copiedList = List.copyOf(originalList);

        System.out.println(copiedList); // [A, B, C]

        // Próba modyfikacji listy rzuci wyjątek
        // copiedList.add("D"); // UnsupportedOperationException
    }
}
```

Kiedy używać List.copyOf()?
Kiedy potrzebujesz niezależnej, niemodyfikowalnej kopii listy.
Gdy chcesz uniknąć elementów null w liście.
Gdy chcesz mieć pewność, że zmiany w oryginalnej liście nie wpłyną na nową listę.
Wady List.copyOf()
Nie tworzy głębokiej kopii — jeśli lista zawiera obiekty referencyjne, ich zmiany będą widoczne w kopii.

```java
import java.util.List;
import java.util.ArrayList;

class Item {
    String value;
    Item(String value) {
        this.value = value;
    }
}

public class Main {
    public static void main(String[] args) {
        List<Item> originalList = new ArrayList<>();
        originalList.add(new Item("A"));

        List<Item> copiedList = List.copyOf(originalList);

        // Zmiana wewnętrzna obiektu wpłynie na oba
        originalList.get(0).value = "B";
        System.out.println(copiedList.get(0).value); // "B"
    }
}
```

# Kolekcje niemodyfikowalne to NIE to samo co kolekcje niemutowalne

Bardzo ważne jest, aby zrozumieć, że kolekcje niemodyfikowalne to NIE to samo co kolekcje niemutowalne.

Kolekcje stają się niemutowalne tylko wtedy, gdy elementy w kolekcjach są w pełni niemutowalne.

Są to kolekcje o ograniczonej funkcjonalności, które mogą pomóc nam zminimalizować możliwość wprowadzania zmian.

- Nie możesz usuwać, dodawać ani czyścić elementów w kolekcji niemutowalnej.
- Nie możesz także zastępować ani sortować elementów.
- Metody modyfikujące zgłoszą wyjątek `UnsupportedOperationException`.
- Nie możesz utworzyć tego typu kolekcji, jeśli zawiera `null`.

---

# Kolekcje niemodyfikowalne vs. Widoki (okna) kolekcji niemodyfikowalnych

Trzy główne interfejsy kolekcji, `List`, `Set` lub `Map`, mają metody do uzyskania niemodyfikowalnej kopii na podstawie określonego interfejsu, w zależności od typu kolekcji, jak pokazano poniżej.

Dodatkowo klasa `java.util.Collections` oferuje metody do uzyskania niemodyfikowalnych widoków, jak pokazano w tabeli.

Te metody pozwalają nam zbliżyć się do ideału niemutowalności, jeśli jest to potrzebne.

| **Typ** | **Niemodyfikowalna kopia kolekcji** | **Widok (okno) niemodyfikowalnej kolekcji** |
| --- | --- | --- |
| **Lista** | `List.copyOf` | `Collections.unmodifiableList` |
|  | `List.of` |  |
| **Zbiór** | `Set.copyOf` | `Collections.unmodifiableSet` |
|  | `Set.of` | `Collections.unmodifiableNavigableSet` |
|  |  | `Collections.unmodifiableSortedSet` |
| **Mapa** | `Map.copyOf` | `Collections.unmodifiableMap` |
|  | `Map.entry(K k, V v)` | `Collections.unmodifiableNavigableMap` |
|  | `Map.of` | `Collections.unmodifiableSortedMap` |
|  | `Map.ofEntries` |  |

Kolekcje niemodyfikowalne
Kolekcje niemodyfikowalne są to struktury danych, których zawartość nie może zostać zmieniona po utworzeniu. Są one zazwyczaj stosowane, aby zapewnić, że dane pozostaną niezmienne przez cały czas życia aplikacji. Przykłady takich kolekcji to:

Listy: Lista niemodyfikowalna (np. List.of(…) w Java).

Zbiory: Zbiór niemodyfikowalny (np. Set.of(…) w Java).

Mapy: Mapa niemodyfikowalna (np. Map.of(…) w Java).

Widoki (okna) kolekcji niemodyfikowalnych
Widoki kolekcji niemodyfikowalnych to “okna” na istniejące kolekcje, które również nie pozwalają na modyfikacje danych przez użytkownika. Jednakże, jeśli podstawa (oryginalna kolekcja) zostanie zmieniona, te zmiany będą widoczne w widoku. Przykłady:

Listy: Widok listy niemodyfikowalnej (np. Collections.unmodifiableList(…) w Java).

Zbiory: Widok zbioru niemodyfikowalnego (np. Collections.unmodifiableSet(…) w Java).

Mapy: Widok mapy niemodyfikowalnej (np. Collections.unmodifiableMap(…) w Java).

Kluczowe różnice
Immutability: Kolekcje niemodyfikowalne są naprawdę niemodyfikowalne – ich zawartość nie może być zmieniona po utworzeniu. Widoki niemodyfikowalnych kolekcji natomiast, choć nie pozwalają na bezpośrednie modyfikacje, odzwierciedlają zmiany w oryginalnej kolekcji.

Tworzenie: Kolekcje niemodyfikowalne są tworzone na poziomie instancji, podczas gdy widoki kolekcji niemodyfikowalnych są tworzone jako “maski” na istniejące już kolekcje.
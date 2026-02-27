# EnumSet, EnumMap

# Enum w Javie

`enum` (typ wyliczeniowy) to specjalna klasa reprezentująca zbiór stałych. Wprowadzony w Javie 5.
Każda wartość enum to publiczna statyczna stała instancja klasy.

---

## 1. Podstawy – definiowanie i użycie

```java
// Podstawowa deklaracja
public enum Direction {
    NORTH, SOUTH, EAST, WEST
}

// Użycie
Direction dir = Direction.NORTH;
System.out.println(dir);        // "NORTH"
System.out.println(dir.name()); // "NORTH" – nazwa jako String
System.out.println(dir.ordinal()); // 0 – indeks (kolejność deklaracji, od 0)
```

### Wbudowane metody każdego enum

| Metoda | Opis |
| --- | --- |
| `name()` | Zwraca nazwę stałej jako `String` |
| `ordinal()` | Zwraca indeks (pozycję) stałej (od 0) |
| `values()` | Zwraca tablicę wszystkich wartości (metoda statyczna) |
| `valueOf(String name)` | Zwraca stałą o podanej nazwie (metoda statyczna) |
| `compareTo(E other)` | Porównuje wg `ordinal()` |
| `toString()` | Domyślnie zwraca `name()`, można nadpisać |

```java
// values() – iterowanie po wszystkich wartościach
for (Direction d : Direction.values()) {
    System.out.println(d.ordinal() + ": " + d.name());
}
// 0: NORTH
// 1: SOUTH
// 2: EAST
// 3: WEST

// valueOf() – pobranie po nazwie
Direction d = Direction.valueOf("NORTH"); // Direction.NORTH
Direction x = Direction.valueOf("INVALID"); // ❌ IllegalArgumentException
```

---

## 2. Enum z polami i metodami

Enum może mieć **pola, konstruktory i metody** – to jedna z jego najpotężniejszych cech:

```java
public enum Planet {
    MERCURY(3.303e+23, 2.4397e6),
    VENUS  (4.869e+24, 6.0518e6),
    EARTH  (5.976e+24, 6.37814e6);

    private final double mass;    // w kilogramach
    private final double radius;  // w metrach

    // Konstruktor – zawsze prywatny (nie można tworzyć instancji z zewnątrz)
    Planet(double mass, double radius) {
        this.mass = mass;
        this.radius = radius;
    }

    // Metoda instancyjna
    public double surfaceGravity() {
        final double G = 6.67300E-11;
        return G * mass / (radius * radius);
    }

    public double surfaceWeight(double otherMass) {
        return otherMass * surfaceGravity();
    }
}

// Użycie
double earthWeight = 75.0;
double mass = earthWeight / Planet.EARTH.surfaceGravity();
for (Planet p : Planet.values()) {
    System.out.printf("Waga na %s: %.2f%n", p, p.surfaceWeight(mass));
}
```

### Nadpisywanie metod w poszczególnych wartościach

```java
public enum Operation {
    ADD {
        @Override
        public double apply(double x, double y) { return x + y; }
    },
    SUBTRACT {
        @Override
        public double apply(double x, double y) { return x - y; }
    },
    MULTIPLY {
        @Override
        public double apply(double x, double y) { return x * y; }
    };

    public abstract double apply(double x, double y);
}

// Użycie
double result = Operation.ADD.apply(3, 4); // 7.0
```

---

## 3. Enum implementujący interfejs

```java
public interface Describable {
    String getDescription();
}

public enum Season implements Describable {
    SPRING {
        @Override
        public String getDescription() { return "Ciepło i kwiaty"; }
    },
    SUMMER {
        @Override
        public String getDescription() { return "Gorąco i wakacje"; }
    },
    AUTUMN {
        @Override
        public String getDescription() { return "Liście opadają"; }
    },
    WINTER {
        @Override
        public String getDescription() { return "Zimno i śnieg"; }
    };
}

System.out.println(Season.SUMMER.getDescription()); // "Gorąco i wakacje"
```

---

## 4. Enum w instrukcji `switch`

### Klasyczny switch (przed Javą 14)

```java
Direction dir = Direction.NORTH;

switch (dir) {
    case NORTH:
        System.out.println("Idziesz na północ");
        break;
    case SOUTH:
        System.out.println("Idziesz na południe");
        break;
    default:
        System.out.println("Inny kierunek");
}
```

### Switch expression (Java 14+) – zalecany

```java
String opis = switch (dir) {
    case NORTH -> "Idziesz na północ";
    case SOUTH -> "Idziesz na południe";
    case EAST  -> "Idziesz na wschód";
    case WEST  -> "Idziesz na zachód";
};
System.out.println(opis);
```

> Kompilator sprawdza czy wszystkie wartości enum są obsłużone –
jeśli tak, `default` nie jest wymagany.
> 

---

## 5. Enum jako Singleton

Enum to **najlepsza implementacja wzorca Singleton** w Javie:

```java
public enum DatabaseConnection {
    INSTANCE;

    private final String url = "jdbc:mysql://localhost/db";

    public void connect() {
        System.out.println("Łączenie z: " + url);
    }

    public void query(String sql) {
        System.out.println("Wykonuję: " + sql);
    }
}

// Użycie
DatabaseConnection.INSTANCE.connect();
DatabaseConnection.INSTANCE.query("SELECT * FROM users");
```

**Zalety Singleton przez enum:**

- Gwarancja jednej instancji przez JVM
- Bezpieczny wielowątkowo bez synchronizacji
- Odporny na ataki przez refleksję i serializację

---

## 6. Porównywanie enum

```java
Direction a = Direction.NORTH;
Direction b = Direction.NORTH;

// == jest bezpieczne dla enum (jedna instancja każdej wartości)
System.out.println(a == b);          // true
System.out.println(a.equals(b));     // true

// compareTo – porównanie wg ordinal()
Direction.NORTH.compareTo(Direction.SOUTH); // ujemna liczba (NORTH < SOUTH)
```

---

## 7. `EnumSet` – zbiór wartości enum

`EnumSet` to wyspecjalizowana implementacja `Set` zoptymalizowana dla typów enum.
Wewnętrznie używa **tablicy bitowej** – każda wartość enum to jeden bit.

### Tworzenie EnumSet

```java
import java.util.EnumSet;

enum Days { MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY }

// allOf – wszystkie wartości
EnumSet<Days> wszystkie = EnumSet.allOf(Days.class);

// noneOf – pusty zbiór
EnumSet<Days> pusty = EnumSet.noneOf(Days.class);

// of – konkretne wartości
EnumSet<Days> weekend = EnumSet.of(Days.SATURDAY, Days.SUNDAY);

// range – zakres (zgodnie z kolejnością deklaracji)
EnumSet<Days> robocze = EnumSet.range(Days.MONDAY, Days.FRIDAY);

// complementOf – dopełnienie (wszystko czego nie ma w podanym zbiorze)
EnumSet<Days> nienWeekend = EnumSet.complementOf(weekend);
System.out.println(nienWeekend); // [MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY]

// copyOf
EnumSet<Days> kopia = EnumSet.copyOf(weekend);
```

### Operacje na EnumSet

```java
EnumSet<Days> zbior = EnumSet.of(Days.MONDAY, Days.WEDNESDAY);

zbior.add(Days.FRIDAY);
zbior.remove(Days.WEDNESDAY);
zbior.contains(Days.MONDAY);  // true
zbior.size();                  // 2

// Iteracja – zawsze w kolejności deklaracji enum
for (Days d : zbior) {
    System.out.println(d);
}
```

### Tabela metod EnumSet

| Metoda | Opis |
| --- | --- |
| `allOf(Class<E>)` | Wszystkie wartości danego enum |
| `noneOf(Class<E>)` | Pusty zbiór dla danego enum |
| `of(E e1, E e2, ...)` | Zbiór z podanych wartości |
| `range(E from, E to)` | Zbiór z zakresu wartości |
| `complementOf(EnumSet<E>)` | Dopełnienie – wartości spoza podanego zbioru |
| `copyOf(EnumSet<E>)` | Kopia EnumSet |
| `copyOf(Collection<E>)` | EnumSet z kolekcji (musi zawierać przynajmniej 1 element) |
| `add(E)` | Dodaje element |
| `remove(Object)` | Usuwa element |
| `contains(Object)` | Sprawdza obecność |
| `size()` | Liczba elementów |
| `isEmpty()` | Czy pusty |
| `clear()` | Usuwa wszystkie |
| `iterator()` | Iterator (kolejność wg deklaracji) |

### EnumSet vs HashSet

|  | `EnumSet` | `HashSet` |
| --- | --- | --- |
| Wydajność | O(1) – operacje bitowe | O(1) – hashowanie |
| Pamięć | Minimalna (bitmaska) | Większa (obiekty) |
| Typy elementów | Tylko enum | Dowolne |
| Kolejność | Wg deklaracji enum | Brak |
| `null` | ❌ | ✅ (jeden) |
| Kiedy używać | Zawsze gdy elementy to enum | Inne typy |

---

## 8. `EnumMap` – mapa z kluczami enum

`EnumMap` to wyspecjalizowana implementacja `Map` gdzie kluczami są wartości enum.
Wewnętrznie to **tablica** indeksowana przez `ordinal()` – bardzo szybka.

### Tworzenie i użycie EnumMap

```java
import java.util.EnumMap;

enum Days { MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY }

EnumMap<Days, String> harmonogram = new EnumMap<>(Days.class);

harmonogram.put(Days.MONDAY, "Siłownia");
harmonogram.put(Days.WEDNESDAY, "Pływalnia");
harmonogram.put(Days.FRIDAY, "Joga");

System.out.println(harmonogram.get(Days.MONDAY));      // "Siłownia"
System.out.println(harmonogram.containsKey(Days.TUESDAY)); // false
System.out.println(harmonogram.size());                 // 3

// Iteracja – zawsze w kolejności deklaracji enum
harmonogram.forEach((dzien, aktywnosc) ->
    System.out.println(dzien + ": " + aktywnosc));
// MONDAY: Siłownia
// WEDNESDAY: Pływalnia
// FRIDAY: Joga

// Metody Java 8+
harmonogram.putIfAbsent(Days.TUESDAY, "Bieganie");
harmonogram.computeIfAbsent(Days.THURSDAY, k -> "Rower");
harmonogram.merge(Days.MONDAY, " + Sauna", String::concat);
```

### Tabela kluczowych metod EnumMap

| Metoda | Opis |
| --- | --- |
| `put(K, V)` | Dodaje lub aktualizuje wpis |
| `get(Object)` | Pobiera wartość dla klucza |
| `remove(Object)` | Usuwa wpis |
| `containsKey(Object)` | Sprawdza klucz |
| `containsValue(Object)` | Sprawdza wartość |
| `size()` / `isEmpty()` / `clear()` | Podstawowe operacje |
| `keySet()` | Zbiór kluczy (w kolejności deklaracji) |
| `values()` | Kolekcja wartości |
| `entrySet()` | Zbiór wpisów klucz-wartość |
| `putIfAbsent(K, V)` | Dodaje tylko gdy klucz nie istnieje |
| `getOrDefault(K, V)` | Pobiera lub zwraca domyślną wartość |
| `forEach(BiConsumer)` | Iteracja z lambdą |
| `merge(K, V, BiFunction)` | Łączy wartości |
| `computeIfAbsent(K, Function)` | Oblicza wartość gdy brak klucza |
| `computeIfPresent(K, BiFunction)` | Oblicza wartość gdy klucz istnieje |
| `replaceAll(BiFunction)` | Aktualizuje wszystkie wartości |

### EnumMap vs HashMap

|  | `EnumMap` | `HashMap` |
| --- | --- | --- |
| Wydajność | O(1) – indeks tablicy | O(1) – hashowanie |
| Pamięć | Minimalna (tablica) | Większa (węzły hash) |
| Typy kluczy | Tylko enum | Dowolne |
| Kolejność kluczy | Wg deklaracji enum | Brak |
| `null` klucz | ❌ | ✅ (jeden) |
| Kiedy używać | Klucze to enum | Inne typy kluczy |

---

## 9. Kiedy używać enum, EnumSet, EnumMap?

| Potrzeba | Rozwiązanie |
| --- | --- |
| Stały zbiór powiązanych stałych | `enum` |
| Stałe z dodatkową logiką/danymi | `enum` z polami i metodami |
| Jedna instancja obiektu (Singleton) | `enum` |
| Zbiór wybranych wartości enum | `EnumSet` |
| Operacje na zakresach wartości enum | `EnumSet.range()` |
| Mapowanie wartości enum na dane | `EnumMap` |
| Konfiguracja per-wartość enum | `EnumMap` |
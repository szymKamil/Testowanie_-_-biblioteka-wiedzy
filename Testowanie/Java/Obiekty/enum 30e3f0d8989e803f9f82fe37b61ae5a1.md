# enum

# Enum

W języku Java, enum (skrót od “enumeration”) to specjalny typ danych, który reprezentuje zbiór stałych wartości. Jest używany, gdy potrzebujesz określić ograniczoną liczbę możliwych wartości, które dany typ może przyjąć. enum jest klasą specjalnego typu, która umożliwia łatwe i bezpieczne definiowanie zestawu stałych oraz operacji na nich.

```java
public enum DzienTygodnia {
    PONIEDZIALEK, WTOREK, SRODA, CZWARTEK, PIATEK, SOBOTA, NIEDZIELA
}

public class Main {
    public static void main(String[] args) {
        DzienTygodnia dzien = DzienTygodnia.PONIEDZIALEK;

        // Wydruk wartości
        System.out.println("Dzień tygodnia: " + dzien);

        // Iterowanie przez wartości
        for (DzienTygodnia d : DzienTygodnia.values()) {
            System.out.println(d);
        }
    }
}
```

W powyższym przykładzie DzienTygodnia to enum, który definiuje siedem dni tygodnia. Każdy z elementów enum to stała wartości, np. PONIEDZIALEK, WTOREK, itp.

## Metody

| Metoda | Opis |
| --- | --- |
| `values()` | Zwraca tablicę wszystkich wartości w `enum`. |
| `valueOf(String name)` | Zwraca stałą `enum` o podanej nazwie (case-sensitive). |
| `ordinal()` | Zwraca indeks danej stałej `enum` (numeracja zaczyna się od 0). |
| `name()` | Zwraca nazwę stałej `enum` jako `String`. |
| `toString()` | Domyślnie działa jak `name()`, ale można ją nadpisać, aby dostosować reprezentację ciągu znaków. |
| `compareTo(Enum other)` | Porównuje dwie wartości `enum` na podstawie ich pozycji. Zwraca wartość ujemną, jeśli bieżąca wartość jest przed podaną wartością, a dodatnią, jeśli jest po niej. |

Enum w Javie to potężna konstrukcja, która umożliwia nie tylko definiowanie zbioru stałych wartości, ale także dodawanie do nich dodatkowych danych, metod, a nawet implementowanie interfejsów. Poniżej kilka dodatkowych zastosowań enum:

1. Mapowanie wartości i kodów
Enum może przechowywać dodatkowe informacje, np. kody, które można następnie użyć w aplikacji do mapowania na tekst, liczby, lub inne wartości.

```java
public enum StatusZamowienia {
    NOWE("N"), W_REALIZACJI("R"), WYSŁANE("W"), DOSTARCZONE("D");

    private final String kod;

    StatusZamowienia(String kod) {
        this.kod = kod;
    }

    public String getKod() {
        return kod;
    }
}

// Użycie
public class Main {
    public static void main(String[] args) {
        System.out.println(StatusZamowienia.NOWE.getKod()); // Wydrukuje: N
    }
}
```

Enum z metodą abstrakcyjną
Enum może zawierać abstrakcyjne metody, a każda stała enum może dostarczyć własną implementację.

```java
public enum Dzialanie {
    DODAWANIE {
        @Override
        public int wykonaj(int a, int b) {
            return a + b;
        }
    },
    ODEJMOWANIE {
        @Override
        public int wykonaj(int a, int b) {
            return a - b;
        }
    },
    MNOZENIE {
        @Override
        public int wykonaj(int a, int b) {
            return a * b;
        }
    },
    DZIELENIE {
        @Override
        public int wykonaj(int a, int b) {
            return a / b;
        }
    };

    public abstract int wykonaj(int a, int b);
}

// Użycie
public class Main {
    public static void main(String[] args) {
        int wynik = Dzialanie.DODAWANIE.wykonaj(5, 3);
        System.out.println("Wynik dodawania: " + wynik); // Wydrukuje: Wynik dodawania: 8
    }
}
```

Enum jako konfiguracja w aplikacjach
Enum może być używany do konfiguracji aplikacji, np. do przechowywania poziomów logowania, rodzajów środowisk, ról użytkowników czy typów dokumentów.

```java
public enum PoziomLogowania {
    DEBUG, INFO, WARN, ERROR
}

// Użycie w systemie logowania
public class Logger {
    private PoziomLogowania poziom;

    public Logger(PoziomLogowania poziom) {
        this.poziom = poziom;
    }

    public void log(PoziomLogowania level, String message) {
        if (level.ordinal() >= poziom.ordinal()) {
            System.out.println("[" + level + "] " + message);
        }
    }
}

// Użycie
public class Main {
    public static void main(String[] args) {
        Logger logger = new Logger(PoziomLogowania.INFO);
        logger.log(PoziomLogowania.DEBUG, "Debug info"); // Nie zostanie wydrukowane
        logger.log(PoziomLogowania.INFO, "Informacja");  // Zostanie wydrukowane
    }
}
```

Enum jako Singleton
Wzorzec Singleton, zapewniający, że tylko jedna instancja klasy istnieje, może być zrealizowany przy pomocy enum. Takie podejście gwarantuje, że wirtualna maszyna Java zadba o to, by instancja była jedyna i globalnie dostępna.

```java
public enum LoggerSingleton {
    INSTANCE;

    public void log(String message) {
        System.out.println(message);
    }
}

// Użycie
public class Main {
    public static void main(String[] args) {
        LoggerSingleton.INSTANCE.log("Log from singleton");
    }
}
```

Implementacja interfejsów przez enum
Enum może implementować interfejsy, co pozwala traktować je jako bardziej elastyczne obiekty, które można łatwo przekazywać lub przechowywać w kolekcjach.

```java
public interface Zachowanie {
    void wykonaj();
}

public enum RolaUzytkownika implements Zachowanie {
    ADMIN {
        @Override
        public void wykonaj() {
            System.out.println("Admin: Pełen dostęp");
        }
    },
    UZYTKOWNIK {
        @Override
        public void wykonaj() {
            System.out.println("Użytkownik: Ograniczony dostęp");
        }
    },
    GOSC {
        @Override
        public void wykonaj() {
            System.out.println("Gość: Tylko do odczytu");
        }
    }
}

// Użycie
public class Main {
    public static void main(String[] args) {
        for (RolaUzytkownika rola : RolaUzytkownika.values()) {
            System.out.print(rola + ": ");
            rola.wykonaj();
        }
    }
}
```

# Konstruktory

Kluczowe cechy konstruktora w enum
Konstruktor w enum zawsze jest prywatny.

Domyślnie konstruktor jest oznaczony jako private, nawet jeśli nie jawnie to zadeklarujemy.
Nie można używać modyfikatorów public ani protected dla konstruktora w enum, ponieważ nie można tworzyć nowych instancji enumu poza jego definicją.
Konstruktor jest wywoływany automatycznie dla każdej wartości enum.

Podczas definiowania wartości enum, konstruktor jest wykonywany dla każdej z tych wartości.
Argumenty w konstruktorze enum.

Konstruktor enum może przyjmować argumenty, które są przekazywane podczas deklaracji wartości enum.

Przykład:

```java
enum Day {
    MONDAY("Start of the workweek"),
    TUESDAY("Second day of workweek"),
    WEDNESDAY("Midweek"),
    THURSDAY("Almost Friday"),
    FRIDAY("End of the workweek"),
    SATURDAY("Weekend!"),
    SUNDAY("Rest day");

    private final String description;

    // Prywatny konstruktor
    Day(String description) {
        this.description = description;
    }

    public String getDescription() {
        return description;
    }
}

public class EnumExample {
    public static void main(String[] args) {
        for (Day day : Day.values()) {
            System.out.println(day + ": " + day.getDescription());
        }
    }
}
```

Ograniczenia konstruktorów w enum
Nie można tworzyć nowych instancji enum za pomocą new.

Konstruktor jest wywoływany automatycznie dla wartości zdefiniowanych w deklaracji.
Brak dziedziczenia:

Enumy w Javie są domyślnie finalne, więc nie można ich rozszerzać.
Każda wartość enum jest stałą.

Są to instancje singleton.

```java
public enum Generation {
    GEN_Z { // 2 bloki kodu pozwalają zapakować instancję
        {
            System.out.println("-- SPECIAL FOR " + this + " --");
        }
    },
    MILLENNIAL(1981, 2000),
    GEN_X(1965, 1980),
    BABY_BOOMER(1946, 1964),
    SILENT_GENERATION(1927, 1945),
    GREATEST_GENERATION(1901, 1926);

    private final int startYear;
    private final int endYear;

    Generation() { // pusty konstruktor wywołuje Gen_Z gdyż jest bez wartości - domyślny
        this(2001, LocalDate.now().getYear());
    }

    Generation(int startYear, int endYear) {
        this.startYear = startYear;
        this.endYear = endYear;
        System.out.println(this);
    }
}
```
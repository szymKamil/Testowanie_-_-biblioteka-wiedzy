# Moduły

W Javie moduły zostały wprowadzone w wersji Java 9 jako część projektu Jigsaw (JSR 376) i stanowią mechanizm do lepszego zarządzania zależnościami, enkapsulacji kodu oraz ułatwiają przygotowanie mniejszych, bezpieczniejszych i bardziej elastycznych aplikacji. Poniżej omówię najważniejsze zagadnienia związane z modułami.

1. Dlaczego moduły?
Enkapsulacja – Możliwość ukrycia pakietów wewnątrz modułu; tylko zadeklarowane w exports są widoczne na zewnątrz.

Kontrola zależności – Deklaratywne zarządzanie, które moduły zewnętrzne są potrzebne (requires).

Bezpieczeństwo i spójność – Eliminacja konfliktów nazw (tzw. „JAR hell”) dzięki jednoznacznej deklaracji modułów.

Lepsza optymalizacja czasu uruchamiania – JVM może ładować tylko potrzebne moduły, co skraca start aplikacji i zmniejsza zużycie pamięci.

1. Struktura modułu - W katalogu źródłowym modułu musimy umieścić plik module-info.java w głównym pakiecie modułu:

pgsql
Kopiuj
Edytuj
/project-root
└─ src
└─ com.acme.mojmodul
├─ module-info.java
├─ com
│ └─ acme
│ └─ mojmodul
│ ├─ Foo.java
│ └─ internal
│ └─ Secret.java
Przykład module-info.java
java
Kopiuj
Edytuj
module com.acme.mojmodul {
// zależności od innych modułów
requires java.logging;
requires com.fasterxml.jackson.databind;

```
// eksportowane pakiety
exports com.acme.mojmodul;

// jeśli moduł udostępnia usługi (Service Provider)
provides com.acme.spi.MojSerwis
    with com.acme.mojmodul.impl.MojSerwisImpl;

// jeśli moduł konsumuje usługi
uses com.acme.spi.InnaUsługa;
```

}
requires – moduły, od których nasz moduł zależy

exports – pakiety widoczne na zewnątrz

provides … with … – deklaracja implementacji serwisu (Service Provider)

uses – deklaracja konsumenta serwisu (Service Consumer)

1. Kompilacja i uruchamianie modułów
Kompilacja
bash
Kopiuj
Edytuj
javac -d out
–module-source-path src
$(find src -name “*.java”)
–module-source-path pozwala wskazać katalog źródłowy modułów.

Wynikowe .class trafią do struktury out/com.acme.mojmodul/….

Uruchomienie
bash
Kopiuj
Edytuj
java –module-path out

–module com.acme.mojmodul/com.acme.mojmodul.Main
–module-path (skrócone -p) wskazuje katalog z modułami.

–module (skrócone -m) określa, który moduł i która klasa z public static void main ma być uruchomiona.

1. Usługi (Service Loader)
Java moduły niosą wbudowany mechanizm Service Loader, dzięki któremu można dynamicznie wstrzykiwać implementacje:

Interfejs serwisu:

java
Kopiuj
Edytuj
package com.acme.spi;
public interface MojSerwis {
void wykonaj();
}
Implementacja:

java
Kopiuj
Edytuj
package com.acme.mojmodul.impl;
import com.acme.spi.MojSerwis;
public class MojSerwisImpl implements MojSerwis {
@Override
public void wykonaj() {
System.out.println(“Hello from Serwis!”);
}
}
W module implementującym:

java
Kopiuj
Edytuj
provides com.acme.spi.MojSerwis
with com.acme.mojmodul.impl.MojSerwisImpl;
Konsument:

java
Kopiuj
Edytuj
ServiceLoader loader = ServiceLoader.load(MojSerwis.class);
for (MojSerwis s : loader) {
s.wykonaj();
}
5. Najczęstsze pułapki
Mieszanie modułowego i niemodułowego kodu – unikaj dodawania niezmodyfikowanych bibliotek JAR na module-path; jeśli są niemodułowe, lepiej umieścić je na class-path lub automatyczne moduły.

Automatyczne moduły – JAR-y bez module-info.java stają się „automatycznymi modułami”, co rozwiązuje problem zależności, ale może ukrywać konflikty.

Nadmierne eksportowanie – eksportuj tylko te pakiety, które naprawdę są częścią API modułu.

1. Podsumowanie
System modułów (JPMS) wprowadza klarowną strukturę aplikacji, lepsze bezpieczeństwo oraz optymalizację.

Każdy moduł definiuje w module-info.java swoje zależności i eksportowane pakiety.

Mechanizm usług (Service Loader) jest wbudowany i wspierany deklaratywnie.

Warto się stopniowo przesiadać na moduły, zaczynając od nowych projektów lub korzystając z automatycznych modułów dla starszych bibliotek.

Dzięki modułom Twoje aplikacje w Javie stają się bardziej przejrzyste, łatwiejsze w utrzymaniu i bezpieczniejsze.

# Moduły w Javie (JPMS)

Moduły zostały wprowadzone w **Javie 9** jako część projektu **Jigsaw (JSR 376)**. Stanowią mechanizm do lepszego
zarządzania zależnościami, enkapsulacji kodu oraz umożliwiają tworzenie mniejszych, bezpieczniejszych i bardziej
elastycznych aplikacji.

---

## 1. Dlaczego moduły?

- **Enkapsulacja** – możliwość ukrycia pakietów wewnątrz modułu; tylko pakiety zadeklarowane w `exports` są widoczne
na zewnątrz.
- **Kontrola zależności** – deklaratywne zarządzanie tym, które moduły zewnętrzne są potrzebne (`requires`).
- **Bezpieczeństwo i spójność** – eliminacja konfliktów nazw (tzw. **JAR hell**) dzięki jednoznacznej deklaracji
modułów.
- **Lepsza optymalizacja czasu uruchamiania** – JVM może ładować tylko potrzebne moduły, co skraca start aplikacji
i zmniejsza zużycie pamięci.

---

## 2. Moduły wbudowane w JDK

Sama Java od wersji 9 jest podzielona na moduły. Najważniejsze z nich:

| Moduł | Zawartość |
| --- | --- |
| `java.base` | Klasy podstawowe (`java.lang`, `java.util`, `java.io` itp.) – **dodawany automatycznie** |
| `java.sql` | JDBC, obsługa baz danych |
| `java.logging` | `java.util.logging` |
| `java.desktop` | AWT, Swing |
| `java.xml` | Parsowanie XML |

> `java.base` jest domyślnie dostępny w każdym module – nie trzeba go deklarować w `requires`.
> 

---

## 3. Struktura modułu

W katalogu źródłowym modułu musimy umieścić plik `module-info.java` w głównym katalogu modułu:

```
/project-root
└─ src
   └─ com.acme.mojmodul
      ├─ module-info.java
      └─ com
         └─ acme
            └─ mojmodul
               ├─ Foo.java
               └─ internal
                  └─ Secret.java
```

### Przykład `module-info.java`

```java
module com.acme.mojmodul {
    // zależności od innych modułów
    requires java.logging;
    requires com.fasterxml.jackson.databind;

    // eksportowane pakiety (publiczne API)
    exports com.acme.mojmodul;

    // eksport tylko do konkretnego modułu (targeted export)
    exports com.acme.mojmodul.internal to com.acme.innymodul;

    // otwarcie pakietu na refleksję (np. dla frameworków)
    opens com.acme.mojmodul.model;

    // jeśli moduł udostępnia usługi (Service Provider)
    provides com.acme.spi.MojSerwis
        with com.acme.mojmodul.impl.MojSerwisImpl;

    // jeśli moduł konsumuje usługi
    uses com.acme.spi.InnaUsługa;
}
```

### Dyrektywy `module-info.java`

| Dyrektywa | Opis |
| --- | --- |
| `requires` | Moduły, od których nasz moduł zależy |
| `requires transitive` | Zależność tranzytywna – widoczna też dla modułów używających naszego |
| `requires static` | Zależność tylko w czasie kompilacji (opcjonalna w runtime) |
| `exports` | Pakiety widoczne publicznie na zewnątrz modułu |
| `exports ... to ...` | Pakiet widoczny tylko dla wskazanego modułu (targeted export) |
| `opens` | Pakiet dostępny przez refleksję w runtime |
| `opens ... to ...` | Refleksja dostępna tylko dla wskazanego modułu |
| `provides ... with ...` | Deklaracja implementacji serwisu (Service Provider) |
| `uses` | Deklaracja konsumenta serwisu (Service Consumer) |

---

## 4. `requires transitive` – zależności tranzytywne

Jeśli moduł **A** deklaruje `requires transitive B`, to każdy moduł **C** który używa A, automatycznie widzi
też moduł B – bez potrzeby osobnego deklarowania go w C.

Przydatne gdy nasz moduł udostępnia API, które zwraca typy z innego modułu:

```java
// moduł A
module com.acme.moduleA {
    requires transitive com.acme.moduleB; // C używający A automatycznie widzi B
    exports com.acme.moduleA;
}

// moduł C – nie musi deklarować requires com.acme.moduleB
module com.acme.moduleC {
    requires com.acme.moduleA; // dostęp do A i B
}
```

---

## 5. `exports` vs `opens` – różnica

To częste źródło nieporozumień, szczególnie przy pracy z frameworkami:

| Dyrektywa | Dostęp w czasie kompilacji | Dostęp w runtime | Refleksja |
| --- | --- | --- | --- |
| `exports` | ✅ | ✅ | ❌ |
| `opens` | ❌ | ✅ | ✅ |
- **`exports`** – udostępnia pakiet jako publiczne API. Klasy są widoczne i można z nich korzystać normalnie,
ale nie można ich introspekcjonować przez refleksję z zewnątrz.
- **`opens`** – udostępnia pakiet wyłącznie na potrzeby **refleksji w runtime**. Niezbędne dla frameworków takich
jak **Spring**, **Hibernate**, **Jackson**, które używają refleksji do wstrzykiwania zależności, mapowania
obiektów itp.

```java
module com.acme.mojmodul {
    exports com.acme.mojmodul.api;      // publiczne API – dostępne normalnie
    opens com.acme.mojmodul.model;      // encje/DTO – dostępne przez refleksję
    opens com.acme.mojmodul.config to spring.core; // refleksja tylko dla Springa
}
```

### `open module` – otwarcie całego modułu

Można oznaczyć cały moduł jako otwarty na refleksję (przydatne przy migracji starszego kodu):

```java
open module com.acme.mojmodul {
    requires java.logging;
    exports com.acme.mojmodul.api;
    // wszystkie pakiety otwarte na refleksję
}
```

---

## 6. Kompilacja i uruchamianie

### Kompilacja

```bash
javac -d out \
  --module-source-path src \
  $(find src -name "*.java")
```

- `-module-source-path` – wskazuje katalog źródłowy modułów
- Wynikowe pliki `.class` trafią do struktury `out/com.acme.mojmodul/...`

### Uruchomienie

```bash
java --module-path out \
     --module com.acme.mojmodul/com.acme.mojmodul.Main
```

- `-module-path` (skrót: `p`) – wskazuje katalog z modułami
- `-module` (skrót: `m`) – określa moduł i klasę z metodą `main`

---

## 7. Usługi (Service Loader)

Moduły mają wbudowany mechanizm **Service Loader**, który umożliwia dynamiczne wstrzykiwanie implementacji bez
twardego powiązania kodu.

**Interfejs serwisu:**

```java
package com.acme.spi;

public interface MojSerwis {
    void wykonaj();
}
```

**Implementacja:**

```java
package com.acme.mojmodul.impl;
import com.acme.spi.MojSerwis;

public class MojSerwisImpl implements MojSerwis {
    @Override
    public void wykonaj() {
        System.out.println("Hello from Serwis!");
    }
}
```

**Deklaracja w `module-info.java` modułu dostawcy:**

```java
module com.acme.mojmodul {
    requires com.acme.spi;
    provides com.acme.spi.MojSerwis
        with com.acme.mojmodul.impl.MojSerwisImpl;
}
```

**Konsument serwisu:**

```java
ServiceLoader<MojSerwis> loader = ServiceLoader.load(MojSerwis.class);
for (MojSerwis s : loader) {
    s.wykonaj();
}
```

**Deklaracja w `module-info.java` modułu konsumenta:**

```java
module com.acme.konsument {
    requires com.acme.spi;
    uses com.acme.spi.MojSerwis;
}
```

---

## 8. Automatyczne moduły

JAR-y **bez** pliku `module-info.java` (stare biblioteki) umieszczone na `--module-path` stają się
**automatycznymi modułami**:

- Nazwa modułu jest wyводzona z nazwy pliku JAR (np. `jackson-databind-2.15.jar` → `jackson.databind`)
- Automatycznie eksportują **wszystkie** swoje pakiety
- Automatycznie `requires` wszystkie inne moduły na ścieżce

> Jeśli biblioteka jest niemodułowa i nie chcemy jej traktować jako automatycznego modułu, lepiej umieścić ją
na `--class-path` zamiast `--module-path`.
> 

---

## 9. Najczęstsze pułapki

**Mieszanie modułowego i niemodułowego kodu** – unikaj dodawania niezmodyfikowanych bibliotek JAR na
`--module-path`; jeśli są niemodułowe, rozważ `--class-path` lub automatyczne moduły.

**Nadmierne eksportowanie** – eksportuj tylko pakiety, które naprawdę są częścią publicznego API modułu.
Pakiety `internal` powinny pozostać ukryte.

**Brak `opens` dla frameworków** – Spring, Hibernate i inne frameworki używają refleksji. Bez odpowiednich
dyrektyw `opens` aplikacja może rzucać `InaccessibleObjectException` w runtime.

**Cykliczne zależności** – moduły nie mogą mieć cyklicznych zależności (`requires`). W przypadku konieczności
współdzielenia kodu warto wydzielić wspólny moduł bazowy.

---

## 10. Podsumowanie

- System modułów (**JPMS**) wprowadza klarowną strukturę, lepsze bezpieczeństwo i optymalizację aplikacji.
- Każdy moduł definiuje w `module-info.java` swoje zależności (`requires`), API (`exports`) i dostęp
refleksyjny (`opens`).
- `requires transitive` pozwala propagować zależności do modułów korzystających z naszego modułu.
- Mechanizm **Service Loader** umożliwia luźne powiązanie między interfejsem a implementacją.
- Warto zaczynać od nowych projektów lub korzystać z automatycznych modułów dla starszych bibliotek.
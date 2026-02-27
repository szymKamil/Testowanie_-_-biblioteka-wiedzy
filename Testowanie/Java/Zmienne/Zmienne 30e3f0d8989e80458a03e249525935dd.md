# Zmienne

W Javie **zmienne (variables)** to nazwy, które przechowują wartości danych. Każda zmienna w Javie ma określony typ,
który decyduje o rodzaju przechowywanych danych oraz operacjach, jakie można na niej wykonać.
Zmienne to wielorazowe kontenery na wartości, które przechowywane są w pamięci.
[Vary + able - z ang. potocznie można zmienić.]

## Rodzaje zmiennych w Javie:

1. **Zmienne lokalne (local variables)**: zmienne deklarowane wewnątrz metod, konstruktorów lub bloków kodu. Istnieją
tylko w obrębie danego bloku i są usuwane po jego zakończeniu.
    
    ```java
    public void metoda() {
        int x = 10;  // Zmienna lokalna
    }
    ```
    

## Zmienne instancyjne (instance variables)

Deklarowane wewnątrz klasy, ale poza metodami. Każda instancja klasy (obiekt) ma swoją kopię zmiennych instancyjnych.

```java
public class Klasa {
    int liczba;  // Zmienna instancyjna
}
```

## Zmienne statyczne (static variables)

Zmienne oznaczone słowem kluczowym static, które należą do klasy, a nie do obiektów. Wszystkie obiekty danej klasy
dzielą tę samą wartość zmiennej statycznej.

```java
public class Klasa {
    static int licznik = 0;  // Zmienna statyczna
}
```

## Zmienne lokalne (local variables)

Umieszczone wewnątrz metody, ograniczone blokiem.

# Zmienne o typach prymitywnych i referencyjnych

🟥 Typy prymitywne – przechowywane bezpośrednio w pamięci stosu (stack).

🟦 Typy referencyjne – na stosie przechowywany jest adres (referencja), który wskazuje na dane znajdujące się w pamięci
sterty (heap).

🟥 Typy prymitywne - są to podstawowe typy danych, które przechowują proste wartości.

```java
byte(8bitów);

short(16bitów);

int(32bity);

long(64bity);

float(32bity, liczba zmiennoprzecinkowa);

double(64bity, liczba zmiennoprzecinkowa);

char(16bitów, pojedynczy znak Unicode);

boolean(1bit, wartość true lub false);
```

| Typ danych | Domyślna wartość | Rozmiar |
| --- | --- | --- |
| boolean | false | 1 bit |
| char | ‘000’ | 2 bytes |
| byte | 0 | 1 byte |
| short | 0 | 2 bytes |
| int | 0 | 4 bytes |
| long | 0L | 8 bytes |
| float | 0.0f | 4 bytes |
| double | 0.0d | 8 bytes |

🟦 Typy referencyjne - przechowują referencje (odwołania) do obiektów. Wszystkie klasy obiektowe w Javie (np. String,
Array) są typami referencyjnymi.
Obejmują:

- Obiekty klas (np. String, Integer).
- Tablice (np. int[], String[]).
- Interfejsy (np. Runnable).
- Typy generyczne (np. List
    
    ).
    

Domyślną wartością jest `null`.

Przykład:

```java
String tekst = "Witaj, świecie!";
int[] tablica = {1, 2, 3};
```

### Inicjalizacja zmiennych

Wszystkie zmienne muszą być zainicjalizowane przed ich użyciem. Zmienne lokalne nie są automatycznie inicjalizowane,
natomiast zmienne instancyjne i statyczne mają domyślne wartości:

- Prymitywne typy liczbowe: 0
- Typy logiczne: *false*
- Typy referencyjne: *null*

Przykład inicjalizacji zmiennych:

```java
public class Przyklad {
    int liczba = 10;  // Inicjalizacja zmiennej instancyjnej
    static int licznik = 0;  // Inicjalizacja zmiennej statycznej

    public void metoda() {
        int lokalna = 5;  // Inicjalizacja zmiennej lokalnej
    }
}
```

Zmienne mogą być zainicjalizowane używając jednego typu:

```java
double d1 = 3.14, d2 = 2 * 3.14;
```

## Zakres zmiennych

Zakres zmiennej to obszar w programie, w którym zmienna jest dostępna. Dzieli się na:

- Zmienne lokalne: dostępne tylko wewnątrz metody, w której są zadeklarowane.
- Zmienne instancyjne: dostępne wewnątrz obiektu klasy, w której zostały zadeklarowane.
- Zmienne statyczne: dostępne w całej klasie i dzielone między wszystkimi obiektami tej klasy.

Java stosuje przekazywanie przez wartość, co oznacza, że dla typów prymitywnych przekazywana jest wartość zmiennej, a
dla typów referencyjnych przekazywana jest referencja do obiektu (ale nie sam obiekt). Warto wspomnieć, że modyfikacja
obiektu wewnątrz metody zmienia stan obiektu poza metodą, natomiast przypisanie nowej wartości zmiennej referencyjnej
nie zmieni oryginalnego obiektu.

Zmienne lokalne mogą być deklarowane w dowolnym bloku kodu, np. wewnątrz pętli czy instrukcji warunkowych. Zasięg takiej
zmiennej kończy się wraz z końcem bloku, w którym jest zadeklarowana.

## VAR

Od Javy 10 istnieje możliwość używania var do deklarowania zmiennych lokalnych z automatycznym określeniem ich typu, co
poprawia czytelność kodu, szczególnie przy typach generycznych:

```java
var liczba = 5; // Typ `int` jest określony na podstawie przypisanej wartości
```

`Var` działa jako automat - kompilator rozpoznaje typ elementu.
Ograniczenia:

- Nie można używać `var` dla zmiennych statycznych ani instancyjnych.
- Typ musi być jednoznacznie określony podczas inicjalizacji.
- `var` działa tylko w lokalnym kontekście

🔸 Czemu typ musi być jednoznaczny podczas inicjalizacji?

Bo var nie jest typem, tylko skrótem do wywnioskowanego typu – i to działa tylko, jeśli kompilator od razu widzi, jaki
to typ:
✅ To działa:

```java
  var x = 5; // typ to int
```

❌ To NIE działa:

```java
var y; // brak inicjalizacji → kompilator nie wie, jaki typ
```

## Stałe

Stałe w języku Java to zmienne, których wartości są niezmienne przez cały czas działania programu. Oznacza to, że
wartość stałej nie może zostać zmieniona po jej nadaniu. W Javie stałe definiuje się za pomocą słowa kluczowego `final`.

Przykład deklaracji stałej:

```java

final int MAX_VALUE = 100;
```

Tutaj:

- final oznacza, że MAX_VALUE jest stałą.
- int to typ danych.
- MAX_VALUE to nazwa stałej.
- 100 to przypisana wartość, której nie można później zmienić.

Stałe są przydatne, gdy mamy wartości, które się nie zmieniają, np. stałe matematyczne (jak π lub e), limity lub
konfiguracje programu. Stosowanie stałych poprawia czytelność kodu i ułatwia jego konserwację, gdyż zamiast powtarzać ”
magiczne liczby”, odwołujemy się do nazwanych wartości, które mają jasne znaczenie.
Nazwy stałych powinny być zapisane **WIELKIMI LITERAMI**, a słowa są oddzielane znakiem podkreślenia (_). Na przykład:
MAX_VALUE, PI, DEFAULT_TIMEOUT.
Stałe powinny być zdefiniowane jako public static final w przypadku stałych klasowych, aby były dostępne bez
konieczności tworzenia obiektu klasy.

```java
public class MyClass {
    public static final int MAX_SIZE = 50;
    public static final String DEFAULT_NAME = "Java";
}
```

Ciekawostka o stałych: Jeśli stała jest obliczana w momencie kompilacji (np. public static final int liczba = 10 + 20;),
to kompilator “wstawia” jej wartość do kodu programu. Jednak jeśli stała jest obliczana w trakcie działania programu,
będzie wczytywana przy pierwszym użyciu.

## Zmienne final a niezmienność obiektów:

Warto wspomnieć, że final w kontekście zmiennych referencyjnych (np. obiektów) oznacza, że nie można zmienić
referencji (odwołania) do obiektu, ale samego obiektu (jego wewnętrznych pól) można już modyfikować. Przykład

```java
final List<String> lista = new ArrayList<>();
lista.add("element");  // Możliwe
lista =new ArrayList<>();  // Błąd kompilacji
```

Klasy takie jak String czy klasy z biblioteki java.util.Collections mają niemutowalne obiekty.

```java
String tekst = "Hello";
tekst = tekst.concat(" World"); // Tworzony nowy obiekt, oryginalny pozostaje niezmieniony
```

## Dostęp do zmiennych

Modyfikatory dostępu (*public*, *protected*, *private*, *bez modyfikatora* (inaczej default)), określają, gdzie dana
zmienna (lub metoda, lub inny element) może być używana:

- public – zmienna dostępna w całym programie.
- private – zmienna dostępna tylko w obrębie klasy, w której została zadeklarowana.
- protected – zmienna dostępna w obrębie klasy, pakietu oraz klasach dziedziczących.
- Bez modyfikatora – dostęp ograniczony do pakietu (packet).

## Shadowing (zaciemnianie)

“Shadowing” (zaciemniania zmiennych), polega na tym, że zmienna lokalna przesłania zmienną instancyjną lub statyczną o tej samej nazwie.

```java
public class Przyklad {
    int liczba = 10;

    public void metoda() {
        int liczba = 20; // Zmienna lokalna przesłania instancyjną
        System.out.println(liczba); // Wyświetli: 20
        System.out.println(this.liczba); // Wyświetli: 10
    }
}
```

# Volatile

`volatile` jest używane w programowaniu wielowątkowym w Javie. Deklaracja zmiennej jako volatile zapewnia, że jej wartość
będzie odczytywana bezpośrednio z pamięci głównej (RAM), a nie z lokalnego cache procesora. Dzięki temu wszystkie wątki
widzą aktualną wartość zmiennej.

```java
public class Przyklad {
    private volatile boolean flaga = true;

    public void zmienFlage() {
        flaga = false; // Zmiana widoczna dla innych wątków
    }
}
```

# Zmienne w Javie

W Javie **zmienne (variables)** to nazwy, które przechowują wartości danych. Każda zmienna w Javie ma określony typ,
który decyduje o rodzaju przechowywanych danych oraz operacjach, jakie można na niej wykonać.
Zmienne to wielorazowe kontenery na wartości, które przechowywane są w pamięci.
[Vary + able – z ang. potocznie: „można zmienić".]

---

## Konwencje nazewnictwa

| Element | Konwencja | Przykład |
| --- | --- | --- |
| Zmienna, metoda | `camelCase` | `myVariable`, `getAge` |
| Klasa | `PascalCase` | `MyClass`, `Animal` |
| Stała | `UPPER_SNAKE_CASE` | `MAX_VALUE`, `PI` |
| Pakiet | `lowercase` | `com.example.app` |

---

## Rodzaje zmiennych w Javie

### 1. Zmienne lokalne (local variables)

Deklarowane wewnątrz metod, konstruktorów lub bloków kodu. Istnieją tylko w obrębie danego bloku i są usuwane po jego
zakończeniu. **Nie są automatycznie inicjalizowane** – muszą być jawnie zainicjalizowane przed użyciem, inaczej
wystąpi błąd kompilacji.

```java
public void metoda() {
    int x = 10;  // Zmienna lokalna – musi być zainicjalizowana
    // int y;
    // System.out.println(y); // Błąd kompilacji!
}
```

Zmienne lokalne mogą być deklarowane w dowolnym bloku kodu, np. wewnątrz pętli czy instrukcji warunkowych. Zasięg
takiej zmiennej kończy się wraz z końcem bloku, w którym jest zadeklarowana.

### 2. Zmienne instancyjne (instance variables)

Deklarowane wewnątrz klasy, ale poza metodami. Każda instancja klasy (obiekt) ma swoją własną kopię zmiennych
instancyjnych. Mają domyślne wartości (patrz tabela poniżej).

```java
public class Klasa {
    int liczba;  // Zmienna instancyjna – domyślna wartość: 0
}
```

### 3. Zmienne statyczne (static variables)

Oznaczone słowem kluczowym `static`, należą do klasy, a nie do konkretnych obiektów. Wszystkie obiekty danej klasy
dzielą tę samą wartość zmiennej statycznej. Mają domyślne wartości tak jak zmienne instancyjne.

```java
public class Klasa {
    static int licznik = 0;  // Zmienna statyczna – wspólna dla wszystkich obiektów
}
```

---

## Zmienne o typach prymitywnych i referencyjnych

🟥 **Typy prymitywne** – przechowywane bezpośrednio w pamięci stosu (stack).

🟦 **Typy referencyjne** – na stosie przechowywany jest adres (referencja), który wskazuje na dane znajdujące się
w pamięci sterty (heap).

### Typy prymitywne

| Typ danych | Domyślna wartość | Rozmiar | Zakres |
| --- | --- | --- | --- |
| `boolean` | `false` | 1 bit | `true` / `false` |
| `char` | `'\u0000'` | 2 bytes | `'\u0000'` do `'\uffff'` |
| `byte` | `0` | 1 byte | -128 do 127 |
| `short` | `0` | 2 bytes | -32 768 do 32 767 |
| `int` | `0` | 4 bytes | -2^31 do 2^31 - 1 |
| `long` | `0L` | 8 bytes | -2^63 do 2^63 - 1 |
| `float` | `0.0f` | 4 bytes | ~7 cyfr precyzji (IEEE 754) |
| `double` | `0.0d` | 8 bytes | ~15-16 cyfr precyzji (IEEE 754) |

### Typy referencyjne

Przechowują referencje (odwołania) do obiektów. Domyślną wartością jest `null`. Obejmują:

- Obiekty klas (np. `String`, `Integer`)
- Tablice (np. `int[]`, `String[]`)
- Interfejsy (np. `Runnable`)
- Typy generyczne (np. `List<String>`)

```java
String tekst = "Witaj, świecie!";
int[] tablica = {1, 2, 3};
String brak = null;  // referencja wskazuje na "nic"
```

---

## Inicjalizacja zmiennych

Zmienne mogą być zainicjalizowane podczas deklaracji lub później. Wiele zmiennych tego samego typu można zadeklarować
w jednej linii:

```java
double d1 = 3.14, d2 = 2 * 3.14;
```

Przykład inicjalizacji różnych rodzajów zmiennych:

```java
public class Przyklad {
    int liczba = 10;         // Inicjalizacja zmiennej instancyjnej
    static int licznik = 0;  // Inicjalizacja zmiennej statycznej

    public void metoda() {
        int lokalna = 5;     // Inicjalizacja zmiennej lokalnej
    }
}
```

---

## Zakres zmiennych (Scope)

Zakres zmiennej to obszar w programie, w którym zmienna jest dostępna:

- **Lokalne** – dostępne tylko wewnątrz bloku, w którym są zadeklarowane.
- **Instancyjne** – dostępne wewnątrz całego obiektu klasy.
- **Statyczne** – dostępne w całej klasie, dzielone między wszystkimi obiektami.

---

## Przekazywanie przez wartość (Pass by Value)

Java zawsze przekazuje argumenty przez wartość:

- Dla **typów prymitywnych** – przekazywana jest kopia wartości. Zmiany wewnątrz metody nie wpływają na oryginalną
zmienną.
- Dla **typów referencyjnych** – przekazywana jest kopia referencji (adresu). Modyfikacja **stanu obiektu** wewnątrz
metody jest widoczna na zewnątrz, ale przypisanie nowej wartości do zmiennej referencyjnej nie zmienia oryginalnego
obiektu.

```java
void zmienPrymityw(int x) {
    x = 99; // nie wpływa na oryginał
}

void zmienObiekt(int[] arr) {
    arr[0] = 99;  // wpływa na oryginał – modyfikujemy stan obiektu
    arr = new int[]{1, 2, 3};  // nie wpływa na oryginał – nowa referencja lokalna
}
```

---

## VAR

Od Javy 10 istnieje możliwość używania `var` do deklarowania zmiennych lokalnych z automatycznym określeniem ich typu
(wnioskowanie typów, ang. *type inference*). Poprawia czytelność kodu, szczególnie przy długich typach generycznych.

```java
var liczba = 5;                          // int
var lista = new ArrayList<String>();     // ArrayList<String>
```

**Ograniczenia `var`:**

- Działa **tylko dla zmiennych lokalnych** – nie można używać dla zmiennych instancyjnych ani statycznych.
- Typ musi być **jednoznacznie określony podczas inicjalizacji** – kompilator musi od razu wiedzieć, jaki to typ.
- Nie można używać `var` bez inicjalizacji.

```java
var x = 5;   // ✅ działa – typ to int
var y;       // ❌ błąd kompilacji – brak inicjalizacji, typ nieznany
```

> `var` nie jest nowym typem – to tylko skrót dla kompilatora. W czasie działania programu zmienna nadal ma konkretny typ.
> 

---

## Stałe (`final`)

Stałe to zmienne, których wartość nie może zostać zmieniona po pierwszym przypisaniu. Definiuje się je słowem kluczowym
`final`.

```java
final int MAX_VALUE = 100;
```

Stałe klasowe (dostępne bez tworzenia obiektu) definiujemy jako `public static final`:

```java
public class MyClass {
    public static final int MAX_SIZE = 50;
    public static final String DEFAULT_NAME = "Java";
}
```

**Ciekawostka:** jeśli stała jest obliczana w momencie kompilacji (np. `static final int X = 10 + 20;`), kompilator
"wstawia" jej wartość bezpośrednio do kodu (ang. *constant folding*). Jeśli jest obliczana w trakcie działania
programu, wczytywana jest przy pierwszym użyciu.

### `final` a niezmienność obiektów

`final` dla zmiennych referencyjnych oznacza, że **nie można zmienić referencji** (wskazania na obiekt), ale **stan
samego obiektu można modyfikować**:

```java
final List<String> lista = new ArrayList<>();
lista.add("element");        // ✅ możliwe – modyfikujemy stan obiektu
lista = new ArrayList<>();   // ❌ błąd kompilacji – próba zmiany referencji
```

Jeśli chcemy obiektu całkowicie niezmiennego (ang. *immutable*), musimy użyć klas niemutowalnych. Przykładem jest
`String` – jest klasą `final`, której obiekty są immutable by design. Każda operacja na `String` tworzy nowy obiekt:

```java
String tekst = "Hello";
tekst = tekst.concat(" World"); // Tworzony jest nowy obiekt; oryginał "Hello" pozostaje niezmieniony
```

---

## Modyfikatory dostępu

Modyfikatory dostępu określają, skąd dana zmienna (lub metoda) może być używana:

| Modyfikator | Klasa | Pakiet | Podklasa | Wszędzie |
| --- | --- | --- | --- | --- |
| `public` | ✅ | ✅ | ✅ | ✅ |
| `protected` | ✅ | ✅ | ✅ | ❌ |
| *(brak, `package`)* | ✅ | ✅ | ❌ | ❌ |
| `private` | ✅ | ❌ | ❌ | ❌ |

---

## Shadowing (zaciemnianie zmiennych)

Shadowing polega na tym, że zmienna lokalna przesłania zmienną instancyjną lub statyczną o tej samej nazwie.
Do zmiennej instancyjnej możemy odwołać się przez `this`:

```java
public class Przyklad {
    int liczba = 10;

    public void metoda() {
        int liczba = 20;              // Zmienna lokalna przesłania instancyjną
        System.out.println(liczba);       // Wyświetli: 20
        System.out.println(this.liczba);  // Wyświetli: 10
    }
}
```

---

## `volatile`

`volatile` stosuje się w programowaniu wielowątkowym. Deklaracja zmiennej jako `volatile` zapewnia, że jej wartość
będzie zawsze odczytywana bezpośrednio z **pamięci głównej (RAM)**, a nie z lokalnego cache procesora. Dzięki temu
wszystkie wątki zawsze widzą aktualną wartość zmiennej.

```java
public class Przyklad {
    private volatile boolean flaga = true;

    public void zmienFlage() {
        flaga = false; // Zmiana natychmiast widoczna dla innych wątków
    }
}
```

---

## `transient`

`transient` wyklucza zmienną z **serializacji** – procesu zapisywania stanu obiektu do pliku lub przesyłania przez
sieć. Przydatne np. dla haseł, danych tymczasowych lub obiektów, których nie da się serializować.

```java
public class Uzytkownik implements Serializable {
    String nazwa;
    transient String haslo;  // Nie zostanie zapisane podczas serializacji
}
```

Po deserializacji (wczytaniu obiektu) zmienna `transient` przyjmie wartość domyślną (`null` dla obiektów, `0` dla
liczb itd.).
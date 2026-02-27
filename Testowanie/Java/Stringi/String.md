# String

# String w Javie

`String` to klasa służąca do reprezentowania ciągów znaków. Stringi są obiektami – wszystkie napisy (np. `"Hello"`)
są instancjami klasy `String`. Java stosuje specjalne mechanizmy optymalizacji, ponieważ stringi są wyjątkowo
często używane.

---

## 1. Kluczowe cechy

### Niezmienność (Immutability)

Obiekty `String` są **niemutowalne** – raz utworzony obiekt nie może być zmodyfikowany. Operacje takie jak
konkatenacja tworzą **nowy obiekt** zamiast modyfikować istniejący:

```java
String s = "Hello";
s = s + " World"; // Tworzony jest nowy obiekt; "Hello" nadal istnieje w pamięci
```

### Pula Stringów (String Pool)

Java optymalizuje pamięć poprzez przechowywanie literałów `String` w specjalnej strukturze zwanej **pulą stringów**
(część Heap). Gdy tworzysz napis przez literał, Java sprawdza czy identyczny tekst już istnieje w puli –
jeśli tak, zwraca istniejący obiekt zamiast tworzyć nowy.

```java
String a = "hello";          // trafia do puli
String b = "hello";          // zwraca ten sam obiekt z puli
String c = new String("hello"); // ZAWSZE tworzy nowy obiekt poza pulą

System.out.println(a == b);        // true  – ten sam obiekt z puli
System.out.println(a == c);        // false – c jest poza pulą
System.out.println(a.equals(c));   // true  – ta sama wartość
```

> **Zasada:** do porównywania zawartości stringów zawsze używaj `.equals()`, nigdy `==`.
> 

### `intern()` – ręczne dodanie do puli

Metoda `intern()` zwraca referencję do obiektu z puli (lub dodaje do niej string jeśli jeszcze nie istnieje):

```java
String c = new String("hello");
String d = c.intern();       // d wskazuje na obiekt z puli

System.out.println(a == d);  // true – d pochodzi z puli, tak jak a
```

---

## 2. Tworzenie Stringów

```java
String greeting = "Hello";                  // literał – korzysta z puli
String name = new String("Alice");           // nowy obiekt poza pulą – unikaj
String empty = "";                           // pusty string
String fromChars = String.valueOf(42);       // konwersja int → String: "42"
String fromBool = String.valueOf(true);      // konwersja boolean → String: "true"
```

---

## 3. String Block (Java 15+)

Wprowadzony eksperymentalnie w Javie 13, dostępny oficjalnie od **Javy 15**. Umożliwia wygodne tworzenie
wieloliniowych stringów bez `\\n` i `+`. Używa potrójnych cudzysłowów `"""`:

```java
String json = """
        {
            "name": "Alice",
            "age": 30
        }
        """;

String sql = """
        SELECT *
        FROM users
        WHERE age > 18
        """;
```

Wcięcie jest automatycznie przycinane na podstawie pozycji zamykającego `"""`.

---

## 4. Metody klasy String

### Podstawowe informacje

| Metoda | Opis |
| --- | --- |
| `length()` | Zwraca długość łańcucha (liczbę znaków) |
| `isEmpty()` | `true` jeśli długość == 0 |
| `isBlank()` | `true` jeśli pusty lub zawiera tylko białe znaki (Java 11+) |
| `charAt(int index)` | Zwraca znak na podanej pozycji |

### Wyszukiwanie

| Metoda | Opis |
| --- | --- |
| `indexOf(String str)` | Indeks pierwszego wystąpienia podanego ciągu (-1 jeśli brak) |
| `lastIndexOf(String str)` | Indeks ostatniego wystąpienia |
| `contains(CharSequence seq)` | Czy łańcuch zawiera podany ciąg |
| `startsWith(String prefix)` | Czy zaczyna się od podanego prefiksu |
| `endsWith(String suffix)` | Czy kończy się podanym sufiksem |

### Pobieranie fragmentów

| Metoda | Opis |
| --- | --- |
| `substring(int beginIndex)` | Podłańcuch od indeksu do końca |
| `substring(int begin, int end)` | Podłańcuch od `begin` do `end` (bez `end`) |
| `subSequence(int begin, int end)` | Jak `substring`, zwraca `CharSequence` |

### Modyfikacje (zwracają nowy obiekt)

| Metoda | Opis |
| --- | --- |
| `toUpperCase()` | Wszystkie znaki na wielkie litery |
| `toLowerCase()` | Wszystkie znaki na małe litery |
| `trim()` | Usuwa białe znaki z początku i końca (stary sposób) |
| `strip()` | Jak `trim()`, ale obsługuje Unicode (Java 11+) |
| `stripLeading()` | Usuwa białe znaki tylko z początku (Java 11+) |
| `stripTrailing()` | Usuwa białe znaki tylko z końca (Java 11+) |
| `replace(CharSequence target, CharSequence replacement)` | Zastępuje wszystkie wystąpienia |
| `replaceAll(String regex, String replacement)` | Zastępuje wg wyrażenia regularnego |
| `replaceFirst(String regex, String replacement)` | Zastępuje pierwsze wystąpienie wg regex |
| `concat(String str)` | Dołącza ciąg na końcu (odpowiednik `+`) |
| `repeat(int count)` | Powtarza łańcuch n razy (Java 11+) |
| `indent(int n)` | Dodaje/usuwa wcięcia w tekście wieloliniowym (Java 12+) |

### Porównywanie

| Metoda | Opis |
| --- | --- |
| `equals(Object obj)` | Porównuje wartości (wielkość liter ma znaczenie) |
| `equalsIgnoreCase(String str)` | Porównuje wartości, ignoruje wielkość liter |
| `compareTo(String str)` | Porównanie leksykograficzne (zwraca int) |
| `compareToIgnoreCase(String str)` | Jak `compareTo`, ignoruje wielkość liter |

### Dzielenie i łączenie

| Metoda | Opis |
| --- | --- |
| `split(String regex)` | Dzieli na tablicę wg wyrażenia regularnego |
| `split(String regex, int limit)` | Jak wyżej, z limitem elementów tablicy |
| `join(CharSequence delimiter, CharSequence... elements)` | Łączy elementy separatorem (metoda statyczna) |

### Metody przyjmujące wyrażenia regularne (regex)

| Metoda | Opis |
| --- | --- |
| `matches(String regex)` | Sprawdza czy **cały** string pasuje do wzorca (odpowiednik `Pattern.matches()`) |
| `replaceAll(String regex, String replacement)` | Zastępuje wszystkie dopasowania wg wzorca |
| `replaceFirst(String regex, String replacement)` | Zastępuje pierwsze dopasowanie wg wzorca |
| `split(String regex)` | Dzieli string wg wzorca |

```java
"12345".matches("\\\\d+");          // true  – cały string to cyfry
"hello123".matches("\\\\d+");       // false – nie cały string to cyfry
"hello123".matches(".*\\\\d+");     // true  – .* dopasowuje "hello"

"a1b2c3".replaceAll("\\\\d", "#");  // "a#b#c#"
"a1b2c3".replaceFirst("\\\\d", "#"); // "a#b2c3"
"a,b,,c".split(",");              // ["a", "b", "", "c"]
"a,b,,c".split(",", -1);          // ["a", "b", "", "c"] (zachowuje puste)
```

> Metody `replaceAll`, `replaceFirst` i `split` używają wyrażeń regularnych.
Szczegółowy opis składni regex → **osobna notatka: `java_regex.md`**
> 

### Konwersja i formatowanie

| Metoda | Opis |
| --- | --- |
| `valueOf(Object obj)` | Konwersja dowolnego typu na `String` (metoda statyczna) |
| `format(String format, Object... args)` | Formatuje string jak `printf` (metoda statyczna) |
| `toCharArray()` | Zwraca tablicę znaków `char[]` |
| `chars()` | Zwraca `IntStream` znaków (Java 8+) |

```java
// Przykłady
String.valueOf(42);           // "42"
String.valueOf(3.14);         // "3.14"
String.valueOf(true);         // "true"

String.join(", ", "a", "b", "c");  // "a, b, c"
String.format("Mam %d lat", 25);   // "Mam 25 lat"

"abc".chars()
     .forEach(c -> System.out.print((char) c)); // a b c
```

---

## 5. printf i String.format

`printf` służy do formatowanego wypisywania tekstu. `String.format` działa identycznie, ale zwraca String
zamiast wypisywać go na konsolę.

```java
System.out.printf(format, arg1, arg2, ...);
String wynik = String.format(format, arg1, arg2, ...);
```

### Pełna składnia specyfikatora formatu

```
%[argument_index$][flagi][szerokość][.precyzja]konwersja
```

| Element | Opis |
| --- | --- |
| `%` | Rozpoczyna specyfikator formatu |
| `argument_index$` | Opcjonalny numer argumentu (np. `1$`, `2$`) – do użycia argumentów w innej kolejności |
| `flagi` | Modyfikują sposób formatowania |
| `szerokość` | Minimalna liczba znaków wyniku |
| `.precyzja` | Miejsca po przecinku (float) lub maks. długość (String) |
| `konwersja` | Typ danych do sformatowania |

### Specyfikatory konwersji

| Specyfikator | Typ | Przykład wejścia | Wynik |
| --- | --- | --- | --- |
| `d` | liczba całkowita | `123` | `123` |
| `f` | liczba zmiennoprzecinkowa | `3.1415` | `3.14` (z `.2f`) |
| `s` | String | `"tekst"` | `tekst` |
| `c` | char | `'A'` | `A` |
| `b` | boolean | `true` | `true` |
| `x` / `X` | szesnastkowy | `255` | `ff` / `FF` |
| `o` | ósemkowy | `8` | `10` |
| `e` / `E` | notacja naukowa | `123456.789` | `1.23e+05` |
| `n` | nowa linia (zależna od OS) | – | – |
| `%` | znak procenta | – | `%` |

> Używaj `%n` zamiast `\\n` w `printf` – `%n` automatycznie dobiera właściwy znak nowej linii dla OS.
> 

### Flagi

| Flaga | Opis | Przykład |
| --- | --- | --- |
| `-` | Wyrównanie do lewej | `%-10s` |
| `+` | Zawsze pokazuj znak | `%+d` → `+42` |
| `0` | Dopełnienie zerami | `%05d` → `00042` |
| `,` | Separator tysięcy | `%,d` → `1,000` |
| `(` | Liczby ujemne w nawiasach | `%(f` → `(3.14)` |
| `#` | Prefix dla hex/octal | `%#x` → `0xff` |
|  | Spacja przed liczbami dodatnimi | `% d` →  `42` |

### Przykłady

```java
// argument_index – użycie argumentów w innej kolejności
System.out.printf("%2$s ma %1$d lat%n", 25, "Alice");  // "Alice ma 25 lat"

// szerokość i wyrównanie
System.out.printf("%-10s|%10s%n", "lewo", "prawo");
// "lewo      |     prawo"

// precyzja
System.out.printf("%.2f%n", 3.14159);   // "3.14"
System.out.printf("%.5s%n", "Hello World"); // "Hello" (max 5 znaków)

// flagi
System.out.printf("%,d%n", 1000000);    // "1,000,000"
System.out.printf("%010.2f%n", 3.14);   // "0000003.14"
```

---

## 6. Pułapka wydajnościowa – konkatenacja w pętli

Konkatenacja `String` w pętli tworzy nowy obiekt przy każdej iteracji – dla dużej liczby operacji
jest to bardzo nieefektywne:

```java
// ŹLE – przy 10 000 iteracjach tworzy 10 000 obiektów String
String wynik = "";
for (int i = 0; i < 10_000; i++) {
    wynik += i;  // każdy += tworzy nowy obiekt
}

// DOBRZE – StringBuilder modyfikuje ten sam obiekt
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 10_000; i++) {
    sb.append(i);
}
String wynik = sb.toString();
```

---

## 7. StringBuilder

`StringBuilder` to mutowalna klasa do efektywnej manipulacji ciągami znaków. W odróżnieniu od `String`,
modyfikacje **nie tworzą nowych obiektów**.

```java
StringBuilder sb = new StringBuilder("Hello");
sb.append(" World");
System.out.println(sb.toString());  // "Hello World"
```

### Pojemność (capacity)

`StringBuilder` ma wewnętrzny bufor. Domyślna pojemność to **16 znaków + długość inicjalnego stringa**.
Gdy bufor jest za mały, automatycznie się rozrasta (podwaja). Można podać pojemność z góry aby uniknąć
realokacji:

```java
StringBuilder sb = new StringBuilder(256); // bufor na 256 znaków od razu
System.out.println(sb.capacity());         // 256
```

### Metody StringBuilder

| Metoda | Opis |
| --- | --- |
| `append(String str)` | Dodaje tekst na końcu |
| `insert(int offset, String str)` | Wstawia tekst na określonej pozycji |
| `delete(int start, int end)` | Usuwa znaki od `start` do `end` |
| `deleteCharAt(int index)` | Usuwa znak na podanym indeksie |
| `replace(int start, int end, String str)` | Zastępuje fragment podanym tekstem |
| `reverse()` | Odwraca kolejność znaków |
| `toString()` | Konwertuje do `String` |
| `length()` | Zwraca aktualną długość |
| `capacity()` | Zwraca aktualną pojemność bufora |
| `setLength(int newLength)` | Ustawia długość (skraca lub dopełnia `\\0`) |
| `charAt(int index)` | Zwraca znak na podanej pozycji |
| `indexOf(String str)` | Indeks pierwszego wystąpienia |

```java
StringBuilder sb = new StringBuilder("Hello World");
sb.reverse();                     // "dlroW olleH"
sb.delete(0, 5);                  // "olleH"  (usuwa od 0 do 4)
sb.insert(0, "Say ");             // "Say olleH"
sb.replace(4, 9, "Hello");        // "Say Hello"
sb.deleteCharAt(3);               // "SayHello"
System.out.println(sb.toString());
```

---

## 8. String vs StringBuilder vs StringBuffer

| Cecha | `String` | `StringBuilder` | `StringBuffer` |
| --- | --- | --- | --- |
| Mutowalność | ❌ niemutowalny | ✅ mutowalny | ✅ mutowalny |
| Bezpieczeństwo wątkowe | ✅ (niemutowalny) | ❌ nie | ✅ tak (synchronizowany) |
| Wydajność | niska przy wielu zmianach | ✅ wysoka | niższa niż StringBuilder |
| Kiedy używać | stałe teksty, mało zmian | pętle, jeden wątek | wiele wątków |

```java
// StringBuilder – szybki, jeden wątek
StringBuilder sb = new StringBuilder();
sb.append("Hello").append(" ").append("World");

// StringBuffer – wolniejszy, ale bezpieczny wielowątkowo
StringBuffer sbf = new StringBuffer();
sbf.append("Hello").append(" World");
```

> W praktyce `StringBuffer` jest rzadko potrzebny – synchronizację lepiej zarządzać na wyższym poziomie.
Domyślnym wyborem powinien być `StringBuilder`.
>
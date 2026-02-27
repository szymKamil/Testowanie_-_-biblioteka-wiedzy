# RegExp

# Wyrażenia regularne (RegExp) w Javie

Wyrażenia regularne (regex) to wzorce tekstowe do wyszukiwania, dopasowywania i manipulowania ciągami znaków.
W Javie obsługuje je pakiet `java.util.regex` z klasami `Pattern` i `Matcher`.

Dokumentacja: https://docs.oracle.com/javase/8/docs/api/java/util/regex/Pattern.html

---

## 1. Szybki start

```java
import java.util.regex.Pattern;
import java.util.regex.Matcher;

String text = "Java 17 jest lepsza niż Java 8!";
Pattern pattern = Pattern.compile("Java\\s\\d+");
Matcher matcher = pattern.matcher(text);

while (matcher.find()) {
    System.out.println(matcher.group()); // "Java 17", "Java 8"
}
```

Skrócony zapis przez `String.matches()` (sprawdza **cały** string):

```java
"12345".matches("\\d+");    // true
"abc123".matches("\\d+");   // false – nie cały string to cyfry
"abc123".matches(".*\\d+"); // true  – .* dopasowuje "abc"
```

---

## 2. Składnia – tabela wzorców

### Klasy znaków

| Wzorzec | Opis |
| --- | --- |
| `.` | Dowolny znak (poza nową linią, chyba że flaga `DOTALL`) |
| `\d` | Cyfra – odpowiednik `[0-9]` |
| `\D` | Nie-cyfra – odpowiednik `[^0-9]` |
| `\w` | Znak alfanumeryczny lub `_` – odpowiednik `[a-zA-Z0-9_]` |
| `\W` | Znak nie-alfanumeryczny |
| `\s` | Biała spacja (spacja, tabulator, nowa linia) |
| `\S` | Znak niebędący białą spacją |

> W Javie w stringu literalnym backslash wymaga podwojenia: `\d` → `"\\d"`
> 

### Zakresy i zestawy znaków `[...]`

| Wzorzec | Opis |
| --- | --- |
| `[abc]` | Dowolny ze znaków: a, b lub c |
| `[a-z]` | Małe litery od a do z |
| `[A-Z]` | Wielkie litery od A do Z |
| `[0-9]` | Cyfry od 0 do 9 |
| `[a-zA-Z]` | Dowolna litera |
| `[a-zA-Z0-9]` | Znak alfanumeryczny |
| `[^abc]` | Negacja – wszystko **poza** a, b, c |
| `[^a-z]` | Wszystko poza małymi literami |
| `[a-f0-3]` | Litery a-f lub cyfry 0-3 |

```java
"[a-zA-Z]+".matches part of "Ala ma kota"  // dopasuje słowa
"[^a-zA-Z\\s]" // wszystko poza literami i spacjami (np. cyfry, interpunkcja)
```

### Kwantyfikatory (ilościowe)

| Wzorzec | Opis |
| --- | --- |
| `*` | 0 lub więcej razy |
| `+` | 1 lub więcej razy |
| `?` | 0 lub 1 raz (opcjonalny) |
| `{n}` | Dokładnie n razy |
| `{n,}` | Co najmniej n razy |
| `{n,m}` | Od n do m razy |

### Chciwe vs leniwe kwantyfikatory

Domyślnie kwantyfikatory są **chciwe** (greedy) – dopasowują jak najwięcej znaków.
Dodanie `?` po kwantyfikatorze czyni go **leniwym** (lazy) – dopasowuje jak najmniej:

```java
String html = "<b>pogrubiony</b> i <i>kursywa</i>";

// Chciwy – dopasuje od pierwszego < do ostatniego >
Pattern.compile("<.+>").matcher(html).find();   // "<b>pogrubiony</b> i <i>kursywa</i>"

// Leniwy – dopasuje jak najkrótszy fragment
Pattern.compile("<.+?>").matcher(html).find();  // "<b>"
```

| Chciwy | Leniwy | Opis |
| --- | --- | --- |
| `*` | `*?` | 0 lub więcej, jak najmniej |
| `+` | `+?` | 1 lub więcej, jak najmniej |
| `?` | `??` | 0 lub 1, jak najmniej |
| `{n,m}` | `{n,m}?` | Od n do m, jak najmniej |

### Kotwice (Anchors)

| Wzorzec | Opis |
| --- | --- |
| `^` | Początek linii (lub całego stringa) |
| `$` | Koniec linii (lub całego stringa) |
| `\b` | Granica słowa (word boundary) |
| `\B` | Nie-granica słowa |

```java
"^Java"    // "Java" tylko na początku
"\\d+$"    // cyfry tylko na końcu
"\\bcat\\b" // słowo "cat" (nie "catch" ani "concatenate")
```

### Grupy

| Wzorzec | Opis |
| --- | --- |
| `(abc)` | Grupa przechwytująca – zapamiętuje dopasowanie |
| `(?:abc)` | Grupa nieprzechwytująca – tylko grupuje, nie zapamiętuje |
| `(?<name>abc)` | Nazwana grupa przechwytująca (Java 7+) |
| `\1`, `\2` | Odwołanie wsteczne do grupy 1, 2 (backreference) |
| `(a|b)` | Alternatywa – a lub b |

```java
// Grupy przechwytujące
Pattern p = Pattern.compile("(\\d{4})-(\\d{2})-(\\d{2})");
Matcher m = p.matcher("Data: 2024-01-15");
if (m.find()) {
    System.out.println(m.group(1)); // "2024" – rok
    System.out.println(m.group(2)); // "01"   – miesiąc
    System.out.println(m.group(3)); // "15"   – dzień
}

// Nazwana grupa (Java 7+)
Pattern p2 = Pattern.compile("(?<rok>\\d{4})-(?<miesiac>\\d{2})-(?<dzien>\\d{2})");
Matcher m2 = p2.matcher("2024-01-15");
if (m2.find()) {
    System.out.println(m2.group("rok"));     // "2024"
    System.out.println(m2.group("miesiac")); // "01"
}

// Grupa nieprzechwytująca – grupuje ale nie numeruje
Pattern p3 = Pattern.compile("(?:Java|Python)\\s\\d+");
// group(1) nie istnieje – nie ma numerowanej grupy
```

### Asercje wyprzedzające i wsteczne (Lookahead / Lookbehind)

Sprawdzają kontekst bez "konsumowania" znaków – dopasowanie nie zawiera tych znaków:

| Wzorzec | Nazwa | Opis |
| --- | --- | --- |
| `(?=abc)` | Positive lookahead | To co następuje = abc |
| `(?!abc)` | Negative lookahead | To co następuje ≠ abc |
| `(?<=abc)` | Positive lookbehind | To co poprzedza = abc |
| `(?<!abc)` | Negative lookbehind | To co poprzedza ≠ abc |

```java
// Znajdź liczby poprzedzone "cena: "
Pattern.compile("(?<=cena: )\\d+").matcher("cena: 150 zł").find(); // "150"

// Znajdź słowa NIE kończące się na "ing"
Pattern.compile("\\b\\w+(?!ing)\\b");

// Znajdź cyfry przed "zł"
Pattern.compile("\\d+(?= zł)").matcher("150 zł").find(); // "150"
```

### Ucieczka znaków specjalnych

Znaki specjalne w regex: `. * + ? ^ $ { } [ ] | ( ) \`
Aby dopasować je dosłownie, użyj `\` (w Javie `\\`):

```java
"3\\.14"     // dopasuje "3.14" (kropka dosłowna, nie "dowolny znak")
"\\$\\d+"    // dopasuje "$100"
Pattern.quote("a.b+c") // automatycznie escapuje cały string
```

---

## 3. Klasy `Pattern` i `Matcher`

### Pattern

`Pattern` reprezentuje skompilowane wyrażenie regularne. Kompilacja jest kosztowna – warto przechowywać
obiekt `Pattern` jako stałą gdy używasz go wielokrotnie:

```java
// Dobrze – kompilacja raz, użycie wiele razy
private static final Pattern EMAIL = Pattern.compile("[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\\.[a-zA-Z]{2,}");
```

| Metoda | Opis |
| --- | --- |
| `Pattern.compile(String regex)` | Kompiluje wzorzec |
| `Pattern.compile(String regex, int flags)` | Kompiluje z flagami |
| `Pattern.matches(String regex, CharSequence input)` | Sprawdza czy **cały** input pasuje (metoda statyczna) |
| `Pattern.quote(String s)` | Escapuje string do użycia jako dosłowny wzorzec |
| `pattern.matcher(CharSequence input)` | Tworzy `Matcher` dla danego tekstu |
| `pattern.split(CharSequence input)` | Dzieli tekst wg wzorca |
| `pattern.pattern()` | Zwraca oryginalny string wzorca |

### Matcher

`Matcher` wykonuje operacje dopasowywania na konkretnym tekście:

| Metoda | Opis |
| --- | --- |
| `find()` | Szuka następnego dopasowania, zwraca `true/false` |
| `find(int start)` | Szuka od podanej pozycji |
| `matches()` | Sprawdza czy **cały** tekst pasuje do wzorca |
| `lookingAt()` | Sprawdza czy **początek** tekstu pasuje |
| `group()` | Zwraca ostatnie dopasowanie |
| `group(int n)` | Zwraca dopasowanie n-tej grupy |
| `group(String name)` | Zwraca dopasowanie nazwanej grupy |
| `groupCount()` | Liczba grup przechwytujących we wzorcu |
| `start()` | Indeks początku ostatniego dopasowania |
| `end()` | Indeks końca ostatniego dopasowania |
| `replaceAll(String replacement)` | Zastępuje wszystkie dopasowania |
| `replaceFirst(String replacement)` | Zastępuje pierwsze dopasowanie |
| `reset()` | Resetuje stan matchera |
| `reset(CharSequence input)` | Resetuje z nowym tekstem |

### `find()` vs `matches()` vs `lookingAt()`

```java
Pattern p = Pattern.compile("\\d+");
Matcher m = p.matcher("abc 123 def");

m.find();       // true  – znajdzie "123" gdziekolwiek w tekście
m.matches();    // false – cały tekst "abc 123 def" nie składa się tylko z cyfr
m.lookingAt();  // false – tekst nie zaczyna się od cyfr

Matcher m2 = p.matcher("123 abc");
m2.lookingAt(); // true  – tekst ZACZYNA się od cyfr
```

---

## 4. Flagi kompilacji

Flagi modyfikują zachowanie wyrażenia regularnego:

| Flaga (stała) | Skrót inline | Opis |
| --- | --- | --- |
| `Pattern.CASE_INSENSITIVE` | `(?i)` | Ignoruje wielkość liter (ASCII) |
| `Pattern.UNICODE_CASE` | `(?u)` | Ignoruje wielkość liter (Unicode) |
| `Pattern.MULTILINE` | `(?m)` | `^` i `$` dopasowują początek/koniec **każdej linii** |
| `Pattern.DOTALL` | `(?s)` | `.` dopasowuje też znaki nowej linii |
| `Pattern.COMMENTS` | `(?x)` | Ignoruje białe znaki i komentarze (`#`) w wzorcu |
| `Pattern.LITERAL` | – | Traktuje wzorzec dosłownie (jak `Pattern.quote`) |

```java
// Przez stałą
Pattern p1 = Pattern.compile("java", Pattern.CASE_INSENSITIVE);
p1.matcher("Java 17").find(); // true

// Łączenie flag
Pattern p2 = Pattern.compile("^\\d+$",
    Pattern.MULTILINE | Pattern.CASE_INSENSITIVE);

// Inline – bezpośrednio w wzorcu
Pattern p3 = Pattern.compile("(?i)java");  // ignoruje wielkość liter

// COMMENTS – czytelny wzorzec z komentarzami
Pattern email = Pattern.compile("""
    (?x)
    [a-zA-Z0-9._%+-]+  # lokalna część
    @                   # małpa
    [a-zA-Z0-9.-]+     # domena
    \\.                 # kropka
    [a-zA-Z]{2,}       # TLD
""");
```

---

## 5. Przykłady praktyczne

### Wyszukiwanie wszystkich dopasowań

```java
String text = "Liczby: 123, 456, 789.";
Pattern pattern = Pattern.compile("\\d+");
Matcher matcher = pattern.matcher(text);

while (matcher.find()) {
    System.out.printf("Znaleziono: %s (pozycja %d-%d)%n",
        matcher.group(), matcher.start(), matcher.end());
}
// Znaleziono: 123 (pozycja 8-11)
// Znaleziono: 456 (pozycja 13-16)
// Znaleziono: 789 (pozycja 18-21)
```

### Walidacja e-maila

```java
private static final Pattern EMAIL_PATTERN = Pattern.compile(
    "[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\\.[a-zA-Z]{2,}"
);

boolean isValidEmail(String email) {
    return EMAIL_PATTERN.matcher(email).matches();
}
```

### Walidacja numeru telefonu

```java
Pattern tel = Pattern.compile("^(\\+48)?[\\s-]?(\\d{3}[\\s-]?){3}$");
tel.matcher("+48 123 456 789").matches(); // true
tel.matcher("123-456-789").matches();     // true
```

### Wyodrębnianie grup (data)

```java
Pattern data = Pattern.compile("(?<rok>\\d{4})-(?<miesiac>\\d{2})-(?<dzien>\\d{2})");
Matcher m = data.matcher("Dziś jest 2024-01-15.");
if (m.find()) {
    System.out.println("Rok: "     + m.group("rok"));
    System.out.println("Miesiąc: " + m.group("miesiac"));
    System.out.println("Dzień: "   + m.group("dzien"));
}
```

### Zastępowanie z grupami (replace z odwołaniem)

```java
// Zamiana formatu daty z YYYY-MM-DD na DD.MM.YYYY
String data = "2024-01-15";
String wynik = data.replaceAll(
    "(\\d{4})-(\\d{2})-(\\d{2})",
    "$3.$2.$1"  // $1 = rok, $2 = miesiąc, $3 = dzień
);
System.out.println(wynik); // "15.01.2024"

// Z nazwaną grupą
String wynik2 = data.replaceAll(
    "(?<rok>\\d{4})-(?<miesiac>\\d{2})-(?<dzien>\\d{2})",
    "${dzien}.${miesiac}.${rok}"
);
```

### Dzielenie tekstu

```java
// Podział po przecinku i/lub spacjach
"jeden, dwa,  trzy".split("[,\\s]+"); // ["jeden", "dwa", "trzy"]

// Podział z zachowaniem separatora (użycie lookahead)
"a1b2c3".split("(?=\\d)"); // ["a", "1b", "2c", "3"]
```

---

## 6. Najczęstsze pułapki

**`matches()` vs `find()`** – `matches()` sprawdza **cały** string, `find()` szuka dopasowania **gdziekolwiek**:

```java
Pattern p = Pattern.compile("\\d+");
p.matcher("abc123").matches(); // false – cały string nie to cyfry
p.matcher("abc123").find();    // true  – cyfry są gdzieś w tekście
```

**Chciwe kwantyfikatory** – mogą dawać nieoczekiwane wyniki przy HTML/XML:

```java
// Chciwy – "zje" cały string między pierwszym < a ostatnim >
"<.+>".matches "<div>tekst</div>" → "<div>tekst</div>" (cały!)
// Rozwiązanie: użyj leniwego <.+?>
```

**Wydajność – kompiluj Pattern raz:**

```java
// Źle – Pattern kompilowany przy każdym wywołaniu metody
boolean validate(String s) {
    return s.matches("\\d{10}"); // kompiluje wzorzec za każdym razem!
}

// Dobrze
private static final Pattern PATTERN = Pattern.compile("\\d{10}");
boolean validate(String s) {
    return PATTERN.matcher(s).matches();
}
```

**`String.matches()` a pełne dopasowanie:**

```java
"123abc".matches("\\d+");    // false – matches() wymaga dopasowania CAŁEGO stringa
"123abc".matches("\\d+.*");  // true  – .* dopasowuje resztę
```
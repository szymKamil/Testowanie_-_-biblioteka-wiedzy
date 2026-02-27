# Prymitywne typy danych

W języku Java istnieją dwa podstawowe rodzaje typów danych: **typy proste (prymitywne)** oraz **typy złożone (referencyjne)**.

![img.png](Prymitywne%20typy%20danych/img.png)

img.png

## 1. **Typy proste (prymitywne)**

Są to najbardziej podstawowe typy danych, które przechowują rzeczywiste wartości. W Java mamy osiem typów prostych:

**Liczbowe całkowitoliczbowe:**

- `byte`: 8-bitowy typ, zakres od -128 do 127.
- `short`: 16-bitowy typ, zakres od -32 768 do 32 767.
- `int`: 32-bitowy typ, zakres od -2^31 do 2^31 - 1.
- `long`: 64-bitowy typ, zakres od -2^63 do 2^63 - 1.

Java domyślnie interpretuje liczby zmiennoprzecinkowe jako double, a całkowitoliczbowe jako int. Warto podać przykład, np.: aby przypisać wartość float, należy dopisać f na końcu liczby (float f = 3.14f;), a dla long – L (long l = 10000000000L;).

**Liczbowe zmiennoprzecinkowe:**

- `float`: 32-bitowy typ zmiennoprzecinkowy (pojedyncza precyzja).
- `double`: 64-bitowy typ zmiennoprzecinkowy (podwójna precyzja).

Float i double stosują notację IEEE 754, gdzie float oferuje około 7 cyfr precyzji, a double – około 15-16 cyfr. Dzięki temu jasne jest, dlaczego np. double jest lepszy do bardziej złożonych obliczeń matematycznych.

W Javie można używać znaku podkreślenia (_) w zapisach liczbowych, aby poprawić czytelność kodu, zwłaszcza gdy pracujemy z długimi liczbami. Podkreślenie nie wpływa na wartość liczby i jest ignorowane podczas kompilacji — służy wyłącznie do ułatwienia interpretacji.

*Zasady użycia znaku podkreślenia:*
- Można umieszczać `_` w dowolnym miejscu między cyframi liczby, aby łatwiej ją odczytać.
- Podkreślenie nie może znajdować się na początku lub końcu liczby, ani obok symboli takich jak `L, l, F, f` (oznaczających `long`, `float` itd.).
- Działa dla wszystkich systemów liczbowych obsługiwanych przez Javę, czyli dziesiętnego, szesnastkowego, ósemkowego, a nawet binarnego.

```java
int RICHARD_NIXONS_SSN = 567_68_0515;      // Reprezentacja czytelna dla wzorca SSN
int for_no_reason = 1___2___3;             // Możliwe, choć raczej nietypowe
int JAVA_ID = 0xCAFE_BABE;                 // Reprezentacja szesnastkowa
long grandTotal = 40_123_456_789L;         // Długi zapis liczb w stylu 40 miliardów
```

Dlaczego warto używać `_` w liczbach?
- Poprawia czytelność kodu: Długie liczby są łatwiejsze do odczytania. Na przykład 1_000_000 szybciej rozpoznamy jako milion niż 1000000.
- Organizuje liczby szesnastkowe: Wzorce jak 0xCAFE_BABE mogą być bardziej intuicyjne dla programistów używających kodów szesnastkowych (np. w aplikacjach niskopoziomowych).

**Inne typy prymitywne:**
- `boolean`: typ logiczny, który może przyjmować wartości `true` lub `false` (0/1).
- `char`: 16-bitowy typ znakowy, który przechowuje pojedynczy znak Unicode.

# **Typy referencyjne (złożone)**

Typy referencyjne odnoszą się do obiektów, a nie do rzeczywistych wartości. Typy referencyjne mogą przechowywać obiekty, klasy, interfejsy lub tablice.

- **Klasy**: Każda zdefiniowana klasa może być typem referencyjnym. Przykładem może być klasa `String`, która reprezentuje ciąg znaków.
- **Interfejsy**: Interfejsy również mogą być typem referencyjnym, ponieważ mogą być implementowane przez klasy.
- **Tablice**: Tablice w Java to również typ referencyjny, który może przechowywać wiele wartości tego samego typu.

## Porównanie typów referencyjnych i prymitywnych w kontekście przechowywania w pamięci:

Typy prymitywne przechowują rzeczywiste wartości bezpośrednio w zmiennych, podczas gdy typy referencyjne przechowują adresy obiektów (referencje).
Typy referencyjne, w odróżnieniu od prymitywnych, mogą przyjmować wartość null, co oznacza brak referencji do jakiegokolwiek obiektu. Jest to ważne w kontekście pracy z obiektami, ponieważ null wskazuje, że referencja nie jest przypisana do żadnego obiektu.

Przykład:

```java
int liczba = 42;           // Typ prosty
String tekst = "Hello";     // Typ referencyjny (klasa String)
boolean flaga = true;       // Typ prosty
int[] liczby = {1, 2, 3};   // Typ referencyjny (tablica)
Animal animal = null;
```

Każdy z tych typów ma swoje specyficzne zastosowania i właściwości, a wybór odpowiedniego typu zależy od konkretnej sytuacji w programie.

Typy prymitywne porównujemy operatorem `==`, który sprawdza, czy wartości są identyczne. W przypadku typów referencyjnych `==` sprawdza, czy referencje wskazują na ten sam obiekt, natomiast do porównania wartości używamy metody `.equals()`.

## Domyślne wartości:

Każdy typ prymitywny ma domyślną wartość, jeśli jest deklarowany jako zmienna instancyjna, np. int i long mają domyślną wartość `0`, `boolean` – false, a char – ‘000’ (pusty znak Unicode).

W Javie (i innych językach programowania) istnieje istotna różnica między **wyrażeniem** (*expression*) a **instrukcją** (*statement*).
Wyrażenie (*expression*) zwraca wartość i można je przypisać do zmiennej, np. int x = 5 + 3;.
Instrukcja (*statement*) to działanie, które może nie zwracać wartości, np. `for` lub `if`, które są bardziej kontrolą przepływu programu niż przypisaniem wartości.

Każdy typ prymitywny ma odpowiadającą klasę opakowującą (np. Integer dla int, Double dla double). Typy opakowujące umożliwiają przechowywanie wartości prymitywnych jako obiektów, co jest szczególnie przydatne w kolekcjach, takich jak List czy Set, gdzie musimy używać typów referencyjnych.

# NaukaElementów.Autoboxing i Unboxing:

Typy prymitywne posiadają możliwość automatycznej konwersji między typami prymitywnymi a ich odpowiednikami opakowującymi, co jest znane jako autoboxing i unboxing, np. automatyczne konwertowanie int do Integer i odwrotnie w odpowiednich kontekstach.

```java
Integer num = 10;   // NaukaElementów.Autoboxing: int do Integer
int n = num;        // Unboxing: Integer do int
```

## Promowanie

W Javie podczas wykonywania operacji arytmetycznych zmienne mogą zostać promowane (czyli przejściowo zmienić swój typ na bardziej pojemny), aby operacja mogła być wykonana poprawnie. Nazywa się to promocją typu (type promotion).

```java
byte b = 42;
int i = 43;
int result = b * i; // b jest promowany do typu int przed mnożeniem
```

Zmienna b o typie byte jest automatycznie promowana do typu int, aby była zgodna z typem i. Wynik operacji b * i także ma typ int, ponieważ typ wyniku jest zgodny z najbardziej pojemnym typem użytym w wyrażeniu.
Ogólne zasady promocji typów w Javie:
Promocja do typu int: Jeśli operacje są wykonywane na typach byte, short lub char, wszystkie są promowane do int przed wykonaniem operacji.
Promocja do typu najbardziej pojemnego: Jeśli w wyrażeniu są zmienne różnych typów, promocja zachodzi do najbardziej pojemnego typu (np. int, long, float, double).

## Rzutowanie

Rzutowanie typów
Aby jawnie powiedzieć kompilatorowi, że jesteśmy świadomi tego ryzyka, stosujemy rzutowanie (ang. explicit casting). Używamy specjalnej składni (byte) (lub innego typu, do którego chcemy rzutować), aby wymusić konwersję.

```java
int i = 13;
byte b = i;         // Błąd kompilacji, ponieważ int ma większy zakres niż byte
byte b = (byte) i;  // OK, rzutowanie jawne; programista mówi kompilatorowi, że wie o konwersji
```

Przy rzutowaniu:

Kompilator nie zgłasza błędu, bo wyraźnie wskazujemy, że chcemy dokonać takiego przypisania.
W przypadku wartości większych niż zakres byte (np. int i = 128;), podczas rzutowania wynik będzie obcięty do zakresu typu docelowego, co może prowadzić do nieprzewidywalnych rezultatów.

# Typy danych w Javie

W języku Java istnieją dwa podstawowe rodzaje typów danych: **typy proste (prymitywne)** oraz **typy złożone (referencyjne)**.

---

## 1. Typy proste (prymitywne)

Są to najbardziej podstawowe typy danych, które przechowują rzeczywiste wartości. W Java mamy osiem typów prostych:

### Liczbowe całkowitoliczbowe:

- `byte`: 8-bitowy typ, zakres od -128 do 127.
- `short`: 16-bitowy typ, zakres od -32 768 do 32 767.
- `int`: 32-bitowy typ, zakres od -2^31 do 2^31 - 1.
- `long`: 64-bitowy typ, zakres od -2^63 do 2^63 - 1.

Java domyślnie interpretuje liczby całkowitoliczbowe jako `int`. Aby przypisać wartość `long`, należy dopisać `L` na końcu liczby:

```java
long l = 10000000000L;
```

### Liczbowe zmiennoprzecinkowe:

- `float`: 32-bitowy typ zmiennoprzecinkowy (pojedyncza precyzja, ~7 cyfr).
- `double`: 64-bitowy typ zmiennoprzecinkowy (podwójna precyzja, ~15-16 cyfr).

Java domyślnie interpretuje liczby zmiennoprzecinkowe jako `double`. Aby przypisać wartość `float`, należy dopisać `f`:

```java
float f = 3.14f;
```

Oba typy stosują standard **IEEE 754**. `double` jest preferowany do bardziej złożonych obliczeń matematycznych ze względu na wyższą precyzję.

### Inne typy prymitywne:

- `boolean`: typ logiczny, wartości `true` lub `false`.
- `char`: 16-bitowy typ znakowy, przechowuje pojedynczy znak Unicode (zakres `'\u0000'` do `'\uffff'`).

---

### Domyślne wartości typów prymitywnych

Domyślne wartości mają zastosowanie wyłącznie do **pól klasy (zmiennych instancyjnych)**. Zmienne lokalne **muszą** być jawnie zainicjalizowane przed użyciem — brak inicjalizacji powoduje błąd kompilacji.

| Typ | Domyślna wartość |
| --- | --- |
| `byte` | `0` |
| `short` | `0` |
| `int` | `0` |
| `long` | `0L` |
| `float` | `0.0f` |
| `double` | `0.0d` |
| `boolean` | `false` |
| `char` | `'\u0000'` |

```java
public class Przykład {
    int pole;           // OK – domyślna wartość 0

    void metoda() {
        int lokalna;
        System.out.println(lokalna); // Błąd kompilacji! Zmienna lokalna nie ma domyślnej wartości
    }
}
```

---

### Podkreślenia w literałach liczbowych

W Javie można używać znaku podkreślenia (`_`) w zapisach liczbowych, aby poprawić czytelność. Podkreślenie jest ignorowane podczas kompilacji.

**Zasady:**

- Można umieszczać `_` w dowolnym miejscu między cyframi.
- Nie może znajdować się na początku ani końcu liczby, ani obok symboli `L`, `f`, `0x` itp.
- Działa dla wszystkich systemów liczbowych: dziesiętnego, szesnastkowego, ósemkowego i binarnego.

```java
int RICHARD_NIXONS_SSN = 567_68_0515;   // czytelny wzorzec SSN
int for_no_reason = 1___2___3;          // możliwe, choć nietypowe
int JAVA_ID = 0xCAFE_BABE;             // szesnastkowy
long grandTotal = 40_123_456_789L;      // 40 miliardów
```

---

## 2. Typy referencyjne (złożone)

Typy referencyjne przechowują **adres obiektu w pamięci (referencję)**, a nie samą wartość. Należą do nich:

- **Klasy** – np. `String`, `Scanner`, własne klasy użytkownika.
- **Interfejsy** – mogą być typem referencyjnym, ponieważ są implementowane przez klasy.
- **Tablice** – przechowują wiele wartości tego samego typu.

> ⚠️ `String` **nie jest** typem prymitywnym — to klasa. Jest jednak traktowany wyjątkowo przez kompilator (np. możliwość tworzenia przez literał `"tekst"`).
> 

Domyślna wartość każdej zmiennej referencyjnej (pola klasy) wynosi **`null`**, co oznacza brak przypisania do obiektu.

```java
int liczba = 42;           // typ prymitywny – przechowuje wartość
String tekst = "Hello";    // typ referencyjny – przechowuje adres obiektu
boolean flaga = true;      // typ prymitywny
int[] liczby = {1, 2, 3};  // typ referencyjny (tablica)
String brak = null;        // referencja wskazuje na "nic"
```

---

### Porównywanie typów

- Typy prymitywne porównujemy operatorem `==` — sprawdza **równość wartości**.
- Typy referencyjne porównywane przez `==` sprawdzają, czy obie zmienne wskazują na **ten sam obiekt w pamięci**.
- Do porównania **zawartości** obiektów używamy metody `.equals()`.

```java
String a = new String("hello");
String b = new String("hello");

System.out.println(a == b);       // false – różne obiekty w pamięci
System.out.println(a.equals(b));  // true  – ta sama zawartość
```

---

## 3. Klasy opakowujące (Wrapper Classes)

Każdy typ prymitywny ma odpowiadającą klasę opakowującą. Jest to konieczne np. przy pracy z kolekcjami (`List`, `Set`), które wymagają typów referencyjnych.

| Prymityw | Klasa opakowująca |
| --- | --- |
| `byte` | `Byte` |
| `short` | `Short` |
| `int` | `Integer` |
| `long` | `Long` |
| `float` | `Float` |
| `double` | `Double` |
| `boolean` | `Boolean` |
| `char` | `Character` |

### Autoboxing i Unboxing

Java automatycznie konwertuje między typem prymitywnym a jego klasą opakowującą:

```java
Integer num = 10;   // Autoboxing: int → Integer
int n = num;        // Unboxing: Integer → int

List<Integer> lista = new ArrayList<>();
lista.add(5);       // Autoboxing dzieje się automatycznie
```

---

## 4. Promocja typów (Type Promotion)

Podczas operacji arytmetycznych Java automatycznie **promuje** typy do bardziej pojemnych, aby operacja była wykonana poprawnie.

**Zasady:**

- Typy `byte`, `short`, `char` są zawsze promowane do `int` przed operacją.
- Jeśli w wyrażeniu uczestniczą różne typy, wynik jest zgodny z **najbardziej pojemnym** typem w wyrażeniu (`int` → `long` → `float` → `double`).

```java
byte b = 42;
int i = 43;
int result = b * i; // b jest promowany do int przed mnożeniem

int x = 5;
double y = 2.0;
double z = x + y;  // x promowany do double, wynik to double
```

---

## 5. Rzutowanie typów (Casting)

### Konwersja rozszerzająca (Widening)

Konwersja do typu o **większym zakresie** — odbywa się **automatycznie**, bez ryzyka utraty danych.

```java
int i = 100;
long l = i;    // automatyczne – int mieści się w long
double d = i;  // automatyczne – int mieści się w double
```

### Konwersja zawężająca (Narrowing)

Konwersja do typu o **mniejszym zakresie** — wymaga **jawnego rzutowania**. Może prowadzić do utraty danych lub przepełnienia.

```java
int i = 13;
byte b = (byte) i;  // OK – jawne rzutowanie
```

### Przepełnienie (Overflow)

Jeśli rzutowana wartość wykracza poza zakres docelowego typu, następuje **zawinięcie zakresu** (ang. overflow), a wynik może być zaskakujący:

```java
int i = 128;
byte b = (byte) i;  // Wynik: -128 (zakres byte to -128..127, wartość "zawija się")

int j = 130;
byte c = (byte) j;  // Wynik: -126
```

---

## 6. Wyrażenia i instrukcje

Warto rozróżnić dwa pojęcia:

- **Wyrażenie (expression)** – zwraca wartość, można je przypisać do zmiennej, np. `int x = 5 + 3;`
- **Instrukcja (statement)** – wykonuje działanie, niekoniecznie zwraca wartość, np. `for`, `if`, `while`.
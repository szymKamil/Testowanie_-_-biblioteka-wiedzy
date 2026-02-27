# Boxing

Boxing w Javie to proces konwersji typu prostego (prymitywnego) na jego odpowiednik typu referencyjnego (klasowego). W
Javie istnieją następujące typy prymitywne: int, boolean, char, byte, short, long, float, i double. Każdy z nich ma swój
odpowiednik klasowy (np. Integer dla int, Boolean dla boolean itd.).
Java automatycznie obsługuje tę konwersję, co nazywa się autoboxingiem. Odbywa się ona w sytuacji, gdy potrzebujemy
obiektu zamiast typu prymitywnego. Konwersja odwrotna, czyli zamiana obiektu na typ prymitywny, nazywa się unboxingiem.

```java
// NaukaElementów.Autoboxing: konwersja z int na Integer
int liczba = 10;
Integer liczbaObiektowa = liczba; // automatyczny boxing

// Unboxing: konwersja z Integer na int
Integer innaLiczbaObiektowa = 20;
int innaLiczba = innaLiczbaObiektowa; // automatyczny unboxing
```

Kiedy stosowany jest autoboxing?
Java wykonuje autoboxing automatycznie w przypadkach, gdy wymagany jest typ obiektowy, np.:
Kolekcje – ArrayList, HashMap, itp., przechowują tylko obiekty, dlatego typy prymitywne muszą być zamienione na
obiektowe.

```java
ArrayList<Integer> lista = new ArrayList<>();
lista.

add(5); // autoboxing z int na Integer
```

Operacje z metodami – gdy metoda oczekuje argumentu typu klasowego, typ prymitywny jest automatycznie przekształcany na
jego odpowiednik klasowy.
Przypisanie – w przypisaniach między typem prymitywnym a klasowym, np. przypisanie int do Integer i odwrotnie.

Wydajność
Boxing i unboxing mogą wiązać się z dodatkowymi kosztami pamięci i obliczeń, ponieważ typ prymitywny jest opakowywany w
obiekt. Należy na to uważać w kodzie wymagającym wysokiej wydajności.
Boxing i unboxing wiążą się z dodatkowymi operacjami alokacji pamięci oraz konwersji, co może być kosztowne w przypadku
dużej liczby operacji.
W sytuacjach wymagających wysokiej wydajności warto rozważyć użycie typów prymitywnych zamiast ich opakowań.

Typy opakowujące (np. Integer, Double) są niemutowalne (immutable). Oznacza to, że raz utworzony obiekt typu
opakowującego nie może zostać zmieniony. Każda operacja, która wydaje się zmieniać wartość, w rzeczywistości tworzy nowy
obiekt.

```java
Integer liczba = 10;
liczba =liczba +5; // Tworzony jest nowy obiekt Integer, a stary pozostaje niezmieniony
```

Od Javy 9 użycie takich konstrukcji jest oznaczone jako deprecated. Zaleca się korzystanie wyłącznie z
autoboxingu/unboxingu.

```java
public class BoxingExample {
    public static void main(String[] args) {
        // Przestarzały sposób boxingu - ręczne tworzenie obiektów
        int oldPrimitiveInt = 10;
        Integer oldBoxedInt = new Integer(oldPrimitiveInt); // Boxing - przestarzałe
        System.out.println("Przestarzały boxing: " + oldBoxedInt);

        // Przestarzały sposób unboxingu - ręczne wywołanie metody
        Integer oldBoxedValue = new Integer(20);
        int oldUnboxedInt = oldBoxedValue.intValue(); // Unboxing - przestarzałe
        System.out.println("Przestarzały unboxing: " + oldUnboxedInt);

        // Nowoczesny sposób boxingu - autoboxing
        int newPrimitiveInt = 30;
        Integer newBoxedInt = newPrimitiveInt; // NaukaElementów.Autoboxing - nowoczesny
        Integer autboxed = 15;
        System.out.println("Nowoczesny boxing (autoboxing): " + newBoxedInt);

        // Nowoczesny sposób unboxingu - automatyczny unboxing
        Integer newBoxedValue = 40;
        int newUnboxedInt = newBoxedValue; // Unboxing - nowoczesny
        System.out.println("Nowoczesny unboxing (automatyczny): " + newUnboxedInt);
    }
}
```

Różnice w porównywaniu typów prymitywnych i ich opakowań:

- Typy prymitywne porównuje się za pomocą operatora ==.
- Typy opakowujące powinny być porównywane za pomocą metody .equals(), ponieważ operator == porównuje referencje, a nie
wartości.

```java
Integer a = 100;
Integer b = 100;
System.out.

println(a ==b); // true dla wartości z zakresu -128 do 127 (cache)
System.out.

println(a.equals(b)); // true - porównanie wartości
```

Typy prymitywne nie mogą być używane bezpośrednio jako typy generyczne. Typy opakowujące są wymagane.

```java
List<int> lista; // Błąd kompilacji
List<Integer> listaPoprawna = new ArrayList<>(); // Poprawne użycie typu opakowującego
```

# Boxing i Unboxing w Javie

**Boxing** to proces konwersji typu prymitywnego na jego odpowiednik klasowy (obiektowy).
**Unboxing** to proces odwrotny – zamiana obiektu na typ prymitywny.
Java obsługuje tę konwersję automatycznie, co nazywa się **autoboxingiem**.

---

## Tabela typów prymitywnych i ich opakowań

| Typ prymitywny | Klasa opakowująca |
| --- | --- |
| `byte` | `Byte` |
| `short` | `Short` |
| `int` | `Integer` |
| `long` | `Long` |
| `float` | `Float` |
| `double` | `Double` |
| `boolean` | `Boolean` |
| `char` | `Character` |

---

## Autoboxing i Unboxing

```java
// Autoboxing: konwersja z int na Integer
int liczba = 10;
Integer liczbaObiektowa = liczba; // automatyczny boxing

// Unboxing: konwersja z Integer na int
Integer innaLiczbaObiektowa = 20;
int innaLiczba = innaLiczbaObiektowa; // automatyczny unboxing
```

---

## Kiedy stosowany jest autoboxing?

Java wykonuje autoboxing automatycznie gdy wymagany jest typ obiektowy:

**Kolekcje** – `ArrayList`, `HashMap` itp. przechowują tylko obiekty:

```java
ArrayList<Integer> lista = new ArrayList<>();
lista.add(5); // autoboxing: int → Integer
```

**Metody** – gdy metoda oczekuje argumentu typu klasowego:

```java
public void przyjmij(Integer x) { ... }
przyjmij(42); // autoboxing: int → Integer
```

**Przypisania** – między typem prymitywnym a klasowym:

```java
Integer x = 100; // autoboxing
int y = x;       // unboxing
```

**Typy generyczne** – typy prymitywne nie mogą być używane bezpośrednio jako parametry generyczne:

```java
List<int> lista;            // Błąd kompilacji!
List<Integer> lista = new ArrayList<>();  // Poprawne
```

---

## Przestarzały sposób tworzenia (deprecated od Javy 9)

Przed Javą 9 można było ręcznie tworzyć obiekty opakowujące. Od Javy 9 jest to oznaczone jako `@Deprecated` –
zaleca się korzystanie wyłącznie z autoboxingu:

```java
// Przestarzałe – nie używać:
Integer stary = new Integer(10);          // boxing – deprecated
int wartosc = stary.intValue();           // unboxing – deprecated

// Nowoczesne – zalecane:
Integer nowy = 10;                        // autoboxing
int wartosc2 = nowy;                      // automatyczny unboxing
```

---

## `Integer.valueOf()` vs `new Integer()`

`Integer.valueOf(n)` korzysta z wewnętrznego **cache'a** (dla wartości -128 do 127), dzięki czemu nie tworzy
nowego obiektu za każdym razem. Autoboxing używa właśnie `valueOf()` pod spodem.
`new Integer(n)` zawsze tworzy **nowy obiekt** w pamięci – stąd między innymi jego deprecacja.

```java
Integer a = Integer.valueOf(100); // korzysta z cache
Integer b = Integer.valueOf(100); // ten sam obiekt z cache
System.out.println(a == b);       // true

Integer c = new Integer(100);     // nowy obiekt – deprecated
Integer d = new Integer(100);     // kolejny nowy obiekt – deprecated
System.out.println(c == d);       // false – różne obiekty w pamięci
```

---

## Integer Cache – ważna pułapka!

Java cachuje obiekty `Integer` dla wartości z zakresu **-128 do 127**. Oznacza to, że dla tych wartości
autoboxing zwraca **ten sam obiekt** z pamięci podręcznej. Dla wartości spoza tego zakresu każdorazowo tworzony
jest **nowy obiekt**.

To częste źródło trudnych do wykrycia bugów przy porównywaniu przez `==`:

```java
Integer a = 100;
Integer b = 100;
System.out.println(a == b);      // true  – wartość w cache (-128..127)
System.out.println(a.equals(b)); // true

Integer c = 200;
Integer d = 200;
System.out.println(c == d);      // false! – wartość spoza cache, różne obiekty
System.out.println(c.equals(d)); // true   – porównanie wartości
```

> **Zasada:** typy opakowujące **zawsze** porównuj przez `.equals()`, nigdy przez `==`.
> 

Ten sam mechanizm cache'owania dotyczy też `Byte`, `Short`, `Long` (zakres -128..127) oraz `Character`
(zakres 0..127). `Float` i `Double` **nie** są cachowane.

---

## NullPointerException przy unboxingu

Typy opakowujące mogą przyjmować wartość `null` (w przeciwieństwie do typów prymitywnych). Próba unboxingu
wartości `null` rzuca **NullPointerException** – to jedna z najczęstszych i najbardziej zdradliwych pułapek:

```java
Integer x = null;
int y = x;  // NullPointerException! – próba unboxingu null

// Bezpieczny sposób:
if (x != null) {
    int z = x;
}

// Lub z użyciem Objects.requireNonNullElse:
int z = Objects.requireNonNullElse(x, 0); // zwróci 0 jeśli x == null
```

Szczególnie groźne w wyrażeniach warunkowych i obliczeniach:

```java
Integer a = null;
Integer b = 5;
int suma = a + b; // NullPointerException! – unboxing a przed dodawaniem
```

---

## Niemutowalność typów opakowujących

Typy opakowujące są **niemutowalne** (immutable) – raz utworzony obiekt nie może zostać zmieniony.
Każda operacja, która wydaje się zmieniać wartość, w rzeczywistości tworzy **nowy obiekt**:

```java
Integer liczba = 10;
liczba = liczba + 5; // Tworzony jest nowy obiekt Integer(15), stary Integer(10) pozostaje niezmieniony
```

---

## Wydajność

Boxing i unboxing wiążą się z dodatkowymi operacjami alokacji pamięci i konwersji. W kodzie wymagającym
wysokiej wydajności warto unikać niepotrzebnego boxingu.

Klasyczny przykład złej pętli z autoboxingiem:

```java
// Źle – w każdej iteracji tworzony jest nowy obiekt Long:
Long suma = 0L;
for (long i = 0; i < 1_000_000; i++) {
    suma += i; // unboxing sumy, dodanie, autoboxing wyniku → milion niepotrzebnych obiektów
}

// Dobrze – używamy typu prymitywnego:
long suma = 0L;
for (long i = 0; i < 1_000_000; i++) {
    suma += i; // żadnego boxingu
}
```

> W sytuacjach wymagających wysokiej wydajności zawsze preferuj typy prymitywne zamiast ich opakowań,
o ile nie jest wymagany typ obiektowy (np. kolekcje, generyki).
> 

---

## Porównywanie – podsumowanie zasad

| Sytuacja | Użyj |
| --- | --- |
| Porównanie typów prymitywnych | `==` |
| Porównanie typów opakowujących | `.equals()` |
| Sprawdzenie czy obiekt to `null` | `== null` |
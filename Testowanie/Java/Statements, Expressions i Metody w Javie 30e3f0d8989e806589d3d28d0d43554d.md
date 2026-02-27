# Statements, Expressions i Metody w Javie

### **Statements (Instrukcje) w Javie**

Statements (instrukcje) w języku Java to jednostki kodu wykonujące określone akcje. W przeciwieństwie do wyrażeń (expressions), nie muszą one zwracać wartości. Są podstawowymi elementami wykonywanymi przez kompilator podczas działania programu.

**Definicja:**
Statement to pojedyncza instrukcja, która może obejmować:

- przypisania,
- deklaracje,
- wywołania metod,
- pętle,
- instrukcje sterujące.

---

### **Typy statements w Javie**

### **1. Expression Statement (Wyrażenie jako instrukcja)**

Wyrażenie użyte jako samodzielna instrukcja, np. przypisanie, wywołanie metody, inkrementacja zmiennej.

```java
x = 10; // Wyrażenie przypisania jako statement
System.out.println("Hello, world!"); // Wywołanie metody jako statement
```

### **2. Declaration Statement (Deklaracja zmiennej)**

Deklaracja zmiennej lub stałej, zwykle na początku bloku kodu.

```java
int a = 5;
String name = "John";
```

### **3. Control Flow Statements (Instrukcje sterujące)**

Kontrolują przepływ wykonania programu.

- **Instrukcje warunkowe:** `if`, `else`, `switch`
- **Pętle:** `for`, `while`, `do-while`

```java
if (x > 0) {
    System.out.println("x jest dodatnie");
} else {
    System.out.println("x jest niedodatnie");
}

for (int i = 0; i < 10; i++) {
    System.out.println(i);
}
```

### **4. Jump Statements (Instrukcje skoku)**

Zmieniają miejsce wykonania programu.

- `break`: kończy pętlę lub `switch`
- `continue`: przechodzi do kolejnej iteracji pętli
- `return`: kończy działanie metody (opcjonalnie zwraca wartość)

```java
while (true) {
    if (x > 10) {
        break;
    }
}

int findMax(int a, int b) {
    if (a > b) {
        return a;
    } else {
        return b;
    }
}
```

### **5. Exception Handling Statements (Instrukcje obsługi wyjątków)**

Obsługują błędy i wyjątki.

```java
try {
    int result = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println("Błąd: " + e.getMessage());
} finally {
    System.out.println("Zawsze się wykona.");
}
```

### **6. Empty Statement (Pusta instrukcja)**

Statement, który nic nie robi. Użyteczny np. w pętlach.

```java
while (x < 10)
    ; // Pusta instrukcja
```

---

### **Podsumowanie: Statements w Javie**

Statements wykonują operacje i zmieniają stan programu. Mogą to być:

- przypisania,
- wywołania metod,
- deklaracje,
- instrukcje sterujące (`if`, `while`, `for` itd.),
- instrukcje skoku (`break`, `continue`, `return`),
- instrukcje obsługi wyjątków (`try-catch`).

W przeciwieństwie do wyrażeń, statements nie muszą zwracać wartości – są ogólnymi poleceniami wykonawczymi.

---

## **Wyrażenia (Expressions) w Javie**

**Definicja:**
Wyrażenie (expression) to fragment kodu, który można ocenić i który zwraca wartość. Może to być zmienna, operacja arytmetyczna, wywołanie metody lub ich kombinacja.

**Rodzaje wyrażeń:**

- Proste: zmienne, stałe, wartości
- Arytmetyczne: operacje na liczbach
- Logiczne: operacje na wartościach boolean
- Warunkowe: operator trójargumentowy
- Funkcyjne: wywołanie funkcji/metody
- Przypisania: przypisanie wartości do zmiennej

**Przykłady:**

```java
int x = 5;                // 'x' jest wyrażeniem
int y = 3 + 2;            // '3 + 2' jest wyrażeniem arytmetycznym
boolean isTrue = (5 > 3); // '(5 > 3)' jest wyrażeniem logicznym
int max = (a > b) ? a : b;// wyrażenie warunkowe
int result = Math.max(a, b); // wyrażenie funkcji
int a = (x = 10);         // wyrażenie przypisania
```

Wyrażenia mogą być używane wszędzie, gdzie oczekiwana jest wartość.

---

### **Ekspresja (Expression) vs Wyrażenie**

W praktyce, w Javie oba terminy oznaczają to samo i odnoszą się do fragmentu kodu zwracającego wartość. W dokumentacji Javy częściej spotyka się termin “wyrażenie”.

---

## **Tabela: Typy statements w Javie**

| Rodzaj | Przykład | Opis |
| --- | --- | --- |
| Deklaracja | int x = 5; | Tworzy zmienną i przypisuje wartość |
| Wyrażenie | x++; | Operacja, np. przypisanie, inkrementacja |
| Blokowy | { int y = 2; y *= 3; } | Grupa instrukcji w nawiasach klamrowych |
| Sterujący | if, for, while, switch | Sterowanie przepływem programu |
| Return | return x; | Zwraca wartość z metody |
| Throw | throw new Exception(); | Rzuca wyjątek |
| Try-catch | try { … } catch { … } | Obsługa wyjątków |

---

### **Przykład kodu z różnymi statementami**

```java
public class Main {
    public static void main(String[] args) {
        int a = 10;                // deklaracja
        a++;                       // wyrażenie
        if (a > 5) {               // instrukcja warunkowa
            System.out.println(a); // wywołanie metody
        }
    }
}
```

---

## **Funkcja vs Metoda w Javie**

| Cecha | Funkcja | Metoda |
| --- | --- | --- |
| Przynależność | Niezależna, nie należy do klasy | Należy do klasy lub obiektu |
| Wywoływanie | Samodzielnie (np. print() w Pythonie) | Na obiekcie lub klasie (obiekt.metoda()) |
| W Javie | Nie występują samodzielnie | Każda funkcja to metoda klasy |
| Przykład | def add(x, y): return x + y (Python) | int add(int x, int y) { return x + y; } |

**Wniosek:**
W Javie nie istnieją funkcje w czystej postaci – wszystko to metody, bo zawsze muszą należeć do jakiejś klasy (nawet jeśli są `static`).

---

## Uporządkowany i Sformatowany Plik: Statements i Expressions w Javie

### **Statements (Instrukcje) w Javie**

Statements (instrukcje) w języku Java to jednostki kodu wykonujące określone akcje. W przeciwieństwie do wyrażeń (expressions), nie muszą one zwracać wartości. Są podstawowymi elementami wykonywanymi przez kompilator podczas działania programu.

**Definicja:**
Statement to pojedyncza instrukcja, która może obejmować:

- przypisania,
- deklaracje,
- wywołania metod,
- pętle,
- instrukcje sterujące.

---

### **Typy statements w Javie**

### **1. Expression Statement (Wyrażenie jako instrukcja)**

Wyrażenie użyte jako samodzielna instrukcja, np. przypisanie, wywołanie metody, inkrementacja zmiennej.

```java
x = 10; // Wyrażenie przypisania jako statement
System.out.println("Hello, world!"); // Wywołanie metody jako statement
```

### **2. Declaration Statement (Deklaracja zmiennej)**

Deklaracja zmiennej lub stałej, zwykle na początku bloku kodu.

```java
int a = 5;
String name = "John";
```

### **3. Control Flow Statements (Instrukcje sterujące)**

Kontrolują przepływ wykonania programu.

- **Instrukcje warunkowe:** `if`, `else`, `switch`
- **Pętle:** `for`, `while`, `do-while`

```java
if (x > 0) {
    System.out.println("x jest dodatnie");
} else {
    System.out.println("x jest niedodatnie");
}

for (int i = 0; i < 10; i++) {
    System.out.println(i);
}
```

### **4. Jump Statements (Instrukcje skoku)**

Zmieniają miejsce wykonania programu.

- `break`: kończy pętlę lub `switch`
- `continue`: przechodzi do kolejnej iteracji pętli
- `return`: kończy działanie metody (opcjonalnie zwraca wartość)

```java
while (true) {
    if (x > 10) {
        break;
    }
}

int findMax(int a, int b) {
    if (a > b) {
        return a;
    } else {
        return b;
    }
}
```

### **5. Exception Handling Statements (Instrukcje obsługi wyjątków)**

Obsługują błędy i wyjątki.

```java
try {
    int result = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println("Błąd: " + e.getMessage());
} finally {
    System.out.println("Zawsze się wykona.");
}
```

### **6. Empty Statement (Pusta instrukcja)**

Statement, który nic nie robi. Użyteczny np. w pętlach.

```java
while (x < 10)
    ; // Pusta instrukcja
```

---

### **Podsumowanie: Statements w Javie**

Statements wykonują operacje i zmieniają stan programu. Mogą to być:

- przypisania,
- wywołania metod,
- deklaracje,
- instrukcje sterujące (`if`, `while`, `for` itd.),
- instrukcje skoku (`break`, `continue`, `return`),
- instrukcje obsługi wyjątków (`try-catch`).

W przeciwieństwie do wyrażeń, statements nie muszą zwracać wartości – są ogólnymi poleceniami wykonawczymi.

---

## **Wyrażenia (Expressions) w Javie**

**Definicja:**
Wyrażenie (expression) to fragment kodu, który można ocenić i który zwraca wartość. Może to być zmienna, operacja arytmetyczna, wywołanie metody lub ich kombinacja.

**Rodzaje wyrażeń:**

- Proste: zmienne, stałe, wartości
- Arytmetyczne: operacje na liczbach
- Logiczne: operacje na wartościach boolean
- Warunkowe: operator trójargumentowy
- Funkcyjne: wywołanie funkcji/metody
- Przypisania: przypisanie wartości do zmiennej

**Przykłady:**

```java
int x = 5;                // 'x' jest wyrażeniem
int y = 3 + 2;            // '3 + 2' jest wyrażeniem arytmetycznym
boolean isTrue = (5 > 3); // '(5 > 3)' jest wyrażeniem logicznym
int max = (a > b) ? a : b;// wyrażenie warunkowe
int result = Math.max(a, b); // wyrażenie funkcji
int a = (x = 10);         // wyrażenie przypisania
```

Wyrażenia mogą być używane wszędzie, gdzie oczekiwana jest wartość.

---

### **Ekspresja (Expression) vs Wyrażenie**

W praktyce, w Javie oba terminy oznaczają to samo i odnoszą się do fragmentu kodu zwracającego wartość. W dokumentacji Javy częściej spotyka się termin “wyrażenie”.

---

## **Tabela: Typy statements w Javie**

| Rodzaj | Przykład | Opis |
| --- | --- | --- |
| Deklaracja | int x = 5; | Tworzy zmienną i przypisuje wartość |
| Wyrażenie | x++; | Operacja, np. przypisanie, inkrementacja |
| Blokowy | { int y = 2; y *= 3; } | Grupa instrukcji w nawiasach klamrowych |
| Sterujący | if, for, while, switch | Sterowanie przepływem programu |
| Return | return x; | Zwraca wartość z metody |
| Throw | throw new Exception(); | Rzuca wyjątek |
| Try-catch | try { … } catch { … } | Obsługa wyjątków |

---

### **Przykład kodu z różnymi statementami**

```java
public class Main {
    public static void main(String[] args) {
        int a = 10;                // deklaracja
        a++;                       // wyrażenie
        if (a > 5) {               // instrukcja warunkowa
            System.out.println(a); // wywołanie metody
        }
    }
}
```

---

## **Funkcja vs Metoda w Javie**

| Cecha | Funkcja | Metoda |
| --- | --- | --- |
| Przynależność | Niezależna, nie należy do klasy | Należy do klasy lub obiektu |
| Wywoływanie | Samodzielnie (np. print() w Pythonie) | Na obiekcie lub klasie (obiekt.metoda()) |
| W Javie | Nie występują samodzielnie | Każda funkcja to metoda klasy |
| Przykład | def add(x, y): return x + y (Python) | int add(int x, int y) { return x + y; } |

**Wniosek:**
W Javie nie istnieją funkcje w czystej postaci – wszystko to metody, bo zawsze muszą należeć do jakiejś klasy (nawet jeśli są `static`).

---

# Statements, Expressions i Metody w Javie

---

## 1. Statements (Instrukcje)

Statement to pojedyncza instrukcja wykonująca określoną akcję. W przeciwieństwie do wyrażeń, nie musi zwracać
wartości. Każdy statement kończy się średnikiem (`;`) lub jest blokiem kodu `{ }`.

Statement może obejmować: przypisania, deklaracje, wywołania metod, pętle, instrukcje sterujące.

---

### Typy Statements

### 1.1 Expression Statement (Wyrażenie jako instrukcja)

Wyrażenie użyte jako samodzielna instrukcja. Ważne ograniczenie: **nie każde wyrażenie może być instrukcją**.
Tylko wyrażenia mające efekt uboczny są poprawne jako samodzielne instrukcje:

```java
x = 10;                        // ✅ przypisanie
x++;                           // ✅ inkrementacja
System.out.println("Hello");   // ✅ wywołanie metody
new ArrayList<>();             // ✅ tworzenie obiektu

5 + 3;                         // ❌ błąd kompilacji – samo wyrażenie arytmetyczne
                               //    bez efektu ubocznego nie może być instrukcją
```

### 1.2 Declaration Statement (Deklaracja zmiennej)

Deklaracja zmiennej lub stałej, zwykle na początku bloku kodu:

```java
int a = 5;
String name = "John";
final double PI = 3.14159;
```

### 1.3 Block Statement (Instrukcja blokowa)

Grupa instrukcji ujęta w nawiasy klamrowe, traktowana jako jedna całość. Zmienne zadeklarowane w bloku
są dostępne tylko wewnątrz niego:

```java
{
    int y = 2;
    y *= 3;
    System.out.println(y); // 6
}
// y niedostępne tutaj
```

### 1.4 Control Flow Statements (Instrukcje sterujące)

Kontrolują przepływ wykonania programu:

```java
// Warunkowe
if (x > 0) {
    System.out.println("x jest dodatnie");
} else {
    System.out.println("x jest niedodatnie");
}

// Pętla for
for (int i = 0; i < 10; i++) {
    System.out.println(i);
}

// Pętla while
while (x < 10) {
    x++;
}

// Pętla do-while – wykonuje się przynajmniej raz
do {
    x++;
} while (x < 10);

// Switch
switch (dzien) {
    case 1 -> System.out.println("Poniedziałek");
    case 2 -> System.out.println("Wtorek");
    default -> System.out.println("Inny dzień");
}
```

### 1.5 Jump Statements (Instrukcje skoku)

Zmieniają miejsce wykonania programu:

- `break` – kończy pętlę lub `switch`
- `continue` – przechodzi do kolejnej iteracji pętli
- `return` – kończy działanie metody, opcjonalnie zwraca wartość

```java
// break
for (int i = 0; i < 10; i++) {
    if (i == 5) break; // zatrzymuje pętlę gdy i == 5
}

// continue
for (int i = 0; i < 10; i++) {
    if (i % 2 == 0) continue; // pomija parzyste
    System.out.println(i);
}

// return
int findMax(int a, int b) {
    if (a > b) return a;
    return b;
}
```

### 1.6 Labeled Statements (Instrukcje etykietowane)

Etykiety pozwalają użyć `break` lub `continue` w odniesieniu do konkretnej, zewnętrznej pętli –
przydatne przy zagnieżdżonych pętlach:

```java
outer:
for (int i = 0; i < 5; i++) {
    for (int j = 0; j < 5; j++) {
        if (j == 2) continue outer; // przejdź do następnej iteracji ZEWNĘTRZNEJ pętli
        if (i == 3) break outer;    // przerwij ZEWNĘTRZNĄ pętlę
        System.out.println(i + ", " + j);
    }
}
```

### 1.7 Exception Handling Statements (Obsługa wyjątków)

```java
try {
    int result = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println("Błąd: " + e.getMessage());
} finally {
    System.out.println("Zawsze się wykona – np. zamknięcie zasobów.");
}
```

### 1.8 Throw Statement

Rzuca wyjątek ręcznie. Może wystąpić w dowolnym miejscu metody i natychmiast przerywa jej wykonanie:

```java
void sprawdzWiek(int wiek) {
    if (wiek < 0) {
        throw new IllegalArgumentException("Wiek nie może być ujemny: " + wiek);
    }
}
```

### 1.9 Empty Statement (Pusta instrukcja)

Statement, który nic nie robi. Przydatny np. gdy ciało pętli jest puste:

```java
// Czekaj dopóki warunek nie będzie spełniony (logika w warunku)
while (!czyGotowyWarunek())
    ; // pusta instrukcja
```

---

### Tabela: Typy Statements

| Rodzaj | Przykład | Opis |
| --- | --- | --- |
| Deklaracja | `int x = 5;` | Tworzy zmienną i przypisuje wartość |
| Wyrażenie | `x++;` | Operacja z efektem ubocznym |
| Blokowy | `{ int y = 2; y *= 3; }` | Grupa instrukcji w nawiasach klamrowych |
| Sterujący | `if`, `for`, `while`, `switch` | Sterowanie przepływem programu |
| Etykietowany | `outer: for(...)` | Pętla z etykietą do użycia z break/continue |
| Return | `return x;` | Zwraca wartość z metody |
| Throw | `throw new Exception();` | Rzuca wyjątek |
| Try-catch | `try { } catch { }` | Obsługa wyjątków |
| Pusta | `;` | Brak akcji |

---

## 2. Expressions (Wyrażenia)

Wyrażenie to fragment kodu, który można ocenić i który **zwraca wartość**. Może to być zmienna, operacja
arytmetyczna, wywołanie metody lub ich kombinacja.

### Rodzaje wyrażeń

```java
int x = 5;                        // 'x' – wyrażenie proste (zmienna)
int y = 3 + 2;                    // '3 + 2' – wyrażenie arytmetyczne
boolean isTrue = (5 > 3);         // '(5 > 3)' – wyrażenie logiczne
int max = (a > b) ? a : b;        // wyrażenie warunkowe (ternarne)
int result = Math.max(a, b);      // wyrażenie wywołania metody
int a = (x = 10);                 // wyrażenie przypisania (zwraca przypisaną wartość)
```

### Wyrażenia lambda (od Javy 8)

Wyrażenia lambda to osobna, ważna kategoria – anonimowe funkcje przekazywane jako argumenty:

```java
// Wyrażenie lambda jako wyrażenie
Runnable r = () -> System.out.println("Hello");

// Lambda z parametrami i zwracaną wartością
Comparator<Integer> comp = (a, b) -> a - b;

// Lambda w kolekcjach
List<String> lista = List.of("Ala", "Bob", "Cezary");
lista.forEach(name -> System.out.println(name));
lista.sort((a, b) -> a.compareTo(b));
```

### Kluczowa różnica: Expression vs Statement

| Cecha | Expression (Wyrażenie) | Statement (Instrukcja) |
| --- | --- | --- |
| Zwraca wartość | ✅ zawsze | ❌ nie musi |
| Może być przypisane | ✅ `int x = 3 + 2` | ❌ nie można przypisać `if` |
| Przykład | `a > b`, `x++`, `Math.max()` | `if(...)`, `for(...)`, `return` |
| Może być instrukcją | tylko z efektem ubocznym | ✅ zawsze |

---

## 3. Funkcja vs Metoda w Javie

W Javie **nie istnieją funkcje w czystej postaci** – wszystko to metody, ponieważ zawsze muszą należeć
do jakiejś klasy (nawet jeśli są `static`).

| Cecha | Funkcja | Metoda |
| --- | --- | --- |
| Przynależność | Niezależna, nie należy do klasy | Należy do klasy lub obiektu |
| Wywoływanie | Samodzielnie (np. `print()` w Python) | Na obiekcie lub klasie (`obiekt.metoda()`) |
| W Javie | Nie występują samodzielnie | Każda "funkcja" to metoda klasy |
| Przykład | `def add(x, y): return x + y` (Python) | `int add(int x, int y) { return x + y; }` |

Metody statyczne są wywoływane na klasie (nie na obiekcie), przez co mogą przypominać funkcje, ale
nadal należą do klasy:

```java
// Metoda statyczna – wywołana na klasie, nie na obiekcie
int wynik = Math.max(3, 7);       // Math to klasa, max to metoda statyczna

// Metoda instancyjna – wywołana na obiekcie
String tekst = "hello";
String duzy = tekst.toUpperCase(); // tekst to obiekt, toUpperCase to metoda instancyjna
```

---

## 4. Przykład łączący wszystkie koncepcje

```java
public class Main {
    public static void main(String[] args) {
        int a = 10;                        // declaration statement
        a++;                               // expression statement

        if (a > 5) {                       // control flow statement
            System.out.println(a);         // expression statement (wywołanie metody)
        }

        int max = (a > 0) ? a : 0;        // expression (ternary) w declaration statement

        outer:                             // labeled statement
        for (int i = 0; i < 3; i++) {
            for (int j = 0; j < 3; j++) {
                if (j == 1) continue outer;
                System.out.println(i + "," + j);
            }
        }

        try {                              // exception handling statement
            int wynik = 10 / a;
        } catch (ArithmeticException e) {
            throw new RuntimeException(e); // throw statement
        }
    }
}
```
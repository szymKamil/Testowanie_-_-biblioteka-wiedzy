# Operatory

# Tabela: Operatory w Javie i ich priorytet

| Priorytet | Typ operatora | Operatory | Opis |
| --- | --- | --- | --- |
| 1 (najwyższy) | Postfix | `expr++`, `expr--` | Operatory inkrementacji i dekrementacji stosowane po wyrażeniu. |
| 2 | Prefix | `++expr`, `--expr`, `+`, `-`, `~`, `!` | Operatory jednostkowe: inkrementacja, dekrementacja, zmiana znaku, negacja logiczna. |
| 3 | Multiplicative | `*`, `/`, `%` | Operatory mnożenia, dzielenia i reszty z dzielenia. |
| 4 | Additive | `+`, `-` | Operatory dodawania i odejmowania. |
| 5 | Shift | `<<`, `>>`, `>>>` | Operatory przesunięcia bitowego w lewo i prawo (z i bez znaku). |
| 6 | Relational | `<`, `>`, `<=`, `>=`, `instanceof` | Operatory porównania i sprawdzania typu obiektu. |
| 7 | Equality | `==`, `!=` | Operatory równości i nierówności. |
| 8 | Bitwise AND | `&` | Operator bitowego AND. |
| 9 | Bitwise XOR | `^` | Operator bitowego XOR. |
| 10 | Bitwise OR | `|` | Operator bitowego OR. |
| 11 | Logical AND | `&&` | Operator logicznego AND. |
| 12 | Logical OR | `|                                                                                      |` | Operator logicznego OR. |
| 13 | Ternary | `? :` | Operator warunkowy (ternarny), zwraca różne wartości na podstawie warunku. |
| 14 | Assignment | `=`, `+=`, `-=`, `*=`, `/=`, `%=` | Operatory przypisania, które przypisują wartości zmiennym. |
|  |  | `&=`, `^=`, `| =`, `<<=`, `>>=`, `>>>=` | Operatorzy przypisania z bitowymi operacjami. |
| 15 (najniższy) | Comma | `,` | Operator przecinka, używany do oddzielania wyrażeń. |

Opis operatorów
Postfix: Operatory expr++ i expr– zwracają wartość przed lub po inkrementacji/dekrementacji.
Unary: Operatory jednostkowe zmieniają wartość na przeciwną lub modyfikują wartość zmiennej bezpośrednio.
Multiplicative: Operatory *, /, i % wykonują mnożenie, dzielenie oraz obliczanie reszty z dzielenia.
Additive: Operatory + i - wykonują dodawanie i odejmowanie.
Shift: Operatory przesunięcia bitowego zmieniają pozycje bitów w liczbie, co jest przydatne w operacjach na bitach.
Relational: Operatory porównania zwracają wartość logiczną w zależności od relacji między operandami.
Equality: Sprawdzają, czy dwa wyrażenia są równe lub różne.
Bitwise: Operatory bitowe wykonują operacje na poziomie bitów, przydatne w programowaniu niskopoziomowym.
Logical: Operatory logiczne wykonują operacje na wartościach logicznych (prawda/fałsz).
Ternary: Operator warunkowy, który działa jak uproszczony blok if-else, zwracając wartość na podstawie warunku.
Assignment: Przypisują wartości zmiennym, operatorzy z dodatkowymi znakami wykonują operacje oraz przypisania w jednym
kroku.
Comma: Operator przecinka umożliwia łączenie wielu wyrażeń w jednym kontekście, np. w pętli lub przy deklaracji
zmiennych.

# Operatory w Javie

Operator to symbol, który wykonuje określoną operację na jednym lub kilku operandach. Java dzieli operatory według
liczby operandów (unarne, binarne, ternarne) oraz według priorytetu wykonania.

---

## Tabela priorytetów operatorów

Im niższy numer, tym operator jest wykonywany **wcześniej**.

| Priorytet | Typ operatora | Operatory | Łączność |
| --- | --- | --- | --- |
| 1 | Postfix | `expr++`, `expr--` | lewo → prawo |
| 2 | Prefix / Unary | `++expr`, `--expr`, `+`, `-`, `~`, `!` | prawo → lewo |
| 3 | Multiplicative | `*`, `/`, `%` | lewo → prawo |
| 4 | Additive | `+`, `-` | lewo → prawo |
| 5 | Shift | `<<`, `>>`, `>>>` | lewo → prawo |
| 6 | Relational | `<`, `>`, `<=`, `>=`, `instanceof` | lewo → prawo |
| 7 | Equality | `==`, `!=` | lewo → prawo |
| 8 | Bitwise AND | `&` | lewo → prawo |
| 9 | Bitwise XOR | `^` | lewo → prawo |
| 10 | Bitwise OR | `|` | lewo → prawo |
| 11 | Logical AND | `&&` | lewo → prawo |
| 12 | Logical OR | `||` | lewo → prawo |
| 13 | Ternary | `? :` | prawo → lewo |
| 14 | Assignment | `=`, `+=`, `-=`, `*=`, `/=`, `%=`, `&=`, `^=`, `|=`, `<<=`, `>>=`, `>>>=` | prawo → lewo |

> Przecinek (`,`) w Javie **nie jest operatorem** – pełni rolę separatora (np. przy deklaracji zmiennych lub argumentach
metod), ale nie jest samodzielnym operatorem wyrażeniowym jak w C/C++.
> 

---

## Opis operatorów z przykładami

### 1. Postfix i Prefix (inkrementacja / dekrementacja)

Kluczowa różnica: **postfix** zwraca wartość **przed** zmianą, **prefix** zwraca wartość **po** zmianie.

```java
int a = 5;

int b = a++;  // b = 5, a = 6 (najpierw przypisz, potem zwiększ)
int c = ++a;  // c = 7, a = 7 (najpierw zwiększ, potem przypisz)

int d = a--;  // d = 7, a = 6
int e = --a;  // e = 5, a = 5
```

### 2. Operatory unarne

```java
int x = 5;
System.out.println(-x);   // -5  (zmiana znaku)
System.out.println(+x);   //  5  (bez efektu, stosowane dla czytelności)

boolean t = true;
System.out.println(!t);   // false (negacja logiczna)

int n = 0b1010;            // binarnie: 1010
System.out.println(~n);   // negacja bitowa: -(n+1) = -11
```

### 3. Multiplicative (, `/`, `%`)

```java
System.out.println(10 / 3);   // 3   (dzielenie całkowitoliczbowe)
System.out.println(10.0 / 3); // 3.3333... (dzielenie zmiennoprzecinkowe)
System.out.println(10 % 3);   // 1   (reszta z dzielenia)
System.out.println(-10 % 3);  // -1  (wynik ma znak lewego operandu)
```

### 4. Additive (`+`, )

Operator `+` służy też do **łączenia Stringów**:

```java
String s = "Wartość: " + 42;  // "Wartość: 42"
int suma = 3 + 4;              // 7
```

### 5. Operatory przesunięcia bitowego (`<<`, `>>`, `>>>`)

Przesunięcie bitowe to szybki sposób na mnożenie lub dzielenie przez potęgi dwójki.

| Operator | Nazwa | Opis |
| --- | --- | --- |
| `<<` | Left shift | Przesuwa bity w lewo, wypełnia zerami z prawej |
| `>>` | Arithmetic right shift | Przesuwa bity w prawo, **zachowuje bit znaku** |
| `>>>` | Logical (unsigned) right shift | Przesuwa bity w prawo, **wypełnia zerami z lewej** |

```java
int a = 8;          // binarnie: 0000 1000
System.out.println(a << 1);   // 16  – przesunięcie w lewo = *2
System.out.println(a >> 1);   // 4   – przesunięcie w prawo = /2

int b = -8;
System.out.println(b >> 1);   // -4  – zachowuje znak (arytmetyczne)
System.out.println(b >>> 1);  // duża dodatnia liczba – wypełnia zerami (logiczne)
```

### 6. Relational i `instanceof`

```java
System.out.println(5 > 3);    // true
System.out.println(5 == 5);   // true

String tekst = "Hello";
System.out.println(tekst instanceof String);  // true
System.out.println(null instanceof String);   // false (null nigdy nie jest instanceof)
```

**Od Javy 16** – Pattern Matching dla `instanceof` (nie trzeba już osobno rzutować):

```java
// Przed Javą 16:
if (obj instanceof String) {
    String s = (String) obj;
    System.out.println(s.length());
}

// Od Javy 16:
if (obj instanceof String s) {
    System.out.println(s.length()); // s jest już dostępne jako String
}
```

### 7. Equality (`==`, `!=`)

Dla typów prymitywnych porównuje **wartości**, dla referencyjnych porównuje **adresy w pamięci**:

```java
int a = 5, b = 5;
System.out.println(a == b);  // true

String s1 = new String("hello");
String s2 = new String("hello");
System.out.println(s1 == s2);      // false – różne obiekty w pamięci
System.out.println(s1.equals(s2)); // true  – ta sama zawartość
```

### 8–10. Operatory bitowe (`&`, `^`, `|`)

Wykonują operacje na poziomie pojedynczych bitów. Przydatne w programowaniu niskopoziomowym, flagach i maskach.

| Operator | Nazwa | Działanie |
| --- | --- | --- |
| `&` | AND | 1 tylko gdy oba bity = 1 |
| `^` | XOR | 1 tylko gdy bity są **różne** |
| `|` | OR | 1 gdy przynajmniej jeden bit = 1 |

```java
int a = 0b1010;  // 10
int b = 0b1100;  // 12

System.out.println(a & b);  // 0b1000 = 8
System.out.println(a ^ b);  // 0b0110 = 6
System.out.println(a | b);  // 0b1110 = 14
```

### 11–12. Logical AND i OR (`&&`, `||`) – Short-Circuit Evaluation

Kluczowa właściwość: **lenive wartościowanie** (short-circuit evaluation) – jeśli wynik jest znany po pierwszym
operandzie, drugi **nie jest w ogóle obliczany**.

```java
int x = 0;

// && – jeśli lewy operand to false, prawy NIE jest sprawdzany
if (x != 0 && 10 / x > 1) {  // bezpieczne – nie dojdzie do dzielenia przez 0
    System.out.println("ok");
}

// || – jeśli lewy operand to true, prawy NIE jest sprawdzany
if (x == 0 || 10 / x > 1) {  // bezpieczne
    System.out.println("ok");
}
```

Różnica między `&&` a `&`:

```java
// & (bitowy AND) – zawsze oblicza oba operandy, nawet dla boolean
if (x != 0 & 10 / x > 1) {  // niebezpieczne! prawy operand zawsze sprawdzany
    // może rzucić ArithmeticException
}
```

### 13. Ternary (`? :`)

Skrócony zapis `if-else`, który **zwraca wartość**:

```java
int a = 10, b = 20;
int max = (a > b) ? a : b;  // max = 20

String wynik = (max > 15) ? "duży" : "mały";  // "duży"
```

Można zagnieżdżać, ale dla czytelności lepiej użyć `if-else` przy więcej niż jednym poziomie.

### 14. Operatory przypisania

Złożone operatory przypisania to skróty: `a += b` to odpowiednik `a = a + b`.

```java
int x = 10;
x += 5;   // x = 15
x -= 3;   // x = 12
x *= 2;   // x = 24
x /= 4;   // x = 6
x %= 4;   // x = 2

// Bitowe przypisania:
x &= 0b1111;   // x = x & 0b1111
x |= 0b0001;   // x = x | 0b0001
x ^= 0b0011;   // x = x ^ 0b0011
x <<= 1;       // x = x << 1
x >>= 1;       // x = x >> 1
x >>>= 1;      // x = x >>> 1
```

> Złożone operatory przypisania zawierają **niejawne rzutowanie**. Na przykład `byte b = 5; b += 1;` działa poprawnie,
choć `b = b + 1;` wymagałoby jawnego rzutowania (`b = (byte)(b + 1)`).
>
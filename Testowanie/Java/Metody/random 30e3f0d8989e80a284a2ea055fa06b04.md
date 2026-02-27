# random

# Random

Oto tabelka w Markdown przedstawiająca różnice między metodami ints i nextInt w klasie Random:

| Cecha | `ints` | `nextInt` |
| --- | --- | --- |
| **Typ zwracany** | `IntStream` (strumień liczb całkowitych). | Pojedyncza wartość `int`. |
| **Zakres liczb** | Możliwość podania zakresu `[min, max)` lub całego zakresu `int`. | Domyślnie `[0, bound)` (jeśli podany). |
| **Liczba generowanych liczb** | Domyślnie nieskończony strumień lub określona liczba. | Generuje tylko jedną liczbę. |
| **Przykład wywołania** | `r.ints(5, 1, 10)` — 5 liczb z zakresu `[1, 10)`. | `r.nextInt(10)` — liczba `[0, 10)`. |
| **Zastosowanie** | Generowanie wielu liczb i praca z kolekcjami. | Generowanie pojedynczej liczby. |

| Nazwa | Metoda | Opis użycia | Przykład |
| --- | --- | --- | --- |
| Losowanie liczby całkowitej | `nextInt(bound)` | Generuje losową liczbę całkowitą z zakresu `[0, bound)`. | `Random r = new Random(); int x = r.nextInt(10); // x ∈ [0, 10)` |
| Losowanie liczby z zakresu | `nextInt(max - min) + min` | Generuje losową liczbę całkowitą z zakresu `[min, max)`. | `int x = r.nextInt(20 - 10) + 10; // x ∈ [10, 20)` |
| Generowanie liczby zmiennoprzecinkowej | `nextDouble()` | Zwraca liczbę zmiennoprzecinkową z zakresu `[0.0, 1.0)`. | `double d = r.nextDouble(); // d ∈ [0.0, 1.0)` |
| Losowanie wartości logicznej | `nextBoolean()` | Generuje losową wartość `true` lub `false`. | `boolean b = r.nextBoolean(); // b ∈ {true, false}` |
| Generowanie strumienia liczb | `ints(count, min, max)` | Tworzy strumień losowych liczb całkowitych z zakresu `[min, max)`. | `r.ints(5, 1, 10).forEach(System.out::println);` |
| Symulacja rzutu kostką | `nextInt(6) + 1` | Losuje wynik rzutu standardową kostką (1–6). | `int roll = r.nextInt(6) + 1; // roll ∈ [1, 6]` |
| Losowanie ciągu znaków | Custom (z wykorzystaniem `nextInt`) | Generuje losowy ciąg znaków z podanych znaków. | `char c = (char)(r.nextInt(26) + 'a'); // Losowy mały znak alfabetu` |
| Generowanie daty | Kombinacja z `nextLong()` | Generuje losową datę w ustalonym zakresie. | `long millis = r.nextLong(); LocalDateTime.now().plusDays(millis);` |
| Symulacja rzutu monetą | `nextBoolean()` | Losuje wynik “orzeł” lub “reszka”. | `String wynik = r.nextBoolean() ? "orzeł" : "reszka";` |
| Losowanie elementu z listy | Kombinacja z `nextInt()` | Wybiera losowy element z listy. | `String item = list.get(r.nextInt(list.size()));` |

“Pseudolosowe” liczby to takie, które:

Wyglądają na losowe,
Są generowane w sposób deterministyczny przez algorytm,
Można je odtworzyć, jeśli zna się ziarno (seed) używane przez generator.
W praktyce oznacza to, że generator pseudolosowy nie jest “prawdziwie losowy”. Zamiast korzystać z fizycznych procesów losowych, generuje liczby na podstawie wzoru matematycznego i ziarna, które jest punktem wyjścia dla obliczeń.

```java
import java.util.Random;

public class Main {
    public static void main(String[] args) {
        Random random = new Random(); // Domyślne ziarno (czas systemowy)

        // Przykłady losowych wartości
        int randomInt = random.nextInt(100); // Liczba całkowita 0-99
        double randomDouble = random.nextDouble(); // Liczba zmiennoprzecinkowa 0.0-1.0
        boolean randomBoolean = random.nextBoolean(); // Wartość true lub false

        System.out.println("Liczba całkowita: " + randomInt);
        System.out.println("Liczba zmiennoprzecinkowa: " + randomDouble);
        System.out.println("Wartość logiczna: " + randomBoolean);
    }
}
```

Różnice między Random a SecureRandom
Random: szybki i odpowiedni do zastosowań ogólnych (np. gier). Nie jest bezpieczny kryptograficznie.
SecureRandom: wolniejszy, ale oparty na źródłach losowości kryptograficznej, więc lepszy do aplikacji wymagających wysokiego poziomu bezpieczeństwa (np. klucze szyfrujące).

```java
import java.security.SecureRandom;

public class Main {
    public static void main(String[] args) {
        SecureRandom secureRandom = new SecureRandom();
        int secureInt = secureRandom.nextInt(100); // Losowa liczba 0-99
        System.out.println("Secure liczba całkowita: " + secureInt);
    }
}
```
# bigDecimal

# BigDecimal

W Java klasa BigDecimal jest częścią pakietu java.math i służy do reprezentowania liczb o wysokiej precyzji. Jest szczególnie przydatna w aplikacjach, które wymagają bardzo dokładnych obliczeń na liczbach zmiennoprzecinkowych (np. aplikacje finansowe, rachunkowe).

Główne cechy BigDecimal
Precyzja
BigDecimal przechowuje liczby w sposób dokładny, bez problemów związanych z zaokrągleniami typowymi dla typów double czy float.

Kontrolowane zaokrąglanie
Możesz dokładnie określić sposób zaokrąglania wyników (np. przez RoundingMode).

Niezmienność
Obiekty BigDecimal są niemutowalne, co oznacza, że każda operacja zwraca nowy obiekt.

Konstrukcja obiektu BigDecimal
BigDecimal można utworzyć za pomocą:

- String
- double (ale jest to odradzane ze względu na błędy precyzji)
- int lub long

```java
import java.math.BigDecimal;

public class BigDecimalExample {
    public static void main(String[] args) {
        BigDecimal bd1 = new BigDecimal("10.123");  // Precyzyjne
        BigDecimal bd2 = new BigDecimal(10.123);    // Nieprecyzyjne
        BigDecimal bd3 = BigDecimal.valueOf(10.123); // Bezpieczne
        System.out.println(bd1); // 10.123
        System.out.println(bd2); // 10.122999999999999444888487687421729564666748046875
        System.out.println(bd3); // 10.123
    }
}
```

Operacje na BigDecimal
Operacje takie jak dodawanie, odejmowanie, mnożenie i dzielenie wymagają wywołania odpowiednich metod, np.:

add(BigDecimal augend) - Dodawanie.
subtract(BigDecimal subtrahend) - Odejmowanie.
multiply(BigDecimal multiplicand) - Mnożenie.
divide(BigDecimal divisor) - Dzielenie (możesz podać tryb zaokrąglania).

```java
BigDecimal num1 = new BigDecimal("10.25");
BigDecimal num2 = new BigDecimal("2.5");

BigDecimal sum = num1.add(num2);          // Dodawanie
BigDecimal difference = num1.subtract(num2); // Odejmowanie
BigDecimal product = num1.multiply(num2);    // Mnożenie
BigDecimal quotient = num1.divide(num2, 2, RoundingMode.HALF_UP); // Dzielenie z zaokrąglaniem

System.out.println("Suma: " + sum);          // Suma: 12.75
System.out.println("Różnica: " + difference); // Różnica: 7.75
System.out.println("Iloczyn: " + product);    // Iloczyn: 25.625
System.out.println("Iloraz: " + quotient);    // Iloraz: 4.10
```

Zalety BigDecimal
Dokładność i brak błędów zaokrągleń.
Możliwość precyzyjnego kontrolowania zaokrągleń i skalowania.
Wady BigDecimal
Wolniejsze w porównaniu do double i float.
Bardziej skomplikowane API.
BigDecimal jest niezastąpiony w miejscach, gdzie precyzja jest ważniejsza od wydajności, np. w systemach bankowych i księgowych.

## Metody

| **Metoda** | **Opis** | **Przykład Użycia** |
| --- | --- | --- |
| **`add(BigDecimal augend)`** | Dodaje wskazaną wartość do bieżącego obiektu. | `BigDecimal sum = new BigDecimal("10.5").add(new BigDecimal("2.3")); // 12.8` |
| **`subtract(BigDecimal subtrahend)`** | Odejmuje wskazaną wartość od bieżącego obiektu. | `BigDecimal diff = new BigDecimal("10.5").subtract(new BigDecimal("2.3")); // 8.2` |
| **`multiply(BigDecimal multiplicand)`** | Mnoży bieżący obiekt przez wskazaną wartość. | `BigDecimal prod = new BigDecimal("10").multiply(new BigDecimal("2.5")); // 25.0` |
| **`divide(BigDecimal divisor, int scale, RoundingMode mode)`** | Dzieli bieżący obiekt przez wskazaną wartość z określeniem skali i trybu zaokrąglania. | `BigDecimal quot = new BigDecimal("10").divide(new BigDecimal("3"), 2, RoundingMode.HALF_UP); // 3.33` |
| **`setScale(int scale, RoundingMode mode)`** | Ustawia skalę (liczbę miejsc po przecinku) z określonym trybem zaokrąglania. | `BigDecimal scaled = new BigDecimal("10.25678").setScale(2, RoundingMode.HALF_UP); // 10.26` |
| **`compareTo(BigDecimal val)`** | Porównuje bieżący obiekt z innym. Zwraca -1, 0 lub 1 (mniej, równe, więcej). | `int result = new BigDecimal("10.5").compareTo(new BigDecimal("10.5")); // 0` |
| **`equals(Object obj)`** | Sprawdza, czy obiekty są równe (w tym skalę). | `boolean isEqual = new BigDecimal("10.0").equals(new BigDecimal("10.00")); // false` |
| **`max(BigDecimal val)`** | Zwraca większą z dwóch wartości. | `BigDecimal max = new BigDecimal("10.5").max(new BigDecimal("2.3")); // 10.5` |
| **`min(BigDecimal val)`** | Zwraca mniejszą z dwóch wartości. | `BigDecimal min = new BigDecimal("10.5").min(new BigDecimal("2.3")); // 2.3` |
| **`negate()`** | Zwraca liczbę o przeciwnym znaku. | `BigDecimal neg = new BigDecimal("10.5").negate(); // -10.5` |
| **`abs()`** | Zwraca wartość bezwzględną. | `BigDecimal abs = new BigDecimal("-10.5").abs(); // 10.5` |
| **`pow(int n)`** | Podnosi bieżącą wartość do potęgi `n`. | `BigDecimal pow = new BigDecimal("2").pow(3); // 8` |
| **`remainder(BigDecimal divisor)`** | Oblicza resztę z dzielenia bieżącej wartości przez podaną. | `BigDecimal rem = new BigDecimal("10").remainder(new BigDecimal("3")); // 1` |
| **`stripTrailingZeros()`** | Usuwa końcowe zera z liczby dziesiętnej. | `BigDecimal stripped = new BigDecimal("10.5000").stripTrailingZeros(); // 10.5` |
| **`toPlainString()`** | Zwraca reprezentację liczby jako łańcuch bez notacji naukowej. | `String plain = new BigDecimal("1E+3").toPlainString(); // "1000"` |
| **`toString()`** | Zwraca reprezentację liczby w postaci ciągu znaków. | `String str = new BigDecimal("10.5").toString(); // "10.5"` |
| **`valueOf(double val)`** | Tworzy obiekt `BigDecimal` z podanej wartości typu `double`. | `BigDecimal bd = BigDecimal.valueOf(10.5);` |
| **`scale()`** | Zwraca skalę, czyli liczbę miejsc po przecinku w bieżącym obiekcie. | `int scale = new BigDecimal("10.50").scale(); // 2` |
| **`precision()`** | Zwraca liczbę cyfr w liczbie. | `int precision = new BigDecimal("123.45").precision(); // 5` |
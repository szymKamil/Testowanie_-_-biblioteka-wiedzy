# consumerInterface

# Consumer

Consumer to funkcjonalny interfejs w Javie, który znajduje się w pakiecie java.util.function. Jego rolą jest wykonywanie operacji na przekazanym argumencie, bez zwracania żadnego wyniku.

Consumer definiuje jedną abstrakcyjną metodę:

```java
void accept(T t);
```

Do czego służy Consumer?
Consumer jest używany wtedy, gdy chcemy wykonać operację na danym obiekcie, ale nie zależy nam na wartości zwracanej. To przydatne w kontekście przetwarzania elementów, np. w strumieniach (Streams), do operacji takich jak wyświetlanie, logowanie, czy zmiany na obiekcie.

```java
import java.util.Arrays;
import java.util.List;
import java.util.function.Consumer;

public class ConsumerExample {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("Alice", "Bob", "Charlie");

        Consumer<String> printName = name -> System.out.println(name);
        names.forEach(printName);

        // Alternatywnie, bezpośrednio w forEach
        names.forEach(name -> System.out.println(name));
    }
}
```

W tym przykładzie Consumer printName przyjmuje String i wyświetla go na konsoli. Dzięki forEach możemy przekazać Consumer jako działanie do wykonania na każdym elemencie listy.

Łączenie Consumer-ów
Consumer ma metodę andThen, która pozwala łączyć dwa obiekty Consumer, aby wykonywały swoje operacje sekwencyjnie na tym samym obiekcie:

```java
Consumer<String> printUpperCase = name -> System.out.println(name.toUpperCase());
Consumer<String> printLowerCase = name -> System.out.println(name.toLowerCase());

printUpperCase.andThen(printLowerCase).accept("Alice");
```

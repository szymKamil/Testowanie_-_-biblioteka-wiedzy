# methods

# Methods

## Methods overloading vs overriding

### Przeciążanie metod (Method Overloading)

Definicja: Przeciążanie metod polega na tworzeniu w tej samej klasie kilku metod o tej samej nazwie, ale różniących się parametrami. Parametry mogą różnić się typem, kolejnością lub liczbą. Przeciążanie pozwala na użycie tej samej nazwy metody do wykonywania podobnych zadań, zależnie od przekazanych argumentów.

Cechy:

Dzieje się na etapie kompilacji (ang. compile time).
Metody różnią się parametrami, ale mają tę samą nazwę.
Może występować w jednej klasie (nie wymaga dziedziczenia).

```java
public class MathOperations {
    public int add(int a, int b) {
        return a + b;
    }

    public double add(double a, double b) {
        return a + b;
    }
}
```

`Return` może być tego samego lub innego typu.

---

### Przesłanianie metod (Method Overriding)

Definicja: Przesłanianie metod występuje, gdy klasa dziedzicząca zmienia implementację metody odziedziczonej z klasy nadrzędnej. Przesłanianie pozwala na dostosowanie zachowania metod w podklasie bez zmieniania klasy bazowej.

Cechy:

Dzieje się na etapie wykonania (ang. runtime).
Metoda w klasie potomnej musi mieć taką samą nazwę, typ zwracany i parametry (argumenty), co metoda w klasie bazowej. // Typ zwracany może być subklasą typu rodzica (nadrzędnego).
Wymaga dziedziczenia (musi występować relacja is-a).
Nie może mieć mniejszego zakresu dostepu (bardziej restrykcyjnego). Może mięc jednak wyższy zakres. Metoda protected nie może być nadpisana metodą prywatną (private). Jednak może być publiczną (public).
Możemy nadpisać (przesłonić) metodę dziedziczoną. Tj. występuje to tylko gdy przesnonięcie następuje w klasie dziedziczącej.
Konstruktory i prywatne metody nie mogą być przesłonięte.
Nie możemy przesłonić metod `final` (finalnych).
Jeśli przesłoniemy metodę, wciąż możemy się do niej odwołać poprzez `super + .nazwaMetody()`.

Przy overriding zalecane dodania `@Override` (adnotation / adnotacja) przed nazwą metody - nie jest obowiązkowe, ale pozwala kompilatorowi wskazać błąd w metodzie, gdy zrobimy to błędnie.

Nie możemy przesłaniać metod statycznych `static`, tylko instancyjne.

```java
class Animal {
    public void makeSound() {
        System.out.println("Animal sound");
    }
}

class Dog extends Animal {
    @Override
    public void makeSound() {
        System.out.println("Bark");
    }
}
```

| Cecha | Przeciążanie (Overloading) | Przesłanianie (Overriding) |
| --- | --- | --- |
| Etap | Kompilacja (compile time) | Wykonanie (runtime) |
| Wymaganie dziedziczenia | Nie | Tak |
| Parametry | Różne parametry (typy, liczba, kolejność) | Te same parametry jak w metodzie bazowej |
| Cel | Wykorzystanie jednej nazwy do różnych zadań | Zmiana działania metody w klasie potomnej |

## Abstrakcyjne metody

Abstrakcyjne metody nie mają implementacji. To oznacza, że ich ciała (czyli logika wykonania) nie są zdefiniowane w klasie, w której są zadeklarowane. Zamiast tego, klasy pochodne muszą implementować te metody i dostarczyć odpowiednią logikę, w tym również instrukcję return.
Zob. też abstrakcyjne obiekty: [Obiekty abstrakcyjne](../Objects/objects.md#abstrakcyjne-klasy-w-javie)

### **Expression (wyrażenie)**

**Wyrażenie** to fragment kodu, który oblicza jakąś wartość. Może być prostym obliczeniem, odwołaniem do zmiennej, wywołaniem metody lub kombinacją tych elementów. Wynikiem wyrażenia zawsze jest jakaś wartość.

- **Przykłady wyrażeń:**
    - `2 + 3` (wyrażenie arytmetyczne, które zwraca wartość 5)
    - `x * y` (oblicza iloczyn zmiennych `x` i `y`)
    - `a > b` (wyrażenie logiczne, które zwraca wartość `true` lub `false`)
    - `someMethod()` (wywołanie metody, które może zwrócić jakąś wartość)

Każde wyrażenie może zostać częścią większej struktury, takiej jak instrukcja.

## Ukrywanie metod

Ukrywanie metod (ang. method hiding) w Javie
Ukrywanie metod występuje, gdy statyczna metoda klasy nadrzędnej jest redefiniowana przez klasę pochodną, ale nie jest to klasyczne nadpisywanie (ang. overriding). Zamiast tego, metoda w klasie pochodnej ukrywa metodę klasy nadrzędnej.

| **Cecha** | **Ukrywanie metod** | **Nadpisywanie metod** |
| --- | --- | --- |
| **Typ metody** | Dotyczy tylko metod statycznych (`static`). | Dotyczy tylko metod niestatycznych. |
| **Zachowanie w czasie wykonania** | Wywoływana metoda zależy od typu referencji. | Wywoływana metoda zależy od typu obiektu. |
| **Dziedziczenie** | Statyczna metoda w klasie pochodnej ukrywa metodę w klasie nadrzędnej. | Metoda w klasie pochodnej nadpisuje metodę w klasie nadrzędnej. |
| **Polimorfizm** | Nie wspiera polimorfizmu. | Wspiera polimorfizm. |
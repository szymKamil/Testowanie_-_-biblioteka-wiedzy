# generics

# Generics

Podejście generyczne w Javie umożliwia pisanie klas, interfejsów i metod, które są bardziej uniwersalne, ponieważ mogą pracować z różnymi typami danych. Oto kluczowe informacje na temat generics, oparte na dostarczonej prezentacji:

Czym są generics?
Generics pozwalają na tworzenie klas, rekordów, interfejsów oraz metod, które są typowane dynamicznie przy użyciu parametrów typów. Zamiast określać konkretny typ podczas definiowania klasy, używamy symbolu zastępczego, np. T, który zostanie sprecyzowany później.

Deklarowanie klasy generycznej
Przy deklaracji klasy generycznej po jej nazwie umieszczamy nawiasy ostre (<>) z literą, np. T (od Type). To jest identyfikator typu, który może być dowolną literą lub słowem, ale T jest najczęściej używany.

Zastosowanie w polach klasy
W klasie generycznej typ pola może być zastąpiony symbolem T, co oznacza, że może przyjąć dowolny typ. T określa typ, który zostanie wskazany, gdy klasa zostanie użyta.

Użycie klasy generycznej jako typu referencyjnego
W praktyce, typ generyczny może być zadeklarowany podczas tworzenia zmiennych, jak np. w przypadku ArrayList. ArrayList to typ referencyjny, a String to parametr typu, określony w nawiasach ostrych. Dzięki temu typ listy jest jednoznacznie określony.

Popularność w bibliotekach Javy
Wiele bibliotek Javy jest opartych na klasach i interfejsach generycznych, jak np. ArrayList czy HashMap. Pisanie własnych klas generycznych pomaga lepiej zrozumieć tę koncepcję i stosować ją skutecznie.

Generics w Javie są bardzo przydatne, ponieważ umożliwiają pisanie bardziej elastycznego i bezpiecznego kodu. Dzięki nim można uniknąć wielu błędów związanych z niezgodnością typów, które mogą pojawić się podczas pracy z typami ogólnymi. Aby lepiej zrozumieć koncepcję, spójrzmy na kilka przykładów:

```java
public class Box<T> {
    private T item;

    public void setItem(T item) {
        this.item = item;
    }

    public T getItem() {
        return item;
    }
}
```

W powyższym przykładzie Box to klasa generyczna, gdzie T jest symbolem zastępującym dowolny typ. Można teraz stworzyć obiekty Box dla różnych typów, np. Box dla liczb całkowitych lub Box dla napisów:

```java
Box<Integer> integerBox = new Box<>();
integerBox.setItem(123);
System.out.println("Integer value: " + integerBox.getItem());

Box<String> stringBox = new Box<>();
stringBox.setItem("Hello Generics");
System.out.println("String value: " + stringBox.getItem());
```

Generics mogą być stosowane również na poziomie metod, umożliwiając pisanie metod, które pracują z różnymi typami.

```java
public class GenericsExample {

    public static <T> void printArray(T[] array) {
        for (T element : array) {
            System.out.print(element + " ");
        }
        System.out.println();
    }
}
```

Tutaj printArray jest metodą generyczną, która akceptuje tablicę dowolnego typu T. Możemy wywołać tę metodę z tablicami różnych typów:

```java
Integer[] intArray = {1, 2, 3, 4};
String[] strArray = {"Apple", "Banana", "Cherry"};

GenericsExample.printArray(intArray);
GenericsExample.printArray(strArray);
```

Przykład: Ograniczenia generyków (bounded generics)
Czasem chcemy ograniczyć, jaki typ może być użyty jako parametr typu. Na przykład:

```java
public class NumberBox<T extends Number> {
    private T number;

    public void setNumber(T number) {
        this.number = number;
    }

    public T getNumber() {
        return number;
    }
}
```

W tym przypadku T jest ograniczone do typów, które są podtypami Number, co oznacza, że możemy używać NumberBox z Integer, Double, itp., ale nie z String.

```java
NumberBox<Integer> integerBox = new NumberBox<>();
integerBox.setNumber(42);

NumberBox<Double> doubleBox = new NumberBox<>();
doubleBox.setNumber(3.14);
```

Zalety generics
Bezpieczeństwo typów: Java sprawdza typy w czasie kompilacji, co zmniejsza ryzyko błędów w czasie działania.
Reużywalność kodu: Dzięki generics można pisać ogólne klasy i metody, które działają z wieloma typami, bez potrzeby duplikacji kodu.
Generics są potężnym narzędziem, które ułatwia tworzenie bardziej uniwersalnego, czytelnego i bezpiecznego kodu.

`T` jest symbolem używanym w generykach Javy jako wskaźnik typu, który będzie określony w momencie tworzenia instancji klasy lub wywołania metody. Nie jest to jednak jedyny symbol, którego można używać; T jest po prostu konwencją, a w praktyce można spotkać różne symbole typów, które pełnią specjalne role:

T - od “Type” - często używany dla typów ogólnych.
E - od “Element” - zwykle używany w kolekcjach (np. ArrayList), gdzie E reprezentuje typ elementów kolekcji.
K i V - od “Key” i “Value” - używane w strukturach danych opartych na kluczach i wartościach, takich jak Map<K, V>.
N - od “Number” - czasem używany w kontekście liczbowym.
S, U, V - stosowane w bardziej złożonych sytuacjach, gdy potrzeba kilku parametrów typów.

### Jak działa T extends?

Kiedy deklarujemy T extends SomeClass, to określamy, że T może być dowolnym typem, który jest podtypem SomeClass (klasy, interfejsu lub ich kombinacji). Jest to szczególnie przydatne, gdy chcemy użyć metod lub pól SomeClass wewnątrz klasy lub metody generycznej, bez względu na dokładny typ T.

Przykład: Ograniczenie typu w klasie
Załóżmy, że chcemy stworzyć klasę NumberBox, która może przechowywać tylko liczby. Możemy użyć T extends Number, aby upewnić się, że T będzie podtypem klasy Number (np. Integer, Double itp.):

```java
public class NumberBox<T extends Number> {
    private T number;

    public NumberBox(T number) {
        this.number = number;
    }

    public T getNumber() {
        return number;
    }

    public double getDoubleValue() {
        return number.doubleValue();  // Możemy użyć metody doubleValue(), ponieważ T jest podtypem Number
    }
}
```

W tym przykładzie NumberBox może przechowywać tylko typy liczbowe, ponieważ T musi rozszerzać (extends) klasę Number. Dzięki temu mamy dostęp do metod klasy Number, takich jak doubleValue(), intValue(), itd.

Korzyści z użycia T extends
Bezpieczeństwo typów: Ograniczenie typu sprawia, że nie można przypadkowo użyć niekompatybilnego typu. W przykładzie z NumberBox, nie można utworzyć NumberBox, ponieważ String nie jest podtypem Number.
Dostęp do specyficznych metod: Dzięki T extends mamy dostęp do metod i pól klasy bazowej lub interfejsu, co ułatwia pisanie bardziej funkcjonalnego kodu.
Elastyczność kodu: Nadal możemy tworzyć elastyczne klasy, ale jednocześnie kontrolujemy, z jakimi typami mogą one pracować.

T extends z wieloma ograniczeniami
Java pozwala na ustawienie wielu ograniczeń przy użyciu &:

```java
public class AdvancedBox<T extends Number & Comparable<T>> {
    // Klasa T musi być podtypem Number i implementować Comparable
}
```

## Metody

Aby stworzyć generyczną, statyczną metodę w Javie, używamy składni  w nagłówku metody. Dzięki temu metoda może przyjmować różne typy danych jako parametr i zwracać wartość o odpowiednim typie. Poniżej przedstawiam przykładową definicję generycznej, statycznej metody:

```java
public class GenericExample {

    // Definicja generycznej, statycznej metody
    public static <T> void printArray(T[] array) {
        for (T element : array) {
            System.out.print(element + " ");
        }
        System.out.println();
    }

    public static void main(String[] args) {
        Integer[] intArray = {1, 2, 3, 4, 5};
        String[] stringArray = {"A", "B", "C"};

        // Wywołanie generycznej metody z różnymi typami
        printArray(intArray);     // Typ Integer
        printArray(stringArray);   // Typ String
    }
}
```

Typ T jest określany automatycznie na podstawie typu przekazanego argumentu.
 musi być zawsze zadeklarowane przed typem zwracanym (void, T itd.) w sygnaturze metody.

Oczywiście! Jeśli chcemy ograniczyć typy, które mogą być używane jako parametr generyczny, możemy użyć słowa kluczowego extends. Dzięki temu metoda lub klasa może przyjmować tylko typy będące podtypami określonej klasy lub implementujące dany interfejs.

Oto przykład statycznej, generycznej metody, która akceptuje listę obiektów dowolnego typu dziedziczącego po klasie Number:

```java
import java.util.List;

public class GenericExample {

    // Statyczna metoda generyczna, akceptująca typy będące podtypami Number
    public static <T extends Number> double sumList(List<T> numbers) {
        double sum = 0.0;
        for (T number : numbers) {
            sum += number.doubleValue(); // Każdy Number ma metodę doubleValue()
        }
        return sum;
    }

    public static void main(String[] args) {
        List<Integer> intList = List.of(1, 2, 3, 4, 5);
        List<Double> doubleList = List.of(1.5, 2.5, 3.5);

        // Wywołanie metody generycznej z listami Integer i Double
        System.out.println("Suma Integer: " + sumList(intList));
        System.out.println("Suma Double: " + sumList(doubleList));
    }
}
```

Deklaracja  przed typem zwracanym (double) ogranicza typ T do klas dziedziczących po Number (np. Integer, Double, Float itp.).
Metoda sumList: przyjmuje listę typu List, gdzie T to podtyp Number. Każdy element T może zatem używać metod klasy Number, takich jak doubleValue().

W Javie możemy użyć znaku `&`, aby wymusić, że parametr generyczny musi implementować więcej niż jeden interfejs lub dziedziczyć po określonej klasie i jednocześnie implementować interfejs.

```java
public static <T extends Number & Comparable<T>> T max(T a, T b) {
    return (a.compareTo(b) > 0) ? a : b;
}
```

## Wildcard

Wildcard (? extends T) w tym przypadku jest używane w kontekście typów dziedziczących.
Oznacza to, że metoda printList przyjmuje listę obiektów, które są dowolnym typem, który dziedziczy po klasie T (lub są tym samym typem co T).
`?` jest wildcard (symbol zastępujący dowolny typ).
`extends T` oznacza, że wildcard reprezentuje typ, który dziedziczy po T (może to być T lub dowolny jego podtyp).
Można to traktować jako ograniczenie typu dla parametru listy. Oznacza to, że lista może zawierać obiekty typu T lub jego podtypów.
Zatem List<? extends T> oznacza, że lista może zawierać obiekty typu T lub dowolnego podtypu klasy T.

Cel
Można używać wielu typów, aby ustawić bardziej restrykcyjne górne ograniczenie (upper bound) dla typu generycznego przy pomocy znaku &.

Zasady
Ograniczenie typu:

Typ argumentu musi implementować wszystkie wymienione interfejsy.
Typ argumentu musi być podtypem każdej klasy wskazanej w ograniczeniu.
Kolejność deklaracji:

Można rozszerzać tylko jedną klasę (albo żadną).
Można implementować zero lub więcej interfejsów.
Kiedy używasz klasy i interfejsów w ograniczeniu, klasa musi być zadeklarowana jako pierwsza.
Składnia:

Używamy słowa kluczowego extends zarówno dla klasy, jak i interfejsów.
Symbol & oznacza, że każdy typ generyczny musi być podtypem wszystkich wymienionych typów.
Przykład
Dla klasy i interfejsów zadeklarowanych jak poniżej:

```java

interface InterfaceA {}
interface InterfaceB {}
abstract class AbstractClassA {}
```

Ograniczenie można zdefiniować w następujący sposób:

```java
public class GenericClass<T extends AbstractClassA & InterfaceA & InterfaceB> {}
```

AbstractClassA jest klasą i musi być pierwsza na liście.
InterfaceA i InterfaceB są interfejsami i następują po klasie.

```java
public static void printList(List<? extends T> list) {
    for (T element : list) {
        System.out.println(element);
    }
}
```

| **Typ** | **Przykład** |
| --- | --- |
| **Wildcard (Unbounded)** | `List<?>` |
| **Wildcard (Upper Bound)** | `List<? extends T>` |
| **Wildcard (Lower Bound)** | `List<? super T>` |

Unbounded (?): Wildcard bez żadnych ograniczeń, oznaczający dowolny typ.
Upper Bound (? extends T): Wildcard dla typów, które są T lub jego podtypami.
Lower Bound (? super T): Wildcard dla typów, które są T lub jego supertypami.

## Erasure

Erasure (ang. Type Erasure) w Javie to proces, w którym informacje o typach generycznych są usuwane podczas kompilacji, tak aby program był kompatybilny z wcześniejszymi wersjami Javy, które nie wspierały generyków.

Wyjaśnienie:
W czasie kompilacji generics w Javie są “usuwane” (ang. erased) i zastępowane odpowiednimi typami supertypów, które zapewniają zgodność z zasadą typów surowych (ang. raw types). Oznacza to, że po skompilowaniu kodu, w pliku bajtowym (bytecode) nie ma już informacji o parametrze typu, a zamiast tego stosowane są odpowiednie typy bazowe.

```java
class Box<T> {
    private T value;

    public void setValue(T value) {
        this.value = value;
    }

    public T getValue() {
        return value;
    }
}
```

W czasie kompilacji, dla typu T, erasure usunie informację o tym parametrze typu. Po kompilacji, w pliku bajtowym będzie to wyglądać tak, jakby klasa Box była typem klasy z surowym typem (czyli bez parametrów typu):

```java
class Box {
    private Object value;

    public void setValue(Object value) {
        this.value = value;
    }

    public Object getValue() {
        return value;
    }
}
```

Dlaczego erasure jest potrzebne?
- Zgodność wsteczna: Java musiała zachować kompatybilność z istniejącymi wersjami, które nie wspierały generyków. Dzięki erasure, kod z generics może działać w starszych wersjach Javy, które nie miały wsparcia dla parametrów typu.

- Optymalizacja: Erasure pozwala na eliminację dodatkowych informacji o typach generycznych w czasie działania programu, co zmniejsza rozmiar plików klas (bytecode).

Co się dzieje podczas erasure?
- Usuwanie parametrów typu: Parametry typu są usuwane, a wszystkie operacje na tych typach są zamieniane na operacje na obiektach typu surowego (najczęściej Object).
- Zastępowanie parametrów typu: Jeśli masz metodę typu T (np. T method(T arg)), po erasure jej typ zostanie zmieniony na typ surowy, np. Object.

Ograniczenia wynikające z erasure:
- Brak dostępu do typów generycznych w czasie wykonania: Ze względu na erasure, informacje o typach generycznych są usuwane w czasie kompilacji, dlatego nie możemy sprawdzić w czasie działania programu, jakiego typu obiekty są przechowywane w generycznych kolekcjach.
- Nie ma przeciążenia na podstawie typów generycznych: Ponieważ typy są usuwane podczas erasure, nie możesz mieć dwóch metod, które różnią się tylko typami generycznymi (np. public void method(List list) i public void method(List list)).

W skrócie, Wildcardy i typy generyczne są usuwane w czasie kompilacji. Można to obejśc np. w ten sposób:

```java
public static void testList(List<?> list) {

        for (var element : list) {
            if (element instanceof String s) {
                System.out.println("String: " + s.toUpperCase());
            } else if (element instanceof Integer i) {
                System.out.println("Integer: " + i.floatValue());
            }
        }
    }
```
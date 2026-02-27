# nestedClass

# Nested Class

W Javie nested class (klasa zagnieżdżona) to klasa zdefiniowana wewnątrz innej klasy. Nested classes są zwykle stosowane w celu grupowania kodu, który jest logicznie powiązany z klasą zewnętrzną (outer class), a także mają dostęp do jej pól oraz metod (nawet prywatnych). W Javie występują dwa główne rodzaje klas zagnieżdżonych:

Static nested class:

Jest zdefiniowana jako klasa statyczna (static), przez co działa bardziej niezależnie od instancji klasy zewnętrznej.
Może korzystać z pól i metod statycznych klasy zewnętrznej, ale nie ma bezpośredniego dostępu do jej niestatycznych elementów.
Używana jest zwykle, gdy logika tej klasy nie potrzebuje instancji klasy zewnętrznej lub może działać bez niej.

```java
public class OuterClass {
    private static String staticField = "Static Field";

    public static class StaticNestedClass {
        public void display() {
            System.out.println("Accessing: " + staticField);
        }
    }
}
```

```java
OuterClass.StaticNestedClass nested = new OuterClass.StaticNestedClass();
nested.display();
```

W przypadku static nested class (klasy zagnieżdżonej statycznej), nie potrzebujesz instancji klasy zewnętrznej. Możesz po prostu bezpośrednio utworzyć instancję klasy zagnieżdżonej, ponieważ działa ona bardziej niezależnie od klasy zewnętrznej i nie wymaga dostępu do jej niestatycznych pól i metod.

```java
OuterClass.StaticNestedClass nested = new OuterClass.StaticNestedClass(); // Tworzenie instancji klasy zagnieżdżonej statycznej
nested.display();                                                         // Wywołanie metody w klasie zagnieżdżonej
```

1. Inner class:

Jest zagnieżdżona bez słowa kluczowego static i wymaga instancji klasy zewnętrznej, aby działać.
Ma dostęp do wszystkich pól i metod klasy zewnętrznej, w tym prywatnych.
Używana jest, gdy klasa zagnieżdżona jest ściśle związana z instancją klasy zewnętrznej i często ma sens tylko w jej kontekście.

```java
public class OuterClass {
    private String instanceField = "Instance Field";

    public class InnerClass {
        public void display() {
            System.out.println("Accessing: " + instanceField);
        }
    }
}
```

W przypadku inner class (czyli zagnieżdżonej klasy niestatycznej), najpierw musisz utworzyć instancję klasy zewnętrznej (outer class), a następnie na jej podstawie utworzyć instancję klasy zagnieżdżonej. Wynika to z faktu, że inner class jest powiązana z instancją klasy zewnętrznej i ma dostęp do jej niestatycznych pól i metod.

```java
OuterClass outer = new OuterClass();               // Tworzenie instancji klasy zewnętrznej
OuterClass.InnerClass inner = outer.new InnerClass(); // Tworzenie instancji klasy zagnieżdżonej
inner.display();                                   // Wywołanie metody w klasie zagnieżdżonej
//lub
var inner = new OuterClass().new InnerClass(); //+<> je\sli potrzebne, tj InnerClass()<>;
```

1. Local class

Local class to rodzaj klasy zagnieżdżonej (nested class) w Javie, która jest zdefiniowana wewnątrz metody, bloku lub konstruktora danej klasy. Klasa lokalna jest więc częścią metody i ma dostęp do jej zmiennych oraz parametrów. Poniżej znajdziesz kilka kluczowych cech klas lokalnych:

Cechy klas lokalnych
Lokalizacja w metodzie: Lokalna klasa jest zdefiniowana wewnątrz metody, bloku kodu (if, for itp.) lub konstruktora.
Zakres dostępności: Można ją wykorzystać wyłącznie wewnątrz metody lub bloku, w którym została zdefiniowana.
Dostęp do zmiennych metody: Ma dostęp do zmiennych lokalnych, parametrów i innych elementów metody, o ile są one oznaczone jako final lub “efektywnie finalne” (czyli nie zmieniają swojej wartości po pierwszym przypisaniu).
Słaba widoczność: Klasa lokalna jest widoczna tylko wewnątrz metody, w której została zadeklarowana, więc nie można jej używać poza tym kontekstem.

```java
public class OuterClass {
public void someMethod() {
int number = 10; // efektywnie finalna zmienna lokalna

        // Definicja klasy lokalnej wewnątrz metody
        class LocalClass {
            public void printNumber() {
                System.out.println("Number: " + number);
            }
        }

        // Utworzenie instancji klasy lokalnej i wywołanie metody
        LocalClass local = new LocalClass();
        local.printNumber();
    }
}
```

W tym przykładzie LocalClass:

Jest dostępna tylko wewnątrz metody someMethod.
Ma dostęp do zmiennej number, która jest efektywnie finalna, ponieważ jej wartość się nie zmienia.
Może być użyta tylko w zakresie metody, więc nie można jej utworzyć ani użyć poza someMethod.
Kiedy używać klasy lokalnej?
Klasy lokalne są przydatne, gdy potrzebujesz pomocniczej klasy do wykonania zadań, które są ograniczone do konkretnej metody. Dzięki nim możesz uniknąć zaśmiecania przestrzeni nazw klasy głównej i utrzymać kod bardziej zorganizowany.

Można też stworzyć lokalny enum, interface czy record.

1. Anonymus class
Anonymous class (klasa anonimowa) w Javie to rodzaj klasy, która nie ma nazwy i jest definiowana oraz instancjonowana “w locie” w miejscu jej użycia. Zwykle tworzy się ją jako rozszerzenie istniejącej klasy lub implementację interfejsu, gdy potrzebna jest tylko pojedyncza instancja klasy o niestandardowej funkcjonalności.

Cechy klas anonimowych
Bez nazwy: Klasy anonimowe są tworzone bez nadawania im nazwy. Kod klasy jest deklarowany bezpośrednio w miejscu, gdzie potrzebna jest jej instancja.
Ograniczony dostęp: Można je instancjonować tylko raz i tylko w miejscu, w którym są zdefiniowane.
Dziedziczenie lub implementacja interfejsu: Klasa anonimowa może rozszerzać tylko jedną klasę lub implementować jeden interfejs, ponieważ w Javie nie jest możliwe wielokrotne dziedziczenie.
Dostęp do zmiennych z zewnętrznej metody: Tak samo jak klasy lokalne, mają dostęp do zmiennych z metody, w której są zdefiniowane, o ile są one final lub “efektywnie finalne”.
Przykład klasy anonimowej
Załóżmy, że mamy interfejs Greeting z metodą sayHello():

```java
public class Main {
    public static void main(String[] args) {
        Greeting greeting = new Greeting() {
            @Override
            public void sayHello() {
                System.out.println("Hello from an anonymous class!");
            }
        };

        greeting.sayHello(); // Wywołanie metody
    }
}
```

lub

```java
var c4 = new Comparator<StoreEmployee>() {
            @Override
            public int compare(StoreEmployee o1, StoreEmployee o2) {
                return o1.getName().compareTo(o2.getName());
            }
        };
```

Klasa anonimowa mogła być użyta jako argument:

```java
        sortIt(storeEmployees, new Comparator<StoreEmployee>() {
 @Override
 public int compare(StoreEmployee o1, StoreEmployee o2) {
  return o1.getName().compareTo(o2.getName());
 }
});
```
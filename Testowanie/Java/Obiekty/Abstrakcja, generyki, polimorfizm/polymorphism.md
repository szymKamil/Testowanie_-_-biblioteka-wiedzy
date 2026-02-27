# polymorphism

# Polymorphism

Polimorfizm to jedna z podstawowych zasad programowania obiektowego, umożliwiająca używanie wielu różnych form (implementacji) metod oraz obiektów w taki sposób, aby zachować elastyczność i skalowalność kodu.

Polimorfizm (ang. Polymorphism) oznacza możliwość posiadania wielu form przez różne obiekty lub metody. W kontekście programowania obiektowego pozwala to na jedną metodę lub obiekt, który może działać na różne sposoby w zależności od kontekstu.

Jeśli jedno zadanie jest wykonywane na różne sposoby, jest to znane jako polimorfizm. Na przykład: przekonać klienta w inny sposób, narysować coś, na przykład kształt, trójkąt, prostokąt itp.
W Javie używamy przeciążania metod i nadpisywania metod lub wykorzystywania metod z różnych obiektów w zależności od kontekstu, aby osiągnąć polimorfizm.

Polimorfizm zapewnia:
Prostszą, bardziej elastyczną strukturę kodu.
Możliwość rozszerzania kodu bez modyfikacji już istniejącego.
Lepsze wsparcie dla rozwoju i skalowania aplikacji.
Rodzaje polimorfizmu w Javie
Polimorfizm statyczny (przeciążanie metod):

Osiągany przez przeciążanie metod (method overloading).
Kompilator decyduje, którą wersję metody wywołać na podstawie liczby lub typu parametrów.
Przykład:

```java
class Calculator {
    public int add(int a, int b) {
        return a + b;
    }
    public double add(double a, double b) {
        return a + b;
    }
}
```

Polimorfizm dynamiczny (przesłanianie metod):

Osiągany przez przesłanianie metod (method overriding).
Decyzja o tym, którą metodę wywołać, jest podejmowana w trakcie działania programu (ang. runtime).
Przykład:

```java
class Animal {
    void sound() {
        System.out.println("Some sound");
    }
}
class Dog extends Animal {
    @Override
    void sound() {
        System.out.println("Woof");
    }
}
```

Przykład zastosowania polimorfizmu
Załóżmy, że mamy klasę bazową Animal i różne klasy potomne, np. Dog i Cat. Każda z nich implementuje metodę sound() w swój unikalny sposób. Dzięki polimorfizmowi możemy przechowywać referencje do obiektów Dog i Cat w tej samej tablicy Animal[], a wywołanie sound() na każdym z elementów wywoła odpowiednią wersję metody.

```java
Animal[] animals = {new Dog(), new Cat()};
for (Animal animal : animals) {
        animal.sound(); // Wynik zależy od rzeczywistego typu obiektu
}
```

Subklasa może dziedziczyć po klasie nadrzędnej,
Cat simon = new Cat();

Tworzymy nowy obiekt Cat (obiekt klasy Cat) i przypisujemy go do zmiennej simon.
simon ma typ Cat, co oznacza, że możemy używać na niej metod i pól zdefiniowanych w klasie Cat.
Animal creature = simon;

Przypisujemy simon (obiekt klasy Cat) do zmiennej creature, która jest typu Animal.
Dzięki temu, że klasa Cat jest podklasą (dziedziczy po) klasy Animal, obiekt simon może być przypisany do zmiennej typu Animal. Tego rodzaju przypisanie nazywamy polimorfizmem.
Polimorfizm pozwala na traktowanie obiektów klas pochodnych (np. Cat) jako obiektów klasy bazowej (Animal). W ten sposób możemy pracować z różnymi obiektami w bardziej ogólny sposób. Dzięki temu kod jest bardziej elastyczny i łatwiejszy do rozszerzania.

Zalety polimorfizmu
Kod jest bardziej modułowy: Obiekty mogą być zastąpione innymi o podobnych właściwościach, bez konieczności zmiany reszty kodu.
Łatwość w rozszerzaniu funkcjonalności: Dodawanie nowych klas lub metod nie wymaga modyfikacji istniejących.
Czytelność i utrzymanie kodu: Kod staje się bardziej zrozumiały, a zmiany lub naprawy są łatwiejsze.
Polimorfizm to ważny mechanizm, który sprawia, że kod w Javie jest bardziej elastyczny i łatwiejszy do zarządzania.

## Zacieniowanie

W kontekście programowania w języku Java, “zacieniowanie” (ang. shadowing) odnosi się do sytuacji, gdy lokalna zmienna, parametr metody lub zmienna instancyjna w klasie o tej samej nazwie “zacienia” (ukrywa) zmienną o tej samej nazwie z wyższego zasięgu, takiego jak klasa nadrzędna lub metoda.

```java
class Parent {
int value = 10; // Zmienna instancyjna w klasie nadrzędnej
}

class Child extends Parent {
int value = 20; // Zmienna instancyjna w klasie potomnej

    void displayValues() {
        System.out.println("Wartość w klasie Child: " + value); // Odnosi się do zmiennej value w Child
        System.out.println("Wartość w klasie Parent: " + super.value); // Odnosi się do zmiennej value w Parent
    }
}

public class Main {
public static void main(String[] args) {
Child child = new Child();
child.displayValues();
}
}
```

Powinniśmy unikać zacieniowania, gdy to możliwe. W większości przypadków zaleca się unikanie zacieniowania w kodzie Java, ponieważ może to prowadzić do niejasności i trudności w utrzymaniu kodu. Oto kilka powodów, dla których warto unikać zacieniowania:

- Zrozumiałość kodu: Zacieniowane zmienne mogą wprowadzać zamieszanie, szczególnie w większych klasach lub złożonych hierarchiach dziedziczenia. Kiedy zmienna w lokalnym zasięgu ma tę samą nazwę co zmienna w zasięgu szerszym, może być trudno zrozumieć, którą zmienną rzeczywiście wykorzystujesz w danym kontekście.
- Potencjalne błędy: Możliwość przypadkowego odwołania się do niewłaściwej zmiennej zwiększa ryzyko błędów. W takim przypadku możesz nieświadomie używać wartości, której nie zamierzałeś używać.
- Utrzymanie i czytelność: Kod, w którym unika się zacieniowania, jest często bardziej przejrzysty i łatwiejszy do zrozumienia dla innych programistów (lub nawet dla Ciebie samego w przyszłości). Użycie jednoznacznych nazw zmiennych pomaga w utrzymaniu czystości kodu.

Praktyki, które mogą pomóc:
- Używaj różnych nazw: Staraj się używać różniących się nazw dla zmiennych w różnych zasięgach, aby uniknąć zacieniowania.

- Przejrzystość w nazwach: Dobre praktyki nazewnictwa mogą pomóc w jasnym rozróżnieniu zmiennych instancyjnych od lokalnych.
- Użycie słowa kluczowego this: W metodach klasowych możesz używać słowa kluczowego this, aby jasno określić, że odnosisz się do zmiennej instancyjnej, co zwiększa czytelność.

## Kompozycja a dziedziczenie

Dziedziczenie reprezentuje relację “jest” (np. Pies jest Zwierzęciem).
Kompozycja reprezentuje relację “ma” (np. Samochód ma Silnik).

Kompozycja to podejście, w którym jedna klasa zawiera instancje innych klas jako swoje atrybuty. To pozwala na budowanie złożonych obiektów z prostszych komponentów.

Kluczowe cechy kompozycji:
Elastyczność: Umożliwia łatwą wymianę komponentów i ich modyfikację bez wpływu na resztę systemu.
Brak problemów z wielodziedziczeniem: Kompozycja nie ma ograniczeń związanych z dziedziczeniem, co pozwala na tworzenie bardziej złożonych relacji między klasami.
Lepsza enkapsulacja: Kompozycja może prowadzić do lepszej enkapsulacji, ponieważ komponenty są odpowiedzialne za swoją logikę.

```java
class Engine {
    void start() {
        System.out.println("Engine starting");
    }
}

class Car {
    private Engine engine;

    Car() {
        engine = new Engine();
    }

    void startCar() {
        engine.start();
    }
}

public class Main {
    public static void main(String[] args) {
        Car myCar = new Car();
        myCar.startCar(); // Wydrukuje: Engine starting
    }
}
```

---

Dziedziczenie
Dziedziczenie pozwala na tworzenie hierarchii klas, gdzie jedna klasa (klasa podrzędna lub podklasa) dziedziczy właściwości i metody z innej klasy (klasy nadrzędnej lub superklasy).

Kluczowe cechy dziedziczenia:
Reużywalność kodu: Podklasa może korzystać z kodu superklasy, co pozwala na mniejsze duplikowanie kodu.
Polimorfizm: Dziedziczenie wspiera polimorfizm, co pozwala na traktowanie obiektów podklas jako obiektów superklasy.
Ograniczenia: W Javie można dziedziczyć tylko po jednej klasie (dziedziczenie pojedyncze), co może ograniczać elastyczność.

```java
class Animal {
    void makeSound() {
        System.out.println("Some sound");
    }
}

class Dog extends Animal {
    void makeSound() {
        System.out.println("Bark");
    }
}

public class Main {
    public static void main(String[] args) {
        Animal myDog = new Dog();
        myDog.makeSound(); // Wydrukuje: Bark
    }
}
```

# Przydatne metody

`x.getClass().getSimpleName()` - zwraca nazwę danej klasy.
`jakiśObiekt1 instanceof Klasa` - weryfikuje czy obiekt jest instancją danej klasy.

```java
if (jakiśObiekt1 instanceof Klasa zmienna) {
// Teraz można używać 'zmienna' bez konieczności ręcznego rzutowania
zmienna.metodaKlasy(); // Możesz bezpośrednio wywoływać metody z Klasa
}
```

Tutaj, jeśli jakiśObiekt1 jest instancją Klasa, to dodatkowo zostaje przypisany do zmiennej zmienna, która jest już typu Klasa. Dzięki temu można od razu wywoływać na niej metody specyficzne dla Klasa, bez potrzeby rzutowania.
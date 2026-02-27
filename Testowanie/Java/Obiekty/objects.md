# objects

# Obiekty

Obiekty tworzymy używając razy `new`.
Klasy (class) to szkice potrzebne do powstawania obiektów (object).

Możemy tworzyć obiekty, które nie są przypisane do zmiennej. Istnieć będą w pamięci, ale nie będzie możliwości dotarcia do nich za pomocą kodu (nie ma zmiennej, do której można się odwołać).
Przykład: `new House("black")`.

**!Obiekt to instancja klasy!**

Wszystkie obiekty dziedziczą po `java.lang.Object`.

Obiekt ma trzy cechy:
- Stan: reprezentuje dane (wartość) obiektu.
- Zachowanie: reprezentuje zachowanie (funkcjonalność) obiektu, takie jak wpłata, wypłata itp.
- Tożsamość: Tożsamość obiektu jest zwykle implementowana za pomocą unikalnego identyfikatora. Wartość ID nie jest widoczna dla użytkownika zewnętrznego. Jest jednak używana wewnętrznie przez maszynę JVM do jednoznacznej identyfikacji każdego obiektu.

## Instancja

W Javie **instancja** i **obiekt** to pojęcia często używane zamiennie, ale mają subtelne różnice:

### 1. **Obiekt**:

- **Obiekt** to konkretny egzemplarz klasy utworzony w pamięci podczas działania programu. Jest to zbiór pól (atrybutów) i metod (zachowań) zdefiniowanych w klasie.
- Obiekty są tworzone na podstawie definicji klasy przy użyciu słowa kluczowego `new`. Na przykład:
    
    ```java
    Klasa k = new Klasa();
    ```
    

W powyższym przykładzie `k` to obiekt typu `Klasa`.

### Inny przykład:

```java
class Car {
    // Atrybuty klasy Car
    String color;
    String model;

    // Konstruktor klasy Car
    public Car(String color, String model) {
        this.color = color;
        this.model = model;
    }

    // Metoda klasy Car
    public void displayInfo() {
        System.out.println("Car model: " + model + ", Color: " + color);
    }
}

// Tworzenie obiektu (instancji) klasy Car
Car myCar = new Car("Red", "Toyota");
myCar.displayInfo(); // Wyjście: Car model: Toyota, Color: Red
```

W powyższym przykładzie Car jest klasą, a myCar jest obiektem (instancją) tej klasy. myCar to konkretny egzemplarz klasy Car, który ma swoje atrybuty (kolor i model).
W jednym pliku .java można zadeklarować tylko jedną klasę jako publiczną. Co więcej, nazwa tego pliku musi być taka sama jak nazwa tej publicznej klasy. Jeśli zdefiniujesz więcej niż jedną klasę w jednym pliku .java, tylko jedna z nich może mieć modyfikator dostępu public.

1. Inicjalizacja obiektu przez zmienną referencyjną
Najprostszy sposób – tworzymy obiekt klasy i przypisujemy go do zmiennej referencyjnej. Tę metodę stosuje się bezpośrednio, gdy chcemy stworzyć nową instancję klasy i przypisać jej referencję do zmiennej.

```java
class Samochod {
    int predkosc;
    String kolor;
}

public class Main {
    public static void main(String[] args) {
        Samochod samochod = new Samochod(); // inicjalizacja przez zmienną referencyjną
        samochod.predkosc = 120;            // ustawienie właściwości
        samochod.kolor = "Czerwony";
    }
}
```

1. Inicjalizacja obiektu przez metodę
Możemy również użyć metody, która tworzy nowy obiekt i zwraca go do miejsca wywołania. Taki sposób jest przydatny, gdy chcemy tworzyć obiekty w sposób bardziej elastyczny, np. z domyślnymi wartościami lub skomplikowaną logiką inicjalizacji.

```java
class Samochod {
    int predkosc;
    String kolor;

    // Metoda inicjalizująca obiekt
    public static Samochod stworzSamochod(int predkosc, String kolor) {
        Samochod samochod = new Samochod();
        samochod.predkosc = predkosc;
        samochod.kolor = kolor;
        return samochod;
    }
}

public class Main {
    public static void main(String[] args) {
        Samochod samochod = Samochod.stworzSamochod(150, "Niebieski"); // inicjalizacja przez metodę
    }
}
```

1. Inicjalizacja obiektu przez konstruktor
Konstruktor to specjalna metoda, która wywołuje się automatycznie podczas tworzenia nowego obiektu. Pozwala on na ustawienie początkowych wartości dla obiektu bez dodatkowego kodu po utworzeniu obiektu. Jeśli nie zdefiniujemy konstruktora, Java automatycznie stworzy dla nas domyślny konstruktor.

```java
class Samochod {
    int predkosc;
    String kolor;

    // Konstruktor
    public Samochod(int predkosc, String kolor) {
        this.predkosc = predkosc;
        this.kolor = kolor;
    }
}

public class Main {
    public static void main(String[] args) {
        Samochod samochod = new Samochod(180, "Czarny"); // inicjalizacja przez konstruktor
    }
}
```

### 2. **Instancja**:

- **Instancja** to bardziej techniczne pojęcie oznaczające obiekt konkretnej klasy w pamięci.
- Każdy obiekt jest instancją danej klasy, co oznacza, że obiekt jest szczególną “realizacją” definicji klasy. Mówiąc prościej, **instancja** odnosi się do faktu, że dany obiekt istnieje w pamięci jako instancja klasy.

```java
Car car1 = new Car("Blue", "Honda");
Car car2 = new Car("Green", "Ford");
Car car3 = new Car("Black", "BMW");

car1.displayInfo(); // Wyjście: Car model: Honda, Color: Blue
car2.displayInfo(); // Wyjście: Car model: Ford, Color: Green
car3.displayInfo(); // Wyjście: Car model: BMW, Color: Black
```

Tutaj car1, car2 i car3 to różne instancje klasy Car. Każda z nich ma swoje unikalne atrybuty, co pokazuje, jak różne obiekty mogą być utworzone z tej samej klasy.

Obiekt: Konkretny egzemplarz utworzony z klasy. Można na niego odwołać się w kodzie (np. myCar, dog1).
Instancja: Termin techniczny, który odnosi się do obiektu w pamięci. Każdy obiekt jest instancją swojej klasy.

### Różnica między instancją a obiektem:

- **Obiekt** to pojęcie bardziej praktyczne — odnosi się do egzemplarza klasy, z którym bezpośrednio pracujemy w kodzie.
- **Instancja** odnosi się do tego, że obiekt jest utworzony w pamięci jako instancja klasy, kładąc nacisk na powiązanie obiektu z jego klasą.

W skrócie: obiekt to termin używany w praktycznym programowaniu, natomiast instancja odnosi się do istnienia tego obiektu jako egzemplarza klasy w pamięci.

## Referencje

W Javie **referencja** odnosi się do wskaźnika lub “odwołania” do obiektu w pamięci, ale nie jest samym obiektem. Mówiąc prościej, referencja to zmienna, która przechowuje adres obiektu w pamięci, dzięki czemu program może uzyskać dostęp do tego obiektu.

### Kluczowe punkty na temat referencji:

1. **Referencja nie przechowuje obiektu**:
- Referencja przechowuje jedynie adres, pod którym obiekt istnieje w pamięci (na stercie, czyli “heap”). Sama referencja nie jest obiektem, a jedynie drogą do uzyskania dostępu do obiektu.
1. **Tworzenie referencji**:
- Gdy tworzysz obiekt za pomocą słowa kluczowego `new`, to zmienna, której przypisujesz ten obiekt, staje się referencją do niego. Na przykład:
    
    ```java
    Klasa k = new Klasa();
    ```
    
    W tym przypadku `k` to referencja do obiektu typu `Klasa`, który został utworzony na stercie.
    

Obiekty tworzone sa metodami o takiej samej nazwie jak nazwa klasy.

1. **Referencje mogą być null**:
- Referencja może być przypisana do wartości `null`, co oznacza, że nie wskazuje na żaden obiekt. Oto przykład:
    
    ```java
    Klasa k = null;
    ```
    
    Gdy referencja jest null, może pojawić się błąd w programie w przypadku odwołania do tego obiektu:
    

```java
Klasa k = null;
k.someMethod(); // Spowoduje NullPointerException
```

1. **Kopiowanie referencji**:
- Gdy przypiszesz jedną referencję do innej, obie będą wskazywać na ten sam obiekt w pamięci. Nie tworzy to nowego obiektu, tylko nową referencję do istniejącego. Na przykład:
    
    ```java
    Klasa k1 = new Klasa();
    Klasa k2 = k1;
    ```
    
    W powyższym przykładzie zarówno `k1`, jak i `k2` wskazują na ten sam obiekt, więc operacje wykonywane na `k2` wpłyną na obiekt, do którego odnosi się `k1`.
    

### Podsumowanie:

- **Referencja** to “wskaźnik” na obiekt w pamięci.
- Umożliwia dostęp do właściwego obiektu, ale nie jest samym obiektem.
- W Javie wszystkie zmienne typów złożonych (nie-prymitywnych) są referencjami.

W praktyce, dzięki referencjom możemy łatwo operować na obiektach, nie musząc zajmować się ich faktycznym umiejscowieniem w pamięci, co ułatwia zarządzanie pamięcią w programowaniu obiektowym.

Obiekty w Javie przechowywane są w pamięci. Java automatycznie lokuje i delokuje je w pamieci urzadzenia.

## Konstruktory

Konstruktory mogą akceptować wartości i mogą być overloaded.

### this

`this` odnosi się do elementu danego konkretnego obiektu, który został utworzony.

`this` jest przydatne w sytuacjach, gdy parametry metody lub konstruktora mają tę samą nazwę co pola klasy.

### Używanie this przy konstruktorach

`This` w konstruktorach klasy pozwala na odwołanie do innego konstuktora, tworząc łańcuch konstruktorów.
Dobra praktyka, to nie duplikowanie kodu, ale tworzenie konstruktorów powiązanych:

```java
class Rectangle {
    private int x;
    private int y;
    private int width;
    private int height;

    public Rectangle() {
        this(0, 0);
    }

    public Rectangle(int width, int height) {
        this(0, 0, height, width);
    }

    //Constructor chaining
    public Rectangle(int x, int y, int width, int height) {
        this.x = x;
        this.y = y;
        this.width = width;
        this.height = height;
    }
}
```

Podobne zasady warto stosować do `super();`

### super

Pozwala na odwołanie się do zmiennych i metod rodzica. Patrz: [inheritance](Polymorphism/inheritance.md)

### static

Używanie `static` w klasach powoduje, że wszystkie obiekty zbudowane z danej klasy podlegać będą pod tą samą zmienną statyczną. Czyli zmiana w jednym obiekcie pociągnie zmiany we wszystkich obiektach.
Uniknąc można tego korzystając z `this`.

Default konstruktor - jeśli nie zdefiniujemy konstruktora dla klasy, kompiler automatycznie doda pusty konstruktor.

Konstruktor może posiadać parametry takie jak: ‘public’, ‘private’, ‘protected’. Konstruktor nie może być jednak abstract i final.

## Metody

Metody tworzymy w klasach. Klasy mogą posiadać metody statyczne jak i metody instancyjne.

### Metody statyczne

Tworzone poprzez użycie `static`. Uzywane do operacji ktore nie potrzebuja odwolań do `this`. To metody ktore maja byc uniwersalne i nie zalezec od zmiennych w obiekcie.
Odwolujemy sie do nich poprzez uzycie nazwy klasy i nazwy metody:

```java
Dog.bark()
```

To potrzebne jest tylko wtedy, gdy metoda jest w innej klasie. Gdy metoda zawiera sie w tej samej klasie, wysatrczy odwołać się do jej nazwy: `bark()`.

### Metody z instancyjne

To metody ktore nie maja `static` w parametrach nazwy.
To metody które odwoływać się do zinacjonowanych metod jak i metod statycznych. Tzn. nie muszą używać `this`. Wynika to z faktu, że takie metody by powstać muszą posiadać zainstacjonowany obiekt.

```java
Dog rex = new Dog();
rex.bark();
```

- Kiedy tworzyć metody statyczne?
- Gdy wiemy, że nie będziemy odwoływać się do zmiennych w obiekcie lub do metod w obiekcie.

```java
class Dog {
    public static void bark() {
        System.out.println("Woof!");
    }

    public void fetch() {
        System.out.println("Fetching...");
    }
}

// Użycie metod:
Dog.bark(); // Metoda statyczna
Dog rex = new Dog();
rex.fetch(); // Metoda instancyjna
```

## Shadowing (zaciemnianie)

Odwołanie do zmiennej która istnieje już w klasie (o takiej samej nazwie). Scope (klamry) definiują zakres uzycia zmiennych.
’’’java
int x, y;
public void move (int x, int y) {

}
’’’

# Abstrakcyjne klasy w Javie

Abstrakcyjne klasy w Javie to klasy, które nie mogą być instancjonowane (tj. nie można tworzyć obiektów tej klasy) i służą jako szablony dla innych klas. Oto kluczowe cechy i zasady dotyczące abstrakcyjnych klas:

## Kluczowe cechy abstrakcyjnych klas:

1. **Deklaracja abstrakcyjna**: Abstrakcyjne klasy są deklarowane za pomocą słowa kluczowego `abstract`. Możesz mieć zarówno metody abstrakcyjne (które nie mają ciała), jak i metody z implementacją.
2. **Metody abstrakcyjne**: Metody abstrakcyjne to metody zadeklarowane bez implementacji. Muszą być zaimplementowane w klasach pochodnych, które rozszerzają klasę abstrakcyjną. Przykład:
```java
abstract class Animal {
abstract void makeSound(); // Metoda abstrakcyjna
}
3. Możliwość posiadania metod konkretnych: Abstrakcyjna klasa może również mieć metody, które mają swoją implementację. Klasy pochodne mogą korzystać z tych metod lub je nadpisywać.
4. Dziedziczenie: Klasa pochodna (subklasa) musi implementować wszystkie metody abstrakcyjne klasy bazowej, chyba że sama jest klasą abstrakcyjną.

```java
abstract class Shape {
    abstract void draw(); // Metoda abstrakcyjna

    void display() {
        System.out.println("This is a shape.");
    }
}

class Circle extends Shape {
    @Override
    void draw() {
        System.out.println("Drawing a circle.");
    }
}

class Rectangle extends Shape {
    @Override
    void draw() {
        System.out.println("Drawing a rectangle.");
    }
}

public class Main {
    public static void main(String[] args) {
        Shape circle = new Circle();
        Shape rectangle = new Rectangle();

        circle.draw(); // Wywołanie metody z klasy Circle
        rectangle.draw(); // Wywołanie metody z klasy Rectangle
        circle.display(); // Wywołanie metody z klasy Shape
    }
}
```

Zastosowanie abstrakcyjnych klas:
Modelowanie hierarchii klas: Abstrakcyjne klasy są przydatne, gdy chcesz zdefiniować wspólne cechy dla grupy klas, ale nie chcesz, aby klasa bazowa była instancjonowana.

Wymuszanie implementacji: Używając abstrakcyjnych klas, możesz wymusić, aby klasy pochodne implementowały określone metody, co zapewnia spójność i standaryzację.

# Klasa wewnetrzna

To klasa tworzona wewnątrz klasy.
Klasa wewnętrzna w Javie to klasa zdefiniowana wewnątrz innej klasy. Umożliwia ona lepszą organizację kodu, szczególnie jeśli klasa wewnętrzna jest logicznie powiązana z klasą zewnętrzną. Klasy wewnętrzne mają dostęp do pól i metod klasy zewnętrznej, nawet jeśli są one prywatne, co może ułatwić tworzenie bardziej złożonych struktur danych i funkcji. W Javie istnieje kilka rodzajów klas wewnętrznych:
Enclosing instance to instancja klasy zewnętrznej (ang. “outer class”), która zawiera klasę wewnętrzną (ang. “inner class”). W przypadku klas wewnętrznych niestatycznych, każda instancja wewnętrznej klasy jest połączona z instancją klasy zewnętrznej, dzięki czemu ma ona dostęp do jej pól i metod.

Enclosing instance jest tworzona, gdy:
- Tworzysz instancję klasy wewnętrznej z poziomu klasy zewnętrznej. Wtedy instancja klasy wewnętrznej wie, do której instancji klasy zewnętrznej należy.
- Odwłujesz się do instancji klasy zewnętrznej z wnętrza klasy wewnętrznej, np. za pomocą OuterClassName.this, co pozwala na wywoływanie metod czy pól klasy zewnętrznej w przypadku konfliktu nazw.

Klasa wewnętrzna niestatyczna – jest zależna od instancji klasy zewnętrznej. Można do niej uzyskać dostęp tylko za pośrednictwem instancji klasy zewnętrznej. Przykład:

```java
public class Zewnetrzna {
    private int liczba = 10;

    public class Wewnetrzna {
        public void wyswietlLiczba() {
            System.out.println("Liczba: " + liczba);  // ma dostęp do prywatnych pól klasy Zewnetrzna
        }
    }
}
```

Klasa statyczna wewnętrzna – jest niezależna od instancji klasy zewnętrznej, dzięki czemu można utworzyć jej instancję bez tworzenia obiektu klasy zewnętrznej. Przykład:

```java
public class Zewnetrzna {
    private static int liczba = 10;

    public static class WewnetrznaStatyczna {
        public void wyswietlLiczba() {
            System.out.println("Liczba: " + liczba);  // ma dostęp tylko do statycznych pól klasy Zewnetrzna
        }
    }
}
```

Klasa lokalna – jest zdefiniowana wewnątrz metody klasy zewnętrznej i jest dostępna tylko w obrębie tej metody. Przykład:

```java
public class Zewnetrzna {
    public void metoda() {
        class Lokalna {
            public void wyswietl() {
                System.out.println("To jest klasa lokalna.");
            }
        }
        Lokalna lokalna = new Lokalna();
        lokalna.wyswietl();
    }
}
```

Klasa anonimowa – jest to klasa bez nazwy, utworzona zazwyczaj jako instancja klasy lub implementacja interfejsu. Używa się jej, gdy klasa jest potrzebna tylko raz, np. w wywołaniach metod jako parametry.
Anonymous object to obiekt w Javie, który jest tworzony bez przypisywania go do zmiennej referencyjnej. Używa się go wtedy, gdy obiekt jest potrzebny tylko do jednorazowego użytku i nie ma potrzeby ponownego odwoływania się do niego.
Przykład:

```java
public class Zewnetrzna {
    public void metoda() {
        Runnable runnable = new Runnable() {
            @Override
            public void run() {
                System.out.println("To jest klasa anonimowa.");
            }
        };
        runnable.run();
    }
}
```

Klasa anonimowa musi dziedziczyć po istniejącej klasie lub implementować interfejs – nie może być “goła”. Klasy anonimowe w Javie zawsze powstają jako jednorazowa implementacja konkretnej klasy lub interfejsu.

```java
Runnable runnable = new Runnable() {
    @Override
    public void run() {
        System.out.println("Klasa anonimowa implementująca interfejs Runnable");
    }
};
```

# Mutable i immutable

| **Kategoria** | **Mutable Classes** | **Immutable Classes** |
| --- | --- | --- |
| **Definicja** | Obiekty mogą zmieniać swój stan po utworzeniu. | Obiekty są niezmienne – ich stan nie może być zmieniony po utworzeniu. |
| **Charakterystyka** | - Dane obiektu mogą być modyfikowane za pomocą setterów lub metod zmieniających. | - Wszystkie pola są zwykle `final`.- Klasa nie udostępnia metod zmieniających dane obiektu. |
| **Bezpieczeństwo wątków** | - Wymaga synchronizacji w środowiskach wielowątkowych. | - Naturalnie bezpieczne w środowiskach wielowątkowych, ponieważ stan się nie zmienia. |
| **Przykłady klas** | - `StringBuilder`, `ArrayList`, własne klasy z setterami. | - `String`, `Integer`, `LocalDate`, własne klasy bez setterów i z polami `final`. |
| **Zastosowanie** | - Gdy wymagana jest elastyczność zmiany stanu obiektu. | - Gdy potrzebna jest niezmienność i prostota użycia w wielowątkowości. |
| **Przykład kodu** | `java<br>class MutableExample {<br>  private int value;<br>  public void setValue(int value) {<br>    this.value = value;<br>  }<br>}<br>` | `java<br>final class ImmutableExample {<br>  private final int value;<br>  public ImmutableExample(int value) {<br>    this.value = value;<br>  }<br>}<br>` |
| **Kiedy używać?** | - Gdy dane obiektu muszą być dynamicznie aktualizowane. | - Gdy priorytetem jest bezpieczeństwo wątków, prostota, lub eliminacja błędów związanych ze stanem. |
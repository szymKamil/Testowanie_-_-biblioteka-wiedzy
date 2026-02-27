# abstraction

# Abstraction

Abstrakcja w Javie to koncepcja programowania obiektowego, która umożliwia ukrycie szczegółów implementacji i przedstawienie jedynie istotnych aspektów danego obiektu lub funkcji. Dzięki abstrakcji użytkownik interfejsu lub klasy nie musi wiedzieć, jak dokładnie coś działa, ale jedynie jakie ma możliwości i jak można z tego skorzystać. Abstrakcja pomaga w uproszczeniu kodu i zmniejszeniu jego skomplikowania.

W Javie abstrakcję można osiągnąć za pomocą:

Klas abstrakcyjnych: Klasa abstrakcyjna jest klasą, która nie może być instancjonowana i służy jako podstawa dla innych klas. Może zawierać zarówno metody abstrakcyjne (bez implementacji), jak i metody konkretne (z implementacją). Klasy, które dziedziczą po klasie abstrakcyjnej, muszą implementować wszystkie jej metody abstrakcyjne lub być również klasami abstrakcyjnymi.

```java
abstract class Shape {
    String color;
    abstract double area(); // metoda abstrakcyjna

    // Metoda konkretna
    public void displayColor() {
        System.out.println("Kolor: " + color);
    }
}

class Circle extends Shape {
    double radius;

    Circle(double radius, String color) {
        this.radius = radius;
        this.color = color;
    }

    @Override
    double area() {
        return Math.PI * radius * radius;
    }
}
```

Interfejsów: Interfejs to w pełni abstrakcyjna “klasa”, która definiuje tylko deklaracje metod, które muszą zostać zaimplementowane przez klasy, które go implementują. W nowszych wersjach Javy interfejsy mogą zawierać metody domyślne (z implementacją), ale głównym celem interfejsów jest zapewnienie kontraktu, jaki klasy implementujące muszą spełnić.

```java
interface Drawable {
    void draw(); // metoda abstrakcyjna
}

class Rectangle implements Drawable {
    @Override
    public void draw() {
        System.out.println("Rysowanie prostokąta");
    }
}
```

W tym przykładzie Drawable to interfejs, a klasa Rectangle implementuje metodę draw, która musi być dostarczona przez każdą klasę implementującą ten interfejs.

Klasy dziedziczące po klasie abrastkcyjnej mogą być umieszczane w kodzie, który odwołuje się do abstrakcji.
Oczywiście! Umieszczanie klas dziedziczących po klasie abstrakcyjnej w kodzie, który odwołuje się do abstrakcji, polega na tym, że zamiast używać bezpośrednio nazw klas dziedziczących, używamy odwołania do klasy abstrakcyjnej lub interfejsu. Pozwala to pisać bardziej elastyczny i zwięzły kod, który można łatwiej rozszerzać, bez konieczności jego modyfikowania przy dodawaniu nowych klas dziedziczących.

Klasa abstrakcyjna jest projektowana tak, by definiować podstawowy kontrakt, czyli metody i pola, które muszą być zaimplementowane przez klasy dziedziczące. Dzięki temu można tworzyć obiekty klas dziedziczących, które są traktowane jako obiekty klasy abstrakcyjnej, co umożliwia późniejsze polimorficzne wywołanie metod.

```java
class Circle extends Shape {
    @Override
    void draw() {
        System.out.println("Rysuję okrąg.");
    }
}

class Rectangle extends Shape {
    @Override
    void draw() {
        System.out.println("Rysuję prostokąt.");
    }
}
public class Main {
   public static void main(String[] args) {
      Shape[] shapes = { new Circle(), new Rectangle() };  // Używamy abstrakcji Shape

      for (Shape shape : shapes) {
         shape.draw();  // Wywołanie metody draw() na obiektach typu Shape
      }
   }
}
```

Korzyści:
Polimorfizm – Kod może działać z dowolną klasą dziedziczącą po Shape, co zwiększa jego elastyczność.
Łatwość rozszerzania – Dodanie nowych klas dziedziczących, takich jak Triangle czy Square, nie wymaga zmian w głównej logice programu.

Zalety abstrakcji
Ukrycie szczegółów: Abstrakcja pozwala na ukrycie szczegółów implementacji, co upraszcza korzystanie z kodu.
Lepsza organizacja kodu: Ułatwia zarządzanie kodem poprzez separację interfejsu od implementacji.
Umożliwia wielokrotne użycie: Dzięki abstrakcji te same interfejsy lub klasy abstrakcyjne mogą być wykorzystywane w różnych częściach programu.
Ułatwia utrzymanie i rozwój oprogramowania: Ponieważ zmiany w implementacji nie wpływają na korzystających z klasy użytkowników.

## Abstrakcja w abstrakcji

Abstrakcyjna klasa która rozszerza inna abstrakcyjną klasę musi posiadać konstruktor, ale może posiadać wszystkie lub żadną z metod pochodzacą z klasy nadrzędnej.

## Metody abstrakcyjne

*!Uwaga!* Klasa abstrakcyjna moze posiadać też konkretne metody.

Abstrakcyjne metody to specjalny rodzaj metod definiowanych w klasach abstrakcyjnych oraz interfejsach, które nie mają implementacji – jedynie deklarację. Służą do wymuszenia na klasach pochodnych (klasach dziedziczących lub implementujących) zapewnienia konkretnej implementacji tych metod.
Metody abstrakcyjne mogą powstawać tylko w abstrakcyjnych klasach lub w `interfaces`.

Cechy abstrakcyjnych metod:
Brak implementacji: Abstrakcyjna metoda nie zawiera ciała (kodu). W klasie abstrakcyjnej lub interfejsie jest jedynie jej nagłówek, np. void draw();. To wymaga, aby każda klasa dziedzicząca po klasie abstrakcyjnej lub implementująca interfejs zaimplementowała tę metodę.

Definiowanie w klasach abstrakcyjnych lub interfejsach: Abstrakcyjne metody można definiować tylko w klasach abstrakcyjnych lub interfejsach, nigdy w klasach konkretnych (nieabstrakcyjnych).

Służą jako kontrakt: Abstrakcyjne metody działają jak kontrakt dla klas dziedziczących. Oznacza to, że klasa pochodna jest zobowiązana do implementacji wszystkich metod abstrakcyjnych lub również musi zostać zdefiniowana jako klasa abstrakcyjna.

```java
abstract class Shape {
    String color;

    // Metoda abstrakcyjna, bez implementacji
    abstract double area();

    // Metoda konkretna z implementacją
    public void displayColor() {
        System.out.println("Kolor: " + color);
    }
}

class Circle extends Shape {
    double radius;

    Circle(double radius, String color) {
        this.radius = radius;
        this.color = color;
    }

    // Implementacja metody abstrakcyjnej
    @Override
    double area() {
        return Math.PI * radius * radius;
    }
}
```

W powyższym przykładzie klasa Shape ma abstrakcyjną metodę area, która jest deklaracją bez implementacji. Klasa Circle, dziedzicząca po Shape, musi zaimplementować tę metodę. Gdyby tego nie zrobiła, musiałaby również zostać oznaczona jako abstrakcyjna.

```java
interface Drawable {
    void draw(); // metoda abstrakcyjna, bez implementacji
}

class Rectangle implements Drawable {
    @Override
    public void draw() {
        System.out.println("Rysowanie prostokąta");
    }
}
```

W interfejsie Drawable metoda draw jest domyślnie abstrakcyjna (w interfejsach nie musimy pisać słowa kluczowego abstract), a klasa Rectangle implementująca ten interfejs musi dostarczyć implementację tej metody.

Kluczowe zalety abstrakcyjnych metod
Wymuszają spójność: Wszystkie klasy dziedziczące po klasie abstrakcyjnej lub implementujące interfejs muszą zaimplementować abstrakcyjne metody, co zapewnia spójność i jednolity sposób korzystania z tych klas.
Oddzielają interfejs od implementacji: Abstrakcyjne metody umożliwiają klasom dostarczającym konkretne implementacje na ukrycie szczegółów wewnętrznych, skupiając się jednocześnie na kontrakcie (tj. zestawie metod), jaki mają udostępnić.

1. Wsparcie Javy dla Abstrakcji
Java wspiera abstrakcję przez:
Tworzenie hierarchii klas z klasami bazowymi.
Abstrakcyjne klasy.
Interfejsy, które definiują kontrakt, jaki klasy implementujące muszą spełnić.
2. Abstrakcyjne Metody
Abstrakcyjna metoda ma sygnaturę i typ zwracany, ale brak ciała metody. Abstrakcyjne metody to metody niezaimplementowane.
Opisuje zachowanie, które powinny posiadać wszystkie klasy dziedziczące.
Przykład: Możemy mieć abstrakcyjną metodę move() w klasie Animal, którą muszą zaimplementować wszystkie klasy reprezentujące zwierzęta.
3. Konkretne Metody
Konkretna metoda ma ciało, zawiera kod, który jest wykonywany w odpowiednich warunkach.
Klasy abstrakcyjne i interfejsy mogą zawierać zarówno metody abstrakcyjne, jak i konkretne.
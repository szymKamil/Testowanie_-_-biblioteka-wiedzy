# inheritance

# Ihneritance (dziedziczenie)

Jedna z klas może rozszerzać drugą. Oznacza to, że metody i zmienne w klasie nadrzędnej (rodzicu) dostępne są też w klasie podrzędnej (dziecku).
Jedna klasa moze dzieciczyć tylko z JEDNEJ innej klasy.
Jednak drzewo dziedziczenia może być nieskończone.

## Konstruktor

Klasy dziedziczą też zmienne. Dlatego gdy klasa nadrzęna posiada konstruktor, trzeba stworzyć go rónież w klasie niższej. Można go jednak rozbudowac o wartości specyficzne dla niższej klasy.

Klasy dziedziczące są też traktowane jako klasy rodzica, dlatego np. w metodach możemy się do nich odwoływać. Dlatego:

```java
import javax.xml.transform.OutputKeys;

Animal animal = new Animal();
Dog dog = new Dog(); // Dog extends Animal

public static run(Animal animal) {
    *stuff() *
}

run(animal) - OK
run(dog) - OK
```

## Super

Super pozwala na odwołanie się do konstruktora klasy rodzica - zmiennych i metod.

```java
public class Dog extends Animal {
    public Dog(){
        super(value1, value2, value3...);
    }
}
```

Jeśli klasa posiada specyficzne dla siebie atrybuty, dodajemy je tak:

```java

public class Dog extends Animal {
    private String weight;

    public Dog(){
        super(value1, value2, value3...);
        this.weight = "20kg";
    }
}
```

## Zmiene prywatne

Jeśli klasa rodzica posiada zmienne oznaczone jako `private`, wówczas dziecko nie może bezpośrednio wchodzić z nimi w interakcję.
Wówczas warto zmienić wartość z `private` na `protected`. Protected oznacza, że klasy z tej samej paczki (pacage) mają dostęp do zmiennych.

## Overriding (nadpisywanie)

To tworzenie metody o takiej samej nazwie w subklasie, oraz takiej samej ilości zmiennych. Jest to przydatne, gdy chcemy zmienić metode w subklasie na specyficzną dla niej.
Zasadne jest dodawanie `@Override` by wskazać, że metoda została nadpisana.
Nadpisywanie może mieć 3 cele:
- Tworzenie zupełnie nowej logiki metody
- Może odwoływać się do metody rodzica, co jest zbędne.
- Metoda może rozszerzać możliwości metody rodzica o nowe funkcjonalności.
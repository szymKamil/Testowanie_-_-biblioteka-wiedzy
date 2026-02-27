# genAndabstract

# Generalization i abstraction

1. Abstrakcja (Abstraction)
Abstrakcja polega na ukrywaniu szczegółów implementacji, a ukazywaniu jedynie istotnych cech obiektu lub klasy. W Javie realizuje się to za pomocą:

Abstrakcyjnych klas (abstract class), które mogą zawierać zarówno metody abstrakcyjne (czyli niezaimplementowane), jak i metody z implementacją.
Interfejsów (interface), które mogą zawierać tylko deklaracje metod (w Javie 8 i wyższych mogą także zawierać metody domyślne z implementacją).
Abstrakcja umożliwia tworzenie ogólnych, niezależnych od szczegółów implementacyjnych klas i interfejsów, które są wykorzystywane przez inne części programu.

```java
abstract class Animal {
    abstract void makeSound(); // Metoda abstrakcyjna, która nie ma implementacji
}

class Dog extends Animal {
    void makeSound() {
        System.out.println("Bark");
    }
}
```

W powyższym przykładzie Animal jest klasą abstrakcyjną, która definiuje ogólną metodę makeSound(), ale jej implementacja jest specyficzna dla klasy Dog.

## Generalizacja (Generalization)

Generalizacja odnosi się do procesu tworzenia ogólnych klas, które mogą reprezentować bardziej specyficzne podklasy. Jest to technika, w której łączy się podobne klasy w jedną, bardziej ogólną klasę nadrzędną. Generalizacja jest stosowana, aby ułatwić ponowne użycie kodu i uniknąć powielania kodu w klasach podrzędnych.

W praktyce generalizacja oznacza tworzenie hierarchii dziedziczenia, w której bardziej ogólne klasy (np. Animal) mogą zostać rozbudowane o bardziej specyficzne klasy (np. Dog).

```java
class Animal {
    void move() {
        System.out.println("Animal moves");
    }
}

class Dog extends Animal {
    void move() {
        System.out.println("Dog runs");
    }
}
```

Tutaj Dog jest bardziej specyficzną klasą w stosunku do ogólnej klasy Animal. Klasa Animal może zostać zrozumiana jako “generalizacja” różnych rodzajów zwierząt.

Podsumowanie różnic:
Abstrakcja polega na ukrywaniu szczegółów implementacji i dostarczaniu tylko niezbędnych informacji użytkownikowi klasy (np. poprzez klasy abstrakcyjne lub interfejsy).
Generalizacja to proces tworzenia bardziej ogólnych klas (rodziców), które obejmują wspólne cechy dla bardziej specyficznych klas (dzieci).
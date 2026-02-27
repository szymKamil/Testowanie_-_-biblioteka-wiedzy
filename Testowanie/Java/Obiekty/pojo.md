# pojo

# POJO

POJO - Plain old java object.

## Czym jest POJO?

POJO (Plain Old Java Object) to termin używany w Javie do opisania prostych obiektów, które nie mają dodatkowych ograniczeń ani wymagań.
POJO jest używane w kontekście różnych frameworków, jak Hibernate czy Spring, które ułatwiają pracę z danymi i obiektami, ale nie narzucają na nie skomplikowanych zależności.

## Cechy POJO

- **Prosty**: Nie wymaga dziedziczenia od konkretnych klas ani implementacji specjalnych interfejsów.
- **Bezpośredni**: Może mieć dowolne pola i metody, które są niezbędne do reprezentowania danych.
- **Wolny od zależności**: Nie jest związany z frameworkami, co ułatwia jego użycie w różnych kontekstach.

## Przykład POJO

Oto przykład klasy `Person`, która reprezentuje osobę:

```java
public class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }
}
```

### Zastosowania POJO

POJO jest często używany do reprezentowania danych w aplikacjach, a jego prostota sprawia, że jest łatwy w testowaniu i używaniu.

POJO gdy zostanie stworzony, zadko kiedy jest modyfikowany.

POJO test stosowany, gdy chcemy modyfikować wartości w nim zawarte. Jeśli mają być niezmieniane, to lepiej stosować `record`.

# Bean (Java Bean)

Bean to POJO z dodatkowymi zasadami.

- Muszą mieć domyślny konstruktor bezargumentowy.
- Muszą mieć metody dostępu (gettery i settery) do wszystkich pól, które mają być publiczne.
- Muszą być serializowalne, aby mogły być łatwo przesyłane lub przechowywane.

## Przykład Bean

```java
import java.io.Serializable;

public class Employee implements Serializable {
private String name;
private int id;

    public Employee() { }  // Domyślny konstruktor

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }
}
```

## Serializacja

Serializacja to proces przekształcania obiektu w postać, która może być łatwo przechowywana lub przesyłana. W kontekście Java, serializacja umożliwia zapisanie stanu obiektu do strumienia bajtów, co pozwala na:

Przechowywanie: Można zapisywać obiekty do plików, a następnie je odczytywać, co umożliwia trwałe przechowywanie stanu aplikacji.

Przesyłanie: Obiekty mogą być przesyłane przez sieć, na przykład podczas komunikacji między serwerem a klientem w aplikacjach rozproszonych.

ak działa serializacja w Javie?
W Javie serializacja jest realizowana za pomocą interfejsu Serializable. Aby obiekt mógł być serializowany, klasa musi implementować ten interfejs. Oto jak to działa:

Implementacja interfejsu Serializable: Klasa musi implementować ten interfejs, aby sygnalizować, że jej obiekty mogą być serializowane. Przykład:

```java
import java.io.Serializable;

public class Person implements Serializable {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    // Gettery i settery...
}
```

Serializacja: Proces polega na użyciu klasy ObjectOutputStream, która zapisuje stan obiektu do strumienia. Przykład:

```java
import java.io.FileOutputStream;
import java.io.ObjectOutputStream;

public class SerializationExample {
    public static void main(String[] args) {
        Person person = new Person("Alice", 30);
        try (FileOutputStream fileOut = new FileOutputStream("person.ser");
             ObjectOutputStream out = new ObjectOutputStream(fileOut)) {
            out.writeObject(person);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

Deserializacja: Odczyt obiektu z pliku polega na użyciu klasy ObjectInputStream, która przywraca stan obiektu z strumienia. Przykład:

```java
import java.io.FileInputStream;
import java.io.ObjectInputStream;

public class DeserializationExample {
    public static void main(String[] args) {
        Person person = null;
        try (FileInputStream fileIn = new FileInputStream("person.ser");
             ObjectInputStream in = new ObjectInputStream(fileIn)) {
            person = (Person) in.readObject();
        } catch (Exception e) {
            e.printStackTrace();
        }
        System.out.println("Name: " + person.getName() + ", Age: " + person.getAge());
    }
}
```

Nie wszystkie pola są serializowane: Można oznaczyć pola, które nie powinny być serializowane, używając słowa kluczowego transient. Przykład:

```java
public class Person implements Serializable {
    private String name;
    private int age;
    transient private String password;  // To pole nie zostanie zserializowane
}
```

Zastosowanie serializacji
- obiektów do plików: Umożliwia tworzenie kopii zapasowych stanu aplikacji.
- Przesyłanie obiektów przez sieć: Wykorzystywane w aplikacjach kliencko-serwerowych.
- Komunikacja między różnymi komponentami aplikacji: Umożliwia przekazywanie obiektów między różnymi warstwami aplikacji.
- Serializacja jest istotnym elementem programowania w Javie, szczególnie w kontekście aplikacji rozproszonych i zarządzania stanem obiektów.
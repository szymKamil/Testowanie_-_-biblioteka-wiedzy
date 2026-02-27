# composition

# Composition (kompozycja)

Kompozycja w Javie (i w ogólności w programowaniu obiektowym) to technika projektowania, w której klasa składa się z innych obiektów, aby osiągnąć złożone funkcjonalności. Zamiast dziedziczenia funkcji z innej klasy, kompozycja pozwala klasie na “posiadanie” obiektów innych klas jako swoich pól, dzięki czemu może korzystać z ich metod i atrybutów. Kompozycja jest często uważana za bardziej elastyczną i łatwiejszą do utrzymania niż dziedziczenie, szczególnie w skomplikowanych hierarchiach klas.

Przyjmuje się w Javie, że tworzac program należy przyjąć najpierw perspektywę zastosowania kompozycji, a dopiero potem dziedziczenia.

```java
class Silnik {
    private String typ;

    public Silnik(String typ) {
        this.typ = typ;
    }

    public void uruchom() {
        System.out.println("Silnik " + typ + " uruchomiony.");
    }
}

class Samochod {
    private Silnik silnik;

    public Samochod(Silnik silnik) {
        this.silnik = silnik;
    }

    public void uruchomSamochod() {
        silnik.uruchom(); // Korzystamy z metody uruchom() obiektu Silnik
        System.out.println("Samochód uruchomiony.");
    }
}

public class Main {
    public static void main(String[] args) {
        Silnik silnik = new Silnik("Diesel");
        Samochod samochod = new Samochod(silnik);
        samochod.uruchomSamochod();
    }
}
```

W powyższym przykładzie:

Klasa Samochod komponuje się z klasy Silnik. Klasa Samochod ma pole typu Silnik, które pozwala jej “posiadać” obiekt Silnik i korzystać z jego metod.
Metoda `uruchomSamochod()` w klasie Samochod wywołuje metodę `uruchom()` na obiekcie Silnik, dzięki czemu Samochod może korzystać z funkcji silnika.
Zastosowania i zalety kompozycji
Elastyczność: Kompozycja pozwala dynamicznie zmieniać zachowanie klasy w czasie działania programu, ponieważ można wymieniać składniki (np. silniki o różnych typach w samochodzie).
Ponowne użycie kodu: Kompozycja promuje ponowne używanie kodu, ponieważ zamiast kopiować lub dziedziczyć funkcjonalności, można je po prostu “wkomponować”.
Lepsze zarządzanie zależnościami: Klasy mogą być tworzone niezależnie, co ułatwia testowanie i debugowanie, a także minimalizuje zależności pomiędzy nimi.
Unikanie problemów z dziedziczeniem: Dziedziczenie może prowadzić do skomplikowanych hierarchii i problemów z rozszerzaniem klas. Kompozycja pozwala unikać tych problemów, ponieważ nie tworzy hierarchii klas.

### Kompozycja a dziedziczenie

Dziedziczenie reprezentuje relację “jest” (np. Pies jest Zwierzęciem).
Kompozycja reprezentuje relację “ma” (np. Samochód ma Silnik).

## Odłowamie do metod innej klasy

- Za pomocą `gettera`: przywołujemy klasę “zbierającą” która posiada getter i odwołujemy się do związanej z nią metody:

```java
thePC.getMonitor().drawPixelAt(int x, int y, String color);
```

- Za pomocą metod w klasie zbierającej:

```java
//Klasa PC zawierająca metodę
private void drawLogo() {
    //Klasa monitor jest powiązana z PC dlatego:
    monitor.drawPixelAt(int x, int y, String color);
}
```
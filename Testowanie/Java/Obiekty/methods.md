# methods

# Methods

Metoda w Javie
Ogólnie rzecz biorąc, metoda jest sposobem na wykonanie jakiegoś zadania. Podobnie, metoda w Javie jest zbiorem instrukcji, które wykonują określone zadanie. Zapewnia to możliwość ponownego wykorzystania kodu. Możemy również łatwo modyfikować kod za pomocą metod. W tej sekcji dowiemy się , czym jest metoda w Javie, rodzaje metod, deklaracja metody i jak wywołać metodę w Javie.
*Czym jest metoda w Javie?*
Metoda to blok kodu, zbiór instrukcji lub zestaw kodu zgrupowany w celu wykonania określonego zadania lub operacji. Służy do osiągnięcia wielokrotnego użytku kodu. Piszemy metodę raz i używamy jej wiele razy. Nie musimy pisać kodu od nowa. Zapewnia również łatwą modyfikację i czytelność kodu, po prostu dodając lub usuwając fragment kodu. Metoda jest wykonywana tylko wtedy, gdy ją wywołamy.
Najważniejszą metodą *w Javie jest metoda main(). Jeśli chcesz dowiedzieć się więcej o metodzie main(), kliknij link https://www.javatpoint.com/java-main-method.*
Deklaracja metody
Deklaracja metody zawiera informacje o atrybutach metody, takich jak widoczność, typ zwracany, nazwa i argumenty. Składa się z sześciu komponentów, które są znane jako nagłówek metody.

Sygnatura metody: Każda metoda ma sygnaturę metody. Jest to część deklaracji metody. Zawiera nazwę metody i listę parametrów.
Specyfikator dostępu: Specyfikator dostępu lub modyfikator to typ dostępu metody. Określa on widoczność metody. Java udostępnia cztery typy specyfikatorów dostępu:
Public: Metoda jest dostępna dla wszystkich klas, gdy używamy specyfikatora publicznego w naszej aplikacji.
Private: Gdy używamy prywatnego specyfikatora dostępu, metoda jest dostępna tylko w klasach, w których jest zdefiniowana.
Protected: Gdy używamy chronionego specyfikatora dostępu, metoda jest dostępna w ramach tego samego pakietu lub podklas w innym pakiecie.
Default: Gdy nie używamy żadnego specyfikatora dostępu w deklaracji metody, Java domyślnie używa domyślnego specyfikatora dostępu. Jest on widoczny tylko z tego samego pakietu.
Typ zwracany: Typ zwracany to typ danych zwracany przez metodę. Może to być prymitywny typ danych, obiekt, kolekcja, void itp. Jeśli metoda niczego nie zwraca, używamy słowa kluczowego void.
Nazwa metody: Jest to unikalna nazwa, która jest używana do zdefiniowania nazwy metody. Musi ona odpowiadać funkcjonalności metody. Załóżmy, że jeśli tworzymy metodę odejmowania dwóch liczb, nazwa metody musi brzmieć odejmowanie(). Metoda jest wywoływana przez jej nazwę.

Lista parametrów: Jest to lista parametrów oddzielonych przecinkiem i ujętych w parę nawiasów. Zawiera typ danych i nazwę zmiennej. Jeśli metoda nie ma parametrów, nawiasy należy pozostawić puste.
Method Body: Jest to część deklaracji metody. Zawiera wszystkie działania, które mają zostać wykonane. Jest zamknięty w parze nawiasów klamrowych.

### Metoda statyczna

Metoda zawierająca słowo kluczowe static jest znana jako metoda statyczna. Innymi słowy, metoda, która należy do klasy, a nie do instancji klasy, jest znana jako metoda statyczna. Możemy również utworzyć metodę statyczną, używając słowa kluczowego static przed nazwą metody.
Główną zaletą metody statycznej jest to, że możemy ją wywołać bez tworzenia obiektu. Może ona uzyskiwać dostęp do statycznych elementów danych, a także zmieniać ich wartość. Służy do tworzenia metody instancji. Jest wywoływana przy użyciu nazwy klasy. Najlepszym przykładem metody statycznej jest metoda main().

## Metoda instancji

Metoda klasy jest znana jako metoda instancji. Jest to niestatyczna metoda zdefiniowana w klasie. Przed wywołaniem metody instancji konieczne jest utworzenie obiektu tej klasy. Zobaczmy przykład metody instancji.

## Rodzaje metod

W Javie istnieją dwa główne rodzaje metod instancyjnych:

1. Accessor Method (Getter)
Metoda dostępowa, zwana też “getterem”, służy do pobierania wartości prywatnych pól klasy.
Nie zmienia stanu obiektu – tylko zwraca wartość.
Zwykle jest publiczna i ma prefiks get przed nazwą pola.
2. Mutator Method (Setter)
Metoda mutująca, zwana też “setterem”, służy do ustawiania lub zmiany wartości prywatnych pól klasy.
Zmienia stan obiektu.
Zwykle jest publiczna i ma prefiks set przed nazwą pola.

# Varargs

Trzy kropki po obiekcie w Javie oznaczają użycie operatora rozprzestrzenienia (ang. varargs, czyli variable-length arguments). Umożliwia to przekazywanie zmiennej liczby argumentów do metody. Operator ten jest często używany w sytuacjach, gdy nie wiadomo z góry, ile argumentów zostanie przekazanych.

```java
public class Example {
public static void printNumbers(int... numbers) {
for (int number : numbers) {
System.out.println(number);
}
}

    public static void main(String[] args) {
        printNumbers(1, 2, 3); // Przekazujemy 3 liczby
        printNumbers(4, 5);    // Przekazujemy 2 liczby
        printNumbers();         // Przekazujemy 0 liczb
    }
}
```

W powyższym przykładzie metoda printNumbers przyjmuje zmienną liczbę argumentów typu int. Dzięki temu możemy przekazać do niej dowolną liczbę argumentów, a nawet żadnego.

Warto wiedzieć:
Można mieć tylko jeden parametr varargs w sygnaturze metody. [Sygnatura metody w Javie to unikalna kombinacja nazwy metody i jej parametrów, która pozwala na identyfikację danej metody w danej klasie. Sygnatura nie obejmuje typu zwracanego metody ani modyfikatorów dostępu (np. public, private).]
Parametr varargs musi być ostatnim parametrem w metodzie.

## Modyfikatory metod

| Modyfikator | Opis |
| --- | --- |
| **public** | Metoda jest dostępna dla wszystkich innych klas. |
| **protected** | Metoda jest dostępna tylko dla klas w tym samym pakiecie oraz dla podklas. |
| **package-private** | (brak modyfikatora) Metoda jest dostępna tylko dla klas w tym samym pakiecie. |
| **private** | Metoda jest dostępna tylko wewnątrz klasy, w której jest zdefiniowana. |
| **static** | Metoda należy do klasy, a nie do instancji obiektu; można ją wywołać bez tworzenia obiektu. |
| **final** | Metoda nie może być przesłonięta w klasach dziedziczących. |
| **abstract** | Metoda nie posiada implementacji; musi być zaimplementowana w klasach dziedziczących. |
| **synchronized** | Metoda może być wykonana przez jeden wątek naraz (przydatne w programowaniu współbieżnym). |
| **native** | Metoda jest zaimplementowana w kodzie natywnym (np. C lub C++) poza Javą. |
| **strictfp** | Metoda stosuje standardową precyzję operacji zmiennoprzecinkowych IEEE 754. |
| **default** | Słowo kluczowe umożliwia dodanie implementacji w interfejsie (od Javy 8). |
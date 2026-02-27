# runTimeCompileTimeCode

# Różnica pomiędzy **Compile Time Code** a **Runtime Code** w Java

W Javie, **compile time code** i **runtime code** odnoszą się do różnych etapów procesu wykonania programu.

## 1. Compile Time Code (kod w czasie kompilacji)

**Compile time** to etap, na którym kod źródłowy jest przekształcany przez kompilator (np. `javac` w przypadku Javy) do kodu bajtowego (bytecode), który następnie może być uruchomiony przez maszynę wirtualną Javy (JVM). Kompilator sprawdza wtedy, czy kod spełnia zasady składniowe i jest zgodny z typami danych (type checking).

Przykłady operacji wykonywanych w czasie kompilacji:
- Sprawdzenie poprawności składniowej (np. zamknięcie nawiasów, średniki na końcu linii).
- Sprawdzenie typów danych i ich zgodności (np. próba przypisania `String` do `int` spowoduje błąd).
- Ustalanie lokalnych zmiennych i metod oraz sprawdzanie ich poprawności.

Jeśli kod zawiera błędy na etapie kompilacji, kompilator zgłosi błędy i nie wygeneruje kodu bajtowego, dopóki te błędy nie zostaną naprawione.

## 2. Runtime Code (kod w czasie wykonania)

**Runtime** to etap, na którym kod bajtowy (bytecode) jest wykonywany przez maszynę wirtualną Javy (JVM). W tym czasie kod jest faktycznie uruchamiany, a JVM zarządza zasobami systemowymi oraz obsługuje potencjalne błędy, które mogą wystąpić w czasie działania programu.

Przykłady sytuacji i operacji, które występują w czasie wykonywania:
- Wywoływanie metod i operacje na zmiennych.
- Alokacja pamięci i zarządzanie nią (np. Garbage Collection).
- Obsługa wyjątków, które mogą wystąpić tylko w czasie działania (np. `NullPointerException`, `ArrayIndexOutOfBoundsException`).
- Dynamiczne ładowanie klas i bibliotek.

Jeśli wystąpi błąd w runtime (jak np. podzielenie przez zero), program przerwie działanie i wygeneruje odpowiedni wyjątek, ale do momentu uruchomienia kodu taki błąd może nie być wykryty.

## Podsumowanie

| Aspekt | Compile Time Code | Runtime Code |
| --- | --- | --- |
| Etap | Kompilacja (przekształcanie na bytecode) | Wykonanie kodu bajtowego |
| Główne zadanie | Sprawdzanie składni, typów i struktury | Uruchamianie kodu i zarządzanie pamięcią |
| Przykłady błędów | Błędy składniowe, błędy typów | Wyjątki, takie jak `NullPointerException` |
| Przykłady operacji | Sprawdzenie typów, poprawność składni | Wywołanie metod, alokacja pamięci |

# Polimorfizm, Casting a Runtime

Polimorfizm i casting mają szczególne znaczenie w czasie wykonania (**runtime**), ponieważ wtedy JVM podejmuje decyzje dotyczące właściwego typu obiektu oraz dostępnych metod.

## Polimorfizm i Dynamiczne Wiązanie (Dynamic Binding)

Polimorfizm umożliwia **dynamiczne wiązanie** metod. Oznacza to, że JVM w czasie wykonania ustala, która metoda ma być wywołana, w zależności od rzeczywistego typu obiektu, a nie zmiennej referencyjnej. Dzięki temu wywołanie metody na zmiennej klasy bazowej, która odwołuje się do obiektu klasy pochodnej, spowoduje uruchomienie nadpisanej wersji tej metody z klasy pochodnej.

Przykład:

```java
class Animal {
    void makeSound() { System.out.println("Animal sound"); }
}

class Dog extends Animal {
    @Override
    void makeSound() { System.out.println("Woof"); }
}

Animal myAnimal = new Dog();
myAnimal.makeSound();  // W runtime zostanie wywołana metoda z klasy Dog: "Woof"
```

# Compile-Time Typing vs. Runtime Typing w Javie

**Compile-time typing** i **runtime typing** to terminy związane z systemem typów w językach programowania, w tym w Javie. Odnoszą się one do momentu, w którym sprawdzana jest zgodność typów danych – w czasie kompilacji czy wykonania programu. Nie jest to dokładnie to samo, co **compile time code** i **runtime code**, ale są to pojęcia powiązane.

## Compile-Time Typing (statyczne typowanie)

**Compile-time typing**, czyli **statyczne typowanie**, oznacza, że typy zmiennych są sprawdzane podczas kompilacji, a nie w czasie wykonania. Java jest językiem statycznie typowanym, co oznacza, że typy zmiennych muszą być określone w czasie kompilacji, a wszelkie niezgodności w typach (np. przypisanie `String` do `int`) zostaną wykryte już na tym etapie.

Przykład:

```java
int number = "tekst";  // Błąd kompilacji, typy są niezgodne
```

W tym przypadku błąd zostanie wykryty przez kompilator i kod nie zostanie skompilowany.

Runtime Typing (dynamiczne typowanie)
Runtime typing, czyli dynamiczne typowanie, polega na sprawdzaniu zgodności typów w czasie wykonania programu. W Javie większość typów jest ustalana w czasie kompilacji (dzięki statycznemu typowaniu), ale w niektórych przypadkach typ musi zostać sprawdzony w runtime, np. przy downcastingu lub polimorfizmie.

```java
Object obj = "Hello, Java!";
String str = (String) obj;  // Sprawdzenie typu obiektu w czasie wykonania
```

W powyższym przykładzie obj jest typu Object, ale podczas rzutowania do String JVM sprawdzi, czy obiekt faktycznie jest typu String. Jeśli nie, zostanie zgłoszony wyjątek ClassCastException.

Compile-Time Typing vs. Runtime Typing
Java używa statycznego typowania, co oznacza, że typy większości zmiennych są sprawdzane w czasie kompilacji, co zapewnia bezpieczeństwo typów. Jednak w przypadkach polimorfizmu i castingu JVM sprawdza typy obiektów dynamicznie, czyli w czasie wykonania programu.
| Typ Typowania | Czas sprawdzania | Przykład | Błędy |
|———————-|——————|————————-|———————-|
| Compile-Time Typing | Kompilacja | `int a = "tekst";` | Błędy kompilacji |
| Runtime Typing | Czas wykonania | `(String) obj` | `ClassCastException` |
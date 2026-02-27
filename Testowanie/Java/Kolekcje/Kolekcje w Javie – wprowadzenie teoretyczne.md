# Kolekcje w Javie – wprowadzenie teoretyczne

## Dlaczego używamy interfejsu przy tworzeniu kolekcji?

W Javie kolekcje tworzymy przez interfejs, a nie przez konkretną klasę implementacji:

```java
List<String> lista = new ArrayList<>();  // ✅ zalecane
ArrayList<String> lista = new ArrayList<>();  // rzadziej stosowane
```

### 1. Łatwa zmiana implementacji

Użycie interfejsu jako typu zmiennej pozwala w dowolnym momencie podmienić implementację
bez zmiany reszty kodu:

```java
List<String> lista = new ArrayList<>();
// Zmiana na LinkedList – wystarczy zmienić jedną linię
lista = new LinkedList<>();
```

### 2. Polimorfizm – uniwersalne metody

Metoda przyjmująca interfejs działa z **każdą** jego implementacją:

```java
public void przetworzListe(List<String> lista) {
    lista.forEach(System.out::println);
}

przetworzListe(new ArrayList<>());   // działa
przetworzListe(new LinkedList<>());  // też działa
```

### 3. Ukrywanie szczegółów implementacji

Kod skupia się na **zachowaniu** kolekcji (co robi), nie na tym jak jest zrealizowana (jak to robi).
Dzięki temu jest czytelniejszy i łatwiejszy w utrzymaniu:

```java
List<Integer> liczby = new ArrayList<>();
liczby.add(1);
liczby.add(2);
System.out.println(liczby); // [1, 2]
```

### 4. Zasada podstawienia Liskov (LSP)

Interfejs jako typ pozwala zamieniać implementacje bez wpływu na działanie kodu –
każda implementacja `List` zachowuje się jak `List`:

```java
List<String> lista = new ArrayList<>();
lista.add("Test");
lista = new LinkedList<>(lista); // zamiana implementacji, kod działa tak samo
```

> **LSP (Liskov Substitution Principle)** – jedna z zasad SOLID. Mówi, że obiekt klasy
pochodnej (lub implementacji interfejsu) powinien móc zastąpić obiekt klasy bazowej
(lub interfejsu) bez zepsucia działania programu.
> 

---

## Wady programowania przez interfejs

**Wydajność** – źle dobrana implementacja może wpłynąć na wydajność:

```java
// LinkedList jest wolna przy dostępie losowym (get(index))
List<String> lista = new LinkedList<>();
lista.get(500); // O(n) – musi przejść przez 500 elementów!

// ArrayList jest szybka przy dostępie losowym
List<String> lista2 = new ArrayList<>();
lista2.get(500); // O(1) – bezpośredni dostęp
```

**Brak dostępu do metod specyficznych dla implementacji:**

```java
// Interfejs List nie ma metody trimToSize()
List<String> lista = new ArrayList<>();
lista.trimToSize(); // ❌ błąd kompilacji – brak tej metody w interfejsie List

// Musisz użyć konkretnego typu
ArrayList<String> arrayList = new ArrayList<>();
arrayList.trimToSize(); // ✅ działa
```

**Brak typów prymitywnych** – kolekcje wymagają typów opakowujących:

```java
List<int> lista;      // ❌ błąd kompilacji
List<Integer> lista;  // ✅ autoboxing obsługuje konwersję automatycznie
```

---

> Szczegółowe omówienie hierarchii interfejsów (`Collection`, `List`, `Set`, `Queue`, `Map`),
konkretnych implementacji i kiedy których używać → kolejne sekcje notatek o kolekcjach.
>
# reference

# Reference

W Javie referencje działają jako odwołania do obiektów, a nie przechowują bezpośrednio danych obiektu (w przeciwieństwie do typów prostych, jak int, double itp.). Dzięki temu praca z obiektami w Javie jest bardziej elastyczna, a programy mogą efektywniej korzystać z pamięci. Oto jak to wygląda:

Tworzenie referencji: Gdy tworzysz obiekt przy użyciu słowa kluczowego new, zmienna przechowuje odniesienie do lokalizacji w pamięci, w której obiekt został utworzony.

```java
String tekst = new String("Hello");
```

Tutaj tekst jest referencją do obiektu typu String przechowującego “Hello”.

Kopiowanie referencji: Gdy przypisujesz jedną referencję do innej zmiennej, obie referencje wskazują na ten sam obiekt w pamięci.

```java
String tekst2 = tekst;
```

Teraz tekst i tekst2 wskazują na ten sam obiekt “Hello”. Zmiana obiektu za pomocą jednej referencji wpłynie na jego stan również dla drugiej referencji (o ile obiekt jest mutowalny).

Porównywanie referencji: Operator == porównuje referencje, a nie faktyczną zawartość obiektów. Dwa różne obiekty o tej samej wartości będą miały różne referencje, więc == zwróci false.

```java
String a = new String("Hello");
String b = new String("Hello");
System.out.println(a == b); // false, ponieważ `a` i `b` to różne obiekty.
System.out.println(a.equals(b)); // true, ponieważ `equals` porównuje zawartość.
```

Typy referencyjne a typy proste: Typy proste (np. int, double) przechowują bezpośrednie wartości, a nie referencje. Przykładowo:

```java
int x = 5;
int y = x; // tutaj `y` dostaje kopię wartości `x`, a nie referencję.
```

Zarządzanie pamięcią: Referencje są kluczowe dla działania garbage collectora, który zwalnia pamięć dla obiektów, do których nie ma już żadnych referencji. Jeśli wszystkie referencje do obiektu zostaną utracone, obiekt stanie się kandydatem do usunięcia przez garbage collector.

Dzięki referencjom Java efektywnie zarządza pamięcią i pozwala na współdzielenie obiektów w programie, co zmniejsza obciążenie pamięci przy zachowaniu bezpieczeństwa typów.
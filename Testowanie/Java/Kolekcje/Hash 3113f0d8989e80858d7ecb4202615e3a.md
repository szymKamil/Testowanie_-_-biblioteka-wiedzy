# Hash

# `hashCode()` i `equals()` w Javie

Metody `hashCode()` i `equals()` są zdefiniowane w klasie `Object` i dziedziczone przez wszystkie klasy w Javie.
Są kluczowe dla poprawnego działania struktur haszujących: `HashMap`, `HashSet`, `Hashtable`.

---

## 1. Kontrakt `hashCode()` i `equals()`

Java wymaga spełnienia następujących zasad:

**1. Równe obiekty muszą mieć ten sam hash code:**

```
a.equals(b) == true  →  a.hashCode() == b.hashCode()
```

**2. Nierówne obiekty mogą mieć ten sam hash code (kolizja):**

```
a.equals(b) == false  →  a.hashCode() może == b.hashCode()
```

Im mniej kolizji, tym lepsza wydajność.

**3. Hash code powinien być stały przez "życie" obiektu:**
Jeśli obiekt jest kluczem w `HashMap`, a jego `hashCode()` się zmieni,
element może stać się **niedostępny** – to częsty, trudny do wykrycia bug.

> ⚠️ **Złota zasada:** jeśli nadpisujesz `equals()`, **zawsze** nadpisuj też `hashCode()` i odwrotnie.
Niedotrzymanie tego kontraktu prowadzi do nieprzewidywalnego zachowania kolekcji haszujących.
> 

---

## 2. Jak działa hashowanie w kolekcjach?

```
Obiekt → hashCode() → indeks bucketa → [porównanie equals()] → wynik
```

1. Java oblicza `hashCode()` obiektu
2. Na podstawie hash code wyznacza **bucket** (miejsce w wewnętrznej tablicy)
3. Jeśli bucket jest zajęty (kolizja) – porównuje przez `equals()`
4. Jeśli `equals()` zwraca `true` – obiekt znaleziony/zastąpiony

```java
Map<String, Integer> map = new HashMap<>();
map.put("Alice", 30);

// Wewnętrznie:
// 1. "Alice".hashCode() → np. 63476538
// 2. indeks = 63476538 % rozmiar_tablicy → np. 6
// 3. bucket[6] = ("Alice", 30)
// 4. map.get("Alice") → hashCode() → bucket[6] → equals("Alice") → 30
```

---

## 3. Domyślna implementacja

Domyślna implementacja z klasy `Object`:

- `equals()` – porównuje **referencje** (`==`)
- `hashCode()` – oparty na **adresie w pamięci** obiektu

```java
String a = new String("hello");
String b = new String("hello");

// Bez nadpisania:
a.equals(b);     // false (porównuje referencje)
a.hashCode() == b.hashCode(); // może być false

// String nadpisuje obie metody:
a.equals(b);     // true (porównuje zawartość)
a.hashCode() == b.hashCode(); // true
```

---

## 4. Implementacja `equals()`

Poprawna implementacja musi spełniać:

- **Zwrotność:** `a.equals(a)` = `true`
- **Symetria:** `a.equals(b)` = `b.equals(a)`
- **Przechodniość:** jeśli `a.equals(b)` i `b.equals(c)`, to `a.equals(c)`
- **Spójność:** wielokrotne wywołanie daje ten sam wynik
- **Null:** `a.equals(null)` = `false`

```java
public class Person {
    private String name;
    private int age;

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;                    // 1. ten sam obiekt
        if (o == null || getClass() != o.getClass())   // 2. null lub inny typ
            return false;
        Person person = (Person) o;                    // 3. rzutowanie
        return age == person.age &&                    // 4. porównanie pól
               Objects.equals(name, person.name);
    }
}
```

---

## 5. Implementacja `hashCode()`

### Ręczna implementacja (klasyczny wzorzec)

```java
@Override
public int hashCode() {
    int result = 17;                                          // startowa liczba pierwsza
    result = 31 * result + (name == null ? 0 : name.hashCode()); // mnożnik 31
    result = 31 * result + age;
    return result;
}
```

**Dlaczego 31?**

- Jest liczbą pierwszą → lepsze rozproszenie wartości
- Wydajna optymalizacja: `31 * x = (x << 5) - x`

### `Objects.hash()` – zalecane od Javy 7

```java
@Override
public int hashCode() {
    return Objects.hash(name, age); // wygodne i poprawne
}
```

### Automatyczne generowanie

W IntelliJ IDEA: `Alt+Insert` → `hashCode() and equals()`
W Eclipse: `Source` → `Generate hashCode() and equals()`

---

## 6. Dobre i złe przykłady

```java
// ✅ Dobry hashCode – dobre rozproszenie
@Override
public int hashCode() {
    int result = 17;
    result = 31 * result + (name == null ? 0 : name.hashCode());
    result = 31 * result + age;
    return result;
}

// ❌ Zły hashCode – wszystkie obiekty w tym samym bucket
@Override
public int hashCode() {
    return 1; // każdy obiekt ma hash = 1 → maksymalna liczba kolizji → O(n)!
}

// ❌ Zły hashCode – używa zmiennych pól (problem gdy obiekt jest kluczem)
@Override
public int hashCode() {
    return Objects.hash(name, System.currentTimeMillis()); // zmienia się!
}
```

---

## 7. Pełny przykład klasy z `equals()` i `hashCode()`

```java
import java.util.Objects;

public class Person {
    private final String name;
    private final int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Person person = (Person) o;
        return age == person.age && Objects.equals(name, person.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }

    @Override
    public String toString() {
        return "Person{name='" + name + "', age=" + age + "}";
    }
}

// Użycie w HashSet i HashMap
Set<Person> zbior = new HashSet<>();
zbior.add(new Person("Alice", 30));
zbior.add(new Person("Alice", 30)); // duplikat – nie zostanie dodany
System.out.println(zbior.size());   // 1 – poprawne dzięki equals() i hashCode()

Map<Person, String> map = new HashMap<>();
map.put(new Person("Bob", 25), "developer");
System.out.println(map.get(new Person("Bob", 25))); // "developer" – działa!
```

---

## 8. Kolizje – co się dzieje?

Gdy dwa różne obiekty mają ten sam `hashCode()` – to **kolizja**:

```
hashCode("Alice") = 5  →  bucket[5]: Alice → Bob → Charlie (lista/drzewo)
```

- **Java 7 i wcześniej:** kolizje obsługiwane przez listę powiązaną → O(n) w najgorszym
- **Java 8+:** gdy w jednym bucket jest ≥8 elementów → zamiana na **drzewo czerwono-czarne** → O(log n)

---

## 9. Wytyczne implementacji – podsumowanie

| Zasada | Opis |
| --- | --- |
| Używaj tych samych pól w `equals()` i `hashCode()` | Kontrakt wymaga zgodności |
| Preferuj `Objects.hash()` | Prostsze i bezpieczniejsze |
| Używaj `final` pól | Stałość hash code |
| Nie używaj pól zmiennych | Może powodować utratę elementów w mapie |
| Zawsze nadpisuj obie metody razem | Nigdy tylko jedną |
| Unikaj `return 1` | Dramatycznie spowalnia kolekcje haszujące |
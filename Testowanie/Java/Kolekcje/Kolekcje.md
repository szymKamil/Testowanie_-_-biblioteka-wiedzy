# Kolekcje

# Java Collections Framework (JCF)

`java.util` – pakiet zawierający wszystkie klasy i interfejsy kolekcji.

---

## 1. Hierarchia interfejsów

```
java.lang.Iterable
└── java.util.Collection
    ├── List
    │   ├── ArrayList
    │   ├── LinkedList
    │   └── Vector (przestarzały)
    ├── Set
    │   ├── HashSet
    │   ├── LinkedHashSet
    │   └── SortedSet
    │       └── NavigableSet
    │           └── TreeSet
    └── Queue
        ├── PriorityQueue
        └── Deque
            ├── ArrayDeque
            └── LinkedList

java.util.Map  (osobna gałąź – NIE dziedziczy po Collection!)
    ├── HashMap
    ├── LinkedHashMap
    └── SortedMap
        └── NavigableMap
            └── TreeMap
```

> `Map` nie jest częścią hierarchii `Collection`, ale należy do Java Collections Framework.
`Iterable` to najwyższy poziom – umożliwia użycie pętli `for-each`.
> 

---

## 2. Interfejs `Collection`

Podstawowy interfejs definiujący wspólne operacje dla wszystkich kolekcji (poza `Map`).

### Kluczowe metody

| Metoda | Opis |
| --- | --- |
| `boolean add(E e)` | Dodaje element |
| `boolean remove(Object o)` | Usuwa element (pierwsze wystąpienie) |
| `int size()` | Liczba elementów |
| `boolean isEmpty()` | Czy kolekcja jest pusta |
| `boolean contains(Object o)` | Czy zawiera dany element |
| `boolean addAll(Collection<? extends E> c)` | Dodaje wszystkie elementy z innej kolekcji |
| `boolean removeAll(Collection<?> c)` | Usuwa wszystkie elementy z podanej kolekcji |
| `boolean retainAll(Collection<?> c)` | Zachowuje tylko elementy wspólne |
| `void clear()` | Usuwa wszystkie elementy |
| `Object[] toArray()` | Konwertuje do tablicy |
| `Iterator<E> iterator()` | Zwraca iterator |
| `Stream<E> stream()` | Zwraca strumień (Java 8+) |
| `Stream<E> parallelStream()` | Zwraca równoległy strumień (Java 8+) |
| `Spliterator<E> spliterator()` | Tworzy rozdzielacz do równoległego przetwarzania |
| `boolean removeIf(Predicate<E> filter)` | Usuwa elementy spełniające warunek (Java 8+) |

### Iterator i pętla for-each

```java
List<String> lista = new ArrayList<>(List.of("A", "B", "C"));

// Iterator – ręczny
Iterator<String> it = lista.iterator();
while (it.hasNext()) {
    String element = it.next();
    if (element.equals("B")) {
        it.remove(); // bezpieczne usuwanie podczas iteracji
    }
}

// for-each (używa Iterable pod spodem)
for (String s : lista) {
    System.out.println(s);
}

// forEach z lambdą (Java 8+)
lista.forEach(System.out::println);
```

> ⚠️ Nie usuwaj elementów kolekcji zwykłą pętlą `for-each` – rzuci `ConcurrentModificationException`.
Do usuwania podczas iteracji używaj `Iterator.remove()` lub `removeIf()`.
> 

```java
lista.removeIf(s -> s.startsWith("A")); // bezpieczne usuwanie (Java 8+)
```

---

## 3. Tworzenie kolekcji – fabryczne metody (Java 9+)

Od Javy 9 można tworzyć niemodyfikowalne kolekcje w jednej linii:

```java
List<String> lista     = List.of("a", "b", "c");
Set<Integer> zbior     = Set.of(1, 2, 3);
Map<String, Integer>   = Map.of("klucz", 1, "drugi", 2);
Map<String, Integer>   = Map.ofEntries(
    Map.entry("jeden", 1),
    Map.entry("dwa",   2)
);
```

> Kolekcje z `of()` są **niemodyfikowalne** – `add()`, `remove()` rzucą `UnsupportedOperationException`.
Nie przyjmują `null`.
> 

Dla modyfikowalnej kopii:

```java
List<String> modyfikowalna = new ArrayList<>(List.of("a", "b", "c"));
```

---

## 3a. Tablica vs `List` vs `List.of()`

| Cecha | Tablica `T[]` | `ArrayList` | `List.of()` |
| --- | --- | --- | --- |
| Rozmiar | Stały | Dynamiczny | Stały (niemutowalna) |
| Typy prymitywne | ✅ `int[]` | ❌ (tylko `Integer`) | ❌ |
| Modyfikacja elementów | ✅ | ✅ | ❌ |
| Dodawanie/usuwanie | ❌ | ✅ | ❌ |
| `null` | ✅ | ✅ | ❌ rzuca NPE |
| Metody (`contains` itp.) | ❌ | ✅ | ✅ |
| Wydajność | Najlepsza | Dobra | Dobra |
| Dostępna od | zawsze | Java 1.2 | Java 9 |

### Kiedy co wybrać?

```java
// Tablica – typy prymitywne, maksymalna wydajność
int[] liczby = {1, 2, 3};

// List.of() – małe, niemodyfikowalne zbiory danych, stałe konfiguracje
List<String> dni = List.of("Pon", "Wt", "Śr", "Czw", "Pt");

// ArrayList – gdy liczba elementów się zmienia
List<String> wyniki = new ArrayList<>();

// Modyfikowalna kopia z List.of()
List<String> kopia = new ArrayList<>(List.of("A", "B", "C"));
```

### Ograniczenia `List.of()`

- **Niemutowalna** – `add()`, `remove()`, `set()` rzucają `UnsupportedOperationException`
- **Brak `null`** – `List.of("a", null)` rzuca `NullPointerException`
- **Stały rozmiar** – nie można go zmienić po utworzeniu
- **Wygodna dla małych kolekcji** – przy dużych danych lepszy `new ArrayList<>(1000)`

---

## 4. Podinterfejsy i implementacje

### `List` – lista z zachowaną kolejnością, z duplikatami

| Implementacja | Cechy | Kiedy używać |
| --- | --- | --- |
| `ArrayList` | Tablica dynamiczna, szybki dostęp `get(i)` O(1) | Domyślny wybór, dużo odczytów |
| `LinkedList` | Lista dwukierunkowa, szybkie dodawanie/usuwanie na końcach O(1) | Dużo wstawiania/usuwania na początku/końcu |
| `Vector` | Jak `ArrayList`, ale synchronizowany | Przestarzały – używaj `ArrayList` |

### Tworzenie `ArrayList`

```java
// Pusta lista
List<String> lista = new ArrayList<>();

// Z początkową pojemnością (unikanie realokacji przy dużych danych)
List<String> duza = new ArrayList<>(1000);

// Z wartościami
List<String> lista2 = new ArrayList<>(List.of("A", "B", "C"));

// Wielowymiarowa lista
ArrayList<ArrayList<String>> multiDim = new ArrayList<>();
```

### `Arrays.asList()` vs `List.of()`

|  | `Arrays.asList()` | `List.of()` |
| --- | --- | --- |
| Modyfikowalność | ✅ `set()` działa | ❌ niemutowalna |
| Zmiana rozmiaru | ❌ `add()`/`remove()` rzuca wyjątek | ❌ `add()`/`remove()` rzuca wyjątek |
| `null` | ✅ akceptuje | ❌ rzuca `NullPointerException` |
| Dostępna od | Java 1.2 | Java 9 |

```java
// Arrays.asList – NIE jest rozmiarowalna, ale jest mutowalna
List<String> asList = Arrays.asList("Sunday", "Monday", "Tuesday");
asList.set(0, "Niedziela");  // ✅ można zmienić wartość
asList.add("Wednesday");     // ❌ UnsupportedOperationException

// List.of – niemutowalna
List<String> listOf = List.of("Sunday", "Monday", "Tuesday");
listOf.set(0, "Niedziela");  // ❌ UnsupportedOperationException

// Z tablicy
String[] days = {"Sunday", "Monday", "Tuesday"};
List<String> fromArray1 = Arrays.asList(days);  // widok na tablicę!
List<String> fromArray2 = List.of(days);         // kopia

// Modyfikowalna kopia z List.of
List<String> modyfikowalna = new ArrayList<>(List.of("A", "B", "C")); // ✅
```

> ⚠️ `Arrays.asList()` zwraca **widok** na oryginalną tablicę – zmiana tablicy wpływa na listę i odwrotnie.
> 

### Autorozrost `ArrayList`

`ArrayList` wewnętrznie przechowuje dane w tablicy. Gdy zabraknie miejsca, tworzy nową i kopiuje elementy:

- Domyślna pojemność: **10 elementów**
- Przy przepełnieniu rozrasta się o **50%** (nowa pojemność = stara × 1.5)

```java
ArrayList<Integer> list = new ArrayList<>(); // pojemność: 10
for (int i = 0; i < 11; i++) list.add(i);   // przy 11. elemencie: realokacja do 15
```

### Metody `ArrayList`

```java
List<String> lista = new ArrayList<>(List.of("A", "B", "C", "B"));

lista.add("D");                   // dodaj na koniec
lista.add(1, "X");               // dodaj na pozycję 1
lista.get(0);                    // "A" – O(1)
lista.set(0, "Z");               // zamień element na pozycji 0
lista.remove("B");               // usuń pierwsze wystąpienie "B"
lista.remove(0);                 // usuń element na pozycji 0
lista.indexOf("B");              // indeks pierwszego "B" (-1 jeśli brak)
lista.lastIndexOf("B");          // indeks ostatniego "B"
lista.contains("C");             // true
lista.size();                    // liczba elementów
lista.isEmpty();                 // false
lista.clear();                   // usuń wszystkie
lista.subList(0, 2);             // podlista [0,2) – widok, nie kopia!
lista.toArray(new String[0]);    // konwersja do String[]
```

### `for-each` – ważne ograniczenie

Pętla `for-each` zwraca **kopię referencji** do elementu, nie bezpośrednie odniesienie.
Dlatego **nie można przez nią modyfikować oryginalnej struktury**:

```java
String[] tablica = {"A", "B", "C"};

// for-each – tylko odczyt
for (String s : tablica) {
    s = "X";  // modyfikuje tylko lokalną kopię – tablica bez zmian!
}

// Standardowy for – modyfikacja w miejscu
for (int i = 0; i < tablica.length; i++) {
    tablica[i] = "X";  // ✅ modyfikuje oryginalną tablicę
}
```

### `ListIterator` – iteracja dwukierunkowa

`ListIterator` rozszerza `Iterator` o możliwość iteracji w obu kierunkach i modyfikacji listy:

```java
List<String> lista = new ArrayList<>(List.of("A", "B", "C", "D"));
ListIterator<String> it = lista.listIterator();

// Do przodu
while (it.hasNext()) {
    String el = it.next();
    if (el.equals("B")) {
        it.set("X");    // zamień bieżący element
        it.add("Y");    // dodaj po bieżącym
    }
}

// Do tyłu
while (it.hasPrevious()) {
    System.out.println(it.previous());
}
```

|  | `Iterator` | `ListIterator` |
| --- | --- | --- |
| Kierunek | → tylko do przodu | ← → obydwa |
| `set()` | ❌ | ✅ |
| `add()` | ❌ | ✅ |
| `nextIndex()` | ❌ | ✅ indeks następnego elementu (lub `size()` na końcu) |
| `previousIndex()` | ❌ | ✅ indeks poprzedniego elementu (lub `-1` na początku) |

### Wewnętrzna struktura `LinkedList`

`LinkedList` to **lista dwukierunkowa** – każdy węzeł zawiera referencję do poprzedniego i następnego:

```
null ← [prev|"A"|next] ↔ [prev|"B"|next] ↔ [prev|"C"|next] → null
        head                                   tail
```

Złożoność operacji LinkedList:

| Operacja | Złożoność | Uwaga |
| --- | --- | --- |
| `addFirst()` / `addLast()` | O(1) | Bezpośredni dostęp do head/tail |
| `removeFirst()` / `removeLast()` | O(1) | Bezpośredni dostęp do head/tail |
| `get(i)` | O(n) | Musi przejść przez węzły! |
| `add(i, e)` | O(n) | Szukanie pozycji |
| `contains()` | O(n) | Przeszukiwanie liniowe |

> ⚠️ **Praktyczna wskazówka:** `LinkedList` jest rzadko lepszym wyborem niż `ArrayList`.
Mimo O(1) na końcach, słaba lokalność pamięci (cache miss) sprawia że `ArrayList`
jest często szybsza nawet przy wstawianiu. Używaj `ArrayDeque` zamiast `LinkedList` jako stos/kolejkę.
> 

### Sortowanie list

```java
List<String> lista = new ArrayList<>(List.of("banana", "apple", "cherry"));

// Sortowanie naturalne
Collections.sort(lista);
lista.sort(null);                              // to samo

// Sortowanie z Comparator
lista.sort(Comparator.naturalOrder());
lista.sort(Comparator.reverseOrder());
lista.sort(Comparator.comparingInt(String::length));
lista.sort(Comparator.comparingInt(String::length)
           .thenComparing(Comparator.naturalOrder())); // wg długości, potem alfabetycznie

// Własne obiekty
osoby.sort(Comparator.comparing(Person::getName));
osoby.sort(Comparator.comparing(Person::getAge).reversed());
```

### `Set` – zbiór unikalnych elementów

| Implementacja | Kolejność | Cechy | Kiedy używać |
| --- | --- | --- | --- |
| `HashSet` | Brak | Najszybszy, O(1) add/remove/contains | Gdy kolejność nieważna |
| `LinkedHashSet` | Wstawiania | Jak HashSet + zachowuje kolejność | Gdy chcesz przewidywalną kolejność |
| `TreeSet` | Posortowana | O(log n), wymaga `Comparable` lub `Comparator` | Gdy potrzebujesz posortowanego zbioru |

```java
Set<String> hashSet   = new HashSet<>();
Set<String> linkedSet = new LinkedHashSet<>();
Set<String> treeSet   = new TreeSet<>();

hashSet.add("banana");
hashSet.add("apple");
hashSet.add("banana"); // ignorowane – duplikat
System.out.println(hashSet.size()); // 2

// TreeSet – dodatkowe metody SortedSet/NavigableSet
TreeSet<Integer> ts = new TreeSet<>(Set.of(1, 3, 5, 7, 9));
ts.first();          // 1  – najmniejszy
ts.last();           // 9  – największy
ts.floor(6);         // 5  – największy ≤ 6
ts.ceiling(6);       // 7  – najmniejszy ≥ 6
ts.headSet(5);       // [1, 3] – elementy < 5
ts.tailSet(5);       // [5, 7, 9] – elementy ≥ 5
ts.subSet(3, 7);     // [3, 5] – od 3 (włącznie) do 7 (wyłącznie)
```

### `Queue` – kolejka FIFO

| Implementacja | Cechy | Kiedy używać |
| --- | --- | --- |
| `PriorityQueue` | Priorytety (naturalny lub komparator), NIE FIFO | Gdy elementy mają priorytety |
| `ArrayDeque` | Kolejka dwustronna, szybka | Domyślna kolejka/stos |
| `LinkedList` | Implementuje też `Deque` | Rzadziej |

```java
// Queue – dwie wersje metod:
// - rzuca wyjątek gdy błąd: add(), remove(), element()
// - zwraca null/false gdy błąd: offer(), poll(), peek()

Queue<String> queue = new ArrayDeque<>();
queue.offer("pierwszy");
queue.offer("drugi");
queue.peek();  // "pierwszy" – podgląd bez usuwania
queue.poll();  // "pierwszy" – pobierz i usuń

// PriorityQueue
PriorityQueue<Integer> pq = new PriorityQueue<>();
pq.offer(5); pq.offer(1); pq.offer(3);
pq.poll(); // 1 – zawsze zwraca najmniejszy element
```

### `Deque` – kolejka dwustronna (FIFO i LIFO/stos)

```java
Deque<String> deque = new ArrayDeque<>();

// Jako kolejka (FIFO)
deque.offerLast("A");   // dodaj na koniec
deque.pollFirst();       // pobierz z początku

// Jako stos (LIFO)
deque.push("A");         // = addFirst()
deque.pop();             // = removeFirst()
deque.peek();            // = peekFirst()
```

> `ArrayDeque` jest szybszy niż `Stack` (przestarzały) i `LinkedList` jako stos/kolejka.
> 

### Wewnętrzny mechanizm `HashSet`

`HashSet` jest wewnętrznie oparty na `HashMap` – elementy są przechowywane jako **klucze**,
a wartością jest stały obiekt `PRESENT`:

```java
// Uproszczona implementacja wewnętrzna HashSet:
private transient HashMap<E, Object> map;
private static final Object PRESENT = new Object();

public boolean add(E e) {
    return map.put(e, PRESENT) == null; // klucz = element, wartość = PRESENT
}
```

Dzięki temu unikalność jest zapewniana przez mechanizm `HashMap`:

1. Obliczany jest `hashCode()` elementu → wyznaczany bucket
2. Jeśli bucket zajęty → sprawdzane `equals()`
3. Jeśli `equals()` = `true` → element już istnieje, nie zostanie dodany

> Poprawne działanie `HashSet` wymaga poprawnej implementacji `hashCode()` i `equals()`
w przechowywanych obiektach → szczegóły w `java_hashcode.md`
> 

### Konstruktory `HashSet` i `LinkedHashSet`

```java
// HashSet
new HashSet<>()                          // domyślna pojemność 16, load factor 0.75
new HashSet<>(int initialCapacity)       // własna pojemność
new HashSet<>(int capacity, float load)  // własna pojemność i load factor
new HashSet<>(Collection<E> c)           // z istniejącej kolekcji

// LinkedHashSet
new LinkedHashSet<>()
new LinkedHashSet<>(int initialCapacity)
new LinkedHashSet<>(int capacity, float loadFactor)
new LinkedHashSet<>(Collection<E> c)    // zachowuje kolejność wstawiania z kolekcji
```

**Load factor (współczynnik obciążenia):** gdy zajętość przekroczy `pojemność × loadFactor`,
tablica jest powiększana (rehashing). Domyślny `0.75` to kompromis między pamięcią a wydajnością.

### `TreeSet` – dodatkowe metody nawigacyjne

```java
TreeSet<Integer> ts = new TreeSet<>(Set.of(1, 3, 5, 7, 9));

// Iteracja w odwrotnej kolejności
ts.descendingSet();                    // [9, 7, 5, 3, 1] – widok w odwrotnej kolejności
Iterator<Integer> desc = ts.descendingIterator(); // iterator od największego

// pollFirst / pollLast – pobierz i usuń
ts.pollFirst();  // 1 – usuwa i zwraca najmniejszy
ts.pollLast();   // 9 – usuwa i zwraca największy

// comparator() – zwraca null jeśli naturalny porządek
TreeSet<String> custom = new TreeSet<>(Comparator.reverseOrder());
custom.comparator(); // Comparator.reverseOrder()
```

### `Map` – mapa klucz-wartość

| Implementacja | Kolejność kluczy | Cechy | Kiedy używać |
| --- | --- | --- | --- |
| `HashMap` | Brak | Najszybszy, O(1) | Domyślny wybór |
| `LinkedHashMap` | Wstawiania | Jak HashMap + kolejność | Przewidywalna kolejność |
| `TreeMap` | Posortowana | O(log n) | Posortowane klucze |

```java
Map<String, Integer> map = new HashMap<>();
map.put("jeden", 1);
map.put("dwa", 2);
map.get("jeden");              // 1
map.getOrDefault("trzy", 0);  // 0 – gdy klucz nie istnieje
map.containsKey("dwa");        // true
map.containsValue(2);          // true
map.remove("jeden");
map.size();                    // 1

// Iterowanie po mapie
for (Map.Entry<String, Integer> entry : map.entrySet()) {
    System.out.println(entry.getKey() + " = " + entry.getValue());
}
map.forEach((k, v) -> System.out.println(k + " = " + v));

// Przydatne metody (Java 8+)
map.putIfAbsent("trzy", 3);               // dodaje tylko gdy klucz nie istnieje
map.computeIfAbsent("cztery", k -> 4);    // oblicza wartość gdy brak klucza
map.merge("jeden", 1, Integer::sum);      // dodaje lub łączy wartości
map.replaceAll((k, v) -> v * 2);          // modyfikuje wszystkie wartości

// TreeMap – dodatkowe metody
TreeMap<String, Integer> tm = new TreeMap<>(map);
tm.firstKey();               // najmniejszy klucz
tm.lastKey();                // największy klucz
tm.headMap("p");             // klucze < "p"
tm.tailMap("p");             // klucze >= "p"
```

---

## 4a. `NavigableMap` – dodatkowe metody `TreeMap`

`TreeMap` implementuje `NavigableMap`, który rozszerza `SortedMap` o metody wyszukiwania najbliższych kluczy:

| Metoda | Opis |
| --- | --- |
| `firstEntry()` | Pierwszy wpis (najmniejszy klucz) lub `null` |
| `lastEntry()` | Ostatni wpis (największy klucz) lub `null` |
| `floorKey(K key)` | Największy klucz ≤ podanego lub `null` |
| `floorEntry(K key)` | Wpis z największym kluczem ≤ podanego |
| `ceilingKey(K key)` | Najmniejszy klucz ≥ podanego lub `null` |
| `ceilingEntry(K key)` | Wpis z najmniejszym kluczem ≥ podanego |
| `lowerKey(K key)` | Największy klucz < podanego lub `null` |
| `higherKey(K key)` | Najmniejszy klucz > podanego lub `null` |
| `pollFirstEntry()` | Usuwa i zwraca pierwszy wpis |
| `pollLastEntry()` | Usuwa i zwraca ostatni wpis |
| `descendingMap()` | Widok mapy w odwrotnej kolejności kluczy |
| `descendingKeySet()` | Zbiór kluczy w odwrotnej kolejności |
| `subMap(K from, boolean fromIncl, K to, boolean toIncl)` | Podmapa w zakresie z kontrolą inkluzywności |
| `headMap(K toKey, boolean inclusive)` | Podmapa kluczy < toKey (lub ≤ jeśli inclusive) |
| `tailMap(K fromKey, boolean inclusive)` | Podmapa kluczy ≥ fromKey (lub > jeśli !inclusive) |

```java
NavigableMap<Integer, String> map = new TreeMap<>();
map.put(1, "Jeden"); map.put(3, "Trzy");
map.put(5, "Pięć"); map.put(7, "Siedem");

map.ceilingKey(4);    // 5  – najmniejszy klucz >= 4
map.floorKey(4);      // 3  – największy klucz <= 4
map.higherKey(5);     // 7  – klucz > 5
map.lowerKey(5);      // 3  – klucz < 5
map.firstEntry();     // 1=Jeden
map.lastEntry();      // 7=Siedem
map.pollFirstEntry(); // usuwa i zwraca 1=Jeden

// Podmapa z kontrolą inkluzywności
map.subMap(3, true, 7, false); // {3=Trzy, 5=Pięć} – od 3 włącznie, do 7 wyłącznie
map.headMap(5, true);          // {1=Jeden, 3=Trzy, 5=Pięć}
map.tailMap(5, false);         // {7=Siedem}

// Widok w odwrotnej kolejności
NavigableMap<Integer, String> desc = map.descendingMap();
System.out.println(desc); // {7=Siedem, 5=Pięć, 3=Trzy}
```

---

## 5. Klasy narzędziowe – `Arrays` vs `Collections`

W Javie istnieją dwie główne klasy narzędziowe do pracy ze strukturami danych:

|  | `Arrays` | `Collections` |
| --- | --- | --- |
| Pakiet | `java.util` | `java.util` |
| Działa na | Tablicach (`int[]`, `String[]` itp.) | Kolekcjach (`List`, `Set`, `Queue`) |
| `sort()` | ✅ | ✅ |
| `binarySearch()` | ✅ | ✅ |
| `toString()` / `deepToString()` | ✅ | ❌ |
| `asList()` | ✅ | ❌ |
| `fill()` | ✅ (tablica) | ✅ (lista) |
| `copyOf()` / `copyOfRange()` | ✅ | ❌ |
| `reverse()` | ❌ | ✅ |
| `shuffle()` | ❌ | ✅ |
| `min()` / `max()` | ❌ | ✅ |
| `frequency()` | ❌ | ✅ |
| `unmodifiable*()` | ❌ | ✅ |
| `synchronized*()` | ❌ | ✅ |

```java
// Arrays – dla tablic
int[] tablica = {5, 3, 8, 1};
Arrays.sort(tablica);
System.out.println(Arrays.toString(tablica)); // [1, 3, 5, 8]
int idx = Arrays.binarySearch(tablica, 5);    // 2

// Collections – dla kolekcji
List<String> lista = new ArrayList<>(List.of("Alice", "Charlie", "Bob"));
Collections.sort(lista);                      // [Alice, Bob, Charlie]
Collections.reverse(lista);                   // [Charlie, Bob, Alice]
Collections.shuffle(lista);                   // losowa kolejność
System.out.println(Collections.min(lista));   // najmniejszy element
System.out.println(Collections.max(lista));   // największy element
```

---

## 5a. Klasa narzędziowa `Collections` – szczegóły

`java.util.Collections` (z dużej litery!) to klasa ze statycznymi metodami narzędziowymi.
Nie mylić z interfejsem `Collection`!

### Sortowanie i kolejność

| Metoda | Opis |
| --- | --- |
| `sort(List<T> list)` | Sortuje w porządku naturalnym |
| `sort(List<T> list, Comparator<T> c)` | Sortuje wg komparatora |
| `reverse(List<?> list)` | Odwraca kolejność |
| `shuffle(List<?> list)` | Losowo przestawia elementy |
| `rotate(List<?> list, int distance)` | Obraca elementy o n pozycji |
| `swap(List<?> list, int i, int j)` | Zamienia dwa elementy miejscami |

### Wyszukiwanie i statystyki

| Metoda | Opis |
| --- | --- |
| `binarySearch(List<T> list, T key)` | Wyszukiwanie binarne (lista musi być posortowana) |
| `min(Collection<T> coll)` | Najmniejszy element |
| `max(Collection<T> coll)` | Największy element |
| `frequency(Collection<?> coll, Object o)` | Liczba wystąpień elementu |
| `disjoint(Collection<?> c1, Collection<?> c2)` | `true` jeśli kolekcje nie mają części wspólnych |

### Kopiowanie i wypełnianie

| Metoda | Opis |
| --- | --- |
| `copy(List<T> dest, List<T> src)` | Kopiuje src do dest (dest musi być ≥ src) – wynik **modyfikowalny** |
| `fill(List<T> list, T obj)` | Wypełnia listę jedną wartością |
| `nCopies(int n, T obj)` | Tworzy niemodyfikowalną listę n kopii elementu |
| `replaceAll(List<T> list, T old, T new)` | Zastępuje wszystkie wystąpienia |

### Niemodyfikowalne i synchronizowane widoki

| Metoda | Opis |
| --- | --- |
| `unmodifiableList(List<T> list)` | Niemodyfikowalny widok listy |
| `unmodifiableSet(Set<T> set)` | Niemodyfikowalny widok zbioru |
| `unmodifiableMap(Map<K,V> map)` | Niemodyfikowalny widok mapy |
| `synchronizedList(List<T> list)` | Wątkobezpieczny widok listy |
| `synchronizedSet(Set<T> set)` | Wątkobezpieczny widok zbioru |
| `synchronizedMap(Map<K,V> map)` | Wątkobezpieczny widok mapy |
| `emptyList()` / `emptySet()` / `emptyMap()` | Puste niemodyfikowalne kolekcje |
| `singletonList(T obj)` | Lista z jednym elementem |

> **`Collections.copy` vs `Collections.copyOf` (Java 10+):**
> 
> - `Collections.copy(dest, src)` – **modyfikowalny** wynik, dest musi być odpowiednio duży
> - `Collections.copyOf(coll)` – **niemodyfikowalna** kopia

```java
List<Integer> lista = new ArrayList<>(List.of(3, 1, 4, 1, 5, 9));

Collections.sort(lista);                    // [1, 1, 3, 4, 5, 9]
Collections.reverse(lista);                 // [9, 5, 4, 3, 1, 1]
Collections.shuffle(lista);                 // losowa kolejność
Collections.frequency(lista, 1);            // 2
Collections.min(lista);                     // 1
Collections.max(lista);                     // 9
Collections.binarySearch(lista, 4);         // indeks 4 (lista musi być posortowana!)

List<Integer> unmod = Collections.unmodifiableList(lista);
unmod.add(1); // ❌ UnsupportedOperationException
```

---

## 6. Typy generyczne w kolekcjach

Kolekcje bez generyki (raw type) są możliwe ale niebezpieczne – kompilator nie sprawdza typów:

```java
List raw = new ArrayList();   // raw type – unikać!
raw.add("tekst");
raw.add(42);                  // kompilator nie protestuje
String s = (String) raw.get(1); // ClassCastException w runtime!

List<String> generic = new ArrayList<>(); // generyk – bezpieczne
generic.add("tekst");
generic.add(42);              // ❌ błąd kompilacji – wykryty wcześniej
```

### Wildcards (symbole wieloznaczne)

```java
// <? extends T> – tylko odczyt (producent)
// "cokolwiek co jest T lub podklasą T"
void drukuj(List<? extends Number> lista) {
    for (Number n : lista) System.out.println(n);
}
drukuj(new ArrayList<Integer>());  // ✅
drukuj(new ArrayList<Double>());   // ✅

// <? super T> – zapis (konsument)
// "cokolwiek co jest T lub nadklasą T"
void dodajLiczby(List<? super Integer> lista) {
    lista.add(1);
    lista.add(2);
}
dodajLiczby(new ArrayList<Integer>());  // ✅
dodajLiczby(new ArrayList<Number>());   // ✅
dodajLiczby(new ArrayList<Object>());   // ✅
```

> Zasada **PECS** (Producer Extends, Consumer Super):
> 
> - czytasz z kolekcji → `<? extends T>`
> - piszesz do kolekcji → `<? super T>`

---

## 7. Tabela porównawcza – kiedy co wybrać?

| Potrzeba | Wybierz |
| --- | --- |
| Lista z szybkim dostępem po indeksie | `ArrayList` |
| Lista z częstym wstawianiem/usuwaniem na początku | `LinkedList` |
| Unikalność, kolejność nieważna | `HashSet` |
| Unikalność, zachowana kolejność wstawiania | `LinkedHashSet` |
| Unikalność, posortowane elementy | `TreeSet` |
| Stos (LIFO) | `ArrayDeque` |
| Kolejka (FIFO) | `ArrayDeque` |
| Kolejka z priorytetami | `PriorityQueue` |
| Mapa, kolejność nieważna | `HashMap` |
| Mapa, zachowana kolejność wstawiania | `LinkedHashMap` |
| Mapa, posortowane klucze | `TreeMap` |
| Niemodyfikowalna kolekcja | `List.of()`, `Set.of()`, `Map.of()` |
| Bezpieczna wielowątkowo | `ConcurrentHashMap`, `CopyOnWriteArrayList` |
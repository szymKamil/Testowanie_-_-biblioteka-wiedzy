# Różnice między kolekcjami

# Struktury danych w Javie – porównanie

---

## 1. Złożoność czasowa – tabela zbiorcza

| Struktura | get(i) | add (koniec) | add (początek/środek) | remove | contains | Kolejność |
| --- | --- | --- | --- | --- | --- | --- |
| `Array` | O(1) | ❌ (stały rozmiar) | ❌ | ❌ | O(n) | indeks |
| `ArrayList` | O(1) | O(1)* | O(n) | O(n) | O(n) | wstawiania |
| `LinkedList` | O(n) | O(1) | O(1) | O(1)** | O(n) | wstawiania |
| `HashSet` | ❌ | O(1) | O(1) | O(1) | O(1) | brak |
| `LinkedHashSet` | ❌ | O(1) | O(1) | O(1) | O(1) | wstawiania |
| `TreeSet` | ❌ | O(log n) | O(log n) | O(log n) | O(log n) | posortowana |
| `HashMap` | ❌ | O(1) | O(1) | O(1) | O(1) klucza | brak |
| `LinkedHashMap` | ❌ | O(1) | O(1) | O(1) | O(1) klucza | wstawiania |
| `TreeMap` | ❌ | O(log n) | O(log n) | O(log n) | O(log n) klucza | posortowana |
| `ArrayDeque` | ❌ | O(1) | O(1) | O(1) | O(n) | FIFO/LIFO |
| `PriorityQueue` | ❌ | O(log n) | O(log n) | O(log n) | O(n) | priorytet |

> * amortyzowane O(1) – sporadycznie O(n) przy rozroście wewnętrznej tablicy
** O(1) gdy mamy referencję do węzła, O(n) gdy szukamy po wartości
> 

---

## 2. Tablice (`Array`)

Statyczna struktura danych – rozmiar ustalany raz przy tworzeniu.

```java
int[] tablica = new int[5];       // domyślne wartości: 0
String[] teksty = new String[3];  // domyślne wartości: null
int[] z_wartosciami = {1, 2, 3};  // inicjalizacja z wartościami
```

**Kiedy używać:** gdy liczba elementów jest z góry znana i nie zmienia się, gdy liczy się maksymalna wydajność dostępu.

| Zalety | Wady |
| --- | --- |
| Najszybszy dostęp `get(i)` – O(1) | Rozmiar niezmienny |
| Minimalne zużycie pamięci | Brak metod zarządzania (add/remove) |
| Typy prymitywne bez autoboxingu | Tylko jeden typ elementów |

---

## 3. `ArrayList`

Dynamiczna lista oparta na tablicy. Automatycznie rozrasta się gdy brakuje miejsca (domyślnie 2x).

```java
List<String> lista = new ArrayList<>();
lista.add("A");
lista.add(0, "B");          // O(n) – przesuwa elementy
lista.get(1);               // O(1) – szybki dostęp
lista.remove("A");          // O(n) – szuka i przesuwa
lista.remove(0);            // O(n) – przesuwa elementy

// Tworzenie z początkową pojemnością (unikanie realokacji)
List<String> duza = new ArrayList<>(1000);
```

**Kiedy używać:** domyślny wybór dla list, dużo odczytów po indeksie, dodawanie głównie na końcu.

| Zalety | Wady |
| --- | --- |
| Szybki dostęp O(1) | Wolne wstawianie/usuwanie w środku O(n) |
| Dynamiczny rozmiar | Autoboxing dla typów prymitywnych |
| Bogaty zestaw metod | Realokacja pamięci przy rozroście |

---

## 4. `LinkedList`

Lista dwukierunkowa – każdy węzeł zawiera referencję do poprzedniego i następnego elementu.
Implementuje zarówno `List` jak i `Deque`.

```java
LinkedList<String> ll = new LinkedList<>();
ll.add("B");
ll.addFirst("A");    // O(1) – na początek
ll.addLast("C");     // O(1) – na koniec
ll.get(1);           // O(n) – musi przejść przez węzły!
ll.removeFirst();    // O(1)
ll.removeLast();     // O(1)
```

**Kiedy używać:** częste wstawianie/usuwanie na początku lub końcu, implementacja stosu/kolejki.

| Zalety | Wady |
| --- | --- |
| Szybkie add/remove na końcach O(1) | Wolny dostęp po indeksie O(n) |
| Brak realokacji pamięci | Większe zużycie pamięci (referencje) |
| Implementuje Deque | Słaba lokalność pamięci (cache miss) |

---

## 5. `Set` – zbiory unikalnych elementów

### `HashSet`

```java
Set<String> hashSet = new HashSet<>();
hashSet.add("banana");
hashSet.add("apple");
hashSet.add("banana"); // ignorowane – duplikat
System.out.println(hashSet.contains("apple")); // true – O(1)
```

### `LinkedHashSet`

```java
Set<String> linked = new LinkedHashSet<>();
linked.add("C"); linked.add("A"); linked.add("B");
System.out.println(linked); // [C, A, B] – zachowuje kolejność wstawiania
```

### `TreeSet`

Wymaga aby elementy implementowały `Comparable` lub dostarczenia `Comparator`.

```java
Set<Integer> tree = new TreeSet<>();
tree.add(5); tree.add(1); tree.add(3);
System.out.println(tree); // [1, 3, 5] – zawsze posortowane

// Dodatkowe metody NavigableSet
TreeSet<Integer> ts = new TreeSet<>(tree);
ts.first();        // 1 – najmniejszy
ts.last();         // 5 – największy
ts.floor(4);       // 3 – największy ≤ 4
ts.ceiling(4);     // 5 – najmniejszy ≥ 4
ts.headSet(3);     // [1] – elementy < 3
ts.tailSet(3);     // [3, 5] – elementy ≥ 3
```

|  | `HashSet` | `LinkedHashSet` | `TreeSet` |
| --- | --- | --- | --- |
| Kolejność | Brak | Wstawiania | Posortowana |
| Wydajność | O(1) | O(1) | O(log n) |
| `null` | ✅ (jeden) | ✅ (jeden) | ❌ |
| Wymaga `Comparable` | ❌ | ❌ | ✅ |

---

## 6. `Map` – mapa klucz-wartość

### `HashMap`

```java
Map<String, Integer> map = new HashMap<>();
map.put("jeden", 1);
map.put("dwa", 2);
map.get("jeden");                  // 1 – O(1)
map.getOrDefault("trzy", 0);      // 0 – gdy brak klucza
map.putIfAbsent("jeden", 99);     // ignoruje – klucz już istnieje
map.containsKey("dwa");           // true
map.remove("jeden");
```

### `LinkedHashMap`

```java
Map<String, Integer> linked = new LinkedHashMap<>();
linked.put("C", 3); linked.put("A", 1); linked.put("B", 2);
System.out.println(linked.keySet()); // [C, A, B] – kolejność wstawiania
```

### `TreeMap`

```java
Map<String, Integer> tree = new TreeMap<>();
tree.put("banana", 2); tree.put("apple", 1); tree.put("cherry", 3);
System.out.println(tree.keySet()); // [apple, banana, cherry] – posortowane

TreeMap<String, Integer> tm = new TreeMap<>(tree);
tm.firstKey();       // "apple"
tm.lastKey();        // "cherry"
tm.headMap("b");     // {apple=1} – klucze < "b"
tm.tailMap("b");     // {banana=2, cherry=3} – klucze >= "b"
```

|  | `HashMap` | `LinkedHashMap` | `TreeMap` |
| --- | --- | --- | --- |
| Kolejność kluczy | Brak | Wstawiania | Posortowana |
| Wydajność | O(1) | O(1) | O(log n) |
| `null` klucz | ✅ (jeden) | ✅ (jeden) | ❌ |
| Wymaga `Comparable` | ❌ | ❌ | ✅ |

---

## 7. `Stack` (przestarzały) i `ArrayDeque`

> ⚠️ Klasa `Stack` jest **przestarzała** – zalecany zamiennik to `ArrayDeque`,
która jest szybsza i nie ma narzutu synchronizacji.
> 

```java
// Stack – przestarzały, unikać
Stack<Integer> stack = new Stack<>();
stack.push(1);
stack.pop();
stack.peek();

// ArrayDeque jako stos (LIFO) – zalecany
Deque<Integer> stos = new ArrayDeque<>();
stos.push(1);      // = addFirst()
stos.pop();        // = removeFirst()
stos.peek();       // = peekFirst()
```

---

## 8. `Queue` i `Deque`

### `Queue` – kolejka FIFO

```java
// Dwie wersje metod:
// rzuca wyjątek gdy błąd: add(), remove(), element()
// zwraca null/false gdy błąd: offer(), poll(), peek()

Queue<String> kolejka = new ArrayDeque<>();
kolejka.offer("pierwszy");
kolejka.offer("drugi");
kolejka.peek();   // "pierwszy" – podgląd bez usuwania
kolejka.poll();   // "pierwszy" – pobierz i usuń
```

### `PriorityQueue` – kolejka priorytetowa

```java
// Domyślnie – min-heap (najmniejszy element zawsze pierwszy)
PriorityQueue<Integer> pq = new PriorityQueue<>();
pq.offer(5); pq.offer(1); pq.offer(3);
pq.poll(); // 1 – zawsze najmniejszy
pq.poll(); // 3
pq.poll(); // 5

// Max-heap – własny komparator
PriorityQueue<Integer> maxPq = new PriorityQueue<>(Comparator.reverseOrder());
maxPq.offer(5); maxPq.offer(1); maxPq.offer(3);
maxPq.poll(); // 5 – największy

// Własne obiekty – wymaga Comparable lub Comparator
PriorityQueue<String> strPq = new PriorityQueue<>(Comparator.comparingInt(String::length));
strPq.offer("dlugie"); strPq.offer("kr"); strPq.offer("abc");
strPq.poll(); // "kr" – najkrótszy
```

### `Deque` – kolejka dwustronna

```java
Deque<String> deque = new ArrayDeque<>();

// Jako kolejka (FIFO)
deque.offerLast("A");   // dodaj na koniec
deque.offerLast("B");
deque.pollFirst();       // "A" – pobierz z początku

// Jako stos (LIFO)
deque.push("X");         // addFirst
deque.push("Y");
deque.pop();             // "Y" – pobierz z początku
```

|  | `ArrayDeque` | `LinkedList` |
| --- | --- | --- |
| Wydajność | Lepsza | Niższa |
| `null` | ❌ nie obsługuje | ✅ obsługuje |
| Pamięć | Mniejsze zużycie | Większe (referencje) |
| Implementuje `List` | ❌ | ✅ |

---

## 9. Wielowątkowość

Standardowe kolekcje **nie są bezpieczne wielowątkowo**. Opcje:

```java
// 1. Collections.synchronized* – wolne, blokują całą kolekcję
List<String> synchList = Collections.synchronizedList(new ArrayList<>());
Map<String, Integer> synchMap = Collections.synchronizedMap(new HashMap<>());

// 2. Kolekcje concurrent – zalecane, blokują tylko fragment
Map<String, Integer> concMap = new ConcurrentHashMap<>();
List<String> cowList = new CopyOnWriteArrayList<>();  // dobre do częstego odczytu
Queue<String> concQueue = new ConcurrentLinkedQueue<>();
```

---

## 10. Tabela porównawcza – kiedy co wybrać?

| Potrzeba | Wybierz | Dlaczego |
| --- | --- | --- |
| Lista, dużo odczytów | `ArrayList` | O(1) get |
| Lista, dużo add/remove na końcach | `ArrayDeque` / `LinkedList` | O(1) na końcach |
| Unikalne elementy, szybkość | `HashSet` | O(1) add/contains |
| Unikalne elementy, posortowane | `TreeSet` | automatyczne sortowanie |
| Unikalne elementy, kolejność wstawiania | `LinkedHashSet` | zachowuje kolejność |
| Mapa, szybkość | `HashMap` | O(1) |
| Mapa, posortowane klucze | `TreeMap` | O(log n) |
| Mapa, kolejność wstawiania | `LinkedHashMap` | zachowuje kolejność |
| Stos (LIFO) | `ArrayDeque` | szybszy niż `Stack` |
| Kolejka (FIFO) | `ArrayDeque` | szybszy niż `LinkedList` |
| Kolejka z priorytetami | `PriorityQueue` | automatyczny heap |
| Niemodyfikowalna kolekcja | `List.of()` / `Set.of()` | zero overhead |
| Wielowątkowość | `ConcurrentHashMap` | bezpieczny, szybki |
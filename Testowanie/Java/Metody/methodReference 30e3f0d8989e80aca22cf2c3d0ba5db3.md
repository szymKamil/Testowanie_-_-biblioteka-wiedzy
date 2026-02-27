# methodReference

# Method Reference

Method reference to skrócony sposób odwołania się do metod lub konstruktorów w Javie. Wprowadzone w Java 8 wraz z wyrażeniami lambda, pozwalają na czytelniejsze i bardziej zwięzłe kodowanie, gdy metoda ma być wywołana w kontekście funkcjonalnym.

Kiedy używać:

Gdy lambda tylko wywołuje metodę – method reference jest krótsze i bardziej czytelne.
W strumieniach (Stream), kolekcjach, mapowaniach, filtrach.
Przy konstruktorach, sortowaniu lub wywoływaniu metod w pętlach.
Gdy sygnatura metody pasuje do interfejsu funkcyjnego.

| **Rodzaj Method Reference** | **Składnia** | **Opis** | **Przykład (Skrócony)** |
| --- | --- | --- | --- |
| **Metoda statyczna** | `ClassName::staticMethodName` | Odwołanie do metody statycznej klasy. | `Function<String, Integer> parse = Integer::parseInt;` |
| **Metoda instancyjna obiektu** | `object::instanceMethodName` | Odwołanie do metody instancyjnej na istniejącym obiekcie. | `Consumer<String> printer = System.out::println;` |
| **Metoda instancyjna klasy** | `ClassName::instanceMethodName` | Odwołanie do metody instancyjnej wywołanej na obiekcie parametru. | `BiFunction<String, String, Boolean> equalsIgnoreCase = String::equalsIgnoreCase;` |
| **Konstruktor** | `ClassName::new` | Odwołanie do konstruktora klasy, tworzy nowe instancje. | `Supplier<String> newString = String::new;` |

Przykłady:
Metoda statyczna:

```java
Function<String, Integer> parse = Integer::parseInt;
System.out.println(parse.apply("123")); // Output: 123
```

Metoda instancyjna obiektu:

```java
Consumer<String> printer = System.out::println;
printer.accept("Hello!"); // Output: Hello!
```

Metoda instancyjna klasy:

```java
BiFunction<String, String, Boolean> equalsIgnoreCase = String::equalsIgnoreCase;
System.out.println(equalsIgnoreCase.apply("java", "JAVA")); // Output: true
```

Konstruktor:

```java
Supplier<String> newString = String::new;
System.out.println(newString.get()); // Output: (pusty String)
```

Istnieją dwa sposoby wywołania metody instancji. Pierwszy z nich polega na odwołaniu się do metody za pomocą instancji pochodzącej z otaczającego kodu. Ta instancja jest deklarowana poza odwołaniem do metody. Przykładem tego jest odwołanie do metody System.out:println. Na niektórych stronach internetowych taką instancję określa się mianem Bounded Receiver i faktycznie podoba mi się ta terminologia jako wyróżnik.

Bounded Receiver to instancja pochodząca z otaczającego kodu, używana w wyrażeniu lambda, na której metoda zostanie wywołana.

Drugi sposób wywołania metody instancji jest źródłem pewnego zamieszania.
Instancja używana do wywołania metody będzie pierwszym argumentem przekazywanym do wyrażenia lambda lub odniesienia do metody podczas jej wykonania. To jest znane w niektórych źródłach jako Unbounded Receiver. Instancja ta zostaje dynamicznie związana z pierwszym argumentem przekazywanym do wyrażenia lambda podczas wywołania metody. Niestety, wygląda to bardzo podobnie do odwołania do metody statycznej z użyciem typu referencyjnego.

Oznacza to, że istnieją dwa odwołania do metod, które wyglądają podobnie, ale mają zupełnie inne znaczenia.
Pierwsze faktycznie wywołuje metodę statyczną, używając typu referencyjnego. Widziałeś to wcześniej, gdy używałem metody sum na klasie opakowującej Integer.
Integer::sum
To jest odniesienie do typu (Type Reference), w którym Integer jest typem, a metoda wywoływana jest jako statyczna. To jest łatwe do zrozumienia.

Ale istnieje inne odniesienie, które zobaczysz, gdy zacznę pracować z metodami odwołującymi się do instancji klasy String.
Oto przykład odniesienia do metody concat klasy String:
String::concat
Mam nadzieję, że wiesz już, że metoda concat nie jest metodą statyczną w klasie String. To jest metoda instancji, która jest wywoływana na konkretnym obiekcie String.

Więc dlaczego to odniesienie do metody jest w ogóle poprawne?
Nigdy nie mógłbyś wywołać concat bezpośrednio z klasy String, ponieważ wymaga ona wywołania na konkretnej instancji.

Pamiętaj, że Unbounded Receiver oznacza, iż pierwszy argument staje się instancją, na której metoda zostaje wywołana.
String::concat
Każde odniesienie do metody, które używa String::concat, musi być użyte w kontekście funkcjonalnego interfejsu z dwoma parametrami. Pierwszym parametrem jest instancja klasy String, na której zostanie wywołana metoda concat, a drugim argumentem jest String, który zostanie przekazany do tej metody.

Różnica między Bounded Receiver a Unbounded Receiver w Javie odnosi się do tego, skąd pochodzi instancja obiektu, na której wywoływana jest metoda referencyjna w przypadku referencji metod.

1. Bounded Receiver
Instancja obiektu jest ustalona z góry (związana) i pochodzi z otaczającego kontekstu.
Metoda referencyjna jest wywoływana na tej konkretnej instancji.
Przykład składni:

```java
containingObject::instanceMethodName
String prefix = "Hello, ";
Consumer<String> bounded = prefix::concat;

bounded.accept("World!"); // Wywołuje prefix.concat("World!")
```

Tutaj obiektem (prefix) jest ustalona instancja (tzw. “związany odbiorca”).
Działanie: Referencja prefix::concat zawsze wywoła metodę concat na obiekcie prefix.

1. Unbounded Receiver
Instancja obiektu nie jest ustalona z góry.
Obiekt, na którym wywoływana jest metoda, jest przekazywany dynamicznie jako pierwszy argument do wyrażenia lambda/metody.
Przykład składni

```java
ContainingType::instanceMethodName
BiFunction<String, String, String> unbounded = String::concat;

String result = unbounded.apply("Hello, ", "World!"); // Wywołuje "Hello, ".concat("World!")
```

Tutaj instancja (“Hello,”) jest przekazywana dynamicznie jako pierwszy argument do metody referencyjnej String::concat.

| **Cechy** | **Bounded Receiver** | **Unbounded Receiver** |
| --- | --- | --- |
| **Skąd pochodzi obiekt?** | Jest ustalony wcześniej (związany). | Przekazywany dynamicznie jako argument. |
| **Typ referencji** | `containingObject::instanceMethodName` | `ContainingType::instanceMethodName` |
| **Przykład** | `String str = "Test"; str::toUpperCase` | `String::toUpperCase` |
| **Jak działa?** | Metoda wywoływana na określonym obiekcie. | Metoda wywoływana na dynamicznie przekazanym obiekcie. |
| **Przydatność** | Gdy instancja jest już dostępna w czasie kompilacji. | Gdy instancja jest przekazywana w trakcie wykonania. |

| Type | Syntax | Method Reference Example | Corresponding Lambda Expression |
| --- | --- | --- | --- |
| static method | `ClassName::staticMethodName(p1, p2, ... pn)` | `Integer::sum` | `(p1, p2) -> p1 + p2` |
| instance method of a particular (Bounded) object | `ContainingObject::instanceMethodName(p1, p2,...pn)` | `System.out::println` | `p1 -> System.out.println(p1)` |
| instance method of an arbitrary (Unbounded) object (as determined by p1) | `ContainingType[=p1]::instanceMethodName(p2, ... pn)` | `String::concat` | `(p1, p2) -> p1.concat(p2)` |
| constructor | `ClassName::new` | `LPAStudent::new` | `() -> new LPAStudent()` |

| **Types of Method References** | **No Args (Supplier)** | **Predicate** | **Consumer** | **Function / UnaryOperator** | **BiPredicate** | **BiConsumer** | **BiFunction / BinaryOperator** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **Reference Type (Static)** | n/a | n/a | n/a | n/a | n/a | n/a | `Integer::sum` |
| **Reference Type (Constructor)** | `Employee::new` | n/a | n/a | `Employee::new` | n/a | n/a | `Employee::new` |
| **Bounded Receiver (Instance)** | n/a | n/a | `System.out::println` | n/a | n/a | `System.out::printf` | `new Random()::nextInt` |
| **Unbounded Receiver (Instance)** | n/a | `String::isEmpty` | `List::clear` | `String::length` | `String::equals` | `List::add` | `String::concat`, `String::split` |
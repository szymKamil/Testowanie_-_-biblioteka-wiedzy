# abstractionVSinterface

# Klasa Abstrakcyjna a Interfejs w Javie

## Klasa Abstrakcyjna

- Klasy abstrakcyjne są podobne do interfejsów – nie można ich instancjonować.
- Mogą zawierać mieszankę metod zdefiniowanych z blokiem kodu lub bez niego.
- Klasy abstrakcyjne pozwalają na deklarację pól, które nie muszą być statyczne i finalne (np. pola instancji).
- Można używać dowolnych modyfikatorów dostępu dla metod konkretnych oraz wszystkich poza prywatnym dla metod abstrakcyjnych.
- Klasa abstrakcyjna może dziedziczyć tylko po jednej klasie nadrzędnej, ale może implementować wiele interfejsów.
- Jeśli klasa dziedzicząca po klasie abstrakcyjnej nie implementuje wszystkich jej metod abstrakcyjnych, musi również zostać zadeklarowana jako abstrakcyjna.

### Użycie klasy abstrakcyjnej:

- Kiedy chcesz dzielić kod pomiędzy blisko spokrewnionymi klasami (np. „Animal” z polami „name”, „age”).
- Kiedy spodziewasz się, że klasy dziedziczące po klasie abstrakcyjnej będą miały wiele wspólnych metod lub pól.
- Kiedy wymagane są modyfikatory dostępu inne niż publiczne.
- Kiedy chcesz, aby klasa bazowa dostarczała domyślną implementację niektórych metod, podczas gdy inne mogą być nadpisywane przez klasy potomne.

**Podsumowanie**: Klasa abstrakcyjna dostarcza wspólną definicję jako klasa bazowa, z której mogą korzystać klasy pochodne.

## Interfejs

- Interfejsy zawierają jedynie deklaracje metod, które klasy mają implementować.
- Definiują, jakie operacje obiekt może wykonywać, ale nie dostarczają implementacji.
- Interfejs tworzy „kontrakt” między klasą a światem zewnętrznym, egzekwowany w czasie kompilacji przez kompilator Javy.
- Wszystkie metody bez ciała są automatycznie publiczne i abstrakcyjne.
- Interfejs może dziedziczyć po innym interfejsie.
- Od Java 8 interfejsy mogą zawierać metody domyślne (z ciałem), a od Java 9 także metody prywatne.

### Użycie interfejsu:

- Kiedy spodziewasz się, że niepowiązane klasy będą implementować twój interfejs (np. Comparable, Cloneable).
- Kiedy chcesz określić zachowanie danego typu danych, nie interesując się, która klasa je implementuje.
- Kiedy chcesz oddzielić różne zachowania.

**Podsumowanie**: Interfejs oddziela „co” od „jak” i służy do ujednolicenia zachowań różnych typów.

## Interfejs w funkcjach Javy

- Java wykorzystuje interfejsy w wielu wbudowanych funkcjach, takich jak Kolekcje (np. List, Queue) oraz w połączeniach bazodanowych (JDBC).
- Interfejsy umożliwiają pisanie kodu bez względu na szczegóły implementacyjne bazy danych, dzięki czemu kod jest bardziej uniwersalny.

## Porównanie – Klasa Abstrakcyjna a Interfejs

- Oba podejścia stosowane są jako typy abstrakcyjne, pełniące rolę typów referencyjnych w kodzie.

| Cechy | Klasa Abstrakcyjna | Interfejs |
| --- | --- | --- |
| Instancjonowanie | Nie można instancjonować | Nie można instancjonować |
| Typy metod | Metody abstrakcyjne i z implementacją | Metody abstrakcyjne i (od Java 8) domyślne |
| Pola | Może zawierać pola instancji | Może zawierać tylko stałe (static final) |
| Modyfikatory dostępu | Dowolne dla metod z implementacją | Wszystkie metody publiczne i abstrakcyjne |
| Dziedziczenie | Dziedziczy po jednej klasie | Może dziedziczyć po wielu interfejsach |
| Kiedy używać? | Kiedy klasy są blisko spokrewnione | Kiedy klasy są niespokrewnione, ale mają wspólne zachowania |
| Przykłady użycia | „Animal” z polami „name”, „age” | Comparable, Cloneable |
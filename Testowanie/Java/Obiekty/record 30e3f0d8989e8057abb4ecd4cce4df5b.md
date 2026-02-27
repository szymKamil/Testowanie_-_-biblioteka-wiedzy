# record

# Record

`Record` ma zastępować POJO. Record to specjalny rodzaj klasy który zawiera dane które nie mają być modyfikowane.

## Czym jest `record`?

`record` to nowa funkcjonalność wprowadzona w Javie 14 jako preview, a od Javy 16 stała się stabilną częścią języka. `record` służy do definiowania prostych klas danych, które automatycznie generują metody, takie jak `equals()`, `hashCode()`, oraz `toString()`, co znacząco upraszcza kod.

## Cechy `record`

- **Immutability**: Pola w `record` są domyślnie `final`, co oznacza, że nie można ich zmieniać po utworzeniu obiektu.
- **Prosta składnia**: Tworzenie klasy `record` wymaga mniej kodu niż tradycyjna klasa.
- **Automatyczne generowanie metod**: Metody `equals()`, `hashCode()`, oraz `toString()` są automatycznie generowane na podstawie zdefiniowanych pól.

## Przykład `record`

Oto przykład użycia `record` do reprezentowania osoby:

```java
public record Person(String name, int age) {
}
```

## Zastosowania record

`Record` jest idealnym rozwiązaniem do reprezentowania danych, które są prostymi strukturami, jak DTO (Data Transfer Object), które nie wymagają skomplikowanej logiki.

Przykład:

```java
public record Person(String name, int age) {
// Możesz dodać dodatkowe metody, jeśli to konieczne
public String description() {
return name + " is " + age + " years old.";
}
}

// Przykład użycia
public class Main {
public static void main(String[] args) {
Person person = new Person("Alice", 30);
System.out.println(person); // Automatycznie wywoła to toString()
System.out.println(person.description()); // Wywołanie metody opisującej
}
}
```

Tworząc record Java automatycznie tworzy zmienne umieszczone w nagłówku klasy.

# Konstruktor

Rodzaje konstruktorów w Javie
Konstruktor kanoniczny (Canonical, Long Constructor)

Jest to implikowany konstruktor, automatycznie generowany przez kompilator.
Można jawnie zadeklarować swój własny konstruktor. W takim przypadku kompilator nie wygeneruje już domyślnego konstruktora kanonicznego.
Jeśli zdecydujesz się zadeklarować własny kanoniczny konstruktor, musisz zadbać, aby wszystkie pola były przypisane wartościom.

Konstruktor niestandardowy (Custom Constructor)
Jest to po prostu przeciążony konstruktor.
Musi jawnie wywołać konstruktor kanoniczny jako swoje pierwsze wyrażenie.

Konstruktor zwarty (Compact, Short Constructor)
Specjalny rodzaj konstruktora, który można stosować tylko w rekordach (records).
Umożliwia zwięzłe deklarowanie konstruktora kanonicznego.

Konstruktor zwarty (Compact Constructor)
Deklarowany bez nawiasów i argumentów.
Ma jednak dostęp do wszystkich argumentów konstruktora kanonicznego.
Uwaga: Argumenty te nie są tym samym, co pola instancji!
Nie można przypisywać wartości bezpośrednio do pól instancji w konstruktorze zwartym.
Przypisania dokonywane przez kanoniczny konstruktor odbywają się po wykonaniu kodu konstruktora zwartego.
Ograniczenie: Nie można mieć jednocześnie konstruktora zwartego i jawnie zadeklarowanego konstruktora kanonicznego.
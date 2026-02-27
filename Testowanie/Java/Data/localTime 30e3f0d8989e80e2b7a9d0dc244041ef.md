# localTime

# LocalTime w Javie

LocalTime to klasa z pakietu java.time, która reprezentuje czas (godziny, minuty, sekundy i nanosekundy) bez daty i strefy czasowej. Jest często używana, gdy potrzebujesz operować tylko na czasie, na przykład godzinach otwarcia sklepu lub godzinie alarmu.

Cechy LocalTime
Reprezentuje tylko czas (bez daty i strefy czasowej).
Jest niezmienna (immutable) – każda operacja zwraca nową instancję.
Możliwość dokładności do nanosekund.
Obsługuje operacje arytmetyczne, np. dodawanie godzin czy minut.

```java
import java.time.LocalTime;

public class LocalTimeExample {
    public static void main(String[] args) {
        LocalTime currentTime = LocalTime.now();
        LocalTime customTime = LocalTime.of(14, 30, 15); // 14:30:15

        System.out.println("Aktualny czas: " + currentTime);
        System.out.println("Czas niestandardowy: " + customTime);
    }
}
```

| **Metoda** | **Opis** | **Przykład użycia** |
| --- | --- | --- |
| `LocalTime.now()` | Pobiera aktualny czas systemowy. | `LocalTime.now()` |
| `LocalTime.of(hour, minute)` | Tworzy czas z podanymi godzinami i minutami. | `LocalTime.of(14, 30)` |
| `LocalTime.of(hour, minute, second)` | Tworzy czas z godzinami, minutami i sekundami. | `LocalTime.of(14, 30, 15)` |
| `LocalTime.parse(String)` | Konwertuje łańcuch znaków na `LocalTime`. | `LocalTime.parse("14:30:15")` |
| `getHour()` | Zwraca godzinę. | `time.getHour()` |
| `getMinute()` | Zwraca minuty. | `time.getMinute()` |
| `getSecond()` | Zwraca sekundy. | `time.getSecond()` |
| `plusHours(long hours)` | Dodaje określoną liczbę godzin. | `time.plusHours(2)` |
| `plusMinutes(long minutes)` | Dodaje określoną liczbę minut. | `time.plusMinutes(15)` |
| `minusMinutes(long minutes)` | Odejmuje określoną liczbę minut. | `time.minusMinutes(10)` |
| `isBefore(LocalTime other)` | Sprawdza, czy czas jest wcześniejszy niż podany czas. | `time.isBefore(LocalTime.of(18, 0))` |
| `isAfter(LocalTime other)` | Sprawdza, czy czas jest późniejszy niż podany czas. | `time.isAfter(LocalTime.of(9, 0))` |
| `toString()` | Konwertuje czas na format tekstowy (`HH:MM:SS`). | `time.toString()` |

## Instant

Instant to klasa z pakietu java.time, która reprezentuje punkt w czasie na osi czasu, mierzony w sekundach i nanosekundach od Epoch (czyli 1 stycznia 1970 roku, 00:00:00 UTC). Jest to odpowiednik timestamp (znacznika czasu) i działa w kontekście strefy czasowej UTC.

Cechy Instant
Reprezentuje punkt w czasie w formacie UTC.
Jest niezmienny (immutable).
Często używany do pomiarów czasu lub w aplikacjach wymagających precyzyjnych znaczników czasu.
Może być konwertowany na inne klasy daty i czasu, jak LocalDateTime czy ZonedDateTime.

```java
import java.time.Instant;

public class InstantExample {
    public static void main(String[] args) {
        // Pobranie aktualnego momentu w czasie (w UTC)
        Instant now = Instant.now();
        System.out.println("Aktualny czas: " + now);

        // Dodanie 10 sekund do obecnego czasu
        Instant future = now.plusSeconds(10);
        System.out.println("Czas po 10 sekundach: " + future);
    }
}
```

Epoch Time (czas epokowy) to sposób reprezentacji czasu jako liczby sekund lub milisekund, które upłynęły od 01 stycznia 1970 roku, 00:00:00 UTC (tzw. Unix Epoch). Jest to powszechnie stosowany standard do mierzenia czasu w systemach informatycznych.
Data 01 stycznia 1970 została przyjęta jako punkt odniesienia (tzw. Unix Epoch) dla większości systemów uniksowych i posłużyła do uproszczenia obliczeń związanych z datą i czasem. W praktyce umożliwia to przechowywanie czasu jako pojedynczej liczby całkowitej, co ułatwia obliczenia oraz porównywanie różnych momentów w czasie.

Różnice między Duration a Period w Javie
Duration i Period to klasy w pakiecie java.time, które służą do reprezentowania różnicy między dwoma punktami w czasie, ale różnią się zakresem i precyzją. Oto szczegółowe porównanie obu klas:

Kiedy używać Duration, a kiedy Period?
Duration – gdy potrzebujesz różnicy w czasie (godziny, minuty, sekundy, nanosekundy). Przykłady: czas trwania filmu, odstęp między dwoma punktami czasowymi.

Period – gdy potrzebujesz różnicy w dniach, miesiącach lub latach. Przykłady: różnica między dwoma datami urodzin, okres obowiązywania umowy.

| **Cecha** | **Duration** | **Period** |
| --- | --- | --- |
| **Zakres** | Godziny, minuty, sekundy, milisekundy, nanosekundy | Lata, miesiące, dni |
| **Precyzja** | Precyzja do nanosekund | Precyzja do pełnych dni |
| **Typ danych** | Reprezentuje **czas** między dwoma punktami w czasie | Reprezentuje **datę** między dwoma datami |
| **Przykład tworzenia** | `Duration.ofHours(5)` | `Period.ofYears(2)` |
| **Obiekt odniesienia** | Operuje na `Instant`, `LocalTime`, `ZonedDateTime` | Operuje na `LocalDate` |
| **Format wyświetlania** | Godziny, minuty, sekundy (np. `PT5H30M`) | Lata, miesiące, dni (np. `P2Y3M5D`) |
| **Przykładowe użycie** | `Duration.between(startTime, endTime)` | `Period.between(startDate, endDate)` |
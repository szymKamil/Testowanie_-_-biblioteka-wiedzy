# Dokumentowanie kodu

# Komenatrze

Komentarze tworzymy jednolinijkowo - “//” (Intelij = <Ctrl+/> i wielolinijkowo “/* */” <Ctrl+Shift+/> uprzednio zaznaczając fragment kodu. Uwaga, `/* */` to komentarz blokowy (wielolinijkowy), a nie tylko "wielolinijkowy" – można go też użyć w środku linii, np. `int x = /* wartość */ 5;`.

**Dobre praktyki** – komentarzy `//` nie używa się do "wyłączania" kodu na stałe, komentarz powinien wyjaśniać *dlaczego*, a nie *co* wykonuje (kod powinien być samodokumentujący)

## Komenatrze Javadoc

Komentarze Javadoc w języku Java służą do dokumentowania kodu źródłowego. Są one rozpoznawane przez narzędzie javadoc, które generuje z nich czytelną dokumentację HTML. Komentarze Javadoc rozpoczynają się od /** i kończą na */. Używa się ich głównie nad klasami, interfejsami, konstruktorami i metodami. 

*Przykład:*

```java
/**
 * Klasa pomocnicza do operacji matematycznych.
 *
 * Przykład użycia:
 * {@code
 * MathUtils utils = new MathUtils();
 * int wynik = utils.dodaj(2, 3);
 * }
 *
 * Zobacz także klasę {@link java.lang.Math} dla bardziej zaawansowanych operacji.
 *
 * @author Jan
 * @version 1.0
 * @since 1.0
 */
public class MathUtils {

    /**
     * Dodaje dwie liczby całkowite.
     *
     * @param a pierwsza liczba
     * @param b druga liczba
     * @return suma liczb a i b
     */
    public int dodaj(int a, int b) {
        return a + b;
    }
```

W dokumentacji Javadoc możemy dodawać też adnotacje odsyłające do fragmentów kodu:

| Tag | Użycie | Opis | Stosowany do |
| --- | --- | --- | --- |
| `@author` | `@author Imię` | Informacja o autorze klasy lub interfejsu. | Klasa, interfejs |
| `@version` | `@version 1.0` | Wersja klasy lub interfejsu. | Klasa, interfejs |
| `@param` | `@param nazwa opis` | Opisuje parametr metody lub konstruktora. | Metoda, konstruktor |
| `@return` | `@return opis` | Opisuje, co metoda zwraca (jeśli nie jest `void`). | Metoda |
| `@throws` | `@throws Exception opis` | Opisuje wyjątek, który może zostać rzucony. | Metoda, konstruktor |
| `@see` | `@see NazwaKlasy` lub `@see #metoda()` | Tworzy link do innego elementu kodu lub dokumentacji. | Klasa, metoda, pole itd. |
| `@since` | `@since 1.5` | Informuje, od której wersji dostępny jest element. | Klasa, metoda, pole itd. |
| `@deprecated` | `@deprecated opis` | Oznacza element jako przestarzały i podaje alternatywę. | Klasa, metoda, pole itd. |
| `{@link}` | `{@link NazwaKlasy}` | Wstawia link do klasy/metody wewnątrz tekstu opisu. | W treści komentarza |
| `{@code}` | `{@code jakiśKod}` | Formatowany tekst kodu wewnątrz komentarza. | W treści komentarza |
| **@`inheritDoc`** |  | Tag Javadoc do dziedziczenia dokumentacji z nadklasy/interfejsu |  |

**Gdzie *nie* stosować Javadoc** – np. nad metodami prywatnymi (konwencjonalnie Javadoc jest dla publicznego API).
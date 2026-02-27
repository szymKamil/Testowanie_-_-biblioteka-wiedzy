# Localization

# Localization

### Klasy związane z lokalizacją

| **Klasa** | **Opis** |
| --- | --- |
| `Locale` | Reprezentuje określony język i kraj. Używana do definiowania lokalizacji dla aplikacji. |
| `ResourceBundle` | Pozwala ładować zlokalizowane zasoby (np. teksty). Działa z plikami `.properties`. |
| `NumberFormat` | Formatowanie liczb zgodnie z lokalizacją (np. daty, waluty). |
| `DateFormat` | Formatowanie dat zgodnie z lokalizacją. |
| `Collator` | Porównywanie ciągów znaków zgodnie z zasadami lokalizacji. |

### Metody klasy `Locale`

| **Metoda** | **Opis** |
| --- | --- |
| `Locale.getDefault()` | Zwraca domyślną lokalizację dla bieżącego środowiska. |
| `Locale.getLanguage()` | Zwraca kod języka (np. `"pl"` dla polskiego). |
| `Locale.getCountry()` | Zwraca kod kraju (np. `"PL"` dla Polski). |
| `Locale.setDefault(Locale locale)` | Ustawia domyślną lokalizację dla aplikacji. |
| `Locale.toString()` | Zwraca tekstową reprezentację lokalizacji (np. `"pl_PL"`). |

# ResourceBundle class

Klasa ResourceBundle w Javie jest używana w kontekście lokalizacji (internationalization, i18n) do zarządzania zasobami, takimi jak teksty, które mogą różnić się w zależności od języka, regionu lub kultury. Jest to mechanizm umożliwiający aplikacjom dostarczanie odpowiednich treści w zależności od ustawień lokalnych użytkownika.

Podstawowe informacje o ResourceBundle:
Czym jest?

Klasa ResourceBundle to abstrakcyjna klasa, która służy do ładowania i zarządzania zestawami zasobów. Najczęściej używa się jej do obsługi plików typu .properties, które zawierają pary klucz-wartość reprezentujące teksty w różnych językach.
Jak działa?

Na podstawie obiektu Locale (np. Locale.ENGLISH, Locale.FRENCH), ResourceBundle wybiera odpowiedni zestaw zasobów (np. plik messages_en.properties dla języka angielskiego).
Jeśli dla danego Locale nie istnieje dedykowany plik, ResourceBundle szuka najbardziej ogólnego pasującego zestawu.
Formaty obsługiwane:

Pliki .properties (najpopularniejsze).
Klasy subklasy ListResourceBundle lub PropertyResourceBundle.
Jak używać ResourceBundle?
1. Przygotowanie plików .properties:
Tworzymy pliki z zasobami dla różnych języków:

messages.properties (domyślne teksty):
properties
Skopiuj kod
greeting=Hello
farewell=Goodbye
messages_pl.properties (teksty dla języka polskiego):
properties
Skopiuj kod
greeting=Witaj
farewell=Do widzenia

```java
   import java.util.Locale;
   import java.util.ResourceBundle;

public class LocalizationExample {
public static void main(String[] args) {
// Ustawienie lokalizacji na polską
Locale locale = new Locale("pl", "PL");
ResourceBundle bundle = ResourceBundle.getBundle("messages", locale);

        // Pobieranie wartości z zasobów
        String greeting = bundle.getString("greeting");
        String farewell = bundle.getString("farewell");

        // Wyświetlanie tekstów
        System.out.println(greeting);  // Witaj
        System.out.println(farewell);  // Do widzenia
    }
}
```

Kluczowe cechy ResourceBundle:
Hierarchia lokalizacji:

Jeśli messages_pl_PL.properties nie istnieje, zostanie użyty plik bardziej ogólny, np. messages_pl.properties, a ostatecznie messages.properties.
Obsługa Locale:

Locale to klasa reprezentująca ustawienia lokalne użytkownika, takie jak język i kraj.
Unikanie hardkodowania:

ResourceBundle pozwala na oddzielenie kodu aplikacji od tekstów lokalizacyjnych.
Fallback Mechanism:

Gdy brak pliku dla danego Locale, używane są bardziej ogólne wersje.
Zalety korzystania z ResourceBundle:
Elastyczność: Wspiera łatwe dodawanie nowych języków.
Czystość kodu: Oddziela teksty od logiki aplikacji.
Modularność: Wystarczy zmienić plik zasobów, aby zaktualizować teksty.
Jeśli chcesz, mogę przygotować przykład bardziej zaawansowanego wykorzystania, np. z interfejsem graficznym (Swing/JavaFX) lub konfiguracją w Mavenie.
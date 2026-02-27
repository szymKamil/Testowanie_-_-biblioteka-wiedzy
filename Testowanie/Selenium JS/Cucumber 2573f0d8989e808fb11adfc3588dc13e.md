# Cucumber

| Nazwa | Język | Opis | Licencja | Opiekun | Strona internetowa |
| --- | --- | --- | --- | --- | --- |
| Cucumber | Ruby, Java, JavaScript, Python | Platforma testowa do tworzenia automatycznych testów akceptacyjnych zgodnie z podejściem BDD | MIT | SmartBear Software | [cucumber.io](https://cucumber.io/) |

Cucumber to framework do testów akceptacyjnych i BDD (Behavior-Driven Development). Umożliwia pisanie testów w języku zrozumiałym dla nietechnicznych osób (np. analityków biznesowych), a jednocześnie wykonywalnym przez programistów i testerów. Scenariusze testowe opisuje się w plikach .feature w języku Gherkin, który używa słów kluczowych takich jak `Given, When, Then`. Kluczowe cechy Cucumbera:

- Czytelne scenariusze dla biznesu.
- Automatyczne mapowanie na kod (np. Java).
- Integracja z narzędziami jak TestNG, JUnit, Selenium.
- Generowanie raportów (HTML, JSON, Allure).

Gherkin w skrócie:

- Feature: Opisuje testowaną funkcjonalność.
- Scenario: Pojedynczy przypadek testowy.
- Given: Stan początkowy (kontekst).
- When: Akcja użytkownika.
- Then: Oczekiwany wynik.
- And, But: Uzupełniają kroki.
- Background: Wspólne kroki dla wszystkich scenariuszy w pliku.
- Scenario Outline i Examples: Parametryzacja testów.
- Data Tables: Tabele danych do przekazywania wielu wartości.

---

2. Konfiguracja projektu - Cucumber najlepiej działa w projektach opartych na Maven lub Gradle. Poniżej konfiguracja dla obu. Maven – pom.xml Dodaj zależności do pliku pom.xml

```xml
<dependencies>
    <!-- Cucumber -->
    <dependency>
        <groupId>io.cucumber</groupId>
        <artifactId>cucumber-java</artifactId>
        <version>7.18.0</version>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>io.cucumber</groupId>
        <artifactId>cucumber-testng</artifactId> <!-- lub cucumber-junit -->
        <version>7.18.0</version>
        <scope>test</scope>
    </dependency>
    <!-- Selenium (opcjonalnie) -->
    <dependency>
        <groupId>org.seleniumhq.selenium</groupId>
        <artifactId>selenium-java</artifactId>
        <version>4.25.0</version>
    </dependency>
    <!-- Allure (opcjonalnie, dla raportów) -->
    <dependency>
        <groupId>io.qameta.allure</groupId>
        <artifactId>allure-cucumber7-jvm</artifactId>
        <version>2.27.0</version>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>io.qameta.allure</groupId>
        <artifactId>allure-testng</artifactId>
        <version>2.27.0</version>
        <scope>test</scope>
    </dependency>
</dependencies>

<build>
    <plugins>
        <!-- Maven Surefire dla równoległego uruchamiania -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-plugin</artifactId>
            <version>3.2.5</version>
            <configuration>
                <parallel>classes</parallel>
                <threadCount>4</threadCount>
            </configuration>
        </plugin>
    </plugins>
</build>
```

Gradle – build.gradleDla projektów Gradle dodaj:gradle

```
dependencies {
    testImplementation 'io.cucumber:cucumber-java:7.18.0'
    testImplementation 'io.cucumber:cucumber-testng:7.18.0' // lub cucumber-junit
    testImplementation 'org.seleniumhq.selenium:selenium-java:4.25.0'
    testImplementation 'io.qameta.allure:allure-cucumber7-jvm:2.27.0'
    testImplementation 'io.qameta.allure:allure-testng:2.27.0'
}
```

Uruchamianie testów:bash

```bash
./gradlew test
```

---

3. Struktura projektu Standardowa struktura projektu:

```
src
 └── test
      ├── java
      │    └── steps        → Definicje kroków (Step Definitions)
      │    └── runners      → Klasy uruchamiające testy
      │    └── support      → WebDriver, Hooks, konfiguracja
      └── resources
           └── features     → Pliki .feature (scenariusze Gherkin)
```

---

4. Pierwszy plik .feature 

```gherkin
Feature: Logowanie do aplikacji

  Background:
    Given użytkownik otwiera stronę logowania

  Scenario: Użytkownik loguje się poprawnie
    When wpisuje login "admin" i hasło "1234"
    Then powinien zobaczyć stronę główną
```

Uwaga: Background pozwala zdefiniować wspólne kroki dla wszystkich scenariuszy w pliku, co zmniejsza duplikację.

Słowa kluczowe można zapisywać także po polsku, Gherkin ma wbydowane tłumaczenie/lokalizację:

| Słowo kluczowe | Odpowiednik |
| --- | --- |
| `Feature` | `Właściwość Funkcja Aspekt Potrzeba biznesowa` |
| `Background` | `Założenia` |
| `Rule` | `ZasadaReguła` |
| `Scenario` | `Przykład Scenariusz` |
| `Scenario Outline` | `Szablon scenariusza` |
| `Examples` | `Przykłady` |
| `Given` | `* Zakładając Mając Zakładając, że` |
| `When` | `* Jeżeli Jeśli Gdy Kiedy` |
| `Then` | `*Wtedy` |
| `And` | `*Oraz I` |
| `But` | `*Ale` |

| Co w feature | Jak to działa | Co w Step Definition | Przykład |
| --- | --- | --- | --- |
| `<login>` / `<hasło>` | Parametr w **Scenario Outline** → jest zastępowany przez wartość z tabeli `Examples` | W kroku Cucumbera używasz `{string}` lub regex, żeby odebrać wartość | Feature: `When Użytkownik wpisuje "<login>" oraz "<hasło>" ...`  Step: `@When("Użytkownik wpisuje {string} oraz {string} ...") public void step(String login, String password)` |
| `"stała wartość"` | Literalny string wpisany na sztywno w scenariuszu | `{string}` w kroku Cucumbera odczyta dokładnie tę wartość | Feature: `When Użytkownik wpisuje "testUser" oraz "haslo123" ...`  Step: `@When("Użytkownik wpisuje {string} oraz {string} ...")` |
| `123` / true / false | Literalna liczba lub boolean | `{int}`, `{double}`, `{boolean}` w kroku | Feature: `Then pole liczbowe ma wartość 123`  Step: `@Then("pole liczbowe ma wartość {int}") public void check(int liczba)` |
| `@tag` | Tag scenariusza do filtrowania testów | W runnerze w `tags = "@tag"` | Feature: `@positive Scenario: ...`  Runner: `tags = "@positive"` |
| `<param>` **bez Scenario Outline** | NIE DZIAŁA – parser nie wie skąd wziąć wartość | → rzuca błąd parsowania | ❌ Feature: `When wpisuje <login>` |

---

5. Definicje kroków (Step Definitions)

```java
package steps;

import io.cucumber.java.en.*;

public class LoginSteps {

    @Given("użytkownik otwiera stronę logowania")
    public void openLoginPage() {
        System.out.println("Otwieram stronę logowania");
    }

    @When("wpisuje login {string} i hasło {string}")
    public void enterCredentials(String username, String password) {
        System.out.println("Wpisuję login: " + username + " i hasło: " + password);
    }

    @Then("powinien zobaczyć stronę główną")
    public void verifyHomePage() {
        System.out.println("Widzę stronę główną");
    }
}
```

---

6. Klasa Runnera

```java
package runners;

import io.cucumber.testng.AbstractTestNGCucumberTests;
import io.cucumber.testng.CucumberOptions;

@CucumberOptions(
        features = "src/test/resources/features",
        glue = {"steps", "support"},
        plugin = {"pretty", "html:target/cucumber-report.html", "io.qameta.allure.cucumber7jvm.AllureCucumber7Jvm"},
        monochrome = true,
        tags = "@smoke" // opcjonalnie, tylko scenariusze z tagiem @smoke
)
public class TestRunner extends AbstractTestNGCucumberTests {
}
```

| Atrybut | Typ / Wartość | Opis | Przykład |
| --- | --- | --- | --- |
| `features` | `String` lub `String[]` | Ścieżka do plików `.feature` albo folderu z nimi | `features = "src/test/resources/features"features = {"src/test/resources/f1", "src/test/resources/f2"}` |
| `glue` | `String[]` | Pakiety, w których Cucumber szuka klas ze step definitions | `glue = {"Cucumber.Rahul.LoginPage"}` |
| `tags` | `String` | Filtr tagów, które mają być uruchomione | `tags = "@positive"tags = "@smoke or @regression"` |
| `plugin` | `String[]` | Raporty / logi, np. html, json, pretty | `plugin = {"pretty", "html:target/cucumber-reports.html"}` |
| `monochrome` | `boolean` | Czy logi w konsoli mają być czytelne (bez znaków ASCII) | `monochrome = true` |
| `dryRun` | `boolean` | Sprawdza, czy wszystkie kroki mają implementację, bez uruchamiania testów | `dryRun = true` |
| `strict` | `boolean` | W starszych wersjach – testy failują, jeśli nie wszystkie kroki są zaimplementowane | `strict = true` |
| `name` | `String[]` | Filtr scenariuszy po nazwie | `name = {"Użytkownik poprawnie loguje się do aplikacji"}` |
| `snippets` | `SnippetType` | Format generowanych kroków dla brakujących stepów (`CAMELCASE` lub `UNDERSCORE`) | `snippets = SnippetType.CAMELCASE` |

---

**🔹 Najczęstsze użycie w praktyce (TestNG):**

```java
@CucumberOptions(
    features = "src/test/resources/Cucumber/Rahul/LoginPage",
    glue = {"Cucumber.Rahul.LoginPage"},
    tags = "@positive",
    plugin = {"pretty", "html:target/cucumber-reports.html"},
    monochrome = true
)
public class TestRunner extends AbstractTestNGCucumberTests {
}

```

7. Uruchamianie testów

- IntelliJ: Prawy klik na TestRunner.java → Run.
- Maven: mvn test
- Gradle: ./gradlew test

Raporty pojawią się w:

- HTML: target/cucumber-report.html
- Allure: target/allure-results (uruchom allure serve target/allure-results).

---

8. Parametryzacja scenariuszy (Scenario Outline) Użyj Scenario Outline do testowania wielu zestawów danych

```gherkin
Feature: Logowanie do aplikacji

  Scenario Outline: Logowanie różnymi użytkownikami
    Given użytkownik otwiera stronę logowania
    When wpisuje login "<login>" i hasło "<hasło>"
    Then powinien zobaczyć stronę główną

  Examples:
    | login | hasło |
    | admin | 1234  |
    | user  | pass  |
```

---

9. Tabele danych (Data Tables)Tabele danych są przydatne do przekazywania wielu wartości w jednym kroku:

```gherkin
Feature: Rejestracja użytkownika

  Scenario: Wypełnianie formularza użytkownika
    Given użytkownik otwiera formularz rejestracji
    When wypełnia formularz danymi:
      | Imię   | Nazwisko | Email             |
      | Jan    | Kowalski | jan@example.com   |
      | Anna   | Nowak    | anna@example.com  |
    Then formularz zostaje zapisany
```

Definicja kroku:

```java
package steps;

import io.cucumber.java.en.*;
import io.cucumber.datatable.DataTable;

import java.util.List;
import java.util.Map;

public class RegistrationSteps {

    @When("wypełnia formularz danymi:")
    public void fillForm(DataTable dataTable) {
        List<Map<String, String>> data = dataTable.asMaps(String.class, String.class);
        for (Map<String, String> row : data) {
            String imie = row.get("Imię");
            String nazwisko = row.get("Nazwisko");
            String email = row.get("Email");
            System.out.println("Wypełniam: " + imie + ", " + nazwisko + ", " + email);
        }
    }
}
```

---

10. Tagi pozwalają grupować i selektywnie uruchamiać scenariusze:

```gherkin
@smoke
Feature: Logowanie do aplikacji

  @positive
  Scenario: Poprawne logowanie
    Given użytkownik otwiera stronę logowania
    When wpisuje login "admin" i hasło "1234"
    Then powinien zobaczyć stronę główną

  @negative
  Scenario: Niepoprawne logowanie
    Given użytkownik otwiera stronę logowania
    When wpisuje login "admin" i hasło "wrong"
    Then powinien zobaczyć komunikat o błędzie
```

Uruchamianie z tagami:
W TestRunner.java ustaw:

```java
tags = "@smoke and @positive" // uruchomi tylko scenariusze z tagiem @positive w pliku z tagiem @smoke
```

Zastosowanie tagów:

- @smoke: Testy podstawowej funkcjonalności.
- @regression: Testy regresyjne.
- @wip: Testy w trakcie implementacji.

W Cucumberze zapisujesz **wymagania biznesowe i scenariusze testowe w języku Gherkin**.

Elementy typu `Business Need:` nie są częścią **samej składni Gherkina** (takiej jak `Feature`, `Scenario`, `Given`, `When`, `Then`), tylko są **konwencją stosowaną w dokumentacji lub w komentarzach**.

```java
Feature: Zakupy w sklepie warzywnym
  Business Need: Przetestowanie sklepu warzywnego
  Benefit: Upewnienie się, że klienci mogą poprawnie kupować produkty
  Role: Jako klient sklepu online
  Goal: Chcę móc kupić warzywa przez stronę internetową

```

Business Need – opis po co testujesz tę funkcję; bardziej narracyjny wstęp, często używany w stylu BDD, żeby podać kontekst biznesowy.

Benefit/Role/Goal – czasem stosuje się jako rozwinięcie metody "Feature Injection" (Given–When–Then na poziomie biznesowym).

---

11. Hooks (Before / After) Hooks pozwalają wykonywać kod przed i po scenariuszach. Przykład z zarządzaniem WebDriverem:

```java
package support;

import io.cucumber.java.After;
import io.cucumber.java.Before;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.firefox.FirefoxDriver;

public class Hooks {
    public static WebDriver driver;

    @Before
    public void setUp() {
        String browser = System.getProperty("browser", "chrome"); // domyślnie chrome
        if (browser.equals("chrome")) {
            driver = new ChromeDriver();
        } else if (browser.equals("firefox")) {
            driver = new FirefoxDriver();
        }
        driver.manage().window().maximize();
        System.out.println("Uruchamiam przeglądarkę: " + browser);
    }

    @After
    public void tearDown() {
        if (driver != null) {
            driver.quit();
        }
        System.out.println("Zamykam przeglądarkę");
    }
}
```

Uruchamianie z różną przeglądarką:

```bash
mvn test -Dbrowser=firefox
```

---

12. Integracja z Selenium – pełny przykład. Przykład scenariusza z Selenium i asercjami:

```gherkin
Feature: Logowanie do aplikacji

  Scenario: Poprawne logowanie z weryfikacją
    Given użytkownik otwiera stronę logowania
    When wpisuje login "admin" i hasło "1234"
    And kliknij przycisk logowania
    Then powinien zobaczyć nagłówek "Witaj, Admin"
```

Definicje kroków:

```java
package steps;

import io.cucumber.java.en.*;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.support.ui.ExpectedConditions;
import org.openqa.selenium.support.ui.WebDriverWait;
import support.Hooks;

import java.time.Duration;

import static org.junit.jupiter.api.Assertions.assertEquals;

public class LoginSteps {
    WebDriver driver = Hooks.driver;
    WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));

    @Given("użytkownik otwiera stronę logowania")
    public void openLoginPage() {
        driver.get("https://example.com/login");
    }

    @When("wpisuje login {string} i hasło {string}")
    public void enterCredentials(String username, String password) {
        driver.findElement(By.id("username")).sendKeys(username);
        driver.findElement(By.id("password")).sendKeys(password);
    }

    @And("kliknij przycisk logowania")
    public void clickLoginButton() {
        driver.findElement(By.id("login-button")).click();
    }

    @Then("powinien zobaczyć nagłówek {string}")
    public void verifyHeader(String expectedHeader) {
        String actualHeader = wait.until(ExpectedConditions.visibilityOfElementLocated(By.tagName("h1"))).getText();
        assertEquals(expectedHeader, actualHeader, "Nagłówek strony nie jest zgodny z oczekiwanym");
    }
}
```

Uwagi:

- Użyto WebDriverWait dla stabilności.
- Asercje z JUnit weryfikują wynik.
- Przykład zakłada elementy o ID username, password, login-button i nagłówek h1.

---

13. Równoległe uruchamianie testów. Dla dużych projektów warto uruchamiać testy równolegle, aby przyspieszyć ich wykonanie. Konfiguracja WebDrivera dla wątków:
Użyj ThreadLocal dla thread-safe WebDrivera:

```java
package support;

public class WebDriverFactory {
    private static final ThreadLocal<WebDriver> driver = new ThreadLocal<>();

    public static WebDriver getDriver() {
        if (driver.get() == null) {
            driver.set(new ChromeDriver());
        }
        return driver.get();
    }

    public static void quitDriver() {
        if (driver.get() != null) {
            driver.get().quit();
            driver.remove();
        }
    }
}
```

Zaktualizuj Hooks:

```java
package support;

import io.cucumber.java.After;
import io.cucumber.java.Before;

public class Hooks {
    @Before
    public void setUp() {
        WebDriverFactory.getDriver().manage().window().maximize();
    }

    @After
    public void tearDown() {
        WebDriverFactory.quitDriver();
    }
}
```

Uruchamianie:

- Maven: Konfiguracja w pom.xml (patrz sekcja 2).
- Gradle: ./gradlew test --parallel

Uwaga: Upewnij się, że aplikacja testowa wspiera równoległe testy (np. brak konfliktów w bazie danych).

---

14. Zaawansowane raporty z Allure. Allure generuje interaktywne raporty z podziałem na scenariusze i kroki. Konfiguracja:

- Dodaj zależności Allure do pom.xml (patrz sekcja 2).
- Zaktualizuj CucumberOptions w TestRunner.java (patrz sekcja 6).

Dodanie zrzutów ekranu przy nieudanych testach:java

```java
package support;

import io.cucumber.java.After;
import io.cucumber.java.Scenario;
import org.openqa.selenium.OutputType;
import org.openqa.selenium.TakesScreenshot;

public class Hooks {
    @After
    public void tearDown(Scenario scenario) {
        if (scenario.isFailed()) {
            byte[] screenshot = ((TakesScreenshot) WebDriverFactory.getDriver()).getScreenshotAs(OutputType.BYTES);
            scenario.attach(screenshot, "image/png", "screenshot");
        }
        WebDriverFactory.quitDriver();
    }
}
```

Generowanie raportu:bash

```bash
allure serve target/allure-results
```

---

15. Zalety Cucumbera

- Czytelność: Scenariusze zrozumiałe dla biznesu.
- Automatyzacja: Łatwe mapowanie Gherkina na kod Java.
- Integracje: Współpraca z TestNG, JUnit, Selenium, Allure.
- Raporty: HTML, JSON, Allure z wizualizacją wyników.
- Parametryzacja: Scenario Outline i Data Tables dla elastyczności.
- Równoległość: Przyspieszenie testów w dużych projektach.

---

16. Dobre praktyki

1. Zwięzłe scenariusze: Skup się na intencji użytkownika, unikaj szczegółów implementacyjnych.
    - Złe: "Kliknij przycisk o ID #submit-button".
    - Dobre: "Użytkownik potwierdza formularz".
2. Czytelne nazwy: Scenariusze i kroki powinny jasno opisywać cel testu.
3. Unikaj duplikacji: Używaj Background i wspólnych metod w krokach.
4. Jedna funkcjonalność na scenariusz: Każdy scenariusz testuje jeden aspekt.
5. Integracja z CI/CD: Skonfiguruj testy w Jenkins, GitHub Actions lub GitLab CI.
6. Dokumentacja: Komentuj skomplikowaną logikę w definicjach kroków.
7. Asercje: Używaj bibliotek jak AssertJ lub JUnit w krokach Then.

Przykład asercji:java

```java
@Then("powinien zobaczyć stronę główną")
public void verifyHomePage() {
    String expectedTitle = "Welcome";
    String actualTitle = WebDriverFactory.getDriver().getTitle();
    Assertions.assertEquals(expectedTitle, actualTitle, "Tytuł strony powinien być 'Welcome'");
}
```

---

17. Rozwiązywanie problemów

1. Błąd: "Step undefined"
    - Sprawdź zgodność kroków w .feature z definicjami w steps.
    - Upewnij się, że glue w CucumberOptions wskazuje właściwe pakiety.
2. Błąd: WebDriver nie działa
    - Dopasuj wersję przeglądarki i sterownika (np. ChromeDriver).
    - Ustaw webdriver.chrome.driver w kodzie lub PATH.
3. Testy działają wolno
    - Włącz równoległe uruchamianie.
    - Minimalizuj operacje w Hooks.
4. Problemy z raportami
    - Sprawdź konfigurację wtyczek (html, Allure).
    - Upewnij się, że folder target nie jest blokowany.

---

18. Przydatne zasoby

- [Oficjalna dokumentacja Cucumbera](https://cucumber.io/docs/cucumber/)
- [Dokumentacja Gherkina](https://cucumber.io/docs/gherkin/)
- [Allure Framework](https://allurereport.org/docs/)
- [Selenium WebDriver](https://www.selenium.dev/documentation/webdriver/)
- [Przykłady projektów na GitHub](https://github.com/search?q=cucumber+java)
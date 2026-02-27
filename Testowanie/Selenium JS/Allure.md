# Allure

[https://allurereport.org/docs/testng/](https://allurereport.org/docs/testng/)

## Co to jest Allure?

**Allure Framework** to **narzędzie do raportowania testów automatycznych**, które generuje **czytelne, interaktywne raporty** z wyników testów.

Umożliwia prezentację przebiegu testów, błędów, screenshotów, logów i danych o środowisku w przyjaznej formie HTML.

---

## Główne cechy Allure

| Funkcja | Opis |
| --- | --- |
| 🧾 **Raporty HTML** | Tworzy interaktywne raporty z testów (sekcje: passed, failed, broken, skipped). |
| 🧩 **Integracja z frameworkami** | Działa z popularnymi narzędziami jak: **JUnit, TestNG, Pytest, Cucumber, Selenium, Playwright**. |
| 📸 **Screenshoty i video** | Może automatycznie dodawać zrzuty ekranu, logi i nagrania do raportów. |
| 🧠 **Kontekst testu** | Pokazuje opis testu, kroki, dane wejściowe, załączniki, stack trace błędu. |
| 📊 **Statystyki i trendy** | Wizualne statystyki, np. procent udanych testów, czas wykonania, historia uruchomień. |
| ⚙️ **Integracja z CI/CD** | Współpracuje z Jenkins, GitHub Actions, GitLab CI, Bamboo itd. |

---

**Jak działa Allure?**

1. **Framework testowy (np. JUnit lub Pytest)** uruchamia testy.
2. Podczas ich wykonania powstają **pliki wynikowe (`.json`, `.xml`)** z danymi o przebiegu testów.
3. Allure **parsuje te dane** i tworzy z nich **raport HTML** z wykresami i szczegółami testów.

 Struktura przykładowa:

```
project/
 ├─ tests/
 ├─ allure-results/       # surowe wyniki testów (tworzone automatycznie)
 └─ allure-report/        # gotowy raport HTML po generacji
```

---

## Przykład użycia (Java + TestNG)

```xml
<!-- pom.xml -->
<dependencies>
    <dependency>
        <groupId>io.qameta.allure</groupId>
        <artifactId>allure-testng</artifactId>
        <version>2.24.0</version>
    </dependency>
</dependencies>

```

## Listenery

**Listener (nasłuchiwacz)** to specjalna klasa, która reaguje na zdarzenia w cyklu życia testów — np. rozpoczęcie testu, sukces, porażka, pominięcie itp.

W przypadku **Allure** listener automatycznie zbiera dane o przebiegu testów i zapisuje je do folderu `allure-results/`.

---

## Wbudowany listener Allure

Allure dostarcza gotowego listenera:

```java
io.qameta.allure.testng.AllureTestNg
```

To właśnie ten listener przechwytuje dane z TestNG i zapisuje je w formacie, który później przetwarza Allure CLI.

---

## Dodanie listenera w `testng.xml`

Plik `testng.xml` może zawierać sekcję `<listeners>` definiującą, które klasy listenerów mają być aktywne dla danej konfiguracji testów.

Przykład:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
<suite name="Allure Suite Example">

    <listeners>
        <!-- Listener Allure -->
        <listener class-name="io.qameta.allure.testng.AllureTestNg"/>
    </listeners>

    <test name="Example Tests">
        <classes>
            <class name="tests.LoginTest"/>
            <class name="tests.RegisterTest"/>
        </classes>
    </test>

</suite>

```

✅ **Efekt**:

Przy każdym uruchomieniu TestNG, Allure automatycznie tworzy pliki wynikowe w katalogu:

```
target/allure-results/
```

---

## 🔹 Alternatywa: adnotacja `@Listeners`

Zamiast edytować `testng.xml`, możesz też dodać listenera bezpośrednio w klasie testowej:

```java
import io.qameta.allure.testng.AllureTestNg;
import org.testng.annotations.Listeners;

@Listeners({AllureTestNg.class})
public class LoginTest {
    // testy...
}
```

 Obie metody działają identycznie, różni się tylko sposób konfiguracji.

---

**Co dzieje się po dodaniu listenera?**

Po uruchomieniu testów:

1. Allure zbiera dane o każdym teście:
    - status (`passed`, `failed`, `broken`, `skipped`)
    - czas wykonania
    - adnotacje (`@Step`, `@Attachment`, `@Description`, itd.)
2. Tworzy w katalogu `allure-results/` pliki `.json`, `.txt`, `.xml`.
3. Te dane można przekształcić w raport HTML:
    
    ```bash
    allure serve target/allure-results
    ```
    

---

Można też dodać więcej listenerów, np. własnych:

```xml
<listeners>
    <listener class-name="io.qameta.allure.testng.AllureTestNg"/>
    <listener class-name="utils.CustomListener"/>
</listeners>
```

---

## Powiązanie Allure Listener z własnym Listenerem TestNG

## Kontekst

Allure ma **własnego listenera** (`io.qameta.allure.testng.AllureTestNg`), który:

- zbiera dane o testach (status, opis, czas, adnotacje),
- zapisuje je do `allure-results/`.

Natomiast **TestNG** pozwala tworzyć **własne listenery**, np. do:

- robienia screenshotów,
- logowania błędów,
- dodatkowych raportów.

Celem jest **zintegrowanie własnego listenera TestNG z Allure**, tak żeby np. **screenshot przy błędzie testu był widoczny w raporcie Allure**.

---

---

**Stwórz własnego listenera TestNG**

Utwórz klasę, która **implementuje interfejs `ITestListener`**.

```java
import io.qameta.allure.Attachment;
import org.testng.ITestListener;
import org.testng.ITestResult;

public class ScreenshotListener implements ITestListener {

    @Override
    public void onTestFailure(ITestResult result) {
        saveScreenshot();  // robimy screenshot po błędzie
    }

    @Attachment(value = "Screenshot on failure", type = "image/png")
    public byte[] saveScreenshot() {
        try {
            // przykład dla Selenium WebDriver
            return ((TakesScreenshot) WebDriverManager.getDriver())
                    .getScreenshotAs(OutputType.BYTES);
        } catch (Exception e) {
            e.printStackTrace();
            return new byte[0];
        }
    }
}
```

Co się dzieje:

- `onTestFailure()` uruchamia się automatycznie, gdy test nie przejdzie,
- metoda z adnotacją `@Attachment` zapisuje obraz w Allure,
- plik trafia do `allure-results` i jest widoczny w raporcie HTML.

---

**Połącz oba listenery w `testng.xml`**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
<suite name="Allure Integration Suite">

    <listeners>
        <!-- Wbudowany listener Allure -->
        <listener class-name="io.qameta.allure.testng.AllureTestNg"/>
        <!-- Własny listener do screenshotów -->
        <listener class-name="listeners.ScreenshotListener"/>
    </listeners>

    <test name="Allure Tests">
        <classes>
            <class name="tests.LoginTest"/>
        </classes>
    </test>
</suite>
```

Dzięki temu:

- **AllureTestNg** zbiera dane testowe,
- **ScreenshotListener** dodaje screenshot przy błędzie,
- obie funkcje działają razem.

---

**Alternatywa: adnotacja `@Listeners`**

Zamiast XML-a, możesz też dodać oba listenery w klasie testowej:

```java
import io.qameta.allure.testng.AllureTestNg;
import org.testng.annotations.Listeners;

@Listeners({AllureTestNg.class, ScreenshotListener.class})
public class LoginTest {
    // ...
}

```

---

**Na końcu wygeneruj raport.** Po uruchomieniu testów (np. przez IntelliJ, Maven lub Jenkins):

```bash
allure serve target/allure-results
```

W raporcie przy nieudanym teście pojawi się sekcja **Attachments**, z miniaturą screenu do pobrania.

---

**(Opcjonalnie) Dodaj logi tekstowe jako załącznik**

```java
@Attachment(value = "Test log", type = "text/plain")
public String saveLog(String message) {
    return message;
}

```

Możesz wywołać `saveLog("Test failed due to timeout")` w `onTestFailure()` — i Allure doda plik tekstowy.

---

## Podsumowanie

| Element | Rola |
| --- | --- |
| `AllureTestNg` | Listener Allure – zbiera dane testów |
| `ScreenshotListener` | Własny listener TestNG – robi screenshot |
| `@Attachment` | Adnotacja Allure – dodaje załączniki do raportu |
| `testng.xml` / `@Listeners` | Umożliwia połączenie obu listenerów |
| `allure serve allure-results` | Generuje raport HTML |

---

## Tip

Jeśli używasz **Selenium Grid** lub **równoległych testów**, pamiętaj by Twój listener używał:

```java
WebDriverManager.getDriver(); // ThreadLocal WebDriver
```

żeby screenshoty nie mieszały się między testami.

### Generowanie raportu:

```bash
allure serve allure-results
```

➡️ automatycznie otwiera raport w przeglądarce.

---

## Allure – Adnotacje w testach (Java / TestNG)

Adnotacje Allure (ang. *annotations*) pozwalają:

- **opisać testy** (np. co testują, jak ważne są),
- **dodać kroki i załączniki** do raportu,
- **grupować testy** według epików, funkcji, historii użytkownika,
- **wzbogacić raport Allure** o metadane (severity, linki, parametry itp.).

---

| Adnotacja | Opis | Przykład |
| --- | --- | --- |
| `@Description` | Dodaje szczegółowy opis testu widoczny w raporcie | `@Description("Sprawdza logowanie użytkownika z poprawnymi danymi")` |
| `@Epic` | Grupuje testy według dużych obszarów (np. modułów systemu) | `@Epic("Panel logowania")` |
| `@Feature` | Opisuje mniejszy element funkcjonalny w obrębie epika | `@Feature("Przycisk 'Zaloguj'")` |
| `@Story` | Reprezentuje konkretny przypadek użycia lub wymaganie | `@Story("Użytkownik może zalogować się poprawnym loginem")` |
| `@Severity` | Oznacza poziom ważności testu (blokujący, krytyczny itd.) | `@Severity(SeverityLevel.CRITICAL)` |
| `@Step` | Dodaje do raportu krok testowy (metodę opisującą akcję) | `@Step("Kliknięcie przycisku logowania")` |
| `@Attachment` | Dołącza do raportu plik, screenshot lub log | `@Attachment(value="Screenshot", type="image/png")` |
| `@Link` | Dodaje link do raportu (np. do Jiry, test case’a, dokumentacji) | `@Link(name="JIRA-101", url="https://jira.company.com/browse/JIRA-101")` |
| `@Issue` | Łączy test z konkretnym zgłoszeniem błędu | `@Issue("BUG-123")` |
| `@TmsLink` | Powiązuje test z przypadkiem testowym w systemie TMS | `@TmsLink("TC-5678")` |
| `@Owner` | Oznacza osobę odpowiedzialną za test | `@Owner("Kamil")` |
| `@Parameter` *(rzadko używana)* | Pokazuje parametry przekazane do testu | `@Parameter("browser")` |

Przykład użycia  adnotacji

```java
import io.qameta.allure.*;
import org.testng.annotations.Test;

@Epic("Panel logowania")
@Feature("Formularz logowania")
public class LoginTest {

    @Test
    @Story("Użytkownik loguje się poprawnymi danymi")
    @Description("Test sprawdza, czy użytkownik może zalogować się używając poprawnych danych logowania.")
    @Severity(SeverityLevel.CRITICAL)
    @Owner("Kamil Szymański")
    @Link(name = "Dokumentacja", url = "https://wiki.company.com/login")
    @TmsLink("TC-001")
    @Issue("BUG-123")
    public void loginWithValidCredentials() {
        openLoginPage();
        enterCredentials("user", "pass");
        clickLoginButton();
        verifyLoginSuccess();
    }

    @Step("Otwieranie strony logowania")
    public void openLoginPage() {
        // otwarcie strony logowania
    }

    @Step("Wprowadzenie loginu i hasła: {username} / {password}")
    public void enterCredentials(String username, String password) {
        // wprowadzenie danych
    }

    @Step("Kliknięcie przycisku logowania")
    public void clickLoginButton() {
        // kliknięcie przycisku
    }

    @Step("Weryfikacja poprawnego logowania")
    @Attachment(value = "Screenshot po zalogowaniu", type = "image/png")
    public byte[] verifyLoginSuccess() {
        // pobranie screenshotu (np. Selenium)
        return ((TakesScreenshot) WebDriverManager.getDriver()).getScreenshotAs(OutputType.BYTES);
    }
}

```

---

<aside>
💡

Warto wiedzieć

| Adnotacja | Dodatkowe informacje |
| --- | --- |
| `@Step` | może przyjmować parametry `{}` i dynamicznie wstawiać wartości do raportu (np. `@Step("Logowanie użytkownika: {username}")`) |
| `@Attachment` | może zwracać `byte[]`, `String` lub `InputStream` |
| `@Severity` | poziomy: `BLOCKER`, `CRITICAL`, `NORMAL`, `MINOR`, `TRIVIAL` |
| `@Link`, `@Issue`, `@TmsLink` | pojawiają się jako **klikalne odnośniki** w raporcie HTML |
| `@Owner` | przydaje się przy analizie testów zespołowych (pokazuje, kto odpowiada za dany test) |
</aside>

---

| Adnotacja / Koncepcja | Opis |
| --- | --- |
| `@Step` w helperach | Można używać w klasach pomocniczych (np. Page Objectach), by raport pokazywał kroki testowe z wielu klas |
| Adnotacje dynamiczne | Można dodawać opisy, kroki i załączniki **programowo** w trakcie testu (np. `Allure.step("Krok dynamiczny")`) |
| Allure lifecycle API | `Allure.getLifecycle().addAttachment(...)` pozwala dodać załączniki spoza adnotacji |
| Parametry testów | TestNG + Allure automatycznie pokazują wartości parametrów testu (np. przy data-driven tests) |

## Integracja z Jenkins

1. Zainstaluj **Allure Plugin** w Jenkinsie.
2. W konfiguracji joba dodaj sekcję:
    - “Post-build action → Allure Report”
    - wskaż katalog `allure-results`.
3. Po uruchomieniu pipeline’a raport jest dostępny jako zakładka “Allure Report”.

---

## 🔹 Zalety i Wady

| ✅ Zalety | ❌ Wady |
| --- | --- |
| Bardzo czytelne raporty | Trzeba konfigurować generowanie wyników |
| Działa z wieloma frameworkami | Wymaga integracji w pipeline |
| Obsługuje załączniki i screenshoty | Brak natywnej obsługi w niektórych językach (np. Go) |
| Łatwo integruje się z CI/CD | Dla dużych projektów raport może być ciężki |

## Screenshoty

[https://allurereport.org/docs/steps/](https://allurereport.org/docs/steps/)

```java
public class TestListener  implements WebDriverListener {

    private void attach(WebDriver driver, String name) {
		    //„Nie rób screenshotów dla kroków, które zawierają findElement”.
        if (name != null && name.contains("findElement")) return;

        try {
	        //Zrobienie screenshotu za pomocą WebDrivera
            byte[] png = ((TakesScreenshot) driver).getScreenshotAs(OutputType.BYTES);
            Allure.step(name, () ->
				            //Tworzy krok (step) w raporcie Allure o nazwie name
                    Allure.getLifecycle().addAttachment(
                    //Parametr	Znaczenie
										//"screenshot"	Nazwa załącznika widoczna w raporcie
										//"image/png"	Typ MIME (czyli format danych)
										//"png"	Rozszerzenie pliku, które Allure pokaże w UI
										//new ByteArrayInputStream(png)	Zawartość — screenshot przekazany w formie bajtów
                            "screenshot", "image/png", "png", new ByteArrayInputStream(png)
                    ));
        } catch (Exception ignored) {}
    }

    private String safeLocator(WebElement e) {
        try { return e.toString(); } catch (Exception ex) { return "<element>"; }
    }

    @Override public void beforeClick(WebElement el) {
        attach(((WrapsDriver) el).getWrappedDriver(), "beforeClick: " + safeLocator(el));
    }
    @Override public void afterClick(WebElement el) {
        attach(((WrapsDriver) el).getWrappedDriver(), "afterClick: " + safeLocator(el));
    }

    @Override public void beforeSendKeys(WebElement el, CharSequence... keys) {
        attach(((WrapsDriver) el).getWrappedDriver(), "beforeSendKeys: " + safeLocator(el));
    }
    @Override public void beforeGet(WebDriver d, String url) { attach(d, "beforeGet: " + url); }

    // (opcjonalnie) screen przy dowolnym wyjątku z eventów
    @Override
    public void onError(Object target, Method method, Object[] args, InvocationTargetException e) {
        WebDriver d = (target instanceof WebDriver) ? (WebDriver) target
                : (target instanceof WebElement) ? ((WrapsDriver) target).getWrappedDriver()
                : null;
        if (d != null) attach(d, "ERROR in: " + method.getName());
    }
}
```

Aby wyświetlać raporty lokalnie, dodajemy do build plugin Allure-Maven. 

```xml
<build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>3.2.5</version>
                <configuration>
                    <suiteXmlFiles>
                        <suiteXmlFile>src/test/java/Selenium/BoniGarcia/BGtestng.xml</suiteXmlFile>
                    </suiteXmlFiles>
                    <systemPropertyVariables>
                    //Ten parametr wskazać folder de którego są zapisywane rezultaty
                        <allure.results.directory>
                            ${project.build.directory}/allure-results
                        </allure.results.directory>
                    </systemPropertyVariables>
                    <properties>
                        <property>
                            <name>listener</name>
                            <value>io.qameta.allure.testng.AllureTestNg</value>
                        </property>
                    </properties>
                </configuration>
            </plugin>
            <plugin>
                <groupId>io.qameta.allure</groupId>
                <artifactId>allure-maven</artifactId>
                <version>${allure.maven}</version>
                <configuration>
                    <resultsDirectory>${project.build.directory}/allure-results</resultsDirectory>
                </configuration>

            </plugin>
        </plugins>
    </build>
```
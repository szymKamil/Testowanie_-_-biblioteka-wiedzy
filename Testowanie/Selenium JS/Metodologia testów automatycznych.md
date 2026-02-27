# Metodologia testów automatycznych

# **Page Object Model (POM)**

## Definicja

**Page Object Model (POM)** to wzorzec projektowy w automatyzacji testów, w którym **oddziela się logikę interakcji z interfejsem użytkownika (UI)** od logiki samych testów.

W praktyce:

- Tworzymy **klasy stron (Page Classes)** reprezentujące strony lub komponenty aplikacji.
- Każda taka klasa **modeluje wygląd i zachowanie** fragmentu systemu zgodnie z paradygmatem obiektowym (**Page Objects**).
- Testy używają tych klas zamiast bezpośrednio korzystać z API Selenium.
- POM korzysta ze wzorców Java takich jak OOP (Object Oriented Programming), Encapsulation (Enkapsulacja), Inheritance (dziedziczenie), Abstraction i Polimorphism.
- Testy odwołują się do klas zawierające elementy strony (zmienne i metody), a te dopiero odwołują się do stron aplikacji.

---

## Cele POM

- **Czytelność** – testy są pisane w języku biznesowym, a nie w czystym Selenium.
- **Łatwość konserwacji** – zmiana w UI wymaga modyfikacji tylko w jednym miejscu.
- **Ponowne użycie kodu** – metody stron mogą być wykorzystywane w wielu testach.
- **Rozdział odpowiedzialności** – testy odpowiadają za scenariusze i asercje, a klasy stron za interakcje z UI.

---

## Przykładowa struktura projektu z POM

```
src
 ├── test
 │    └── java
 │         ├── tests           ← logika testów (scenariusze)
 │         └── pages           ← klasy stron (Page Objects)
 └── main
      └── java
           └── utils           ← np. WebDriver manager, konfiguracje

```

---

## Klasy stron (Page Classes)

- **Zawierają**:
    - Lokatory elementów (`By` lub `@FindBy`)
    - Metody wykonujące akcje na stronie (`login()`, `searchProduct()`)
    - Opcjonalnie metody sprawdzające stan strony (`isLoaded()`)
- **Testy nigdy** nie powinny bezpośrednio używać `driver.findElement(...)` – korzystają wyłącznie z metod klas stron.

---

## Przykład

**LoginPage.java**

```java
public class LoginPage {
    WebDriver driver;
    By usernameInput = By.id("username");
    By passwordInput = By.id("password");
    By loginButton = By.id("login");

    public LoginPage(WebDriver driver) {
        this.driver = driver;
    }

    public void enterUsername(String username) {
        driver.findElement(usernameInput).sendKeys(username);
    }

    public void enterPassword(String password) {
        driver.findElement(passwordInput).sendKeys(password);
    }

    public void clickLogin() {
        driver.findElement(loginButton).click();
    }
}

```

**LoginTest.java**

```java
@Test
public void loginTest() {
    LoginPage loginPage = new LoginPage(driver);
    driver.get("https://example.com/login");
    loginPage.enterUsername("admin");
    loginPage.enterPassword("1234");
    loginPage.clickLogin();
    assertTrue(driver.findElement(By.id("welcomeMessage")).isDisplayed());
}

```

---

## Rozszerzenia POM

### **1. Base Page**

- Klasa bazowa dla wszystkich stron.
- Zawiera wspólną logikę: czekanie (`WebDriverWait`), metody `click()` / `type()`, obsługę alertów itp.

To klasa będąca rodzicem. Może ona być zadeklarowana jako abstrakcyjna lub możemy używać relacji rodzic/dziecko (dziedziczenie). 

| Relacja / Element | Opis | Krótki przykład |
| --- | --- | --- |
| **Abstrakcja** (`abstract class`) | Base Page może być abstrakcyjna – nie tworzymy jej bezpośrednio, tylko przez klasy potomne. Może zawierać metody abstrakcyjne (do implementacji w dzieciach) oraz zwykłe metody wspólne. | `abstract class BasePage { abstract boolean isLoaded(); }` |
| **Konstruktor** | W Base Page konstruktor przyjmuje WebDriver (i opcjonalnie inne obiekty), żeby wszystkie strony miały dostęp do niego. | `BasePage(WebDriver driver) { this.driver = driver; }` |
| **Dziedziczenie** (`extends`) | Klasa strony dziedziczy metody z Base Page. | `class LoginPage extends BasePage {}` |
| **Implementacja interfejsu** (`implements`) | Wymusza określone metody w klasach stron. | `class LoginPage extends BasePage implements Page {}` |
| **Kompozycja** (`has-a`) | Base Page tworzy w sobie obiekty pomocnicze. | `WaitUtils wait = new WaitUtils(driver);` |
| **Agregacja** | Base Page dostaje obiekty pomocnicze z zewnątrz. | `BasePage(driver, waitUtils)` |
| **Delegacja** | Base Page przekazuje zadania innym klasom. | `actions.click(locator);` |
| **Interfejs funkcyjny / lambda** | Akcja przekazywana jako lambda. | `doAction(() -> click(btn));` |

### **2. DSL (Domain Specific Language)**

- API testowe w języku biznesowym:

```java
loginPage
    .enterUsername("admin")
    .enterPassword("1234")
    .clickLogin();
```

**Czym jest DSL w POM**

W kontekście testów automatycznych **DSL** oznacza:

> Tworzenie metod w klasach stron w taki sposób, aby testy wyglądały jak scenariusze biznesowe, a nie niskopoziomowe wywołania Selenium.
> 

Czyli zamiast:

```java
driver.findElement(By.id("username")).sendKeys("admin");
driver.findElement(By.id("password")).sendKeys("1234");
driver.findElement(By.id("login")).click();
```

Mamy:

```java
loginPage
    .typeUsername("admin")
    .typePassword("1234")
    .submitLogin();
```

…lub bardziej „biznesowo”:

```java
loginPage.loginAs("admin", "1234");
```

---

## 🏗 Przykład: DSL w POM

### **BasePage.java**

```java
public class BasePage<T extends BasePage<T>> {
    protected WebDriver driver;
    protected WebDriverWait wait;

    public BasePage(WebDriver driver) {
        this.driver = driver;
        this.wait = new WebDriverWait(driver, Duration.ofSeconds(10));
    }

    protected WebElement find(By locator) {
        return wait.until(ExpectedConditions.visibilityOfElementLocated(locator));
    }

    protected void click(By locator) {
        find(locator).click();
    }

    protected void type(By locator, String text) {
        find(locator).clear();
        find(locator).sendKeys(text);
    }
}
```

---

### **LoginPage.java**

```java
public class LoginPage extends BasePage<LoginPage> {

    private final By usernameField = By.id("username");
    private final By passwordField = By.id("password");
    private final By loginButton = By.id("login");

    public LoginPage(WebDriver driver) {
        super(driver);
    }

    public LoginPage typeUsername(String username) {
        type(usernameField, username);
        return this; // zwraca obiekt strony -> pozwala na chaining
    }

    public LoginPage typePassword(String password) {
        type(passwordField, password);
        return this;
    }

    public DashboardPage submitLogin() {
        click(loginButton);
        return new DashboardPage(driver); // przejście na inną stronę
    }

    // skrót do pełnego logowania w jednym kroku
    public DashboardPage loginAs(String username, String password) {
        return typeUsername(username)
               .typePassword(password)
               .submitLogin();
    }
}

```

---

### **DashboardPage.java**

```java
public class DashboardPage extends BasePage<DashboardPage> {
    private final By welcomeMsg = By.id("welcomeMessage");

    public DashboardPage(WebDriver driver) {
        super(driver);
    }

    public boolean isWelcomeMessageVisible() {
        return find(welcomeMsg).isDisplayed();
    }
}
```

---

## 📄 Test z użyciem DSL

**Podejście klasyczne (bez DSL)**:

```java
@Test
public void loginTestClassic() {
    driver.get("https://example.com/login");
    driver.findElement(By.id("username")).sendKeys("admin");
    driver.findElement(By.id("password")).sendKeys("1234");
    driver.findElement(By.id("login")).click();
    assertTrue(driver.findElement(By.id("welcomeMessage")).isDisplayed());
}

```

**Podejście DSL – czytelne, biznesowe**:

```java
@Test
public void loginTestDSL() {
    LoginPage loginPage = new LoginPage(driver);

    boolean isWelcomeVisible = loginPage
        .typeUsername("admin")
        .typePassword("1234")
        .submitLogin()
        .isWelcomeMessageVisible();

    assertTrue(isWelcomeVisible);
}

```

**Jeszcze bardziej „biznesowo”**:

```java
java
KopiujEdytuj
@Test
public void loginTestBusinessStyle() {
    boolean isWelcomeVisible = new LoginPage(driver)
        .loginAs("admin", "1234")
        .isWelcomeMessageVisible();

    assertTrue(isWelcomeVisible);
}

```

---

## 🎯 Zalety DSL w POM

- **Czytelność** – testy wyglądają jak scenariusze biznesowe.
- **Łatwe modyfikacje** – jeśli zmieni się sposób logowania, wystarczy poprawić jedną metodę (`loginAs`).
- **Chaining** – zwracanie `this` lub innego obiektu strony pozwala łączyć metody w jedną linię.
- **Eliminacja powtarzalności** – skrótowe metody (np. `loginAs`) mogą opakowywać kilka prostych kroków.

---

## 💡 Dodatkowe pomysły na DSL

Możesz rozbudować DSL tak, aby:

- **Używać fluent API z warunkami**:

```java
java
KopiujEdytuj
dashboardPage
    .openUserMenu()
    .selectOption("Logout")
    .confirmAction();

```

- **Dodawać asercje bezpośrednio w metodach** (np. `assertWelcomeMessageIsVisible()`), jeśli chcesz testów w pełni „samokontrolujących się”.
- **Łączyć POM z Allure Steps** – wtedy DSL staje się też raportowalny.

### **3. Page Factory**

- Mechanizm w Selenium do inicjalizacji elementów z `@FindBy`.
- Można użyć **@CacheLookup** w aplikacjach statycznych.

Użycie fabryki stron (ang. Page Factory) to zaawansowana technika w metodologii Page Object Model (POM), która może sprawić, że kod będzie bardziej czytelny, modularny i łatwiejszy w utrzymaniu. Fabryka stron pozwala na centralizację tworzenia instancji klas stron, co eliminuje konieczność ręcznego wywoływania konstruktorów w testach oraz zapewnia spójność w inicjalizacji obiektów. Poniżej wyjaśnię, jak zbudować fabrykę stron w kontekście twojego kodu oraz jak zaimplementować mechanizm w rodzaju getPage(MainPage.class).Czym jest fabryka stron?Fabryka stron to klasa lub zestaw metod odpowiedzialnych za tworzenie instancji klas stron (np. MainPage) w sposób scentralizowany. Może to być:

- Dedykowana klasa PageFactory (nie mylić z wbudowanym w Selenium PageFactory).
- Zbiór statycznych metod w klasie bazowej (np. AbstractPage).
- Mechanizm oparty na refleksji, który dynamicznie tworzy obiekty na podstawie klasy strony (np. MainPage.class).

W twoim przypadku pytanie dotyczy metody w stylu getPage(MainPage.class), co sugeruje podejście oparte na refleksji lub generycznym mechanizmie tworzenia stron.Jak zbudować fabrykę stron?Pokażę dwa podejścia do implementacji fabryki stron: prostsze (statyczne metody w klasie bazowej) oraz bardziej zaawansowane (z użyciem refleksji do dynamicznego tworzenia stron). Skupię się na kontekście twojego kodu, gdzie klasa MainPage przyjmuje w konstruktorze WebDriver, WebDriverWait i Logger.1. Proste podejście: Statyczna metoda w klasie bazowejW tym podejściu dodajemy statyczną metodę do klasy bazowej AbstractPage, która tworzy instancje stron. To rozwiązanie jest proste i nie wymaga refleksji.Krok 1: Dodaj metodę fabryczną do AbstractPage
Zakładam, że masz klasę AbstractPage, po której dziedziczy MainPage. Zmodyfikuj ją, aby zawierała metodę fabryczną:

```java
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.support.ui.WebDriverWait;
import org.slf4j.Logger;

public abstract class AbstractPage {
    protected WebDriver driver;
    protected WebDriverWait wait;
    protected Logger log;

    public AbstractPage(WebDriver driver, WebDriverWait wait, Logger log) {
        this.driver = driver;
        this.wait = wait;
        this.log = log;
    }

    // Statyczna metoda fabryczna
    public static <T extends AbstractPage> T getPage(Class<T> pageClass, WebDriver driver, WebDriverWait wait, Logger log) {
        try {
            return pageClass.getConstructor(WebDriver.class, WebDriverWait.class, Logger.class)
                           .newInstance(driver, wait, log);
        } catch (Exception e) {
            throw new RuntimeException("Nie udało się utworzyć instancji strony: " + pageClass.getName(), e);
        }
    }
}
```

Krok 2: Użyj fabryki w teście
Zamiast ręcznie tworzyć instancję MainPage w teście, użyj metody getPage:java

```java
@Test
public void mainPageTestElementsVerification() {
    MainPage mainPage = AbstractPage.getPage(MainPage.class, driver, wait, log);
    driver.get(mainPage.boniGarciaMainURL);
    wait.until(ExpectedConditions.visibilityOfElementLocated(ap.mainHeader));
    // Reszta testu pozostaje bez zmian
    String currentUrl = driver.getCurrentUrl();
    assert currentUrl != null;
    if (currentUrl.contains("https://bonigarcia.dev/selenium-webdriver-java/")) {
        log.info("Adres URL jest poprawny.");
    } else {
        log.error("Niepoprawny adres URL: " + currentUrl);
    }
    // ...
}
```

Zalety tego podejścia:

- Centralizuje tworzenie instancji stron, co ułatwia zarządzanie zależnościami.
- Używa generyków, dzięki czemu metoda getPage może tworzyć instancje dowolnej klasy dziedziczącej po AbstractPage.
- Jest proste w implementacji i nie wymaga dodatkowej biblioteki.

Wady:

- Wymaga, aby wszystkie klasy stron miały konstruktor z dokładnie tymi samymi parametrami (WebDriver, WebDriverWait, Logger).
- Mniej elastyczne w przypadku stron z różnymi zależnościami.

2. Zaawansowane podejście: Dedykowana klasa fabryki stron. Jeśli chcesz bardziej elastyczną fabrykę, możesz stworzyć osobną klasę PageFactory, która zarządza tworzeniem stron i obsługuje różne typy stron.

Krok 1: Stwórz klasę PageFactoryjava

```java
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.support.ui.WebDriverWait;
import org.slf4j.Logger;

public class PageFactory {
    private final WebDriver driver;
    private final WebDriverWait wait;
    private final Logger log;

    public PageFactory(WebDriver driver, WebDriverWait wait, Logger log) {
        this.driver = driver;
        this.wait = wait;
        this.log = log;
    }

    public <T extends AbstractPage> T getPage(Class<T> pageClass) {
        try {
            return pageClass.getConstructor(WebDriver.class, WebDriverWait.class, Logger.class)
                           .newInstance(driver, wait, log);
        } catch (Exception e) {
            throw new RuntimeException("Nie udało się utworzyć instancji strony: " + pageClass.getName(), e);
        }
    }
}
```

Krok 2: Użyj fabryki w teście
Zainicjalizuj fabrykę w teście i użyj jej do tworzenia stron:java

```java
@Test
public void mainPageTestElementsVerification() {
    PageFactory factory = new PageFactory(driver, wait, log);
    MainPage mainPage = factory.getPage(MainPage.class);
    driver.get(mainPage.boniGarciaMainURL);
    wait.until(ExpectedConditions.visibilityOfElementLocated(ap.mainHeader));
    // Reszta testu pozostaje bez zmian
    String currentUrl = driver.getCurrentUrl();
    assert currentUrl != null;
    if (currentUrl.contains("https://bonigarcia.dev/selenium-webdriver-java/")) {
        log.info("Adres URL jest poprawny.");
    } else {
        log.error("Niepoprawny adres URL: " + currentUrl);
    }
    // ...
}
```

Zalety tego podejścia:

- Lepsza enkapsulacja – fabryka jest oddzielnym komponentem, który można rozszerzać o dodatkowe funkcjonalności (np. cachowanie instancji stron).
- Możliwość dodania logiki specyficznej dla różnych typów stron w metodzie getPage.
- Łatwiejsze zarządzanie zależnościami, jeśli w przyszłości strony będą wymagały różnych parametrów.

Wady:

- Wprowadza dodatkowy obiekt (PageFactory), co może zwiększyć złożoność kodu w prostych projektach.
- Nadal wymaga, aby klasy stron miały spójny konstruktor.

3. Alternatywa: Selenium PageFactory (wbudowany mechanizm)Selenium oferuje wbudowaną klasę org.openqa.selenium.support.PageFactory, która automatycznie inicjalizuje elementy strony oznaczone adnotacjami @FindBy. Można ją połączyć z własną fabryką stron.Krok 1: Zmodyfikuj klasę MainPage
Użyj adnotacji @FindBy do lokalizatorów i inicjalizuj elementy za pomocą PageFactory.initElements:java

```java
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.support.FindBy;
import org.openqa.selenium.support.PageFactory;
import org.openqa.selenium.support.ui.WebDriverWait;
import org.slf4j.Logger;

public class MainPage extends AbstractPage {
    public static final String boniGarciaMainURL = "https://bonigarcia.dev/selenium-webdriver-java/";

    @FindBy(css = "div.col-md-4, py-2")
    private List<WebElement> containers;

    @FindBy(css = "p.lead")
    private WebElement lead;

    @FindBy(css = "span.text-muted")
    private WebElement copySpan;

    public MainPage(WebDriver driver, WebDriverWait wait, Logger log) {
        super(driver, wait, log);
        PageFactory.initElements(driver, this); // Inicjalizacja elementów
    }

    public int getContainersCount() {
        return containers.size();
    }

    public boolean isLeadDisplayed() {
        return wait.until(ExpectedConditions.visibilityOf(lead)).isDisplayed();
    }

    public String getLeadText() {
        return wait.until(ExpectedConditions.visibilityOf(lead)).getText();
    }

    public String getCopyrightText() {
        return wait.until(ExpectedConditions.visibilityOf(copySpan)).getText();
    }

    public WebElement getBtn(String btnName) {
        By btn = By.xpath("//a[@class='btn btn-outline-primary mb-2' and text()='" + btnName + "']");
        return wait.until(ExpectedConditions.visibilityOfElementLocated(btn));
    }

    public void goToSubPage(String btnName) {
        WebElement btn = getBtn(btnName);
        wait.until(ExpectedConditions.elementToBeClickable(btn)).click();
    }
}
```

Krok 2: Użyj fabryki w teście
Możesz nadal używać metody getPage z pierwszego lub drugiego podejścia, ale elementy strony są teraz automatycznie inicjalizowane przez PageFactory.initElements.java

```java
@Test
public void mainPageTestElementsVerification() {
    MainPage mainPage = AbstractPage.getPage(MainPage.class, driver, wait, log);
    driver.get(mainPage.boniGarciaMainURL);
    wait.until(ExpectedConditions.visibilityOfElementLocated(ap.mainHeader));
    String currentUrl = driver.getCurrentUrl();
    assert currentUrl != null;
    if (currentUrl.contains("https://bonigarcia.dev/selenium-webdriver-java/")) {
        log.info("Adres URL jest poprawny.");
    } else {
        log.error("Niepoprawny adres URL: " + currentUrl);
    }
    assertThat(mainPage.isLeadDisplayed()).isTrue();
    assertThat(mainPage.getLeadText()).isEqualTo(ap.leadText);
    assertThat(mainPage.getCopyrightText()).contains(ap.copyrights);
    assertThat(mainPage.getContainersCount()).isEqualTo(6);
}
```

Zalety Selenium PageFactory:

- Automatyczna inicjalizacja elementów strony za pomocą adnotacji @FindBy.
- Czytelniejszy kod, ponieważ lokalizatory są zdefiniowane bezpośrednio przy polach.
- Możliwość użycia lazy initialization (elementy są lokalizowane dopiero przy pierwszym użyciu).

Wady:

- Mniejsza elastyczność w porównaniu do ręcznego zarządzania lokalizatorami z WebDriverWait.
- Wymaga dodatkowej konfiguracji, jeśli chcesz używać niestandardowych mechanizmów oczekiwania.

RekomendacjaDla twojego przypadku polecam pierwsze podejście (statyczna metoda getPage w AbstractPage), ponieważ:

- Jest proste i nie wymaga dodatkowej klasy.
- Współgra z twoim obecnym kodem, który już używa WebDriver, WebDriverWait i Logger.
- Pozwala na łatwe rozszerzenie w-European Space Agency (ESA) w przyszłości.

Jeśli chcesz bardziej zaawansowane rozwiązanie, rozważ połączenie z Selenium PageFactory (trzecie podejście), aby uprościć zarządzanie elementami strony. Dedykowana klasa PageFactory (drugie podejście) jest dobrym wyborem, jeśli planujesz bardziej złożoną logikę tworzenia stron w przyszłości.PodsumowanieFabryka stron w stylu getPage(MainPage.class) to świetny sposób na centralizację tworzenia stron i zwiększenie czytelności kodu. Najprostsza implementacja to statyczna metoda w klasie AbstractPage, ale możesz też użyć dedykowanej klasy PageFactory lub wbudowanego mechanizmu Selenium. Wybór zależy od złożoności projektu i preferencji zespołu

---

## 💡 Dobre praktyki i wskazówki

1. **Metody w klasach stron powinny być proste** – wykonują pojedyncze akcje (np. kliknięcie, wpisanie tekstu), a nie całe scenariusze.
2. **Stosuj modularne Page Objects** – dla komponentów (np. menu, koszyk) w dużych projektach, nie tylko dla całych stron.
3. **Externalizacja lokatorów** – trzymanie lokatorów w plikach `.properties` lub `.json` ułatwia konserwację.
4. **Automatyzacja generowania lokatorów** – np. za pomocą narzędzi typu Selenium IDE, Katalon Recorder.
5. **Single Responsibility Principle** – każda klasa strony odpowiada za jeden fragment UI.
6. **Unikaj `Thread.sleep()`** – używaj **explicit waits** (`WebDriverWait`).
7. **Testy muszą być niezależne** – nie bazuj na stanie pozostawionym przez inne testy.

---

## ⚠️ Ograniczenia POM

- **Nieopłacalny dla bardzo prostych aplikacji** – narzut struktury większy niż korzyści.
- **Ryzyko „tight coupling”** – przy złym podziale klas stron można doprowadzić do nadmiernych zależności, utrudniających refaktoryzację.
- **Zmiany w UI wciąż wymagają pracy** – choć mniej, to jednak nadal trzeba aktualizować lokatory.

---

## 🔄 Alternatywy i rozszerzenia

- **Screen Objects** – rozszerzenie POM dla aplikacji mobilnych lub interfejsów opartych na ekranach.
- **Component Objects** – modelowanie nie całych stron, a modułów/komponentów (np. okno logowania w modalu, koszyk w nagłówku).
- **Loadable Components** – POM + mechanizm weryfikujący, czy strona jest w pełni załadowana.

---

## 🚀 Podsumowanie

POM to podstawowy wzorzec w automatyzacji testów Selenium, który:

- Ułatwia utrzymanie kodu
- Poprawia czytelność testów
- Pozwala unikać powtarzalnego kodu

Jego skuteczność rośnie wraz ze złożonością aplikacji, szczególnie w połączeniu z dobrymi praktykami: prostymi metodami, modularnością, externalizacją lokatorów i używaniem **Base Page** oraz **Page Factory**.

Alternatywne Podejścia:

1. Podejście oparte na wzorcu ScreenplayOpis: Wzorzec Screenplay (czasami nazywany "Journey Pattern") koncentruje się na modelowaniu testów w sposób zorientowany na użytkownika i jego interakcje z systemem. Zamiast klasycznych stron (Page Objects) i testów, używa się abstrakcji takich jak Actors, Tasks, Actions, Questions i Abilities.Jak to działa?

- Actors: Reprezentują użytkownika lub system wykonujący akcje. Każdy aktor ma zdolności (np. zdolność do korzystania z przeglądarki).
- Abilities: Definiują, co aktor potrafi (np. sterowanie WebDriverem).
- Tasks: Składają się z sekwencji akcji (np. "zaloguj się", "wypełnij formularz").
- Actions: Pojedyncze, atomowe interakcje z UI (np. kliknięcie przycisku, wpisanie tekstu).
- Questions: Służą do weryfikacji stanu aplikacji (np. "czy użytkownik jest zalogowany?").

Zalety:

- Lepsza czytelność i modularność kodu.
- Skupienie na intencji użytkownika, a nie na szczegółach implementacji.
- Łatwiejsza refaktoryzacja i ponowne użycie kodu.
- Naturalna izolacja kontekstu testu (nie wymaga ThreadLocal, bo każdy aktor ma swój kontekst).

Wady:

- Większa krzywa uczenia się.
- Może być nadmiarowy dla prostych testów.

Przykład implementacji:
Zamiast Page Factory i klas testowych:java

```java
public class LoginTask implements Task {
    private String username;
    private String password;

    public LoginTask(String username, String password) {
        this.username = username;
        this.password = password;
    }

    @Override
    public <T extends Actor> void performAs(T actor) {
        actor.attemptsTo(
            Enter.theValue(username).into(LoginPage.USERNAME_FIELD),
            Enter.theValue(password).into(LoginPage.PASSWORD_FIELD),
            Click.on(LoginPage.LOGIN_BUTTON)
        );
    }
}

public class LoginTest {
    Actor user = Actor.named("User").whoCan(BrowseTheWeb.with(driver));

    @Test
    public void shouldLoginSuccessfully() {
        user.attemptsTo(LoginTask.withCredentials("user", "pass"));
        user.should(seeThat(LoginSuccessfulQuestion.isDisplayed()));
    }
}
```

W tym podejściu można użyć bibliotek takich jak Serenity BDD lub samodzielnie zaimplementować wzorzec.

---

2. Podejście oparte na Dependency Injection (DI)Opis: Zamiast używać ThreadLocal do zarządzania driverami, można wykorzystać frameworki do wstrzykiwania zależności, takie jak Spring, Guice lub PicoContainer, aby zarządzać cyklem życia driverów i innych komponentów testowych.Jak to działa?

- Driver i inne zasoby są definiowane jako beany w kontekście DI.
- Testy są pisane jako klasy lub metody, które otrzymują driver i inne obiekty poprzez wstrzykiwanie.
- Kontekst testu (np. instancja WebDrivera) jest zarządzany przez framework DI, co eliminuje potrzebę ThreadLocal.

Zalety:

- Lepsza separacja odpowiedzialności.
- Możliwość łatwego zarządzania zależnościami (np. różne konfiguracje przeglądarek).
- Redukcja boilerplate’u w zarządzaniu driverami.
- Naturalna obsługa równoległości dzięki scope’om (np. prototype w Springu).

Wady:

- Wprowadza dodatkową złożoność (konfiguracja DI).
- Wymaga znajomości frameworku DI.

Przykład implementacji (Spring):java

```java
@Configuration
public class TestConfig {
    @Bean
    @Scope("prototype")
    public WebDriver webDriver() {
        return new ChromeDriver();
    }
}

@Component
public class LoginPage {
    private final WebDriver driver;

    @Autowired
    public LoginPage(WebDriver driver) {
        this.driver = driver;
    }

    public void login(String username, String password) {
        driver.findElement(By.id("username")).sendKeys(username);
        driver.findElement(By.id("password")).sendKeys(password);
        driver.findElement(By.id("login")).click();
    }
}

@SpringBootTest
public class LoginTest {
    @Autowired
    private LoginPage loginPage;

    @Test
    public void shouldLoginSuccessfully() {
        loginPage.login("user", "pass");
        // assertions
    }
}
```

---

3. Podejście oparte na Fluent Interface (API łańcuchowe)Opis: Zamiast Page Factory i klasycznych Page Objects, można stworzyć API testowe oparte na płynnym interfejsie, które pozwala na budowanie testów w sposób bardziej zwięzły i czytelny. Elementy UI i akcje są modelowane jako łańcuchowe metody.Jak to działa?

- Zamiast oddzielnych klas Page Object, tworzysz interfejsy lub klasy, które zwracają this dla każdej metody, umożliwiając łańcuchowe wywoływanie.
- Driver jest przekazywany jako parametr lub zarządzany w kontekście testu (np. przez klasę kontekstową).

Zalety:

- Czytelny, zwięzły kod testowy.
- Mniejsza ilość klas i boilerplate’u.
- Łatwo rozszerzalne.

Wady:

- Może być trudniejsze do debugowania przy złożonych scenariuszach.
- Mniejsza modularność w porównaniu do Page Objects.

Przykład implementacji:java

```java
public class LoginPage {
    private WebDriver driver;

    public LoginPage(WebDriver driver) {
        this.driver = driver;
    }

    public LoginPage enterUsername(String username) {
        driver.findElement(By.id("username")).sendKeys(username);
        return this;
    }

    public LoginPage enterPassword(String password) {
        driver.findElement(By.id("password")).sendKeys(password);
        return this;
    }

    public LoginPage submit() {
        driver.findElement(By.id("login")).click();
        return this;
    }
}

public class LoginTest {
    private WebDriver driver = new ChromeDriver();
    private LoginPage loginPage = new LoginPage(driver);

    @Test
    public void shouldLoginSuccessfully() {
        loginPage.enterUsername("user")
                 .enterPassword("pass")
                 .submit();
        // assertions
    }
}
```

---

4. Podejście kontekstowe z klasami zarządzającymi stanemOpis: Zamiast ThreadLocal i klas bazowych, można stworzyć klasę kontekstową, która przechowuje stan testu (np. WebDriver, konfiguracje, dane testowe) i jest przekazywana między komponentami testowymi.Jak to działa?

- Tworzysz klasę TestContext, która przechowuje wszystkie zasoby (np. WebDriver, logger, konfiguracje).
- Każda klasa testowa lub komponent otrzymuje TestContext jako parametr.
- Testy są pisane w sposób, który korzysta z kontekstu zamiast globalnych zmiennych czy ThreadLocal.

Zalety:

- Pełna kontrola nad stanem testu.
- Łatwe zarządzanie zależnościami bez frameworków DI.
- Dobra izolacja testów bez konieczności używania ThreadLocal.

Wady:

- Wymaga ręcznego zarządzania kontekstem.
- Może prowadzić do bardziej rozwlekłego kodu.

Przykład implementacji:java

```java
public class TestContext {
    private WebDriver driver;
    private Map<String, Object> testData;

    public TestContext() {
        this.driver = new ChromeDriver();
        this.testData = new HashMap<>();
    }

    public WebDriver getDriver() {
        return driver;
    }

    public void setData(String key, Object value) {
        testData.put(key, value);
    }

    public Object getData(String key) {
        return testData.get(key);
    }
}

public class LoginPage {
    private TestContext context;

    public LoginPage(TestContext context) {
        this.context = context;
    }

    public void login(String username, String password) {
        context.getDriver().findElement(By.id("username")).sendKeys(username);
        context.getDriver().findElement(By.id("password")).sendKeys(password);
        context.getDriver().findElement(By.id("login")).click();
    }
}

public class LoginTest {
    @Test
    public void shouldLoginSuccessfully() {
        TestContext context = new TestContext();
        LoginPage loginPage = new LoginPage(context);
        loginPage.login("user", "pass");
        // assertions
    }
}
```

---

5. Podejście oparte na wzorcu BuilderOpis: Wzorzec Builder można zastosować do tworzenia testów, w których scenariusze testowe są budowane w sposób deklaratywny. Zamiast klasycznych Page Objects, używasz buildera do definiowania akcji i weryfikacji.Jak to działa?

- Tworzysz klasę TestBuilder, która pozwala na definiowanie kroków testu w sposób łańcuchowy.
- Builder zarządza driverem i stanem testu, a testy są budowane w sposób deklaratywny.

Zalety:

- Bardzo czytelne testy.
- Łatwo rozszerzalne o nowe akcje.
- Eliminuje potrzebę ThreadLocal poprzez lokalne zarządzanie driverem.

Wady:

- Może być zbyt abstrakcyjne dla prostych testów.
- Wymaga starannego zaprojektowania buildera.

Przykład implementacji:java

```java
public class TestBuilder {
    private WebDriver driver;

    public TestBuilder() {
        this.driver = new ChromeDriver();
    }

    public TestBuilder openPage(String url) {
        driver.get(url);
        return this;
    }

    public TestBuilder login(String username, String password) {
        driver.findElement(By.id("username")).sendKeys(username);
        driver.findElement(By.id("password")).sendKeys(password);
        driver.findElement(By.id("login")).click();
        return this;
    }

    public TestBuilder verifyLoginSuccess() {
        assertTrue(driver.findElement(By.id("welcome")).isDisplayed());
        return this;
    }
}

public class LoginTest {
    @Test
    public void shouldLoginSuccessfully() {
        new TestBuilder()
            .openPage("http://example.com")
            .login("user", "pass")
            .verifyLoginSuccess();
    }
}
```

---

PodsumowanieKażde z tych podejść ma swoje mocne i słabe strony, a wybór zależy od potrzeb projektu:

- Screenplay jest idealny dla dużych, złożonych projektów, gdzie czytelność i modularność są kluczowe.
- Dependency Injection sprawdzi się w projektach, gdzie już używasz Springa lub podobnych frameworków.
- Fluent Interface jest dobry dla prostszych testów, gdzie chcesz zminimalizować boilerplate.
- Kontekstowe podejście to elastyczna alternatywa dla ThreadLocal, szczególnie w mniejszych projektach.
- Builder jest świetny do tworzenia deklaratywnych testów, które są łatwe do czytania i rozszerzania.
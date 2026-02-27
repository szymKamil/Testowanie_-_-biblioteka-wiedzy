# Problemy techniczne Selenium

- Notatka dotycząca problemów z równoległym uruchomieniem testów
    
    ---
    
    1. Wprowadzenie do zarządzania wątkamiW automatyzacji testów z TestNG w trybie równoległym (parallel="methods", parallel="tests" lub parallel="classes") każdy test lub metoda testowa może działać w oddzielnym wątku. Aby uniknąć konfliktów (np. współdzielenia instancji WebDriver lub stron POM między wątkami), należy używać mechanizmu ThreadLocal. Kluczowe elementy do zarządzania to:
    
    - WebDriver: Każda metoda testowa/wątek musi mieć własną instancję WebDriver.
    - WebDriverWait: Każda instancja WebDriver potrzebuje własnego WebDriverWait.
    - Strony POM: Strony współdzielone między metodami testowymi wymagają ThreadLocal, jeśli są inicjalizowane w klasie bazowej; strony lokalne w metodach testowych nie potrzebują ThreadLocal.
    
    Problemy takie jak StaleElementReferenceException (z Twojego przypadku) czy NullPointerException (z wcześniejszego kodu) wynikają z:
    
    - Współdzielenia WebDriver lub stron POM między wątkami bez ThreadLocal.
    - Niepoprawnego zarządzania cyklem życia elementów strony (np. nieaktualne referencje do DOM).
    
    ---
    
    2. Driver Factory: Tworzenie instancji WebDriverDriver Factory to klasa odpowiedzialna za tworzenie instancji WebDriver w sposób bezpieczny wątkowo. Używa ThreadLocal, aby każdy wątek miał własną instancję WebDriver.java
    
    ```java
    package Base;
    
    import io.github.bonigarcia.wdm.WebDriverManager;
    import org.openqa.selenium.WebDriver;
    import org.openqa.selenium.chrome.ChromeDriver;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    
    public class DriverFactory {
        private static final Logger log = LoggerFactory.getLogger(DriverFactory.class);
        private static final ThreadLocal<WebDriver> driverThread = new ThreadLocal<>();
        private static final ThreadLocal<String> browserThread = new ThreadLocal<>();
    
        public static void initDriver(String browser) {
            log.info("Inicjalizacja WebDriver dla wątku: {}", Thread.currentThread().getId());
            if (driverThread.get() != null) {
                log.warn("WebDriver już zainicjalizowany dla wątku: {}", Thread.currentThread().getId());
                return;
            }
            browserThread.set(browser);
            WebDriver driver;
            switch (browser.toLowerCase()) {
                case "chrome":
                    WebDriverManager.chromedriver().setup();
                    driver = new ChromeDriver();
                    break;
                // Dodaj inne przeglądarki, np. Firefox, Edge
                default:
                    throw new IllegalArgumentException("Nieobsługiwana przeglądarka: " + browser);
            }
            driver.manage().window().maximize();
            driverThread.set(driver);
            log.info("Driver uruchomiony pomyślnie dla wątku: {}", Thread.currentThread().getId());
        }
    
        public static WebDriver getDriver() {
            WebDriver driver = driverThread.get();
            if (driver == null) {
                throw new IllegalStateException("WebDriver nie zainicjalizowany dla wątku: " + Thread.currentThread().getId());
            }
            return driver;
        }
    
        public static void quitDriver() {
            WebDriver driver = driverThread.get();
            if (driver != null) {
                log.info("Zamykanie WebDriver dla wątku: {}", Thread.currentThread().getId());
                try {
                    driver.quit();
                } finally {
                    driverThread.remove();
                    browserThread.remove();
                }
            }
        }
    }
    ```
    
    Kluczowe elementy:
    
    - ThreadLocal: driverThread przechowuje instancję WebDriver dla każdego wątku.
    - Inicjalizacja: initDriver tworzy nową instancję WebDriver tylko, jeśli jeszcze nie istnieje.
    - Pobieranie: getDriver zwraca instancję specyficzną dla wątku.
    - Czyszczenie: quitDriver zamyka przeglądarkę i usuwa instancję z ThreadLocal.
    - Logowanie: Używa SLF4J do śledzenia inicjalizacji i zamykania.
    
    Uwagi:
    
    - Możesz rozszerzyć initDriver o inne przeglądarki (np. Firefox, Edge) lub opcje (np. tryb headless).
    - Unikaj statycznych pól WebDriver bez ThreadLocal, bo prowadzą do współdzielenia między wątkami.
    
    ---
    
    3. BaseTest: Zarządzanie cyklem życia testówKlasa bazowa BaseTest konfiguruje środowisko testowe, inicjalizuje WebDriver i WebDriverWait oraz opcjonalnie współdzielone strony POM.java
    
    ```java
    package Base;
    
    import org.openqa.selenium.support.ui.WebDriverWait;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.testng.annotations.AfterMethod;
    import org.testng.annotations.BeforeMethod;
    
    import java.time.Duration;
    
    public abstract class BaseTest {
        private static final Logger log = LoggerFactory.getLogger(BaseTest.class);
        private static final ThreadLocal<WebDriverWait> waitThread = new ThreadLocal<>();
        // Opcjonalnie: ThreadLocal dla współdzielonej strony startowej
        // private static final ThreadLocal<MainPage> mainPageThread = new ThreadLocal<>();
    
        @BeforeMethod
        public void setUp() {
            log.info("Inicjalizacja środowiska dla wątku: {}", Thread.currentThread().getId());
            DriverFactory.initDriver("chrome"); // Możesz użyć parametru TestNG
            WebDriverWait wait = new WebDriverWait(DriverFactory.getDriver(), Duration.ofSeconds(30));
            waitThread.set(wait);
            // Opcjonalnie: Inicjalizacja współdzielonej strony
            // mainPageThread.set(new MainPage(DriverFactory.getDriver(), waitThread.get()));
        }
    
        @AfterMethod
        public void tearDown() {
            log.info("Czyszczenie środowiska dla wątku: {}", Thread.currentThread().getId());
            waitThread.remove();
            // mainPageThread.remove();
            DriverFactory.quitDriver();
        }
    
        protected WebDriverWait getWait() {
            WebDriverWait wait = waitThread.get();
            if (wait == null) {
                throw new IllegalStateException("WebDriverWait nie zainicjalizowany dla wątku: " + Thread.currentThread().getId());
            }
            return wait;
        }
    
        // Opcjonalnie: Getter dla współdzielonej strony
        /*
        protected MainPage getMainPage() {
            MainPage mainPage = mainPageThread.get();
            if (mainPage == null) {
                throw new IllegalStateException("MainPage nie zainicjalizowany dla wątku: " + Thread.currentThread().getId());
            }
            return mainPage;
        }
        */
    }
    ```
    
    Kluczowe elementy:
    
    - Inicjalizacja: @BeforeMethod wywołuje DriverFactory.initDriver i tworzy WebDriverWait dla każdego wątku.
    - ThreadLocal: waitThread przechowuje instancję WebDriverWait specyficzną dla wątku.
    - Czyszczenie: @AfterMethod zamyka WebDriver i usuwa instancje z ThreadLocal.
    - Opcjonalna strona współdzielona: Możesz dodać ThreadLocal dla strony startowej (np. MainPage), jeśli jest używana w wielu testach (odkomentuj kod, jeśli potrzebny).
    
    Uwagi:
    
    - Unikaj inicjalizacji stron POM w BaseTest, jeśli są używane tylko lokalnie w metodach testowych.
    - Upewnij się, że @BeforeMethod i @AfterMethod są wywoływane dla każdego testu (sprawdź logi).
    
    ---
    
    4. Współdzielone strony POM i instancjeStrony POM (np. MainPage, WebForm) mogą być:
    
    - Lokalne: Tworzone w metodach testowych, nie wymagają ThreadLocal.
    - Współdzielone: Inicjalizowane w BaseTest i używane w wielu metodach testowych, wymagają ThreadLocal.
    
    Klasa abstrakcyjna AbstractPageDefiniuje wspólne zachowanie dla stron POM.java
    
    ```java
    package POM.WebTest.BoniGarcia.Pages;
    
    import org.openqa.selenium.By;
    import org.openqa.selenium.WebDriver;
    import org.openqa.selenium.support.PageFactory;
    import org.openqa.selenium.support.ui.ExpectedConditions;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    
    public abstract class AbstractPage {
        private static final Logger log = LoggerFactory.getLogger(AbstractPage.class);
        protected final WebDriver driver;
        protected final org.openqa.selenium.support.ui.WebDriverWait wait;
    
        public AbstractPage(WebDriver driver, org.openqa.selenium.support.ui.WebDriverWait wait) {
            if (driver == null || wait == null) {
                throw new IllegalArgumentException("WebDriver lub WebDriverWait nie może być null");
            }
            this.driver = driver;
            this.wait = wait;
            PageFactory.initElements(driver, this);
            log.info("Inicjalizacja {} dla wątku: {}", this.getClass().getSimpleName(), Thread.currentThread().getId());
        }
    
        public void verifyAbstractPage() {
            log.info("Weryfikacja AbstractPage w wątku: {}", Thread.currentThread().getId());
            verifyMainHeader();
        }
    
        protected void verifyMainHeader() {
            log.info("Weryfikacja nagłówka w wątku: {}", Thread.currentThread().getId());
            By headerLocator = By.xpath("//h1[@class='display-4']");
            try {
                wait.until(ExpectedConditions.presenceOfElementLocated(headerLocator));
                String headerText = driver.findElement(headerLocator).getText();
                log.info("Tekst nagłówka: {}", headerText);
            } catch (Exception e) {
                log.error("Błąd podczas weryfikacji nagłówka: {}", e.getMessage(), e);
                throw e;
            }
        }
    }
    ```
    
    Uwagi:
    
    - Używa ExpectedConditions do wyszukiwania elementów, aby zapobiec StaleElementReferenceException.
    - Elementy są wyszukiwane dynamicznie (np. By.xpath), zamiast przechowywać WebElement jako pola.
    
    Klasa MainPageStrona startowa, rozszerzająca AbstractPage.java
    
    ```java
    package POM.WebTest.BoniGarcia.Pages;
    
    import org.openqa.selenium.WebDriver;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    
    public class MainPage extends AbstractPage {
        private static final Logger log = LoggerFactory.getLogger(MainPage.class);
    
        public MainPage(WebDriver driver, org.openqa.selenium.support.ui.WebDriverWait wait) {
            super(driver, wait);
        }
    
        public void openMainPage() {
            log.info("Otwieranie MainPage w wątku: {}", Thread.currentThread().getId());
            long startTime = System.nanoTime();
            driver.get("https://example.com"); // Zastąp rzeczywistym URL
            long endTime = System.nanoTime();
            log.info("Czas potrzebny na załadowanie strony wynosi {} sekund", (endTime - startTime) / 1_000_000_000.0);
        }
    
        public void verifyHomePageContent() {
            log.info("Weryfikacja zawartości MainPage w wątku: {}", Thread.currentThread().getId());
            verifyAbstractPage();
        }
    
        public WebForm goToSubPage(String subPage) {
            log.info("Przechodzenie do podstrony {} w wątku: {}", subPage, Thread.currentThread().getId());
            // Przykład: driver.findElement(By.linkText(subPage)).click();
            return new WebForm(driver, wait);
        }
    }
    ```
    
    Uwagi:
    
    - Metoda goToSubPage zwraca nową instancję WebForm, co eliminuje potrzebę przechowywania jej w ThreadLocal.
    
    Klasa WebFormStrona formularza, rozszerzająca AbstractPage.java
    
    ```java
    package POM.WebTest.BoniGarcia.Pages;
    
    import org.openqa.selenium.WebDriver;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    
    public class WebForm extends AbstractPage {
        private static final Logger log = LoggerFactory.getLogger(WebForm.class);
    
        public WebForm(WebDriver driver, org.openqa.selenium.support.ui.WebDriverWait wait) {
            super(driver, wait);
        }
    
        public void fillTextInput() {
            log.info("Login został poprawnie wpisany.");
            // Implementacja
        }
    
        // Pozostałe metody (fillPasswordInput, submitForm itp.) jak w Twoim kodzie
    }
    ```
    
    Zarządzanie stronami POM:
    
    - Lokalne instancje: Jeśli strony (np. MainPage, WebForm) są tworzone w metodach testowych (jak w Twoim kodzie), nie potrzebują ThreadLocal, ponieważ są unikalne dla wątku i metody.
    - Współdzielone instancje: Jeśli strona (np. MainPage) jest używana w wielu metodach testowych, inicjalizuj ją w BaseTest z ThreadLocal:java
        
        ```java
        private static final ThreadLocal<MainPage> mainPageThread = new ThreadLocal<>();
        @BeforeMethod
        public void setUp() {
            DriverFactory.initDriver("chrome");
            waitThread.set(new WebDriverWait(DriverFactory.getDriver(), Duration.ofSeconds(30)));
            mainPageThread.set(new MainPage(DriverFactory.getDriver(), waitThread.get()));
        }
        ```
        
    
    ---
    
    5. Klasa testowaPrzykładowa klasa testowa z lokalnym instancjowaniem stron.java
    
    ```java
    package POM.WebTest.BoniGarcia.Tests;
    
    import Base.BaseTest;
    import Base.DriverFactory;
    import POM.WebTest.BoniGarcia.Pages.MainPage;
    import POM.WebTest.BoniGarcia.Pages.WebForm;
    import org.testng.annotations.Parameters;
    import org.testng.annotations.Test;
    
    import java.time.LocalDate;
    
    public class MainTest extends BaseTest {
    
        @Test(priority = 1, groups = {"interface", "regression"})
        public void mainPageTestElementsVerification() {
            MainPage mainPage = new MainPage(DriverFactory.getDriver(), getWait());
            mainPage.openMainPage();
            mainPage.verifyHomePageContent();
        }
    
        @Parameters("path")
        @Test(priority = 1, groups = {"functional", "regression"})
        public void webFormTest(@Optional("D:\\Programowanie\\Nauka\\SeleniumTestAutomation\\SeleniumTestAutomation\\src\\main\\resources\\f-vat_2011.pdf") String path) {
            MainPage mainPage = new MainPage(DriverFactory.getDriver(), getWait());
            mainPage.openMainPage();
            WebForm webForm = mainPage.goToSubPage("Web form");
            webForm.verifyAbstractPage();
            webForm.fillTextInput();
            // Pozostałe akcje jak w Twoim kodzie
        }
    }
    ```
    
    Uwagi:
    
    - Używa DriverFactory.getDriver() i getWait() zamiast pól klasy.
    - Strony POM są instancjonowane lokalnie, więc nie potrzebują ThreadLocal.
    
    ---
    
    6. Plik XML TestNGKonfiguracja dla równoległego testowania.xml
    
    ```xml
    <!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
    <suite name="ParallelTestSuite" parallel="methods" thread-count="2">
        <test name="Packed 1">
            <classes>
                <class name="POM.WebTest.BoniGarcia.Tests.MainTest"/>
            </classes>
        </test>
    </suite>
    ```
    
    Uwagi:
    
    - parallel="methods" thread-count="2" uruchamia metody testowe w dwóch wątkach, każda z własną instancją WebDriver.
    
    ---
    
    7. Najlepsze praktyki
    
    1. Używaj ThreadLocal dla współdzielonych zasobów:
        - Zawsze przechowuj WebDriver i WebDriverWait w ThreadLocal w DriverFactory lub BaseTest.
        - Używaj ThreadLocal dla stron POM tylko, jeśli są współdzielone między metodami testowymi (np. strona startowa inicjalizowana w @BeforeMethod).
    2. Unikaj statycznych pól bez ThreadLocal:
        - Statyczne pola WebDriver lub stron POM (np. static WebDriver driver) powodują współdzielenie między wątkami, co prowadzi do błędów jak StaleElementReferenceException.
    3. Zapobiegaj StaleElementReferenceException:
        - Wyszukuj elementy dynamicznie za pomocą By i ExpectedConditions zamiast przechowywać WebElement jako pola.
        - Przykład:java
            
            ```java
            By locator = By.xpath("//h1[@class='display-4']");
            wait.until(ExpectedConditions.presenceOfElementLocated(locator));
            driver.findElement(locator).getText();
            ```
            
    4. Centralizuj inicjalizację wspólnych stron:
        - Jeśli strona (np. MainPage) jest używana w wielu testach, inicjalizuj ją w BaseTest z ThreadLocal zamiast w każdej metodzie testowej.
    5. Loguj dla diagnostyki:
        - Dodaj logi w DriverFactory, BaseTest i klasach POM, aby śledzić inicjalizację i akcje w wątkach.
    6. Testuj różne konfiguracje:
        - Uruchamiaj testy z parallel="methods", parallel="tests" i parallel="classes", aby zweryfikować bezpieczeństwo wątkowe.
        - Sprawdź logi, aby upewnić się, że każdy wątek ma oddzielną instancję WebDriver.
    
    ---
    
    8. Debugowanie problemówJeśli pojawią się błędy (np. StaleElementReferenceException, tylko jedna przeglądarka):
    
    1. Sprawdź WebDriver:
        - Upewnij się, że driver pochodzi z DriverFactory.getDriver() i jest zarządzany przez ThreadLocal.
        - Sprawdź logi setUp – powinieneś zobaczyć inicjalizację dla każdego wątku (np. „Driver uruchomiony pomyślnie dla wątku: 35” i „…dla wątku: 36”).
    2. Zweryfikuj elementy strony:
        - Upewnij się, że metody takie jak verifyMainHeader ponownie wyszukują elementy, aby uniknąć StaleElementReferenceException.
    3. Sprawdź TestNG XML:
        - Upewnij się, że parallel="methods" thread-count="2" jest poprawnie ustawione.
    4. Logi i wersje:
        - Włącz szczegółowe logowanie (np. SLF4J).
        - Potwierdź zgodność wersji Chrome (np. 139.x) z ChromeDriver (139.0.7258.138).
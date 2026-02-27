# Problemy

- **JdkWebSocket initial request execution error**
    
    ## Notatka Techniczna: Rozwiązanie Problemu z Selenium Grid w Dockerze
    
    Rozwiązanie znalezione w:
    
    [https://github.com/SeleniumHQ/selenium/issues/11892](https://github.com/SeleniumHQ/selenium/issues/11892)
    
    ### Problem: Błąd połączenia WebSocket przy zdalnym sterowniku
    
    Podczas próby uruchomienia testów Selenium przy użyciu `RemoteWebDriver` z kodem Java na maszynie hosta (Windows) i serwerem Selenium Grid w kontenerze Docker, wystąpiły następujące objawy:
    
    1. **Początkowy objaw:** Przeglądarka Chrome uruchamiała się w kontenerze, ale natychmiast zawieszała się na pustej stronie z adresem `data;`. Test nie przechodził dalej.
    2. **Ostateczny błąd:** Po aktualizacji zależności lub zmianach w konfiguracji, problem manifestował się jako wyjątek w kodzie Java:
    
    ```
    Exception in thread "main" org.openqa.selenium.remote.http.ConnectionFailedException: JdkWebSocket initial request execution error
    
    ```
    
    Wskazywało to na nieudaną próbę nawiązania dwukierunkowej komunikacji WebSocket między testem a przeglądarką.
    
    ### Główna Przyczyna: Niezgodność Adresów Sieciowych
    
    Sedno problemu leżało w fundamentalnej niezgodności sieciowej między środowiskiem hosta (Twój komputer z Windowsem) a środowiskiem gościa (kontener Docker).
    
    1. **Żądanie wychodzące (działało):** Twój kod testowy wysyłał żądanie utworzenia sesji na adres `http://localhost:4444`. Docker poprawnie mapował ten port i przekazywał żądanie do kontenera Selenium.
    2. **Odpowiedź i komunikacja zwrotna (nie działało):** Serwer Selenium Grid, działający wewnątrz kontenera, otrzymywał żądanie. Następnie, aby ustanowić pełną komunikację, musiał "powiedzieć" Twojemu testowi, gdzie ma się połączyć zwrotnie (w celu obsługi WebSocket). Domyślnie, **ogłaszał on swój wewnętrzny adres IP z sieci Docker** (np. `http://172.17.0.2:4444`).
    3. **Konflikt:** Twój komputer z systemem Windows nie miał pojęcia, czym jest adres `172.17.0.2` i nie miał do niego dostępu. Próba nawiązania połączenia zwrotnego z tym adresem kończyła się niepowodzeniem (timeoutem), co generowało błąd `ConnectionFailedException`.
    
    ### Ostateczne Rozwiązanie: Jawne Ustawienie Publicznego Adresu URL Grida
    
    Rozwiązaniem jest jawne poinformowanie kontenera Selenium, pod jakim adresem jest on widoczny dla świata zewnętrznego. Robi się to za pomocą zmiennej środowiskowej `SE_NODE_GRID_URL` podczas uruchamiania kontenera.
    
    ### Krok 1: Znajdź adres IP swojej maszyny
    
    W wierszu poleceń (CMD/PowerShell) użyj komendy `ipconfig`, aby znaleźć swój adres IPv4 w sieci lokalnej (np. `192.168.1.105`).
    
    ### Krok 2: Uruchom kontener Docker z poprawną zmienną
    
    Użyj swojego adresu IP, aby ustawić zmienną `SE_NODE_GRID_URL`.
    
    **Finalna komenda `docker run`:**
    
    ```bash
    # Zastąp 192.168.1.105 swoim rzeczywistym adresem IP
    docker run -d -p 4444:4444 --name selenium-chrome --shm-size="2g" -e SE_NODE_GRID_URL=http://192.168.1.108:4444 selenium/standalone-chrome:latest
    
    ```
    
    Dla wersji z nodem (hubem), używamy:
    
    ```docker
    docker run -d --net selenium-grid `
      -e SE_EVENT_BUS_HOST=selenium-hub-env `
      --shm-size="2g" `
      -e SE_EVENT_BUS_PUBLISH_PORT=4442 `
      -e SE_EVENT_BUS_SUBSCRIBE_PORT=4443 `
      -e SE_NODE_GRID_URL="http://192.168.1.106:4444" `
      selenium/node-chrome:latest
    ```
    
    ### Użycie Nazwy Hosta (Zalecane)
    
    Jeśli maszyna jest w sieci z działającym **DNS** (lub jeśli nazwa hosta jest poprawnie rozpoznawana lokalnie, co często ma miejsce), użyj **nazwy hosta** zamiast zmiennego adresu IP.
    
    - **Krok 1: Znajdź Nazwę Hosta:**
    Użyj komendy `$hostname$` (CMD/PowerShell) lub `$uname -n$` (Linux), aby uzyskać nazwę Twojej maszyny (np. `$DESKTOP-SD08TEB$`).
    - **Krok 2: Użyj Nazwy Hosta w `$SE\_NODE\_GRID\_URL$`:**
    Zamiast sztywnego IP, użyj nazwy:Bash
        
        ```bash
        docker run -d -p 4444:4444 --name selenium-chrome --shm-size="2g" \
          -e SE_NODE_GRID_URL=http://<NAZWA_TWOJEGO_HOSTA>:4444 \
          selenium/standalone-chrome:latest
        ```
        
    
    ### Krok 3: Użyj tego samego adresu w kodzie Java
    
    Kod testowy musi łączyć się z tym samym, publicznie dostępnym adresem.
    
    **Finalny kod Java:**
    
    ```java
    import org.openqa.selenium.WebDriver;
    import org.openqa.selenium.chrome.ChromeOptions;
    import org.openqa.selenium.remote.RemoteWebDriver;
    import java.net.URL;
    
    public class DzialajacyTestSelenium {
        public static void main(String[] args) throws Exception {
            // Użyj tego samego adresu IP, co w komendzie docker run
            URL seleniumURL = new URL("<http://192.168.1.105:4444>");
    
            ChromeOptions chromeOptions = new ChromeOptions();
            // Ten argument jest wciąż zalecany dla kompatybilności
            chromeOptions.addArguments("--remote-allow-origins=*");
    
            WebDriver driver = RemoteWebDriver.builder()
                                              .oneOf(chromeOptions)
                                              .address(seleniumURL)
                                              .build();
    
            driver.get("<https://www.google.pl/>");
            System.out.println("Tytuł strony: " + driver.getTitle());
            driver.quit();
        }
    }
    
    ```
    
    ### Wyjaśnienie, dlaczego to rozwiązanie działa
    
    To podejście zapewnia **spójność adresacji** w całym procesie komunikacji:
    
    1. Twój test wysyła żądanie na `http://192.168.1.105:4444`. Jest to prawidłowy, osiągalny adres w Twojej sieci.
    2. Docker przekazuje to żądanie do kontenera na port `4444`.
    3. Serwer Selenium Grid w kontenerze, dzięki zmiennej `SE_NODE_GRID_URL`, wie, że jego publiczny adres to `http://192.168.1.105:4444`.
    4. W odpowiedzi zwrotnej do Twojego testu (w celu nawiązania połączenia WebSocket) **używa tego samego, poprawnego adresu**.
    5. Twój test otrzymuje adres, z którym może się bez problemu połączyć, co pozwala na ustanowienie stabilnej, dwukierunkowej komunikacji.
    
    W ten sposób eliminujemy konflikt adresów, który był pierwotną przyczyną wszystkich problemów.
    

##
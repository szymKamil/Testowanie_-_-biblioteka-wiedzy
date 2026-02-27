# BiDi

https://www.w3.org/TR/webdriver-bidi/

BiDi (Bidirectional) w Selenium to nowy protokół komunikacji między testami a przeglądarką, który zastępuje starszy protokół JSON Wire Protocol.

**Główne cechy WebDriver BiDi:**

BiDi umożliwia dwukierunkową komunikację - przeglądarka może aktywnie wysyłać informacje do testów (nie tylko odpowiadać na zapytania). Dzięki temu możesz nasłuchiwać zdarzeń w czasie rzeczywistym, takich jak żądania sieciowe, logi konsoli czy błędy JavaScript.

**Praktyczne zastosowania:**

- Przechwytywanie żądań i odpowiedzi HTTP w locie
- Monitorowanie logów konsoli i błędów JavaScript bez odpytywania
- Subskrypcja zdarzeń DOM
- Lepsza wydajność dzięki asynchronicznej komunikacji

# Selenium WebDriver BiDi: Obsługa Logów Konsoli

**Bidirectional (BiDi)** w Selenium umożliwia przechwytywanie zdarzeń z konsoli przeglądarki w czasie rzeczywistym.

**Główne Obiekty i Inicjalizacja**

Do obsługi logów BiDi wykorzystujemy asynchroniczne kontenery oraz listy bezpieczne wątkowo (`Thread-safe`).

- **CompletableFuture**: Służy do przechwytywania pojedynczych, konkretnych zdarzeń asynchronicznych.
- **CopyOnWriteArrayList**: Idealna do gromadzenia wszystkich logów w trakcie trwania testu bez ryzyka błędów współbieżności.

```java
if (driver instanceof HasBiDi hasBiDi) {
    biDi = hasBiDi.getBiDi();
}
```

**Metody Implementacji**

**1. Rejestracja Listenera (Nasłuchiwanie)**

Najpopularniejszy sposób to dodanie listenera do obiektu `BiDi`. Pozwala on rozróżnić standardowe wpisy konsoli od wyjątków JavaScript.

- **Console Log**: Standardowe `console.log`, `console.info`.
- **JS Log**: Błędy i wyjątki silnika JavaScript.

```java
hasBiDi.getBiDi().addListener(Log.entryAdded(), (LogEntry logEntry) -> {
    if (logEntry.getConsoleLogEntry().isPresent()) {
        // Obsługa standardowego logu
        consoleLogs.add(logEntry);
    } else if (logEntry.getJavascriptLogEntry().isPresent()) {
        // Obsługa błędów JS
        consoleJSEventList.add(logEntry);
    }
});
```

**Wykorzystanie LogInspector (Alternatywa)**

Uproszczony sposób nasłuchiwania przy użyciu dedykowanej klasy `LogInspector`.

> TIP
Pamiętaj o użyciu bloków try-with-resources, aby automatycznie zamknąć inspektora po zakończeniu operacji.
> 

```java
try (LogInspector logInspector = new LogInspector(driver)) {
     logInspector.onConsoleEntry(log -> System.out.println("Log: " + log.getText()));
}
```

**Przetwarzanie i Odczyt Danych**

**Przechwytywanie z limitem czasu (Timeout)**

Jeśli czekasz na konkretny log, warto użyć metody `.get()` z limitem czasu, aby test nie zawisł w nieskończoność.

```java
LogEntry znalezionyLog = logFuture.get(15, TimeUnit.SECONDS);
```

**Raportowanie na koniec testu**

Iteracja po zebranych obiektach pozwala na szczegółowe wypisanie błędów w logach testowych.

**Filtrowanie logów (Log Filtering)**

Zamiast zbierać wszystko, warto skupić się na błędach (LogLevel.ERROR). Dzięki temu raporty z testów są czytelniejsze.

```java
// Przykład filtrowania tylko błędów (LEVEL ERROR)
hasBiDi.getBiDi().addListener(Log.entryAdded(), (LogEntry logEntry) -> {
    logEntry.getConsoleLogEntry().ifPresent(console -> {
        if ("error".equalsIgnoreCase(console.getType())) {
            log.error("Wychwycono błąd krytyczny: {}", console.getText());
            consoleLogs.add(logEntry);
        }
    });
});
```

Przykład kawałka kodu:

```java
CompletableFuture<ConsoleEvent> consoleEvent = new CompletableFuture<>();
CompletableFuture<JavascriptException> jsEvent = new CompletableFuture<>();
CopyOnWriteArrayList<LogEntry> consoleLogs = new CopyOnWriteArrayList<>();
CopyOnWriteArrayList<ConsoleEvent> consoleEventList = new CopyOnWriteArrayList<>();
CopyOnWriteArrayList<LogEntry> consoleJSEventList = new CopyOnWriteArrayList<>();
CopyOnWriteArrayList<JavascriptException> javascriptExceptions = new CopyOnWriteArrayList<>();
(...)
	
	if (driver instanceof HasBiDi hasBiDi) {
			biDi = hasBiDi.getBiDi();
			
				// Uruchomienie nasłuchu 
				hasBiDi.getBiDi()
					.addListener(Log.entryAdded(), (LogEntry logEntry) -> {
						if (logEntry.getConsoleLogEntry()
								.isPresent()) {
							log.info("Wychwyciłem log w trakcie testu: {}", logEntry.getConsoleLogEntry()
									.get()
									.getText());
							consoleLogs.add(logEntry);
						} else if (logEntry.getJavascriptLogEntry()
								.isPresent()) {
							log.info("Wychwyciłem log JS w trakcie testu: {}", logEntry.getJavascriptLogEntry()
									.get()
									.getText());
							consoleJSEventList.add(logEntry);
						}
					});
					
		// Łapanie logów
					try {
			if (consoleEvent.isDone() & jsEvent.isDone()) {
				log.info("Złapałem console event o typie {} i treści {}, timesamp: {}, ", consoleEvent.get()
						.getType(), consoleEvent.get()
						.getMessages(), consoleEvent.get()
						.getTimestamp());
						
			// Łapanie logów dla CompletableFuture
			LogEntry znalezionyLog = logFuture.get(15, TimeUnit.SECONDS);
						----- 
						
	//	Odpalenie logów na koniec testu
						
						for (Object browserLog : logArray) {
				if (browserLog instanceof LogEntry entry) {
					log.info("Log z konsoli: {}, typ: {}, metoda: {}", entry.getConsoleLogEntry()
							.get()
							.getText(), entry.getConsoleLogEntry()
							.get()
							.getType(), entry.getConsoleLogEntry()
							.get()
							.getMethod());
				} else if (browserLog instanceof ConsoleEvent event) {
					log.info("W teście znaleziono następujące błędy z konsoli: {}, {}, {}", event.getMessages(), event.getType(), event.getTimestamp());
				} else if (browserLog instanceof JavascriptException exception) {
					log.info("Log z konsoli JS: {}", exception.getMessage());
					
					
	// Możliwe jest także nasłuchiwanie poprzez LogInspector 
		
		try (LogInspector logInspector = new LogInspector(driver)) {
			 logInspector.onConsoleEntry(log -> System.out.println("Log: " + log.getText()));
		 }
```

# TODO: Nasłuchiwanie sieci

```bash
BiDi bidi = ((HasBiDi) firefoxDriver).getBiDi();
			Network network = new Network(firefoxDriver);
			CompletableFuture<ResponseDetails> future = new CompletableFuture<>();
			firefoxDriver.manage()
					.deleteAllCookies();
			firefoxDriver.manage()
					.timeouts()
					.pageLoadTimeout(Duration.ofSeconds(3));

			network.addIntercept(new AddInterceptParameters(InterceptPhase.RESPONSE_STARTED));
			network.onBeforeRequestSent(event -> {
				System.out.println(">>> WYSŁANO: " + event.getRequest()
						.getUrl());
				System.out.println("    Metoda: " + event.getRequest()
						.getMethod());
			});

			network.onResponseCompleted(event -> {
				System.out.println("<<< ODEBRANO: " + event.getRequest()
						.getUrl());
				System.out.println("    Status: " + event.getRequest()
						.getHeaders());
				System.out.println("    Typ: " + event.getRequest()
						.getRequestId());
				System.out.println("    Metoda: " + event.getRequest()
						.getMethod());
			});
			
			Map<String, Object> interceptParams = new HashMap<>();
			interceptParams.put("phases", List.of("beforeRequestSent"));
			interceptParams.put("urlPatterns", Collections.emptyList()); // Nasłuchuj wszystkiego

// Używamy Command<String>, mapper czyta wynik jako Mapę i wyciąga ID
			String interceptId = bidi.send(new Command<>(
					"network.addIntercept",
					interceptParams,
					jsonInput -> {
						// Czytamy odpowiedź jako Mapę
						Map<String, Object> result = jsonInput.read(Map.class);
						// Wyciągamy klucz "intercept"
						return (String) result.get("intercept");
					}
			));

			System.out.println("Intercept ID: " + interceptId);

			// 2. NASŁUCHIWANIE (Używamy surowego stringa nazwy eventu)
			// Musimy rzutować event na właściwą klasę, Selenium robi to automatycznie jeśli użyjemy odpowiedniego mappera
			// Ale najprościej przy surowym podejściu użyć Network.getMapper() jeśli dostępny,
			// LUB po prostu użyć klasy Network do listenera (bo ona działa dobrze), a send() tylko do sterowania.

			// 1. SUBSKRYPCJA (Niezbędna)
		/*Map<String, Object> subscribeParams = new HashMap<>();
		subscribeParams.put("events", List.of("network.beforeRequestSent"));*/
			//bidi.send(new Command<>("session.subscribe", subscribeParams, json -> null));

			log.info("Test 1");
			// Definicja zdarzenia jako MAPA (działa zawsze)
			Event<Map<String, Object>> RAW_NETWORK_EVENT = new Event<>(
					"network.beforeRequestSent",
				/*jsonInput -> {
					log.info("Test 2");
					jsonInput.values().forEach(e -> logger.info("{}", e));
					return jsonInput;
				}*/
					(Map<String, Object> eventData) -> {
						// eventData to już gotowa mapa z danymi zdarzenia!
						log.info("Test 2 - Odebrano zdarzenie: {}", eventData);

						// Po prostu zwracamy to co dostaliśmy
						return eventData;
					}

			);

			bidi.addListener(RAW_NETWORK_EVENT, map -> {
				// Musimy ręcznie "kopać" w mapie
				// Struktura: { "request": { "url": "...", "requestId": "..." }, ... }

				Map<String, Object> requestMap = (Map<String, Object>) map.get("request");
				String url = (String) requestMap.get("url");
				String requestId = (String) requestMap.get("request");

				System.out.println("Złapano URL: " + url);
				System.out.println("Złapano request: " + requestId);

				new Thread(() -> {
					try {
						if (url.contains(".css")) {
							Map<String, Object> params = new HashMap<>();
							params.put("request", requestId);
							params.put("errorText", "unknown error");
							bidi.send(new Command<>("network.failRequest", params, json -> null));
						} else {
							Map<String, Object> params = new HashMap<>();
							params.put("request", requestId);
							bidi.send(new Command<>("network.continueRequest", params, json -> null));
						}
					} catch (Exception e) {
						e.printStackTrace();
					}
				}).start();
			});

			firefoxDriver.get("https://bonigarcia.dev/selenium-webdriver-java/");

			network.onBeforeRequestSent((BeforeRequestSent event) -> {
				RequestData req = event.getRequest();

			});
```

```java
WebDriver driver;
	WebDriverWait wait;
	Network network;
	List<String> interceptIds = new ArrayList<>();

	private final By searchInput = By.id("search-query");
	private final By searchButton = By.cssSelector("button[data-test='search-submit']");
	private final By searchContainer = By.cssSelector("div[data-test='search_completed']");

	public void setUp() {
		ChromeOptions options = new ChromeOptions();
		options.setCapability("webSocketUrl", true);
		driver = new ChromeDriver(options);
		wait = new WebDriverWait(driver, Duration.ofSeconds(15));
		network = new Network(driver);
	}

	void addIntercept(String host, String path, String term) {
		String encoded = encodeURIComponent(term);
		String search = "?q=" + encoded;

		UrlPattern pattern = new UrlPattern().protocol("https")
				.hostname(host)
				.pathname(path)
				.search(search);

		System.out.println("Przechwytujemy: " + pattern.toMap());

		String id = network.addIntercept(new AddInterceptParameters(InterceptPhase.BEFORE_REQUEST_SENT)
				.urlPattern(pattern));
		interceptIds.add(id);
	}

	private String encodeURIComponent(String term) {
		String enc = URLEncoder.encode(term, StandardCharsets.UTF_8);
		return enc.replace("+", "%20");
	}

	public void mockItemNotFound(String toolName) {
		final String emptyResponse = """
				 {
				 "current_page": 1,
				    "data": [ ],
				    "from": null,
				    "last_page": 1,
				    "per_page": 9,
				    "to": null,
				    "total": 0
				 }
				""";
		
		addIntercept(
				"api.practicesoftwaretesting.com",
				"/products/search",
				toolName);

		network.onBeforeRequestSent((BeforeRequestSent event) -> {
			RequestData req = event.getRequest();
			String url = req.getUrl();
			String method = req.getMethod();
			boolean blocked = event.isBlocked();
			System.out.println("Złapany event: " + url + " metoda? " + method + " zablokowany? " + blocked);

			if (!blocked) return;

			/*if (url.contains("hammer02.avif")) {
				network.failRequest(event.getRequest().getRequestId());
				System.out.println("[BLOCKED] " + url);
			} else {
				network.continueRequest(
						new ContinueRequestParameters(event.getRequest()
								.getRequestId())
				);
			}*/
			byte[] imageBytes = Base64.getDecoder().decode(emptyResponse);

			String base64 = Base64.getEncoder().encodeToString(imageBytes);

			if ("GET".equalsIgnoreCase(method)) {
				System.out.println("Mamy event do odpowiedzenia");
				ProvideResponseParameters resp = new ProvideResponseParameters(req.getRequestId()).statusCode(200)
						.reasonPhrase("OK")
						/*.headers(List.of(
								new Header("content-type",
										new BytesValue(BytesValue.Type.STRING, "image/avif")),
								new Header("cache-control",
										new BytesValue(BytesValue.Type.STRING, "max-age=14400")),
									new Header(
											"content-length",
											new BytesValue(
													BytesValue.Type.STRING,
													String.valueOf(imageBytes.length)
											)
									)
						))*/

						.headers(List.of(
								new Header("access-control-allow-origin",
										new BytesValue(BytesValue.Type.STRING, "*")),
								new Header("content-type",
										new BytesValue(BytesValue.Type.STRING, "application/json"))
						))
						.body(new BytesValue(BytesValue.Type.BASE64, base64));

				network.provideResponse(resp);
				System.out.println("[BLOCKED] " + method + " " + url);
			} else {
				System.out.println("Kontynuuję");
				network.continueRequest(new ContinueRequestParameters(req.getRequestId()));
			}
		});
	}

	public void performSearch(String term){
		WebElement input = wait.until(ExpectedConditions.elementToBeClickable(searchInput));
		input.click();
		input.sendKeys(term);
		driver.findElement(searchButton).click();

	}

	public void tearDown(){
		try {
			for (String id : interceptIds) network.removeIntercept(id);
			if (network != null) network.close();
		} finally {
			if (driver != null) driver.quit();
		}
	}

	public static void main(String[] args) {
		new MockApiTest().run();
	}

	private void run() {
		setUp();
		try {
			mockItemNotFound("hammer");
			driver.get("https://practicesoftwaretesting.com/");
			performSearch("hammer");
			String resultMsg = wait.until(d -> d.findElement(searchContainer).getText());
			assertThat("There are no products found.").isEqualTo(resultMsg);
		} finally {
			//tearDown();
		}
	}
```

Błędy:

org.openqa.selenium.remote.http.ConnectionFailedException: JdkWebSocket initial request execution error

- niezgodnośc drivera z przeglądarką
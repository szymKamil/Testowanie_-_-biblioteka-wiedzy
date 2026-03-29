# Playwright/TS

Darmowe smoke testy dla całego design systemu bez pisania ani jednej linii kodu testowego.

<aside>
💡

Playwright posiada bardzo rozbudowaną, szczegółową dokumentację na oficjalnej stronie projektu. Nie ma sensu jej przepisywanie, jednak istnieje wiele różnych rozwiązań i podejść, które wychodzą poza dokumentację. Dlatego te treści będą bardziej obecne w tym dokumencie. Standardowe funkcjonalności ale i takie, które nie są używane “na co dzień” opisane w dokumentacji umieszczone będą tu tylko w formie ściągawki. 

</aside>

# Instalacja / aktualizacja

```jsx
# Zakładamy, że NPM zainstalowany

Playwright:
npm init playwright@latest

Update:
npm install -D @playwright/test@latest
npx playwright install --with-deps

Weryfikacja wersji:
npx playwright --version

Canary relase:
npm install -D @playwright/test@next
```

# Config.playwright.ts

https://playwright.dev/docs/test-configuration

Serce konfiguracji repozytorium. Przykład pliku:

```jsx
import { defineConfig, devices } from '@playwright/test';

export default defineConfig({
  // Konfiguracja katalogu z testami, wskazanie testów do uruchomienia, ignorowania. 
  // Możemy bardziej zawęzić to szczegółowo za pomocą projektów
  testDir: './e2e',
	testMatch: '*todo-tests/*.spec.ts',
	testIgnore: '*test-assets',

  //Tryb równoległego uruchomienia testów
  fullyParallel: true,

  // Fail the build on CI if you accidentally left test.only in the source code.
  forbidOnly: !!process.env.CI,

  // Retry on CI only.
  retries: process.env.CI ? 2 : 0,

  // Opt out of parallel tests on CI.
  workers: process.env.CI ? 1 : undefined,

  // Reporter to use
  reporter: 'html',

  use: {
    // Base URL to use in actions like `await page.goto('/')`.
    baseURL: 'http://localhost:3000',

    // Collect trace when retrying the failed test.
    trace: 'on-first-retry',
    permissions: ['notifications'],
  },
  // Configure projects for major browsers.
  projects: [
    {
      name: 'chromium',
      use: { ...devices['Desktop Chrome'] },
      headless: !!process.env.CI, // true in CI, false locally
    },
  ],
  // Run your local dev server before starting the tests.
  webServer: {
    command: 'npm run start',
    url: 'http://localhost:3000',
    reuseExistingServer: !process.env.CI,
  },
});
```

Opcje:

| Opcja | Opis |
| --- | --- |
| `build` | Konfiguracja kroku budowania projektu przed uruchomieniem testów (np. kompilacja TypeScript, bundling). |
| `captureGitInfo` | Dołącza informacje o repozytorium Git (branch, commit) do metadanych raportu. |
| `expect` | Globalne ustawienia asercji — np. `timeout` dla `expect()`, `toMatchSnapshot` (progi podobieństwa obrazów). |
| `failOnFlakyTests` | Jeśli `true`, testy oznaczone jako flaky (niestabilne przy ponownych próbach) kończą run jako FAILED. |
| `forbidOnly` | Jeśli `true`, blokuje użycie `.only` w kodzie testów — przydatne na CI, by zapobiec przypadkowemu pominięciu testów. |
| `fullyParallel` | Uruchamia wszystkie testy (nawet z tego samego pliku) równolegle. Domyślnie pliki są równoległe, ale testy w pliku — sekwencyjne. |
| `globalSetup` | Ścieżka do modułu wykonywanego **raz przed całą suitą** (np. logowanie, seed bazy danych). |
| `globalTeardown` | Ścieżka do modułu wykonywanego **raz po całej suicie** (np. czyszczenie zasobów). |
| `globalTimeout` | Maksymalny czas (ms) na wykonanie całego testu run. Przekroczenie = błąd całego procesu. |
| `grep` | Filtr (RegExp lub string) — uruchamia tylko testy, których tytuł pasuje do wyrażenia. |
| `grepInvert` | Odwrócony filtr — pomija testy pasujące do wyrażenia (RegExp lub string). |
| `ignoreSnapshots` | Jeśli `true`, pomija asercje snapshotowe (`toMatchSnapshot`, `toHaveScreenshot`) bez błędu. |
| `maxFailures` | Zatrzymuje cały run po osiągnięciu podanej liczby nieudanych testów. `0` = bez limitu. |
| `metadata` | Dowolne dane (obiekt) dołączane do raportu HTML — np. wersja aplikacji, środowisko. |
| `name` | Nazwa projektu (wyświetlana w raportach). Przydatna przy wielu projektach w jednym configu. |
| `outputDir` | Katalog na artefakty testowe (screenshoty, wideo, trace). Domyślnie `test-results/`. |
| `preserveOutput` | Kiedy zachowywać artefakty: `always`, `never`, `failures-only`. Domyślnie `failures-only`. |
| `projects` | Tablica konfiguracji projektów (np. różne przeglądarki, viewporty, środowiska) — każdy projekt dziedziczy z globalnego configu. |
| `quiet` | Jeśli `true`, wycisza stdout podczas testów (mniej verbose output w terminalu). |
| `repeatEach` | Ile razy powtórzyć każdy test (liczba całkowita). Przydatne do wykrywania flakiness. |
| `reportSlowTests` | Konfiguruje próg i liczbę wolnych testów raportowanych jako ostrzeżenia (`{ max, threshold }`). |
| `reporter` | Reporter(y) wyników: `html`, `list`, `dot`, `json`, `junit` lub ścieżka do własnego. Może być tablica. |
| `respectGitIgnore` | Jeśli `true`, pliki z `.gitignore` są pomijane podczas szukania testów. |
| `retries` | Liczba ponownych prób dla nieudanego testu. `0` = brak retry. Na CI zwykle `2`. |
| `shard` | Dzieli testy na N fragmentów do równoległego CI: `{ total: N, current: i }`. |
| `snapshotPathTemplate` | Szablon ścieżki do plików snapshot, np. `{testDir}/__snapshots__/{testFilePath}/{arg}{ext}`. |
| `tag` | Tagi testów używane do filtrowania (powiązane z `grep` i opcją `--grep` w CLI). |
| `testDir` | Katalog główny z plikami testów. Domyślnie katalog z plikiem config. |
| `testIgnore` | Glob lub RegExp — wzorce plików/katalogów **wykluczonych** z wyszukiwania testów. |
| `testMatch` | Glob lub RegExp — wzorce plików **uwzględnianych** jako testy. Domyślnie `**/*.{spec,test}.{js,ts,...}`. |
| `timeout` | Maksymalny czas (ms) na wykonanie pojedynczego testu. Domyślnie `30000`. |
| `tsconfig` | Ścieżka do pliku `tsconfig.json` używanego przez Playwright do kompilacji testów. |
| `updateSnapshots` | Tryb aktualizacji snapshotów: `all` (zawsze), `none` (nigdy), `missing` (tylko brakujące), `changed` (zmienione). |
| `updateSourceMethod` | Sposób zapisu zmian do pliku źródłowego przy aktualizacji snapshotów: `patch` lub `overwrite`. |
| `use` | Globalne opcje dla wszystkich testów: `baseURL`, `browserName`, `headless`, `viewport`, `locale`, `storageState`, `trace`, `video`, itp. |
| `webServer` | Startuje lokalny serwer przed testami (`command`, `url`, `port`, `reuseExistingServer`). Można podać tablicę serwerów. |
| `workers` | Liczba równoległych workerów. Liczba całkowita lub procent CPU (np. `'50%'`). Domyślnie `50%` lokalne / `1` na CI. |

## Ściągawka opcji `use`

| Opcja | Opis |
| --- | --- |
| `acceptDownloads` | Jeśli `true`, automatycznie akceptuje pobieranie plików. Domyślnie `true`. |
| `actionTimeout` | Timeout (ms) dla pojedynczej akcji (`click`, `fill`, itp.). Domyślnie `0` (brak limitu). |
| `baseURL` | Bazowy URL dla nawigacji — pozwala używać relatywnych ścieżek, np. `page.goto('/login')`. |
| `browserName` | Przeglądarka do uruchomienia: `chromium`, `firefox`, `webkit`. |
| `bypassCSP` | Jeśli `true`, wyłącza Content Security Policy na stronie. Przydatne przy injektowaniu skryptów. |
| `channel` | Kanał przeglądarki Chrome/Edge, np. `chrome`, `chrome-beta`, `msedge`. |
| `clientCertificates` | Tablica certyfikatów klienta (mTLS) — `{ origin, certPath, keyPath, pfxPath, passphrase }`. |
| `colorScheme` | Emulacja preferencji kolorystycznych: `light`, `dark`, `no-preference`. |
| `connectOptions` | Opcje połączenia ze zdalną przeglądarką przez `browserType.connect()` (np. `wsEndpoint`). |
| `contextOptions` | Dodatkowe opcje przekazywane do `browser.newContext()` — nadpisuje/uzupełnia pozostałe ustawienia. |
| `deviceScaleFactor` | Współczynnik DPR (Device Pixel Ratio) — np. `2` symuluje ekran Retina. |
| `extraHTTPHeaders` | Obiekt z dodatkowymi nagłówkami HTTP dołączanymi do każdego żądania w kontekście. |
| `geolocation` | Emulowana lokalizacja GPS: `{ latitude, longitude, accuracy }`. |
| `hasTouch` | Jeśli `true`, emuluje urządzenie dotykowe (touch events). |
| `headless` | Jeśli `true`, przeglądarka działa bez UI. Domyślnie `true` na CI. |
| `httpCredentials` | Dane do HTTP Basic Auth: `{ username, password }`. |
| `ignoreHTTPSErrors` | Jeśli `true`, ignoruje błędy certyfikatu SSL/TLS. Przydatne na środowiskach dev. |
| `isMobile` | Jeśli `true`, emuluje widok mobilny (meta viewport, touch, user agent). |
| `javaScriptEnabled` | Jeśli `false`, wyłącza JavaScript na stronie. Domyślnie `true`. |
| `launchOptions` | Opcje przekazywane do `browserType.launch()` — np. `args`, `executablePath`, `slowMo`. |
| `locale` | Locale przeglądarki, np. `pl-PL`, `en-US`. Wpływa na `navigator.language` i formaty dat. |
| `navigationTimeout` | Timeout (ms) dla nawigacji (`goto`, `waitForURL`, itp.). Domyślnie `0` (brak limitu). |
| `offline` | Jeśli `true`, emuluje brak połączenia sieciowego. |
| `permissions` | Tablica uprawnień przeglądarki, np. `['geolocation', 'notifications', 'clipboard-read']`. |
| `proxy` | Konfiguracja proxy: `{ server, bypass, username, password }`. |
| `screenshot` | Kiedy robić screenshoty: `on`, `off`, `only-on-failure`. |
| `serviceWorkers` | Tryb obsługi Service Workerów: `allow` (domyślnie) lub `block`. |
| `storageState` | Ścieżka do pliku lub obiekt ze stanem sesji (cookies, localStorage) — np. do zachowania logowania. |
| `testIdAttribute` | Nazwa atrybutu HTML używanego przez `getByTestId()`. Domyślnie `data-testid`. |
| `timezoneId` | Emulowana strefa czasowa, np. `Europe/Warsaw`, `America/New_York`. |
| `trace` | Kiedy nagrywać trace: `on`, `off`, `retain-on-failure`, `on-first-retry`, `on-all-retries`. |
| `userAgent` | Niestandardowy User-Agent dla kontekstu przeglądarki. |
| `video` | Kiedy nagrywać wideo: `on`, `off`, `retain-on-failure`, `on-first-retry` + opcjonalny `size`. |
| `viewport` | Rozmiar okna przeglądarki: `{ width, height }`. `null` = brak stałego rozmiaru. |

## Dostępne wartości `permissions`

| Wartość | Opis |
| --- | --- |
| `geolocation` | Dostęp do lokalizacji GPS (`navigator.geolocation`). |
| `midi` | Dostęp do urządzeń MIDI (bez SysEx). |
| `midi-sysex` | Dostęp do urządzeń MIDI z obsługą wiadomości SysEx. |
| `notifications` | Wyświetlanie powiadomień systemowych (`Notification.requestPermission()`). |
| `push` | Odbieranie powiadomień push (Web Push API). |
| `camera` | Dostęp do kamery (`getUserMedia`). |
| `microphone` | Dostęp do mikrofonu (`getUserMedia`). |
| `background-sync` | Rejestrowanie zdarzeń Background Sync w Service Workerze. |
| `ambient-light-sensor` | Odczyt danych z czujnika oświetlenia otoczenia. |
| `accelerometer` | Dostęp do akcelerometru urządzenia. |
| `gyroscope` | Dostęp do żyroskopu urządzenia. |
| `magnetometer` | Dostęp do magnetometru (kompas). |
| `accessibility-events` | Nasłuchiwanie zdarzeń accessibility (AT). |
| `clipboard-read` | Odczyt zawartości schowka (`navigator.clipboard.readText()`). |
| `clipboard-write` | Zapis do schowka (`navigator.clipboard.writeText()`). |
| `payment-handler` | Obsługa płatności przez Payment Handler API. |
| `idle-detection` | Wykrywanie bezczynności użytkownika (Idle Detection API). |
| `storage-access` | Dostęp do cookies/storage w kontekście cross-site (Storage Access API). |
| `colorScheme` | Tryb wyświetlania nocnego, dziennego: `dark` // `light` |

---

# Shardy - dzielenie uruchomień

[Sharding | Playwright](https://playwright.dev/docs/test-sharding)

W potoku CI każdy fragment może działać jako oddzielne zadanie, wykorzystując zasoby sprzętowe dostępne w potoku CI, takie jak rdzenie procesora, co pozwala na szybsze przeprowadzanie testów.
Należy pamiętać, że Playwright może rozdzielać na części tylko te testy, które można uruchamiać równolegle. 

W `playwright.config.ts`  :

```jsx
import { defineConfig } from '@playwright/test';

export default defineConfig({
  shard: { total: 10, current: 3 },
});

// alternatywnie, podczas uruchomienia:

`npx playwright test --shard=3/10`

```

Sharding oznacza uruchomienie testów na różnych maszynach. Możemy podzielić testy np. 50/50 i uruchomić na serwerach A i B. Shardy można też konfigurować w `playwright.config.ts`.

![image.png](/Testowanie/Playwright%20TS/image.png)

## Blob

Wówczas każdy fragment testu ma swój własny raport z testów. Jeśli chcemy uzyskać zbiorczy raport zawierający wszystkie wyniki testów ze wszystkich fragmentów, trzeba je połączyć. Raporty Blob zawierają wszystkie szczegóły dotyczące przebiegu testu i mogą posłużyć później do wygenerowania dowolnego innego raportu. Ich głównym zadaniem jest ułatwienie łączenia raportów z testów rozdzielonych na fragmenty. Blob opisany jest niżej, w sekcji reporterów. 

Mergowania raportów z blobów: `npx playwright merge-reports --reporter html ./all-blob-reports`

# Attach

Do raportów możemy dodawać pliki utworzone podczas testu. Załączniki są widoczne w **HTML reporterze** jako klikalne linki/podglądy przy danym teście. Przykład:

```jsx
import { test } from '@playwright/test';
import AxeBuilder from '@axe-core/playwright';
import { AxeResults } from 'axe-core';

		test('example with attachment', async ({ page }, testInfo) => {
			await page.goto('https://emma11y.github.io/tester-a11y/cas-pratique-5');
			const accessibilityScanResults: AxeResults = await new AxeBuilder({
			page,
		}).analyze();
			await testInfo.attach('accessibility-scan-results', {
			body: JSON.stringify(accessibilityScanResults, null, 2),
			contentType: 'application/json',
		});
});

// body — dane w pamięci (string, Buffer)
await testInfo.attach('log', {
  body: 'jakiś tekst',
  contentType: 'text/plain',
});

// path — plik z dysku
await testInfo.attach('screenshot', {
  path: 'screenshots/result.png',
  contentType: 'image/png',
});
```

**`contentType` — kilka typowych wartości**

- `application/json`
- `text/plain`
- `image/png` / `image/jpeg`
- `video/webm`

Playwright sam dodaje załączniki gdy włączone jest `screenshot`, `video` lub `trace` w configu.

# Projekty

[TestProject | Playwright](https://playwright.dev/docs/api/class-testproject)

Projekt to logiczna grupa testów uruchamianych przy użyciu tej samej konfiguracji. Korzystamy z projektów, aby móc uruchamiać testy w różnych przeglądarkach i na różnych urządzeniach. Projekty konfiguruje się w pliku `playwright.config.ts`, a po skonfigurowaniu można uruchamiać testy we wszystkich projektach lub tylko w konkretnym projekcie. Projekty można również wykorzystać do uruchamiania tych samych testów w różnych konfiguracjach. Na przykład można uruchomić te same testy w stanie zalogowanym i wylogowanym.

```jsx
export default defineConfig({
  projects: [
    {
      name: 'chromium',
      use: { ...devices['Desktop Chrome'] },
    },

    {
      name: 'firefox',
      use: { ...devices['Desktop Firefox'] },
    },
		{
      name: 'Mobile Chrome',
      use: { ...devices['Pixel 5'] },
    },
    {
      name: 'Mobile Safari',
      use: { ...devices['iPhone 12'] },
    },

    /* Test against branded browsers. */
    {
      name: 'Microsoft Edge',
      use: {
        ...devices['Desktop Edge'],
        channel: 'msedge'
...
```

**Uruchamianie konkretnego projektu przez CLI**

```jsx
npx playwright test --project=chromium
npx playwright test --project=chromium --project=firefox
```

Definiowanie zależności pomiędzy projektami oraz wskazywanie katalogów poprzez `testMatch` i `testDir` :

```jsx
projects: [
  {
    name: 'setup',
    testMatch: /global.setup\.ts/,
  },
  {
    name: 'chromium',
    testDir: './tests/api',
    use: { ...devices['Desktop Chrome'] },
    dependencies: ['setup'],
  },
]
```

Warto zaznaczyć, że `use` w projekcie **nadpisuje** globalne `use` z configu — to częste źródło nieporozumień.

**Struktura katalogów projektu**

```jsx
project/
├── playwright.config.ts        # konfiguracja globalna
├── .auth/                      # pliki sesji (storageState) — w .gitignore
├── fixtures/                   # custom fixtures, auth, data providers
│   ├── index.ts                # eksport customowego `test`
│   ├── auth.fixture.ts         # logowanie, role użytkowników
│   └── data.fixture.ts         # seed danych, API clients
├── pages/                      # Page Object Model
│   ├── LoginPage.ts
│   ├── CheckoutPage.ts
│   └── index.ts                # barrel export
├── tests/                      # pliki testów
│   ├── auth/
│   │   └── login.spec.ts
│   └── checkout/
│       └── checkout.spec.ts
├── utils/                      # helpers, custom matchers, stałe
│   ├── matchers.ts
│   └── constants.ts
└── data/                       # dane testowe (JSON, CSV)
    └── users.json
```

---

**Co gdzie trafia**

| Co | Gdzie | Dlaczego nie bezpośrednio w teście |
| --- | --- | --- |
| Konfiguracja przeglądarki, baseURL, timeouty | `playwright.config.ts` | Jedna zmiana = efekt globalny |
| Login, auth setup, role użytkowników | `fixtures/` | Reużywalne między plikami i suitami |
| Locatory i akcje na stronie | `pages/` (POM) | Zmiana selektora = poprawka w jednym miejscu |
| Seed danych, API clients | `fixtures/` lub `data/` | Separacja logiki od danych |
| Custom matchers, helpery | `utils/` | Nie należą do żadnej strony ani testu |
| Sam przepływ testowy | `tests/` | Tylko AAA — zero setupu, zero locatorów |

---

**Zasada czystego testu**

Test powinien czytać się jak scenariusz biznesowy — bez locatorów, bez szczegółów implementacyjnych, bez setupu:

```jsx
// ❌ test który robi za dużo
test('checkout', async ({ page }) => {
  await page.goto('/login');
  await page.locator('#email').fill('user@test.com');
  await page.locator('#password').fill('secret');
  await page.locator('[type=submit]').click();
  await page.goto('/shop');
  await page.locator('.product').first().locator('button').click();
  await expect(page.locator('.cart-count')).toHaveText('1');
});

// ✅ test który opisuje przepływ
test('checkout', async ({ loggedInPage, shopPage, cartPage }) => {
  await shopPage.addFirstProductToCart();
  await expect(cartPage.itemCount).toHaveText('1');
});
```

---

**Warstwy projektu**
```
┌─────────────────────────────────┐
│           tests/                │  ← co testujemy (scenariusz)
├─────────────────────────────────┤
│      fixtures/ + pages/         │  ← jak testujemy (mechanika)
├─────────────────────────────────┤
│     utils/ + data/ + .auth/     │  ← czym testujemy (zasoby)
├─────────────────────────────────┤
│      playwright.config.ts       │  ← gdzie i w jakich warunkach
└─────────────────────────────────┘
```

---

**Pytania kontrolne przy dodawaniu nowego kodu**

- Czy ten kod pojawi się w więcej niż jednym teście? → `fixtures/` lub `pages/`
- Czy dotyczy konkretnej strony UI? → `pages/`
- Czy to logika setupu środowiska? → `fixtures/`
- Czy to helper niezwiązany ze stroną? → `utils/`
- Czy to dane wejściowe? → `data/`
- Czy to jednorazowe? → inline w teście

## **Organizacja plików testowych**

---

**Dwa podejścia**

|  | Jeden plik = jeden przepływ | Jeden plik = cały moduł |
| --- | --- | --- |
| Styl | Playwright-native | Selenium-legacy |
| Plik | `login-happy-path.spec.ts` | `login.spec.ts` (50+ testów) |
| Izolacja | Każdy test niezależny | Testy często zależne od kolejności |
| Równoległość | Naturalna | Problematyczna |
| Debugowanie | Łatwe — jeden cel | Trudne — który test co psuje |

---

**Podejście Playwright-native (zalecane)**

Każdy plik testowy = jeden scenariusz lub jedna funkcjonalność. Testy wewnątrz pliku są krótkie i niezależne:

```jsx
tests/
├── auth/
│   ├── login-standard-user.spec.ts
│   ├── login-locked-user.spec.ts
│   └── logout.spec.ts
├── checkout/
│   ├── add-to-cart.spec.ts
│   ├── checkout-flow.spec.ts
│   └── checkout-validation.spec.ts
└── navigation/
    └── menu.spec.ts
```

Zalety:

- Pliki uruchamiają się równolegle — każdy w osobnym workerze
- Łatwo znaleźć failing test po nazwie pliku
- Łatwo uruchomić tylko jeden obszar: `npx playwright test tests/checkout/`

---

**Podejście Selenium-legacy (unikaj)**

Jeden plik = cały moduł, masa testów wewnątrz, często zależnych od siebie:

```jsx
// login.spec.ts — 300 linii, 40 testów
test('login valid', ...);
test('login invalid email', ...);
test('login after logout', ...); // zależy od stanu z poprzedniego testu
test('login with remember me', ...);
// ...
```

Problemy:
- Testy zależne od kolejności wykonania — klasyczny anti-pattern
- Jeden failing test blokuje cały plik
- Trudna równoległość — testy w jednym pliku działają szeregowo
- Trudne debugowanie — kontekst się nakłada

---

**Zasada podziału w Playwright**
```
jeden plik spec = jeden obszar funkcjonalny
jeden test      = jeden scenariusz (jeden Act)
jeden describe  = grupowanie wariantów tego samego scenariusza
```

ts

```jsx
// checkout.spec.ts
test.describe('checkout z zalogowanym użytkownikiem', () => {
  test('dodaje produkt do koszyka', ...);
  test('usuwa produkt z koszyka', ...);
  test('przechodzi do płatności', ...);
});

// NIE: 'dodaje produkt, przechodzi do płatności i składa zamówienie'
// to są 3 osobne testy, nie jeden
```

---

**Wyjątek — testy seryjne (`serial`)**

Jeśli scenariusz jest z natury sekwencyjny (np. wieloetapowy checkout który trwa 5 minut i nie opłaca się resetować stanu), można użyć `test.describe.serial()`:

```jsx
test.describe.serial('wieloetapowy checkout', () => {
  test('krok 1 — dodaj produkt', ...);
  test('krok 2 — wpisz adres', ...);
  test('krok 3 — zapłać', ...);
});
```

<aside>
💡

`serial` to świadomy wyjątek, nie domyślny styl. Testy seryjne są kruche — jeden failing test blokuje resztę grupy.

</aside>

# Uruchomienie

Korzystamy z CLI lub ustawień w `config.playwright.ts`. Testy uruchamiamy przez CLI lub konfigurując opcje w `playwright.config.ts`. Ustawienia podane w CLI zawsze nadpisują ustawienia z pliku konfiguracyjnego.

```jsx
# konkretny plik, folder lub wzorzec nazwy
npx playwright test my.spec.ts
npx playwright test folder/
npx playwright test mobile           # częściowe dopasowanie nazwy pliku
npx playwright test "example.*spec"  # wyrażenie regularne

# po tytule testu lub describe
npx playwright test -g "login page"

# po tagach
npx playwright test --grep @fast                    # jeden tag
npx playwright test --grep "@fast|@slow"            # OR
npx playwright test --grep "(?=.*@fast)(?=.*@slow)" # AND
npx playwright test --grep-invert @fast             # wyklucz tag
npx playwright test --grep-invert "@fast|@slow"     # wyklucz wiele

# można łączyć grep i grep-invert
npx playwright test --grep @fast --grep-invert @slow

# liczba workerów — liczbowo lub procentowo (% dostępnych rdzeni CPU)
npx playwright test --workers=4
npx playwright test --workers=50%

# konkretny projekt
npx playwright test --project=firefox

# shardowanie
npx playwright test --shard=1/2

# liczba ponownych prób przy failurze
npx playwright test --retries=2

# limit czasu pojedynczego testu (ms)
npx playwright test --timeout=30000

# tylko testy które ostatnio nie przeszły
npx playwright test --last-failed

# tylko testy z plików zmienionych względem gita
npx playwright test --only-changed

# wylistuj testy bez uruchamiania
npx playwright test --list

# Tryb uruchomienia

npx playwright test --headed   # widoczna przeglądarka
npx playwright test --ui       # tryb interaktywny UI
npx playwright test --debug    # tryb debugowania (Playwright Inspector)
npx playwright test --trace on # nagrywanie trace

# Raportowanie 
npx playwright show-report
```

## Workers - fine tuning

**Workers — fine tuning**

Aby ręcznie dobrać optymalną liczbę workerów, można odczytać liczbę rdzeni CPU maszyny i na tej podstawie ustawić wartość w konfiguracji:

```jsx
import os from 'os';
import { defineConfig } from '@playwright/test';

const cpuCount = os.cpus().length;

export default defineConfig({
  workers: Math.max(1, cpuCount - 1),
});`
```

Playwright nie bierze pod uwagę dostępnej pamięci RAM — przy zbyt dużej liczbie workerów testy mogą zakończyć się błędem OOM (Out of Memory). W takim przypadku należy obniżyć liczbę workerów.

<aside>
💡

Ustawienie `workers: 99%` w praktyce oznacza "wszystkie rdzenie minus jeden" — jeden rdzeń jest zarezerwowany dla procesu koordynatora testów.

</aside>

---

**Benchmarking z hyperfine**

Optymalną wartość workerów dla danego projektu i maszyny można wyznaczyć empirycznie narzędziem [hyperfine](https://github.com/sharkdp/hyperfine), które porównuje czasy wykonania poleceń:

```jsx
# skanowanie wartości od 1 do 10
hyperfine --parameter-scan w 1 10 --runs 1 'npx playwright test --workers={w}'

# porównanie konkretnych wartości procentowych
hyperfine --parameter-list w 1,25%,50%,75%,99% --runs 1 \
'npx playwright test --repeat-each=5 --workers={w}'
```

# Trace viewer

Trace Viewer to jedno z najbardziej przydatnych narzędzi do debugowania w Playwright. Zapewnia szczegółowy obraz całego przebiegu testu, co ułatwia wykrywanie problemów pojawiających się w trybie bezinterfejsowym (headless) lub w środowisku CI/CD.

Konfiguracja

```jsx
// playwright.config.ts
import { defineConfig } from '@playwright/test';

export default defineConfig({
  use: {
    trace: 'on-first-retry',
  },
});
```

Dostępne wartości opcji `trace`:

| Wartość | Kiedy nagrywa |
| --- | --- |
| `on` | zawsze |
| `off` | nigdy |
| `on-first-retry` | tylko przy pierwszym ponowieniu |
| `on-all-retries` | przy każdym ponowieniu |
| `retain-on-failure` | zawsze, usuwa jeśli test przeszedł |

Można też włączyć trace jednorazowo przez CLI:

`npx playwright show-trace path\to\trace.zip`

**Otwieranie trace**

```jsx
# lokalnie
npx playwright show-trace path/to/trace.zip

# lub przez HTML report — ikona przy nieudanym teście
npx playwright show-report
```

Plik `.zip` można też wrzucić bez instalacji na:
```
https://trace.playwright.dev
```

**Co zawiera trace**

- **Oś czasu** — wizualna mapa całego przebiegu testu z miniaturami ekranu
- **Szczegóły akcji** — lokalizator, czasy i logi dla każdego kroku
- **Migawki DOM** — stan HTML/CSS przed i po każdej akcji, pomocne gdy element nie był widoczny lub klikalny
- **Logi konsoli** — komunikaty zarejestrowane w konsoli przeglądarki
- **Żądania sieciowe** — pełny log aktywności sieciowej, przydatny przy debugowaniu nieładujących się zasobów lub błędnych wywołań API
- **Kod źródłowy** — podświetlona linia skryptu testowego odpowiadająca danej akcji

# Budowa testu

**Hierarchia obiektów przeglądarki**

Testy i automatyzacja działają na stronie osadzonej w izolowanym kontekście przeglądarki. Hierarchia wygląda następująco:

- **Browser** — instancja przeglądarki (Chromium, Firefox, WebKit)
- **BrowserContext** — izolowana sesja, odpowiednik trybu incognito
- **Page** — pojedyncza karta w tej sesji

Bez fixture'ów trzeba by tworzyć przeglądarkę, kontekst i stronę ręcznie przy każdym teście. Playwright dostarcza je automatycznie jako wbudowane fixture'y (`browser`, `context`, `page`), dzięki czemu ta sama instancja przeglądarki może być współdzielona przez cały zestaw testów.

**Wzorzec Arrange-Act-Assert**

[Arrange-Act-Assert: A Pattern for Writing Good Tests](https://automationpanda.com/2020/07/07/arrange-act-assert-a-pattern-for-writing-good-tests/)

Czytelne testy organizuje się według wzorca **AAA**:

```jsx
test('should add item to cart', async ({ page }) => {
  // Arrange — przygotowanie środowiska
  await page.goto('/shop');
  await page.getByRole('button', { name: 'Login' }).click();

  // Act — wykonanie akcji powodującej zmianę stanu
  await page.getByRole('button', { name: 'Add to cart' }).click();

  // Assert — weryfikacja oczekiwanego stanu
  await expect(page.getByRole('status')).toHaveText('1 item in cart');
});
```

- **Arrange** — przygotowanie środowiska: nawigacja, logowanie, seed danych
- **Act** — jedna akcja powodująca zmianę stanu aplikacji
- **Assert** — weryfikacja czy stan aplikacji jest zgodny z oczekiwaniem

<aside>
💡

Dobrą praktyką jest utrzymanie sekcji **Act** jak najkrócej — najlepiej jedna akcja na test. Jeśli potrzeba wielu akcji, rozważ rozbicie testu lub przeniesienie setupu do `beforeEach`.

</aside>

Asercje w Playwright są inteligentne (auto-retry). To odróżnia Playwrighta od starszych narzędzi.

- `expect(locator).toBeVisible()` – czeka, aż element się pojawi.
- `expect(locator).toHaveText()` – czeka na tekst.

# Hooki

Hooki służą do wykonywania kodu przed lub po testach — typowo do setupu i teardownu środowiska testowego.

| Hook |  |
| --- | --- |
| `beforeAll` | Przed wszystkimi testami |
| `beforeEach` | Przed każdym testem |
| `afterEach` | Po każdym teście |
| `afterAll` | Po wszystkich testach |

```jsx
import { test, expect } from '@playwright/test';

test.describe('koszyk', () => {

  test.beforeAll(async ({ browser }) => {
    // jednorazowy setup — np. przygotowanie danych testowych
  });

  test.beforeEach(async ({ page }) => {
    // przed każdym testem — np. nawigacja do strony startowej
    await page.goto('/shop');
  });

  test.afterEach(async ({ page }) => {
    // po każdym teście — np. czyszczenie stanu
  });

  test.afterAll(async () => {
    // jednorazowy teardown — np. usunięcie danych testowych
  });

  test('dodaj produkt do koszyka', async ({ page }) => {
    // ...
  });
});
```

<aside>
💡

Hooki działają w zakresie bloku `describe`, w którym są zdefiniowane. Hook zdefiniowany poza `describe` ma zasięg globalny — wykonuje się dla wszystkich testów w pliku.

</aside>

<aside>
⚠️

`beforeAll` i `afterAll` współdzielą kontekst między testami — jeśli jeden test zmodyfikuje stan, może wpłynąć na kolejne. Z tego powodu preferuje się `beforeEach`/`afterEach`, które gwarantują izolację.

</aside>

# Adnotacje do testów/metadane

[Playwright Test | Playwright](https://playwright.dev/docs/api/class-test#methods)

**Flagi sterujące wykonaniem**

```jsx
// pomiń test (nie uruchomi się)
test.skip('nazwa', async ({ page }) => { ... });

// pomiń warunkowo
test.skip(browserName === 'firefox', 'Still working on it');

// oznacz jako oczekiwany fail — Playwright uruchomi i sprawdzi czy faktycznie pada
test.fail('nazwa', async ({ page }) => { ... });

// oznacz jako zepsuty — Playwright w ogóle nie uruchomi testu
test.fixme('nazwa', async ({ page }) => { ... });

// potraja domyślny timeout testu
test.slow('nazwa', async ({ page }) => { ... });

// uruchom tylko ten test
test.only('nazwa', async ({ page }) => { ... });
```

**Tagowanie**

```jsx
test('test login page', {
  tag: '@fast',
}, async ({ page }) => { ... });

// uruchamianie po tagu przez CLI
// npx playwright test --grep @fast
```

**Adnotacje / metadane**

```jsx
// pojedyncza adnotacja
test('test login page', {
  annotation: {
    type: 'issue',
    description: 'https://github.com/microsoft/playwright/issues/23180',
  },
}, async ({ page }) => { ... });

// wiele adnotacji
test('full report', {
  annotation: [
    { type: 'issue', description: 'https://github.com/microsoft/playwright/issues/23180' },
    { type: 'performance', description: 'very slow test!' },
    { type: '_hidden', description: 'niewidoczne w raporcie — prefix _' },
  ],
}, async ({ page }) => { ... });

// adnotacja dodana dynamicznie w trakcie testu
test.info().annotate({ type: 'note', description: 'coś się dzieje' });
```

<aside>
💡

Adnotacje z typem zaczynającym się od `_` są ukryte w HTML reporterze.

</aside>

**test.step — kroki w raporcie**

```jsx
test('checkout', async ({ page }) => {
  await test.step('przejdź do koszyka', async () => {
    await page.goto('/cart');
  });

  await test.step('złóż zamówienie', async () => {
    await page.getByRole('button', { name: 'Order' }).click();
  });

  await test.step.skip('pomiń ten krok', async () => { ... });
});
```

**test.info() — informacje o bieżącym teście**

```jsx
test.info().title           // nazwa testu
test.info().retry           // numer ponowienia (0, 1, 2...)
test.info().workerIndex     // indeks workera
test.info().setTimeout(60_000)  // dynamiczne przedłużenie timeoutu

// załącznik w trakcie testu
await test.info().attach('screenshot', {
  body: await page.screenshot(),
  contentType: 'image/png',
});
```

**Grupowanie i describe**

```jsx
test.describe('login', () => {
  test('poprawne dane', async ({ page }) => { ... });
  test('błędne hasło', async ({ page }) => { ... });
});

test.describe.only(...)   // uruchom tylko tę grupę
test.describe.skip(...)   // pomiń całą grupę
test.describe.fixme(...)  // oznacz całą grupę jako fixme
```

**Tryby równoległości**

| Tryb | Zachowanie |
| --- | --- |
| Domyślny | pliki równolegle, testy wewnątrz pliku szeregowo |
| `test.describe.parallel()` | równoległość wewnątrz konkretnej grupy |
| `fullyParallel: true` | każdy test dostaje własnego workera |
| `test.describe.serial()` | testy w grupie muszą iść po kolei |

```jsx
// plik playwright.config.ts
export default defineConfig({
  fullyParallel: true, // można też ustawić per projekt
});

// lub lokalnie w pliku testowym
test.describe.configure({ mode: 'parallel' });
test.describe.configure({ mode: 'serial' });
```

**test.use — nadpisywanie opcji i fixture'ów**

```jsx
test.describe('mobile', () => {
  test.use({ viewport: { width: 390, height: 844 } });

  test('renders correctly', async ({ page }) => { ... });
});
```

# Akcje

**Nawigacja — goto**

`page.goto()` to zazwyczaj pierwsza akcja w teście — otwiera stronę przed wykonaniem pozostałych kroków. W przeciwieństwie do innych akcji, dostępna jest tylko na obiekcie `page`, nie na locatorze.

Po przejściu do strony możemy zweryfikować poprawność nawigacji na kilka sposobów:

```jsx
// 1. po prostu kontynuuj test — auto-waiting kolejnych akcji/asercji zadba o resztę

// 2. sprawdź aktualny URL (przydatne przy testowaniu routingu i przekierowań)
await expect(page).toHaveURL(expectedURL);

// 3. sprawdź status HTTP odpowiedzi
const response = await page.goto(url);
expect(response?.status()).toBe(200);
expect(response?.ok()).toBeTruthy(); // ok() = HTTP 2xx
```

**Kliknięcia**

| **Metoda** | **Opis** | **Przykład** |
| --- | --- | --- |
| `.click()` | Kliknięcie lewym przyciskiem myszy | `await page.locator('#btn').click()` |
| `.dblclick()` | Podwójne kliknięcie | `await page.locator('.item').dblclick()` |
| `.click({ button:'right' })` | Kliknięcie prawym przyciskiem | `await el.click({ button: 'right' })` |
| `.click({ modifiers:['Shift'] })` | Klik z modyfikatorem (Shift/Ctrl/Alt/Meta) | `await el.click({ modifiers: ['Shift'] })` |
| `.click({ force:true })` | Wymusza klik nawet jeśli element zakryty | `await el.click({ force: true })` |
| `.tap()` | Dotknięcie (mobile / touch) | `await page.locator('.card').tap()` |
| `.hover()` | Najechanie kursorem na element | `await page.locator('.menu').hover()` |
| `.focus()` | Ustawienie focusu na elemencie | `await page.locator('input').focus()` |
| `.blur()` | Usunięcie focusu z elementu | `await page.locator('input').blur()` |
| `.dragTo(target)` | Przeciągnij element na inny target | `await page.locator('#src').dragTo(page.locator('#dst'))` |
| `.dragAndDrop()` | Alias page.dragAndDrop(src, dst) — wygodniejszy shortcut | `await page.dragAndDrop('#a', '#b')` |

**Wpisywanie tekstu**

| **Metoda** | **Opis** | **Przykład** |
| --- | --- | --- |
| `.fill(value)` | Czyści pole i wpisuje tekst — najczęściej używana | `await page.locator('#email').fill('test@test.pl')` |
| `.type(text)` | Symuluje wciśnięcie każdego klawisza z opóźnieniem | `await page.locator('#chat').type('hej')` |
| `.pressSequentially()` | Jak .type() — nowsza nazwa (v1.38+) | `await el.pressSequentially('hello', { delay: 50 })` |
| `.press(key)` | Wciśnięcie klawisza (np. 'Enter', 'Tab', 'ArrowDown') | `await page.locator('input').press('Enter')` |
| `.clear()` | Czyści wartość pola formularza | `await page.locator('input').clear()` |
| `.selectOption(val)` | Wybiera opcję z  po value/label/index | `await page.locator('select').selectOption('pl')` |
| `.check()` | Zaznacza checkbox lub radio button | `await page.locator('#agree').check()` |
| `.uncheck()` | Odznacza checkbox | `await page.locator('#agree').uncheck()` |
| `.setInputFiles()` | Ustawia plik(i) w | `await page.locator('input[type=file]').setInputFiles('file.pdf')` |

**Nawigacja**

| **Metoda** | **Opis** | **Przykład** |
| --- | --- | --- |
| `page.goto(url)` | Otwiera adres URL i czeka na networkidle/load | `await page.goto('https://example.com')` |
| `page.goBack()` | Cofa się w historii przeglądarki | `await page.goBack()` |
| `page.goForward()` | Przechodzi do przodu w historii | `await page.goForward()` |
| `page.reload()` | Odświeżenie strony | `await page.reload()` |
| `page.url()` | Zwraca aktualny URL strony | `const url = page.url()` |
| `page.title()` | Zwraca tytuł strony (Promise) | `const title = await page.title()` |
| `page.setViewportSize()` | Ustawia rozmiar viewportu | `await page.setViewportSize({ width: 1280, height: 720 })` |

**Oczekiwanie**

| **Metoda** | **Opis** | **Przykład** |
| --- | --- | --- |
| `.waitFor()` | Czeka aż element spełni stan: visible/hidden/attached | `await page.locator('#modal').waitFor({ state: 'visible' })` |
| `page.waitForURL()` | Czeka aż URL pasuje do wzorca (string/regex) | `await page.waitForURL('**/dashboard')` |
| `page.waitForLoadState()` | Czeka na stan ładowania: load/domcontentloaded/networkidle | `await page.waitForLoadState('networkidle')` |
| `page.waitForSelector()` | Czeka na selektor (legacy, lepiej użyj .waitFor()) | `await page.waitForSelector('.toast')` |
| `page.waitForResponse()` | Czeka na konkretną odpowiedź sieciową | `await page.waitForResponse('**/api/users')` |
| `page.waitForRequest()` | Czeka na konkretne żądanie sieciowe | `await page.waitForRequest(r => r.url().includes('/api'))` |
| `page.waitForFunction()` | Czeka aż funkcja JS w przeglądarce zwróci truthy | `await page.waitForFunction(() => window.__ready)` |
| `page.waitForEvent()` | Czeka na zdarzenie page (np. 'dialog', 'download') | `const dlPromise = page.waitForEvent('download')` |

**Asercje (expect)**

| **Metoda** | **Opis** | **Przykład** |
| --- | --- | --- |
| `expect(loc).toBeVisible()` | Element jest widoczny w DOM i na ekranie | `await expect(page.locator('#title')).toBeVisible()` |
| `expect(loc).toBeHidden()` | Element nie jest widoczny (hidden/opacity:0 itp.) | `await expect(page.locator('#spinner')).toBeHidden()` |
| `expect(loc).toBeEnabled()` | Element jest aktywny (nie disabled) | `await expect(page.locator('button')).toBeEnabled()` |
| `expect(loc).toBeDisabled()` | Element jest wyłączony (disabled) | `await expect(page.locator('button')).toBeDisabled()` |
| `expect(loc).toBeChecked()` | Checkbox/radio jest zaznaczony | `await expect(page.locator('#agree')).toBeChecked()` |
| `expect(loc).toHaveText()` | Element zawiera podany tekst (partial lub exact) | `await expect(el).toHaveText('Zaloguj się')` |
| `expect(loc).toHaveValue()` | Pole formularza ma podaną wartość | `await expect(page.locator('input')).toHaveValue('admin')` |
| `expect(loc).toHaveAttribute()` | Element ma atrybut z określoną wartością | `await expect(el).toHaveAttribute('href', '/home')` |
| `expect(loc).toHaveClass()` | Element ma podaną klasę CSS | `await expect(el).toHaveClass(/active/)` |
| `expect(loc).toHaveCount()` | Locator zwraca dokładnie N elementów | `await expect(page.locator('li')).toHaveCount(5)` |
| `expect(loc).toHaveCSS()` | Element ma konkretny styl CSS | `await expect(el).toHaveCSS('display', 'flex')` |
| `expect(loc).toHaveScreenshot()` | Visual regression — porównanie ze screenshotem | `await expect(page.locator('#chart')).toHaveScreenshot()` |
| `expect(page).toHaveURL()` | Strona ma podany URL lub pasuje do regex/glob | `await expect(page).toHaveURL(/dashboard/)` |
| `expect(page).toHaveTitle()` | Strona ma podany tytuł | `await expect(page).toHaveTitle('Panel')` |
| `expect(page).toHaveScreenshot()` | Screenshot całej strony — visual regression | `await expect(page).toHaveScreenshot('home.png')` |

**Odczyt wartości**

| **Metoda** | **Opis** | **Przykład** |
| --- | --- | --- |
| `.textContent()` | Zwraca textContent elementu (w tym ukryty tekst) | `const txt = await page.locator('h1').textContent()` |
| `.innerText()` | Zwraca innerText (tylko widoczny tekst) | `const txt = await page.locator('p').innerText()` |
| `.inputValue()` | Zwraca wartość pola input/textarea/select | `const val = await page.locator('input').inputValue()` |
| `.getAttribute(name)` | Zwraca wartość atrybutu HTML | `const href = await el.getAttribute('href')` |
| `.innerHTML()` | Zwraca innerHTML elementu | `const html = await el.innerHTML()` |
| `.isVisible()` | Sprawdza widoczność — zwraca boolean (bez retry) | `const ok = await el.isVisible()` |
| `.isEnabled()` | Sprawdza czy element aktywny — zwraca boolean | `const ok = await el.isEnabled()` |
| `.isChecked()` | Sprawdza zaznaczenie checkbox — zwraca boolean | `const ok = await el.isChecked()` |
| `.boundingBox()` | Zwraca {x,y,width,height} elementu na stronie | `const box = await el.boundingBox()` |
| `.count()` | Zwraca liczbę elementów pasujących do locatora | `const n = await page.locator('li').count()` |
| `.allTextContents()` | Zwraca tablicę textContent dla wszystkich dopasowań | `const items = await page.locator('li').allTextContents()` |
| `.allInnerTexts()` | Zwraca tablicę innerText dla wszystkich dopasowań | `const items = await page.locator('li').allInnerTexts()` |

**Dialogi i popupy**

| **Metoda** | **Opis** | **Przykład** |
| --- | --- | --- |
| `page.on('dialog', handler)` | Obsługa alert/confirm/prompt — musi być przed akcją | `page.on('dialog', d => d.accept())` |
| `dialog.accept()` | Akceptuje dialog (OK / enter text dla prompt) | `page.on('dialog', d => d.accept('wpisany tekst'))` |
| `dialog.dismiss()` | Odrzuca dialog (Cancel) | `page.on('dialog', d => d.dismiss())` |
| `dialog.message()` | Zwraca treść komunikatu dialogu | `page.on('dialog', d => console.log(d.message()))` |
| `page.waitForEvent('dialog')` | Promise — czeka na pojawienie się dialogu | `const dlg = await page.waitForEvent('dialog')` |

**Ramki (frames) i nowe okna**

| **Metoda** | **Opis** | **Przykład** |
| --- | --- | --- |
| `page.frame(name)` | Zwraca Frame po nazwie lub URL | `const frame = page.frame({ name: 'myFrame' })` |
| `page.frameLocator(sel)` | Locator dla elementów wewnątrz iframe | `const inner = page.frameLocator('iframe').locator('h1')` |
| `page.frames()` | Tablica wszystkich ramek na stronie | `const frames = page.frames()` |
| `context.waitForEvent('page')` | Czeka na otwarcie nowej zakładki/okna | `const [newPage] = await Promise.all([context.waitForEvent('page'), el.click()])` |
| `page.evaluate(fn)` | Wykonuje funkcję JS w kontekście przeglądarki | `const val = await page.evaluate(() => document.title)` |
| `page.evaluateHandle(fn)` | Jak evaluate, zwraca JSHandle do obiektu | `const hdl = await page.evaluateHandle(() => window)` |
| `page.addScriptTag()` | Wstrzykuje skrypt JS na stronę | `await page.addScriptTag({ url: 'https://cdn.js/lib.js' })` |
| `page.screenshot()` | Zapisuje screenshot strony do pliku/bufora | `await page.screenshot({ path: 'screen.png', fullPage: true })` |
| `.screenshot()` | Screenshot konkretnego elementu | `await page.locator('#chart').screenshot({ path: 'chart.png' })` |

**Klawiatura**

| **Metoda** | **Opis** | **Przykład** |
| --- | --- | --- |
| `page.keyboard.press(key)` | Wciśnięcie i zwolnienie klawisza | `await page.keyboard.press('Enter')` |
| `page.keyboard.down(key)` | Wciśnięcie klawisza (bez zwolnienia) | `await page.keyboard.down('Shift')` |
| `page.keyboard.up(key)` | Zwolnienie klawisza | `await page.keyboard.up('Shift')` |
| `page.keyboard.type(text)` | Wpisuje tekst klawisz po klawiszu | `await page.keyboard.type('hello')` |
| `page.keyboard.insertText(text)` | Wstawia tekst bez symulacji klawiszy | `await page.keyboard.insertText('ñ')` |

<aside>
💡

`keyboard.down/up` przydaje się do kombinacji klawiszy, np. Ctrl+A: `down('Control')` → `press('a')` → `up('Control')`.

</aside>

**Mysz**

| **Metoda** | **Opis** | **Przykład** |
| --- | --- | --- |
| `page.mouse.move(x, y)` | Przesuwa kursor do współrzędnych | `await page.mouse.move(100, 200)` |
| `page.mouse.click(x, y)` | Klik w podanych współrzędnych | `await page.mouse.click(100, 200)` |
| `page.mouse.dblclick(x, y)` | Podwójny klik w podanych współrzędnych | `await page.mouse.dblclick(100, 200)` |
| `page.mouse.down()` | Wciśnięcie przycisku myszy | `await page.mouse.down()` |
| `page.mouse.up()` | Zwolnienie przycisku myszy | `await page.mouse.up()` |
| `page.mouse.wheel(x, y)` | Scroll kółkiem myszy | `await page.mouse.wheel(0, 3)` |

<aside>
💡

`mouse.down/up` przydaje się do ręcznego drag & drop gdy `.dragTo()` nie działa z powodu złożonej logiki JS.

</aside>

# Asercje

**Web-first vs generyczne**

```jsx
// ❌ generyczna — sprawdza tylko w danym momencie, bez retry
expect(await locator.textContent()).toBe('Action');

// ✅ web-first — auto-retry aż warunek zostanie spełniony lub minie timeout
await expect(locator).toHaveText('Action');
```

<aside>
💡

Asercje web-first automatycznie czekają na locator i ponawiają sprawdzenie. Generyczne (`toBe`, `toEqual` itp.) wykonują się jednorazowo — nie czekają na DOM. Pamiętaj o `await` przy asercjach web-first — pomocna reguła ESLint: `@typescript-eslint/no-floating-promises` oraz plugin `eslint-plugin-playwright`.

</aside>

**Generyczne (bez await)**

```jsx
expect(value).toBe(expected)            // ta sama wartość i instancja obiektu
expect(value).toEqual(expected)         // deep equality
expect(value).toBeGreaterThan(expected) // liczby
expect(value).toBeLessThan(expected)
```

**Web-first — locator**

```jsx
// tekst
await expect(locator).toHaveText(expected)
await expect(locator).toContainText(text)

// widoczność i stan
await expect(locator).toBeVisible()
await expect(locator).toBeHidden()
await expect(locator).toBeAttached()
await expect(locator).toBeInViewport()
await expect(locator).toBeEnabled()
await expect(locator).toBeDisabled()
await expect(locator).toBeEditable()
await expect(locator).toBeEmpty()
await expect(locator).toBeFocused()

// formularze
await expect(locator).toBeChecked()
await expect(locator).toHaveValue(value)
await expect(locator).toHaveValues(values)  // multi-select

// atrybuty i CSS
await expect(locator).toHaveAttribute(name, value)
await expect(locator).toHaveClass(expected)
await expect(locator).toHaveCSS(name, value)
await expect(locator).toHaveId(id)
await expect(locator).toHaveJSProperty(name, value)
await expect(locator).toHaveRole(role)
await expect(locator).toHaveCount(count)

// dostępność
await expect(locator).toHaveAccessibleName(name)
await expect(locator).toHaveAccessibleDescription(description)

// visual regression
await expect(locator).toHaveScreenshot(name)
```

**Web-first — page**

```jsx
await expect(page).toHaveTitle(title)
await expect(page).toHaveURL(url)
await expect(page).toHaveScreenshot(name)
```

**Komunikat błędu asercji**

Można dodać własny opis do asercji — pojawi się w raporcie gdy test padnie:

```jsx
expect(items.length, 'liczba użytkowników: Adel, Filip, Frank').toBe(3);
```
```
Error: liczba użytkowników: Adel, Filip, Frank
expect(received).toBe(expected)
Expected: 3
Received: 2
```

# Aria snapshot

**ARIA Snapshot**

Mechanizm wprowadzony w Playwright v1.49, który pozwala uchwycić i porównać strukturę dostępności (accessibility tree) strony lub elementu — zamiast sprawdzać DOM czy wizualny wygląd.

---

**Czym jest Accessibility Tree?**

Przeglądarka buduje równolegle do DOM tzw. **accessibility tree** — uproszczoną, semantyczną reprezentację strony przeznaczoną dla czytników ekranu. Zawiera role, nazwy i stany elementów. Jest częścią **AOM** (Accessibility Object Model), który z kolei wchodzi w skład **WAI-ARIA** (Web Accessibility Initiative – Accessible Rich Internet Applications).

```jsx
document
  ├── heading "Witaj na stronie" (level 1)
  ├── navigation "Menu główne"
  │     ├── link "Strona główna"
  │     └── link "Kontakt"
  └── button "Zaloguj się"
```

---

**Format snapshotu**

ARIA snapshot to zapis accessibility tree w formacie YAML — odzwierciedla hierarchię, rolę ARIA i dostępną nazwę każdego elementu:

```jsx
- heading "Wyniki wyszukiwania" [level: 1]
- list:
  - listitem:
    - link "Playwright docs"
    - text: "Oficjalna dokumentacja"
  - listitem:
    - link "GitHub Playwright"
    - text: "Repozytorium projektu"
- button "Następna strona"
```

Wzorce dopasowania:

| Wzorzec | Opis |
| --- | --- |
| `"dokładny tekst"` | Dopasowanie dokładne |
| `/regex/` | Dopasowanie przez wyrażenie regularne |
| pominięty węzeł | Partial match — nie sprawdza tego elementu |

---

**⚠️ Pułapka: tabulatory vs spacje**

YAML jest wrażliwy na indentację i **nie akceptuje tabulatorów** — tylko spacje. Jeśli VS Code ma `editor.insertSpaces: false` lub automatycznie konwertuje wcięcia na taby, snapshot będzie wyglądał poprawnie, ale Playwright rzuci błędem parsowania YAML lub snapshot nie będzie matchował.

**Jak naprawić:**

Lokalnie — w prawym dolnym rogu VS Code kliknij `Tab Size` i wybierz `Indent Using Spaces`.

Globalnie dla projektu — `.vscode/settings.json`:

```jsx
{
  "[typescript]": {
    "editor.insertSpaces": true,
    "editor.tabSize": 2
  }
}
```

**Jak wykryć taby w snapshоcie:**

Włącz `View → Render Whitespace` (lub `editor.renderWhitespace: "all"`) — taby pokazują się jako `→`, spacje jako `·`.

# Lokatory

---

**Metody lokalizacji**

| Metoda | Opis |
| --- | --- |
| `page.getByRole()` | Lokalizuje po jawnych i niejawnych atrybutach dostępności |
| `page.getByText()` | Lokalizuje po treści tekstowej |
| `page.getByLabel()` | Lokalizuje kontrolkę formularza po tekście powiązanej etykiety |
| `page.getByPlaceholder()` | Lokalizuje input po placeholderze |
| `page.getByAltText()` | Lokalizuje element (zwykle obraz) po tekście alternatywnym |
| `page.getByTitle()` | Lokalizuje element po atrybucie `title` |
| `page.getByTestId()` | Lokalizuje po atrybucie `data-testid` (można skonfigurować inny atrybut w configu) |
| `page.locator()` | Lokalizuje po CSS lub XPath |

<aside>
💡

Selector roli nie zastępuje audytów dostępności, ale daje wczesny feedback zgodności z wytycznymi ARIA.

</aside>

---

**Dobre praktyki**

Unikaj selektorów opartych na strukturze DOM — są kruche i wiążą test z detalami implementacyjnymi, które programiści zmieniają bez ostrzeżenia:

```jsx
// ❌ kombinatory strukturalne — unikaj
div + span
ul ~ li
div > button
```

---

**CSS i XPath**

Prefiksy `css=` i `xpath=` są opcjonalne:

```jsx
await page.locator('button').click();           // CSS (domyślnie)
await page.locator('css=button').click();       // CSS (jawnie)
await page.locator('xpath=//button').click();   // XPath (jawnie)
```

---

**Shadow DOM**

Wszystkie lokatory domyślnie działają z elementami w Shadow DOM. Wyjątki:

- XPath nie przebija korzeni shadow
- Shadow DOM w trybie zamkniętym (`closed`) nie jest obsługiwany

Do ręcznego przejścia przez shadow root służy kombinator `>>>`:

```jsx
await page.locator('parent-element >>> child-element >>> button').click();
```

---

**Łańcuchowanie lokatorów**

```jsx
await page.getByRole('list').getByRole('listitem').getByText('Playwright').click();
```

```jsx

**Ściągawka decyzyjna**

```
Czy element ma rolę (button, input, link...)?
  ✅ TAK → getByRole()
  ❌ NIE ↓

Czy to pole formularza z labelem?
  ✅ TAK → getByLabel()
  ❌ NIE ↓

Czy ma wyróżniający tekst widoczny na stronie?
  ✅ TAK → getByText()
  ❌ NIE ↓

Czy to obrazek?
  ✅ TAK → getByAltText()
  ❌ NIE ↓

Czy ma atrybut title?
  ✅ TAK → getByTitle()
  ❌ NIE ↓

Czy masz wpływ na kod aplikacji?
  ✅ TAK → getByTestId() (dodaj data-testid do kodu)
  ❌ NIE ↓

Czy element ma stabilny, unikalny atrybut (id, aria-label...)?
  ✅ TAK → locator('[aria-label="Submit"]')
  ❌ NIE ↓

Ostateczność → locator('xpath=//div[@class="x"]/span')
```

| Lokator | Rekomendacja | Uwagi |
| --- | --- | --- |
| `getByRole()` | ✅ Zawsze | Najlepszy — opiera się na semantic HTML i ARIA |
| `getByLabel()` | ✅ Zawsze | Idealny dla pól formularzy |
| `getByPlaceholder()` | ⚠️ Gdy trzeba | Gdy `getByLabel()` niedostępny |
| `getByTestId()` | 👍 Dobry | Wymaga `data-testid` w kodzie |
| `getByText()` | 🔸 Oszczędnie | Rozważ `getByRole()` z opcją `name` |
| `getByAltText()` | 🖼️ Tylko obrazki | Dedykowany atrybutowi `alt` |
| `getByTitle()` | 🔍 Sytuacyjny | Przydatny dla `<iframe title="...">` |
| CSS (tag / atrybut) | 🔸 Oszczędnie | Stabilne selektory: tag HTML (button), unikalny atrybut ([aria-label="Submit"]), id (#submit) |
| CSS (utility classes) | ❌ Nigdy | Klasy typu .mt-4, .flex, .text-red-500 — opisują wygląd, nie semantykę; zmieniają się przy każdym redesignie |
| XPath | ❌ Nigdy | Kruche, wiążą test ze strukturą DOM |

**CSS tag/atrybut vs CSS utility classes**

`locator('button[type="submit"]')` — stabilne, bo opisuje *czym element jest*.

`locator('.bg-blue-500.rounded.px-4')` — kruche, bo opisuje *jak element wygląda*. Zmiana stylu w Tailwindzie/Bootstrap nie powinna łamać testów, a z utility classes łamie.

## Semantic HTML i jego związek z Playwright

---

**Czym jest Semantic HTML?**

HTML to język do opisywania treści, nie do tworzenia layoutów. Tagi powinny nieść znaczenie same w sobie:

```jsx
<!-- ❌ "div soup" — zero znaczenia -->
<div class="box">
  <div class="title">Artykuł</div>
  <div class="text">Treść...</div>
</div>

<!-- ✅ Semantic HTML — znaczenie od razu czytelne -->
<article>
  <h1>Artykuł</h1>
  <p>Treść...</p>
</article>
```

Tagi jak `<nav>`, `<header>`, `<article>`, `<button>`, `<main>` mówią *czym coś jest*, nie tylko jak wygląda.

---

**Dlaczego to ważne dla maszyn?**

Dobrze oznakowany HTML rozumieją:

- **Przeglądarki** — wiedzą jak renderować i obsługiwać klawiaturę
- **Wyszukiwarki** — lepiej indeksują treść (SEO)
- **Screen readery** — osoby niewidome mogą korzystać ze strony
- **Narzędzia automatyzacji** — w tym Playwright

---

**Atrybut `role` i WAI-ARIA**

Kiedy nie możesz użyć natywnego tagu semantycznego (np. budujesz custom komponent w React), używasz atrybutu `role`:

```jsx
<div role="button">Kliknij mnie</div>
```

Dzięki temu maszyny wiedzą, że ten `<div>` zachowuje się jak przycisk, mimo że nim nie jest. To część specyfikacji **WAI-ARIA** (Web Accessibility Initiative – Accessible Rich Internet Applications).

Każdy natywny element HTML ma **implicit role** (domyślną, wbudowaną):

| Tag | Implicit role |
| --- | --- |
| `<button>` | `button` |
| `<a href="...">` | `link` |
| `<h1>`–`<h6>` | `heading` |
| `<input type="checkbox">` | `checkbox` |
| `<nav>` | `navigation` |

---

**Związek z `getByRole()` w Playwright**

`getByRole()` nie szuka po CSS klasach ani ID — szuka po roli dostępności (ARIA role):

```jsx
// Playwright pyta: "znajdź element z rolą 'button' i nazwą 'Wyślij'"
await page.getByRole('button', { name: 'Wyślij' }).click();
```

To oznacza że:
- Jeśli **możesz** zlokalizować element przez `getByRole()` → element jest dobrze zbudowany pod accessibility
- Jeśli **nie możesz** → sygnał że HTML jest napisany źle

**`getByRole()` jest więc jednocześnie locatorem i testem jakości kodu frontendowego.** Używając go, mimowolnie wymuszasz na developerach pisanie dostępnego HTML.

---

**Łańcuch zależności**
```
Semantic HTML
  ↓
Implicit / explicit ARIA roles
  ↓
Screen readery działają poprawnie
  ↓
getByRole() w Playwright działa poprawnie
  ↓
Testy są odporne na zmiany CSS i struktury
```

---

**💡 DevTools tip**

Przeglądarki udostępniają w konsoli skróty do odpytywania DOM:

| Skrót | Odpowiednik | Opis |
| --- | --- | --- |
| `$('selector')` | `document.querySelector()` | Zwraca pierwszy pasujący element |
| `$$('selector')` | `document.querySelectorAll()` | Zwraca wszystkie pasujące elementy |
| `$0` | — | Zwraca ostatnio zaznaczony element w inspektorze DOM |

# Filtrowanie elementów w Playwright

**Filtrowanie elementów**

---

**Metoda `.filter()`**

Pozwala zawęzić zbiór zlokalizowanych elementów według tekstu lub zagnieżdżonego lokatora:

```jsx
// filtrowanie po tekście
await page.getByRole('listitem').filter({ hasText: 'Product 2' });

// filtrowanie po zagnieżdżonym elemencie
await page.getByRole('listitem').filter({ has: page.getByRole('button', { name: 'Add to cart' }) });

// wykluczanie po tekście
await page.getByRole('listitem').filter({ hasNotText: 'Out of stock' });

// wykluczanie po zagnieżdżonym lokatorze
await page.getByRole('listitem').filter({ hasNot: page.getByRole('button', { name: 'Remove' }) });
```

Filtry można łączyć w łańcuch:

```jsx
await page
  .getByRole('listitem')
  .filter({ hasText: 'Product' })
  .filter({ has: page.getByRole('button', { name: 'Add to cart' }) });
```

---

**Operatory `and` / `or` (Playwright ≥ 1.34)**

```jsx
// and — element musi spełniać oba warunki jednocześnie (ten sam węzeł DOM)
const button = page.getByRole('button').and(page.getByTitle('Subscribe'));

// or — element spełnia przynajmniej jeden warunek (przydatne przy wariantach UI)
const saveButton = page.getByRole('button', { name: 'Save' })
  .or(page.getByRole('button', { name: 'Zapisz' }));
```

---

**Wybór konkretnego elementu z listy**

```jsx
await page.getByRole('listitem').nth(0);   // pierwszy (indeks 0-based)
await page.getByRole('listitem').nth(2);   // trzeci

await page.getByRole('listitem').first();
await page.getByRole('listitem').last();
```

---

**Filtrowanie wewnątrz komponentu (scope)**

```jsx
const product = page.getByTestId('product-card').filter({ hasText: 'Laptop' });

// szukamy wewnątrz znalezionego elementu
await product.getByRole('button', { name: 'Add to cart' }).click();
```

---

**Opcja `hasText` — szczegóły dopasowania**

| Wartość | Zachowanie |
| --- | --- |
| `string` | Częściowe, case-insensitive |
| `/regex/` | Wyrażenie regularne |
| `/^exact$/i` | Wymuszenie dokładnego dopasowania |

```jsx
page.getByRole('listitem').filter({ hasText: 'apple' });    // częściowe
page.getByRole('listitem').filter({ hasText: /apple/i });   // regex
page.getByRole('listitem').filter({ hasText: /^apple$/i }); // dokładne
```

---

**Podsumowanie**

| Metoda / Opcja | Kiedy używać |
| --- | --- |
| `.filter({ hasText })` | Zawężenie po widocznym tekście |
| `.filter({ hasNotText })` | Wykluczenie po tekście |
| `.filter({ has })` | Zawężenie po zagnieżdżonym lokatorze |
| `.filter({ hasNot })` | Wykluczenie po zagnieżdżonym lokatorze |
| `.and()` | Element spełnia dwa lokatory naraz |
| `.or()` | Element spełnia jeden z dwóch lokatorów |
| `.nth(n)` | Konkretny element z listy duplikatów |
| `.first()` / `.last()` | Pierwszy lub ostatni z duplikatów |

# Role ARIA

**Interaktywne                                                                                  Strukturalne / nawigacyjne**

| Rola | Typowy element |
| --- | --- |
| `button` | `<button>`, `<input type="button">` |
| `link` | `<a href="...">` |
| `checkbox` | `<input type="checkbox">` |
| `radio` | `<input type="radio">` |
| `textbox` | `<input type="text">`, `<textarea>` |
| `searchbox` | `<input type="search">` |
| `spinbutton` | `<input type="number">` |
| `slider` | `<input type="range">` |
| `switch` | toggle (zazwyczaj z `role="switch"`) |
| `combobox` | `<select>` lub custom dropdown |
| `listbox` | lista opcji do wyboru |
| `option` | pojedyncza opcja w `listbox` |
| `menuitem` | element menu |
| `tab` | zakładka |
| `treeitem` | element drzewa (np. sidebar nav) |

| Rola | Typowy element |
| --- | --- |
| `heading` | `<h1>`–`<h6>` |
| `img` | `<img>` |
| `list` | `<ul>`, `<ol>` |
| `listitem` | `<li>` |
| `table` | `<table>` |
| `row` | `<tr>` |
| `cell` | `<td>` |
| `columnheader` | `<th>` |
| `rowheader` | `<th scope="row">` |
| `figure` | `<figure>` |
| `separator` | `<hr>` |

**Landmarki (obszary strony)                          Dynamiczne / alerty**

| Rola | Typowy element |
| --- | --- |
| `banner` | `<header>` |
| `navigation` | `<nav>` |
| `main` | `<main>` |
| `complementary` | `<aside>` |
| `contentinfo` | `<footer>` |
| `form` | `<form>` |
| `search` | `<search>` lub `role="search"` |
| `region` | `<section>` z labelem |

## Actionability checks

| Rola | Typowy element |
| --- | --- |
| `dialog` | `<dialog>`, modal |
| `alertdialog` | modal z alertem wymagającym akcji |
| `alert` | komunikat błędu/ostrzeżenia |
| `status` | komunikat statusu (np. "Zapisano") |
| `tooltip` | dymek podpowiedzi |
| `progressbar` | `<progress>` |
| `log` | live region z logiem |

Zanim Playwright wykona akcję (`.click()`, `.fill()`, `.check()` itp.), automatycznie sprawdza czy element jest gotowy do interakcji.

| Stan | Znaczenie |
| --- | --- |
| **visible** | Element istnieje w DOM i jest widoczny (nie ma `display:none`, `visibility:hidden`, zerowego rozmiaru) |
| **stable** | Element nie porusza się — jego pozycja nie zmienia się między dwoma klatkami (np. po animacji CSS) |
| **enabled** | Element nie ma atrybutu `disabled` |
| **editable** | Element nie jest `readonly` i nie jest `disabled` — sprawdzane przy `fill()` |
| **receives events** | Element nie jest zasłonięty przez inny element — klik faktycznie trafi w niego, nie w nakładkę/modal/spinner |

<aside>
💡

Nie każda akcja wymaga wszystkich sprawdzeń — `click()` weryfikuje wszystkie pięć, natomiast `textContent()` sprawdza tylko `visible`.

</aside>

---

# Interakcja z elementami

**Wpisywanie tekstu**

```jsx
// fill() — czyści pole i natychmiast ustawia wartość
await page.getByLabel('Email').fill('test@example.com');

// pressSequentially() — symuluje realistyczne wpisywanie znak po znaku
await page.getByLabel('Email').pressSequentially('test@example.com', { delay: 50 });
```

| Cecha | `fill()` | `press()` | `pressSequentially()` |
| --- | --- | --- | --- |
| Cel | Ustawia całą wartość pola | Symuluje naciśnięcie klawisza | Symuluje realistyczne wpisywanie |
| Czy czyści pole? | ✅ Tak | ❌ Nie | ❌ Nie |
| Wywołuje event `input`? | ✅ Tak | ❌ Nie | ✅ Tak |
| Wywołuje eventy klawiatury? | ❌ Nie | ✅ Tak | ✅ Tak |
| Symulacja pisania | ❌ Natychmiastowe | ❌ Pojedynczy klawisz | ✅ Znak po znaku |
| Szybkość | Szybka | N/A | Wolniejsza |
| Zastosowanie | Szybkie ustawianie wartości | Wysyłanie formularzy, nawigacja | Imitowanie pisania przez użytkownika |

---

**Dropdowny natywne (`<select>`)**

```jsx
const dropdown = page.getByLabel('Country');

await dropdown.selectOption('DE');                      // po value
await dropdown.selectOption({ label: 'United Kingdom' }); // po etykiecie
await dropdown.selectOption({ index: 2 });              // po indeksie (0-based)

// multi-select
await page.getByLabel('Toppings').selectOption(['pepperoni', 'mushrooms']);
await page.getByLabel('Toppings').selectOption([
  { label: 'Pepperoni' },
  { label: 'Mushrooms' },
]);
```

| Metoda | Kiedy używać |
| --- | --- |
| Po `value` | Znasz dokładną wartość atrybutu — odporne na zmiany etykiety |
| Po `label` | Mimujesz zachowanie użytkownika — uwaga na lokalizację |
| Po `index` | Wartości/etykiety są dynamiczne — kruche, kolejność może się zmienić |

---

**Dropdowny custom (non-`<select>`)**

Zbudowane z `<div>`, `<ul>` itp. — `selectOption()` tu nie zadziała. Należy odwzorować akcje użytkownika:

```jsx
const stateDropdown = page.locator('#state');
await stateDropdown.click();

const option = page.getByText('Haryana');
await expect(option).toBeVisible();
await option.click();
```

---

**Checkboxy i radio buttons**

```jsx
await page.getByLabel('I agree to the terms').check();
await page.locator('#accept-terms').uncheck();

// radio button
await page.check('input[value="red"]');
```

<aside>
💡

`check()` jest idempotentne — jeśli checkbox jest już zaznaczony, nie kliknie go ponownie. Dla radio buttonów nie ma `uncheck()` — odznaczenie następuje automatycznie przy wyborze innej opcji w grupie.

</aside>

Weryfikacja stanu:

```jsx
await expect(page.locator('#accept-terms')).toBeChecked();
await expect(page.locator('#accept-terms')).not.toBeChecked();
```

---

**Date pickery**

Natywny `<input type="date">` — najprostszy przypadek:

```jsx
await page.fill('input[type="date"]', '2025-08-08'); // format zależy od pola — sprawdź w inspektorze
```

Date picker z kalendarzem (jQuery UI, Bootstrap, Material UI):

```jsx
await page.locator('#date-picker-input').click();
await page.locator('.calendar').waitFor({ state: 'visible' });
await page.locator('.year-dropdown').selectOption('2025');
await page.locator('.month-selector[data-month="August"]').click();
await page.locator('.day-selector[data-day="8"]').click();
```

Date picker w React/Vue/Angular — gdy UI sprawia problemy, można ustawić wartość przez JavaScript:

```jsx
await page.evaluate((date) => {
  const input = document.querySelector('#date-picker-input');
  input.value = date;
  input.dispatchEvent(new Event('change', { bubbles: true }));
}, '2025-08-08');
```

<aside>
💡

Podejście JS omija eventy klawiatury i animacje — szybsze, ale może nie wyzwolić wszystkich listenerów. Stosuj gdy podejście przez UI jest zawodne.

</aside>

# Waity

**Auto-waiting (domyślny)**

Playwright automatycznie czeka aż element będzie gotowy przed każdą akcją — nie trzeba pisać `waitFor` do standardowych interakcji:

```jsx
// Playwright sam czeka aż przycisk będzie visible + enabled + stable
await page.getByRole('button', { name: 'Submit' }).click();
```

---

**Jawne czekanie na stan elementu**

```jsx
await page.getByText('Loading...').waitFor({ state: 'hidden' });
await page.getByRole('dialog').waitFor({ state: 'visible' });
```

| Stan | Znaczenie |
| --- | --- |
| `visible` | Element istnieje i jest widoczny |
| `hidden` | Element nie istnieje lub jest ukryty |
| `attached` | Element istnieje w DOM (niekoniecznie widoczny) |
| `detached` | Element zniknął z DOM |

---

**Metody waitFor***

| Metoda | Opis |
| --- | --- |
| `page.waitForURL(url)` | Czeka aż przeglądarka przejdzie na dany URL (string/regex/glob) |
| `page.waitForRequest(url)` | Czeka na wysłanie requestu pasującego do URL/regex |
| `page.waitForResponse(url)` | Czeka na otrzymanie response pasującego do URL/regex |
| `page.waitForLoadState(state)` | Czeka na stan ładowania strony |
| `page.waitForFunction(fn)` | Czeka aż funkcja JS w przeglądarce zwróci truthy |
| `page.waitForEvent(event)` | Czeka na zdarzenie Playwright (nowy tab, dialog, download) |
| `page.waitForSelector(sel)` | ⚠️ Legacy — lepiej użyj `locator.waitFor()` |
| `locator.waitFor(state)` | Czeka na konkretny stan lokowanego elementu |

```jsx
// czekanie na URL
await page.waitForURL('**/dashboard');
await page.waitForURL(/dashboard/);

// czekanie na odpowiedź sieciową
const response = await page.waitForResponse('**/api/users');
await page.getByRole('button', { name: 'Load' }).click();

// czekanie na request i akcja razem — Promise.all zapobiega race condition
const [response] = await Promise.all([
  page.waitForResponse('**/api/login'),
  page.getByRole('button', { name: 'Login' }).click(),
]);

// czekanie na dowolny warunek JS
await page.waitForFunction(() => document.title === 'Dashboard');
```

---

**Stany `waitForLoadState`**

| Stan | Kiedy odpala |
| --- | --- |
| `load` | Zdarzenie `load` — DOM + zasoby załadowane *(domyślny)* |
| `domcontentloaded` | DOM sparsowany, bez czekania na zasoby |
| `networkidle` | Brak requestów sieciowych przez 500ms — przydatny po dynamicznych ładowaniach |

---

**Opcje `page.goto()`**

```jsx
await page.goto(url, {
  waitUntil: 'load', // 'commit' | 'domcontentloaded' | 'load' | 'networkidle'
  timeout: 30000,
  referer: 'https://example.com',
});
```

| `waitUntil` | Kiedy kontynuuje |
| --- | --- |
| `commit` | Odpowiedź serwera odebrana, zaczyna się parsowanie HTML |
| `domcontentloaded` | HTML sparsowany i skrypty wykonane |
| `load` | Strona w pełni załadowana *(domyślny)* |
| `networkidle` | ⚠️ Niezalecany — brak połączeń przez 500ms |

<aside>
💡

`timeout` w `goto()` domyślnie wynosi 0 (brak limitu). W praktyce pilnuje tego ogólny timeout testu — nie ma potrzeby go zmieniać.

</aside>

---

**Czego unikać**

```jsx
// ❌ nigdy nie używaj — testy stają się wolne i niestabilne
await page.waitForTimeout(2000);
```

Zamiast `waitForTimeout` zawsze czekaj na konkretny stan elementu, URL lub odpowiedź sieciową.

# Weryfikacja CSS

**Testowanie pozycji elementów**

Playwright nie ma wbudowanych matcherów do weryfikacji układu przestrzennego, ale można je napisać samodzielnie jako **custom matchers**. Przydatne np. do sprawdzenia że menu jest po lewej stronie głównej treści na desktopie, a inaczej na mobile.

Każdy custom matcher:

- przyjmuje `receiver` (argument `expect()`) oraz dowolne dodatkowe parametry
- zwraca `MatcherReturnType` lub `Promise<MatcherReturnType>`
- musi obsługiwać negację przez `this.isNot`

```jsx
// layout-matchers.ts
import { Locator, expect } from '@playwright/test';
import type { MatcherReturnType } from '@playwright/test';

export async function toBeRightOf(
  locator: Locator,
  reference: Locator,
): Promise<MatcherReturnType> {
  let pass: boolean;

  const candidateBox = await locator.boundingBox();
  const refBox = await reference.boundingBox();

  if (!candidateBox || !refBox) {
    pass = false;
  } else {
    // element jest po prawej jeśli jego x >= x + width referencji
    pass = candidateBox.x >= refBox.x + refBox.width;
  }

  if (this.isNot) {
    pass = !pass;
  }

  return {
    message: pass ? () => 'Element jest po prawej' : () => 'Element nie jest po prawej',
    pass,
  };
}

export async function toBeLeftOf(
  locator: Locator,
  reference: Locator,
): Promise<MatcherReturnType> {
  let pass: boolean;

  const candidateBox = await locator.boundingBox();
  const refBox = await reference.boundingBox();

  if (!candidateBox || !refBox) {
    pass = false;
  } else {
    pass = candidateBox.x + candidateBox.width <= refBox.x;
  }

  if (this.isNot) {
    pass = !pass;
  }

  return {
    message: pass ? () => 'Element jest po lewej' : () => 'Element nie jest po lewej',
    pass,
  };
}
```

Rejestracja matcherów w `playwright.config.ts`:

```jsx
import { expect } from '@playwright/test';
import { toBeRightOf, toBeLeftOf } from './layout-matchers';

expect.extend({ toBeRightOf, toBeLeftOf });
```

Użycie w teście:

```jsx
await expect(page.locator('main')).toBeRightOf(page.locator('nav'));
await expect(page.locator('nav')).toBeLeftOf(page.locator('main'));
```

<aside>
💡

Metoda `boundingBox()` zwraca `{x, y, width, height}` elementu względem viewportu. Jeśli element nie istnieje lub jest ukryty, zwraca `null` — stąd sprawdzenie przed matemtyką.

</aside>

---

**`expect.poll` — asercja z retry dla wartości spoza DOM**

Standardowe web-first asercje (`expect(locator).toHaveText()`) mają wbudowany retry. Dla wartości które nie są locatorem — np. wynik funkcji async — używamy `expect.poll`:

```jsx
// zamiast ręcznego waitForFunction + expect
await expect.poll(async () => await locator.textContent()).toBe('Hello');

// z własnym timeoutem i interwałem
await expect.poll(
  async () => await page.evaluate(() => window.__dataLoaded),
  { timeout: 10000, intervals: [500, 1000, 2000] }
).toBe(true);
```

<aside>
💡

`expect.poll` odpytuje funkcję w pętli aż asercja przejdzie lub minie timeout. Przydatne gdy czekasz na stan który nie jest bezpośrednio widoczny w DOM — np. wartość w `window`, wynik API, licznik w store.

</aside>

# JSON CSV

Playwright działa w Node.js — możesz zasilać testy danymi z dowolnego źródła: pliki JSON, CSV, bazy danych, API.

```jsx
// JSON — import bezpośredni
import testData from './test-data.json' with { type: 'json' };

test('dane z JSON', async () => {
  expect(testData.username).toBe('test_user');
  expect(testData.role).toBe('admin');
});

// CSV — przez bibliotekę csv-parse
import { parse } from 'csv-parse/sync';
import { readFileSync } from 'fs';

test.describe('dane z CSV', () => {
  const records = parse(readFileSync('./tests/input.csv'), {
    columns: true,
    delimiter: ';',
    cast: true,
  });

  const record = records[0];

  test('suma wartości', () => {
    expect(record.some_value + record.another_value).toEqual(record.total);
  });
});
```

<aside>
💡

Czytanie pliku odbywa się poza blokiem `test()` — raz przy ładowaniu suite'a, nie przy każdym teście.

</aside>

---

# **Dialogi**

Playwright automatycznie odrzuca dialogi jeśli nie ma zarejestrowanego handlera. Handler musi być zarejestrowany **przed** akcją która wywołuje dialog.

```jsx
page.on('dialog', dialog => dialog.accept());
page.on('dialog', dialog => dialog.dismiss());
page.on('dialog', dialog => dialog.accept('moja odpowiedź')); // prompt

// sprawdzenie treści przed decyzją
page.on('dialog', async dialog => {
  console.log(dialog.message()); // treść dialogu
  console.log(dialog.type());    // 'alert' | 'confirm' | 'prompt' | 'beforeunload'
  await dialog.accept();
});
```

<aside>
⚠️

Handler rejestruj przed kliknięciem które wywołuje dialog — inaczej Playwright go nie przechwyci.

</aside>

---

# **iFrame**

Playwright nie wchodzi do iframe automatycznie — musisz najpierw uzyskać jego kontekst przez `frameLocator()`:

```jsx
// po selektorze lub atrybucie src
const frame = page.frameLocator('#my-iframe');
const frame = page.frameLocator('[src*="payment"]');

await frame.getByRole('button', { name: 'Submit' }).click();

// zagnieżdżony iframe
const nested = page.frameLocator('#outer').frameLocator('#inner');

// page.frame() — starszy sposób, nadal działa
const frame = page.frame({ name: 'payment' });
const frame = page.frame({ url: /stripe/ });
await frame.getByLabel('Card number').fill('4242...');
```

|  | `frameLocator()` | `page.frame()` |
| --- | --- | --- |
| Zwraca | `FrameLocator` (lazy) | `Frame` (eager) |
| Auto-waiting | ✅ Tak | ❌ Nie |
| Użycie | Zalecane | Legacy |

---

**Overlays (consent, cookie banner)**

`addLocatorHandler` pozwala zdefiniować handler raz — będzie aktywny przez cały czas życia strony i uruchomi się automatycznie gdy overlay się pojawi, niezależnie od tego w którym momencie testu:

```jsx
const overlay = page.getByTitle('Consent window');

// obsługa przez kliknięcie przycisku
await page.addLocatorHandler(overlay, async () => {
  await overlay
    .contentFrame()
    .getByRole('button', { name: 'Accept all' })
    .click();
});

// lub bardziej radykalnie — usunięcie z DOM przez JavaScript
await page.addLocatorHandler(overlay, async () => {
  await overlay.evaluate((el) => el.remove());
});
```

<aside>
💡

W przeciwieństwie do `waitForSelector` + klik, `addLocatorHandler` działa reaktywnie przez całą sesję — nie musisz obsługiwać overlaya w każdym kroku testu osobno.

</aside>

# **Screenshoty**

```jsx
// cała strona
await page.screenshot({ path: 'shot.png', fullPage: true });

// konkretny element
await someElement.screenshot({ path: 'element.png' });
```

Opcje:

| Opcja | Opis |
| --- | --- |
| `path` | Ścieżka zapisu pliku |
| `fullPage` | `true` = cały dokument, `false` = tylko widoczny viewport |
| `clip` | Obiekt `{x, y, width, height}` — wycinek ekranu |
| `omitBackground` | `true` = przezroczyste tło zamiast białego (PNG) |
| `quality` | 0–100, jakość (tylko JPEG) |
| `type` | `'png'` | `'jpeg'` |
| `scale` | `'css'` | `'device'` — uwzględnia device pixel ratio |
| `timeout` | Czas oczekiwania na screenshot |
| `animations` | `'disabled'` wyłącza animacje CSS i Web Animations |
| `caret` | `'hide'` ukrywa kursor tekstowy |
| `mask` | Tablica lokatorów do zamaskowania dynamicznych elementów |

---

## **Testy wizualne (Visual Regression)**

```jsx
// porównanie z zapisanym wzorcem
await expect(page).toHaveScreenshot('page.png');
await expect(page.locator('#chart')).toHaveScreenshot('chart.png');

// aktualizacja wzorców po zamierzonej zmianie UI
npx playwright test --update-snapshots

// maskowanie dynamicznych elementów (awatary, daty itp.)
await expect(page).toHaveScreenshot('page.png', {
  mask: [
    page.locator('.dynamic-class'),
    page.locator('.avatar'),
  ],
  maxDiffPixels: 100,    // dopuszczalna liczba różniących się pikseli
  threshold: 0.2,        // dopuszczalny próg różnicy (0–1)
});
```

Opcje można też ustawić globalnie w `playwright.config.ts`:

```jsx
expect: {
  toHaveScreenshot: {
    maxDiffPixels: 100,
    threshold: 0.2,
  },
},
```

---

# **Upload plików**

```jsx
// pojedynczy plik z dysku
await page.locator('input[type="file"]').setInputFiles('document.pdf');

// wiele plików
await page.locator('input[type="file"]').setInputFiles([
  'tests/sample1.jpg',
  'tests/sample2.jpg',
]);

// wyczyszczenie inputu
await page.locator('input[type="file"]').setInputFiles([]);

// plik wirtualny (bez zapisu na dysk)
await page.locator('input[type="file"]').setInputFiles([
  {
    name: 'file.txt',
    mimeType: 'text/plain',
    buffer: Buffer.from('zawartość pliku'),
  },
]);

// weryfikacja
await expect(page.locator('#uploadPicture')).toHaveValue(/document\.pdf$/);
```

---

# **Download plików**

```jsx
// czekanie na download przed kliknięciem — Promise.all zapobiega race condition
const downloadPromise = page.waitForEvent('download');
await page.getByText('Download').click();
const download = await downloadPromise;

// weryfikacja nazwy
const fileName = download.suggestedFilename();
expect(fileName).toMatch(/report\.pdf$/);

// zapis do konkretnej lokalizacji (np. CI)
await download.saveAs(`./downloads/${fileName}`);

// odczyt zawartości pliku
import fs from 'fs/promises';
const content = await fs.readFile(await download.path(), 'utf8');
expect(content).toContain('Expected text');

// weryfikacja rozmiaru
const stats = await fs.stat(await download.path());
const fileSizeInMB = stats.size / (1024 * 1024);
expect(fileSizeInMB).toBeGreaterThan(0.1);
expect(fileSizeInMB).toBeLessThan(100);

// obsługa błędu pobierania
const error = await download.failure();
expect(error).toContain('404');

// czyszczenie po teście
test.afterAll(async () => {
  if (downloadedFilePath) {
    await fs.unlink(downloadedFilePath);
  }
});
```

---

# **Nagrywanie wideo**

W `playwright.config.ts`:

```jsx
use: {
  video: 'retain-on-failure',
},
```

W teście (manualnie):

```jsx
const context = await browser.newContext({
  recordVideo: {
    dir: 'videos/',
    size: { width: 1280, height: 720 },
  },
});
```

| Wartość | Kiedy nagrywa |
| --- | --- |
| `off` | Nigdy |
| `on` | Zawsze |
| `retain-on-failure` | Zawsze, usuwa jeśli test przeszedł |
| `on-first-retry` | Tylko przy pierwszym ponowieniu |

<aside>
💡

Jeśli `size` nie jest ustawione, używany jest domyślny viewport.

</aside>

---

**Generowanie PDF**

Przez CLI:

```jsx
npx playwright pdf https://example.com example.pdf
# --wait-for-selector "css=.loaded" — czeka na element przed generowaniem
```

Przez skrypt Node.js (więcej kontroli):

```jsx
import { chromium } from 'playwright';

const browser = await chromium.launch();
const page = await browser.newPage();
await page.goto('https://example.com');

await page.pdf({
  path: './example.pdf',
  format: 'A4',
});

await browser.close();
```

<aside>
💡

Generowanie PDF działa tylko w Chromium i tylko w trybie headless.

</aside>

# Testy dostepności

Playwright umożliwia testy zgodne z **WCAG** — międzynarodowym zbiorem wytycznych tworzenia dostępnych stron, opracowanym przez W3C. Zapewniają równe korzystanie z sieci osobom z niepełnosprawnościami (wzroku, słuchu, ruchu, poznawczymi).

> 💡 **Terminologia:** A11y = accessibility (11 liter między "a" i "y"). POUR = Perceivable, Operable, Understandable, Robust — cztery filary WCAG.
> 

---

**Instalacja**

```jsx
npm install @axe-core/playwright --save-dev
```

---

**Podstawowe użycie**

```jsx
import AxeBuilder from '@axe-core/playwright';

test('brak naruszeń dostępności', async ({ page }) => {
  await page.goto('/');

  const results = await new AxeBuilder({ page })
    .withTags(['wcag2aa'])
    .analyze();

  expect(results.violations).toHaveLength(0);
});
```

---

**Poziomy WCAG**

| Poziom | Opis |
| --- | --- |
| A | Minimum — podstawowe wymagania |
| AA | Rekomendowany — standard dla większości projektów |
| AAA | Najwyższy — pełna zgodność, trudna do osiągnięcia |

---

**Tagi do `withTags()`**

| Tag | Zakres |
| --- | --- |
| `wcag2a` | WCAG 2.0 poziom A |
| `wcag2aa` | WCAG 2.0 poziom AA |
| `wcag21a` | WCAG 2.1 poziom A |
| `wcag21aa` | WCAG 2.1 poziom AA |
| `wcag22aa` | WCAG 2.2 poziom AA |
| `section508` | Amerykański standard dostępności |
| `best-practice` | Dobre praktyki (poza WCAG) |

---

**Filtrowanie zakresu analizy**

```jsx
const results = await new AxeBuilder({ page })
  .withTags(['wcag2aa', 'section508'])
  .include('#main-content')          // analizuj tylko ten obszar
  .exclude('.cookie-banner')         // pomiń ten element
  .withRules(['color-contrast'])     // uruchom tylko wskazane reguły
  .disableRules(['html-has-lang'])   // wyłącz wskazane reguły
  .analyze();

if (results.violations.length > 0) {
  console.log(results.violations);
}
```

# Debugowanie scenariuszy

```jsx
npx playwright test --debug   # Playwright Inspector + krok po kroku
```

```jsx
await page.pause();           
// wstrzymuje skrypt, otwiera interaktywne okno przeglądarki
```

Screenshot przy błędzie — w `playwright.config.ts`:

```jsx
use: {
  screenshot: 'only-on-failure',
}
```

| Wartość | Kiedy tworzy screenshot |
| --- | --- |
| `off` | Nigdy *(domyślny)* |
| `on` | Przy każdym teście |
| `only-on-failure` | Tylko gdy test pada |
| `on-first-retry` | Przy pierwszym ponowieniu |

<aside>
💡

 Screenshoty zapisywane są w folderze `test-results`.

</aside>

Pozostałe narzędzia debugowania: **Trace Viewer** i flaga `--trace` — opisane w sekcji [Trace Viewer].

---

# **Fixtures**

---

https://playwright.dev/docs/test-fixtures

**Czym są fixtures?**

Mechanizm Playwright do przygotowywania środowiska przed testem i sprzątania po nim. Zastępują `beforeEach`/`afterEach` — są wstrzykiwane do testów przez destrukturyzację argumentów.

```jsx
test('przykład', async ({ page, context }) => { ... });
//                         ↑ wbudowane fixtures wstrzykiwane automatycznie
```

---

**Wbudowane fixtures**

| Fixture | Opis |
| --- | --- |
| `browser` | Instancja przeglądarki — współdzielona w ramach workera, rzadko używana bezpośrednio |
| `context` | Izolowany kontekst (ciasteczka, localStorage, sesja) — każdy test dostaje świeży |
| `page` | Pojedyncza karta — najczęściej używany fixture |
| `request` | Klient HTTP do wysyłania requestów z pominięciem przeglądarki (seed danych, weryfikacja API) |

---

**Custom Fixtures**

```jsx
import { test as base } from '@playwright/test';

type MyFixtures = {
  todoPage: TodoPage;
  userData: { firstName: string; lastName: string };
};

export const test = base.extend<MyFixtures>({

  todoPage: async ({ page }, use) => {
    const todoPage = new TodoPage(page);

    // SETUP
    await todoPage.goto();
    await todoPage.addToDo('item1');

    await use(todoPage); // ← tutaj wykonuje się ciało testu

    // TEARDOWN
    await todoPage.removeAll();
  },

  userData: async ({}, use) => {
    await use({ firstName: 'Jane', lastName: 'Doe' });
  },

});

export { expect } from '@playwright/test';
```

<aside>
💡

Wszystko **przed** `use()` to setup, wszystko **po** `use()` to teardown. Playwright gwarantuje wykonanie teardownu nawet jeśli test rzuci błąd.

</aside>

---

**Automatic Fixtures**

Fixture uruchamiany automatycznie dla każdego testu — bez potrzeby jawnego dodawania:

```jsx
export const test = base.extend<{ forEachTest: void }>({
  forEachTest: [
    async ({}, use) => {
      console.log('setup');
      await use();
      console.log('teardown');
    },
    { auto: true },
  ],
});
```

---

**Fixture Scope**

| Scope | Czas życia | Kiedy używać |
| --- | --- | --- |
| `test` *(domyślny)* | Jeden test | Izolacja między testami |
| `worker` | Cały worker | Kosztowny setup — logowanie, seed DB |

```jsx
const test = base.extend({
  dbConnection: [async ({}, use) => {
    const db = await connectToDb();
    await use(db);
    await db.close();
  }, { scope: 'worker' }],
});
```

---

**Fixture Lifecycle**
```
Worker start
  └── [scope: worker] fixtures — inicjalizowane raz
        └── Test 1 start
              └── [scope: test] fixtures — świeże dla każdego testu
                    └── ciało testu (use())
              └── teardown test-scoped fixtures
        └── Test 2 start
              └── ...
  └── teardown worker-scoped fixtures
Worker end
```

<aside>
💡

Fixtures są **leniwe** — inicjalizują się tylko gdy test ich potrzebuje. Teardown jest zawsze odwrócony względem setupu.

</aside>

---

**Organizacja fixtures**

Zalecane podejście — jeden plik eksportujący customowy `test`:

```jsx
// fixtures/index.ts
import { test as base, expect } from '@playwright/test';
import { CheckoutPage } from './checkout-page';
import { userData, type UserData } from './user-data-fixture';

type MyFixtures = {
  checkoutPage: CheckoutPage;
  userData: UserData;
};

const checkoutPage = async ({ page }, use) => {
  await use(new CheckoutPage(page));
};

export const test = base.extend<MyFixtures>({ checkoutPage, userData });
export { expect } from '@playwright/test';
```

Łączenie fixtures z bibliotek zewnętrznych:

```jsx
import { mergeTests } from '@playwright/test';
import { test as dbTest } from 'database-test-utils';
import { test as a11yTest } from 'a11y-test-utils';

export const test = mergeTests(dbTest, a11yTest);
```

---

**Dlaczego fixtures zamiast `beforeEach`?**

| Cecha | Fixtures | `beforeEach` |
| --- | --- | --- |
| Kompozycyjność | ✅ Mogą zależeć od innych fixtures | ❌ Brak |
| Reużywalność | ✅ Współdzielone między plikami | ❌ Tylko w pliku |
| Inicjalizacja | ✅ Leniwa — tylko gdy potrzebne | ❌ Zawsze |
| Izolacja | ✅ Każdy test dostaje własną instancję | ⚠️ Współdzielony stan |
| Czytelność testu | ✅ Test dostaje gotowy obiekt | ⚠️ Setup widoczny w teście |

**Fixtures — warstwa decyzyjna**

---

**Kiedy używać czego**

| Problem | Rozwiązanie |
| --- | --- |
| Powtarzający się login między testami | `fixture` + `storageState` |
| Setup specyficzny dla jednego testu | `beforeEach` w pliku |
| Jednorazowy kosztowny setup (DB, serwer) | `fixture` ze `scope: 'worker'` |
| Różne role użytkowników | fixture factory |
| Globalny seed danych przed całą sesją | `globalSetup` w `playwright.config.ts` |
| Izolowana logika nawigacji po stronie | POM |
| Szybki jednorazowy scenariusz | inline, bez POM i fixture |
| Zewnętrzne API niedostępne w teście | `page.route()` — mock |
| Test integracyjny z prawdziwym API | fixture `request` |

---

**Fixtures jako dependency injection**

Fixture to nie tylko "setup danych" — to mechanizm **dependency injection + lifecycle management**. Test deklaruje czego potrzebuje, Playwright dostarcza to w odpowiednim momencie i sprząta po zakończeniu.

```jsx
// fixture dostarcza gotowe środowisko — test nie wie jak powstało
test('user can buy product', async ({ loggedInPage }) => {
  await loggedInPage.goto('/shop');
  await loggedInPage.getByRole('button', { name: 'Buy' }).click();
  await expect(loggedInPage.getByText('Order confirmed')).toBeVisible();
});
```

Fixture `loggedInPage` może wewnętrznie używać `storageState`, POM i API seed — test tego nie widzi i nie musi wiedzieć.

---

**Fixture factory — różne role użytkowników**

```jsx
type UserRole = 'admin' | 'standard' | 'readonly';

const test = base.extend<{ asUser: (role: UserRole) => Promise<Page> }>({
  asUser: async ({ browser }, use) => {
    const pages: Page[] = [];

    await use(async (role: UserRole) => {
      const context = await browser.newContext({
        storageState: `.auth/${role}.json`,
      });
      const page = await context.newPage();
      pages.push(page);
      return page;
    });

    // teardown wszystkich stron
    for (const page of pages) await page.close();
  },
});

// użycie
test('admin widzi panel, standard nie', async ({ asUser }) => {
  const adminPage = await asUser('admin');
  const standardPage = await asUser('standard');

  await adminPage.goto('/admin');
  await expect(adminPage.getByRole('heading', { name: 'Admin Panel' })).toBeVisible();

  await standardPage.goto('/admin');
  await expect(standardPage).toHaveURL(/403/);
});
```

---

**Fixture zależny od innego fixture**

```jsx
const test = base.extend<{
  apiClient: ApiClient;
  seededUser: User;
}>({
  apiClient: async ({ request }, use) => {
    await use(new ApiClient(request));
  },

  // seededUser zależy od apiClient
  seededUser: async ({ apiClient }, use) => {
    const user = await apiClient.createUser({ role: 'standard' });
    await use(user);
    await apiClient.deleteUser(user.id); // teardown
  },
});
```

Playwright sam rozwiązuje kolejność inicjalizacji i teardownu na podstawie grafu zależności.

---

**Anti-patterny**

```jsx
// ❌ login w każdym teście — wolne i kruche
test('test 1', async ({ page }) => {
  await page.goto('/login');
  await page.fill('#email', 'user@example.com');
  await page.fill('#password', 'secret');
  await page.click('[type=submit]');
  // właściwy test...
});

// ✅ fixture z storageState — login raz, reużywany wszędzie
test('test 1', async ({ loggedInPage }) => {
  // od razu zalogowany
});
```

```jsx
// ❌ shared mutable state między testami — prowadzi do zależności między testami
let sharedPage: Page;
test.beforeAll(async ({ browser }) => {
  sharedPage = await browser.newPage();
});
test('test A', async () => { await sharedPage.goto('/a'); });
test('test B', async () => { await sharedPage.goto('/b'); }); // może zależeć od stanu z test A

// ✅ każdy test dostaje własną instancję
test('test A', async ({ page }) => { await page.goto('/a'); });
test('test B', async ({ page }) => { await page.goto('/b'); });
```

```jsx
// ❌ over-engineering — fixture dla jednorazowego użycia
const test = base.extend<{ specialButton: Locator }>({
  specialButton: async ({ page }, use) => {
    await use(page.getByRole('button', { name: 'Submit' }));
  },
});
// ✅ inline — to jest po prostu locator
const submitBtn = page.getByRole('button', { name: 'Submit' });
```

```jsx
// ❌ logika biznesowa w teście zamiast w fixture/POM
test('checkout', async ({ page }) => {
  await page.goto('/login');
  await page.fill('#email', 'user@test.com');
  // 20 linii setupu...
  // właściwy test: 3 linie
});

// ✅ fixture enkapsuluje setup
test('checkout', async ({ loggedInPage, checkoutPage }) => {
  await checkoutPage.fill();
  await checkoutPage.submit();
  await expect(loggedInPage.getByText('Thank you')).toBeVisible();
});
```

---

**Trzy pytania przed napisaniem fixture**

1. **Czy ten kod powtarza się w więcej niż jednym teście?** Jeśli nie — `beforeEach` lub inline.
2. **Czy setup jest kosztowny (logowanie, baza, API)?** Jeśli tak — fixture ze `scope: 'worker'`.
3. **Czy różne testy potrzebują różnych wariantów?** Jeśli tak — fixture factory.

# Chrome DevTools Protocol

[https://github.com/aslushnikov/getting-started-with-cdp](https://github.com/aslushnikov/getting-started-with-cdp)

[https://chromedevtools.github.io/devtools-protocol/](https://chromedevtools.github.io/devtools-protocol/)

CDP to niskopoziomowy protokół komunikacji z przeglądarką Chromium — pozwala na operacje niedostępne przez standardowe API Playwright, takie jak throttling sieci i CPU czy zbieranie metryk wydajności.

<aside>
💡

CDP działa tylko w Chromium — testy używające CDP należy skipować na innych przeglądarkach.

</aside>

---

**Tworzenie sesji CDP**

```jsx
const session = await context.newCDPSession(page);
```

---

**Throttling sieci i CPU**

```jsx
test('wydajność z throttlingiem', async ({ browser, browserName }) => {
  if (browserName !== 'chromium') test.skip();

  const context = await browser.newContext();
  const page = await context.newPage();
  const session = await context.newCDPSession(page);

  await session.send('Network.enable');
  await session.send('Network.emulateNetworkConditions', {
    offline: false,
    latency: 100,               // ms
    downloadThroughput: 75000,  // bytes/s
    uploadThroughput: 25000,
  });

  await session.send('Emulation.setCPUThrottlingRate', { rate: 4 }); // 4× wolniejszy CPU

  await page.goto('https://playwright.dev');
  await context.close();
});
```

---

**Zbieranie metryk wydajności przez CDP**

```jsx
await session.send('Performance.enable');
const metricsResponse = await session.send('Performance.getMetrics');
console.log(metricsResponse.metrics); // heap, DOM nodes, layout count itp.
```

---

**Metryki nawigacji przez Performance API**

```jsx
const metrics = await page.evaluate(() => {
  const nav = performance.getEntriesByType('navigation')[0];
  return {
    dnsLookup:        nav.domainLookupEnd - nav.domainLookupStart,
    tcpConnect:       nav.connectEnd - nav.connectStart,
    tlsHandshake:     nav.requestStart - nav.secureConnectionStart, // 0 jeśli HTTP
    ttfb:             nav.responseStart - nav.requestStart,
    download:         nav.responseEnd - nav.responseStart,
    domProcessing:    nav.domComplete - nav.responseEnd,
    domContentLoaded: nav.domContentLoadedEventEnd - nav.domContentLoadedEventStart,
    totalLoad:        nav.loadEventEnd - nav.startTime,
  };
});
```

| Metoda | Co daje |
|---|---|
| `performance.getEntriesByType('resource')` | Timing każdego zasobu (JS, CSS, img) |
| `performance.getEntriesByType('paint')` | `first-paint` i `first-contentful-paint` |
| CDP `Performance.getMetrics` | Heap, DOM nodes, layout count |

---

**Core Web Vitals**

Zestaw metryk Google wpływających na SEO, audyty wydajności (Lighthouse) i kontrakty SLA.

| Metryka | Co mierzy | Perspektywa | Dobry wynik |
|---|---|---|---|
| **TTFB** | Czas do pierwszego bajtu odpowiedzi serwera | Backend | < 200ms |
| **FCP** | Pierwszy widoczny element (tekst, obraz) | UX / Frontend | < 1.8s |
| **LCP** | Największy element na stronie | UX / Frontend | < 2.5s |
| **CLS** | Skoki layoutu (przesunięcia elementów) | UX / Frontend | < 0.1 |
| **INP** | Reakcja na klik / interakcję | UX / Interaktywność | < 200ms |
| **TTI** | Czas do pełnej interaktywności | UX / Frontend | < 3.8s |
| **TBT** | Czas blokowania wątku głównego przez JS | Frontend / Dev | < 200ms |
| **SI** | Szybkość wypełniania widocznej części ekranu | UX / Frontend | < 3.4s |

**TTFB** — czas od wysłania żądania do otrzymania pierwszego bajtu:
```
Przeglądarka          Serwer
    |--- request -----→ |
    |                   | ← serwer przetwarza (baza, logika…)
    |← pierwszy bajt ---|
    ↑___________________↑
           TTFB
```

**LCP** — czas do wyrenderowania największego widocznego elementu (hero image, nagłówek):
```
[ brak ]  →  [nagłówek]  →  [hero image ████████]
                                      ↑
                                     LCP
```

**TTI / TBT / SI** — uzupełniające metryki developerskie:

- **TTI** *(Time to Interactive)* — strona wygląda gotowo, ale czy można już klikać bez opóźnień?
- **TBT** *(Total Blocking Time)* — jak długo JS blokował wątek główny między FCP a TTI
- **SI** *(Speed Index)* — im szybciej piksele wypełniają ekran, tym lepiej

<aside>
💡

W kontekście Playwright — **TTFB** i **LCP** to dwie metryki najłatwiejsze do zmierzenia automatycznie i z bezpośrednim przełożeniem na doświadczenie użytkownika.

</aside>

# Autetykacja

**Storage State — zapisywanie sesji**

Zamiast logować się przed każdym testem, można zapisać stan sesji (ciasteczka, localStorage) do pliku i reużywać go:

```jsx
// zapisanie stanu sesji po zalogowaniu
await page.context().storageState({ path: '.auth/standard_user.json' });
```

<aside>
💡

Dodaj `.auth/` do `.gitignore` — pliki sesji zawierają wrażliwe dane. Dodaj `.gitkeep` żeby folder był śledzony przez Git i dostępny w CI/CD.

</aside>

---

**Konfiguracja w `playwright.config.ts`**

```jsx
export default defineConfig({
  projects: [
    {
      name: 'setup',
      testMatch: /.*\.setup\.ts/,
    },
    {
      name: 'e2e-tests',
      dependencies: ['setup'],
      use: {
        storageState: '.auth/standard_user.json',
      },
    },
  ],
});
```

Dla różnych ról użytkowników twórz osobne pliki setup — np. `.auth/admin_user.json`, `.auth/standard_user.json`.

Użycie w konkretnym teście:

```jsx
test.use({ storageState: '.auth/standard_user.json' });
```

---

**Weryfikacja ciasteczek**

```jsx
// sprawdź czy ciasteczko sesji już nie istnieje (np. po wylogowaniu)
await expect.poll(async () =>
  (await page.context().cookies()).some(
    cookie => cookie.name === 'session-username'
  )
).toBeFalsy();
```

---

**Wiele kontekstów — różne role jednocześnie**

Każdy `BrowserContext` ma własną sesję — można testować interakcje między różnymi rolami bez konfliktów:

```jsx
let standardContext: BrowserContext;
let lockedoutContext: BrowserContext;

test.afterAll(async () => {
  await standardContext.close();
  await lockedoutContext.close();
});

test('Standard vs Locked Out', async ({ browser }) => {
  standardContext = await browser.newContext();
  lockedoutContext = await browser.newContext();

  const standardPage = await standardContext.newPage();
  const lockedoutPage = await lockedoutContext.newPage();

  // standardowy użytkownik
  await standardPage.goto('https://www.saucedemo.com/');
  await standardPage.getByPlaceholder('Username').fill('standard_user');
  await standardPage.getByPlaceholder('Password').fill('secret_sauce');
  await standardPage.getByRole('button', { name: 'Login' }).click();
  await expect(standardPage).toHaveURL(/inventory/);

  // zablokowany użytkownik
  await lockedoutPage.goto('https://www.saucedemo.com/');
  await lockedoutPage.getByPlaceholder('Username').fill('locked_out_user');
  await lockedoutPage.getByPlaceholder('Password').fill('secret_sauce');
  await lockedoutPage.getByRole('button', { name: 'Login' }).click();
  await expect(lockedoutPage.locator('[data-test="error"]'))
    .toHaveText('Epic sadface: Sorry, this user has been locked out.');
});
```

---

**Zmienne środowiskowe**

Dane logowania trzymaj w `.env`, nie hardkoduj w testach:

```jsx
npm install dotenv
```

```jsx
// .env
USERNAME=standard_user
PASSWORD=secret_sauce
```

```jsx
// w teście
const username = process.env.USERNAME;
const password = process.env.PASSWORD;
```

---

**OAuth / SSO / MFA**

| Scenariusz | Podejście |
| --- | --- |
| SSO (np. Google) | Ręczne zalogowanie w persistent context → zapis sesji → reużycie w testach |
| CAPTCHA | Wyłączenie w środowisku testowym lub mock responses (konfiguracja po stronie IdP) |
| MFA / TOTP | Biblioteka `OTPAuth` — generowanie kodów z klucza TOTP zapisanego w `.env` |
| MFA email/SMS | `Mailosaur` — API do pobierania kodów z testowych skrzynek |
| OAuth token | Bezpośrednia iniekcja tokenu do localStorage lub ustawienie ciasteczka z pominięciem UI |

# Emulacje

---

**Sieć — throttling (CDP, tylko Chromium)**

```jsx
const slow3G = {
  downloadThroughput: 500 * 1024 / 8, // 500 kbps
  uploadThroughput:   500 * 1024 / 8,
  latency: 400,
  offline: false,
};

test.skip(({ browserName }) => browserName !== 'chromium', 'Tylko Chromium');

test('slow 3G', async ({ page }) => {
  const client = await page.context().newCDPSession(page);
  await client.send('Network.emulateNetworkConditions', slow3G);

  await page.goto('https://playwright.dev');

  // wyłączenie throttlingu
  await client.send('Network.emulateNetworkConditions', {
    offline: false,
    latency: 0,
    downloadThroughput: -1, // -1 = brak ograniczenia
    uploadThroughput: -1,
  });
});
```

---

**Tryb offline**

```jsx
await context.setOffline(true);
```

---

**Geolokacja**

```jsx
test.use({
  ...devices['Pixel 5'],
  geolocation: { latitude: 41.889938, longitude: 12.492507 }, // Rzym
  permissions: ['geolocation'],
});

test('geolokacja', async ({ page }) => {
  await page.goto('https://www.openstreetmap.org/');
  await page.getByRole('button', { name: /Show My Location/i }).click();
  await expect(page).toHaveURL(/41\.88/);
});
```

---

**Urządzenia mobilne**

```jsx
test.use({ ...devices['Pixel 5'] });
test.use({ ...devices['iPhone 12'] });
```

Lista dostępnych urządzeń: `npx playwright devices`

---

**Locale i strefa czasowa**

```jsx
test.use({ locale: 'en-US' });
test.use({ timezoneId: 'Europe/Paris' });
```

---

**Zegar (Clock)**

```jsx
// ustaw stały czas — Date.now() i new Date() zwrócą tę wartość
await page.clock.setFixedTime(new Date('2024-01-01T12:00:00'));

// przewiń czas do przodu o 5 minut
await page.clock.fastForward('05:00');

// przewiń i wykonaj wszystkie timery (setTimeout, setInterval)
await page.clock.runFor(1000);
```

---

**Dialogi (alert / confirm / prompt)**

Omówione szczegółowo w sekcji [Dialogi]. Krótkie przypomnienie:

```jsx
page.on('dialog', dialog => dialog.accept());
page.on('dialog', dialog => dialog.dismiss());
```

<aside>
💡

Na urządzeniach mobilnych dialogi mogą wyglądać inaczej niż na desktopie — są stylizowane przez system operacyjny (iOS vs Android). Custom popupy (cookie banners, modale HTML) obsługuje się przez `addLocatorHandler`.

</aside>

---

**Wykonywanie JavaScript na stronie**

```jsx
// bez argumentów
await page.evaluate(() => document.writeln('Hello'));

// z przekazaniem zmiennej z Node.js do przeglądarki
const name = 'Jane';
await page.evaluate((n) => document.writeln(`Hello ${n}`), name);
```

<aside>
💡

`page.evaluate()` wykonuje funkcję w kontekście przeglądarki — nie ma dostępu do zmiennych Node.js, chyba że przekażesz je jako drugi argument.

</aside>

# Wydajność testów Playwright

---

**Wydajność testów**

---

**Blokowanie zasobów sieciowych**

Ograniczenie niepotrzebnych zasobów skraca czas wykonania testów:

```jsx
// blokowanie obrazków po rozszerzeniu
await page.route('**/*.{png,jpg,jpeg,svg,gif}', route => route.abort());

// blokowanie obrazków po resourceType
await page.route('**/*', route =>
  route.request().resourceType() === 'image'
    ? route.abort()
    : route.continue()
);

// blokowanie trackerów i reklam
await page.route('**/ads.googleads.com/**', route => route.abort());
await page.route('https://*.ads.com/**', route => route.abort());
```

---

**Mockowanie odpowiedzi API**

```jsx
// podmiana całej odpowiedzi
await page.route('**/api/v1/fruits', async route => {
  await route.fulfill({ json: [{ name: 'Strawberry', id: 21 }] });
});

// modyfikacja istniejącej odpowiedzi
await page.route('**/api/v1/account/features*', async route => {
  const response = await route.fetch();
  const json = await response.json();

  const proPlan = json.features.find(f => f.name === 'Pro Plan');
  proPlan.status = 'ACTIVE';

  await route.fulfill({ json });
});
```

---

**Konfiguracja — oszczędność miejsca**

Screenshoty, video i trace generuj tylko przy błędach:

```jsx
use: {
  trace:      'on-first-retry',
  screenshot: 'on-first-retry',
  video:      'on-first-retry',
},
```

---

**Pomiar LCP**

`addInitScript` wstrzykuje skrypt przed nawigacją — dzięki temu obserwator jest gotowy zanim strona zacznie się ładować:

```jsx
test('LCP na google.com', async ({ page }) => {
  await page.addInitScript(() => {
    let lcp: number;
    new PerformanceObserver((entries) => {
      entries.getEntries().forEach(e => { lcp = e.startTime; });
    }).observe({ type: 'largest-contentful-paint', buffered: true });
    window.__lcp = () => lcp;
  });

  await page.goto('https://www.google.com', { waitUntil: 'load' });
  await page.waitForTimeout(3000); // czekamy na zakończenie paintów

  const lcpValue = await page.evaluate(() => window.__lcp());
  console.log('LCP:', lcpValue, 'ms');

  expect(lcpValue).toBeLessThanOrEqual(2000); // dobry LCP ≤ 2s
});
```

<aside>
💡

`waitForTimeout` jest tu uzasadniony wyjątkowo — metryki paint wymagają chwili po załadowaniu strony. W standardowych testach funkcjonalnych nadal należy go unikać.

</aside>

---

**Pomiar zużycia pamięci**

```jsx
const memory = await page.evaluate(() =>
  performance.memory
    ? {
        usedJSHeap:  performance.memory.usedJSHeapSize,
        totalJSHeap: performance.memory.totalJSHeapSize,
      }
    : null
);
```

<aside>
💡

`performance.memory` jest dostępne tylko w Chromium — w Firefox i WebKit zwróci `null`.

</aside>

# Testy mobilne

Playwright obsługuje testy mobilne na dwa sposoby: emulację urządzeń w przeglądarce (wszystkie platformy) oraz testy na fizycznych urządzeniach przez Android Debug Bridge (ADB).

---

**Device descriptors**

Gotowe profile urządzeń ustawiają automatycznie viewport, user agent, touch i deviceScaleFactor:

```jsx
import { test, devices } from '@playwright/test';

// w teście
const context = await browser.newContext({ ...devices['iPhone 15 Pro Max'] });

// w playwright.config.ts
export default defineConfig({
  projects: [
    { name: 'Mobile Chrome',  use: { ...devices['Pixel 5'] } },
    { name: 'Mobile Safari',  use: { ...devices['iPhone 12'] } },
    { name: 'Desktop Chrome', use: { ...devices['Desktop Chrome'] } },
  ],
});
```

Listowanie dostępnych urządzeń:

```jsx
npx playwright show-devices
```

```jsx
import { devices } from 'playwright';
console.log(Object.keys(devices));
```

---

**Ręczna konfiguracja kontekstu mobilnego**

```jsx
const context = await browser.newContext({
  viewport:          { width: 375, height: 667 },
  deviceScaleFactor: 2,
  isMobile:          true,
  hasTouch:          true,
  userAgent: 'Mozilla/5.0 (iPhone; CPU iPhone OS 13_5 like Mac OS X) ...',
});
```

---

**Testowanie wielu viewportów**

```jsx
const viewports = [
  { name: 'Mobile',   width: 390,  height: 844  },
  { name: 'Tablet',   width: 768,  height: 1024 },
  { name: 'Desktop',  width: 1920, height: 1080 },
];

for (const viewport of viewports) {
  test(`renders correctly on ${viewport.name}`, async ({ page }) => {
    await page.setViewportSize({ width: viewport.width, height: viewport.height });
    await page.goto('https://example.com');
    await expect(page).toHaveScreenshot({ fullPage: true });
  });
}
```

---

**Symulacja obrotu ekranu**

```jsx
const context = await browser.newContext({
  ...devices['iPhone 12'],
  screen: { width: 844, height: 390 }, // landscape
});
```

---

**Gesty dotykowe**

```jsx
test.use({ ...devices['iPhone 15'] });

test('wyszukiwanie na mobile', async ({ page }) => {
  await page.goto('https://www.wikipedia.org/');

  await page.tap('input[name="search"]');
  await page.fill('input[name="search"]', 'Playwright');
  await page.tap('button[type="submit"]');

  await expect(page).toHaveURL(/Playwright/);
});
```

Dla złożonych gestów niedostępnych przez `tap()`:

```jsx
await page.touchscreen.tap(x, y);

// lub ręczna sekwencja przez evaluate
await page.evaluate(() => {
  const el = document.querySelector('#element');
  el.dispatchEvent(new TouchEvent('touchstart', { bubbles: true }));
  el.dispatchEvent(new TouchEvent('touchend',   { bubbles: true }));
});
```

<aside>
💡

Jeśli `tap()` nie wywołuje oczekiwanego zachowania — sprawdź w DevTools czy element ma listenery na `touchstart`/`touchend`. Niektóre komponenty reagują tylko na zdarzenia dotykowe, nie na click.

</aside>

---

**Popularne user agenty**

| Urządzenie | User Agent |
| --- | --- |
| Chrome 120 / Windows | `Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 ... Chrome/120.0.0.0 Safari/537.36` |
| Chrome 120 / macOS | `Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 ... Chrome/120.0.0.0 Safari/537.36` |
| Firefox 120 / Windows | `Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:120.0) Gecko/20100101 Firefox/120.0` |
| Safari 17 / macOS | `Mozilla/5.0 (Macintosh; Intel Mac OS X 14_1) AppleWebKit/605.1.15 ... Version/17.1 Safari/605.1.15` |
| Chrome / Android | `Mozilla/5.0 (Linux; Android 13; Pixel 7) AppleWebKit/537.36 ... Chrome/120.0.0.0 Mobile Safari/537.36` |
| Safari / iPhone | `Mozilla/5.0 (iPhone; CPU iPhone OS 17_1 like Mac OS X) AppleWebKit/605.1.15 ... Version/17.1 Mobile/15E148 Safari/604.1` |
| Googlebot | `Mozilla/5.0 (compatible; Googlebot/2.1; +http://www.google.com/bot.html)` |

<aside>
💡

W praktyce rzadko wpisujesz UA ręcznie — `devices['iPhone 15']` ustawia go automatycznie wraz z viewport i touch.

</aside>

# POM

POM to wzorzec projektowy polegający na reprezentowaniu stron (lub ich części) jako klas TypeScript. Klasa enkapsuluje lokatory i akcje — test nie zna szczegółów implementacji, tylko wywołuje metody.

**Problem bez POM:** selektory i akcje powtarzają się w wielu testach. Zmiana jednego elementu UI wymaga poprawek w dziesiątkach miejsc.

---

**Implementacja**

```jsx
// pages/TodoPage.ts
import { type Page, type Locator } from '@playwright/test';

export class TodoPage {
  readonly page: Page;
  readonly newTodoInput: Locator;
  readonly todoItems: Locator;
  readonly clearCompletedButton: Locator;

  constructor(page: Page) {
    this.page = page;
    this.newTodoInput          = page.locator('input.new-todo');
    this.todoItems             = page.locator('ul.todo-list li');
    this.clearCompletedButton  = page.locator('button.clear-completed');
  }

  async goto() {
    await this.page.goto('https://demo.playwright.dev/todomvc');
  }

  async addTodo(text: string) {
    await this.newTodoInput.fill(text);
    await this.newTodoInput.press('Enter');
  }

  async toggleTodo(index: number) {
    await this.todoItems.nth(index).locator('input.toggle').check();
  }

  async clearCompleted() {
    await this.clearCompletedButton.click();
  }
}
```

Użycie w teście:

```jsx
import { test, expect } from '@playwright/test';
import { TodoPage } from '../pages/TodoPage';

test('dodaj i ukończ todo', async ({ page }) => {
  const todoPage = new TodoPage(page);
  await todoPage.goto();
  await todoPage.addTodo('Buy groceries');

  await expect(todoPage.todoItems).toHaveCount(1);

  await todoPage.toggleTodo(0);
  await todoPage.clearCompleted();

  await expect(todoPage.todoItems).toHaveCount(0);
});
```

<aside>
💡

Lokatory w konstruktorze są **leniwe** — nie odpytują DOM aż do użycia. Jeśli selektor się zmieni, poprawiasz go w jednym miejscu.

</aside>

---

**POM + Fixture**

Zamiast tworzyć POM ręcznie w każdym teście, można delegować to do fixture:

```jsx
// fixtures/index.ts
import { test as base, expect } from '@playwright/test';
import { CheckoutPage } from './checkout-page';

type MyFixtures = { checkoutPage: CheckoutPage };

export const test = base.extend<MyFixtures>({
  checkoutPage: async ({ page }, use) => {
    const checkoutPage = new CheckoutPage(page);
    await use(checkoutPage);
    // teardown jeśli potrzebny
  },
});

export { expect } from '@playwright/test';
```

```jsx
// w teście
import { test, expect } from './fixtures';

test('checkout', async ({ page, checkoutPage }) => {
  await page.goto('/checkout');
  await checkoutPage.fill();
  await checkoutPage.submit();
  await expect(page.getByRole('heading')).toContainText('Thank you!');
});
```

<aside>
💡

POM przez fixture jest inicjalizowany na żądanie, obsługuje setup/teardown i nadal daje dostęp do `page` — to dobry przykład kompozycji fixtures.

</aside>

---

**Unikanie hardkodowania danych**

Dane testowe w `config.json`:

```jsx
{
  "baseUrl": "https://www.saucedemo.com/",
  "users": {
    "standard": { "username": "standard_user", "password": "secret_sauce" },
    "admin":    { "username": "problem_user",  "password": "secret_sauce" }
  }
}
```

```jsx
import config from '../config.json';

await page.goto(config.baseUrl);
await page.getByPlaceholder('Username').fill(config.users.standard.username);
```

# Dynamiczne dane testowe — biblioteka Faker

```jsx
npm install @faker-js/faker --save-dev
```

```jsx
import { faker } from '@faker-js/faker';

const user = {
  name:  faker.person.fullName(),
  email: faker.internet.email(),
};
```

# API

**Mockowanie API**

```jsx
await page.route('https://jsonplaceholder.typicode.com/posts', async route => {
  await route.fulfill({
    status: 200,
    contentType: 'application/json',
    body: JSON.stringify([
      { id: 1, title: 'Mocked Post One' },
      { id: 2, title: 'Mocked Post Two' },
    ]),
  });
});
```

Modyfikacja istniejącej odpowiedzi (zamiast jej podmiany):

```jsx
await page.route('**/api/features*', async route => {
  const response = await route.fetch();
  const json = await response.json();
  json.features.find(f => f.name === 'Pro Plan').status = 'ACTIVE';
  await route.fulfill({ json });
});
```

---

**Testy API (fixture `request`)**

```jsx
// GET
test('GET quote', async ({ request }) => {
  const response = await request.get('https://dummyjson.com/quotes/1');
  expect(response.ok()).toBeTruthy();
  expect(response.status()).toBe(200);

  const data = await response.json();

  // porównanie wybranych pól — nie wymaga wszystkich
  expect(data).toMatchObject({
    author: 'Rumi',
  });
});
```

| Metoda HTTP | Fixture |
| --- | --- |
| GET | `request.get()` |
| POST | `request.post()` |
| PUT | `request.put()` |
| PATCH | `request.patch()` |
| DELETE | `request.delete()` |

<aside>
💡

`toMatchObject()` jest lepsze niż `toEqual()` przy weryfikacji odpowiedzi API — porównuje tylko wskazane pola, ignoruje resztę.

</aside>

---

**Tworzenie treści strony w teście (`page.setContent`)**

Przydatne gdy chcemy przetestować konkretny fragment HTML bez potrzeby uruchamiania serwera:

```jsx
await page.setContent(`
  <button id="btn" disabled>Click me</button>
  <div id="message" style="display:none">Success</div>
  <script>
    setTimeout(() => { document.getElementById('btn').disabled = false; }, 1000);
    document.getElementById('btn').addEventListener('click', () => {
      document.getElementById('message').style.display = 'block';
    });
  </script>
`);

await page.getByRole('button', { name: 'Click me' }).click({ force: true });
await expect(page.getByText('Success')).toBeVisible();
```

---

**Własny reporter**

Reporter implementuje interfejs `Reporter` — wszystkie metody są opcjonalne:

```jsx
import type { Reporter, FullConfig, Suite, TestCase, TestResult, FullResult } from '@playwright/test/reporter';

class MyReporter implements Reporter {
  path: string;

  constructor(options: { path?: string } = {}) {
    this.path = options.path ?? 'reports';
  }

  onBegin(config: FullConfig, suite: Suite) {
    console.log(`Liczba testów: ${suite.allTests().length}`);
    // np. czyszczenie folderu raportów
  }

  onTestEnd(test: TestCase, result: TestResult) {
    // np. generowanie HTML raportu z załączników axe
    const attachment = result.attachments.find(a => a.name === 'accessibility-scan-results');
    if (!attachment?.body) return;
    // przetwarzanie wyników...
  }

  async onEnd(result: FullResult) {
    console.log('Testy zakończone');
  }
}

export default MyReporter;
```

Rejestracja w `playwright.config.ts`:

```jsx
export default defineConfig({
  reporter: [['./reporters/my-reporter.ts', { path: 'reports' }]],
});
```

Cykl życia wywołań:
```
onBegin         — start całego suite'a
  onTestBegin   — przed każdym testem
    onStepBegin — przed każdym krokiem (test.step)
    onStepEnd
  onTestEnd     — po każdym teście (tu dostępne załączniki, status)
onEnd           — koniec wszystkich testów
onExit          — tuż przed zamknięciem test runnera
```

---

**Component Testing (eksperymentalne)**

Testy komponentów uruchamiane w prawdziwej przeglądarce — w przeciwieństwie do jsdom/happy-dom wykrywają błędy CSS i stylów.

Inicjalizacja:

```jsx
npm init playwright -- --ct
```

Skrypt w `package.json`:

```jsx
{ "scripts": { "test-ct": "playwright test -c playwright-ct.config.ts" } }
```

Pliki dodane przez CLI:

| Plik | Opis |
| --- | --- |
| `playwright/index.html` | Harness dla komponentów |
| `playwright/index.tsx` | Globalny kod, importy, style |
| `playwright-ct.config.ts` | Konfiguracja dla CT |

Obsługiwane frameworki: **React** (17, 18), **Vue** (2, 3), **Svelte**, **Solid**.

<aside>
💡

Angular nie jest oficjalnie obsługiwany. CT jest nadal eksperymentalne — brak systemu pluginów dla nowych frameworków.

</aside>

# Eslint-plugin-playwright

`eslint-plugin-playwright` wyłapuje błędy specyficzne dla Playwright, których ogólny TypeScript nie rozumie:

```jsx
npm install -D eslint-plugin-playwright
```

```jsx
// eslint.config.ts
import playwright from 'eslint-plugin-playwright';

export default [
  {
    ...playwright.configs['flat/recommended'],
    files: ['**/*.spec.ts', '**/*.test.ts'],
  }
];
```

Co wykrywa:

```jsx
// ❌ brak await — test przejdzie, ale asercja się nie wykona
expect(page.locator('.btn')).toBeVisible();
page.click('.btn');

// ✅
await expect(page.locator('.btn')).toBeVisible();
await page.click('.btn');

// ❌ zbędny await przy generycznych asercjach (no-useless-await)
await expect(1).toBe(1);
await page.getByRole('heading'); // samo pobranie lokatora nie wymaga await

// ❌ test.only pozostawione przez przypadek (no-focused-test)
test.only('mój test', ...);

// ❌ użycie isVisible() zamiast web-first asercji
expect(await page.locator('.post').isVisible()).toBe(true);
// ✅
await expect(page.locator('.post')).toBeVisible();
```

<aside>
💡

`no-floating-promises` (typescript-eslint) łapie niezaawaitowane Promise'y globalnie, `eslint-plugin-playwright` łapie wzorce specyficzne dla Playwright. Razem tworzą solidną siatkę bezpieczeństwa.

</aside>

Naprawianie automatycznie:

`npm run lint -- --fix`

---

**Blokowanie reklam i trackerów**

`npm install --save-dev @ghostery/adblocker-playwright`

```jsx
import { PlaywrightBlocker } from '@ghostery/adblocker-playwright';

test('z ad blockerem', async ({ page }) => {
  const blocker = await PlaywrightBlocker.fromPrebuiltAdsAndTracking();
  blocker.enableBlockingInPage(page);
  await page.goto('https://example.com');
});
```

---

**Rozszerzanie matcherów — jest-extended**

```jsx
import { toBeFinite, toBeValidDate } from 'jest-extended';
import { test, expect as baseExpect } from '@playwright/test';

const expect = baseExpect.extend({ toBeFinite, toBeValidDate });

test('valid date', async () => {
  expect(new Date()).toBeValidDate();
  expect(new Date('01/90/2018')).not.toBeValidDate();
});
```

---

**Flaky testy**

Traktuj flaky testy jak dług techniczny — nie blokują teraz, ale będą spowalniać w przyszłości i padną w najgorszym możliwym momencie (release, hotfix).

**Tagowanie flaky testów:**

```jsx
test('flaky test', {
  tag: '@flaky',
  annotation: { type: 'issue', description: 'https://github.com/acme/app/issues/42' },
}, async ({ page }) => { ... });

// uruchamianie z pominięciem flaky
npx playwright test --grep-invert @flaky
```

**Test burn-in — weryfikacja stabilności nowego testu:**

```jsx
# uruchom 100 razy bez retry — każdy fail jest widoczny
npx playwright test --retries=0 --repeat-each=100

# tylko zmienione testy względem main
npx playwright test --only-changed=origin/main --retries=0 --repeat-each=100
```

**Chaos fixture — sztuczne utrudnienia do wykrywania niestabilności:**

```jsx
export const test = base.extend<{ chaos: () => Promise<void> }>({
  chaos: async ({ browserName, page }, use) => {
    await use(async () => {
      if (browserName !== 'chromium') test.skip();

      // CPU throttling 4×
      const client = await page.context().newCDPSession(page);
      await client.send('Emulation.setCPUThrottlingRate', { rate: 4 });

      // opóźnienie XHR o 1s
      await page.route('**', async route => {
        if (route.request().resourceType() === 'xhr') {
          await setTimeout(1_000);
        }
        await route.continue();
      });
    });
  },
});
```

---

**`expect.toPass` — retry bloku kodu**

Przydatne przy hydration i niestabilnych komponentach — ponawia cały blok aż przejdzie:

```jsx
await expect(async () => {
  await page.getByRole('button', { name: 'Click me' }).click();
  await expect(page.getByText('Success')).toBeVisible();
}).toPass();
```

Hierarchia podejść do czekania:

```jsx
// ❌ nigdy
await page.waitForTimeout(2000);

// akceptowalne
await orderSent.waitFor();

// ✅ najlepsze
await expect(orderSent).toBeVisible();
```

---

# **BDD (Behavior-Driven Development)**

`npm install playwright-bdd`

Scenariusz w Gherkin:

```jsx
Given I am on home page
When I click link "Get started"
Then I see a heading "Installation"
```

Implementacja kroków:

```jsx
import { createBdd } from 'playwright-bdd';
const { Given, When, Then } = createBdd();

Given('I am on home page', async ({ page }) => {
  await page.goto('https://playwright.dev');
});

When('I click link {string}', async ({ page }, name) => {
  await page.getByRole('link', { name }).click();
});

Then('I see a heading {string}', async ({ page }, keyword) => {
  await expect(page.getByRole('heading', { name: keyword })).toBeVisible();
});
```

Uruchomienie:

```jsx
npx bddgen && npx playwright test
```

# Storybook

Storybook to katalog komponentów UI — pozwala uruchamiać i testować pojedyncze komponenty w izolacji, bez potrzeby ładowania całej aplikacji. Każdy stan komponentu (Story) ma własny URL, co Playwright może wykorzystać bezpośrednio.

---

**Trzy formy współpracy**

**A. Storybook Test Runner**

Narzędzie używające Playwright i Jesta pod maską. Odwiedza każdą Story i sprawdza czy renderuje się bez błędów.

```jsx
npm install @storybook/test-runner --save-dev
npx test-storybook
```

<aside>
💡

Darmowe smoke testy dla całego design systemu bez pisania ani jednej linii kodu testowego.

</aside>

**B. Interaction Testing**

Scenariusze interakcji pisane bezpośrednio w pliku Story (składnia Testing Library). Playwright zapewnia środowisko przeglądarki.

**C. Visual Regression Testing**

Playwright wchodzi na każdą Story, robi screenshot i porównuje ze wzorcem:

```jsx
test('Button - primary variant', async ({ page }) => {
  await page.goto('http://localhost:6006/?path=/story/button--primary');
  await expect(page.locator('#storybook-root')).toHaveScreenshot('button-primary.png');
});
```

---

**Storybook vs klasyczne E2E**

| Cecha | E2E (cała aplikacja) | Storybook + Playwright |
| --- | --- | --- |
| Szybkość | Wolne — cała aplikacja musi działać | Szybkie — tylko izolowany komponent |
| Stabilność | Częste flaky testy przez błędy API | Wysoka — dane zamockowane w Story |
| Debugowanie | Trudne — trzeba odtworzyć stan aplikacji | Łatwe — wchodzisz bezpośrednio w Story |
| Zakres | Pełne przepływy użytkownika | Pojedyncze komponenty i ich stany |

<aside>
💡

Storybook dostarcza "scenę" (izolowane komponenty), Playwright to "inspektor" który wchodzi na tę scenę, weryfikuje działanie, robi screenshoty i raportuje błędy.

</aside>

Źródła:
https://playwright.dev/
https://claude.com/

Hands-On Automated Testing with Playwright Faraz K. Kelhini | Butch Mayhew
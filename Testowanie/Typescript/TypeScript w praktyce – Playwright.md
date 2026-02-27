# TypeScript w praktyce – Playwright

> **Zakres:** TypeScript z Playwright · Page Object Model · Fixtures · Konfiguracja `tsconfig.json` · ESLint i narzędzia
> 
> 
> **Uzupełnienia JS:** Event loop i async · Promise chain · `async`/`await` mechanizm
> 

---

## 🟨 Fundamenty JS: Event Loop i asynchroniczność

> **Dla programisty Java:** Java używa wątków do obsługi współbieżności. JavaScript jest **jednowątkowy** – używa event loop i asynchronicznych callbacków. To fundamentalne dla Playwright, który jest w 100% asynchroniczny.
> 

### Jak działa Event Loop

```
Stos wywołań (Call Stack)     Kolejka zadań (Task Queue)
┌─────────────────────┐       ┌──────────────────────┐
│  aktualnie wykonany │  ←──  │ setTimeout callback   │
│  kod synchroniczny  │       │ Promise.then callback │
└─────────────────────┘       │ await continuation    │
                              └──────────────────────┘

JS wykonuje kod synchronicznie → gdy stos pusty → bierze z kolejki
```

```tsx
console.log('1 – synchronicznie');

setTimeout(() => console.log('3 – po setTimeout'), 0);

Promise.resolve().then(() => console.log('2 – mikrotask'));

console.log('4 – nadal synchronicznie');

// Kolejność: 1, 4, 2, 3
// Mikrotaski (Promise) zawsze przed makrotaskami (setTimeout)
```

### `Promise` – podstawy

```tsx
// Promise to "obietnica" wartości w przyszłości:
const promise = new Promise<string>((resolve, reject) => {
  // asynchroniczna operacja:
  setTimeout(() => {
    if (Math.random() > 0.5) {
      resolve('Sukces!');  // spełniona obietnica
    } else {
      reject(new Error('Błąd!'));  // odrzucona obietnica
    }
  }, 1000);
});

// Trzy stany Promise:
// - pending   → oczekuje
// - fulfilled → resolve() zostało wywołane
// - rejected  → reject() zostało wywołane

// Łańcuch .then():
promise
  .then(value => value.toUpperCase())  // transformacja wartości
  .then(upper => console.log(upper))   // dalsze przetwarzanie
  .catch(err  => console.error(err))   // obsługa błędów
  .finally(() => console.log('Koniec')); // zawsze na końcu
```

### `async`/`await` – syntactic sugar nad Promise

```tsx
// Kod z .then():
function fetchUserOld(id: number): Promise<User> {
  return fetch(`/api/users/${id}`)
    .then(res => {
      if (!res.ok) throw new Error('Błąd HTTP');
      return res.json();
    })
    .then(data => data as User);
}

// Ten sam kod z async/await (czytelniejszy):
async function fetchUser(id: number): Promise<User> {
  const res = await fetch(`/api/users/${id}`);
  if (!res.ok) throw new Error('Błąd HTTP');
  const data = await res.json();
  return data as User;
}

// async funkcja ZAWSZE zwraca Promise – nawet gdy zwracasz prymityw:
async function getName(): Promise<string> {
  return 'Jan'; // automatycznie opakowane w Promise.resolve('Jan')
}
```

### Równoległe wykonanie vs sekwencyjne

```tsx
// ❌ Sekwencyjne – wolne (każde czeka na poprzednie):
const user    = await fetchUser(1);    // czeka 1s
const posts   = await fetchPosts(1);   // czeka jeszcze 1s
const profile = await fetchProfile(1); // czeka jeszcze 1s
// łącznie: ~3s

// ✅ Równoległe – szybkie (wszystko startuje naraz):
const [user, posts, profile] = await Promise.all([
  fetchUser(1),    //  ─┐
  fetchPosts(1),   //   ├─ wszystkie startują jednocześnie
  fetchProfile(1), //  ─┘
]);
// łącznie: ~1s (czas najwolniejszego)

// Promise.allSettled – nawet gdy część się nie uda:
const results = await Promise.allSettled([fetchUser(1), fetchUser(2)]);
results.forEach(result => {
  if (result.status === 'fulfilled') console.log(result.value);
  else console.error(result.reason);
});
```

> **💡 Dla Playwright:** Prawie każda operacja Playwright to `async` – klikanie, wypełnianie, nawigacja, oczekiwanie. `await` jest obowiązkowe. Pominięcie `await` to jeden z najczęstszych błędów.
> 

---

## 10.1 Konfiguracja projektu Playwright z TypeScript

### Instalacja i setup

```bash
# Nowy projekt Playwright:
npm init playwright@latest

# Dodanie do istniejącego projektu:
npm install --save-dev @playwright/test
npx playwright install  # instalacja przeglądarek

# Struktura projektu:
# playwright.config.ts   – konfiguracja
# tests/                 – pliki testowe
# pages/                 – Page Objects
# fixtures/              – własne fixtures
# utils/                 – helpery
```

### `playwright.config.ts` – typowana konfiguracja

```tsx
import { defineConfig, devices } from '@playwright/test';

export default defineConfig({
  // Katalog z testami:
  testDir: './tests',

  // Uruchamianie testów:
  fullyParallel: true,
  forbidOnly: !!process.env.CI, // blokuj test.only w CI
  retries: process.env.CI ? 2 : 0,
  workers: process.env.CI ? 1 : undefined,

  // Reporter:
  reporter: [
    ['html', { outputFolder: 'playwright-report' }],
    ['json', { outputFile: 'test-results.json' }],
    process.env.CI ? ['github'] : ['list'],
  ],

  // Globalne ustawienia dla wszystkich testów:
  use: {
    baseURL: process.env.BASE_URL ?? 'http://localhost:3000',
    trace: 'on-first-retry',
    screenshot: 'only-on-failure',
    video: 'retain-on-failure',
    actionTimeout: 10_000,
    navigationTimeout: 30_000,
  },

  // Projekty (przeglądarki/urządzenia):
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
      name: 'mobile-chrome',
      use: { ...devices['Pixel 5'] },
    },
    {
      name: 'setup',
      testMatch: /.*\.setup\.ts/,
    },
  ],

  // Uruchom dev server przed testami:
  webServer: {
    command: 'npm run dev',
    url: 'http://localhost:3000',
    reuseExistingServer: !process.env.CI,
  },
});
```

### `tsconfig.json` dla Playwright

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "lib": ["ES2020"],
    "module": "CommonJS",
    "moduleResolution": "node",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "resolveJsonModule": true,
    "outDir": "./dist",
    "rootDir": ".",
    "baseUrl": ".",
    "paths": {
      "@pages/*":    ["pages/*"],
      "@fixtures/*": ["fixtures/*"],
      "@utils/*":    ["utils/*"],
      "@data/*":     ["test-data/*"]
    }
  },
  "include": ["**/*.ts"],
  "exclude": ["node_modules", "dist", "playwright-report"]
}
```

---

## 10.2 Page Object Model (POM) z TypeScript

### Bazowa klasa Page Object

```tsx
// pages/base.page.ts
import { Page, Locator, expect } from '@playwright/test';

export abstract class BasePage {
  protected readonly page: Page;

  constructor(page: Page) {
    this.page = page;
  }

  // Abstrakcyjna właściwość – każda strona musi podać URL:
  abstract get pageUrl(): string;

  // Nawigacja:
  async goto(): Promise<void> {
    await this.page.goto(this.pageUrl);
    await this.waitForLoad();
  }

  // Opcjonalna metoda do nadpisania:
  protected async waitForLoad(): Promise<void> {
    await this.page.waitForLoadState('networkidle');
  }

  // Wspólne helpery:
  protected async fillAndVerify(locator: Locator, value: string): Promise<void> {
    await locator.fill(value);
    await expect(locator).toHaveValue(value);
  }

  async takeScreenshot(name: string): Promise<void> {
    await this.page.screenshot({
      path: `screenshots/${name}-${Date.now()}.png`,
      fullPage: true,
    });
  }

  async getTitle(): Promise<string> {
    return this.page.title();
  }
}
```

### Page Object dla konkretnej strony

```tsx
// pages/login.page.ts
import { Page, Locator, expect } from '@playwright/test';
import { BasePage } from './base.page';
import { DashboardPage } from './dashboard.page';

export interface LoginCredentials {
  email: string;
  password: string;
}

export class LoginPage extends BasePage {
  // Locators – zdefiniowane raz, używane wszędzie:
  private readonly emailInput: Locator;
  private readonly passwordInput: Locator;
  private readonly submitButton: Locator;
  private readonly errorMessage: Locator;
  private readonly rememberMeCheckbox: Locator;

  constructor(page: Page) {
    super(page);
    // Inicjalizacja locatorów w konstruktorze:
    this.emailInput        = page.getByLabel('Email');
    this.passwordInput     = page.getByLabel('Hasło');
    this.submitButton      = page.getByRole('button', { name: 'Zaloguj się' });
    this.errorMessage      = page.getByTestId('error-message');
    this.rememberMeCheckbox = page.getByRole('checkbox', { name: 'Zapamiętaj mnie' });
  }

  get pageUrl(): string {
    return '/login';
  }

  // Metody akcji – zwracają Page Objects (Page Object Chain):
  async login(credentials: LoginCredentials): Promise<DashboardPage> {
    await this.emailInput.fill(credentials.email);
    await this.passwordInput.fill(credentials.password);
    await this.submitButton.click();
    await this.page.waitForURL('/dashboard');
    return new DashboardPage(this.page);
  }

  async loginWithRemember(credentials: LoginCredentials): Promise<DashboardPage> {
    await this.rememberMeCheckbox.check();
    return this.login(credentials);
  }

  // Metody asercji – enkapsulują oczekiwania:
  async expectErrorMessage(message: string): Promise<void> {
    await expect(this.errorMessage).toBeVisible();
    await expect(this.errorMessage).toHaveText(message);
  }

  async expectLoginFormVisible(): Promise<void> {
    await expect(this.emailInput).toBeVisible();
    await expect(this.passwordInput).toBeVisible();
    await expect(this.submitButton).toBeEnabled();
  }

  // Gettery do asercji zewnętrznych:
  get email(): Locator { return this.emailInput; }
  get error(): Locator { return this.errorMessage; }
}
```

### Komponent wielokrotnego użytku

```tsx
// pages/components/navigation.component.ts
import { Page, Locator } from '@playwright/test';

export class NavigationComponent {
  private readonly nav: Locator;
  private readonly userMenu: Locator;
  private readonly logoutButton: Locator;

  constructor(private readonly page: Page) {
    this.nav          = page.getByRole('navigation');
    this.userMenu     = page.getByTestId('user-menu');
    this.logoutButton = page.getByRole('button', { name: 'Wyloguj' });
  }

  async navigateTo(section: 'dashboard' | 'settings' | 'profile'): Promise<void> {
    await this.nav.getByRole('link', { name: section }).click();
    await this.page.waitForURL(`/${section}`);
  }

  async logout(): Promise<void> {
    await this.userMenu.click();
    await this.logoutButton.click();
    await this.page.waitForURL('/login');
  }

  async getUserName(): Promise<string | null> {
    return this.userMenu.textContent();
  }
}

// Użycie w Page Object:
export class DashboardPage extends BasePage {
  readonly nav: NavigationComponent;

  constructor(page: Page) {
    super(page);
    this.nav = new NavigationComponent(page);
  }

  get pageUrl(): string { return '/dashboard'; }
}
```

---

## 10.3 Fixtures – typowane dane i kontekst testowy

### Podstawowe fixtures

```tsx
// fixtures/index.ts
import { test as base, Page } from '@playwright/test';
import { LoginPage } from '@pages/login.page';
import { DashboardPage } from '@pages/dashboard.page';

// Typ własnych fixtures:
type MyFixtures = {
  loginPage: LoginPage;
  dashboardPage: DashboardPage;
  loggedInPage: DashboardPage; // zalogowany użytkownik
};

// Typ danych testowych:
type TestData = {
  adminUser: { email: string; password: string };
  regularUser: { email: string; password: string };
};

export const test = base.extend<MyFixtures & TestData>({
  // Page Object fixtures – tworzone per test:
  loginPage: async ({ page }, use) => {
    const loginPage = new LoginPage(page);
    await loginPage.goto();
    await use(loginPage);
  },

  dashboardPage: async ({ page }, use) => {
    await use(new DashboardPage(page));
  },

  // Fixture z zalogowanym użytkownikiem:
  loggedInPage: async ({ page }, use) => {
    const loginPage = new LoginPage(page);
    await loginPage.goto();
    const dashboard = await loginPage.login({
      email: process.env.TEST_USER_EMAIL ?? 'test@example.com',
      password: process.env.TEST_USER_PASSWORD ?? 'password',
    });
    await use(dashboard);
  },

  // Dane testowe:
  adminUser: async ({}, use) => {
    await use({
      email: process.env.ADMIN_EMAIL ?? 'admin@example.com',
      password: process.env.ADMIN_PASSWORD ?? 'adminpass',
    });
  },

  regularUser: async ({}, use) => {
    await use({
      email: 'user@example.com',
      password: 'userpass',
    });
  },
});

export { expect } from '@playwright/test';
```

### Fixture z globalnym setupem (auth state)

```tsx
// tests/auth.setup.ts
import { test as setup, expect } from '@playwright/test';
import path from 'path';

const authFile = path.join(__dirname, '../.auth/user.json');

setup('authenticate', async ({ page }) => {
  await page.goto('/login');
  await page.fill('#email', process.env.TEST_EMAIL!);
  await page.fill('#password', process.env.TEST_PASSWORD!);
  await page.click('button[type="submit"]');
  await expect(page).toHaveURL('/dashboard');

  // Zapisz stan sesji (cookies, localStorage):
  await page.context().storageState({ path: authFile });
});

// playwright.config.ts – użyj savedState:
// projects: [{
//   name: 'authenticated',
//   use: {
//     storageState: '.auth/user.json',
//   },
//   dependencies: ['setup'],
// }]
```

---

## 10.4 Typowanie testów Playwright

### Podstawowa struktura testu

```tsx
// tests/login.spec.ts
import { test, expect } from '../fixtures';
import type { LoginCredentials } from '@pages/login.page';

// Grupowanie testów:
test.describe('Logowanie', () => {
  // Hook przed każdym testem:
  test.beforeEach(async ({ loginPage }) => {
    await expect(loginPage.email).toBeVisible();
  });

  test('poprawne logowanie', async ({ loginPage, regularUser }) => {
    const dashboard = await loginPage.login(regularUser);
    await expect(dashboard.page).toHaveURL('/dashboard');
  });

  test('błędne hasło', async ({ loginPage, regularUser }) => {
    const wrongCredentials: LoginCredentials = {
      ...regularUser,
      password: 'wrong-password',
    };

    await loginPage.login(wrongCredentials).catch(() => {}); // ignorujemy throw
    await loginPage.expectErrorMessage('Nieprawidłowe dane logowania');
  });

  test('puste pola', async ({ loginPage }) => {
    await loginPage.submitButton.click(); // próba bez wypełnienia
    await loginPage.expectErrorMessage('Email jest wymagany');
  });
});
```

### Własne asercje (Custom Matchers)

```tsx
// utils/custom-matchers.ts
import { expect } from '@playwright/test';

// Rozszerzenie interfejsu matchers:
interface CustomMatchers<R = unknown> {
  toBeValidEmail(): R;
  toHaveLoadingState(state: 'loading' | 'loaded' | 'error'): Promise<void>;
}

declare global {
  namespace PlaywrightTest {
    interface Matchers<R> extends CustomMatchers<R> {}
  }
}

expect.extend({
  toBeValidEmail(received: string) {
    const re = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    const pass = re.test(received);
    return {
      pass,
      message: () => pass
        ? `Oczekiwano, że${received} NIE jest poprawnym emailem`
        : `Oczekiwano, że${received} jest poprawnym emailem`,
    };
  },

  async toHaveLoadingState(locator: Locator, state: 'loading' | 'loaded' | 'error') {
    const dataAttr = await locator.getAttribute('data-state');
    const pass = dataAttr === state;
    return {
      pass,
      message: () => `Oczekiwano state="${state}", otrzymano "${dataAttr}"`,
    };
  },
});

// Użycie:
// expect('jan@example.com').toBeValidEmail();
// await expect(spinner).toHaveLoadingState('loaded');
```

### Helpery z typami

```tsx
// utils/playwright.helpers.ts
import { Page, Locator, expect } from '@playwright/test';

// Typowany helper do tabel:
export async function getTableData(
  page: Page,
  tableSelector: string
): Promise<Record<string, string>[]> {
  const headers = await page
    .locator(`${tableSelector} th`)
    .allTextContents();

  const rows = await page.locator(`${tableSelector} tbody tr`).all();

  return Promise.all(
    rows.map(async row => {
      const cells = await row.locator('td').allTextContents();
      return Object.fromEntries(
        headers.map((h, i) => [h.trim(), cells[i]?.trim() ?? ''])
      );
    })
  );
}

// Typowany helper do formularzy:
interface FormData {
  [key: string]: string | boolean | string[];
}

export async function fillForm(page: Page, data: FormData): Promise<void> {
  for (const [field, value] of Object.entries(data)) {
    const locator = page.locator(`[name="${field}"]`);

    if (typeof value === 'boolean') {
      value ? await locator.check() : await locator.uncheck();
    } else if (Array.isArray(value)) {
      await locator.selectOption(value);
    } else {
      await locator.fill(value);
    }
  }
}

// Helper do retryingu asercji:
export async function waitForCondition(
  condition: () => Promise<boolean>,
  options: { timeout?: number; interval?: number; message?: string } = {}
): Promise<void> {
  const { timeout = 10_000, interval = 500, message = 'Warunek nie spełniony' } = options;
  const start = Date.now();

  while (Date.now() - start < timeout) {
    if (await condition()) return;
    await new Promise(r => setTimeout(r, interval));
  }

  throw new Error(`Timeout:${message}`);
}
```

---

## 10.5 API Testing z Playwright i TypeScript

```tsx
// tests/api/users.api.spec.ts
import { test, expect } from '@playwright/test';

// Typowanie odpowiedzi API:
interface User {
  id: number;
  name: string;
  email: string;
  role: 'admin' | 'user';
}

interface ApiResponse<T> {
  data: T;
  status: number;
  message: string;
}

test.describe('API: Użytkownicy', () => {
  // Bazowy URL API – z konfiguracji:
  const apiUrl = '/api/users';

  test('GET /api/users zwraca listę', async ({ request }) => {
    const response = await request.get(apiUrl);

    expect(response.status()).toBe(200);

    const body: User[] = await response.json();
    expect(body).toBeInstanceOf(Array);
    expect(body.length).toBeGreaterThan(0);

    // Sprawdzenie struktury pierwszego elementu:
    const first = body[0];
    expect(first).toMatchObject({
      id:    expect.any(Number),
      name:  expect.any(String),
      email: expect.any(String),
      role:  expect.stringMatching(/^(admin|user)$/),
    });
  });

  test('POST /api/users tworzy użytkownika', async ({ request }) => {
    const newUser = {
      name:  'Test User',
      email: `test-${Date.now()}@example.com`,
      role:  'user' as const,
    };

    const response = await request.post(apiUrl, { data: newUser });

    expect(response.status()).toBe(201);

    const created: User = await response.json();
    expect(created.id).toBeDefined();
    expect(created.name).toBe(newUser.name);
    expect(created.email).toBe(newUser.email);
  });

  test('DELETE /api/users/:id usuwa użytkownika', async ({ request }) => {
    // Najpierw utwórz:
    const createResponse = await request.post(apiUrl, {
      data: { name: 'Do usunięcia', email: 'delete@example.com', role: 'user' },
    });
    const { id }: User = await createResponse.json();

    // Usuń:
    const deleteResponse = await request.delete(`${apiUrl}/${id}`);
    expect(deleteResponse.status()).toBe(204);

    // Zweryfikuj:
    const getResponse = await request.get(`${apiUrl}/${id}`);
    expect(getResponse.status()).toBe(404);
  });
});
```

---

## 10.6 Konfiguracja tsconfig.json

```json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "noUncheckedIndexedAccess": true,

    "target": "ES2020",
    "lib": ["ES2020"],
    "module": "CommonJS",
    "moduleResolution": "node",
    "esModuleInterop": true,
    "skipLibCheck": true,
    "resolveJsonModule": true,
    "sourceMap": true,

    "baseUrl": ".",
    "paths": {
      "@pages/*":    ["pages/*"],
      "@fixtures/*": ["fixtures/*"],
      "@utils/*":    ["utils/*"]
    }
  }
}
```

---

## 10.7 ESLint dla projektów Playwright

```bash
npm install --save-dev eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin
npm install --save-dev eslint-plugin-playwright
```

```jsx
// .eslintrc.cjs
module.exports = {
  parser: '@typescript-eslint/parser',
  parserOptions: { project: './tsconfig.json' },
  plugins: ['@typescript-eslint', 'playwright'],
  extends: [
    'eslint:recommended',
    'plugin:@typescript-eslint/recommended',
    'plugin:playwright/recommended',
  ],
  rules: {
    // Playwright-specific:
    'playwright/no-wait-for-timeout': 'warn',     // unikaj stałych timeoutów
    'playwright/no-force-option': 'warn',          // unikaj force: true
    'playwright/prefer-web-first-assertions': 'error', // expect(locator) nie (await locator.text())
    'playwright/no-conditional-in-test': 'warn',  // unikaj if/else w testach
    'playwright/no-skipped-test': 'warn',          // pilnuj skip-ów

    // TypeScript:
    '@typescript-eslint/no-explicit-any': 'error',
    '@typescript-eslint/no-floating-promises': 'error', // brakujący await!
    '@typescript-eslint/consistent-type-imports': ['error', { prefer: 'type-imports' }],
    '@typescript-eslint/no-unused-vars': ['error', { argsIgnorePattern: '^_' }],
  },
};
```

---

## Podsumowanie – Cheat Sheet Playwright

| Temat | Kluczowe informacje |
| --- | --- |
| Setup | `npm init playwright@latest` |
| Konfiguracja | `playwright.config.ts` + `defineConfig()` |
| Page Object | `class XxxPage extends BasePage` |
| Locatory | `page.getByRole()`, `getByLabel()`, `getByTestId()` |
| Fixtures | `test.extend<MyFixtures>({ fixture: async ({}, use) => { await use(value); } })` |
| Auth state | `page.context().storageState({ path: '.auth/user.json' })` |
| API testing | `request.get/post/put/delete()` z typowanymi response |
| Custom matchers | `expect.extend({ myMatcher() {} })` |
| `await` | ZAWSZE wymagane dla operacji Playwright! |
| `Promise.all` | Równoległe operacje – szybsze testy |
| Page Object Chain | Metody zwracają kolejny Page Object |
| `no-floating-promises` | ESLint wyłapie brakujący `await` |
| Selektory | Preferuj `getByRole`, `getByLabel`, `getByTestId` nad CSS |
| Timeouty | Konfiguruj w `playwright.config.ts`, nie `waitForTimeout` |

---

*Notatki dotyczą TypeScript 5.x · Playwright 1.4x+*

> **Zakres:** `page.evaluate()` i granica Node.js ↔︎ przeglądarka · Type Guards dla elementów DOM · `!` vs `?.` vs `expect().toBeVisible()` – kiedy co stosować
> 

---

## U1. `page.evaluate()` – granica między Node.js a przeglądarką

### Kluczowy koncept: dwa oddzielne środowiska

Playwright działa w dwóch oddzielnych środowiskach wykonawczych jednocześnie:

```
┌─────────────────────────────┐      ┌──────────────────────────────┐
│        Node.js              │      │        Przeglądarka          │
│                             │      │                              │
│  Twój kod testowy (TS)      │ ─── ▶│  page.evaluate(() => { })    │
│  Page Objects, fixtures     │      │  Kod JS w kontekście DOM     │
│  await page.click(...)      │      │  window, document, DOM APIs  │
└─────────────────────────────┘      └──────────────────────────────┘
         Protokół CDP (Chrome DevTools Protocol)
```

**Kluczowa zasada:** funkcja przekazana do `page.evaluate()` jest **serializowana** (zamieniana na string) i wykonywana po drugiej stronie granicy. Oznacza to:

```tsx
// ❌ Closures NIE działają ponad granicą Node.js ↔ przeglądarka:
const multiplier = 10; // zmienna w Node.js

const result = await page.evaluate(() => {
  // Ta funkcja jest IZOLOWANA od Node.js!
  // multiplier tutaj nie istnieje – ReferenceError w runtime!
  return multiplier * 5; // ❌ crash
});

// ✅ Przekaż zmienne jako argument:
const result = await page.evaluate((mult) => {
  return mult * 5; // ✅ mult = 10 – przekazane przez CDP
}, multiplier);

// ✅ Przekazywanie wielu wartości – jako obiekt lub tablica:
const result2 = await page.evaluate(({ a, b }) => {
  return a * b;
}, { a: 5, b: multiplier });
```

> **💡 Dla programisty Java:** To jakbyś wysyłał bytecode do zdalnej JVM przez sieć – nie możesz używać lokalnych zmiennych, musisz je jawnie przesłać. Pomyśl o tym jak o Remote Procedure Call (RPC).
> 

### Typowanie `page.evaluate()`

```tsx
import { Page } from '@playwright/test';

// page.evaluate<ReturnType, ArgType>(fn, arg?)
// TypeScript inferuje typy automatycznie gdy jest to możliwe:

// Prosty zwrot:
const title = await page.evaluate(() => document.title);
// title: string ✅

// Złożony zwrot z typem:
interface ElementInfo {
  tagName: string;
  isVisible: boolean;
  boundingBox: { x: number; y: number; width: number; height: number } | null;
}

const info = await page.evaluate<ElementInfo>(() => {
  const el = document.querySelector('.my-element');
  if (!el) return { tagName: '', isVisible: false, boundingBox: null };

  const rect = el.getBoundingClientRect();
  return {
    tagName:     el.tagName,
    isVisible:   rect.width > 0 && rect.height > 0,
    boundingBox: { x: rect.x, y: rect.y, width: rect.width, height: rect.height },
  };
});

// info.tagName:    string ✅
// info.isVisible:  boolean ✅
// info.boundingBox: { x, y, width, height } | null ✅
```

### Serializacja argumentów – co można przekazać

Argumenty przekazywane do `page.evaluate()` muszą być **serializowalne przez JSON**. To ograniczenie podobne do `JSON.stringify()`.

```tsx
// ✅ Można przekazać:
await page.evaluate((data) => { /* ... */ }, {
  text:    'hello',
  number:  42,
  boolean: true,
  array:   [1, 2, 3],
  nested:  { a: { b: 1 } },
});

// ❌ Nie można przekazać (nie serializowalne):
const fn = () => 'test';
// await page.evaluate((f) => f(), fn); // ❌ funkcje nie są serializowalne

const map = new Map([['key', 'value']]);
// await page.evaluate((m) => m.get('key'), map); // ❌ Map nie jest JSON

// ❌ Nie można przekazać instancji klasy (tylko plain object):
class Point { constructor(public x: number, public y: number) {} }
const p = new Point(1, 2);
// await page.evaluate((point) => point.x, p); // ❌ klasa

// ✅ Obejście – plain object:
await page.evaluate((point) => point.x, { x: p.x, y: p.y }); // ✅
```

### `page.evaluate()` vs `page.evaluateHandle()`

```tsx
// page.evaluate() – zwraca zwykłą wartość JS (serializowaną z przeglądarki):
const text = await page.evaluate(() => document.title); // string

// page.evaluateHandle() – zwraca uchwyt do obiektu przeglądarki (nie serializuje):
const bodyHandle = await page.evaluateHandle(() => document.body);
// bodyHandle: JSHandle<HTMLBodyElement>

// Kiedy używać evaluateHandle:
// - gdy chcesz operować na obiekcie DOM dalej w Playwright
// - gdy obiekt nie jest serializowalny (nie da się go przesłać do Node.js)

const element = await page.evaluateHandle(() =>
  document.querySelector('#my-element')
);

// Możesz potem użyć uchwytu w innych metodach:
await element.asElement()?.click();
```

### `page.exposeFunction()` – wywołanie Node.js z przeglądarki

```tsx
// Eksponowanie funkcji Node.js do kodu przeglądarki:
await page.exposeFunction('readToken', () => {
  return process.env.API_TOKEN; // kod Node.js
});

// Teraz w evaluate możesz wywołać tę funkcję:
const token = await page.evaluate(async () => {
  // @ts-ignore – funkcja jest eksponowana globalnie
  return await window.readToken(); // wywołuje Node.js!
});
```

---

## U2. Type Guards dla elementów DOM

### Problem: generyczny `Locator` vs konkretny element HTML

Playwright zwraca `Locator` – abstrakcję nad elementem DOM. Gdy używasz `page.evaluate()`, dostajesz dostęp do konkretnych elementów HTML z pełną hierarchią typów.

```tsx
// W przeglądarce element może być różnego typu:
// HTMLInputElement, HTMLButtonElement, HTMLSelectElement, HTMLAnchorElement...
// Wszystkie dziedziczą po HTMLElement → Element → Node → EventTarget

// Bez type guard – ogólny HTMLElement:
const value = await page.evaluate((selector) => {
  const el = document.querySelector(selector);
  // el: Element | null – brak dostępu do .value (pole InputElement)!
  return (el as HTMLInputElement).value; // wymaga rzutowania
}, '#email');
```

### Własne Type Guards dla elementów DOM

```tsx
// Type Guards dla typów HTML:
function isHTMLInput(el: Element | null): el is HTMLInputElement {
  return el !== null && el.tagName === 'INPUT';
}

function isHTMLSelect(el: Element | null): el is HTMLSelectElement {
  return el !== null && el.tagName === 'SELECT';
}

function isHTMLAnchor(el: Element | null): el is HTMLAnchorElement {
  return el !== null && el.tagName === 'A';
}

function isHTMLButton(el: Element | null): el is HTMLButtonElement {
  return el !== null && el.tagName === 'BUTTON';
}

// Alternatywnie – sprawdzanie przez instanceof (bezpieczniejsze):
function isInput(el: Element | null): el is HTMLInputElement {
  return el instanceof HTMLInputElement;
}

// Użycie w page.evaluate():
const inputValue = await page.evaluate((selector) => {
  const el = document.querySelector(selector);

  if (isHTMLInput(el)) {
    return el.value;      // ✅ el: HTMLInputElement – mamy dostęp do .value
  }
  if (isHTMLSelect(el)) {
    return el.value;      // ✅ el: HTMLSelectElement
  }

  return null;
}, '#my-field');
```

> **⚠️ Uwaga:** Type Guards zdefiniowane w Node.js **nie działają** wewnątrz `page.evaluate()` – granica środowisk! Musisz je zdefiniować bezpośrednio wewnątrz funkcji `evaluate` lub użyć `instanceof` (które działa w obu środowiskach).
> 

```tsx
// ❌ Type Guard z Node.js niedostępny w przeglądarce:
function isHTMLInput(el: Element | null): el is HTMLInputElement {
  return el instanceof HTMLInputElement; // działa w Node.js
}

const value = await page.evaluate((selector) => {
  const el = document.querySelector(selector);
  if (isHTMLInput(el)) { /* ... */ } // ❌ isHTMLInput nie istnieje w przeglądarce!
}, '#email');

// ✅ Definiuj type guard wewnątrz evaluate:
const value = await page.evaluate((selector) => {
  const el = document.querySelector(selector);
  if (el instanceof HTMLInputElement) { // instanceof działa natywnie
    return el.value; // ✅
  }
  return null;
}, '#email');
```

### Type Guards dla Locatorów – kiedy można je używać

W odróżnieniu od `page.evaluate()`, Playwright `Locator` API pracuje po stronie Node.js – tutaj type guards działają normalnie:

```tsx
import { Locator, Page } from '@playwright/test';

// Type guard sprawdzający stan locatora:
async function isLocatorVisible(locator: Locator): Promise<boolean> {
  try {
    await locator.waitFor({ state: 'visible', timeout: 1000 });
    return true;
  } catch {
    return false;
  }
}

// Type guard dla wyniku API:
function isSuccessResponse<T>(
  response: { ok: boolean; data?: T; error?: string }
): response is { ok: true; data: T } {
  return response.ok && response.data !== undefined;
}

// Sprawdzenie atrybutu elementu – bezpieczne typowanie:
async function getInputType(locator: Locator): Promise<string | null> {
  const el = await locator.elementHandle();
  if (!el) return null;

  // elementHandle zwraca JSHandle – evaluate po stronie przeglądarki:
  return el.evaluate((node) => {
    if (node instanceof HTMLInputElement) {
      return node.type; // 'text', 'email', 'password', 'checkbox', ...
    }
    return null;
  });
}
```

---

## U3. `!` vs `?.` vs `expect().toBeVisible()` – kiedy co w testach

### Trzy podejścia do “element musi istnieć”

W testach Playwright często wiesz (lub zakładasz), że element istnieje. Masz trzy narzędzia – każde z innym poziomem bezpieczeństwa:

```tsx
// Podejście 1: Non-null assertion operator (!)
// Mówisz TS: "ufaj mi, to nie jest null"
const text = await page.locator('.title').textContent()!;
// ↑ Brak błędu TS, ale CRASH w runtime jeśli .title nie istnieje!

// Podejście 2: Optional chaining (?.)
const text = await page.locator('.title').textContent() ?? '';
// ↑ Bezpieczne – zwraca '' zamiast crasha, ale test nie zgłosi błędu!

// Podejście 3: Playwright assertion (expect)
await expect(page.locator('.title')).toBeVisible();
const text = await page.locator('.title').textContent();
// ↑ Test FAIL z czytelnym komunikatem jeśli elementu nie ma ✅
```

### Kiedy używać `!` (non-null assertion)

`!` jest uzasadnione tylko gdy:
1. Playwright już potwierdził istnienie elementu przez asercję
2. Używasz go na wyniku `page.evaluate()` gdzie logika gwarantuje wartość
3. Piszesz helper, który zostanie wywołany po wcześniejszej asercji

```tsx
// ✅ Uzasadnione użycie ! – po wcześniejszej asercji Playwright:
await expect(page.locator('.user-name')).toBeVisible(); // najpierw asercja!
const name = await page.locator('.user-name').textContent(); // może być null...
const upperName = name!.toUpperCase(); // ! uzasadnione – asercja gwarantuje obecność

// ✅ Uzasadnione ! – evaluate z logiką gwarantującą wartość:
const count = await page.evaluate(() => {
  const list = document.querySelector('ul');
  if (!list) throw new Error('Lista nie istnieje'); // throw zamiast null
  return list.children.length; // gwarantowane – list istnieje
});
// count: number (nie number | null)

// ❌ Nieuzasadnione ! – potencjalny crash:
const badText = await page.locator('.might-not-exist').textContent()!;
// Jeśli element nie istnieje – crash zamiast czytelnego błędu testu
```

### Kiedy używać `?.` (optional chaining)

`?.` jest dobre gdy brak wartości jest **poprawnym scenariuszem testowym**:

```tsx
// ✅ ?. gdy element jest opcjonalny (np. badge z liczbą powiadomień):
const notificationCount = await page.locator('.badge').textContent() ?? '0';
// Brak badge = 0 powiadomień – to poprawna sytuacja

// ✅ ?. w helperach ogólnego przeznaczenia:
async function safeGetText(page: Page, selector: string): Promise<string> {
  return await page.locator(selector).textContent() ?? '';
}

// ✅ ?. przy opcjonalnym atrybucie:
const href = await page.locator('a.logo').getAttribute('href') ?? '/';
```

### Kiedy używać `expect().toBeVisible()` / `expect().toHaveText()`

**Zawsze w testach** gdy element MUSI istnieć. Asercja Playwright:
- Czeka automatycznie (auto-wait do `actionTimeout`)
- Daje czytelny komunikat błędu (co się stało, gdzie, dlaczego)
- Zatrzymuje test w czytelnym miejscu

```tsx
// ✅ Najlepsze podejście w testach – asercja + operator:
test('wyświetla dane użytkownika', async ({ page }) => {
  await page.goto('/profile');

  // Asercja sprawdza i czeka jednocześnie:
  await expect(page.locator('.user-name')).toBeVisible();
  await expect(page.locator('.user-email')).toHaveText(/^.+@.+\..+$/);

  // Po asercji – możemy używać ! lub ?? z pełną świadomością:
  const name  = await page.locator('.user-name').textContent() ?? '';
  const email = await page.locator('.user-email').textContent() ?? '';

  // Dalsze operacje na wartościach:
  expect(name.length).toBeGreaterThan(0);
  expect(email).toContain('@');
});
```

### Zestawienie – kiedy co stosować

| Sytuacja | Narzędzie | Uzasadnienie |
| --- | --- | --- |
| Element MUSI istnieć w teście | `expect(locator).toBeVisible()` | Czytelny błąd, auto-wait |
| Element jest opcjonalny (może go nie być) | `?? wartość_domyślna` | Bezpieczny fallback |
| Już sprawdziłeś asercją, teraz operujesz na wartości | `textContent()!` lub `?? ''` | Uzasadnione po asercji |
| Piszesz helper ogólny (nie wiesz czy element istnieje) | `?.` i `?? ''` | Defensywne programowanie |
| Wynik z `page.evaluate()` z gwarancją | `!` | Logika w evaluate gwarantuje wartość |
| Debugujesz / szybki skrypt | `!` | Akceptowalne tymczasowo |

### Praktyczny wzorzec – “sprawdź potem używaj”

```tsx
// Wzorzec: najpierw asercja (Playwright czeka i weryfikuje),
// potem wyciąganie wartości (możemy być pewni istnienia):
class UserProfilePage extends BasePage {
  private readonly nameLocator    = this.page.locator('[data-testid="user-name"]');
  private readonly emailLocator   = this.page.locator('[data-testid="user-email"]');
  private readonly avatarLocator  = this.page.locator('[data-testid="avatar"]');

  // Metoda asercji – weryfikuje stan strony:
  async expectLoaded(): Promise<void> {
    await expect(this.nameLocator).toBeVisible();
    await expect(this.emailLocator).toBeVisible();
  }

  // Metoda danych – zakłada, że expectLoaded() było wywołane wcześniej:
  async getUserData(): Promise<{ name: string; email: string }> {
    // ?? '' – bezpieczny fallback, ale po expectLoaded() nie powinno być null:
    const name  = await this.nameLocator.textContent()  ?? '';
    const email = await this.emailLocator.textContent() ?? '';
    return { name, email };
  }

  // Opcjonalne pole – może nie istnieć:
  async getAvatarUrl(): Promise<string | null> {
    const isVisible = await this.avatarLocator.isVisible();
    if (!isVisible) return null;
    return this.avatarLocator.getAttribute('src');
  }
}

// Użycie w teście – czytelny przepływ:
test('profil użytkownika wyświetla dane', async ({ page }) => {
  const profile = new UserProfilePage(page);
  await profile.goto();

  // Najpierw asercja – Playwright czeka i weryfikuje:
  await profile.expectLoaded();

  // Potem wyciągamy dane – bezpiecznie po asercji:
  const { name, email } = await profile.getUserData();

  expect(name).toBe('Jan Kowalski');
  expect(email).toBe('jan@example.com');

  const avatarUrl = await profile.getAvatarUrl();
  // avatarUrl może być null – to nie błąd
  if (avatarUrl) {
    expect(avatarUrl).toContain('/avatars/');
  }
});
```

---

## Podsumowanie – Cheat Sheet uzupełnienia

| Temat | Kluczowa zasada |
| --- | --- |
| `page.evaluate()` | Kod wykonuje się w przeglądarce – oddzielny kontekst od Node.js |
| Closures w evaluate | ❌ Nie działają! Przekazuj zmienne jako argument |
| Serializacja argumentów | Tylko JSON-serializowalne wartości (nie: funkcje, Map, Set, klasy) |
| `page.evaluate()` zwraca | Wartość JSON (serializowana z przeglądarki do Node.js) |
| `page.evaluateHandle()` | Uchwyt do obiektu – nie serializuje, operujesz na nim dalej |
| Type Guards w evaluate | Używaj `instanceof` bezpośrednio wewnątrz evaluate |
| Type Guards po stronie Node | Działają normalnie dla Locator, APIResponse itp. |
| `!` (non-null assertion) | Tylko po asercji Playwright lub z gwarantowaną wartością |
| `?.` + `??` | Dla opcjonalnych elementów i defensywnych helperów |
| `expect().toBeVisible()` | Zawsze gdy element MUSI istnieć – auto-wait + czytelny błąd |
| Wzorzec: “sprawdź potem używaj” | `expectLoaded()` → `getData()` – asercja osobno od wyciągania danych |

---

*Uzupełnienie do rozdziału 10 · TypeScript 5.x · Playwright 1.4x+*
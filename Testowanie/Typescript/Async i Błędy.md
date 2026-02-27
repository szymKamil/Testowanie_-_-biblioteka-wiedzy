# TypeScript – Uzupełnienie: Asynchroniczność i obsługa błędów w testach

> **Zakres:** `Promise<T>` w głąb · Łańcuchy `.then()` · `Promise.all/allSettled/race/any` · Własne klasy błędów · `try/catch/finally` w testach · Stack traces i debugowanie

---

## A1. `Promise<T>` – mechanizm w głąb

### Trzy stany Promise

```ts
// Promise<T> to kontener na wartość, która pojawi się w przyszłości:
//
//  pending  ──resolve(value)──▶  fulfilled  (wartość: T)
//           ──reject(reason)──▶  rejected   (błąd)
//
// Po przejściu do fulfilled/rejected – stan jest niezmienialny

const p = new Promise<string>((resolve, reject) => {
  // Symulacja operacji asynchronicznej:
  setTimeout(() => {
    if (Math.random() > 0.5) {
      resolve("Sukces!"); // ▶ fulfilled, wartość: 'Sukces!'
    } else {
      reject(new Error("Błąd")); // ▶ rejected, powód: Error
    }
  }, 1000);
});
```

### Metody statyczne Promise

```ts
// Promise.resolve() – natychmiast fulfilled:
const immediate = Promise.resolve(42); // Promise<number>, już fulfilled

// Promise.reject() – natychmiast rejected:
const failed = Promise.reject(new Error("od razu błąd"));

// Praktyczne użycie w testach – mock async funkcji:
jest.spyOn(userService, "getUser").mockResolvedValue({ id: 1, name: "Jan" });
// mockResolvedValue to skrót dla: jest.fn().mockReturnValue(Promise.resolve(...))
```

### Łańcuchy `.then()` / `.catch()` / `.finally()`

```ts
// Każda metoda .then() zwraca NOWY Promise:
fetchUser(1)
  .then((user) => {
    // user: User
    return user.name.toUpperCase(); // zwraca string → następny .then dostaje string
  })
  .then((upperName) => {
    // upperName: string
    console.log(upperName);
    return upperName.length; // zwraca number
  })
  .then((len) => {
    // len: number
    console.log(`Długość: ${len}`);
  })
  .catch((err) => {
    // łapie błędy z CAŁEGO łańcucha
    console.error("Gdzieś wystąpił błąd:", err);
  })
  .finally(() => {
    // zawsze wykonywany (jak finally w Java)
    console.log("Operacja zakończona");
  });
```

> **💡 Dla programisty Java:** Łańcuch `.then()` to odpowiednik `.thenApply()`, `.thenAccept()` z `CompletableFuture`. `.catch()` to `.exceptionally()`. `.finally()` to `.whenComplete()`.

### `async/await` – to samo, czytelniej

```ts
// Equivalent do łańcucha powyżej:
async function processUser(id: number): Promise<void> {
  try {
    const user = await fetchUser(id);
    const upperName = user.name.toUpperCase();
    console.log(upperName);
    console.log(`Długość: ${upperName.length}`);
  } catch (err) {
    console.error("Gdzieś wystąpił błąd:", err);
  } finally {
    console.log("Operacja zakończona");
  }
}

// async funkcja ZAWSZE zwraca Promise:
async function getValue(): Promise<number> {
  return 42; // automatycznie: Promise.resolve(42)
}

// ⚠️ Najczęstszy błąd w Playwright – brakujący await:
async function badTest(page: Page) {
  page.click("#button"); // ❌ brak await – nie czeka!
  page.waitForURL("/next"); // ❌ uruchamia się natychmiast, nie po kliknięciu
}

async function goodTest(page: Page) {
  await page.click("#button"); // ✅
  await page.waitForURL("/next"); // ✅ czeka na kliknięcie
}
```

---

## A2. Kombinatory Promise

### `Promise.all` – wszystko albo nic

```ts
// Uruchamia równolegle, czeka na WSZYSTKIE, fail-fast przy błędzie:
const [user, posts, settings] = await Promise.all([
  fetchUser(1), // Promise<User>
  fetchPosts(1), // Promise<Post[]>
  fetchSettings(1), // Promise<Settings>
]);
// Typy inferowane: [User, Post[], Settings] ✅

// Jeśli JEDEN Promise się nie powiedzie → cały Promise.all odrzucony:
try {
  const results = await Promise.all([
    Promise.resolve("ok"),
    Promise.reject(new Error("błąd")),
    Promise.resolve("też ok"),
  ]);
} catch (err) {
  // err: Error – dostajemy tylko pierwszy błąd, reszta ignorowana
}

// Playwright – równoległe operacje (szybsze testy):
const [loginVisible, titleText] = await Promise.all([
  expect(page.locator("#login")).toBeVisible(),
  page.title(),
]);
```

### `Promise.allSettled` – poczekaj na wszystkie, niezależnie od błędów

```ts
// Nie fail-fast – czeka na WSZYSTKIE, zwraca statusy:
const results = await Promise.allSettled([
  fetchUser(1),
  fetchUser(999), // ten może zawieść
  fetchUser(2),
]);

results.forEach((result, index) => {
  if (result.status === "fulfilled") {
    console.log(`User ${index}:`, result.value); // User
  } else {
    console.error(`Błąd ${index}:`, result.reason); // Error
  }
});

// TypeScript zna typ każdego wyniku:
// result: PromiseFulfilledResult<User> | PromiseRejectedResult

// Playwright – sprawdzanie wielu elementów niezależnie:
const checks = await Promise.allSettled([
  expect(page.locator(".header")).toBeVisible(),
  expect(page.locator(".footer")).toBeVisible(),
  expect(page.locator(".sidebar")).toBeVisible(),
]);
const failures = checks.filter((r) => r.status === "rejected");
console.log(`${failures.length} elementów niewidocznych`);
```

### `Promise.race` – pierwszy wygrywa

```ts
// Zwraca wynik pierwszego zakończonego Promise (fulfilled LUB rejected):
const result = await Promise.race([
  fetchUser(1),
  new Promise<never>((_, reject) =>
    setTimeout(() => reject(new Error("Timeout!")), 5000),
  ),
]);
// Jeśli fetchUser skończy w 2s → result: User
// Jeśli minie 5s → Error: Timeout!

// Praktyczny pattern – timeout dla operacji:
function withTimeout<T>(promise: Promise<T>, ms: number): Promise<T> {
  return Promise.race([
    promise,
    new Promise<never>((_, reject) =>
      setTimeout(() => reject(new Error(`Timeout po ${ms}ms`)), ms),
    ),
  ]);
}

// Playwright – własny timeout (rzadko potrzebny – PW ma wbudowany):
const user = await withTimeout(fetchUser(1), 3000);
```

### `Promise.any` – pierwszy sukces

```ts
// Zwraca pierwszy FULFILLED (ignoruje rejected):
// Jeśli WSZYSTKIE odrzucone → AggregateError
const fastestServer = await Promise.any([
  fetch("https://server1.example.com/api"),
  fetch("https://server2.example.com/api"),
  fetch("https://server3.example.com/api"),
]);
// Zwraca odpowiedź najszybszego serwera
```

### Porównanie kombinatorów

| Metoda               | Czeka na                   | Fail gdy        | Zwraca                   |
| -------------------- | -------------------------- | --------------- | ------------------------ |
| `Promise.all`        | Wszystkie                  | Pierwszy błąd   | `T[]` (tuple)            |
| `Promise.allSettled` | Wszystkie                  | Nigdy           | `PromiseSettledResult[]` |
| `Promise.race`       | Pierwszy (sukces lub błąd) | Pierwszy błąd   | `T`                      |
| `Promise.any`        | Pierwszy sukces            | Wszystkie błędy | `T`                      |

---

## A3. Obsługa błędów – `try/catch/finally`

### Podstawy w kontekście testów

```ts
// catch(err) – err ma typ unknown (od TS 4.0):
async function safeApiCall<T>(url: string): Promise<T | null> {
  try {
    const response = await fetch(url);

    if (!response.ok) {
      throw new Error(`HTTP ${response.status}: ${response.statusText}`);
    }

    return (await response.json()) as T;
  } catch (err: unknown) {
    // ❌ Stary styl – err: any (niebezpieczne):
    // console.error(err.message);

    // ✅ Sprawdź typ przed użyciem:
    if (err instanceof Error) {
      console.error(`Błąd: ${err.message}`);
      console.error(`Stack: ${err.stack}`);
    } else {
      console.error("Nieznany błąd:", err);
    }

    return null;
  } finally {
    // Zawsze wykonywany – cleanup, zamykanie połączeń, logowanie:
    console.log(`Zapytanie do ${url} zakończone`);
  }
}
```

### `instanceof` i hierarchia Error

```ts
// Wbudowane typy błędów – dziedziczą po Error:
// Error
// ├── TypeError       (zły typ wartości)
// ├── RangeError      (wartość poza zakresem)
// ├── ReferenceError  (niezadeklarowana zmienna)
// ├── SyntaxError     (błąd składni)
// └── URIError        (błąd URI)

try {
  JSON.parse("invalid json");
} catch (err: unknown) {
  if (err instanceof SyntaxError) {
    console.error("Błędny JSON:", err.message);
    // err.name:    'SyntaxError'
    // err.message: opis błędu
    // err.stack:   stack trace
  }
}

// Sprawdzanie stack trace – przydatne przy debugowaniu testów:
function logError(err: unknown): void {
  if (err instanceof Error) {
    console.error("─".repeat(40));
    console.error(`Typ:     ${err.name}`);
    console.error(`Wiadomość: ${err.message}`);
    if (err.stack) {
      console.error("Stack trace:");
      console.error(err.stack.split("\n").slice(1, 5).join("\n"));
    }
  }
}
```

### Własne klasy błędów – wzorzec dla frameworku testowego

```ts
// Bazowa klasa błędu:
class AppError extends Error {
  constructor(
    message: string,
    public readonly code: string,
    public readonly statusCode: number = 500,
  ) {
    super(message);
    this.name = this.constructor.name; // 'AppError', 'ValidationError' itp.

    // ⚠️ Wymagane w TypeScript – naprawia instanceof w łańcuchu dziedziczenia:
    Object.setPrototypeOf(this, new.target.prototype);
  }
}

// Specyficzne klasy błędów:
class ValidationError extends AppError {
  constructor(
    message: string,
    public readonly fields: Record<string, string>,
  ) {
    super(message, "VALIDATION_ERROR", 400);
  }
}

class NotFoundError extends AppError {
  constructor(resource: string, id: number | string) {
    super(`${resource} o id=${id} nie istnieje`, "NOT_FOUND", 404);
  }
}

class TimeoutError extends AppError {
  constructor(operation: string, ms: number) {
    super(
      `Operacja "${operation}" przekroczyła timeout ${ms}ms`,
      "TIMEOUT",
      408,
    );
  }
}

// Użycie – pełna hierarchia instanceof działa:
try {
  throw new ValidationError("Nieprawidłowe dane", { email: "Zły format" });
} catch (err: unknown) {
  if (err instanceof ValidationError) {
    console.error("Pola z błędami:", err.fields);
    // err.fields: Record<string, string> ✅
  } else if (err instanceof AppError) {
    console.error(`[${err.code}]`, err.message);
  } else if (err instanceof Error) {
    console.error("Nieoczekiwany błąd:", err.message);
  }
}
```

### Własne błędy w testach Playwright

```ts
// Klasy błędów dla frameworku testowego:
class PageNotLoadedError extends AppError {
  constructor(url: string, timeout: number) {
    super(
      `Strona ${url} nie załadowała się w ciągu ${timeout}ms`,
      "PAGE_NOT_LOADED",
    );
  }
}

class ElementNotFoundError extends AppError {
  constructor(selector: string) {
    super(`Element "${selector}" nie został znaleziony`, "ELEMENT_NOT_FOUND");
  }
}

class AssertionError extends AppError {
  constructor(expected: unknown, actual: unknown, context?: string) {
    super(
      `Asercja nieudana${context ? ` (${context})` : ""}: ` +
        `oczekiwano ${JSON.stringify(expected)}, ` +
        `otrzymano ${JSON.stringify(actual)}`,
      "ASSERTION_FAILED",
    );
  }
}

// Helper do bezpiecznego wykonania operacji Playwright:
async function safePlaywrightAction<T>(
  action: () => Promise<T>,
  errorMessage: string,
): Promise<T> {
  try {
    return await action();
  } catch (err: unknown) {
    if (err instanceof Error && err.message.includes("Timeout")) {
      throw new TimeoutError(errorMessage, 30_000);
    }
    throw err; // re-throw innych błędów
  }
}

// Użycie w Page Object:
class LoginPage {
  constructor(private page: Page) {}

  async login(email: string, password: string): Promise<void> {
    await safePlaywrightAction(
      () => this.page.fill("#email", email),
      "Wypełnienie pola email",
    );
    await safePlaywrightAction(
      () => this.page.fill("#password", password),
      "Wypełnienie pola hasło",
    );
    await safePlaywrightAction(
      () => this.page.click('button[type="submit"]'),
      "Kliknięcie przycisku logowania",
    );
  }
}
```

### Wzorzec Result – alternatywa dla wyjątków

```ts
// W testach często lepiej zwrócić Result niż rzucać wyjątek:
type Ok<T> = { ok: true; value: T };
type Err<E> = { ok: false; error: E };
type Result<T, E = Error> = Ok<T> | Err<E>;

const ok = <T>(value: T): Ok<T> => ({ ok: true, value });
const err = <E>(error: E): Err<E> => ({ ok: false, error });

async function tryNavigate(
  page: Page,
  url: string,
): Promise<Result<void, string>> {
  try {
    await page.goto(url, { waitUntil: "networkidle" });
    return ok(undefined);
  } catch (e: unknown) {
    return err(e instanceof Error ? e.message : "Nieznany błąd");
  }
}

// Użycie – bez try/catch w kodzie wywołującym:
const result = await tryNavigate(page, "/dashboard");
if (!result.ok) {
  console.error(`Nawigacja nieudana: ${result.error}`);
  await page.screenshot({ path: "failure.png" });
}
```

### `error.cause` – łańcuchowanie błędów (ES2022)

```ts
// Zachowaj oryginalny błąd jako przyczynę:
async function fetchUserData(id: number): Promise<User> {
  try {
    const response = await fetch(`/api/users/${id}`);
    return response.json();
  } catch (originalError) {
    throw new Error(
      `Nie można pobrać użytkownika ${id}`,
      { cause: originalError }, // ← łańcuch przyczynowy
    );
  }
}

try {
  await fetchUserData(1);
} catch (err: unknown) {
  if (err instanceof Error) {
    console.error(err.message); // 'Nie można pobrać użytkownika 1'
    console.error(err.cause); // oryginalny błąd sieciowy
  }
}
```

---

## A4. Uwaga o dekoratorach w Playwright

> **Pytanie:** Czy Playwright używa dekoratorów `@Test`, `@Describe`, `@Before` jak JUnit?

**Odpowiedź: Nie.** To częste nieporozumienie dla programistów Java.

Playwright używa **zwykłych funkcji**, nie dekoratorów:

```ts
// ❌ Playwright NIE używa stylu JUnit/dekoratorów:
// @Describe('Logowanie')
// class LoginTests {
//   @Test('poprawne logowanie')
//   async testLogin() { }
//
//   @BeforeEach
//   async setup() { }
// }

// ✅ Playwright używa funkcji:
import { test, expect } from "@playwright/test";

test.describe("Logowanie", () => {
  test.beforeEach(async ({ page }) => {
    await page.goto("/login");
  });

  test("poprawne logowanie", async ({ page }) => {
    await page.fill("#email", "user@example.com");
    await page.fill("#password", "password");
    await page.click('button[type="submit"]');
    await expect(page).toHaveURL("/dashboard");
  });
});
```

Dekoratory w TypeScript (`@Injectable`, `@Component`, `@Controller`) są używane w:

- **Angular** – komponenty, serwisy
- **NestJS** – kontrolery, moduły
- **TypeORM** – encje bazy danych

Jeśli pracujesz z Playwright – dekoratory nie są Ci potrzebne.

---

## Podsumowanie – Cheat Sheet

| Temat                    | Składnia                                                                                                              |
| ------------------------ | --------------------------------------------------------------------------------------------------------------------- |
| Tworzenie Promise        | `new Promise<T>((resolve, reject) => { })`                                                                            |
| Natychmiastowy sukces    | `Promise.resolve(value)`                                                                                              |
| Natychmiastowy błąd      | `Promise.reject(new Error('...'))`                                                                                    |
| Wszystkie równolegle     | `await Promise.all([p1, p2, p3])`                                                                                     |
| Wszystkie, ignoruj błędy | `await Promise.allSettled([p1, p2])`                                                                                  |
| Najszybszy               | `await Promise.race([p1, p2])`                                                                                        |
| Pierwszy sukces          | `await Promise.any([p1, p2])`                                                                                         |
| Async funkcja            | `async function f(): Promise<T>`                                                                                      |
| Czekaj na Promise        | `const result = await somePromise`                                                                                    |
| Obsługa błędów           | `try { await ... } catch (err: unknown) { }`                                                                          |
| Sprawdzenie typu błędu   | `if (err instanceof Error)`                                                                                           |
| Własna klasa błędu       | `class MyError extends Error { constructor(...) { super(...); Object.setPrototypeOf(this, new.target.prototype); } }` |
| Łańcuch przyczynowy      | `throw new Error('msg', { cause: originalError })`                                                                    |
| Result pattern           | `type Result<T> = Ok<T> \| Err<Error>`                                                                                |

---

_Uzupełnienie do rozdziałów 3 i 10 · TypeScript 5.x · Playwright 1.4x+_

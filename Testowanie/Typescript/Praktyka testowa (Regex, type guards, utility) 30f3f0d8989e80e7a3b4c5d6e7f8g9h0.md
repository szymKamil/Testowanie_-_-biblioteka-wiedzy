# TypeScript – Uzupełnienie: Regex w testach · `typeof` vs `instanceof` vs `is`

> **Zakres:** Wyrażenia regularne w TypeScript · Praktyczne zastosowania w testach · Pełne porównanie operatorów zawężania typów

---

## R1. Wyrażenia regularne (Regex) w TypeScript

### Podstawowa składnia

```ts
// Dwa sposoby tworzenia regex:
const re1 = /hello/i; // literał (kompilowany – szybszy)
const re2 = new RegExp("hello", "i"); // konstruktor (dynamiczny)

// Flagi:
// i – case-insensitive
// g – global (znajdź wszystkie dopasowania)
// m – multiline (^ i $ dla każdej linii)
// s – dotAll (. pasuje też do \n)
// u – unicode
// d – indices (TS 4.5+ – zwraca indeksy dopasowań)

// Typy w TypeScript:
const pattern: RegExp = /\d+/;
```

### Metody – `test()`, `match()`, `exec()`, `replace()`

```ts
const email = "jan.kowalski@example.com";
const url = "https://example.com/users/123";

// test() – zwraca boolean (czy pasuje):
/^[\w.-]+@[\w.-]+\.\w+$/.test(email); // true
/^\d+$/.test("123abc"); // false

// match() – zwraca tablicę dopasowań lub null:
const matches = url.match(/\/users\/(\d+)/);
if (matches) {
  console.log(matches[0]); // '/users/123' – pełne dopasowanie
  console.log(matches[1]); // '123' – grupa przechwytująca
}
// Typ: RegExpMatchArray | null

// exec() – jak match(), ale z obsługą flag g:
const re = /\d+/g;
const text = "Port 8080, backup 3000";
let match: RegExpExecArray | null;
while ((match = re.exec(text)) !== null) {
  console.log(`Znaleziono: ${match[0]} na pozycji ${match.index}`);
  // 8080 na pozycji 5
  // 3000 na pozycji 20
}

// replace() – zamiana:
const cleaned = "Hello   World".replace(/\s+/g, " "); // 'Hello World'
const masked = "Jan Kowalski".replace(/(\w+)\s(\w+)/, "$2, $1");
// 'Kowalski, Jan'
```

### Named Capture Groups – typowane grupy

```ts
// Named groups – od ES2018, pełne wsparcie TS:
const dateRe = /(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})/;
const result = "2026-02-27".match(dateRe);

if (result?.groups) {
  const { year, month, day } = result.groups;
  // year: string, month: string, day: string
  console.log(`Rok: ${year}, Miesiąc: ${month}, Dzień: ${day}`);
}

// Praktyczny helper z typowaniem:
function parseDate(
  input: string,
): { year: string; month: string; day: string } | null {
  const match = input.match(/(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})/);
  if (!match?.groups) return null;
  const { year, month, day } = match.groups;
  return { year, month, day };
}
```

### Regex w testach Playwright – praktyczne wzorce

```ts
import { test, expect } from "@playwright/test";

test.describe("Asercje z Regex", () => {
  // 1. Sprawdzenie URL – toHaveURL z regex:
  test("URL pasuje do wzorca", async ({ page }) => {
    await page.goto("/users/42");

    // Playwright akceptuje string, regex lub predykat:
    await expect(page).toHaveURL(/\/users\/\d+/);
    await expect(page).toHaveURL(/^https?:\/\/.+\/users\/\d+$/);
  });

  // 2. Sprawdzenie tekstu – toHaveText z regex:
  test("tekst pasuje do wzorca", async ({ page }) => {
    // Dokładny tekst:
    await expect(page.locator("h1")).toHaveText("Witaj, Jan!");

    // Regex – np. ignoruj białe znaki, case-insensitive:
    await expect(page.locator(".status")).toHaveText(/^(active|inactive)$/i);

    // Tekst zawiera datę w formacie DD.MM.RRRR:
    await expect(page.locator(".date")).toHaveText(/\d{2}\.\d{2}\.\d{4}/);

    // Email w elemencie:
    await expect(page.locator(".email")).toHaveText(/^[\w.-]+@[\w.-]+\.\w+$/);
  });

  // 3. Sprawdzenie atrybutu:
  test("atrybut pasuje do wzorca", async ({ page }) => {
    // href zaczyna się od https:
    await expect(page.locator("a.external")).toHaveAttribute(
      "href",
      /^https:\/\//,
    );

    // src obrazu to URL z CDN:
    await expect(page.locator("img.avatar")).toHaveAttribute(
      "src",
      /^https:\/\/cdn\.example\.com\/.+\.(jpg|png|webp)$/,
    );
  });

  // 4. Filtrowanie elementów przez regex:
  test("filtrowanie locatorów przez tekst", async ({ page }) => {
    // Znajdź wszystkie linki z numerem ID w URL:
    const userLinks = page.locator("a").filter({
      hasText: /Użytkownik #\d+/,
    });

    const count = await userLinks.count();
    expect(count).toBeGreaterThan(0);
  });

  // 5. API response – sprawdzenie wartości:
  test("API zwraca email w poprawnym formacie", async ({ request }) => {
    const response = await request.get("/api/users/1");
    const user: { email: string } = await response.json();

    expect(user.email).toMatch(/^[\w.-]+@[\w.-]+\.\w+$/);
  });
});
```

### Własne helpery regex dla testów

```ts
// utils/regex.helpers.ts

export const PATTERNS = {
  EMAIL: /^[\w.+-]+@[\w-]+\.[a-z]{2,}$/i,
  URL: /^https?:\/\/([\w-]+\.)+[\w-]+(\/[\w-./?%&=]*)?$/,
  UUID: /^[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}$/i,
  ISO_DATE:
    /^\d{4}-\d{2}-\d{2}(T\d{2}:\d{2}:\d{2}(\.\d+)?(Z|[+-]\d{2}:\d{2}))?$/,
  PHONE_PL: /^(\+48\s?)?(\d{3}[\s-]?){3}$/,
  POSTAL_PL: /^\d{2}-\d{3}$/,
} as const;

// Type guard z regex:
export function isValidEmail(value: unknown): value is string {
  return typeof value === "string" && PATTERNS.EMAIL.test(value);
}

export function isValidUuid(value: unknown): value is string {
  return typeof value === "string" && PATTERNS.UUID.test(value);
}

// Użycie w testach:
test("odpowiedź API zawiera poprawne pola", async ({ request }) => {
  const response = await request.post("/api/users", {
    data: { name: "Jan", email: "jan@example.com" },
  });
  const user = await response.json();

  expect(user.email).toMatch(PATTERNS.EMAIL);
  expect(user.id).toMatch(PATTERNS.UUID);
  expect(user.createdAt).toMatch(PATTERNS.ISO_DATE);
});
```

---

## R2. `typeof` vs `instanceof` vs `is` – kompletne porównanie

### Przegląd mechanizmów

| Operator              | Gdzie działa | Sprawdza           | Zwraca                     |
| --------------------- | ------------ | ------------------ | -------------------------- |
| `typeof`              | Runtime + TS | Typ prymitywny     | `string` (nazwa typu)      |
| `instanceof`          | Runtime + TS | Łańcuch prototypów | `boolean`                  |
| `is` (type predicate) | Tylko TS     | Dowolny warunek    | `boolean` + zawężenie typu |
| `in`                  | Runtime + TS | Istnienie pola     | `boolean`                  |

### `typeof` – prymitywy i funkcje

```ts
// Co typeof może zwrócić:
// 'string' | 'number' | 'bigint' | 'boolean' | 'symbol'
// 'undefined' | 'object' | 'function'

function describe(value: unknown): string {
  switch (typeof value) {
    case "string":
      return `String: "${value}"`;
    case "number":
      return `Liczba: ${value}`;
    case "boolean":
      return `Boolean: ${value}`;
    case "undefined":
      return "Undefined";
    case "function":
      return `Funkcja: ${value.name}`;
    case "object":
      if (value === null) return "Null";
      if (Array.isArray(value)) return `Tablica (${value.length} el.)`;
      return "Obiekt";
    default:
      return "Inny typ";
  }
}

// ⚠️ Pułapki typeof:
typeof null; // 'object' – historyczny błąd JS! Nie typeof null === 'null'
typeof []; // 'object' – nie 'array'!
typeof new Date(); // 'object'
typeof function () {}; // 'function'

// Zawężanie w TypeScript:
function process(value: string | number | null) {
  if (typeof value === "string") {
    value.toUpperCase(); // value: string ✅
  } else if (typeof value === "number") {
    value.toFixed(2); // value: number ✅
  } else {
    value; // value: null ✅
  }
}
```

### `instanceof` – klasy i hierarchia

```ts
// instanceof sprawdza łańcuch prototypów – czy obiekt jest instancją klasy:
class Animal {
  constructor(public name: string) {}
}
class Dog extends Animal {
  bark() {
    return "Hau!";
  }
}
class Cat extends Animal {
  meow() {
    return "Miau!";
  }
}

const dog = new Dog("Rex");

dog instanceof Dog; // true – bezpośrednia klasa
dog instanceof Animal; // true – klasa bazowa (jak isInstance w Javie)
dog instanceof Cat; // false

// Zawężanie z instanceof:
function makeSound(animal: Dog | Cat): string {
  if (animal instanceof Dog) {
    return animal.bark(); // animal: Dog ✅
  }
  return animal.meow(); // animal: Cat ✅
}

// Kluczowe dla obsługi błędów:
try {
  await riskyOperation();
} catch (err: unknown) {
  if (err instanceof TypeError) {
    console.error("Błąd typu:", err.message);
  } else if (err instanceof RangeError) {
    console.error("Poza zakresem:", err.message);
  } else if (err instanceof Error) {
    console.error("Błąd ogólny:", err.message);
  }
  // Kolejność ważna! Bardziej szczegółowe klasy pierwsze
}

// ⚠️ instanceof nie działa z interfejsami (znikają po kompilacji):
interface Serializable {
  serialize(): string;
}
class User implements Serializable {
  serialize() {
    return "{}";
  }
}

const u = new User();
// u instanceof Serializable; // ❌ błąd! interfejs nie istnieje runtime
u instanceof User; // ✅ klasa istnieje
```

### Type Predicate (`is`) – własna logika zawężania

```ts
// Składnia: parametr is Typ
// Mówi TypeScript: "jeśli ta funkcja zwraca true, parametr ma podany typ"

// Podstawowy type predicate:
function isString(value: unknown): value is string {
  return typeof value === "string";
}

function isUser(value: unknown): value is User {
  return (
    typeof value === "object" &&
    value !== null &&
    "id" in value &&
    typeof (value as Record<string, unknown>).id === "number" &&
    "name" in value &&
    typeof (value as Record<string, unknown>).name === "string"
  );
}

// Użycie:
const data: unknown = await response.json();
if (isUser(data)) {
  data.name; // data: User ✅
  data.id; // data: User ✅
}

// Type predicate filtrujący tablice – bardzo przydatne:
const values: (string | null | undefined)[] = ["a", null, "b", undefined, "c"];

// Bez type predicate – typ: (string | null | undefined)[]:
const withNulls = values.filter((v) => v !== null && v !== undefined);

// Z type predicate – typ: string[]:
function isDefined<T>(val: T | null | undefined): val is T {
  return val !== null && val !== undefined;
}

const clean = values.filter(isDefined); // string[] ✅
```

### Praktyczne scenariusze testowe

```ts
// Scenariusz 1: Walidacja odpowiedzi API
interface ApiUser {
  id: number;
  name: string;
  email: string;
  role: "admin" | "user";
}

function isApiUser(val: unknown): val is ApiUser {
  if (typeof val !== "object" || val === null) return false;
  const obj = val as Record<string, unknown>;
  return (
    typeof obj.id === "number" &&
    typeof obj.name === "string" &&
    typeof obj.email === "string" &&
    (obj.role === "admin" || obj.role === "user")
  );
}

test("API zwraca poprawny format użytkownika", async ({ request }) => {
  const response = await request.get("/api/users/1");
  const body = await response.json();

  if (!isApiUser(body)) {
    throw new Error(`Nieprawidłowy format: ${JSON.stringify(body)}`);
  }

  // Od tego miejsca body: ApiUser ✅
  expect(body.id).toBe(1);
  expect(body.role).toMatch(/^(admin|user)$/);
});

// Scenariusz 2: Obsługa różnych typów odpowiedzi
type ApiResponse<T> =
  | { status: "success"; data: T }
  | { status: "error"; message: string }
  | { status: "empty" };

function isSuccess<T>(r: ApiResponse<T>): r is { status: "success"; data: T } {
  return r.status === "success";
}

function isError<T>(
  r: ApiResponse<T>,
): r is { status: "error"; message: string } {
  return r.status === "error";
}

async function handleApiResponse<T>(
  response: ApiResponse<T>,
): Promise<T | null> {
  if (isSuccess(response)) {
    return response.data; // response.data: T ✅
  }
  if (isError(response)) {
    console.error(response.message); // response.message: string ✅
    return null;
  }
  return null; // empty
}

// Scenariusz 3: Playwright – sprawdzenie elementu przed interakcją
async function getInputValue(page: Page, selector: string): Promise<string> {
  const element = await page.$(selector);

  if (!element) {
    throw new ElementNotFoundError(selector);
  }

  // Sprawdzenie typu elementu w kontekście przeglądarki:
  const value = await element.evaluate((el) => {
    if (el instanceof HTMLInputElement || el instanceof HTMLTextAreaElement) {
      return el.value;
    }
    return el.textContent ?? "";
  });

  return value;
}
```

### Kiedy użyć którego mechanizmu – ściąga

```ts
// typeof – gdy sprawdzasz prymitywy:
if (typeof x === "string") {
  /* string */
}
if (typeof x === "number") {
  /* number */
}
if (typeof x === "undefined") {
  /* undefined */
}
// Pamiętaj: typeof null === 'object' – sprawdź x !== null osobno!

// instanceof – gdy sprawdzasz klasy:
if (err instanceof Error) {
  /* Error lub podklasy */
}
if (element instanceof HTMLInputElement) {
  /* input w evaluate() */
}
if (page instanceof Page) {
  /* obiekt Playwright */
}

// is (type predicate) – gdy:
// 1. Walidacja złożonego obiektu (np. JSON z API)
// 2. Typ jest interfejsem (instanceof nie działa)
// 3. Kombinacja warunków
// 4. Filtrowanie tablic z zachowaniem typów
if (isUser(data)) {
  /* data: User */
}
const users = items.filter(isUser); // User[]

// in – gdy sprawdzasz istnienie pola:
if ("email" in obj) {
  /* obj ma pole email */
}
if ("message" in err) {
  /* err ma pole message */
}
```

---

## Podsumowanie – Cheat Sheet

| Cel                     | Mechanizm        | Przykład                                 |
| ----------------------- | ---------------- | ---------------------------------------- |
| Sprawdź czy string      | `typeof`         | `typeof x === 'string'`                  |
| Sprawdź czy number      | `typeof`         | `typeof x === 'number'`                  |
| Sprawdź czy null        | Porównanie       | `x === null`                             |
| Sprawdź czy Error       | `instanceof`     | `err instanceof Error`                   |
| Sprawdź czy klasa       | `instanceof`     | `obj instanceof MyClass`                 |
| Sprawdź czy interfejs   | `is`             | `function isUser(x): x is User`          |
| Waliduj JSON z API      | `is`             | `function isApiResponse(x): x is T`      |
| Filtruj tablicę null    | `is`             | `arr.filter(isDefined)`                  |
| Regex – czy pasuje      | `test()`         | `/pattern/.test(str)`                    |
| Regex – wyciągnij grupy | `match()`        | `str.match(/(?<name>\w+)/)`              |
| Regex w Playwright URL  | `toHaveURL`      | `expect(page).toHaveURL(/\/users\/\d+/)` |
| Regex w Playwright text | `toHaveText`     | `expect(el).toHaveText(/^active$/i)`     |
| Named capture groups    | `match().groups` | `result.groups?.year`                    |

---

_Uzupełnienie do rozdziałów 3, 7, 10 · TypeScript 5.x · Playwright 1.4x+_

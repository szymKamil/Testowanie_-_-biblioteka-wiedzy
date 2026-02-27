# TypeScript – Uzupełnienie: Testowanie jednostkowe (Vitest / Jest)

> **Zakres:** Vitest i Jest z TypeScript · `describe`, `it`, `test` · Matchery · Mocki i spiony · Testowanie async · Utility types w testach

---

## T1. Vitest vs Jest – co wybrać?

|                           | **Vitest**              | **Jest**                     |
| ------------------------- | ----------------------- | ---------------------------- |
| Setup z TS                | Zero-config (Vite)      | Wymaga `ts-jest` lub `babel` |
| Szybkość                  | ⚡ Szybszy (ESM native) | Wolniejszy (transformacja)   |
| API                       | Identyczne z Jest       | Standard                     |
| Watch mode                | ⚡ Natychmiastowy (HMR) | Wolniejszy                   |
| Ekosystem                 | Nowszy                  | Dojrzalszy, większy          |
| Playwright kompatybilność | Osobne procesy (ok)     | Osobne procesy (ok)          |

> **Rekomendacja:** Vitest dla nowych projektów (szczególnie z Vite). Jest gdy projekt już go używa lub gdy potrzebujesz dojrzałego ekosystemu.

---

## T2. Setup

### Vitest

```bash
npm install --save-dev vitest @vitest/coverage-v8

# Opcjonalnie – UI do testów:
npm install --save-dev @vitest/ui
```

```ts
// vitest.config.ts
import { defineConfig } from "vitest/config";

export default defineConfig({
  test: {
    globals: true, // describe, it, expect bez importu
    environment: "node", // lub 'jsdom' dla testów DOM
    coverage: {
      provider: "v8",
      reporter: ["text", "html"],
      exclude: ["node_modules", "dist", "*.config.*"],
    },
    include: ["**/*.{test,spec}.{ts,tsx}"],
  },
});
```

### Jest

```bash
npm install --save-dev jest ts-jest @types/jest
```

```json
// jest.config.json
{
  "preset": "ts-jest",
  "testEnvironment": "node",
  "moduleNameMapper": {
    "^@utils/(.*)$": "<rootDir>/src/utils/$1",
    "^@pages/(.*)$": "<rootDir>/src/pages/$1"
  },
  "coverageDirectory": "coverage",
  "collectCoverageFrom": ["src/**/*.ts", "!src/**/*.d.ts"]
}
```

---

## T3. Podstawowa struktura testów

```ts
// utils/math.ts – kod do testowania:
export function add(a: number, b: number): number {
  return a + b;
}

export function divide(a: number, b: number): number {
  if (b === 0) throw new RangeError("Dzielenie przez zero");
  return a / b;
}

export async function fetchWithRetry<T>(url: string, retries = 3): Promise<T> {
  for (let i = 0; i < retries; i++) {
    try {
      const res = await fetch(url);
      if (!res.ok) throw new Error(`HTTP ${res.status}`);
      return res.json();
    } catch (err) {
      if (i === retries - 1) throw err;
    }
  }
  throw new Error("Unreachable");
}
```

```ts
// utils/math.test.ts – testy:
import { describe, it, test, expect, beforeEach, afterEach } from "vitest";
// lub: import { describe, it, expect } from '@jest/globals';
// lub: przy globals: true – bez importu

import { add, divide, fetchWithRetry } from "./math";

// describe – grupuje powiązane testy:
describe("add()", () => {
  it("dodaje dwie liczby dodatnie", () => {
    expect(add(2, 3)).toBe(5);
  });

  it("dodaje liczby ujemne", () => {
    expect(add(-1, -2)).toBe(-3);
  });

  it("dodaje zero", () => {
    expect(add(5, 0)).toBe(5);
  });
});

describe("divide()", () => {
  it("dzieli poprawnie", () => {
    expect(divide(10, 2)).toBe(5);
  });

  // Testowanie wyjątków:
  it("rzuca RangeError przy dzieleniu przez zero", () => {
    expect(() => divide(10, 0)).toThrow(RangeError);
    expect(() => divide(10, 0)).toThrow("Dzielenie przez zero");
  });
});
```

---

## T4. Matchery – przegląd z typowaniem

### Podstawowe matchery

```ts
// Równość:
expect(2 + 2).toBe(4); // Object.is – ścisłe porównanie
expect({ a: 1 }).toEqual({ a: 1 }); // głębokie porównanie
expect({ a: 1 }).not.toBe({ a: 1 }); // różne referencje
expect({ a: 1, b: 2 }).toMatchObject({ a: 1 }); // częściowe dopasowanie

// Typy i wartości:
expect(null).toBeNull();
expect(undefined).toBeUndefined();
expect("string").toBeDefined();
expect(true).toBeTruthy();
expect(0).toBeFalsy();
expect(42).toBeInstanceOf(Number); // rzadko potrzebne

// Liczby:
expect(3.14).toBeCloseTo(3.14159, 2); // precyzja do 2 miejsc
expect(10).toBeGreaterThan(5);
expect(10).toBeGreaterThanOrEqual(10);
expect(5).toBeLessThan(10);

// Stringi:
expect("TypeScript").toContain("Script");
expect("hello@example.com").toMatch(/^[\w.]+@[\w.]+$/);
expect("Hello World").toMatch("World");

// Tablice:
expect([1, 2, 3]).toContain(2);
expect([1, 2, 3]).toHaveLength(3);
expect([{ id: 1 }, { id: 2 }]).toContainEqual({ id: 1 });

// Obiekty:
expect({ a: 1, b: 2 }).toHaveProperty("a");
expect({ a: 1, b: 2 }).toHaveProperty("a", 1);
expect({ user: { name: "Jan" } }).toHaveProperty("user.name", "Jan");
```

### Asymetryczne matchery (`expect.any`, `expect.objectContaining`)

```ts
interface User {
  id: number;
  name: string;
  createdAt: Date;
  token: string;
}

// expect.any(Constructor) – sprawdza typ, nie wartość:
expect(createUser("Jan")).toMatchObject({
  id: expect.any(Number), // jakikolwiek number
  name: "Jan", // dokładna wartość
  createdAt: expect.any(Date), // jakakolwiek Data
  token: expect.stringMatching(/^[a-z0-9]+$/i), // regex
});

// expect.objectContaining – jak toMatchObject ale jako matcher:
expect(createUser("Jan")).toEqual(
  expect.objectContaining({
    name: "Jan",
  }),
);

// expect.arrayContaining – tablica zawiera podane elementy:
expect(["a", "b", "c", "d"]).toEqual(expect.arrayContaining(["b", "d"]));

// expect.not – negacja:
expect(result).toEqual(
  expect.not.objectContaining({ error: expect.any(String) }),
);

// Praktyczne w testach API:
test("POST /users zwraca nowego użytkownika", async () => {
  const response = await createUser({ name: "Jan", email: "jan@example.com" });

  expect(response).toMatchObject({
    id: expect.any(Number),
    name: "Jan",
    email: "jan@example.com",
    createdAt: expect.any(String), // ISO date string
    updatedAt: expect.any(String),
    // Nie sprawdzamy: token, passwordHash itp.
  });
});
```

---

## T5. Mocki i spiony

### `vi.fn()` / `jest.fn()` – mock funkcji

```ts
import { vi, describe, it, expect } from "vitest";

// Podstawowy mock:
const mockFn = vi.fn();
mockFn("hello");
mockFn(42);

expect(mockFn).toHaveBeenCalledTimes(2);
expect(mockFn).toHaveBeenCalledWith("hello");
expect(mockFn).toHaveBeenLastCalledWith(42);

// Mock z implementacją:
const mockAdd = vi.fn((a: number, b: number) => a + b);
mockAdd(2, 3); // 5

// Mock z wartością zwracaną:
const mockGetUser = vi.fn<[number], User>();
mockGetUser.mockReturnValue({
  id: 1,
  name: "Jan",
  email: "jan@test.com",
  role: "user",
});
mockGetUser.mockReturnValueOnce({
  id: 2,
  name: "Anna",
  email: "anna@test.com",
  role: "admin",
});
// pierwsze wywołanie → Anna, kolejne → Jan

// Mock async:
const mockFetch = vi.fn<[string], Promise<User>>();
mockFetch.mockResolvedValue({ id: 1, name: "Jan", email: "", role: "user" });
mockFetch.mockRejectedValueOnce(new Error("Network error"));
```

### `vi.spyOn()` – szpiegowanie istniejących metod

```ts
// Szpiegowanie – obserwujesz wywołania bez zmiany implementacji:
const user = { getName: () => "Jan" };
const spy = vi.spyOn(user, "getName");

user.getName(); // oryginalna implementacja nadal działa

expect(spy).toHaveBeenCalledTimes(1);

// Szpiegowanie z zastąpieniem implementacji:
spy.mockReturnValue("Anna");
user.getName(); // 'Anna' – zastąpiona

// Przywrócenie oryginału:
spy.mockRestore();
user.getName(); // 'Jan' – oryginał

// Przydatne dla globalnych funkcji:
const consoleSpy = vi.spyOn(console, "error").mockImplementation(() => {});
// teraz console.error nie wypisuje – ale możesz sprawdzić wywołania:
expect(consoleSpy).toHaveBeenCalledWith(expect.stringContaining("Error"));
consoleSpy.mockRestore();
```

### Mockowanie modułów

```ts
// Mockowanie całego modułu:
vi.mock("./user-service", () => ({
  getUserById: vi.fn(),
  createUser: vi.fn(),
}));

// Importuj PO vi.mock():
import { getUserById, createUser } from "./user-service";

// Teraz getUserById i createUser to vi.fn():
(getUserById as ReturnType<typeof vi.fn>).mockResolvedValue({
  id: 1,
  name: "Jan",
  email: "",
  role: "user",
});

// Czystszy zapis z pomocą vi.mocked():
import { vi } from "vitest";
const mockedGetUser = vi.mocked(getUserById);
mockedGetUser.mockResolvedValue({
  id: 1,
  name: "Jan",
  email: "",
  role: "user",
});
```

---

## T6. Testowanie async

```ts
import { describe, it, expect, vi, beforeEach } from "vitest";

describe("fetchWithRetry()", () => {
  beforeEach(() => {
    vi.restoreAllMocks(); // przywróć po każdym teście
  });

  it("zwraca dane przy sukcesie", async () => {
    const mockData = { id: 1, name: "Jan" };

    vi.spyOn(global, "fetch").mockResolvedValueOnce({
      ok: true,
      json: async () => mockData,
    } as Response);

    const result = await fetchWithRetry<typeof mockData>("/api/user");
    expect(result).toEqual(mockData);
  });

  it("ponawia przy błędzie HTTP", async () => {
    const fetchSpy = vi
      .spyOn(global, "fetch")
      .mockResolvedValueOnce({ ok: false, status: 500 } as Response)
      .mockResolvedValueOnce({ ok: false, status: 500 } as Response)
      .mockResolvedValueOnce({
        ok: true,
        json: async () => ({ id: 1 }),
      } as Response);

    const result = await fetchWithRetry("/api/user", 3);

    expect(fetchSpy).toHaveBeenCalledTimes(3); // 2 porażki + 1 sukces
    expect(result).toEqual({ id: 1 });
  });

  it("rzuca błąd po wyczerpaniu prób", async () => {
    vi.spyOn(global, "fetch").mockRejectedValue(new Error("Network error"));

    await expect(fetchWithRetry("/api/user", 3)).rejects.toThrow(
      "Network error",
    );
  });

  // Testowanie z fake timers – kontrola setTimeout:
  it("czeka między próbami", async () => {
    vi.useFakeTimers();

    const fetchSpy = vi
      .spyOn(global, "fetch")
      .mockRejectedValueOnce(new Error("timeout"))
      .mockResolvedValueOnce({ ok: true, json: async () => ({}) } as Response);

    const promise = fetchWithRetry("/api", 2);
    await vi.runAllTimersAsync();
    await promise;

    vi.useRealTimers();
  });
});
```

---

## T7. Setup i teardown

```ts
import {
  beforeAll,
  afterAll,
  beforeEach,
  afterEach,
  describe,
  it,
} from "vitest";

describe("UserService", () => {
  let db: TestDatabase;
  let service: UserService;

  // Raz przed wszystkimi testami w describe:
  beforeAll(async () => {
    db = await TestDatabase.create();
    await db.runMigrations();
  });

  // Raz po wszystkich testach:
  afterAll(async () => {
    await db.close();
  });

  // Przed każdym testem:
  beforeEach(async () => {
    service = new UserService(db);
    await db.seed([
      { id: 1, name: "Jan", email: "jan@test.com", role: "user" as const },
      { id: 2, name: "Anna", email: "anna@test.com", role: "admin" as const },
    ]);
  });

  // Po każdym teście – cleanup:
  afterEach(async () => {
    await db.clear();
    vi.restoreAllMocks();
  });

  it("pobiera użytkownika po ID", async () => {
    const user = await service.findById(1);
    expect(user).toMatchObject({ id: 1, name: "Jan" });
  });

  it("zwraca null dla nieistniejącego ID", async () => {
    const user = await service.findById(999);
    expect(user).toBeNull();
  });
});
```

---

## T8. Utility types w testach

```ts
// Tworzenie danych testowych z Partial + wymagane pola:
type TestUser = Required<Pick<User, "id" | "email">> &
  Partial<Omit<User, "id" | "email">>;

function createTestUser(overrides: Partial<User> = {}): User {
  const defaults: User = {
    id: 1,
    name: "Test User",
    email: `test-${Date.now()}@example.com`,
    role: "user",
    createdAt: new Date(),
  };
  return { ...defaults, ...overrides };
}

// Użycie – nadpisuj tylko to co ważne dla testu:
const admin = createTestUser({ role: "admin" });
const user2 = createTestUser({ id: 2, email: "other@test.com" });

// Record – typowany słownik danych testowych:
const TEST_USERS: Record<"admin" | "user" | "guest", User> = {
  admin: createTestUser({ id: 1, role: "admin", name: "Admin" }),
  user: createTestUser({ id: 2, role: "user", name: "User" }),
  guest: createTestUser({ id: 3, role: "user", name: "Guest" }),
};

// Pick – testy sprawdzające tylko część obiektu:
type UserPublicData = Pick<User, "id" | "name" | "email">;

function assertUserPublicData(actual: unknown, expected: UserPublicData): void {
  expect(actual).toMatchObject(expected);
}

// Użycie:
assertUserPublicData(apiResponse, {
  id: 1,
  name: "Jan",
  email: "jan@test.com",
});

// Omit – testy ignorujące dynamiczne pola:
type UserWithoutDates = Omit<User, "createdAt" | "updatedAt">;

test("tworzenie użytkownika", async () => {
  const user = await userService.create({ name: "Jan", email: "jan@test.com" });

  // Nie sprawdzamy dat – są dynamiczne:
  const { createdAt, updatedAt, ...userWithoutDates } = user;

  expect(userWithoutDates).toEqual<UserWithoutDates>({
    id: expect.any(Number),
    name: "Jan",
    email: "jan@test.com",
    role: "user",
  });
});
```

---

## Podsumowanie – Cheat Sheet

| Cel                   | Vitest/Jest API                                |
| --------------------- | ---------------------------------------------- |
| Grupuj testy          | `describe('nazwa', () => { })`                 |
| Pojedynczy test       | `it('co robi', () => { })` lub `test(...)`     |
| Asercja               | `expect(value).toBe(expected)`                 |
| Głębokie porównanie   | `.toEqual({...})`                              |
| Częściowe porównanie  | `.toMatchObject({...})`                        |
| Wyjątek synchroniczny | `expect(() => fn()).toThrow(Error)`            |
| Wyjątek async         | `await expect(asyncFn()).rejects.toThrow(...)` |
| Regex                 | `.toMatch(/pattern/)`                          |
| Typ wartości          | `.toBeInstanceOf(Class)`                       |
| Mock funkcji          | `vi.fn()` / `jest.fn()`                        |
| Szpieg                | `vi.spyOn(obj, 'method')`                      |
| Mock modułu           | `vi.mock('./module', () => ({...}))`           |
| Typowanie mocka       | `vi.mocked(fn)`                                |
| Async mock sukces     | `.mockResolvedValue(value)`                    |
| Async mock błąd       | `.mockRejectedValue(new Error(...))`           |
| Przed wszystkimi      | `beforeAll(async () => { })`                   |
| Przed każdym          | `beforeEach(() => { })`                        |
| Przywróć mocki        | `vi.restoreAllMocks()`                         |
| Dane testowe          | `createTestUser(overrides)` z Partial          |

---

_Uzupełnienie · TypeScript 5.x · Vitest 1.x / Jest 29+_

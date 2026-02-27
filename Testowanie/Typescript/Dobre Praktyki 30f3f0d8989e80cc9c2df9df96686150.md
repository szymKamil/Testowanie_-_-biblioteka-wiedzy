# Dobre Praktyki

# TypeScript – Rozdział 11: Dobre praktyki

> **Zakres:** Kiedy używać `type` a kiedy `interface` · Unikanie `any` · Strukturalne typowanie · Wzorce projektowe w TypeScript
> 
> 
> **Uzupełnienia:** Wzorce Playwright · Destrukturyzacja i spread · Nullish operators · Dobre praktyki testowania
> 

---

## 🟨 Fundamenty JS: Destrukturyzacja i Spread

> Dla programisty Java: destrukturyzacja i spread to zwięzłe operacje na obiektach i tablicach – bez odpowiednika w starszej Javie, ale podobne do record patterns z Javy 21.
> 

### Destrukturyzacja obiektów

```tsx
interface User {
  id: number;
  name: string;
  email: string;
  role: 'admin' | 'user';
}

const user: User = { id: 1, name: 'Jan', email: 'jan@example.com', role: 'admin' };

// Podstawowa destrukturyzacja:
const { name, email } = user;
console.log(name);  // 'Jan'
console.log(email); // 'jan@example.com'

// Z aliasami:
const { name: userName, role: userRole } = user;
// userName = 'Jan', userRole = 'admin'

// Z wartościami domyślnymi:
const { name: n = 'Nieznany', age = 0 } = user as any;

// Zagnieżdżona destrukturyzacja:
const config = { server: { host: 'localhost', port: 3000 } };
const { server: { host, port } } = config;

// W parametrach funkcji – bardzo częste w Playwright fixtures:
function greetUser({ name, role }: Pick<User, 'name' | 'role'>): string {
  return `Cześć${name}, rola:${role}`;
}
```

### Destrukturyzacja tablic

```tsx
const [first, second, ...rest] = [1, 2, 3, 4, 5];
// first = 1, second = 2, rest = [3, 4, 5]

// Pomijanie elementów:
const [, , third] = [1, 2, 3];
// third = 3

// Zamiana wartości bez zmiennej tymczasowej:
let a = 1, b = 2;
[a, b] = [b, a];
// a = 2, b = 1

// Z Promise.all – bardzo częste:
const [user, posts] = await Promise.all([fetchUser(1), fetchPosts(1)]);
```

### Spread operator (`...`)

```tsx
// Kopiowanie i rozszerzanie obiektów (shallow copy!):
const base = { id: 1, name: 'Jan' };
const extended = { ...base, role: 'admin', name: 'Anna' }; // name nadpisane
// { id: 1, name: 'Anna', role: 'admin' }

// Immutable update – wzorzec Redux/Playwright fixtures:
const updatedUser: User = { ...user, name: 'Anna' }; // nowy obiekt

// Łączenie tablic:
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];
const combined = [...arr1, ...arr2]; // [1, 2, 3, 4, 5, 6]

// Kopiowanie tablicy:
const copy = [...arr1]; // shallow copy

// Uwaga – spread jest PŁYTKI (shallow):
const nested = { a: { b: 1 } };
const copy2 = { ...nested };
copy2.a.b = 99;
nested.a.b; // 99 – ten sam obiekt wewnętrzny!
```

### Nullish operators – `??` i `?.`

```tsx
// ?? (nullish coalescing) – fallback tylko dla null/undefined (nie dla 0 i ''):
const name = user.name ?? 'Brak';       // 'Brak' tylko gdy null/undefined
const count = user.count ?? 0;          // 0 tylko gdy null/undefined

// || (OR) – fallback dla wszystkich falsy (0, '', false też!):
const count2 = user.count || 0;         // 0 gdy count = 0 – błąd!

// ?. (optional chaining) – bezpieczny dostęp do zagnieżdżonych pól:
const city = user?.address?.city;       // undefined zamiast błędu
const len = user?.name?.length;         // undefined zamiast błędu
const result = user?.getRole?.();       // wywołanie opcjonalnej metody

// ??= (nullish assignment) – przypisz tylko gdy null/undefined:
user.nickname ??= 'Użytkownik';

// Praktyczne w Playwright:
const text = await page.textContent('.title') ?? '';
const href = await element?.getAttribute('href') ?? '#';
```

---

## 11.1 Kiedy używać `type` a kiedy `interface`

### Zasada ogólna

> **Praktyczna zasada:** `interface` dla obiektów i klas (szczególnie publicznego API). `type` dla wszystkiego innego – union, tuple, aliasów prymitywów, mapped types.
> 

```tsx
// ✅ interface – obiekty, kontrakty klas:
interface User {
  id: number;
  name: string;
}

interface Repository<T> {
  findById(id: number): Promise<T | undefined>;
  save(entity: T): Promise<T>;
}

// ✅ type – union, tuple, aliasy, mapped types:
type Status    = 'active' | 'inactive' | 'pending';
type ID        = string | number;
type Point     = [number, number];
type Predicate<T> = (value: T) => boolean;
```

### Tabela decyzyjna

| Potrzebuję… | Użyj |
| --- | --- |
| Opisać kształt obiektu | `interface` (domyślnie) |
| Klasa implementuje kontrakt | `interface` |
| Rozszerzać typy z bibliotek | `interface` (declaration merging) |
| Union typów | `type` |
| Tuple | `type` |
| Alias prymitywu | `type` |
| Mapped / conditional type | `type` |

---

## 11.2 Unikanie `any`

### Dlaczego `any` jest niebezpieczne

```tsx
// ❌ any – TypeScript przestaje pomagać:
function processData(data: any) {
  data.toUpperCase();  // brak błędu – crash w runtime!
  return data.value;   // brak błędu – nawet gdy value nie istnieje
}

// Wirus any – rozszerza się dalej:
const result = processData(someInput); // result: any
result.anything.deeply.nested;         // nadal any
```

### Bezpieczne alternatywy

```tsx
// unknown – musisz sprawdzić typ przed użyciem:
function processData(data: unknown) {
  if (typeof data === 'string') {
    data.toUpperCase(); // ✅ data: string
  }
}

// Generyki – zachowują typ:
function first<T>(arr: T[]): T | undefined { return arr[0]; }
const n = first([1, 2, 3]); // n: number | undefined ✅

// Zod – walidacja danych z zewnątrz:
import { z } from 'zod';

const UserSchema = z.object({
  id:    z.number(),
  name:  z.string(),
  email: z.string().email(),
});

type User = z.infer<typeof UserSchema>; // typ z schematu!

// W teście Playwright – walidacja odpowiedzi API:
const body = await response.json();
const user = UserSchema.parse(body); // rzuci błąd jeśli dane nie pasują
user.name; // ✅ string
```

### `@ts-expect-error` vs `@ts-ignore`

```tsx
// ❌ @ts-ignore – tłumi błąd nawet gdy go nie ma:
// @ts-ignore
const x: string = 42;

// ✅ @ts-expect-error – błąd kompilacji gdy NIE MA błędu:
// @ts-expect-error: Celowo niepoprawny typ dla testu
const x: string = 42;
```

---

## 11.3 Strukturalne typowanie

### Jak działa (vs Java)

```tsx
// Java: typy sprawdzane przez nazwę klasy/interfejsu
// TypeScript: typy sprawdzane przez kształt (strukturę)

interface Named {
  name: string;
}

class Person {
  constructor(public name: string, public age: number) {}
}

// Person nie implementuje Named – ale ma pole name!
function greet(entity: Named): string {
  return `Cześć,${entity.name}`;
}

greet(new Person('Jan', 30)); // ✅ Person ma name – wystarczy
greet({ name: 'Literał' });   // ✅ literał też ma name
```

### Branded Types – wymuszanie semantyki

```tsx
// Problem – number = number dla TS, nawet gdy semantycznie różne:
type UserId    = number;
type ProductId = number;

function getUser(id: UserId): User { return {} as User; }

const productId: ProductId = 123;
getUser(productId); // ✅ brak błędu – number = number!

// Rozwiązanie – branded types:
type Brand<T, B> = T & { readonly __brand: B };

type UserId    = Brand<number, 'UserId'>;
type ProductId = Brand<number, 'ProductId'>;

const createUserId    = (id: number): UserId    => id as UserId;
const createProductId = (id: number): ProductId => id as ProductId;

const userId    = createUserId(1);
const productId2 = createProductId(123);

getUser(userId);     // ✅
// getUser(productId2); // ❌ błąd! ProductId ≠ UserId ✅

// Praktyczne w Playwright – typowane ID testowe:
type TestId    = Brand<string, 'TestId'>;
type SelectorId = Brand<string, 'SelectorId'>;
```

---

## 11.4 Wzorce projektowe z TypeScript

### Builder Pattern

```tsx
class QueryBuilder<
  TTable extends string | undefined = undefined
> {
  private _table?: string;
  private _conditions: string[] = [];
  private _limit?: number;

  from<T extends string>(table: T): QueryBuilder<T> {
    this._table = table;
    return this as any;
  }

  where(condition: string): this {
    this._conditions.push(condition);
    return this;
  }

  limit(n: number): this {
    this._limit = n;
    return this;
  }

  build(this: QueryBuilder<string>): string {
    const cond = this._conditions.length > 0
      ? ` WHERE${this._conditions.join(' AND ')}`
      : '';
    const lim = this._limit ? ` LIMIT${this._limit}` : '';
    return `SELECT * FROM${this._table}${cond}${lim}`;
  }
}
```

### Result Type – bezpieczna obsługa błędów

```tsx
type Ok<T>  = { ok: true;  value: T };
type Err<E> = { ok: false; error: E };
type Result<T, E = Error> = Ok<T> | Err<E>;

const ok  = <T>(value: T): Ok<T>   => ({ ok: true, value });
const err = <E>(error: E): Err<E>  => ({ ok: false, error });

// Użycie w testach – zamiast try/catch:
async function safeNavigate(page: Page, url: string): Promise<Result<void, string>> {
  try {
    await page.goto(url);
    return ok(undefined);
  } catch (e) {
    return err(e instanceof Error ? e.message : 'Nieznany błąd');
  }
}

const result = await safeNavigate(page, '/dashboard');
if (!result.ok) {
  console.error(`Nawigacja nieudana:${result.error}`);
}
```

---

## 11.5 Wzorce Playwright

### Data Builder – wzorzec do danych testowych

```tsx
// utils/builders/user.builder.ts
interface UserData {
  email: string;
  password: string;
  firstName: string;
  lastName: string;
  role: 'admin' | 'user';
  isActive: boolean;
}

class UserBuilder {
  private data: UserData = {
    email:     `test-${Date.now()}@example.com`,
    password:  'TestPassword123!',
    firstName: 'Jan',
    lastName:  'Kowalski',
    role:      'user',
    isActive:  true,
  };

  withEmail(email: string): this {
    this.data.email = email;
    return this;
  }

  withRole(role: 'admin' | 'user'): this {
    this.data.role = role;
    return this;
  }

  withName(firstName: string, lastName: string): this {
    this.data = { ...this.data, firstName, lastName };
    return this;
  }

  inactive(): this {
    this.data.isActive = false;
    return this;
  }

  build(): UserData {
    return { ...this.data };
  }
}

// Użycie w testach – czytelne, bez magicznych danych:
test('admin widzi panel zarządzania', async ({ loginPage }) => {
  const admin = new UserBuilder()
    .withRole('admin')
    .withName('Anna', 'Nowak')
    .build();

  await loginPage.login({ email: admin.email, password: admin.password });
});

test('nieaktywny użytkownik nie może się zalogować', async ({ loginPage }) => {
  const inactiveUser = new UserBuilder().inactive().build();
  // ...
});
```

### Page Object z Locator Composition

```tsx
// pages/product-list.page.ts
import { Page, Locator } from '@playwright/test';
import { BasePage } from './base.page';

export class ProductListPage extends BasePage {
  private readonly productGrid: Locator;

  constructor(page: Page) {
    super(page);
    this.productGrid = page.getByTestId('product-grid');
  }

  get pageUrl(): string { return '/products'; }

  // Locator composition – budowanie locatorów z kontekstu:
  private productCard(name: string): Locator {
    return this.productGrid.getByRole('article').filter({ hasText: name });
  }

  async getProductPrice(name: string): Promise<string | null> {
    return this.productCard(name)
      .getByTestId('price')
      .textContent();
  }

  async addToCart(name: string): Promise<void> {
    await this.productCard(name)
      .getByRole('button', { name: 'Dodaj do koszyka' })
      .click();
  }

  async getProductCount(): Promise<number> {
    return this.productGrid.getByRole('article').count();
  }

  async filterByCategory(category: string): Promise<void> {
    await this.page
      .getByRole('combobox', { name: 'Kategoria' })
      .selectOption(category);
    await this.page.waitForLoadState('networkidle');
  }
}
```

### Fixture Factory – wielokrotne użycie setupu

```tsx
// fixtures/api.fixture.ts
import { test as base, APIRequestContext } from '@playwright/test';

type ApiFixtures = {
  authenticatedRequest: APIRequestContext;
  adminRequest: APIRequestContext;
};

export const test = base.extend<ApiFixtures>({
  authenticatedRequest: async ({ playwright }, use) => {
    const context = await playwright.request.newContext({
      baseURL: process.env.API_URL,
      extraHTTPHeaders: {
        'Authorization': `Bearer${process.env.TEST_TOKEN}`,
        'Content-Type': 'application/json',
      },
    });
    await use(context);
    await context.dispose();
  },

  adminRequest: async ({ playwright }, use) => {
    const context = await playwright.request.newContext({
      baseURL: process.env.API_URL,
      extraHTTPHeaders: {
        'Authorization': `Bearer${process.env.ADMIN_TOKEN}`,
        'Content-Type': 'application/json',
      },
    });
    await use(context);
    await context.dispose();
  },
});
```

### Wzorzec Screenplay (alternatywa dla POM)

```tsx
// Screenplay – akcje zamiast stron:
interface Actor {
  attemptsTo(...tasks: Task[]): Promise<void>;
  asks<T>(question: Question<T>): Promise<T>;
}

interface Task {
  performAs(actor: ActorWithAbilities): Promise<void>;
}

interface Question<T> {
  answeredBy(actor: ActorWithAbilities): Promise<T>;
}

// Implementacja akcji:
class Navigate implements Task {
  constructor(private url: string) {}

  async performAs({ page }: ActorWithAbilities): Promise<void> {
    await page.goto(this.url);
  }
}

class FillIn implements Task {
  constructor(private selector: string, private value: string) {}

  async performAs({ page }: ActorWithAbilities): Promise<void> {
    await page.fill(this.selector, this.value);
  }
}

// Bardziej czytelne testy:
test('logowanie', async ({ page }) => {
  const jan = new ActorWithAbilities(page);
  await jan.attemptsTo(
    new Navigate('/login'),
    new FillIn('#email', 'jan@example.com'),
    new FillIn('#password', 'secret'),
    new Click('button[type="submit"]'),
  );
});
```

---

## 11.6 Ogólne dobre praktyki

### Zasady pisania typowanego kodu

```tsx
// ✅ 1. Discriminated Union zamiast boolean flags:
// ❌ Niejasne – możliwe niemożliwe stany:
type BadState = {
  isLoading: boolean;
  data: User | null;
  error: string | null;
};

// ✅ Jasne – niemożliwe stany są niemożliwe typowo:
type State =
  | { status: 'idle' }
  | { status: 'loading' }
  | { status: 'success'; data: User }
  | { status: 'error';   error: string };

// ✅ 2. as const dla stałych:
const SELECTORS = {
  email:    '#email',
  password: '#password',
  submit:   'button[type="submit"]',
} as const;

type SelectorKey = keyof typeof SELECTORS;

// ✅ 3. satisfies – walidacja bez zawężenia inference:
const config = {
  baseURL: 'http://localhost:3000',
  timeout: 5000,
  retries: 2,
} satisfies Record<string, string | number>;

config.baseURL; // string ✅ (nie string | number)
config.timeout; // number ✅

// ✅ 4. Readonly dla parametrów wejściowych:
function processUsers(users: readonly User[]): string[] {
  // users.push(...)  // ❌ nie można mutować
  return users.map(u => u.name);
}

// ✅ 5. Exhaustiveness check w switch:
type TestStatus = 'pass' | 'fail' | 'skip';

function getIcon(status: TestStatus): string {
  switch (status) {
    case 'pass': return '✅';
    case 'fail': return '❌';
    case 'skip': return '⏭️';
    default:
      const _: never = status; // błąd gdy dodasz nowy wariant bez obsługi
      return '';
  }
}
```

### Unikanie typowych błędów w Playwright

```tsx
// ❌ Brakujący await – najczęstszy błąd:
test('błędny test', async ({ page }) => {
  page.click('#button');      // brak await – nie czeka!
  expect(page.url()).toBe(''); // sprawdza zanim kliknięcie się wykona
});

// ✅ Zawsze await dla operacji Playwright:
test('poprawny test', async ({ page }) => {
  await page.click('#button');
  expect(page.url()).toBe('/expected');
});

// ❌ Stałe timeouty – kruche testy:
test('kruchy test', async ({ page }) => {
  await page.click('#button');
  await page.waitForTimeout(3000); // zawsze czeka 3s – za wolno lub za krótko
  await expect(page.locator('.result')).toBeVisible();
});

// ✅ Playwright auto-wait + web-first assertions:
test('stabilny test', async ({ page }) => {
  await page.click('#button');
  await expect(page.locator('.result')).toBeVisible(); // automatyczne czekanie
});

// ❌ Testy zależne od siebie – efekt domina:
test('test 1 – tworzy dane', async ({ page }) => { /* ... */ });
test('test 2 – używa danych z testu 1', async ({ page }) => { /* ... */ }); // ❌

// ✅ Testy izolowane – każdy ma własne dane:
test('test niezależny', async ({ page, request }) => {
  // Utwórz dane w setUp tego testu:
  await request.post('/api/users', { data: { name: 'Test' } });
  // ... testuj ...
});
```

---

## Podsumowanie – Cheat Sheet

| Temat | Zasada |
| --- | --- |
| `interface` vs `type` | `interface` dla obiektów/klas, `type` dla union/tuple/mapped |
| `any` | Zastąp `unknown`, generykami lub Zod |
| Structural typing | TypeScript patrzy na kształt, nie nazwę |
| Branded types | `type ID = number & { __brand: 'ID' }` |
| Discriminated union | `{ status: 'ok'; data: T } \| { status: 'err'; error: E }` |
| `??` vs `\|\|` | `??` tylko dla null/undefined, `\|\|` dla wszystkich falsy |
| `?.` | Bezpieczny dostęp: `user?.address?.city` |
| `as const` | Zamrożone literalne typy, `type T = typeof X[number]` |
| `satisfies` | Waliduje kształt bez zawężania inference |
| Destrukturyzacja | `const { name, role } = user` |
| Spread shallow | `{ ...obj }` – płytka kopia! |
| Playwright: `await` | ZAWSZE wymagane – ESLint `no-floating-promises` |
| Playwright: asercje | `expect(locator)` nie `await locator.text()` |
| Playwright: selektory | `getByRole`, `getByLabel`, `getByTestId` > CSS |
| Data Builder | Budowniczy dla danych testowych – czytelność |
| Page Object Chain | Metody zwracają kolejny Page Object |
| Testy izolowane | Każdy test tworzy własne dane, nie zależy od kolejności |

---

*Notatki dotyczą TypeScript 5.x · Playwright 1.4x+*
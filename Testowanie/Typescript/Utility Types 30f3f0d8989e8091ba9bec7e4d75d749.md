# Utility Types

# TypeScript – Rozdział 8: Narzędzia wbudowane (Utility Types)

> **Zakres:** `Partial`, `Required`, `Readonly` · `Pick`, `Omit`, `Exclude`, `Extract` · `Record`, `ReturnType`, `Parameters`
> 

---

## 8.1 Czym są Utility Types?

Utility types to gotowe, wbudowane typy generyczne dostarczane przez TypeScript. Pozwalają **transformować istniejące typy** bez ręcznego pisania mapped types czy conditional types. Są zdefiniowane w bibliotece standardowej (`lib.es5.d.ts`) i dostępne globalnie – bez żadnych importów.

```tsx
// Zamiast pisać ręcznie:
type ManualPartialUser = {
  id?: number;
  name?: string;
  email?: string;
};

// Używamy utility type:
type PartialUser = Partial<User>; // identyczny efekt, mniej kodu
```

---

## 8.2 Modyfikatory właściwości

### `Partial<T>` – wszystkie pola opcjonalne

Tworzy typ, w którym **każde pole** oryginalnego typu staje się opcjonalne (`?`). Niezbędny przy aktualizacjach i patch endpointach.

```tsx
interface User {
  id: number;
  name: string;
  email: string;
  role: 'admin' | 'user';
}

type PartialUser = Partial<User>;
// {
//   id?: number;
//   name?: string;
//   email?: string;
//   role?: 'admin' | 'user';
// }

// Klasyczny wzorzec – funkcja update:
function updateUser(id: number, data: Partial<User>): User {
  const existing = findUser(id);
  return { ...existing, ...data };
}

updateUser(1, { name: 'Anna' });             // ✅ tylko name
updateUser(1, { email: 'a@b.com', role: 'admin' }); // ✅ kilka pól
updateUser(1, {});                           // ✅ puste – też OK

// Partial jest płytki (shallow):
interface Company {
  name: string;
  address: { street: string; city: string };
}

type PartialCompany = Partial<Company>;
// address jest opcjonalne, ale jeśli podane – musi być kompletne:
const c: PartialCompany = { address: { street: 'Długa' } }; // ❌ brak city

// Głęboki Partial – własna implementacja:
type DeepPartial<T> = {
  [K in keyof T]?: T[K] extends object ? DeepPartial<T[K]> : T[K];
};
```

### `Required<T>` – wszystkie pola wymagane

Odwrotność `Partial` – usuwa opcjonalność (`?`) ze **wszystkich** pól.

```tsx
interface Config {
  host?: string;
  port?: number;
  debug?: boolean;
  timeout?: number;
}

type FullConfig = Required<Config>;
// {
//   host: string;
//   port: number;
//   debug: boolean;
//   timeout: number;
// }

// Przydatne gdy mamy domyślne wartości i chcemy wyrazić "po scaleniu z defaults":
const defaults: Required<Config> = {
  host: 'localhost',
  port: 3000,
  debug: false,
  timeout: 5000,
};

function resolveConfig(partial: Config): Required<Config> {
  return { ...defaults, ...partial };
}
```

### `Readonly<T>` – wszystkie pola tylko do odczytu

Dodaje modyfikator `readonly` do **każdego** pola. Próba zmiany wartości to błąd kompilacji.

```tsx
interface Point {
  x: number;
  y: number;
}

type FrozenPoint = Readonly<Point>;
// { readonly x: number; readonly y: number; }

const p: FrozenPoint = { x: 10, y: 20 };
// p.x = 5; // ❌ Cannot assign to 'x' because it is a read-only property

// Readonly jest płytki – tablice wewnętrznie nadal mutowalne:
interface State {
  users: User[];
  count: number;
}

const state: Readonly<State> = { users: [], count: 0 };
// state.count = 1;       // ❌ readonly
state.users.push(user);   // ✅ tablica mutowalna!

// Dla tablic – ReadonlyArray lub readonly T[]:
type ImmutableState = {
  readonly users: ReadonlyArray<User>;
  readonly count: number;
};

// Praktyczny wzorzec – reducer Redux:
function reducer(state: Readonly<State>, action: Action): Readonly<State> {
  // state nie może być mutowany – musimy zwrócić nowy obiekt
  return { ...state, count: state.count + 1 };
}
```

---

## 8.3 Wybieranie i pomijanie pól

### `Pick<T, K>` – wybierz tylko wskazane pola

Tworzy typ zawierający **wyłącznie** wymienione klucze z `T`.

```tsx
interface User {
  id: number;
  name: string;
  email: string;
  password: string;
  role: 'admin' | 'user';
  createdAt: Date;
}

// Bezpieczne DTO – nie zwracamy hasła:
type UserPublic = Pick<User, 'id' | 'name' | 'email' | 'role'>;
// { id: number; name: string; email: string; role: 'admin' | 'user' }

// Miniaturka dla list:
type UserSummary = Pick<User, 'id' | 'name'>;

// Formularz – tylko edytowalne pola:
type UserForm = Pick<User, 'name' | 'email' | 'role'>;

// Pick z generyczną funkcją:
function selectFields<T, K extends keyof T>(obj: T, keys: K[]): Pick<T, K> {
  const result = {} as Pick<T, K>;
  keys.forEach(k => { result[k] = obj[k]; });
  return result;
}

selectFields(user, ['id', 'name']); // { id: number; name: string }
```

### `Omit<T, K>` – pomiń wskazane pola

Tworzy typ **bez** wymienionych kluczy. Odwrotność `Pick`.

```tsx
// Wszystkie pola oprócz password:
type UserSafe   = Omit<User, 'password'>;

// Wszystkie pola oprócz id i createdAt (np. do tworzenia):
type CreateUser = Omit<User, 'id' | 'createdAt'>;

// Omit przy dziedziczeniu (unikanie konfliktów):
interface ButtonProps {
  label: string;
  onClick: () => void;
  disabled?: boolean;
}

interface IconButtonProps extends Omit<ButtonProps, 'label'> {
  icon: string;
  label?: string; // teraz opcjonalne
}

// Wzorzec – UpdateDTO z obowiązkowym id:
type UpdateUserDto = Partial<Omit<User, 'id'>> & { id: number };

const dto: UpdateUserDto = { id: 1, name: 'Anna' }; // ✅
// const bad: UpdateUserDto = { name: 'Anna' };      // ❌ brak id
```

### `Pick` vs `Omit` – kiedy co wybrać?

| Sytuacja | Użyj |
| --- | --- |
| Chcesz wybrać **kilka** konkretnych pól | `Pick` |
| Chcesz wykluczyć **jedno lub kilka** pól, resztę zachować | `Omit` |
| Chcesz wykluczyć `id`, `createdAt`, `updatedAt` z dużego interfejsu | `Omit` |
| Chcesz zabrać tylko `name` i `email` z dużego interfejsu | `Pick` |

---

## 8.4 Filtrowanie union types

### `Exclude<T, U>` – usuń z union pasujące typy

Usuwa z `T` wszystkie typy, które rozszerzają `U`.

```tsx
type Primitive = string | number | boolean | null | undefined;

type NonNullPrimitive = Exclude<Primitive, null | undefined>;
// string | number | boolean

type StringOrNumber = Exclude<string | number | boolean, boolean>;
// string | number

// Wykluczanie konkretnych wartości z union:
type Status   = 'active' | 'inactive' | 'pending' | 'deleted';
type Visible  = Exclude<Status, 'deleted'>;
// 'active' | 'inactive' | 'pending'

// Wykluczanie typów funkcji:
type NonFunction<T> = Exclude<T, Function>;
type Data = string | number | (() => void);
type PlainData = NonFunction<Data>; // string | number
```

### `Extract<T, U>` – zostaw tylko pasujące typy

Odwrotność `Exclude` – zachowuje tylko te elementy `T`, które rozszerzają `U`.

```tsx
type Mixed = string | number | boolean | null | undefined;

type Strings = Extract<Mixed, string>;          // string
type Numbers = Extract<Mixed, number | string>; // string | number
type Nullish = Extract<Mixed, null | undefined>; // null | undefined

// Wyciąganie konkretnych wariantów z discriminated union:
type Action =
  | { type: 'ADD';    payload: string }
  | { type: 'REMOVE'; payload: number }
  | { type: 'CLEAR' };

type AddOrRemove = Extract<Action, { type: 'ADD' | 'REMOVE' }>;
// { type: 'ADD'; payload: string } | { type: 'REMOVE'; payload: number }

// Wspólne typy między dwoma unionami:
type A = 'a' | 'b' | 'c' | 'd';
type B = 'b' | 'c' | 'e';
type Common = Extract<A, B>; // 'b' | 'c'
```

### `NonNullable<T>` – usuń null i undefined

Skrót dla `Exclude<T, null | undefined>`.

```tsx
type MaybeString = string | null | undefined;
type DefiniteString = NonNullable<MaybeString>; // string

type MaybeUser = User | null | undefined;
type DefiniteUser = NonNullable<MaybeUser>; // User

// Praktyczny wzorzec – typowany filtr null:
function compact<T>(arr: (T | null | undefined)[]): NonNullable<T>[] {
  return arr.filter((x): x is NonNullable<T> => x != null);
}

const values = ['a', null, 'b', undefined, 'c'];
const clean = compact(values); // string[] ✅
```

---

## 8.5 `Record<K, V>` – słownik / mapa

Tworzy typ obiektu z kluczami `K` i wartościami `V`. Odpowiednik mapy/słownika.

```tsx
// Prosty słownik:
type ScoreMap = Record<string, number>;
const scores: ScoreMap = { math: 95, english: 87 };

// Z union jako kluczem – typowane enumy:
type Status = 'active' | 'inactive' | 'pending';
type StatusConfig = Record<Status, { label: string; color: string }>;

const statusConfig: StatusConfig = {
  active:   { label: 'Aktywny',   color: 'green' },
  inactive: { label: 'Nieaktywny', color: 'gray' },
  pending:  { label: 'Oczekujący', color: 'yellow' },
};
// Jeśli brakuje któregoś statusu → błąd kompilacji ✅

// Record z generyczną wartością:
type GroupedBy<T> = Record<string, T[]>;

function groupBy<T>(items: T[], key: keyof T): GroupedBy<T> {
  return items.reduce((acc, item) => {
    const k = String(item[key]);
    acc[k] = acc[k] ?? [];
    acc[k].push(item);
    return acc;
  }, {} as GroupedBy<T>);
}

// Record zagnieżdżony:
type Matrix = Record<number, Record<number, number>>;
const m: Matrix = { 0: { 0: 1, 1: 0 }, 1: { 0: 0, 1: 1 } };

// Wbudowana implementacja Record:
// type Record<K extends keyof any, T> = { [P in K]: T };
```

---

## 8.6 Utility types dla funkcji

### `ReturnType<T>` – typ zwracany funkcji

```tsx
function getUser(id: number): { id: number; name: string } {
  return { id, name: 'Jan' };
}

type User = ReturnType<typeof getUser>;
// { id: number; name: string }

// Z async funkcjami:
async function fetchUser(id: number): Promise<User> {
  return fetch(`/api/users/${id}`).then(r => r.json());
}

type FetchResult = ReturnType<typeof fetchUser>;  // Promise<User>
type UserData    = Awaited<ReturnType<typeof fetchUser>>; // User

// Praktyczne – gdy typ wynika z implementacji:
const createStore = () => ({
  state: { count: 0 },
  increment() { this.state.count++; },
  getCount() { return this.state.count; },
});

type Store = ReturnType<typeof createStore>;
// Automatycznie odzwierciedla kształt obiektu
```

### `Parameters<T>` – typy parametrów jako tuple

```tsx
function createUser(name: string, age: number, role: 'admin' | 'user'): User {
  return { id: 1, name, age, role };
}

type CreateUserParams = Parameters<typeof createUser>;
// [name: string, age: number, role: 'admin' | 'user']

// Dostęp do konkretnego parametru przez indeks:
type NameParam = Parameters<typeof createUser>[0]; // string
type RoleParam = Parameters<typeof createUser>[2]; // 'admin' | 'user'

// Praktyczny wzorzec – wrapper zachowujący typy:
function withLogging<T extends (...args: any[]) => any>(
  fn: T,
  label: string
): (...args: Parameters<T>) => ReturnType<T> {
  return (...args) => {
    console.log(`[${label}] wywołano z`, args);
    const result = fn(...args);
    console.log(`[${label}] wynik:`, result);
    return result;
  };
}

const loggedCreate = withLogging(createUser, 'createUser');
loggedCreate('Jan', 30, 'admin'); // w pełni typowane ✅
```

### `ConstructorParameters<T>` i `InstanceType<T>`

```tsx
class Database {
  constructor(
    public host: string,
    public port: number,
    private password: string
  ) {}

  connect(): Promise<void> { return Promise.resolve(); }
}

type DbParams   = ConstructorParameters<typeof Database>;
// [host: string, port: number, password: string]

type DbInstance = InstanceType<typeof Database>;
// Database

// Fabryka z zachowaniem typów:
function createInstance<T extends new (...args: any[]) => any>(
  ctor: T,
  ...args: ConstructorParameters<T>
): InstanceType<T> {
  return new ctor(...args);
}

const db = createInstance(Database, 'localhost', 5432, 'secret');
// db: Database ✅
```

### `Awaited<T>` – odpakowywanie Promise

Od TypeScript 4.5. Rekurencyjnie odpakowuje zagnieżdżone `Promise`.

```tsx
type A = Awaited<Promise<string>>;           // string
type B = Awaited<Promise<Promise<number>>>;  // number
type C = Awaited<string>;                    // string (nie Promise – brak zmiany)

// Praktyczne użycie:
async function loadData(): Promise<{ users: User[]; total: number }> {
  return fetch('/api/users').then(r => r.json());
}

type LoadedData = Awaited<ReturnType<typeof loadData>>;
// { users: User[]; total: number }
```

---

## 8.7 Mniej znane, ale przydatne Utility Types

### `ThisParameterType<T>` i `OmitThisParameter<T>`

```tsx
function greet(this: { name: string }): string {
  return `Cześć, jestem${this.name}`;
}

type ThisType = ThisParameterType<typeof greet>; // { name: string }
type WithoutThis = OmitThisParameter<typeof greet>; // () => string

const boundGreet = greet.bind({ name: 'Jan' });
const fn: OmitThisParameter<typeof greet> = boundGreet; // ✅
```

### `ThisType<T>` – typowanie `this` w literałach obiektów

```tsx
type ObjectWithMethods = ThisType<{ name: string; age: number }>;

const obj: { greet(): string } & ObjectWithMethods = {
  greet() {
    return `Cześć,${this.name}`; // this: { name, age } ✅
  }
};
```

### `Uppercase`, `Lowercase`, `Capitalize`, `Uncapitalize`

```tsx
type U   = Uppercase<'hello'>;      // 'HELLO'
type L   = Lowercase<'WORLD'>;      // 'world'
type Cap = Capitalize<'hello'>;     // 'Hello'
type UC  = Uncapitalize<'Hello'>;   // 'hello'

// Użycie z mapped types:
type CapsKeys<T> = {
  [K in keyof T as Uppercase<string & K>]: T[K];
};

interface Colors { red: string; blue: string; }
type CapsColors = CapsKeys<Colors>;
// { RED: string; BLUE: string; }
```

---

## 8.8 Komponowanie Utility Types

Największa siłą utility types jest ich **łączenie** – jeden za drugim, budując dokładnie potrzebny typ.

```tsx
interface User {
  id: number;
  name: string;
  email: string;
  password: string;
  role: 'admin' | 'user';
  createdAt: Date;
  updatedAt: Date;
}

// DTO do tworzenia użytkownika:
// - bez id, createdAt, updatedAt (nadaje serwer)
// - bez password (oddzielny endpoint)
// - wszystkie pola wymagane
type CreateUserDto = Required<Omit<User, 'id' | 'password' | 'createdAt' | 'updatedAt'>>;

// DTO do aktualizacji:
// - bez id (w URL), bez createdAt/updatedAt
// - wszystkie pola opcjonalne
// - ale id jest wymagane do identyfikacji
type UpdateUserDto = Partial<Omit<User, 'id' | 'createdAt' | 'updatedAt'>> & { id: number };

// Publiczne dane użytkownika (bez wrażliwych):
type UserPublic = Readonly<Pick<User, 'id' | 'name' | 'email' | 'role'>>;

// Response ze statusem:
type ApiResponse<T> = {
  data: T;
  status: number;
  timestamp: Date;
};

type UserListResponse = ApiResponse<Pick<User, 'id' | 'name' | 'role'>[]>;

// Wymagane klucze + opcjonalne reszta:
type WithRequired<T, K extends keyof T> = Required<Pick<T, K>> & Partial<Omit<T, K>>;

type UserWithName = WithRequired<User, 'id' | 'name'>;
// id i name – wymagane, reszta opcjonalna
```

---

## Podsumowanie – Cheat Sheet

| Utility Type | Co robi | Przykład |
| --- | --- | --- |
| `Partial<T>` | Wszystkie pola opcjonalne | `Partial<User>` |
| `Required<T>` | Wszystkie pola wymagane | `Required<Config>` |
| `Readonly<T>` | Wszystkie pola readonly | `Readonly<State>` |
| `Pick<T, K>` | Tylko wybrane pola | `Pick<User, 'id' \| 'name'>` |
| `Omit<T, K>` | Wszystkie pola poza K | `Omit<User, 'password'>` |
| `Exclude<T, U>` | Usuń z union pasujące do U | `Exclude<string \| null, null>` |
| `Extract<T, U>` | Zostaw w union pasujące do U | `Extract<string \| number, string>` |
| `NonNullable<T>` | Usuń null i undefined | `NonNullable<string \| null>` |
| `Record<K, V>` | Obiekt z kluczami K, wartościami V | `Record<Status, Config>` |
| `ReturnType<T>` | Typ zwracany funkcji | `ReturnType<typeof fn>` |
| `Parameters<T>` | Parametry funkcji jako tuple | `Parameters<typeof fn>` |
| `ConstructorParameters<T>` | Parametry konstruktora | `ConstructorParameters<typeof Cls>` |
| `InstanceType<T>` | Typ instancji klasy | `InstanceType<typeof Cls>` |
| `Awaited<T>` | Odpakowuje Promise rekurencyjnie | `Awaited<Promise<string>>` |
| `Uppercase<S>` | String uppercase na poziomie typów | `Uppercase<'hello'>` → `'HELLO'` |
| `Capitalize<S>` | Pierwsza litera wielka | `Capitalize<'hello'>` → `'Hello'` |

---

*Notatki dotyczą TypeScript 5.x*
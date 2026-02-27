# Obiekty i Interfejsy

# TypeScript – Rozdział 4: Obiekty i interfejsy

> **Zakres:** Typowanie obiektów · Interface – definicja i rozszerzanie · Type alias – różnice względem interface · Właściwości opcjonalne i `readonly`
> 

---

## 4.1 Typowanie obiektów

### Podstawowa składnia

Obiekt typuje się przez opisanie jego kształtu – podanie nazw pól i ich typów. Można to zrobić inline lub przez nazwany typ/interfejs.

```tsx
// Inline – bezpośrednio przy zmiennej
const user: { name: string; age: number } = {
  name: 'Jan',
  age: 30,
};

// Inline jako parametr funkcji
function printUser(user: { name: string; age: number }): void {
  console.log(`${user.name},${user.age} lat`);
}
```

TypeScript stosuje **structural typing** (typowanie strukturalne) – liczy się kształt obiektu, nie jego “nazwa”. Jeśli obiekt ma wymagane pola, jest kompatybilny z typem, nawet jeśli ma pola dodatkowe.

```tsx
type Point = { x: number; y: number };

const p = { x: 10, y: 20, label: 'A' }; // ma też label
const point: Point = p; // ✅ OK – p "spełnia" kształt Point
```

### Indeksowane typy obiektów (Index Signatures)

Gdy klucze obiektu nie są z góry znane, używamy sygnatury indeksowej.

```tsx
// Obiekt z dowolnymi kluczami string → number
type ScoreMap = {
  [key: string]: number;
};

const scores: ScoreMap = {
  math: 95,
  english: 87,
  science: 91,
};

// Można łączyć ze stałymi polami (muszą być zgodne z typem indeksu):
type Config = {
  [key: string]: string | number;
  version: number;  // ✅ number pasuje do string | number
  name: string;     // ✅ string pasuje do string | number
};
```

### Zagnieżdżone obiekty

```tsx
type Address = {
  street: string;
  city: string;
  zip: string;
};

type User = {
  id: number;
  name: string;
  address: Address;
  metadata: {
    createdAt: Date;
    updatedAt: Date;
  };
};

const user: User = {
  id: 1,
  name: 'Jan',
  address: { street: 'Długa 1', city: 'Warszawa', zip: '00-001' },
  metadata: { createdAt: new Date(), updatedAt: new Date() },
};
```

### Excess Property Checking

TypeScript sprawdza nadmiarowe właściwości **tylko przy bezpośrednim przypisaniu literału obiektu**. Przez zmienną pośrednią – nie sprawdza.

```tsx
type Point = { x: number; y: number };

// ❌ Błąd – literał obiektu ma nadmiarowe pole
const p: Point = { x: 1, y: 2, z: 3 };

// ✅ OK – przez zmienną pośrednią (structural typing)
const obj = { x: 1, y: 2, z: 3 };
const p: Point = obj;

// ✅ OK – użycie type assertion (ale trać bezpieczeństwo)
const p: Point = { x: 1, y: 2, z: 3 } as Point;
```

---

## 4.2 Interface – definicja i rozszerzanie

### Definiowanie interfejsu

`interface` opisuje kształt obiektu lub klasy. To podstawowy sposób na nazywanie typów obiektowych w TypeScript.

```tsx
interface User {
  id: number;
  name: string;
  email: string;
}

// Interfejs z metodami:
interface Repository<T> {
  findById(id: number): T | undefined;
  findAll(): T[];
  save(entity: T): void;
  delete(id: number): boolean;
}

// Interfejs z sygnaturą indeksową:
interface Dictionary {
  [key: string]: string;
  readonly size: number; // można mieszać ze stałymi polami
}
```

### Rozszerzanie interfejsów (`extends`)

Interfejs może rozszerzać jeden lub wiele innych interfejsów – dziedziczy wszystkie ich pola.

```tsx
interface Animal {
  name: string;
  age: number;
}

interface Pet extends Animal {
  owner: string;
}

interface ServiceDog extends Pet {
  certification: string;
  tasks: string[];
}

// ServiceDog ma: name, age, owner, certification, tasks

const dog: ServiceDog = {
  name: 'Rex',
  age: 3,
  owner: 'Jan',
  certification: 'ADI-2023',
  tasks: ['Guide', 'Alert'],
};
```

**Rozszerzanie wielu interfejsów naraz:**

```tsx
interface Serializable {
  serialize(): string;
}

interface Loggable {
  log(): void;
}

interface Model extends Serializable, Loggable {
  id: number;
  validate(): boolean;
}
```

### Implementacja interfejsu przez klasę

```tsx
interface Flyable {
  altitude: number;
  fly(): void;
  land(): void;
}

interface Swimmable {
  depth: number;
  swim(): void;
}

// Klasa może implementować wiele interfejsów:
class Duck implements Flyable, Swimmable {
  altitude = 0;
  depth = 0;

  fly() { this.altitude = 100; }
  land() { this.altitude = 0; }
  swim() { this.depth = 2; }
}
```

### Declaration Merging

Unikalna cecha interfejsów – jeśli zadeklarujesz interfejs o tej samej nazwie dwa razy, TypeScript **scala** je automatycznie. Przydatne przy rozszerzaniu typów z zewnętrznych bibliotek.

```tsx
interface Window {
  myPlugin: () => void;
}

interface Window {
  analyticsId: string;
}

// TypeScript widzi:
// interface Window {
//   myPlugin: () => void;
//   analyticsId: string;
// }

window.myPlugin();     // ✅
window.analyticsId;    // ✅

// Praktyczne zastosowanie – rozszerzanie typów Express:
declare global {
  namespace Express {
    interface Request {
      userId?: number;
    }
  }
}
```

---

## 4.3 Type alias – różnice względem `interface`

### Definiowanie type alias

`type` pozwala nadać nazwę dowolnemu typowi – nie tylko obiektowemu.

```tsx
// Typ obiektowy – podobnie jak interface
type User = {
  id: number;
  name: string;
};

// Union – tylko type, nie interface
type ID = string | number;
type Status = 'active' | 'inactive' | 'pending';

// Tuple – tylko type
type Point = [number, number];
type RGB = [red: number, green: number, blue: number];

// Typ funkcji – tylko type
type Comparator<T> = (a: T, b: T) => number;

// Typ prymitywny z aliasem
type Username = string;
type Timestamp = number;
```

### Rozszerzanie type alias (przez intersection `&`)

`type` nie ma słowa kluczowego `extends`, ale można łączyć typy operatorem `&`.

```tsx
type Animal = {
  name: string;
  age: number;
};

type Pet = Animal & {
  owner: string;
};

type ServiceDog = Pet & {
  certification: string;
};

// ServiceDog: { name, age, owner, certification }
```

### Porównanie: `interface` vs `type`

| Cecha | `interface` | `type` |
| --- | --- | --- |
| Typy obiektów | ✅ | ✅ |
| Union types (`\|`) | ❌ | ✅ |
| Intersection (`&`) | przez `extends` | ✅ |
| Tuple | ❌ | ✅ |
| Typy prymitywów | ❌ | ✅ |
| Declaration Merging | ✅ (automatyczne) | ❌ |
| `implements` w klasie | ✅ | ✅ (gdy nie jest union) |
| Rozszerzanie | `extends` | `&` intersection |
| Rekurencja | ✅ | ✅ (od TS 3.7) |
| Komunikaty błędów | Zazwyczaj czytelniejsze | Mogą być rozwlekłe |

### Kiedy wybrać `interface`, a kiedy `type`?

```tsx
// ✅ interface – dla obiektów, klas, publicznego API biblioteki
interface UserService {
  getUser(id: number): Promise<User>;
  createUser(data: CreateUserDto): Promise<User>;
}

// ✅ type – dla union, tuple, mapped types, aliasów
type Result<T> = { ok: true; data: T } | { ok: false; error: string };
type Pair<T> = [T, T];
type EventHandler = (event: MouseEvent) => void;

// ✅ type – gdy chcesz aliasować prymityw lub union literałów
type Direction = 'up' | 'down' | 'left' | 'right';
type HttpMethod = 'GET' | 'POST' | 'PUT' | 'DELETE' | 'PATCH';
```

> **💡 Praktyczna zasada:** Używaj `interface` domyślnie dla obiektów i klas (zwłaszcza eksportowanych). Sięgaj po `type` gdy potrzebujesz union, tuple, intersection lub złożonych transformacji typów.
> 

### Przypadki gdzie `type` jest niezastąpiony

```tsx
// 1. Union types
type StringOrNumber = string | number;

// 2. Discriminated union (tagged union)
type Shape =
  | { kind: 'circle'; radius: number }
  | { kind: 'rect'; width: number; height: number }
  | { kind: 'triangle'; base: number; height: number };

// 3. Mapped types
type Optional<T> = { [K in keyof T]?: T[K] };

// 4. Conditional types
type Flatten<T> = T extends Array<infer U> ? U : T;

// 5. Template literal types
type EventName = 'click' | 'hover';
type Handler = `on${Capitalize<EventName>}`; // 'onClick' | 'onHover'
```

---

## 4.4 Właściwości opcjonalne i `readonly`

### Właściwości opcjonalne (`?`)

Pole oznaczone `?` może być nieobecne w obiekcie (lub mieć wartość `undefined`).

```tsx
interface User {
  id: number;
  name: string;
  email?: string;       // opcjonalne – string | undefined
  phone?: string;
  address?: {
    street: string;
    city: string;
  };
}

const user: User = { id: 1, name: 'Jan' }; // ✅ email, phone, address mogą nie być

// Bezpieczny dostęp do opcjonalnych pól:
console.log(user.email?.toUpperCase()); // optional chaining
console.log(user.email ?? 'brak');      // nullish coalescing
console.log(user.address?.city);        // zagnieżdżone opcjonalne
```

### Właściwości `readonly`

Pole `readonly` można ustawić tylko raz – przy inicjalizacji obiektu. Późniejsza zmiana to błąd kompilacji.

```tsx
interface Config {
  readonly host: string;
  readonly port: number;
  timeout: number;       // można zmieniać
}

const config: Config = {
  host: 'localhost',
  port: 3000,
  timeout: 5000,
};

config.timeout = 10000;  // ✅ można zmienić
// config.host = 'prod'; // ❌ Cannot assign to 'host' (readonly)
```

**`readonly` w klasach:**

```tsx
class Circle {
  readonly PI = 3.14159;
  readonly radius: number;

  constructor(radius: number) {
    this.radius = radius; // ✅ OK w konstruktorze
  }

  area(): number {
    return this.PI * this.radius ** 2;
  }
}

const c = new Circle(5);
// c.radius = 10; // ❌ błąd
```

### `Readonly<T>` i `ReadonlyArray<T>`

Utility types do tworzenia w pełni niemutowalnych wersji typów.

```tsx
interface User {
  id: number;
  name: string;
  tags: string[];
}

// Wszystkie pola readonly:
type FrozenUser = Readonly<User>;
// { readonly id: number; readonly name: string; readonly tags: string[] }

// Readonly jest płytki (shallow) – tablice nadal mutowalne wewnętrznie:
const user: FrozenUser = { id: 1, name: 'Jan', tags: ['admin'] };
// user.name = 'Anna'; // ❌ błąd
user.tags.push('user'); // ✅ tablica wewnętrznie nadal mutowalna!

// Aby zamrozić tablicę – ReadonlyArray lub readonly T[]:
interface ImmutableUser {
  readonly id: number;
  readonly name: string;
  readonly tags: ReadonlyArray<string>; // lub: readonly string[]
}

const iUser: ImmutableUser = { id: 1, name: 'Jan', tags: ['admin'] };
// iUser.tags.push('user'); // ❌ błąd – ReadonlyArray nie ma push
```

### Głęboki `readonly` (Deep Readonly)

Wbudowany `Readonly<T>` jest płytki. Dla głębokiego zamrożenia potrzebny jest rekurencyjny typ.

```tsx
type DeepReadonly<T> = {
  readonly [K in keyof T]: T[K] extends object ? DeepReadonly<T[K]> : T[K];
};

interface Config {
  server: {
    host: string;
    port: number;
  };
  db: {
    url: string;
    name: string;
  };
}

type FrozenConfig = DeepReadonly<Config>;

const cfg: FrozenConfig = {
  server: { host: 'localhost', port: 3000 },
  db: { url: 'mongodb://...', name: 'mydb' },
};

// cfg.server.host = 'prod'; // ❌ błąd – głęboko readonly ✅
```

### Wzorzec: `readonly` dla danych wejściowych

Dobra praktyka – parametry funkcji, które nie powinny być mutowane, warto oznaczyć `readonly`.

```tsx
// Funkcja nie powinna modyfikować przekazanej tablicy
function sum(numbers: readonly number[]): number {
  return numbers.reduce((acc, n) => acc + n, 0);
}

// Funkcja nie powinna modyfikować przekazanego obiektu
function displayUser(user: Readonly<User>): string {
  return `${user.name} (${user.id})`;
}

const nums = [1, 2, 3, 4, 5];
sum(nums); // ✅
```

### Opcjonalne vs wymagane – Utility types

TypeScript ma wbudowane narzędzia do masowej zmiany opcjonalności pól.

```tsx
interface User {
  id: number;
  name: string;
  email?: string;
  phone?: string;
}

// Partial – wszystkie pola opcjonalne (przydatne do update/patch)
type UserUpdate = Partial<User>;
// { id?: number; name?: string; email?: string; phone?: string }

// Required – wszystkie pola wymagane
type FullUser = Required<User>;
// { id: number; name: string; email: string; phone: string }

// Praktyczny wzorzec – DTO dla aktualizacji:
function updateUser(id: number, data: Partial<Omit<User, 'id'>>): User {
  // data może zawierać dowolny podzbiór pól (bez id)
  return { id, name: 'Jan', ...data };
}

updateUser(1, { name: 'Anna' });           // ✅
updateUser(1, { email: 'a@b.com' });       // ✅
updateUser(1, { name: 'Anna', phone: '123' }); // ✅
```

---

## Wzorce projektowe z obiektami

### Builder pattern z readonly

```tsx
interface QueryOptions {
  readonly table: string;
  readonly limit?: number;
  readonly offset?: number;
  readonly orderBy?: string;
}

class QueryBuilder {
  private options: Partial<QueryOptions> = {};

  from(table: string): this {
    this.options = { ...this.options, table };
    return this;
  }

  limit(n: number): this {
    this.options = { ...this.options, limit: n };
    return this;
  }

  offset(n: number): this {
    this.options = { ...this.options, offset: n };
    return this;
  }

  build(): QueryOptions {
    if (!this.options.table) throw new Error('Brak tabeli');
    return this.options as QueryOptions;
  }
}

const query = new QueryBuilder()
  .from('users')
  .limit(10)
  .offset(20)
  .build();
```

### Discriminated Union z obiektami

```tsx
type ApiResponse<T> =
  | { status: 'success'; data: T; timestamp: Date }
  | { status: 'error'; error: string; code: number }
  | { status: 'loading' };

function handleResponse<T>(res: ApiResponse<T>): void {
  switch (res.status) {
    case 'success':
      console.log(res.data);      // TS wie: res.data istnieje ✅
      break;
    case 'error':
      console.error(res.error);   // TS wie: res.error istnieje ✅
      break;
    case 'loading':
      console.log('Ładowanie...'); // TS wie: brak data/error ✅
      break;
  }
}
```

---

## Podsumowanie – Cheat Sheet

| Co chcę zapisać | Składnia |
| --- | --- |
| Typ obiektu inline | `{ name: string; age: number }` |
| Interface | `interface User { id: number; name: string }` |
| Rozszerzanie interface | `interface Admin extends User { role: string }` |
| Implementacja w klasie | `class C implements InterfaceA, InterfaceB` |
| Type alias obiektu | `type User = { id: number; name: string }` |
| Type alias union | `type ID = string \| number` |
| Rozszerzanie type | `type Admin = User & { role: string }` |
| Pole opcjonalne | `email?: string` |
| Pole readonly | `readonly id: number` |
| Readonly tablica | `readonly string[]` lub `ReadonlyArray<string>` |
| Wszystkie pola opcjonalne | `Partial<User>` |
| Wszystkie pola wymagane | `Required<User>` |
| Wszystkie pola readonly | `Readonly<User>` |
| Index signature | `[key: string]: number` |
| Discriminated union | `type S = { kind: 'a'; x: number } \| { kind: 'b'; y: string }` |
| Declaration merging | Dwa `interface` o tej samej nazwie – TS scala je |

---

*Notatki dotyczą TypeScript 5.x*
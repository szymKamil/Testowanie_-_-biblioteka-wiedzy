# Zaawansowane typy

# TypeScript – Rozdział 7: Zaawansowane typy

> **Zakres:** Union i Intersection types · Literal types · Type narrowing i type guards · `keyof`, `typeof`, `infer` · Mapped types · Conditional types · Template literal types
> 

---

## 7.1 Union types (`|`) i Intersection types (`&`)

### Union types – „jeden z wielu”

Union oznacza, że wartość może być **jednym z kilku typów**. TypeScript pozwala operować na niej tylko w zakresie, który jest wspólny dla wszystkich wariantów – dopiero po zawężeniu (narrowing) mamy dostęp do specyficznych metod.

```tsx
type StringOrNumber = string | number;

function printId(id: string | number): void {
  // Tylko metody wspólne dla string i number są dostępne od razu:
  console.log(id); // ✅ – toString() istnieje na obu

  // Specyficzne metody wymagają narrowing:
  if (typeof id === 'string') {
    console.log(id.toUpperCase()); // ✅ id: string
  } else {
    console.log(id.toFixed(2));    // ✅ id: number
  }
}

// Union z null/undefined – bardzo częsty wzorzec:
type MaybeUser = User | null;
type MaybeString = string | undefined;

function greet(name: string | null): string {
  return `Cześć,${name ?? 'nieznajomy'}!`;
}
```

### Discriminated Union (tagged union)

Najważniejszy wzorzec z union – każdy wariant ma pole-tag (discriminant), które TypeScript używa do zawężania typu. Kod staje się bezpieczny i wyczerpujący.

```tsx
type Shape =
  | { kind: 'circle';   radius: number }
  | { kind: 'rect';     width: number; height: number }
  | { kind: 'triangle'; base: number;  height: number };

function area(shape: Shape): number {
  switch (shape.kind) {
    case 'circle':
      return Math.PI * shape.radius ** 2;    // shape: { kind: 'circle', radius }
    case 'rect':
      return shape.width * shape.height;     // shape: { kind: 'rect', ... }
    case 'triangle':
      return (shape.base * shape.height) / 2;
  }
}

// Exhaustiveness check – TS zgłosi błąd gdy dodamy nowy wariant bez obsługi:
function assertNever(x: never): never {
  throw new Error(`Nieobsłużony wariant:${JSON.stringify(x)}`);
}

function areaExhaustive(shape: Shape): number {
  switch (shape.kind) {
    case 'circle':   return Math.PI * shape.radius ** 2;
    case 'rect':     return shape.width * shape.height;
    case 'triangle': return (shape.base * shape.height) / 2;
    default:         return assertNever(shape); // ❌ błąd gdy brak wariantu
  }
}
```

### Intersection types – „wszystko naraz”

Intersection łączy wiele typów w jeden – obiekt musi spełniać **wszystkie** wymagania jednocześnie.

```tsx
type Named  = { name: string };
type Aged   = { age: number };
type Person = Named & Aged;

const p: Person = { name: 'Jan', age: 30 }; // ✅ musi mieć oba pola

// Praktyczne zastosowanie – komponowanie typów:
type WithTimestamps = {
  createdAt: Date;
  updatedAt: Date;
};

type WithId = { id: number };

type Entity<T> = T & WithId & WithTimestamps;

type UserEntity = Entity<{ name: string; email: string }>;
// { name: string; email: string; id: number; createdAt: Date; updatedAt: Date }
```

### Konflikty w Intersection

Gdy dwa typy mają pole o tej samej nazwie, ale różnych typach, intersection tworzy typ `never` dla tego pola.

```tsx
type A = { id: string; name: string };
type B = { id: number; role: string };

type AB = A & B;
// { id: never; name: string; role: string }
// id: never – string & number = never (brak wartości spełniającej oba)

// Bezpieczne łączenie – Omit przed intersection:
type SafeAB = Omit<A, 'id'> & Omit<B, 'id'> & { id: string | number };
```

---

## 7.2 Literal types

### Literal types – konkretna wartość jako typ

Zamiast ogólnego `string`, typ może być **konkretną wartością** – np. dokładnie `'red'` lub `42`.

```tsx
// String literals:
type Direction    = 'up' | 'down' | 'left' | 'right';
type HttpMethod   = 'GET' | 'POST' | 'PUT' | 'DELETE' | 'PATCH';
type Alignment    = 'left' | 'center' | 'right';

// Number literals:
type DiceRoll     = 1 | 2 | 3 | 4 | 5 | 6;
type HttpStatus   = 200 | 201 | 400 | 401 | 403 | 404 | 500;

// Boolean literal (rzadziej używane):
type True         = true;
type AlwaysFalse  = false;

// Mieszane literal union:
type Padding = number | 'auto' | 'none';

function move(direction: Direction, steps: number): void {
  console.log(`Idę${direction} o${steps} kroków`);
}

move('up', 3);       // ✅
// move('diagonal'); // ❌ 'diagonal' nie pasuje do Direction
```

### `as const` – zamrażanie typów

`as const` sprawia, że TypeScript inferuje jak najbardziej szczegółowy (wąski) typ zamiast ogólnego.

```tsx
// Bez as const – TypeScript widzi ogólne typy:
const config = {
  host: 'localhost', // typ: string
  port: 3000,        // typ: number
  env: 'dev',        // typ: string
};

// Z as const – TypeScript widzi konkretne wartości:
const config = {
  host: 'localhost', // typ: 'localhost'
  port: 3000,        // typ: 3000
  env: 'dev',        // typ: 'dev'
} as const;

// Tablica as const – tuple z literalnymi typami:
const ROLES = ['admin', 'user', 'moderator'] as const;
type Role = typeof ROLES[number]; // 'admin' | 'user' | 'moderator'

// Wyliczanie typów ze stałych obiektów:
const STATUS = {
  ACTIVE:   'active',
  INACTIVE: 'inactive',
  PENDING:  'pending',
} as const;

type Status = typeof STATUS[keyof typeof STATUS];
// 'active' | 'inactive' | 'pending'
```

### Literal types w przeciążeniach i warunkach

```tsx
// Literal type jako "tag" zwracanego typu:
function createElement(tag: 'div'):   HTMLDivElement;
function createElement(tag: 'input'): HTMLInputElement;
function createElement(tag: 'span'):  HTMLSpanElement;
function createElement(tag: string):  HTMLElement {
  return document.createElement(tag);
}

const div   = createElement('div');   // HTMLDivElement ✅
const input = createElement('input'); // HTMLInputElement ✅

// Template literals z literalnymi typami:
type Size    = 'sm' | 'md' | 'lg';
type Color   = 'red' | 'blue' | 'green';
type Class   = `text-${Size}` | `bg-${Color}`;
// 'text-sm' | 'text-md' | 'text-lg' | 'bg-red' | 'bg-blue' | 'bg-green'
```

---

## 7.3 Type narrowing i type guards

### Co to jest narrowing?

TypeScript śledzi możliwe typy w każdym miejscu kodu i automatycznie **zawęża** (narrowing) typ zmiennej na podstawie warunków. Dzięki temu wewnątrz `if` wiemy dokładnie, jaki typ mamy do czynienia.

### `typeof` – dla prymitywów

```tsx
function process(value: string | number | boolean | null): string {
  if (typeof value === 'string') {
    return value.toUpperCase();  // value: string
  }
  if (typeof value === 'number') {
    return value.toFixed(2);     // value: number
  }
  if (typeof value === 'boolean') {
    return value ? 'tak' : 'nie'; // value: boolean
  }
  return 'null';                 // value: null (pozostałe przypadki wykluczone)
}
```

### `instanceof` – dla klas i obiektów

```tsx
class Cat { meow() { return 'Miau!'; } }
class Dog { bark() { return 'Hau!'; } }

function makeSound(animal: Cat | Dog): string {
  if (animal instanceof Cat) {
    return animal.meow(); // animal: Cat
  }
  return animal.bark();   // animal: Dog
}

// Praktyczne z Error:
function handleError(err: unknown): string {
  if (err instanceof Error) {
    return err.message;       // err: Error
  }
  if (typeof err === 'string') {
    return err;               // err: string
  }
  return 'Nieznany błąd';
}
```

### Operator `in` – sprawdzanie pola

```tsx
type Admin  = { role: 'admin'; permissions: string[] };
type User   = { role: 'user';  email: string };
type Guest  = { name?: string };

function describe(entity: Admin | User | Guest): string {
  if ('permissions' in entity) {
    return `Admin z${entity.permissions.length} uprawnieniami`; // Admin
  }
  if ('email' in entity) {
    return `Użytkownik:${entity.email}`;  // User
  }
  return `Gość:${entity.name ?? 'anonim'}`;  // Guest
}
```

### Truthiness narrowing

```tsx
function printLength(value: string | null | undefined): void {
  if (value) {
    // value: string (null i undefined są falsy)
    console.log(value.length);
  } else {
    console.log('Brak wartości');
  }
}

// Uwaga na falsy wartości:
function processValue(x: string | number | null): void {
  if (x) {
    // x: string | number (ale 0 i '' też są falsy!)
    console.log(x);
  }
}
// Dla 0 i '' – lepiej użyć x != null
```

### Type predicates – własne type guards

Gdy wbudowane operatory nie wystarczają, można napisać własną funkcję zwężającą typ.

```tsx
// Składnia: parametr is Typ
function isString(value: unknown): value is string {
  return typeof value === 'string';
}

function isUser(obj: unknown): obj is User {
  return (
    typeof obj === 'object' &&
    obj !== null &&
    'id' in obj &&
    typeof (obj as any).id === 'number' &&
    'name' in obj &&
    typeof (obj as any).name === 'string'
  );
}

function processInput(input: unknown): void {
  if (isString(input)) {
    console.log(input.toUpperCase()); // input: string ✅
  }
  if (isUser(input)) {
    console.log(input.name);          // input: User ✅
  }
}

// Type guard z tablicami – filtrowanie null:
const values: (string | null)[] = ['a', null, 'b', null, 'c'];

// Bez type guard – zwraca (string | null)[]:
const withNull = values.filter(v => v !== null);

// Z type guard – zwraca string[]:
function isNotNull<T>(value: T | null): value is T {
  return value !== null;
}
const withoutNull = values.filter(isNotNull); // string[] ✅
```

### Assertion functions

```tsx
function assert(condition: unknown, msg: string): asserts condition {
  if (!condition) throw new Error(msg);
}

function assertIsString(val: unknown): asserts val is string {
  if (typeof val !== 'string') throw new TypeError(`Oczekiwano string, otrzymano${typeof val}`);
}

function processName(input: unknown) {
  assertIsString(input);
  // Po asercji TS wie: input: string
  console.log(input.toUpperCase()); // ✅
}
```

---

## 7.4 `keyof`, `typeof`, `infer`

### `keyof` – klucze typu

`keyof T` zwraca union wszystkich kluczy typu `T`.

```tsx
interface User {
  id: number;
  name: string;
  email: string;
  age: number;
}

type UserKeys = keyof User;  // 'id' | 'name' | 'email' | 'age'

// Bezpieczne pobieranie pola:
function getField<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

// keyof z typeof (dla obiektów runtime):
const COLORS = { red: '#FF0000', green: '#00FF00', blue: '#0000FF' };
type ColorName = keyof typeof COLORS; // 'red' | 'green' | 'blue'

// Iterowanie po kluczach z zachowaniem typów:
function mapValues<T extends object, U>(
  obj: T,
  fn: (value: T[keyof T], key: keyof T) => U
): Record<keyof T, U> {
  const result = {} as Record<keyof T, U>;
  for (const key in obj) {
    result[key as keyof T] = fn(obj[key as keyof T], key as keyof T);
  }
  return result;
}
```

### `typeof` – typ wartości runtime

`typeof` w kontekście typów (nie runtime) pozwala pobrać typ zmiennej lub funkcji.

```tsx
const defaultConfig = {
  host: 'localhost',
  port: 3000,
  debug: false,
  retries: 3,
};

// Zamiast ręcznie pisać interfejs – pobieramy typ ze stałej:
type Config = typeof defaultConfig;
// { host: string; port: number; debug: boolean; retries: number }

function updateConfig(patch: Partial<typeof defaultConfig>): void {
  Object.assign(defaultConfig, patch);
}

// typeof z funkcjami:
function add(a: number, b: number): number { return a + b; }
type AddFn = typeof add; // (a: number, b: number) => number

// ReturnType i Parameters korzystają z typeof:
type AddReturn = ReturnType<typeof add>;     // number
type AddParams = Parameters<typeof add>;     // [number, number]

// typeof w conditional types:
type IsArray<T> = typeof [] extends T ? true : false;
```

### `infer` – wyciąganie typów z wnętrza

`infer` działa **wyłącznie** wewnątrz conditional types. Pozwala „wyciągnąć” i nazwać fragment typu.

```tsx
// Odpakowywanie Promise:
type UnwrapPromise<T> = T extends Promise<infer U> ? UnwrapPromise<U> : T;

type R1 = UnwrapPromise<Promise<string>>;           // string
type R2 = UnwrapPromise<Promise<Promise<number>>>;  // number
type R3 = UnwrapPromise<string>;                    // string (nie Promise)

// Typ elementu tablicy:
type ItemType<T> = T extends (infer U)[] ? U : never;
type I1 = ItemType<string[]>;    // string
type I2 = ItemType<User[]>;      // User
type I3 = ItemType<string>;      // never

// Pierwszy parametr funkcji:
type FirstArg<T> = T extends (first: infer F, ...rest: any[]) => any ? F : never;
type F1 = FirstArg<(x: string, y: number) => void>; // string

// Typ zwracany metody:
type ReturnOf<T, K extends keyof T> =
  T[K] extends (...args: any[]) => infer R ? R : never;

interface Service {
  fetchUser(id: number): Promise<User>;
  getName(): string;
}
type T1 = ReturnOf<Service, 'fetchUser'>; // Promise<User>
type T2 = ReturnOf<Service, 'getName'>;   // string

// infer w konstruktorach:
type InstanceOf<T> = T extends new (...args: any[]) => infer I ? I : never;
type ClassInstance = InstanceOf<typeof User>; // User

// Wyciąganie typów z tuple:
type Head<T extends any[]> = T extends [infer H, ...any[]] ? H : never;
type Tail<T extends any[]> = T extends [any, ...infer T] ? T : never;

type H = Head<[string, number, boolean]>; // string
type T = Tail<[string, number, boolean]>; // [number, boolean]
```

---

## 7.5 Mapped types

### Podstawy mapped types

Mapped type tworzy nowy typ przez **iterowanie po kluczach** innego typu i transformację każdego pola.

```tsx
// Składnia: { [K in keyof T]: ... }
type Stringify<T> = {
  [K in keyof T]: string;
};

interface User { id: number; name: string; active: boolean; }
type StringUser = Stringify<User>;
// { id: string; name: string; active: string; }

// Własne implementacje utility types:
type MyPartial<T>  = { [K in keyof T]?: T[K] };
type MyRequired<T> = { [K in keyof T]-?: T[K] };    // -? usuwa opcjonalność
type MyReadonly<T> = { readonly [K in keyof T]: T[K] };
type Mutable<T>    = { -readonly [K in keyof T]: T[K] }; // usuwa readonly
```

### Remapping kluczy z `as`

Od TypeScript 4.1 można przekształcać klucze używając `as`.

```tsx
// Dodanie prefiksu 'get':
type Getters<T> = {
  [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K];
};

interface User { id: number; name: string; }
type UserGetters = Getters<User>;
// { getId: () => number; getName: () => string; }

// Filtrowanie kluczy – `never` wyklucza pole:
type PickByValue<T, V> = {
  [K in keyof T as T[K] extends V ? K : never]: T[K];
};

interface Mixed {
  id: number;
  name: string;
  active: boolean;
  count: number;
}

type OnlyNumbers  = PickByValue<Mixed, number>;
// { id: number; count: number }

type OnlyStrings  = PickByValue<Mixed, string>;
// { name: string }

// Usuwanie pól null/undefined:
type NonNullableProps<T> = {
  [K in keyof T as T[K] extends null | undefined ? never : K]: T[K];
};
```

### Mapped types z union

```tsx
// Tworzenie obiektu z union kluczy:
type Flags<T extends string> = { [K in T]: boolean };
type PermissionFlags = Flags<'read' | 'write' | 'delete'>;
// { read: boolean; write: boolean; delete: boolean; }

// Record – wbudowany, ale tak wygląda w środku:
type MyRecord<K extends keyof any, V> = { [P in K]: V };

type Scores = MyRecord<'math' | 'english' | 'science', number>;
// { math: number; english: number; science: number; }
```

### Głęboki mapped type

```tsx
type DeepPartial<T> = {
  [K in keyof T]?: T[K] extends object ? DeepPartial<T[K]> : T[K];
};

type DeepReadonly<T> = {
  readonly [K in keyof T]: T[K] extends object ? DeepReadonly<T[K]> : T[K];
};

interface AppConfig {
  server: { host: string; port: number };
  database: { url: string; name: string; pool: { min: number; max: number } };
}

type PartialConfig  = DeepPartial<AppConfig>;   // wszystko opcjonalne rekurencyjnie
type FrozenConfig   = DeepReadonly<AppConfig>;  // wszystko readonly rekurencyjnie
```

---

## 7.6 Conditional types

### Podstawy conditional types

Conditional type to typ, który zależy od warunku: `T extends U ? X : Y`.

```tsx
// Podstawowa składnia:
type IsString<T>   = T extends string  ? true  : false;
type IsArray<T>    = T extends any[]   ? true  : false;
type IsFunction<T> = T extends Function ? true : false;

type A = IsString<string>; // true
type B = IsString<number>; // false
type C = IsArray<string[]>; // true

// Wbudowane utility types korzystają z conditional types:
type NonNullable<T>  = T extends null | undefined ? never : T;
type Flatten<T>      = T extends Array<infer U>   ? U     : T;
```

### Distributive conditional types

Gdy `T` jest type parametrem i jest union, conditional type **automatycznie dystrybuuje** się po każdym elemencie union.

```tsx
type ToArray<T> = T extends any ? T[] : never;

type A = ToArray<string | number>;
// string[] | number[]
// (a nie (string | number)[])

// Wyłączenie dystrybucji – owijamy w tuple:
type ToArrayNonDistrib<T> = [T] extends [any] ? T[] : never;
type B = ToArrayNonDistrib<string | number>;
// (string | number)[]

// Praktyczne użycie dystrybucji:
type Exclude<T, U>  = T extends U ? never : T;
type Extract<T, U>  = T extends U ? T     : never;

type E1 = Exclude<'a' | 'b' | 'c', 'a'>;  // 'b' | 'c'
type E2 = Extract<'a' | 'b' | 'c', 'a' | 'b'>; // 'a' | 'b'
```

### Zaawansowane conditional types

```tsx
// Czy T ma pole K:
type HasField<T, K extends string> = K extends keyof T ? true : false;
type H1 = HasField<{ name: string }, 'name'>;  // true
type H2 = HasField<{ name: string }, 'age'>;   // false

// Typ pola jeśli istnieje:
type FieldType<T, K extends string> =
  K extends keyof T ? T[K] : never;

type FT = FieldType<{ name: string; age: number }, 'name'>; // string

// Warunkowe modyfikatory:
type MaybeReadonly<T, IsReadonly extends boolean> =
  IsReadonly extends true ? Readonly<T> : T;

type R = MaybeReadonly<{ x: number }, true>;  // { readonly x: number }
type M = MaybeReadonly<{ x: number }, false>; // { x: number }

// Rekurencyjny conditional type – spłaszczenie zagnieżdżonych tablic:
type DeepFlatten<T> = T extends Array<infer U>
  ? DeepFlatten<U>
  : T;

type DF = DeepFlatten<number[][][]>; // number
```

---

## 7.7 Template literal types

### Podstawy template literal types

Template literal types działają jak template literals w JS, ale na poziomie typów. Pozwalają budować nowe typy string przez łączenie i transformację innych.

```tsx
type Greeting = `Hello,${string}`;
// Pasuje do dowolnego stringa zaczynającego się od 'Hello, '

type EventName = 'click' | 'focus' | 'blur';
type Handler   = `on${Capitalize<EventName>}`;
// 'onClick' | 'onFocus' | 'onBlur'

type CSSUnit    = 'px' | 'em' | 'rem' | '%';
type CSSValue   = `${number}${CSSUnit}`;
// `${number}px` | `${number}em` | `${number}rem` | `${number}%`

// Kombinacje – cross product:
type Axis      = 'x' | 'y';
type Direction = 'positive' | 'negative';
type Movement  = `${Axis}-${Direction}`;
// 'x-positive' | 'x-negative' | 'y-positive' | 'y-negative'
```

### Wbudowane string utility types

TypeScript ma gotowe typy do transformacji stringów:

```tsx
type U = Uppercase<'hello'>;         // 'HELLO'
type L = Lowercase<'WORLD'>;         // 'world'
type C = Capitalize<'helloWorld'>;   // 'HelloWorld'
type UC = Uncapitalize<'HelloWorld'>; // 'helloWorld'

// Łączone transformacje:
type ToPascalCase<S extends string> =
  S extends `${infer Head}_${infer Tail}`
    ? `${Capitalize<Head>}${ToPascalCase<Capitalize<Tail>>}`
    : Capitalize<S>;

type P = ToPascalCase<'hello_world_foo'>; // 'HelloWorldFoo'

type ToCamelCase<S extends string> =
  S extends `${infer Head}_${infer Tail}`
    ? `${Lowercase<Head>}${ToPascalCase<Tail>}`
    : Lowercase<S>;

type Cam = ToCamelCase<'hello_world_foo'>; // 'helloWorldFoo'
```

### Praktyczne zastosowania

```tsx
// Typowane zdarzenia DOM:
type HTMLTag = 'div' | 'span' | 'input' | 'button';
type GetElement<T extends HTMLTag> = `get${Capitalize<T>}`;
// 'getDiv' | 'getSpan' | 'getInput' | 'getButton'

// Getter/setter z template literals:
interface UserData {
  name: string;
  age: number;
  email: string;
}

type Getters<T> = {
  [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K];
};

type Setters<T> = {
  [K in keyof T as `set${Capitalize<string & K>}`]: (value: T[K]) => void;
};

type UserAPI = Getters<UserData> & Setters<UserData>;
// {
//   getName: () => string;     setName: (value: string) => void;
//   getAge:  () => number;     setAge:  (value: number) => void;
//   getEmail: () => string;    setEmail: (value: string) => void;
// }

// Typowana ścieżka do zagnieżdżonego pola:
type NestedPaths<T, Prefix extends string = ''> = {
  [K in keyof T & string]: T[K] extends object
    ? NestedPaths<T[K], `${Prefix}${K}.`>
    : `${Prefix}${K}`;
}[keyof T & string];

interface Config {
  server: { host: string; port: number };
  db: { url: string; name: string };
}

type ConfigPaths = NestedPaths<Config>;
// 'server.host' | 'server.port' | 'db.url' | 'db.name'

// Typowany event system z template literals:
type EventPayloads = {
  'user:login':   { userId: number; timestamp: Date };
  'user:logout':  { userId: number };
  'cart:add':     { productId: string; quantity: number };
  'cart:remove':  { productId: string };
};

function emit<K extends keyof EventPayloads>(
  event: K,
  payload: EventPayloads[K]
): void {
  console.log(event, payload);
}

emit('user:login', { userId: 1, timestamp: new Date() }); // ✅
// emit('user:login', { userId: 'abc' }); // ❌ userId musi być number
```

### `infer` w template literal types

```tsx
// Wyciąganie fragmentów stringa:
type ExtractRouteParams<T extends string> =
  T extends `${string}:${infer Param}/${infer Rest}`
    ? Param | ExtractRouteParams<`/${Rest}`>
    : T extends `${string}:${infer Param}`
    ? Param
    : never;

type Params = ExtractRouteParams<'/users/:userId/posts/:postId'>;
// 'userId' | 'postId'

// Odwrócenie camelCase na snake_case:
type ToSnakeCase<S extends string> =
  S extends `${infer Head}${infer Tail}`
    ? Head extends Uppercase<Head>
      ? `_${Lowercase<Head>}${ToSnakeCase<Tail>}`
      : `${Head}${ToSnakeCase<Tail>}`
    : S;

type SN = ToSnakeCase<'helloWorldFoo'>; // 'hello_world_foo'
```

---

## Podsumowanie – Cheat Sheet

| Co chcę zapisać | Składnia |
| --- | --- |
| Union type | `type T = A \| B \| C` |
| Intersection type | `type T = A & B & C` |
| Discriminated union | `type T = { kind: 'a'; x: number } \| { kind: 'b'; y: string }` |
| Literal type | `type Dir = 'up' \| 'down' \| 'left' \| 'right'` |
| Zamrożenie wartości | `const x = { a: 1 } as const` |
| Union z as const | `type T = typeof ARR[number]` |
| typeof (typy) | `type T = typeof someValue` |
| keyof | `type Keys = keyof User` |
| Indeksowanie | `type T = User['name']` – typ pola name |
| Type predicate | `function isStr(x: unknown): x is string` |
| Assertion function | `function assert(v: unknown): asserts v is string` |
| Conditional type | `type T = A extends B ? X : Y` |
| infer | `type T = A extends Promise<infer U> ? U : never` |
| Mapped type | `type T = { [K in keyof U]: string }` |
| Filtrowanie kluczy | `[K in keyof T as T[K] extends X ? K : never]` |
| Usuwanie opcjonalności | `{ [K in keyof T]-?: T[K] }` |
| Usuwanie readonly | `{ -readonly [K in keyof T]: T[K] }` |
| Template literal | `type T = `on${Capitalize<EventName>}`` |
| Uppercase/Lowercase | `Uppercase<'hello'>` → `'HELLO'` |
| Dystrybucja union | `type T<U> = U extends any ? U[] : never` |
| Brak dystrybucji | `type T<U> = [U] extends [any] ? U[] : never` |
| Exhaustiveness check | `default: return assertNever(x)` w switch |

---

*Notatki dotyczą TypeScript 5.x*
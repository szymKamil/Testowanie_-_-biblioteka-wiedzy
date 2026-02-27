# Generyki

# TypeScript – Rozdział 6: Generyki (Generics)

> **Zakres:** Po co są generyki · Funkcje generyczne · Generyczne interfejsy i klasy · Ograniczenia generyczne (`extends`)
> 

---

## 6.1 Po co są generyki?

### Problem bez generyk

Gdy piszemy kod wielokrotnego użytku, szybko napotykamy dylemat: albo duplikujemy kod dla każdego typu, albo uciekamy się do `any` i tracimy bezpieczeństwo typów.

```tsx
// ❌ Duplikacja – osobna funkcja dla każdego typu
function firstNumber(arr: number[]): number | undefined {
  return arr[0];
}
function firstString(arr: string[]): string | undefined {
  return arr[0];
}

// ❌ Użycie any – tracimy typ zwracany
function firstAny(arr: any[]): any {
  return arr[0]; // zwraca any – TypeScript nie wie co to jest
}

const result = firstAny([1, 2, 3]);
result.toUpperCase(); // TS nie zgłosi błędu – crash w runtime!
```

### Rozwiązanie: generyki

Generyk `<T>` to parametr typowy – placeholder, który TypeScript wypełnia konkretnym typem przy każdym wywołaniu. Kod jest jeden, ale działa bezpiecznie z dowolnym typem.

```tsx
// ✅ Jedna funkcja dla wszystkich typów, pełne bezpieczeństwo
function first<T>(arr: T[]): T | undefined {
  return arr[0];
}

const n = first([1, 2, 3]);       // T = number → zwraca number | undefined
const s = first(['a', 'b', 'c']); // T = string → zwraca string | undefined
const d = first([new Date()]);    // T = Date   → zwraca Date | undefined

n?.toFixed(2);      // ✅ TS wie że n to number
s?.toUpperCase();   // ✅ TS wie że s to string
// n?.toUpperCase() // ❌ błąd – number nie ma toUpperCase
```

### Inferowanie typu generycznego

TypeScript najczęściej sam wnioskuje `T` z przekazanych argumentów. Jawne podanie typu jest opcjonalne, ale czasem przydatne.

```tsx
function identity<T>(arg: T): T {
  return arg;
}

identity(42);           // T inferowany jako number
identity('hello');      // T inferowany jako string
identity<boolean>(true); // T jawnie podany

// Kiedy jawne podanie T jest przydatne:
function createArray<T>(length: number, fill: T): T[] {
  return Array.from({ length }, () => fill);
}

createArray(3, 0);           // T = number → [0, 0, 0]
createArray(3, 'x');         // T = string → ['x', 'x', 'x']
createArray<string | null>(3, null); // T jawnie szerszy
```

---

## 6.2 Funkcje generyczne

### Podstawowe wzorce

```tsx
// Zamiana miejscami elementów:
function swap<A, B>(pair: [A, B]): [B, A] {
  return [pair[1], pair[0]];
}

swap([1, 'hello']);     // ['hello', 1] : [string, number]
swap([true, 42]);       // [42, true]   : [number, boolean]

// Mapowanie z zachowaniem typów:
function mapArray<T, U>(arr: T[], fn: (item: T, index: number) => U): U[] {
  return arr.map(fn);
}

mapArray([1, 2, 3], n => n * 2);          // number[]
mapArray(['a', 'b'], s => s.toUpperCase()); // string[]
mapArray([1, 2, 3], n => n.toString());    // string[]

// Filtrowanie z type predicatem:
function filter<T>(arr: T[], predicate: (item: T) => boolean): T[] {
  return arr.filter(predicate);
}

// Grupowanie:
function groupBy<T, K extends string | number>(
  arr: T[],
  keyFn: (item: T) => K
): Record<K, T[]> {
  return arr.reduce((acc, item) => {
    const key = keyFn(item);
    acc[key] = acc[key] ?? [];
    acc[key].push(item);
    return acc;
  }, {} as Record<K, T[]>);
}

groupBy([1, 2, 3, 4], n => n % 2 === 0 ? 'even' : 'odd');
// { odd: [1, 3], even: [2, 4] }
```

### Wiele parametrów typowych

```tsx
// Łączenie dwóch obiektów:
function merge<T extends object, U extends object>(a: T, b: U): T & U {
  return { ...a, ...b };
}

const result = merge({ name: 'Jan' }, { age: 30 });
result.name; // string ✅
result.age;  // number ✅

// Pipeline – przekazywanie wartości przez funkcje:
function pipe<A, B, C>(
  value: A,
  fn1: (a: A) => B,
  fn2: (b: B) => C
): C {
  return fn2(fn1(value));
}

pipe(
  '  hello  ',
  s => s.trim(),
  s => s.toUpperCase()
); // 'HELLO'
```

### Generyki w callbackach i higher-order functions

```tsx
// Memoizacja z zachowaniem typów:
function memoize<TArgs extends unknown[], TReturn>(
  fn: (...args: TArgs) => TReturn
): (...args: TArgs) => TReturn {
  const cache = new Map<string, TReturn>();

  return (...args: TArgs): TReturn => {
    const key = JSON.stringify(args);
    if (cache.has(key)) return cache.get(key)!;
    const result = fn(...args);
    cache.set(key, result);
    return result;
  };
}

const memoAdd = memoize((a: number, b: number) => a + b);
memoAdd(1, 2); // 3 – obliczone
memoAdd(1, 2); // 3 – z cache
// memoAdd('a', 'b'); // ❌ błąd – TS inferuje TArgs = [number, number]

// Retry z zachowaniem typów:
async function retry<T>(
  fn: () => Promise<T>,
  attempts: number
): Promise<T> {
  for (let i = 0; i < attempts; i++) {
    try {
      return await fn();
    } catch (err) {
      if (i === attempts - 1) throw err;
      await new Promise(r => setTimeout(r, 1000 * (i + 1)));
    }
  }
  throw new Error('Unreachable');
}

const user = await retry(() => fetchUser(1), 3); // user: User ✅
```

### Domyślny typ generyczny

```tsx
// T ma wartość domyślną = string
interface ApiResponse<T = string> {
  data: T;
  status: number;
  message: string;
}

const textResponse: ApiResponse = { data: 'OK', status: 200, message: '' };
const userResponse: ApiResponse<User> = { data: { id: 1, name: 'Jan' }, status: 200, message: '' };

// Domyślny typ w funkcji (rzadziej, ale możliwe):
function createState<T = null>(initial?: T): { value: T | null } {
  return { value: initial ?? null };
}

createState();          // { value: null }     – T = null (default)
createState(42);        // { value: 42 }       – T = number
createState('hello');   // { value: 'hello' }  – T = string
```

---

## 6.3 Generyczne interfejsy i klasy

### Generyczne interfejsy

```tsx
// Podstawowy generyczny interfejs:
interface Box<T> {
  value: T;
  isEmpty(): boolean;
  map<U>(fn: (value: T) => U): Box<U>;
}

// Interfejs repozytorium – klasyczny wzorzec:
interface Repository<T, ID = number> {
  findById(id: ID): Promise<T | undefined>;
  findAll(): Promise<T[]>;
  findWhere(predicate: Partial<T>): Promise<T[]>;
  save(entity: T): Promise<T>;
  delete(id: ID): Promise<boolean>;
}

// Interfejs z wieloma parametrami:
interface Transformer<TInput, TOutput, TError = Error> {
  transform(input: TInput): TOutput;
  transformOrFail(input: TInput): TOutput | TError;
  canTransform(input: TInput): boolean;
}
```

### Generyczne klasy

```tsx
// Stack – klasyczny przykład:
class Stack<T> {
  private items: T[] = [];

  push(item: T): this {
    this.items.push(item);
    return this;
  }

  pop(): T | undefined {
    return this.items.pop();
  }

  peek(): T | undefined {
    return this.items[this.items.length - 1];
  }

  get size(): number {
    return this.items.length;
  }

  isEmpty(): boolean {
    return this.items.length === 0;
  }

  toArray(): T[] {
    return [...this.items];
  }
}

const numStack = new Stack<number>();
numStack.push(1).push(2).push(3);
numStack.pop();  // 3 : number
numStack.peek(); // 2 : number

const strStack = new Stack<string>();
strStack.push('a');
// strStack.push(1); // ❌ błąd – Stack<string>
```

### Generyczna klasa z ograniczeniami

```tsx
// EventEmitter – typowane zdarzenia:
type EventMap = Record<string, unknown>;

class TypedEventEmitter<Events extends EventMap> {
  private listeners = new Map<keyof Events, Set<(data: unknown) => void>>();

  on<K extends keyof Events>(
    event: K,
    listener: (data: Events[K]) => void
  ): this {
    if (!this.listeners.has(event)) {
      this.listeners.set(event, new Set());
    }
    this.listeners.get(event)!.add(listener as (data: unknown) => void);
    return this;
  }

  emit<K extends keyof Events>(event: K, data: Events[K]): void {
    this.listeners.get(event)?.forEach(fn => fn(data));
  }

  off<K extends keyof Events>(event: K, listener: (data: Events[K]) => void): void {
    this.listeners.get(event)?.delete(listener as (data: unknown) => void);
  }
}

// Użycie – pełne bezpieczeństwo typów:
interface AppEvents {
  userLogin: { userId: number; timestamp: Date };
  userLogout: { userId: number };
  error: { message: string; code: number };
}

const emitter = new TypedEventEmitter<AppEvents>();

emitter.on('userLogin', ({ userId, timestamp }) => {
  console.log(`User${userId} logged in at${timestamp}`);
});

emitter.emit('userLogin', { userId: 1, timestamp: new Date() }); // ✅
// emitter.emit('userLogin', { userId: 'abc' }); // ❌ userId musi być number
// emitter.emit('unknown', {});                  // ❌ nieznane zdarzenie
```

### Klasa generyczna implementująca generyczny interfejs

```tsx
interface Comparable<T> {
  compareTo(other: T): number;
}

interface Displayable {
  display(): string;
}

// Klasa generyczna może implementować interfejsy:
class SortedList<T extends Comparable<T> & Displayable> {
  private items: T[] = [];

  add(item: T): void {
    this.items.push(item);
    this.items.sort((a, b) => a.compareTo(b));
  }

  getAll(): T[] {
    return [...this.items];
  }

  display(): string {
    return this.items.map(i => i.display()).join(', ');
  }
}
```

### Dziedziczenie klas generycznych

```tsx
// Klasa bazowa:
class Container<T> {
  constructor(protected value: T) {}

  getValue(): T {
    return this.value;
  }
}

// Podklasa ze stałym T:
class StringContainer extends Container<string> {
  toUpperCase(): string {
    return this.value.toUpperCase();
  }
}

// Podklasa zachowująca T:
class LoggedContainer<T> extends Container<T> {
  private logs: string[] = [];

  override getValue(): T {
    this.logs.push(`Accessed at${new Date().toISOString()}`);
    return super.getValue();
  }

  getLogs(): string[] {
    return [...this.logs];
  }
}

// Podklasa zawężająca T:
class NumberContainer<T extends number> extends Container<T> {
  double(): number {
    return (this.value as number) * 2;
  }
}
```

---

## 6.4 Ograniczenia generyczne (`extends`)

### Podstawowe ograniczenia

Operator `extends` w kontekście generycznym nie oznacza dziedziczenia – oznacza **ograniczenie**: T musi być kompatybilny ze wskazanym typem (musi mieć co najmniej te właściwości).

```tsx
// T musi mieć właściwość length:
function longest<T extends { length: number }>(a: T, b: T): T {
  return a.length >= b.length ? a : b;
}

longest('hello', 'hi');      // ✅ string ma length
longest([1, 2, 3], [1, 2]);  // ✅ array ma length
longest({ length: 5 }, { length: 3 }); // ✅
// longest(1, 2);             // ❌ number nie ma length

// T musi być obiektem (nie prymitywem):
function cloneObject<T extends object>(obj: T): T {
  return { ...obj };
}

// T musi być tablicą:
function flatten<T>(arr: T[][]): T[] {
  return arr.flat();
}
```

### `keyof` z ograniczeniami generycznymi

Jeden z najważniejszych wzorców – bezpieczne pobieranie pól obiektu.

```tsx
// K musi być kluczem T – gwarantuje bezpieczny dostęp:
function getField<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const user = { id: 1, name: 'Jan', age: 30 };
getField(user, 'name');   // string ✅
getField(user, 'age');    // number ✅
// getField(user, 'xyz'); // ❌ 'xyz' nie jest kluczem user

// Bezpieczne ustawianie pola:
function setField<T, K extends keyof T>(obj: T, key: K, value: T[K]): T {
  return { ...obj, [key]: value };
}

setField(user, 'name', 'Anna');  // ✅
// setField(user, 'name', 42);   // ❌ 42 nie pasuje do string

// Pick – własna implementacja:
function pick<T, K extends keyof T>(obj: T, keys: K[]): Pick<T, K> {
  const result = {} as Pick<T, K>;
  keys.forEach(k => { result[k] = obj[k]; });
  return result;
}

pick(user, ['name', 'age']); // { name: string; age: number }
```

### Ograniczenia z `extends` i union

```tsx
// T musi być string lub number:
function toArray<T extends string | number>(value: T | T[]): T[] {
  return Array.isArray(value) ? value : [value];
}

toArray(1);           // [1]   : number[]
toArray('a');         // ['a'] : string[]
toArray([1, 2, 3]);   // [1, 2, 3] : number[]

// Conditional type z ograniczeniem:
type NonNullableProps<T> = {
  [K in keyof T as T[K] extends null | undefined ? never : K]: T[K];
};

interface Profile {
  name: string;
  email: string | null;
  phone?: string;
  age: number;
}

type RequiredProfile = NonNullableProps<Profile>;
// { name: string; age: number } – pola nullable/optional usunięte
```

### Wzorzec: ograniczenie do konstruktora

```tsx
// T musi być klasą (konstruktorem):
function getInstance<T>(ctor: new () => T): T {
  return new ctor();
}

// Z parametrami konstruktora:
function createInstance<T>(ctor: new (...args: any[]) => T, ...args: any[]): T {
  return new ctor(...args);
}

// Mixin factory – praktyczny wzorzec:
type Constructor<T = {}> = new (...args: any[]) => T;

function withTimestamp<TBase extends Constructor>(Base: TBase) {
  return class extends Base {
    readonly createdAt = new Date();
  };
}

function withId<TBase extends Constructor>(Base: TBase) {
  return class extends Base {
    readonly id = Math.random().toString(36).slice(2);
  };
}

class BaseModel {
  constructor(public name: string) {}
}

const TimestampedModel = withTimestamp(withId(BaseModel));
const m = new TimestampedModel('test');
m.name;      // string
m.id;        // string
m.createdAt; // Date
```

### Ograniczenia rekurencyjne

```tsx
// T musi być porównywalny sam ze sobą (jak Comparable<T>):
function max<T extends Comparable<T>>(items: T[]): T | undefined {
  if (items.length === 0) return undefined;
  return items.reduce((best, item) =>
    item.compareTo(best) > 0 ? item : best
  );
}

// Głęboki dostęp do zagnieżdżonego pola:
function deepGet<T, K1 extends keyof T, K2 extends keyof T[K1]>(
  obj: T,
  key1: K1,
  key2: K2
): T[K1][K2] {
  return obj[key1][key2];
}

const config = {
  server: { host: 'localhost', port: 3000 },
  db: { url: 'mongodb://...', name: 'mydb' }
};

deepGet(config, 'server', 'host'); // string ✅
deepGet(config, 'db', 'name');     // string ✅
// deepGet(config, 'server', 'url'); // ❌ 'url' nie istnieje w server
```

### Wzorzec `infer` – wyciąganie typów

`infer` działa tylko wewnątrz conditional types i pozwala “wyciągnąć” fragment typu.

```tsx
// Odpakowywanie Promise:
type Awaited<T> = T extends Promise<infer U> ? Awaited<U> : T;

type A = Awaited<Promise<string>>;           // string
type B = Awaited<Promise<Promise<number>>>;  // number

// Typ elementu tablicy:
type ElementType<T> = T extends (infer U)[] ? U : never;

type E1 = ElementType<string[]>;  // string
type E2 = ElementType<number[]>;  // number
type E3 = ElementType<string>;    // never

// Typ parametrów funkcji:
type FirstParam<T> = T extends (first: infer F, ...rest: any[]) => any ? F : never;

type F1 = FirstParam<(x: number, y: string) => void>; // number
type F2 = FirstParam<() => void>;                      // never

// Typ zwracany metody z obiektu:
type MethodReturnType<T, K extends keyof T> =
  T[K] extends (...args: any[]) => infer R ? R : never;

interface Service {
  getUser(id: number): Promise<User>;
  getName(): string;
}

type UserResult = MethodReturnType<Service, 'getUser'>; // Promise<User>
type NameResult  = MethodReturnType<Service, 'getName'>; // string
```

---

## Zaawansowane wzorce z generykami

### Builder z generykami

```tsx
// Typowany builder – każdy krok zwęża typ:
type BuilderState = {
  name?: string;
  age?: number;
  email?: string;
};

class UserBuilder<T extends BuilderState = {}> {
  private data: T;

  constructor(data: T = {} as T) {
    this.data = data;
  }

  setName<S extends string>(name: S): UserBuilder<T & { name: S }> {
    return new UserBuilder({ ...this.data, name });
  }

  setAge(age: number): UserBuilder<T & { age: number }> {
    return new UserBuilder({ ...this.data, age });
  }

  setEmail(email: string): UserBuilder<T & { email: string }> {
    return new UserBuilder({ ...this.data, email });
  }

  // build() dostępne tylko gdy wszystkie wymagane pola są ustawione:
  build(this: UserBuilder<BuilderState & { name: string; age: number }>): {
    name: string;
    age: number;
    email?: string;
  } {
    return this.data as any;
  }
}

const user = new UserBuilder()
  .setName('Jan')
  .setAge(30)
  .build(); // ✅

// new UserBuilder().build(); // ❌ brak name i age
```

### Result type – bezpieczna obsługa błędów

```tsx
type Ok<T> = { ok: true; value: T };
type Err<E> = { ok: false; error: E };
type Result<T, E = Error> = Ok<T> | Err<E>;

function ok<T>(value: T): Ok<T> {
  return { ok: true, value };
}

function err<E>(error: E): Err<E> {
  return { ok: false, error };
}

function divide(a: number, b: number): Result<number, string> {
  if (b === 0) return err('Dzielenie przez zero');
  return ok(a / b);
}

const result = divide(10, 2);
if (result.ok) {
  console.log(result.value); // number ✅
} else {
  console.error(result.error); // string ✅
}

// Mapowanie Result:
function mapResult<T, U, E>(
  result: Result<T, E>,
  fn: (value: T) => U
): Result<U, E> {
  if (result.ok) return ok(fn(result.value));
  return result;
}
```

---

## Podsumowanie – Cheat Sheet

| Co chcę zapisać | Składnia |
| --- | --- |
| Prosta funkcja generyczna | `function f<T>(arg: T): T` |
| Wiele parametrów typowych | `function f<T, U>(a: T, b: U): [T, U]` |
| Domyślny typ generyczny | `function f<T = string>(arg?: T): T` |
| Ograniczenie do obiektu | `function f<T extends object>(arg: T)` |
| Ograniczenie do klucza | `function f<T, K extends keyof T>(obj: T, key: K): T[K]` |
| Ograniczenie do konstruktora | `function f<T>(ctor: new () => T): T` |
| Generyczny interfejs | `interface Box<T> { value: T }` |
| Generyczna klasa | `class Stack<T> { items: T[] = [] }` |
| Podklasa ze stałym T | `class StringBox extends Box<string>` |
| Podklasa z zachowanym T | `class LoggedBox<T> extends Box<T>` |
| Typ elementu tablicy | `type Item<T> = T extends (infer U)[] ? U : never` |
| Odpakowywanie Promise | `type Unwrap<T> = T extends Promise<infer U> ? U : T` |
| Filtrowanie kluczy | `[K in keyof T as T[K] extends X ? K : never]` |
| Result type | `type Result<T, E = Error> = \| { ok: true; value: T } \| { ok: false; error: E }` |

---

*Notatki dotyczą TypeScript 5.x*
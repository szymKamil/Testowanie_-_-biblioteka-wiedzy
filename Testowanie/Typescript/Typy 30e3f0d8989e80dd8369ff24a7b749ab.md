# Typy

# TypeScript – Rozdział 2: Podstawowe typy

> **Zakres:** Typy prymitywne · `any`, `unknown`, `never`, `void` · `null` i `undefined` · Tablice i krotki · Enum
> 

---

## 2.1 Typy prymitywne

TypeScript rozszerza JavaScript o statyczne typowanie. Typ zapisuje się po nazwie zmiennej za dwukropkiem – składnia podobna do Javy, ale adnotacja jest opcjonalna gdy TypeScript może ją wywnioskować.

```tsx
// Jawna adnotacja:
let name: string  = 'Jan';
let age:  number  = 30;
let active: boolean = true;

// Inferowanie – TS sam ustala typ z wartości:
let city = 'Warszawa'; // typ: string (inferowany)
let year = 2026;       // typ: number (inferowany)
```

### Przegląd typów prymitywnych

| Typ | Opis | Przykład |
| --- | --- | --- |
| `string` | Ciąg znaków (UTF-16) | `'Jan'`, `"hello"`, ``template ${x}`` |
| `number` | Liczba (int i float w jednym) | `42`, `3.14`, `NaN`, `Infinity` |
| `boolean` | Wartość logiczna | `true`, `false` |
| `bigint` | Duże liczby całkowite (ES2020) | `100n`, `BigInt(9999)` |
| `symbol` | Unikalny identyfikator | `Symbol('id')` |

```tsx
// string – template literals:
const greeting = `Cześć, ${name}! Masz ${age} lat.`; // string

// number – jeden typ dla int i float (inaczej niż Java!):
const int:   number = 42;
const float: number = 3.14;
const hex:   number = 0xFF;
const nan:   number = NaN;       // też number!
const inf:   number = Infinity;  // też number!

// bigint – dla liczb większych niż Number.MAX_SAFE_INTEGER:
const big: bigint = 9007199254740993n;
// big + 1n  ✅
// big + 1   ❌ błąd – nie można mieszać bigint z number

// symbol – zawsze unikalny, nawet gdy opis taki sam:
const s1 = Symbol('id');
const s2 = Symbol('id');
s1 === s2; // false – każdy Symbol jest unikalny
```

> **💡 Dla programisty Java:** W JS/TS nie ma rozróżnienia na `int`, `long`, `float`, `double` – jest jeden typ `number` (IEEE 754 double precision). Do dużych liczb całkowitych służy `bigint`.
> 

---

## 2.2 Typy specjalne: `any`, `unknown`, `never`, `void`

### `any` – ucieczka z systemu typów

`any` wyłącza sprawdzanie typów – TypeScript traktuje wartość jak zwykły JS. **Unikaj** – tracisz całą ochronę kompilatora.

```tsx
let data: any = 42;
data = 'teraz string';   // OK – brak błędu
data = { id: 1 };       // OK – brak błędu
data.foo.bar.baz;       // OK w TS, ale crash w runtime!

// Wirus any – rozszerza się na kolejne wywołania:
function processAny(x: any) { return x.value; }
const result = processAny(someInput); // result: any – nadal brak ochrony
```

> **⚠️ Zasada:** Jeśli nie znasz typu – użyj `unknown`, nie `any`.
> 

### `unknown` – bezpieczna nieznana wartość

Bezpieczna alternatywa dla `any`. Przed użyciem **musisz** sprawdzić typ (type narrowing).

```tsx
let input: unknown = getExternalData();

// ❌ Nie można od razu użyć:
// input.toUpperCase(); // błąd kompilacji

// ✅ Najpierw sprawdź typ:
if (typeof input === 'string') {
  console.log(input.toUpperCase()); // input: string ✅
}

if (typeof input === 'object' && input !== null && 'name' in input) {
  console.log((input as { name: string }).name);
}

// Typowy wzorzec – obsługa błędów z try/catch:
try {
  await riskyOperation();
} catch (err: unknown) {
  if (err instanceof Error) {
    console.error(err.message); // err: Error ✅
  }
}
```

### `never` – wartość niemożliwa

Reprezentuje wartość, która **nigdy nie zostanie zwrócona**. Dwie główne sytuacje:

```tsx
// 1. Funkcja zawsze rzuca błąd:
function throwError(msg: string): never {
  throw new Error(msg); // nigdy nie wraca
}

// 2. Nieskończona pętla:
function infiniteLoop(): never {
  while (true) {}
}

// 3. Exhaustiveness check – sprawdzenie wyczerpania przypadków:
type Shape = 'circle' | 'rect' | 'triangle';

function describe(shape: Shape): string {
  switch (shape) {
    case 'circle':   return 'Koło';
    case 'rect':     return 'Prostokąt';
    case 'triangle': return 'Trójkąt';
    default:
      const _exhaustive: never = shape;
      // ↑ Błąd kompilacji gdy dodasz nowy wariant bez obsługi ✅
      return _exhaustive;
  }
}
```

> **💡 Dla programisty Java:** `never` nie ma bezpośredniego odpowiednika w Javie. Najbliżej jest `void` przy metodach rzucających `UnsupportedOperationException`, ale `never` jest częścią systemu typów i może być używany w wyrażeniach.
> 

### `void` – brak wartości zwracanej

Funkcja, która nie zwraca żadnej wartości użytecznej.

```tsx
function logMessage(msg: string): void {
  console.log(msg);
  // brak return lub return undefined
}

// Różnica od Java: void w TS to typ (można przypisać undefined):
let v: void = undefined; // OK
// let v: void = 42;     // ❌

// Callbacki z void – funkcja może cokolwiek zwrócić, wynik jest ignorowany:
type Callback = () => void;
const cb: Callback = () => 42; // OK – wynik ignorowany
```

### Porównanie typów specjalnych

| Typ | Kiedy używać | Wartość zwracana | Przypisanie |
| --- | --- | --- | --- |
| `any` | Nigdy (lub migracja JS→TS) | Cokolwiek | Cokolwiek |
| `unknown` | Dane zewnętrzne, JSON, catch | Wymaga narrowing | Cokolwiek |
| `void` | Funkcje bez return | `undefined` | Tylko `undefined` |
| `never` | Throw, infinite loop, exhaustiveness | Brak (nigdy) | Nic |

---

## 2.3 `null` i `undefined`

W TypeScript (przy włączonym `strictNullChecks`, które jest częścią `strict: true`) `null` i `undefined` są **osobnymi typami** – nie można ich przypisać do `string` czy `number` bez jawnej deklaracji.

```tsx
// ❌ Błąd przy strictNullChecks:
let name: string = null;      // ❌
let age:  number = undefined; // ❌

// ✅ Jawne dopuszczenie null/undefined:
let name: string | null      = null;
let age:  number | undefined = undefined;
let val:  string | null | undefined; // opcjonalne pole

// Różnica między null a undefined:
// null      = celowo pusta wartość (brak danych)
// undefined = wartość nie została przypisana / pole nie istnieje
```

### Operatory do pracy z null/undefined

```tsx
const user = getUser(); // User | null | undefined

// ?. (optional chaining) – bezpieczny dostęp do zagnieżdżonych pól:
const city = user?.address?.city;    // undefined zamiast błędu
const len  = user?.name?.length;     // undefined zamiast błędu

// ?? (nullish coalescing) – fallback tylko dla null/undefined:
const name  = user?.name ?? 'Anonim'; // 'Anonim' gdy null/undefined
const count = user?.count ?? 0;       // 0 gdy null/undefined

// Uwaga: ?? vs || (OR):
const a = 0  ?? 'default'; // 0   – bo 0 nie jest null/undefined
const b = 0  || 'default'; // 'default' – bo 0 jest falsy!
const c = '' ?? 'default'; // ''  – bo '' nie jest null/undefined
const d = '' || 'default'; // 'default' – bo '' jest falsy!

// ??= (nullish assignment) – przypisz tylko gdy null/undefined:
user.nickname ??= 'Użytkownik';

// Non-null assertion (!) – "ufam, że to nie jest null":
const name2 = user!.name; // TS zakłada że user ≠ null/undefined
// Używaj ostrożnie – crash w runtime jeśli user jest null!
```

> **💡 Dla programisty Java:** `??` to odpowiednik `Objects.requireNonNullElse()` lub ternary `x != null ? x : default`. `?.` to odpowiednik `Optional.map()` ale zwięzły.
> 

### `strictNullChecks` – dlaczego ważne

```tsx
// Bez strictNullChecks (ryzykowne – jak Java bez Optional):
let name: string = null; // OK – ale crash gdy użyjesz name.length

// Z strictNullChecks (zalecane):
let name: string | null = null;
// name.length; // ❌ błąd kompilacji – musisz obsłużyć null
if (name !== null) {
  name.length; // ✅ TypeScript wie, że name: string
}
```

---

## 2.4 Tablice i krotki (tuple)

### Tablice

```tsx
// Dwa równoważne zapisy:
let numbers: number[]       = [1, 2, 3];
let strings: Array<string>  = ['a', 'b', 'c'];

// Tablica obiektów:
let users: { name: string; age: number }[] = [];

// Tablica typów mieszanych (union):
let mixed: (string | number)[] = [1, 'dwa', 3, 'cztery'];

// ReadonlyArray – niemutowalna tablica:
const frozen: ReadonlyArray<number> = [1, 2, 3];
const frozen2: readonly number[]    = [1, 2, 3]; // skrócony zapis
// frozen.push(4); // ❌ błąd – brak metod mutujących

// Operacje na tablicach (zawsze zwracają nową tablicę – immutable style):
const nums = [1, 2, 3, 4, 5];
const doubled  = nums.map(n => n * 2);       // [2, 4, 6, 8, 10]
const evens    = nums.filter(n => n % 2 === 0); // [2, 4]
const sum      = nums.reduce((acc, n) => acc + n, 0); // 15
const first3   = nums.slice(0, 3);           // [1, 2, 3]
```

### Krotki (tuple)

Tablica o **stałej długości** i **znanych typach na każdej pozycji**. Odpowiednik np. `Pair<A, B>` z Javy, ale wbudowany w język.

```tsx
// Podstawowy tuple:
let point: [number, number] = [10, 20];
let entry: [string, number] = ['klucz', 42];

// Named tuple (TS 4.0+) – lepsza czytelność:
let rgb: [red: number, green: number, blue: number] = [255, 0, 128];
let coord: [x: number, y: number, z?: number] = [1, 2]; // z opcjonalne

// Opcjonalne elementy muszą być na końcu:
let pair: [string, number?] = ['tylko string']; // OK
// let bad: [string?, number] = []; // ❌

// Destrukturyzacja tuple:
const [x, y] = point;        // x=10, y=20
const [name, score] = entry; // name='klucz', score=42

// Rest w tuple:
type StringsAndNumber = [string, ...string[], number];
const data: StringsAndNumber = ['a', 'b', 'c', 42]; // ostatni: number

// Readonly tuple:
const frozen: readonly [number, number] = [1, 2];
// frozen[0] = 5; // ❌

// Praktyczne zastosowanie – zwracanie wielu wartości z funkcji:
function minMax(arr: number[]): [min: number, max: number] {
  return [Math.min(...arr), Math.max(...arr)];
}

const [min, max] = minMax([3, 1, 4, 1, 5, 9]);
// min: 1, max: 9 – nazwy z named tuple w IDE ✅
```

> **💡 Dla programisty Java:** Tuple to jak `record` z Javy 16+ albo `Pair<A, B>`, ale wbudowany w język bez boilerplate. W Javie musisz tworzyć osobne klasy – w TS to natywny typ.
> 

---

## 2.5 Enum

Enum pozwala definiować zestawy nazwanych stałych. W TypeScript enum istnieje zarówno jako typ jak i jako wartość runtime (w odróżnieniu od interfejsów).

### Enum numeryczny (domyślny)

```tsx
enum Direction {
  Up,    // 0
  Down,  // 1
  Left,  // 2
  Right, // 3
}

let dir: Direction = Direction.Up;
console.log(dir);             // 0 (wartość numeryczna)
console.log(Direction[0]);    // 'Up' – odwrotne mapowanie!
console.log(Direction.Up);    // 0

// Własne wartości startowe:
enum HttpStatus {
  OK        = 200,
  Created   = 201,
  BadRequest = 400,
  NotFound  = 404,
  ServerError = 500,
}

// Użycie:
function handleStatus(status: HttpStatus): string {
  switch (status) {
    case HttpStatus.OK:      return 'Sukces';
    case HttpStatus.NotFound: return 'Nie znaleziono';
    default:                 return 'Inny status';
  }
}
```

### Enum stringowy (zalecany w praktyce)

```tsx
enum Status {
  Active   = 'ACTIVE',
  Inactive = 'INACTIVE',
  Pending  = 'PENDING',
}

let s: Status = Status.Active;
console.log(s); // 'ACTIVE' – czytelne w logach i debuggerze!

// Brak odwrotnego mapowania (inaczej niż enum numeryczny):
// Status['ACTIVE']; // undefined
```

> **💡 Dla programisty Java:** String enum w TS to odpowiednik enum z polem `String value` w Javie:
> 
> 
> ```java
> public enum Status {
>   ACTIVE("ACTIVE"), INACTIVE("INACTIVE");
>   private final String value;
> }
> ```
> 
> W TS jest to znacznie zwięzłe.
> 

### Const enum – optymalizacja wydajności

```tsx
// Kompilator inlinuje wartości – nie ma kodu runtime:
const enum Color { Red = 0, Green = 1, Blue = 2 }

let c = Color.Red;
// Po kompilacji: let c = 0; (Color znika całkowicie)

// Kiedy używać const enum:
// ✅ Wewnętrzne stałe aplikacji – dla wydajności
// ❌ Biblioteki eksportowane – problemy z .d.ts i bundlerami
// ❌ Gdy potrzebujesz odwrotnego mapowania (Color[0] → 'Red')
```

### Enum vs alternatywy

W nowoczesnym TypeScript często preferuje się **union literal types** lub **`as const`** zamiast enum:

```tsx
// Enum:
enum Direction { Up = 'UP', Down = 'DOWN' }

// Alternatywa 1 – union literal (prostsze, mniej kodu):
type Direction = 'UP' | 'DOWN';

// Alternatywa 2 – as const (obiekt + typ):
const Direction = { Up: 'UP', Down: 'DOWN' } as const;
type Direction = typeof Direction[keyof typeof Direction]; // 'UP' | 'DOWN'
```

|  | Enum | Union literal | `as const` |
| --- | --- | --- | --- |
| Istnieje w runtime | ✅ | ❌ | ✅ (obiekt) |
| Odwrotne mapowanie | ✅ (numeryczny) | ❌ | ❌ |
| Iterowanie po wartościach | ✅ | ❌ | ✅ |
| Prosty zapis | ✅ | ✅ | Średni |
| Treeshaking | ❌ (const enum ✅) | ✅ | ✅ |

---

## 2.6 Adnotacje typów a inferowanie

TypeScript sam ustala typy gdy jest to możliwe. Jawna adnotacja jest potrzebna gdy inferowanie nie wystarczy lub dla czytelności.

```tsx
// Inferowanie – TypeScript sam ustala typ:
const name = 'Jan';     // typ: 'Jan' (literal type!)
let   role = 'admin';   // typ: string (let – może się zmienić)
const nums = [1, 2, 3]; // typ: number[]
const user = { id: 1, name: 'Jan' }; // typ: { id: number; name: string }

// Jawna adnotacja – gdy inferowanie jest zbyt szerokie lub nieczytelne:
const emptyArray: string[] = []; // bez adnotacji: never[]
const maybeNull: string | null = null;

// Funkcje – adnotacja parametrów zawsze wymagana (TS nie inferuje):
function add(a: number, b: number): number {
  return a + b;
}

// Kiedy adnotacja jest konieczna:
// 1. Puste tablice
// 2. null/undefined jako wartość inicjalna
// 3. Parametry funkcji
// 4. Gdy chcesz szerszy typ niż inferowany
// 5. Publiczne API (kontrakt)
```

### `satisfies` – walidacja bez zawężania (TS 4.9+)

```tsx
// Problem z jawną adnotacją – zawęża typ:
const palette: Record<string, string | number[]> = {
  red: [255, 0, 0],
  blue: '#0000FF',
};
palette.red; // string | number[] – straciliśmy precyzję!

// satisfies – waliduje kształt BEZ zawężania:
const palette2 = {
  red:  [255, 0, 0],
  blue: '#0000FF',
} satisfies Record<string, string | number[]>;

palette2.red;  // number[] ✅ – zachowany precyzyjny typ
palette2.blue; // string  ✅ – zachowany precyzyjny typ
```

---

## Podsumowanie – Cheat Sheet

| Co chcę zapisać | Składnia |
| --- | --- |
| Prymitywy | `string`, `number`, `boolean`, `bigint`, `symbol` |
| Wyłącz sprawdzanie typów | `any` – unikaj! |
| Nieznany typ | `unknown` – bezpieczne, wymaga narrowing |
| Funkcja bez return | `void` |
| Funkcja nigdy nie wraca | `never` (throw / infinite loop) |
| Dopuszczenie null | `string \| null` |
| Dopuszczenie undefined | `string \| undefined` lub pole `name?: string` |
| Bezpieczny dostęp | `user?.address?.city` |
| Fallback dla null/undefined | `value ?? 'default'` |
| Tablica | `number[]` lub `Array<number>` |
| Niemutowalna tablica | `readonly number[]` lub `ReadonlyArray<number>` |
| Tuple | `[string, number]` |
| Named tuple | `[name: string, age: number]` |
| Enum numeryczny | `enum Color { Red, Green, Blue }` |
| Enum stringowy | `enum Status { Active = 'ACTIVE' }` |
| Enum bez runtime kodu | `const enum Color { Red = 0 }` |
| Walidacja bez zawężania | `satisfies Record<string, ...>` |
| Inferowanie | Brak adnotacji – TS ustala sam |
| Wymuś literal type | `const x = 'admin' as const` → typ `'admin'` |

---

*Notatki dotyczą TypeScript 5.x*
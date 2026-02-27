# Funkcje

# TypeScript – Rozdział 3: Funkcje

> **Zakres:** Typowanie parametrów i wartości zwracanej · Parametry opcjonalne i domyślne · Przeciążanie funkcji · Funkcje strzałkowe
> 
> 
> **Uzupełnienia JS:** Closures · Zakres zmiennych (`var`/`let`/`const`) · Mechanizm `this` · Kontekst wywołania
> 

---

## 🟨 Fundamenty JS: Zakres zmiennych

> Dla programisty Java: w JS zakres zmiennych działał historycznie inaczej niż w językach blokowych. TypeScript wymusza nowszy, bezpieczny styl – ale warto rozumieć dlaczego.
> 

### `var` vs `let` vs `const`

```tsx
// var – zakres funkcji (function scope), NIE bloku. Legacy, unikaj:
function example() {
  if (true) {
    var x = 10; // x istnieje w całej funkcji!
  }
  console.log(x); // 10 – zaskakujące dla programisty Java
}

// let – zakres bloku (block scope), jak w Javie:
function example2() {
  if (true) {
    let y = 10; // y istnieje tylko w bloku if
  }
  // console.log(y); // ❌ ReferenceError – jak w Javie ✅
}

// const – zakres bloku + brak reassignment (nie oznacza immutability!):
const user = { name: 'Jan' };
// user = {}; // ❌ Cannot reassign
user.name = 'Anna'; // ✅ obiekt jest mutowalny – const dotyczy referencji!

// W TypeScript: zawsze używaj const, gdy nie reasygnujesz, let gdy reasygnujesz.
// var – nigdy.
```

### Hoisting – zjawisko bez odpowiednika w Javie

```tsx
// var jest "podnoszone" (hoisted) na szczyt funkcji:
console.log(a); // undefined – nie ReferenceError!
var a = 5;

// let/const – są hoisted, ale nie zainicjowane (Temporal Dead Zone):
// console.log(b); // ❌ ReferenceError: Cannot access 'b' before initialization
let b = 5;

// Funkcje deklarowane (function declarations) są hoisted w całości:
greet(); // ✅ działa przed deklaracją!
function greet() { console.log('Cześć'); }

// Funkcje jako zmienne (function expressions) – NIE są hoisted:
// hello(); // ❌ ReferenceError
const hello = () => console.log('Hello');
```

---

## 🟨 Fundamenty JS: Closures (domknięcia)

> Closure to funkcja, która "pamięta" zmienne ze swojego zewnętrznego zakresu nawet po tym, jak ten zakres zakończył działanie. To jeden z najważniejszych mechanizmów JS – bez odpowiednika w Javie (przed Java 8).
> 

### Podstawy closures

```tsx
function makeCounter(start: number = 0) {
  let count = start; // ta zmienna jest "zamknięta" w closurze

  return {
    increment: () => ++count,
    decrement: () => --count,
    getValue:  () => count,
    reset:     () => { count = start; },
  };
}

const counter = makeCounter(10);
counter.increment(); // 11
counter.increment(); // 12
counter.decrement(); // 11
counter.getValue();  // 11

// count jest niedostępne z zewnątrz – jak private w Javie,
// ale bez klas i bez słowa kluczowego private!
// (counter as any).count // undefined
```

### Closure jako prywatny stan – porównanie z Javą

```tsx
// Java:
// class Counter {
//   private int count;
//   public Counter(int start) { this.count = start; }
//   public int increment() { return ++count; }
// }

// JavaScript/TypeScript – closure zamiast klasy:
function createCounter(start: number) {
  let count = start; // prywatny stan bez klasy

  function increment(): number { return ++count; }
  function getCount(): number  { return count; }

  return { increment, getCount }; // eksponujemy tylko to co chcemy
}

// Alternatywnie klasa w TS (bardziej jak Java):
class Counter {
  private count: number;

  constructor(start: number) {
    this.count = start;
  }

  increment(): number { return ++this.count; }
  getCount(): number  { return this.count; }
}
```

### Closures w praktyce – Playwright

```tsx
// Closure często pojawia się przy callbackach testowych:
function createPageHelper(baseUrl: string) {
  // baseUrl jest "zamknięte" – dostępne we wszystkich zwracanych funkcjach:
  return {
    navigate: async (page: Page, path: string) => {
      await page.goto(`${baseUrl}${path}`); // baseUrl z closure
    },
    getFullUrl: (path: string) => `${baseUrl}${path}`,
  };
}

const helper = createPageHelper('<https://example.com>');
// helper ma dostęp do baseUrl bez przekazywania go za każdym razem
```

### Pułapka closure w pętli

```tsx
// ❌ Klasyczna pułapka z var (wszystkie callbacki mają tę samą i):
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100); // wypisze: 3, 3, 3 (!)
}

// ✅ Z let – każda iteracja ma własne i:
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100); // wypisze: 0, 1, 2 ✅
}

// TypeScript domyślnie używa let – ta pułapka jest rzadsza, ale warto znać
```

---

## 🟨 Fundamenty JS: Mechanizm `this`

> `this` w JS to jeden z największych "gotchas" dla programistów Java. W Javie `this` zawsze wskazuje na bieżącą instancję klasy. W JS `this` zależy od **sposobu wywołania** funkcji – nie od miejsca jej definicji (z wyjątkiem arrow functions).
> 

### Cztery sposoby wywołania i ich wpływ na `this`

```tsx
// 1. Wywołanie metody (method call) – this = obiekt przed kropką:
const obj = {
  name: 'Jan',
  greet() {
    console.log(this.name); // 'Jan' – this = obj
  }
};
obj.greet(); // ✅ 'Jan'

// 2. Wywołanie funkcji (function call) – this = undefined (strict mode) lub global:
function standalone() {
  console.log(this); // undefined w strict mode (TS domyślnie)
}
standalone();

// 3. Wywołanie z new (constructor call) – this = nowy obiekt:
function Person(this: { name: string }, name: string) {
  this.name = name; // this = nowy obiekt
}
const p = new Person('Anna'); // this = p

// 4. Wywołanie z call/apply/bind – this = jawnie podany:
function greet(this: { name: string }) {
  return `Cześć, ${this.name}`;
}
greet.call({ name: 'Jan' });   // 'Cześć, Jan'
greet.apply({ name: 'Anna' }); // 'Cześć, Anna'
const boundGreet = greet.bind({ name: 'Piotr' });
boundGreet(); // 'Cześć, Piotr'
```

### Problem z `this` w callbackach – klasyczna pułapka

```tsx
class Timer {
  private seconds = 0;
  private label: string;

  constructor(label: string) {
    this.label = label;
  }

  // ❌ Klasyczna funkcja w setInterval – this jest tracone:
  startBroken(): void {
    setInterval(function() {
      // this nie jest Timer! W strict mode: undefined
      // this.seconds++; // ❌ błąd runtime
    }, 1000);
  }

  // ✅ Rozwiązanie 1 – arrow function (przechwytuje this leksykalnie):
  start(): void {
    setInterval(() => {
      this.seconds++; // ✅ this = Timer (lexical this z arrow)
      console.log(`${this.label}: ${this.seconds}s`);
    }, 1000);
  }

  // ✅ Rozwiązanie 2 – bind:
  startWithBind(): void {
    setInterval(function(this: Timer) {
      this.seconds++;
    }.bind(this), 1000);
  }

  // ✅ Rozwiązanie 3 – zmienna self/that (stary styl, historyczny):
  startWithSelf(): void {
    const self = this; // zachowujemy referencję
    setInterval(function() {
      self.seconds++; // self zamiast this
    }, 1000);
  }
}
```

### `this` w Playwright

```tsx
import { test, expect } from '@playwright/test';

class LoginPage {
  private page: Page;
  private url = '/login';

  constructor(page: Page) {
    this.page = page;
  }

  async navigate(): Promise<void> {
    await this.page.goto(this.url); // this = LoginPage ✅
  }

  async login(email: string, password: string): Promise<void> {
    await this.page.fill('#email', email);
    await this.page.fill('#password', password);

    // ❌ Potencjalny problem – this może być stracone przy destrukturyzacji:
    // const { fill } = this.page; // fill traci kontekst this!

    // ✅ Zawsze używaj this.page.metoda():
    await this.page.click('button[type="submit"]');
  }
}
```

---

## 3.1 Typowanie parametrów i wartości zwracanej

### Podstawowa składnia

```tsx
// Inferowanie – TS ustali typ zwracany sam:
function add(a: number, b: number) {
  return a + b; // TS inferuje: number
}

// Jawna adnotacja – zalecana dla publicznego API:
function add(a: number, b: number): number {
  return a + b;
}

// Obiekt jako parametr:
function printUser(user: { name: string; age: number }): void {
  console.log(`${user.name}, ${user.age} lat`);
}

// Tablica jako parametr:
function filterPositive(nums: number[]): number[] {
  return nums.filter(n => n > 0);
}
```

### `void` vs `never`

| Typ | Znaczenie | Kiedy |
| --- | --- | --- |
| `void` | Funkcja nic nie zwraca | Procedury, callbacki |
| `never` | Funkcja **nigdy** nie wraca | Throw, infinite loop |

```tsx
function logMessage(msg: string): void {
  console.log(msg);
}

function throwError(msg: string): never {
  throw new Error(msg);
}
```

> **💡 Dla programisty Java:** `void` w TS/JS to typ (można go używać jako wartość), w Javie `void` to brak wartości. `never` nie ma odpowiednika w Javie.
> 

---

## 3.2 Parametry opcjonalne i domyślne

### Parametry opcjonalne (`?`)

```tsx
function greet(name: string, greeting?: string): string {
  // greeting: string | undefined – musisz obsłużyć oba przypadki
  return `${greeting ?? 'Cześć'}, ${name}!`;
}

greet('Anna');        // 'Cześć, Anna!'
greet('Anna', 'Hej'); // 'Hej, Anna!'

// Parametry opcjonalne muszą być NA KOŃCU:
// function bad(a?: string, b: number): void {} // ❌
function good(a: number, b?: string): void {}   // ✅
```

### Parametry domyślne

```tsx
function createUser(name: string, role: string = 'user'): object {
  return { name, role };
}

createUser('Jan');           // { name: 'Jan', role: 'user' }
createUser('Jan', 'admin'); // { name: 'Jan', role: 'admin' }
```

### `?` vs wartość domyślna

|  | Opcjonalny `?` | Wartość domyślna |
| --- | --- | --- |
| Typ wewnątrz funkcji | `T \| undefined` | `T` |
| Wartość gdy pominięty | `undefined` | wartość domyślna |
| Przypadek użycia | "nie zawsze potrzebny" | "mam sensowny fallback" |

### Parametry rest (`...`)

```tsx
function sum(...numbers: number[]): number {
  return numbers.reduce((acc, n) => acc + n, 0);
}

sum(1, 2, 3, 4, 5); // 15

// Playwright – zbieranie selektorów:
async function clickAll(page: Page, ...selectors: string[]): Promise<void> {
  for (const selector of selectors) {
    await page.click(selector);
  }
}
```

---

## 3.3 Przeciążanie funkcji (Overloads)

### Składnia

```tsx
// Sygnatury (publiczne – widoczne dla wywołujących):
function format(value: string): string;
function format(value: number, decimals: number): string;
function format(value: Date): string;

// Implementacja (ukryta):
function format(value: string | number | Date, decimals?: number): string {
  if (typeof value === 'string') return value.trim();
  if (typeof value === 'number') return value.toFixed(decimals ?? 2);
  return value.toLocaleDateString('pl-PL');
}

format('  hello  '); // 'hello'
format(3.14, 2);     // '3.14'
format(new Date());  // '21.02.2026'
```

### Overloady vs Union

```tsx
// Union wystarczy – ta sama liczba parametrów, ten sam zwracany typ:
function printId(id: string | number): void {
  console.log(id);
}

// Overload potrzebny – różny typ zwracany zależny od wejścia:
function parse(input: string): number;
function parse(input: number): string;
function parse(input: string | number): string | number {
  return typeof input === 'string' ? parseInt(input) : input.toString();
}

const n = parse('42'); // TS wie: number ✅
const s = parse(42);   // TS wie: string ✅
```

---

## 3.4 Funkcje strzałkowe

### Składnia

```tsx
// Pełna forma:
const add = (a: number, b: number): number => {
  return a + b;
};

// Skrócona (implicit return):
const double = (n: number): number => n * 2;

// Zwracanie obiektu – wymaga nawiasów:
const makeUser = (name: string) => ({ name, active: true });

// Typ jako alias:
type Predicate<T> = (value: T) => boolean;
```

### `function` vs `=>` – kluczowe różnice

| Cecha | `function` | `=>` (arrow) |
| --- | --- | --- |
| **Hoisting** | ✅ dostępna przed definicją | ❌ |
| **`this`** | dynamiczne – zależy od wywołania | leksykalne – z miejsca definicji |
| **`arguments`** | ✅ | ❌ (użyj `...rest`) |
| **Konstruktor (`new`)** | ✅ | ❌ |
| **Callbacki, closures** | ⚠️ pułapki z `this` | ✅ bezpieczne |

> **💡 Kluczowa różnica od Javy:** Arrow functions to nie tylko skrócony zapis lambdy. Ich główna zaleta to **leksykalne `this`** – `this` wewnątrz arrow function to zawsze `this` z otaczającego zakresu, nie dynamiczne jak w metodach. Dlatego są idealne jako callbacki.
> 

### `this` w arrow functions – dlaczego są bezpieczne

```tsx
class Poller {
  private results: string[] = [];

  // ✅ Arrow jako metoda klasy – this zawsze = Poller:
  poll = async (page: Page): Promise<void> => {
    // this = Poller niezależnie od kontekstu wywołania
    const text = await page.textContent('.result');
    this.results.push(text ?? '');
  };

  // Można bezpiecznie przekazać jako callback:
  async runAll(page: Page): Promise<void> {
    await Promise.all([
      this.poll(page),  // ✅
      // Gdyby poll była zwykłą metodą, poniższe byłoby problemem:
      [1, 2].forEach(this.poll), // ✅ arrow – this zachowane
    ]);
  }
}
```

### Generyczne arrow functions w `.tsx`

```tsx
// W plikach .tsx – trailing comma lub extends {} żeby TS nie mylił z JSX tagiem:
const identity = <T,>(arg: T): T => arg;
const first = <T extends object>(arr: T[]): T | undefined => arr[0];
```

---

## Podsumowanie – Cheat Sheet

| Co chcę zapisać | Składnia |
| --- | --- |
| Deklaracja funkcji | `function f(a: string, b: number): boolean` |
| Parametr opcjonalny | `function f(a: string, b?: number)` |
| Parametr domyślny | `function f(a: string, b: number = 0)` |
| Parametry rest | `function f(...args: string[])` |
| Funkcja strzałkowa | `const f = (a: string): number => a.length` |
| Typ funkcji | `type F = (a: string) => number` |
| Overload | `function f(x: string): string;` (+ impl.) |
| `this` bezpieczne | Arrow function lub `bind(this)` |
| Closure – prywatny stan | Zmienna w zewnętrznym zakresie funkcji |
| `let` vs `const` | `const` domyślnie, `let` gdy reasygnujesz, `var` nigdy |
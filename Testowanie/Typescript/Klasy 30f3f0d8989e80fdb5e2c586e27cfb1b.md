# Klasy

# TypeScript – Rozdział 5: Klasy

> **Zakres:** Klasy w TypeScript vs ES6 · Modyfikatory dostępu · Klasy abstrakcyjne · Implementacja interfejsów
> 
> 
> **Uzupełnienia JS:** Prototypy · Łańcuch prototypów vs dziedziczenie Javowe · Jak klasy ES6 działają pod spodem
> 

---

## 🟨 Fundamenty JS: Prototypy i łańcuch prototypów

> **Dla programisty Java:** Java używa klasycznego dziedziczenia opartego na klasach. JavaScript używa **prototypowego dziedziczenia** – klasy w JS/TS to syntactic sugar nad prototypami. Warto zrozumieć ten mechanizm, żeby nie być zaskoczonym zachowaniem runtime.
> 

### Czym jest prototyp?

Każdy obiekt w JS ma ukrytą właściwość `[[Prototype]]` (dostępną przez `__proto__` lub `Object.getPrototypeOf()`), która wskazuje na inny obiekt – jego prototyp. Gdy szukasz właściwości na obiekcie, JS szuka jej najpierw na obiekcie, potem na jego prototypie, potem na prototypie prototypu itd. – to jest **łańcuch prototypów**.

```tsx
// Java (pseudo-kod):
// class Animal { String name; void speak() {} }
// class Dog extends Animal { void bark() {} }
// Dog d = new Dog(); d.speak(); // ze stosu klas

// JavaScript pod spodem (bez cukru składniowego):
const animalProto = {
  speak() {
    return `${this.name} wydaje dźwięk`;
  }
};

const dogProto = Object.create(animalProto); // dogProto.__proto__ = animalProto
dogProto.bark = function() {
  return `${this.name}: Hau!`;
};

// Tworzenie instancji:
const dog = Object.create(dogProto);
dog.name = 'Rex';

dog.bark();  // szuka bark na dog → nie ma → szuka na dogProto → znaleziono ✅
dog.speak(); // szuka speak na dog → nie ma → dogProto → nie ma → animalProto ✅
```

### Klasy ES6 = syntactic sugar nad prototypami

```tsx
// To co piszesz w TS/ES6:
class Animal {
  constructor(public name: string) {}

  speak(): string {
    return `${this.name} wydaje dźwięk`;
  }
}

class Dog extends Animal {
  bark(): string {
    return `${this.name}: Hau!`;
  }
}

// Co JS faktycznie tworzy pod spodem (uproszczone):
// - Animal.prototype.speak = function() { ... }
// - Dog.prototype.__proto__ = Animal.prototype
// - Dog.prototype.bark = function() { ... }

// Można to sprawdzić:
const dog = new Dog('Rex');

Dog.prototype.bark;                        // funkcja bark
Object.getPrototypeOf(Dog.prototype) === Animal.prototype; // true
dog instanceof Dog;    // true
dog instanceof Animal; // true (bo Animal jest w łańcuchu prototypów)
```

### Praktyczne konsekwencje prototypów

```tsx
// 1. Metody są współdzielone między instancjami (jeden obiekt w pamięci):
class Counter {
  count = 0;
  increment() { this.count++; }
}

const c1 = new Counter();
const c2 = new Counter();
c1.increment === c2.increment; // true – ta sama funkcja!
c1.count === c2.count;         // false – osobne pola instancji

// 2. Można dodawać metody do prototypu w runtime (monkey patching):
// (Counter.prototype as any).reset = () => this.count = 0; // możliwe, ale złe

// 3. Arrow functions jako pola klasy – KAŻDA instancja ma kopię:
class Counter2 {
  count = 0;
  increment = () => this.count++; // arrow field – kopia na każdej instancji!
}

const d1 = new Counter2();
const d2 = new Counter2();
d1.increment === d2.increment; // false – osobne kopie!

// Kiedy używać arrow field:
// ✅ Gdy przekazujesz metodę jako callback (this nie zostanie utracone)
// ❌ Gdy tworzysz wiele instancji (pamięć)
```

### `instanceof` a łańcuch prototypów

```tsx
class Shape {}
class Circle extends Shape {}
class ColoredCircle extends Circle {}

const c = new ColoredCircle();

c instanceof ColoredCircle; // true – bezpośrednia klasa
c instanceof Circle;        // true – przodek
c instanceof Shape;         // true – dalszy przodek
c instanceof Object;        // true – wszystko dziedziczy po Object

// Ważne dla Playwright – Page Objects:
class BasePage {
  constructor(protected page: Page) {}
}

class LoginPage extends BasePage {
  async login(email: string, password: string) { /* ... */ }
}

const login = new LoginPage(page);
login instanceof LoginPage; // true
login instanceof BasePage;  // true
```

---

## 🟨 Fundamenty JS: Różnice między JS a Javą w kontekście klas

| Cecha | Java | JavaScript/TypeScript |
| --- | --- | --- |
| Dziedziczenie | Oparte na klasach (compile-time) | Oparte na prototypach (runtime) |
| `private` | Prawdziwe (runtime) | TS `private` – tylko compile-time; `#field` – runtime |
| Interfejsy | Pełny kontrakt runtime | Tylko TypeScript (znikają po kompilacji) |
| Wielokrotne dziedziczenie | ❌ (interfejsy tak) | ❌ (ale mixiny tak) |
| Overriding | `@Override` z walidacją | `override` od TS 4.3 |
| Abstrakcja | `abstract class` pełna | `abstract class` – tylko TS |
| `static` | Statyczne metody/pola | Jak w Javie + `static {}` bloki (ES2022) |
| Konstruktor | Zawsze ten sam co klasa | Można oddzielić funkcję konstruktora |

---

## 5.1 Klasy w TypeScript vs ES6

### Podstawowa składnia

```tsx
class Person {
  name: string; // deklaracja pola – wymagana w TS
  age: number;

  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }

  greet(): string {
    return `Cześć, jestem${this.name}, mam${this.age} lat.`;
  }
}
```

### Parameter Properties – skrócony zapis

```tsx
// Długa forma:
class UserLong {
  name: string;
  email: string;

  constructor(name: string, email: string) {
    this.name = name;
    this.email = email;
  }
}

// Krótka forma – TS automatycznie deklaruje pole i przypisuje wartość:
class User {
  constructor(
    public name: string,
    public age: number,
    private email: string,
    readonly id: number,
  ) {}
}
```

### Pola statyczne

```tsx
class IdGenerator {
  private static nextId = 1;

  static generate(): number {
    return IdGenerator.nextId++;
  }

  static reset(): void {
    IdGenerator.nextId = 1;
  }
}

IdGenerator.generate(); // 1
IdGenerator.generate(); // 2
```

### Gettery i settery

```tsx
class Temperature {
  private _celsius: number;

  constructor(celsius: number) {
    this._celsius = celsius;
  }

  get celsius(): number { return this._celsius; }

  set celsius(value: number) {
    if (value < -273.15) throw new RangeError('Poniżej zera absolutnego!');
    this._celsius = value;
  }

  get fahrenheit(): number {
    return this._celsius * 9 / 5 + 32;
  }
}
```

### Dziedziczenie i `override`

```tsx
class Animal {
  constructor(public name: string) {}

  speak(): string {
    return `${this.name} wydaje dźwięk.`;
  }
}

class Dog extends Animal {
  constructor(name: string, public breed: string) {
    super(name); // ⚠️ super() musi być pierwsze – tak jak w Javie
  }

  override speak(): string { // override – jawne od TS 4.3
    return `${this.name} szczeka: Hau hau!`;
  }
}

const dog = new Dog('Rex', 'Labrador');
dog instanceof Dog;    // true
dog instanceof Animal; // true (łańcuch prototypów)
```

---

## 5.2 Modyfikatory dostępu

### Przegląd modyfikatorów

| Modyfikator | W klasie | W podklasie | Z zewnątrz | Runtime? |
| --- | --- | --- | --- | --- |
| `public` | ✅ | ✅ | ✅ | — |
| `protected` | ✅ | ✅ | ❌ | ❌ (tylko TS) |
| `private` | ✅ | ❌ | ❌ | ❌ (tylko TS) |
| `#field` (JS) | ✅ | ❌ | ❌ | ✅ (runtime) |

> **💡 Dla programisty Java:** TypeScript `private` to tylko adnotacja – znika po kompilacji do JS. W runtime pole jest dostępne! Dla prawdziwej enkapsulacji w runtime użyj JavaScript native `#field`.
> 

```tsx
class BankAccount {
  // TS private – ochrona tylko w czasie kompilacji:
  private balance: number = 0;

  // JS native private – ochrona w runtime:
  #pin: string;

  constructor(initialBalance: number, pin: string) {
    this.balance = initialBalance;
    this.#pin = pin;
  }

  deposit(amount: number): void {
    this.balance += amount;
  }
}

const acc = new BankAccount(1000, '1234');
// acc.balance;      // ❌ błąd TS – ale w runtime: (acc as any).balance działa!
// acc.#pin;         // ❌ błąd i runtime SyntaxError – prawdziwa enkapsulacja
```

### `protected` – podobnie jak w Javie

```tsx
class Shape {
  protected color: string;

  constructor(color: string) {
    this.color = color;
  }

  protected describe(): string {
    return `Kształt koloru${this.color}`;
  }
}

class Circle extends Shape {
  constructor(color: string, public radius: number) {
    super(color);
  }

  toString(): string {
    return `${this.describe()}, promień:${this.radius}`; // ✅ protected dostępne
  }
}

// circle.color; // ❌ protected – niedostępne z zewnątrz
```

---

## 5.3 Klasy abstrakcyjne

```tsx
abstract class BasePage {
  constructor(protected page: Page) {}

  // Metoda abstrakcyjna – musi być zaimplementowana w podklasie:
  abstract get url(): string;
  abstract isLoaded(): Promise<boolean>;

  // Metoda konkretna – dziedziczona:
  async navigate(): Promise<void> {
    await this.page.goto(this.url);
    await this.isLoaded();
  }

  async waitForElement(selector: string): Promise<void> {
    await this.page.waitForSelector(selector);
  }
}

// ❌ Nie można instancjonować abstrakcyjnej klasy:
// new BasePage(page); // Error

class LoginPage extends BasePage {
  get url(): string { return '/login'; }

  async isLoaded(): Promise<boolean> {
    await this.page.waitForSelector('#email');
    return true;
  }

  async login(email: string, password: string): Promise<void> {
    await this.page.fill('#email', email);
    await this.page.fill('#password', password);
    await this.page.click('button[type="submit"]');
  }
}
```

### Abstract vs Interface – kiedy co

|  | Klasa abstrakcyjna | Interface |
| --- | --- | --- |
| Może zawierać implementację | ✅ | ❌ |
| Dziedziczyć można tylko jedną | ✅ (jedna) | — (wiele) |
| Istnieje w runtime | ✅ | ❌ (znika po kompilacji) |
| Stan (pola) | ✅ | ❌ |

> **💡 Dla programisty Java:** W TS interfejsy to **tylko typy** – znikają po kompilacji do JS. Nie ma `instanceof` na interfejsie! Klasy abstrakcyjne istnieją w runtime.
> 

```tsx
interface Clickable {
  click(): Promise<void>;
}

class Button implements Clickable {
  async click(): Promise<void> { /* ... */ }
}

const btn = new Button();
btn instanceof Button;    // ✅ klasa istnieje w runtime
// btn instanceof Clickable; // ❌ błąd! interfejs nie istnieje w runtime
```

---

## 5.4 Implementacja interfejsów

```tsx
interface Navigable {
  navigate(url: string): Promise<void>;
}

interface Screenshottable {
  takeScreenshot(path: string): Promise<void>;
}

class PageWrapper implements Navigable, Screenshottable {
  constructor(private page: Page) {}

  async navigate(url: string): Promise<void> {
    await this.page.goto(url);
  }

  async takeScreenshot(path: string): Promise<void> {
    await this.page.screenshot({ path });
  }
}
```

### Page Object Model – wzorzec w Playwright

```tsx
// Bazowa klasa dla wszystkich page objects:
abstract class BasePageObject {
  constructor(protected readonly page: Page) {}

  protected async waitForUrl(pattern: string | RegExp): Promise<void> {
    await this.page.waitForURL(pattern);
  }

  protected async fill(selector: string, value: string): Promise<void> {
    await this.page.fill(selector, value);
  }

  protected async click(selector: string): Promise<void> {
    await this.page.click(selector);
  }
}

class LoginPage extends BasePageObject {
  // Selektory jako pola – łatwe do utrzymania:
  private readonly emailInput = '#email';
  private readonly passwordInput = '#password';
  private readonly submitButton = 'button[type="submit"]';
  private readonly errorMessage = '.error-message';

  async login(email: string, password: string): Promise<DashboardPage> {
    await this.fill(this.emailInput, email);
    await this.fill(this.passwordInput, password);
    await this.click(this.submitButton);
    await this.waitForUrl('/dashboard');
    return new DashboardPage(this.page); // Page Object Chain
  }

  async getError(): Promise<string | null> {
    return this.page.textContent(this.errorMessage);
  }
}

class DashboardPage extends BasePageObject {
  async getUserName(): Promise<string | null> {
    return this.page.textContent('.user-name');
  }
}

// Użycie w teście:
test('poprawne logowanie', async ({ page }) => {
  const loginPage = new LoginPage(page);
  await loginPage.navigate('/login');
  const dashboard = await loginPage.login('user@example.com', 'password');
  const name = await dashboard.getUserName();
  expect(name).toBe('Jan Kowalski');
});
```

---

## Podsumowanie – Cheat Sheet

| Co chcę zapisać | Składnia |
| --- | --- |
| Klasa z polami | `class C { name: string; constructor(name: string) { this.name = name; } }` |
| Parameter properties | `constructor(public name: string, private age: number) {}` |
| Getter / setter | `get value(): number` / `set value(v: number)` |
| Metoda statyczna | `static create(): C { return new C(); }` |
| Dziedziczenie | `class Dog extends Animal { constructor() { super(); } }` |
| Override | `override speak(): string { ... }` |
| TS private (tylko kompilacja) | `private secret: string` |
| JS private (runtime) | `#secret: string` |
| Klasa abstrakcyjna | `abstract class Base { abstract doIt(): void; }` |
| Implementacja interfejsu | `class C implements A, B { ... }` |
| Arrow field (this bezpieczne) | `handleClick = () => this.doSomething()` |
| Prototyp – sprawdzenie | `Object.getPrototypeOf(obj)` |
| instanceof | Sprawdza cały łańcuch prototypów |
| Interface w runtime | ❌ Nie istnieje – tylko TS |

---

*Notatki dotyczą TypeScript 5.x · Kontekst: Playwright / testowanie*
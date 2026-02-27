# Moduły

# TypeScript – Rozdział 9: Moduły i przestrzenie nazw

> **Zakres:** Import i export · Namespace · Deklaracje typów (`.d.ts`)
> 

---

## 9.1 Import i export

### Podstawy modułów

TypeScript używa systemu modułów ES6 (ESModules). Plik jest modułem, jeśli zawiera co najmniej jeden `import` lub `export` – inaczej jego zawartość trafia do zakresu globalnego.

```tsx
// ─── math.ts ───────────────────────────────────────────
// Named exports – można eksportować wiele rzeczy:
export const PI = 3.14159;

export function add(a: number, b: number): number {
  return a + b;
}

export function subtract(a: number, b: number): number {
  return a - b;
}

export interface MathResult {
  value: number;
  operation: string;
}

// ─── main.ts ───────────────────────────────────────────
import { PI, add, MathResult } from './math';
import { add as mathAdd } from './math'; // alias

console.log(PI);          // 3.14159
console.log(add(2, 3));   // 5
```

### Default export

Każdy moduł może mieć **jeden** default export. Importujący sam nadaje mu nazwę.

```tsx
// ─── user.ts ───────────────────────────────────────────
export interface User {
  id: number;
  name: string;
}

// Default export – klasa, funkcja lub wartość:
export default class UserService {
  private users: User[] = [];

  getAll(): User[] { return this.users; }
  findById(id: number): User | undefined {
    return this.users.find(u => u.id === id);
  }
}

// ─── main.ts ───────────────────────────────────────────
import UserService, { User } from './user'; // default + named razem

const service = new UserService();
```

> **💡 Wskazówka:** Wiele projektów i bibliotek unika default exportów – named exporty są łatwiejsze do refaktoryzacji i wyszukiwania w IDE.
> 

### Export zebrany (re-export)

Tworzenie pliku `index.ts`, który zbiera i re-eksportuje wszystko z modułu – klasyczny wzorzec Barrel.

```tsx
// ─── services/user.service.ts ──
export class UserService { /* ... */ }
export interface UserDto { /* ... */ }

// ─── services/auth.service.ts ──
export class AuthService { /* ... */ }
export type AuthToken = string;

// ─── services/index.ts (barrel) ──
export { UserService, UserDto } from './user.service';
export { AuthService, AuthToken } from './auth.service';

// Re-export wszystkiego:
export * from './user.service';
export * from './auth.service';

// Re-export z aliasem:
export { UserService as default } from './user.service';

// ─── main.ts ──────────────────
import { UserService, AuthService } from './services'; // czysty import
```

### Import typów (`import type`)

Od TypeScript 3.8 można importować **wyłącznie typy**. Są usuwane całkowicie przy kompilacji – nie trafiają do JS.

```tsx
// import type – gwarancja, że nic runtime-owego nie zostanie zaimportowane:
import type { User, UserDto } from './user.service';
import type { Config } from './config';

// Można mieszać:
import { UserService } from './user.service';        // wartość runtime
import type { UserDto } from './user.service';        // tylko typ

// Inline type import (TS 4.5+):
import { UserService, type UserDto } from './user.service';

// Kiedy używać import type:
// 1. Typy używane tylko w adnotacjach (nie w runtime)
// 2. Unikanie circular dependencies
// 3. Optymalizacja bundlera (eliminacja martwego kodu)
```

### Dynamiczne importy

```tsx
// Leniwy ładowanie modułu (code splitting):
async function loadHeavyModule() {
  const { HeavyClass } = await import('./heavy-module');
  return new HeavyClass();
}

// Z obsługą błędów:
async function loadPlugin(name: string) {
  try {
    const plugin = await import(`./plugins/${name}`);
    return plugin.default;
  } catch {
    console.error(`Nie można załadować pluginu:${name}`);
    return null;
  }
}

// Typ dynamicznego importu:
type HeavyModule = typeof import('./heavy-module');
type HeavyClass  = import('./heavy-module').HeavyClass;
```

### Aliasy ścieżek (path aliases)

Konfiguracja w `tsconfig.json` eliminuje długie ścieżki względne.

```json
// tsconfig.json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*":          ["src/*"],
      "@services/*":  ["src/services/*"],
      "@models/*":    ["src/models/*"],
      "@utils/*":     ["src/utils/*"]
    }
  }
}
```

```tsx
// Bez aliasów – brzydko:
import { UserService } from '../../../services/user.service';

// Z aliasami – czytelnie:
import { UserService } from '@services/user.service';
import { User } from '@models/user';
import { formatDate } from '@utils/date';
```

> **⚠️ Uwaga:** Path aliases z `tsconfig.json` działają tylko dla TypeScript. Bundler (Webpack, Vite, esbuild) potrzebuje osobnej konfiguracji aliasów.
> 

---

## 9.2 Namespace

### Czym jest namespace?

`namespace` to sposób na grupowanie powiązanych typów, interfejsów i klas pod wspólną nazwą – unikanie kolizji w globalnej przestrzeni nazw. Współcześnie w kodzie modularnym (`import`/`export`) są rzadko potrzebne – ich główne zastosowanie to pisanie deklaracji typów (`.d.ts`).

```tsx
// Podstawowa definicja namespace:
namespace Validation {
  export interface Validator<T> {
    validate(value: T): boolean;
    getErrors(): string[];
  }

  export class StringValidator implements Validator<string> {
    private errors: string[] = [];

    validate(value: string): boolean {
      this.errors = [];
      if (value.length === 0) this.errors.push('Wartość nie może być pusta');
      if (value.length > 100) this.errors.push('Wartość za długa');
      return this.errors.length === 0;
    }

    getErrors(): string[] {
      return [...this.errors];
    }
  }

  export class EmailValidator implements Validator<string> {
    private errors: string[] = [];

    validate(value: string): boolean {
      this.errors = [];
      const re = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
      if (!re.test(value)) this.errors.push('Niepoprawny format email');
      return this.errors.length === 0;
    }

    getErrors(): string[] {
      return [...this.errors];
    }
  }
}

// Użycie:
const v = new Validation.StringValidator();
v.validate('hello'); // true

const ev = new Validation.EmailValidator();
ev.validate('jan@example.com'); // true
```

### Zagnieżdżone namespace

```tsx
namespace App {
  export namespace Models {
    export interface User {
      id: number;
      name: string;
    }

    export interface Product {
      id: number;
      price: number;
    }
  }

  export namespace Services {
    export interface UserService {
      getUser(id: number): Models.User;
    }
  }

  export namespace Utils {
    export function formatCurrency(amount: number): string {
      return new Intl.NumberFormat('pl-PL', {
        style: 'currency',
        currency: 'PLN',
      }).format(amount);
    }
  }
}

// Użycie:
const user: App.Models.User = { id: 1, name: 'Jan' };
App.Utils.formatCurrency(1234.56); // '1 234,56 zł'
```

### Aliasy namespace

```tsx
import Models = App.Models;
import Utils = App.Utils;

// Teraz krócej:
const u: Models.User = { id: 1, name: 'Jan' };
Utils.formatCurrency(100);
```

### Rozszerzanie namespace (`/// <reference>`)

```tsx
// ─── validators.ts ──────────────
namespace Validation {
  export class NumberValidator {
    validate(value: number): boolean {
      return !isNaN(value) && isFinite(value);
    }
  }
}

// ─── main.ts ────────────────────
///<reference path="validators.ts"/>

// Namespace jest scalany automatycznie:
const nv = new Validation.NumberValidator();
```

> **💡 Namespace vs moduły:** W nowoczesnym TypeScript preferuj moduły ES6 (`import`/`export`). Namespace używaj głównie w plikach deklaracji `.d.ts` do opisywania globalnych bibliotek JS.
> 

---

## 9.3 Deklaracje typów (`.d.ts`)

### Czym są pliki `.d.ts`?

Pliki deklaracji (Declaration Files) zawierają **wyłącznie informacje o typach** – bez żadnej logiki runtime. Informują TypeScript o kształcie istniejącego kodu JavaScript (bibliotek zewnętrznych, plików bez typów).

```tsx
// Plik .d.ts zawiera tylko deklaracje – brak implementacji:
declare const VERSION: string;
declare function greet(name: string): void;
declare class EventBus {
  on(event: string, handler: () => void): void;
  emit(event: string): void;
}
```

### Typy dla bibliotek JS (`@types/*`)

Większość popularnych bibliotek JS ma gotowe typy w organizacji `@types` na npm (projekt DefinitelyTyped).

```bash
# Instalacja typów dla popularnych bibliotek:
npm install --save-dev @types/node
npm install --save-dev @types/lodash
npm install --save-dev @types/express
npm install --save-dev @types/react @types/react-dom
```

```tsx
// Po zainstalowaniu @types/lodash:
import _ from 'lodash';
_.chunk([1, 2, 3, 4], 2); // TypeScript wie, że zwraca number[][]

// Po zainstalowaniu @types/node:
import { readFileSync } from 'fs';
import { join } from 'path';
```

### Tworzenie własnych plików `.d.ts`

### Dla lokalnego pliku JS

```tsx
// ─── legacy.js (istniejący JS bez typów) ──────────────
function calculateTax(income, rate) {
  return income * rate;
}

module.exports = { calculateTax };

// ─── legacy.d.ts (deklaracja typów) ────────────────────
export function calculateTax(income: number, rate: number): number;
```

### Dla globalnej biblioteki (nie modułu)

```tsx
// ─── globals.d.ts ──────────────────────────────────────
// Deklaracja globalnej zmiennej (np. ładowanej przez <script>):
declare const __APP_VERSION__: string;
declare const __DEV__: boolean;

// Deklaracja globalnej funkcji:
declare function trackEvent(name: string, data?: object): void;

// Rozszerzenie globalnego Window:
interface Window {
  myAnalytics: {
    track(event: string): void;
    identify(userId: string): void;
  };
  __store: unknown;
}

// Deklaracja modułu CSS (Webpack CSS Modules):
declare module '*.css' {
  const styles: Record<string, string>;
  export default styles;
}

// Deklaracja modułu SVG:
declare module '*.svg' {
  const url: string;
  export default url;
}

// Deklaracja modułu JSON:
declare module '*.json' {
  const value: unknown;
  export default value;
}
```

### Dla biblioteki npm bez typów

```tsx
// ─── @types/some-library/index.d.ts ────────────────────
// lub w projekcie: types/some-library.d.ts

declare module 'some-library' {
  export interface Options {
    timeout?: number;
    retries?: number;
  }

  export function initialize(options?: Options): void;
  export function destroy(): void;

  export class Client {
    constructor(apiKey: string);
    get(url: string): Promise<unknown>;
    post(url: string, data: object): Promise<unknown>;
  }

  export default Client;
}
```

### Ambient declarations

```tsx
// declare – słowo kluczowe mówiące TS "ta wartość istnieje w runtime,
// ale nie definiuję jej tutaj":

// Zmienne środowiskowe (Node.js):
declare namespace NodeJS {
  interface ProcessEnv {
    NODE_ENV: 'development' | 'production' | 'test';
    DATABASE_URL: string;
    API_KEY: string;
    PORT?: string;
  }
}

// Teraz process.env jest typowane:
const env = process.env.NODE_ENV;  // 'development' | 'production' | 'test'
const port = process.env.PORT;     // string | undefined

// Deklaracja przestrzeni nazw modułu:
declare module 'express' {
  interface Request {
    userId?: number;    // rozszerzenie Request o własne pole
    user?: User;
  }
}
```

### Generowanie `.d.ts` automatycznie

TypeScript może sam generować pliki deklaracji przy kompilacji biblioteki.

```json
// tsconfig.json
{
  "compilerOptions": {
    "declaration": true,          // generuj .d.ts
    "declarationDir": "./types",  // katalog docelowy
    "declarationMap": true,       // source maps dla .d.ts
    "emitDeclarationOnly": false  // czy tylko .d.ts (bez .js)
  }
}
```

```tsx
// Wejście: src/math.ts
export function add(a: number, b: number): number {
  return a + b;
}

export type MathFn = (a: number, b: number) => number;

// Wyjście: types/math.d.ts (auto-generowane)
// export declare function add(a: number, b: number): number;
// export declare type MathFn = (a: number, b: number) => number;
```

### `tsconfig.json` – opcje związane z modułami

```json
{
  "compilerOptions": {
    // System modułów docelowy:
    "module": "ESNext",          // ESModules (zalecane dla nowych projektów)
    "module": "CommonJS",        // Node.js (require/module.exports)
    "module": "NodeNext",        // Node.js ESM z .mjs/.cjs

    // Rozwiązywanie modułów:
    "moduleResolution": "bundler",  // Vite, esbuild (zalecane)
    "moduleResolution": "NodeNext", // Node.js 12+
    "moduleResolution": "node",     // klasyczny Node.js

    // Ścieżki:
    "baseUrl": ".",
    "paths": { "@/*": ["src/*"] },

    // Deklaracje:
    "declaration": true,
    "declarationMap": true,

    // Import/export:
    "esModuleInterop": true,       // kompatybilność CommonJS/ESM
    "allowSyntheticDefaultImports": true,

    // Dodatkowe typy globalne:
    "types": ["node", "jest"],     // tylko wymienione @types/*
    "typeRoots": ["./node_modules/@types", "./types"]
  }
}
```

---

## Podsumowanie – Cheat Sheet

| Co chcę zapisać | Składnia |
| --- | --- |
| Named export | `export const x = 1` / `export function f()` |
| Default export | `export default class MyClass {}` |
| Named import | `import { x, f } from './module'` |
| Default import | `import MyClass from './module'` |
| Import z aliasem | `import { x as myX } from './module'` |
| Import wszystkiego | `import * as mod from './module'` |
| Import tylko typów | `import type { User } from './user'` |
| Inline import type | `import { type User, UserService } from './user'` |
| Re-export | `export { X } from './x'` / `export * from './x'` |
| Dynamiczny import | `const mod = await import('./module')` |
| Typ dynamicznego importu | `type T = import('./module').MyType` |
| Namespace | `namespace Foo { export interface Bar {} }` |
| Użycie namespace | `const x: Foo.Bar = {}` |
| Alias namespace | `import Bar = Foo.Bar` |
| Declare global | `declare const __DEV__: boolean` |
| Deklaracja modułu JS | `declare module 'lib' { export function f(): void }` |
| Deklaracja pliku | `declare module '*.svg' { const url: string; export default url }` |
| Rozszerzenie Request | `declare module 'express' { interface Request { userId?: number } }` |
| Zmienne środowiskowe | `declare namespace NodeJS { interface ProcessEnv { PORT?: string } }` |

---

*Notatki dotyczą TypeScript 5.x*
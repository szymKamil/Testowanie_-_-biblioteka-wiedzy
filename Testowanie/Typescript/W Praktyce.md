# W Praktyce

# TypeScript – Rozdział 10: TypeScript w praktyce

> **Zakres:** TypeScript z React · TypeScript z Node.js / Express · Konfiguracja `tsconfig.json` · Debugowanie i narzędzia (ESLint, Prettier)
> 

---

## 10.1 TypeScript z React

### Konfiguracja projektu

```bash
# Nowy projekt z Vite (zalecane):
npm create vite@latest my-app -- --template react-ts

# Nowy projekt z Create React App (legacy):
npx create-react-app my-app --template typescript

# Dodanie TS do istniejącego projektu:
npm install --save-dev typescript @types/react @types/react-dom
```

```json
// tsconfig.json dla React + Vite
{
  "compilerOptions": {
    "target": "ES2020",
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "module": "ESNext",
    "moduleResolution": "bundler",
    "jsx": "react-jsx",          // React 17+ (bez importu React)
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true
  }
}
```

### Typowanie komponentów funkcyjnych

```tsx
import { FC, ReactNode, MouseEvent } from 'react';

// FC<Props> – Function Component (zawiera children niejawnie w starszych wersjach)
// Nowszy, preferowany zapis – bez FC, zwracamy ReactNode lub JSX.Element:

interface ButtonProps {
  label: string;
  onClick: (e: MouseEvent<HTMLButtonElement>) => void;
  disabled?: boolean;
  variant?: 'primary' | 'secondary' | 'danger';
  children?: ReactNode;
}

// Preferowany zapis – explicit props:
function Button({ label, onClick, disabled = false, variant = 'primary', children }: ButtonProps) {
  return (
    <button
      onClick={onClick}
      disabled={disabled}
      className={`btn btn-${variant}`}
    >
      {children ?? label}
    </button>
  );
}

// Alternatywnie z FC:
const Button: FC<ButtonProps> = ({ label, onClick, disabled = false, variant = 'primary' }) => (
  <button onClick={onClick} disabled={disabled}>
    {label}
  </button>
);
```

### Typowanie `useState`

```tsx
import { useState } from 'react';

interface User {
  id: number;
  name: string;
  email: string;
}

function UserProfile() {
  // TypeScript inferuje typ z wartości początkowej:
  const [count, setCount] = useState(0);            // number
  const [name, setName] = useState('');             // string
  const [active, setActive] = useState(false);      // boolean

  // Gdy wartość początkowa to null/undefined – podaj generyk:
  const [user, setUser] = useState<User | null>(null);
  const [users, setUsers] = useState<User[]>([]);
  const [error, setError] = useState<string | null>(null);

  // Użycie:
  setUser({ id: 1, name: 'Jan', email: 'jan@example.com' }); // ✅
  // setUser('string'); // ❌

  return <div>{user?.name ?? 'Brak użytkownika'}</div>;
}
```

### Typowanie `useRef`

```tsx
import { useRef, useEffect } from 'react';

function VideoPlayer() {
  // Ref do elementu DOM – null na początku:
  const videoRef = useRef<HTMLVideoElement>(null);
  const inputRef = useRef<HTMLInputElement>(null);

  // Ref do wartości mutowalnej (nie DOM):
  const timerRef = useRef<ReturnType<typeof setInterval> | null>(null);
  const countRef = useRef<number>(0);

  useEffect(() => {
    if (videoRef.current) {
      videoRef.current.play(); // videoRef.current: HTMLVideoElement
    }

    timerRef.current = setInterval(() => {
      countRef.current++;
    }, 1000);

    return () => {
      if (timerRef.current) clearInterval(timerRef.current);
    };
  }, []);

  return <video ref={videoRef} />;
}
```

### Typowanie `useReducer`

```tsx
import { useReducer } from 'react';

interface State {
  count: number;
  loading: boolean;
  error: string | null;
}

type Action =
  | { type: 'INCREMENT' }
  | { type: 'DECREMENT' }
  | { type: 'RESET' }
  | { type: 'SET_LOADING'; payload: boolean }
  | { type: 'SET_ERROR'; payload: string | null };

const initialState: State = {
  count: 0,
  loading: false,
  error: null,
};

function reducer(state: State, action: Action): State {
  switch (action.type) {
    case 'INCREMENT': return { ...state, count: state.count + 1 };
    case 'DECREMENT': return { ...state, count: state.count - 1 };
    case 'RESET':     return initialState;
    case 'SET_LOADING': return { ...state, loading: action.payload };
    case 'SET_ERROR':   return { ...state, error: action.payload };
    // TypeScript wymusi obsługę wszystkich akcji
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <div>
      <p>{state.count}</p>
      <button onClick={() => dispatch({ type: 'INCREMENT' })}>+</button>
      <button onClick={() => dispatch({ type: 'DECREMENT' })}>-</button>
    </div>
  );
}
```

### Typowanie kontekstu (`useContext`)

```tsx
import { createContext, useContext, useState, ReactNode } from 'react';

interface AuthContextValue {
  user: User | null;
  login: (email: string, password: string) => Promise<void>;
  logout: () => void;
  isLoading: boolean;
}

// Null jako wartość domyślna + sprawdzenie przy użyciu:
const AuthContext = createContext<AuthContextValue | null>(null);

function AuthProvider({ children }: { children: ReactNode }) {
  const [user, setUser] = useState<User | null>(null);
  const [isLoading, setIsLoading] = useState(false);

  const login = async (email: string, password: string) => {
    setIsLoading(true);
    try {
      const user = await authService.login(email, password);
      setUser(user);
    } finally {
      setIsLoading(false);
    }
  };

  const logout = () => setUser(null);

  return (
    <AuthContext.Provider value={{ user, login, logout, isLoading }}>
      {children}
    </AuthContext.Provider>
  );
}

// Hook z walidacją kontekstu:
function useAuth(): AuthContextValue {
  const ctx = useContext(AuthContext);
  if (!ctx) throw new Error('useAuth musi być używany wewnątrz AuthProvider');
  return ctx;
}
```

### Typowanie custom hooks

```tsx
import { useState, useCallback } from 'react';

// Hook zwracający stan + metody:
function useCounter(initial = 0, step = 1) {
  const [count, setCount] = useState(initial);

  const increment = useCallback(() => setCount(c => c + step), [step]);
  const decrement = useCallback(() => setCount(c => c - step), [step]);
  const reset     = useCallback(() => setCount(initial), [initial]);

  return { count, increment, decrement, reset } as const;
  // as const → TypeScript nie rozszerzy typów metod do Function
}

// Typ zwracany hooka:
type UseCounterReturn = ReturnType<typeof useCounter>;

// Generyczny hook do fetchowania danych:
interface FetchState<T> {
  data: T | null;
  loading: boolean;
  error: Error | null;
}

function useFetch<T>(url: string): FetchState<T> {
  const [state, setState] = useState<FetchState<T>>({
    data: null,
    loading: true,
    error: null,
  });

  // ... fetch logic

  return state;
}

// Użycie z inferowaniem:
const { data, loading } = useFetch<User[]>('/api/users');
// data: User[] | null ✅
```

### Typowanie zdarzeń

```tsx
import { ChangeEvent, FormEvent, KeyboardEvent, DragEvent } from 'react';

function Form() {
  const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
    console.log(e.target.value);    // string
    console.log(e.target.checked);  // boolean (dla checkbox)
  };

  const handleSelect = (e: ChangeEvent<HTMLSelectElement>) => {
    console.log(e.target.value);
  };

  const handleTextarea = (e: ChangeEvent<HTMLTextAreaElement>) => {
    console.log(e.target.value);
  };

  const handleSubmit = (e: FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    // logika formularza
  };

  const handleKeyDown = (e: KeyboardEvent<HTMLInputElement>) => {
    if (e.key === 'Enter') {
      // obsługa Enter
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <input onChange={handleChange} onKeyDown={handleKeyDown} />
      <select onChange={handleSelect}><option>A</option></select>
      <textarea onChange={handleTextarea} />
      <button type="submit">Wyślij</button>
    </form>
  );
}
```

### Props z generycznym komponentem

```tsx
// Generyczny komponent listy:
interface ListProps<T> {
  items: T[];
  renderItem: (item: T, index: number) => ReactNode;
  keyExtractor: (item: T) => string | number;
  emptyMessage?: string;
}

function List<T>({ items, renderItem, keyExtractor, emptyMessage = 'Brak danych' }: ListProps<T>) {
  if (items.length === 0) return <p>{emptyMessage}</p>;

  return (
    <ul>
      {items.map((item, i) => (
        <li key={keyExtractor(item)}>{renderItem(item, i)}</li>
      ))}
    </ul>
  );
}

// Użycie:
<List
  items={users}
  keyExtractor={u => u.id}
  renderItem={u => <span>{u.name}</span>}
/>
```

---

## 10.2 TypeScript z Node.js / Express

### Konfiguracja projektu

```bash
npm init -y
npm install express
npm install --save-dev typescript @types/node @types/express ts-node nodemon
npx tsc --init
```

```json
// tsconfig.json dla Node.js
{
  "compilerOptions": {
    "target": "ES2020",
    "lib": ["ES2020"],
    "module": "CommonJS",
    "moduleResolution": "node",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "resolveJsonModule": true,
    "declaration": true,
    "sourceMap": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

```json
// package.json scripts
{
  "scripts": {
    "dev":   "nodemon --exec ts-node src/index.ts",
    "build": "tsc",
    "start": "node dist/index.js"
  }
}
```

### Typowany serwer Express

```tsx
// src/types/index.ts
export interface User {
  id: number;
  name: string;
  email: string;
  role: 'admin' | 'user';
}

export interface CreateUserBody {
  name: string;
  email: string;
  role?: 'admin' | 'user';
}

export interface UpdateUserBody {
  name?: string;
  email?: string;
  role?: 'admin' | 'user';
}

// Generyczne typy dla Express:
export interface TypedRequest<
  TBody = unknown,
  TParams = Record<string, string>,
  TQuery  = Record<string, string>
> extends Express.Request {
  body: TBody;
  params: TParams;
  query: TQuery;
}

export interface TypedResponse<T> extends Express.Response {
  json: (body: ApiResponse<T>) => this;
}

export interface ApiResponse<T> {
  data?: T;
  error?: string;
  message?: string;
}
```

```tsx
// src/routes/users.ts
import { Router, Request, Response } from 'express';
import type { User, CreateUserBody, UpdateUserBody } from '../types';

const router = Router();
const users: User[] = [];

// GET /users
router.get('/', (req: Request, res: Response<User[]>) => {
  res.json(users);
});

// GET /users/:id
router.get('/:id', (req: Request<{ id: string }>, res: Response) => {
  const id = parseInt(req.params.id);
  const user = users.find(u => u.id === id);

  if (!user) {
    return res.status(404).json({ error: 'Nie znaleziono użytkownika' });
  }

  res.json(user);
});

// POST /users
router.post(
  '/',
  (req: Request<{}, {}, CreateUserBody>, res: Response) => {
    const { name, email, role = 'user' } = req.body;

    const user: User = {
      id: users.length + 1,
      name,
      email,
      role,
    };

    users.push(user);
    res.status(201).json(user);
  }
);

// PATCH /users/:id
router.patch(
  '/:id',
  (req: Request<{ id: string }, {}, UpdateUserBody>, res: Response) => {
    const id = parseInt(req.params.id);
    const idx = users.findIndex(u => u.id === id);

    if (idx === -1) {
      return res.status(404).json({ error: 'Nie znaleziono użytkownika' });
    }

    users[idx] = { ...users[idx], ...req.body };
    res.json(users[idx]);
  }
);

export default router;
```

### Middleware z typowaniem

```tsx
// src/middleware/auth.ts
import { Request, Response, NextFunction } from 'express';
import type { User } from '../types';

// Rozszerzenie Request o własne pola:
declare global {
  namespace Express {
    interface Request {
      user?: User;
      requestId?: string;
    }
  }
}

export function authMiddleware(
  req: Request,
  res: Response,
  next: NextFunction
): void {
  const token = req.headers.authorization?.replace('Bearer ', '');

  if (!token) {
    res.status(401).json({ error: 'Brak tokenu' });
    return;
  }

  try {
    const user = verifyToken(token); // zwraca User
    req.user = user;                 // teraz typowane ✅
    next();
  } catch {
    res.status(401).json({ error: 'Nieprawidłowy token' });
  }
}

// Middleware loggujący z ID żądania:
export function requestLogger(
  req: Request,
  res: Response,
  next: NextFunction
): void {
  req.requestId = Math.random().toString(36).slice(2);
  console.log(`[${req.requestId}]${req.method}${req.path}`);
  next();
}
```

### Obsługa błędów

```tsx
// src/errors.ts
export class AppError extends Error {
  constructor(
    public message: string,
    public statusCode: number = 500,
    public code?: string
  ) {
    super(message);
    this.name = 'AppError';
    Object.setPrototypeOf(this, AppError.prototype);
  }
}

export class NotFoundError extends AppError {
  constructor(resource: string) {
    super(`${resource} nie zostało znalezione`, 404, 'NOT_FOUND');
  }
}

export class ValidationError extends AppError {
  constructor(message: string) {
    super(message, 400, 'VALIDATION_ERROR');
  }
}

// src/middleware/errorHandler.ts
import { Request, Response, NextFunction } from 'express';
import { AppError } from '../errors';

export function errorHandler(
  err: Error,
  req: Request,
  res: Response,
  next: NextFunction  // wymagane przez Express, nawet jeśli nieużywane
): void {
  if (err instanceof AppError) {
    res.status(err.statusCode).json({
      error: err.message,
      code: err.code,
    });
    return;
  }

  console.error('Nieobsłużony błąd:', err);
  res.status(500).json({ error: 'Wewnętrzny błąd serwera' });
}
```

---

## 10.3 Konfiguracja `tsconfig.json`

### Kluczowe opcje kompilatora

```json
{
  "compilerOptions": {

    // ─── STRICTNESS ───────────────────────────────────────
    "strict": true,
    // Włącza grupę opcji jednocześnie:
    // - strictNullChecks: null/undefined ≠ inne typy
    // - strictFunctionTypes: dokładna zgodność typów funkcji
    // - strictBindCallApply: poprawne typy bind/call/apply
    // - strictPropertyInitialization: pola klasy muszą być zainicjowane
    // - noImplicitAny: brak niejawnego any
    // - noImplicitThis: this nie może być any
    // - alwaysStrict: "use strict" w każdym pliku

    "noImplicitAny": true,          // błąd gdy TypeScript inferuje any
    "strictNullChecks": true,       // null/undefined wymagają jawnej obsługi
    "noUncheckedIndexedAccess": true, // dostęp przez indeks zwraca T | undefined
    "noImplicitReturns": true,      // każda ścieżka musi zwracać wartość
    "noFallthroughCasesInSwitch": true, // brak fallthrough w switch
    "noUnusedLocals": true,         // błąd dla nieużywanych zmiennych
    "noUnusedParameters": true,     // błąd dla nieużywanych parametrów
    "exactOptionalPropertyTypes": true, // rozróżnia brak pola od undefined

    // ─── WYJŚCIE ──────────────────────────────────────────
    "target": "ES2020",             // wersja JS wyjściowego
    "lib": ["ES2020", "DOM"],       // dostępne typy wbudowane
    "outDir": "./dist",             // katalog wyjściowy
    "rootDir": "./src",             // katalog źródłowy
    "sourceMap": true,              // mapy źródłowe do debugowania
    "declaration": true,            // generuj .d.ts
    "declarationMap": true,         // mapy dla .d.ts

    // ─── MODUŁY ───────────────────────────────────────────
    "module": "ESNext",             // system modułów
    "moduleResolution": "bundler",  // strategia rozwiązywania modułów
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"],
      "@components/*": ["src/components/*"]
    },
    "esModuleInterop": true,        // kompatybilność CJS/ESM
    "allowSyntheticDefaultImports": true,
    "resolveJsonModule": true,      // importowanie plików JSON

    // ─── JSX ──────────────────────────────────────────────
    "jsx": "react-jsx",             // React 17+ bez importu React
    "jsx": "preserve",              // zachowaj JSX (dla Babel/esbuild)

    // ─── TYPY ─────────────────────────────────────────────
    "typeRoots": ["./node_modules/@types", "./types"],
    "types": ["node", "jest"],      // tylko wymienione @types
    "skipLibCheck": true            // pomijaj sprawdzanie .d.ts bibliotek
  }
}
```

### Profile `tsconfig.json` dla różnych środowisk

```json
// tsconfig.base.json – wspólne ustawienia
{
  "compilerOptions": {
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  }
}

// tsconfig.json – deweloperska
{
  "extends": "./tsconfig.base.json",
  "compilerOptions": {
    "sourceMap": true,
    "noUnusedLocals": false,
    "noUnusedParameters": false
  },
  "include": ["src"]
}

// tsconfig.build.json – produkcyjna
{
  "extends": "./tsconfig.base.json",
  "compilerOptions": {
    "sourceMap": false,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "declaration": true,
    "outDir": "./dist"
  },
  "exclude": ["**/*.test.ts", "**/*.spec.ts"]
}

// tsconfig.test.json – testy
{
  "extends": "./tsconfig.base.json",
  "compilerOptions": {
    "types": ["jest", "node"],
    "noUnusedLocals": false
  },
  "include": ["src", "tests"]
}
```

### `noUncheckedIndexedAccess` – bezpieczny dostęp przez indeks

```tsx
// Bez noUncheckedIndexedAccess:
const arr: number[] = [1, 2, 3];
const first = arr[0]; // typ: number (może być undefined!)
first.toFixed(2);     // brak błędu, choć arr może być puste

// Z noUncheckedIndexedAccess: true:
const first = arr[0]; // typ: number | undefined
// first.toFixed(2);  // ❌ błąd – musisz sprawdzić
first?.toFixed(2);    // ✅

const obj: Record<string, number> = {};
const val = obj['key']; // number | undefined ✅
```

---

## 10.4 Debugowanie i narzędzia

### ESLint z TypeScript

```bash
npm install --save-dev eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin
# Dla React:
npm install --save-dev eslint-plugin-react eslint-plugin-react-hooks
```

```jsx
// .eslintrc.cjs
module.exports = {
  root: true,
  parser: '@typescript-eslint/parser',
  parserOptions: {
    project: './tsconfig.json',  // wymagane dla reguł type-aware
    ecmaVersion: 'latest',
    sourceType: 'module',
  },
  plugins: ['@typescript-eslint'],
  extends: [
    'eslint:recommended',
    'plugin:@typescript-eslint/recommended',
    'plugin:@typescript-eslint/recommended-requiring-type-checking', // zaawansowane
  ],
  rules: {
    // Zakaz any:
    '@typescript-eslint/no-explicit-any': 'error',

    // Wymagaj zwracanego typu dla eksportowanych funkcji:
    '@typescript-eslint/explicit-module-boundary-types': 'warn',

    // Zakaz nieużywanych zmiennych (z wyjątkiem _ prefix):
    '@typescript-eslint/no-unused-vars': ['error', {
      argsIgnorePattern: '^_',
      varsIgnorePattern: '^_'
    }],

    // Preferuj import type dla typów:
    '@typescript-eslint/consistent-type-imports': ['error', {
      prefer: 'type-imports'
    }],

    // Zakaz non-null assertion operator (!):
    '@typescript-eslint/no-non-null-assertion': 'warn',

    // Bezpieczne operacje na floatach:
    '@typescript-eslint/no-floating-promises': 'error',

    // Zakaz any w rzutowaniu:
    '@typescript-eslint/no-unsafe-assignment': 'error',
  },
};
```

### Prettier

```bash
npm install --save-dev prettier eslint-config-prettier
```

```json
// .prettierrc
{
  "semi": true,
  "singleQuote": true,
  "trailingComma": "all",
  "printWidth": 100,
  "tabWidth": 2,
  "arrowParens": "always",
  "endOfLine": "lf"
}
```

```json
// package.json
{
  "scripts": {
    "lint":        "eslint src --ext .ts,.tsx",
    "lint:fix":    "eslint src --ext .ts,.tsx --fix",
    "format":      "prettier --write 'src/**/*.{ts,tsx}'",
    "format:check": "prettier --check 'src/**/*.{ts,tsx}'",
    "type-check":  "tsc --noEmit"
  }
}
```

### Source Maps i debugowanie w VS Code

```json
// .vscode/launch.json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Debug Node.js",
      "type": "node",
      "request": "launch",
      "runtimeExecutable": "ts-node",
      "args": ["${workspaceFolder}/src/index.ts"],
      "sourceMaps": true,
      "cwd": "${workspaceFolder}",
      "env": { "NODE_ENV": "development" }
    },
    {
      "name": "Debug Tests (Jest)",
      "type": "node",
      "request": "launch",
      "runtimeExecutable": "${workspaceFolder}/node_modules/.bin/jest",
      "args": ["--runInBand", "--watchAll=false"],
      "sourceMaps": true
    }
  ]
}
```

### Przydatne rozszerzenia VS Code

```
// Kluczowe do TypeScript:
dbaeumer.vscode-eslint           – ESLint
esbenp.prettier-vscode           – Prettier
ms-vscode.vscode-typescript-next – najnowszy TS language server

// Pomocne:
PKief.material-icon-theme        – ikony plików
usernamehw.change-case           – zmiana wielkości liter
streetsidesoftware.code-spell-checker – sprawdzanie pisowni
christian-kohler.path-intellisense   – autouzupełnianie ścieżek
```

```json
// .vscode/settings.json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true,
    "source.organizeImports": true
  },
  "typescript.preferences.importModuleSpecifier": "relative",
  "typescript.updateImportsOnFileMove.enabled": "always",
  "typescript.inlayHints.parameterNames.enabled": "literals",
  "typescript.inlayHints.variableTypes.enabled": false
}
```

### Typowe skrypty CI/CD

```yaml
# .github/workflows/ci.yml
name: CI
on:[push, pull_request]

jobs:
check:
runs-on: ubuntu-latest
steps:
-uses: actions/checkout@v4
-uses: actions/setup-node@v4
with:
node-version:'20'
cache:'npm'

-run: npm ci

-name: Type check
run: npm run type-check

-name: Lint
run: npm run lint

-name: Format check
run: npm run format:check

-name: Test
run: npm test

-name: Build
run: npm run build
```

### Narzędzia pomocnicze

```bash
# ts-node – uruchamianie TS bezpośrednio:
npx ts-node src/script.ts

# tsx – szybsza alternatywa dla ts-node:
npx tsx src/script.ts
npx tsx watch src/index.ts  # tryb watch

# tsc --watch – automatyczna rekompilacja:
npx tsc --watch

# type-coverage – sprawdzenie pokrycia typami:
npx type-coverage --detail --strict

# depcheck – nieużywane zależności:
npx depcheck

# knip – nieużywany kod i eksporty:
npx knip
```

---

## Podsumowanie – Cheat Sheet

| Temat | Kluczowe informacje |
| --- | --- |
| React setup | `npm create vite@latest app -- --template react-ts` |
| useState z typem | `useState<User \| null>(null)` |
| useRef DOM | `useRef<HTMLInputElement>(null)` |
| Eventy | `ChangeEvent<HTMLInputElement>`, `FormEvent<HTMLFormElement>` |
| Generyczny komponent | `function List<T>({ items }: { items: T[] })` |
| Express Request typing | `Request<Params, ResBody, ReqBody, Query>` |
| Rozszerzenie Request | `declare module 'express' { interface Request { user?: User } }` |
| tsconfig strict | `"strict": true` – włącza wszystkie opcje strictness |
| Bezpieczny indeks | `"noUncheckedIndexedAccess": true` → `arr[0]` to `T \| undefined` |
| ESLint + TS | `@typescript-eslint/parser` + `@typescript-eslint/eslint-plugin` |
| Sprawdzenie typów (CI) | `tsc --noEmit` |
| Szybki runtime TS | `tsx src/index.ts` |
| Source maps | `"sourceMap": true` w tsconfig |

---

*Notatki dotyczą TypeScript 5.x, React 18+, Express 4.x*
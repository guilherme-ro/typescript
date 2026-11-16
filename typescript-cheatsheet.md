# TypeScript Cheat Sheet

> Referência rápida e completa de TypeScript (atualizado para TS 5.x / 6.x)

## Instalação e Uso Básico

```bash
# Instalação global
npm install -g typescript

# Instalação local (recomendado)
npm install -D typescript

# Inicializar tsconfig.json
npx tsc --init

# Compilar
npx tsc
npx tsc arquivo.ts

# Verificar tipos sem emitir JS
npx tsc --noEmit

# Executar diretamente (com tsx)
npx tsx src/index.ts
```

---

## Tipos Básicos (Primitive Types)

```ts
let isDone: boolean = false;
let decimal: number = 6;
let hex: number = 0xf00d;
let binary: number = 0b1010;
let octal: number = 0o744;
let big: bigint = 100n;

let color: string = "blue";
let fullName: string = `Bob ${color}`;

let list: number[] = [1, 2, 3];
let listGeneric: Array<number> = [1, 2, 3];

let tuple: [string, number] = ["hello", 10];

let u: undefined = undefined;
let n: null = null;

let notSure: any = 4;          // Evite quando possível
let safer: unknown = 4;       // Mais seguro que any

function warnUser(): void {
  console.log("This is my warning message");
}

function error(message: string): never {
  throw new Error(message);
}

let obj: object = { name: "Alice" };
```

---

## Inferência de Tipos e Anotações

```ts
// Inferência automática
let count = 0;                // number
let name = "Alice";           // string

// Anotação explícita
let age: number = 25;

// Funções
function add(a: number, b: number): number {
  return a + b;
}

const multiply = (a: number, b: number): number => a * b;

// Parâmetro opcional
function greet(name: string, greeting?: string): string {
  return `${greeting ?? "Olá"} ${name}`;
}

// Parâmetro padrão
function createUser(name: string, isAdmin = false) {}

// Rest parameters
function sum(...numbers: number[]): number {
  return numbers.reduce((a, b) => a + b, 0);
}
```

---

## Type Assertions (Asserções de Tipo)

```ts
let someValue: unknown = "this is a string";

// Sintaxe "as"
let strLength: number = (someValue as string).length;

// Sintaxe angle-bracket (não funciona em arquivos .tsx)
let strLength2: number = (<string>someValue).length;

// Non-null assertion
function process(value?: string) {
  console.log(value!.toUpperCase()); // Afirma que value não é null/undefined
}

// Const assertion
const directions = ["up", "down", "left", "right"] as const;
// Tipo: readonly ["up", "down", "left", "right"]
```

---

## Interfaces

```ts
interface User {
  readonly id: number;        // Somente leitura
  name: string;
  age?: number;               // Opcional
  email: string;
}

// Extensão
interface Admin extends User {
  role: "admin" | "superadmin";
  permissions: string[];
}

// Métodos
interface Logger {
  log(message: string): void;
  error(message: string): void;
}

// Index signature
interface StringMap {
  [key: string]: string;
}

// Implementação em classe
class ConsoleLogger implements Logger {
  log(message: string) {
    console.log(message);
  }
  error(message: string) {
    console.error(message);
  }
}
```

---

## Type Aliases

```ts
type ID = string | number;

type Point = {
  x: number;
  y: number;
};

type Callback = (data: string) => void;

type UserID = string;
type ProductID = number;

// Union Types
type Status = "pending" | "approved" | "rejected";

// Intersection Types
type Person = { name: string };
type Employee = { employeeId: number };
type Staff = Person & Employee;
```

---

## Union e Intersection Types

```ts
// Union
function printId(id: number | string) {
  if (typeof id === "string") {
    console.log(id.toUpperCase());
  } else {
    console.log(id);
  }
}

// Intersection
type Movable = { move: () => void };
type Flyable = { fly: () => void };
type SuperHero = Movable & Flyable;
```

---

## Narrowing (Estreitamento de Tipos)

```ts
function padLeft(value: string, padding: string | number) {
  if (typeof padding === "number") {
    return " ".repeat(padding) + value;
  }
  return padding + value;
}

// Type guards personalizados
function isString(value: unknown): value is string {
  return typeof value === "string";
}

// Discriminated Unions
type Shape =
  | { kind: "circle"; radius: number }
  | { kind: "square"; side: number }
  | { kind: "rectangle"; width: number; height: number };

function area(shape: Shape): number {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius ** 2;
    case "square":
      return shape.side ** 2;
    case "rectangle":
      return shape.width * shape.height;
  }
}
```

---

## Enums

```ts
// Numeric Enum
enum Direction {
  Up,      // 0
  Down,    // 1
  Left,    // 2
  Right,   // 3
}

// String Enum
enum Color {
  Red = "RED",
  Green = "GREEN",
  Blue = "BLUE",
}

// Const Enum (mais eficiente)
const enum HttpStatus {
  OK = 200,
  NotFound = 404,
  InternalError = 500,
}

// Heterogeneous (não recomendado)
enum Mixed {
  No = 0,
  Yes = "YES",
}
```

---

## Classes

```ts
class Animal {
  // Propriedades
  name: string;
  private age: number;           // Acessível apenas dentro da classe
  protected species: string;     // Acessível na classe e subclasses
  readonly id: number;           // Somente leitura

  // Construtor com parameter properties
  constructor(name: string, age: number, species: string, id: number) {
    this.name = name;
    this.age = age;
    this.species = species;
    this.id = id;
  }

  // Métodos
  move(distance: number = 0) {
    console.log(`${this.name} moved ${distance}m.`);
  }

  // Getter / Setter
  get info(): string {
    return `${this.name} (${this.species})`;
  }
}

class Dog extends Animal {
  bark() {
    console.log("Woof!");
  }

  // Acesso a protected
  getSpecies() {
    return this.species;
  }
}

// Abstract class
abstract class Vehicle {
  abstract start(): void;
  stop() {
    console.log("Stopped");
  }
}

// Static members
class MathUtils {
  static PI = 3.14159;
  static calculateCircumference(radius: number) {
    return 2 * MathUtils.PI * radius;
  }
}
```

---

## Generics

```ts
// Função genérica
function identity<T>(arg: T): T {
  return arg;
}

const output = identity<string>("myString");
const output2 = identity(42); // Inferido

// Interface genérica
interface Box<T> {
  contents: T;
}

// Classe genérica
class Queue<T> {
  private data: T[] = [];

  push(item: T) {
    this.data.push(item);
  }

  pop(): T | undefined {
    return this.data.shift();
  }
}

// Constraints
function getLength<T extends { length: number }>(arg: T): number {
  return arg.length;
}

// Multiple type parameters
function pair<T, U>(first: T, second: U): [T, U] {
  return [first, second];
}

// Default type parameter
interface ApiResponse<T = unknown> {
  data: T;
  status: number;
}
```

---

## Utility Types (Tipos Utilitários)

```ts
interface Todo {
  title: string;
  description: string;
  completed: boolean;
  createdAt: number;
}

// Partial - torna todas as propriedades opcionais
type PartialTodo = Partial<Todo>;

// Required - torna todas obrigatórias
type RequiredTodo = Required<Todo>;

// Readonly - torna todas readonly
type ReadonlyTodo = Readonly<Todo>;

// Pick - seleciona propriedades
type TodoPreview = Pick<Todo, "title" | "completed">;

// Omit - remove propriedades
type TodoInfo = Omit<Todo, "completed" | "createdAt">;

// Record - cria um tipo de objeto
type PageInfo = Record<"home" | "about" | "contact", { title: string }>;

// Exclude / Extract
type T0 = Exclude<"a" | "b" | "c", "a">;           // "b" | "c"
type T1 = Extract<"a" | "b" | "c", "a" | "f">;     // "a"

// NonNullable
type T2 = NonNullable<string | number | undefined>; // string | number

// ReturnType
type T3 = ReturnType<() => string>;                // string

// Parameters
type T4 = Parameters<(s: string, n: number) => void>; // [string, number]

// Awaited
type T5 = Awaited<Promise<string>>;                // string

// Uppercase / Lowercase / Capitalize / Uncapitalize
type T6 = Uppercase<"hello">;                      // "HELLO"
type T7 = Capitalize<"hello">;                     // "Hello"
```

---

## Mapped Types e Conditional Types

```ts
// Mapped Type
type ReadonlyProps<T> = {
  readonly [P in keyof T]: T[P];
};

// Conditional Type
type IsString<T> = T extends string ? true : false;

type A = IsString<"hello">; // true
type B = IsString<number>;  // false

// Infer
type GetReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

type RT = GetReturnType<() => string>; // string

// Distributive Conditional Types
type ToArray<T> = T extends any ? T[] : never;
type StrArrOrNumArr = ToArray<string | number>; // string[] | number[]
```

---

## Modules e Namespaces

```ts
// Export
export interface User {
  name: string;
}

export function createUser(name: string): User {
  return { name };
}

export default class App {}

// Import
import { User, createUser } from "./user";
import App from "./app";
import * as Utils from "./utils";
import type { User } from "./user"; // Type-only import

// Re-export
export { User } from "./user";
export type { User } from "./user";
```

---

## Decorators (Experimental / Stage 3)

```ts
// Habilitar em tsconfig: "experimentalDecorators": true

function sealed(constructor: Function) {
  Object.seal(constructor);
  Object.seal(constructor.prototype);
}

@sealed
class BugReport {
  type = "report";
  title: string;

  constructor(t: string) {
    this.title = t;
  }
}

// Method decorator
function log(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
  const original = descriptor.value;
  descriptor.value = function (...args: any[]) {
    console.log(`Calling ${propertyKey}`);
    return original.apply(this, args);
  };
}
```

---

## tsconfig.json Essenciais

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "noEmit": true,
    "isolatedModules": true,
    "jsx": "react-jsx",
    "lib": ["ES2022", "DOM", "DOM.Iterable"],
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"]
    }
  },
  "include": ["src"],
  "exclude": ["node_modules", "dist"]
}
```

### Flags importantes de Strict Mode

| Flag                        | Descrição                                      |
|-----------------------------|------------------------------------------------|
| `strict`                    | Ativa todas as verificações estritas           |
| `noImplicitAny`             | Erro em tipos implícitos `any`                 |
| `strictNullChecks`          | `null` e `undefined` são tipos distintos       |
| `strictFunctionTypes`       | Checagem mais rigorosa de funções              |
| `strictBindCallApply`       | Checagem correta de `bind`, `call`, `apply`    |
| `noImplicitThis`            | Erro em `this` implícito                       |
| `alwaysStrict`              | Emite `"use strict"`                           |

---

## Dicas Rápidas

- Prefira `unknown` a `any`
- Use `as const` para valores literais imutáveis
- Prefira `interface` para objetos extensíveis e `type` para unions/intersections
- Use `satisfies` (TS 4.9+) para validar tipos sem mudar a inferência:
  ```ts
  const config = {
    endpoint: "/api",
    timeout: 3000
  } satisfies Record<string, string | number>;
  ```
- Type narrowing com `in`, `typeof`, `instanceof` e type predicates
- Evite enums numéricos quando possível — prefira union de strings ou `as const` objects

---

## Recursos Oficiais

- [Documentação oficial](https://www.typescriptlang.org/docs/)
- [Playground](https://www.typescriptlang.org/play)
- [Handbook](https://www.typescriptlang.org/docs/handbook/intro.html)
- [Cheatsheets oficiais (PDF)](https://www.typescriptlang.org/cheatsheets/)

---

**Criado para consulta rápida** · TypeScript é um superset de JavaScript que adiciona tipagem estática opcional.

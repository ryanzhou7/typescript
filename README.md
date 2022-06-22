# typescript

## TypeScript Fundamentals v3

[Repo](https://github.com/mike-north/ts-fundamentals-v3)

[Bare bones TS compiler](https://www.typescript-training.com/course/fundamentals-v3/02-hello-typescript/)

```json
"compilerOptions": {
  "target": "ES2017"
  // cleaner generated dist than ES2015
  // creates .d.ts - declaration file
}
```

- .ts files contain info and runs
- .js run only
- .d.ts only contain info
- `export` is CommonJS modules

```ts
6, 'hello' = literal type
const age = 6; // immutable value type
```

- object literal type may only specific known props, unstate types will cause an error

```ts

```

-Type-checking can be thought of as a task that attempts to evaluate the question of compatibility or type equivalence:

- static type checking = done at compile time (TS, java)
- dynamic = done at runtime (js, python, perl)

- Nominal type system, about names

```java

public class CarChecker {
  // takes a `Car` argument, returns a `String`
  public static String printCar(Car car) {  }
}
Car car = new Car();
CarChecker.checkCar(car); // checks if car is instance of class named Car car
```

- Structural type system, about structure / names

```ts
class Car {
  make: string;
  isElectric: boolean;
}

class Truck {
  make: string;
  towingCapacity: number;
}

const vehicle = {
  make: "Honda",
  model: "Accord",
  year: 2017,
};

function printCar(car: { make: string }) {
  console.log(`${car.make} ${car.model} (${car.year})`);
}

printCar(new Car()); // Fine
printCar(new Truck()); // Fine
printCar(vehicle); // Fine
// Only cares that has make prop
```

- type guards: expressions, which when used with control flow statement, allow us to have a more specific type for a particular value.

```ts
const e: Error | string = '';
if( e instance of Error){ // <- type guard
  // can do Error specific code here, aka narrowing
}else{
  // Discriminated Unions, can do string things here (aka)
}
```

- 2 ways to define type, type alias & interfaces
- type alias declaration is per scope

```ts
// combine existing types
type SpecialDate = Date & { getReason(): string };
```

interface: defines an object type, i.e. an instance of a class could look like this

```ts
// 2 heritage clauses
// extends: a subclass extends from a base class
interface Animal {
  isAlive(): boolean;
}
interface Mammal extends Animal {
  getFurOrHairColor(): string;
}

// implement: a given class should produce instances that confirm to given interface implements
interface AnimalLike {
  eat(food): void;
}
class Dog implements AnimalLike {
  eat() {
    return "woof";
  }
}
```

- no multiple inheritance, but can implement multiple

```ts
// what it means for interfaces to be open
interface AnimalLike {
  isAlive(): boolean;
}
interface AnimalLike {
  eat(food): void;
}
// this is fine, they will merge together

// use case - tells TS that `exampleProperty` exists
interface Window {
  exampleProperty: number;
}
```

Type vs interfaces

1. If you need to define something other than an object type (e.g., use of the | union type operator), you must use a type alias
2. If you need to define a type to use with the implements heritage term, it’s best to use an interface
3. If you need to allow consumers of your types to augment them, you must use an interface.

Recursion types: self-referential, and are often used to describe infinitely nestable types

```ts
type NestedNumbers = number | NestedNumbers[];

const val: NestedNumbers = [3, 4, [5, 6, [7], 59], 221];

if (typeof val !== "number") {
  val.push(41);
}
```

- void type: the return value of a void function is intended to be ignored

function overloads - defining multiple function heads that serve as entry points to a single implementation

```ts
// not good as HTMLFormElement and FormSubmitHandler should link, but this defines the entire cardinal set
function handleMainEvent(
  elem: HTMLFormElement | HTMLIFrameElement,
  handler: FormSubmitHandler | MessageHandler
) {}

// improved
function handleMainEvent(elem: HTMLFormElement, handler: FormSubmitHandler);
function handleMainEvent(elem: HTMLIFrameElement, handler: MessageHandler);
function handleMainEvent(
  elem: HTMLFormElement | HTMLIFrameElement,
  handler: FormSubmitHandler | MessageHandler
) {
  // implementation...
}

handleMainEvent;
// function handleMainEvent(elem: HTMLFormElement, handler: FormSubmitHandler): any (+1 overload)
// looks like 3 function declarations but really just 2 heads exposed, the last head / body is inaccessible
// note: the implementation must be general enough to include everything that possible through the explosed,
```

this type:

```ts
function myClickHandler(event: Event) {
  this.disabled = true;
  // ERROR 'this' implicitly has type 'any' because it does not have a type annotation.
}
myClickHandler(new Event("click")); // seems ok

// Fix
function myClickHandler(this: HTMLButtonElement, event: Event) {
  this.disabled = true;
}
```

Function type best practices: Explicitly define return types

[Classes (skipped)](https://www.typescript-training.com/course/fundamentals-v3/10-classes/)

- top type (symbol: ⊤) is a type that describes any possible value allowed by the system, i.e. any & unknown
- Any differs from unknown in that values with an unknown type cannot be used without first applying a type guard
- A bottom type (symbol: ⊥) is a type that describes no possible value allowed by the system. To use our set theory mental model, we could describe this as "any value from the following set: { } (intentionally empty)", i.e. never

```ts
// @errors: 2322
function obtainRandomVehicle(): any {
  return {} as any;
}
/// ---cut---
class Car {
  drive() {
    console.log("vroom");
  }
}
class Truck {
  tow() {
    console.log("dragging something");
  }
}
class Boat {
  isFloating() {
    return true;
  }
}
type Vehicle = Truck | Car | Boat;

let myVehicle: Vehicle = obtainRandomVehicle();

// The exhaustive conditional
if (myVehicle instanceof Truck) {
  myVehicle.tow(); // Truck
} else if (myVehicle instanceof Car) {
  myVehicle.drive(); // Car
} else {
  // ERROR! Type 'Boat' is not assignable to type 'never'.
  const neverValue: never = myVehicle;
}
```

Type guards

```ts
// Built in
let value:
  | Date
  | null
  | undefined
  | "pineapple"
  | [number]
  | { dateRange: [Date, Date] };

// instanceof
if (value instanceof Date) {
  value;
  // ^?
}
// typeof
else if (typeof value === "string") {
  value;
  // ^?
}
// Specific value check
else if (value === null) {
  value;
  // ^?
}
// Truthy/falsy check
else if (!value) {
  value;
  // ^?
}
// Some built-in functions
else if (Array.isArray(value)) {
  value;
  // ^?
}
// Property presence check
else if ("dateRange" in value) {
  value;
  // ^?
} else {
  value;
  // ^?
}

// user defined
interface CarLike {
  make: string;
  model: string;
  year: number;
}

let maybeCar: unknown;

// the guard
function isCarLike(valueToTest: any): valueToTest is CarLike {
  return (
    valueToTest &&
    typeof valueToTest === "object" &&
    "make" in valueToTest &&
    typeof valueToTest["make"] === "string" &&
    "model" in valueToTest &&
    typeof valueToTest["model"] === "string" &&
    "year" in valueToTest &&
    typeof valueToTest["year"] === "number"
  );
}

// using the guard
if (isCarLike(maybeCar)) {
  maybeCar;
  // ^?
}

// alternative - eliminates the if statement
function assertsIsCarLike(valueToTest: any): asserts valueToTest is CarLike {
  if (
    !(
      valueToTest &&
      typeof valueToTest === "object" &&
      "make" in valueToTest &&
      typeof valueToTest["make"] === "string" &&
      "model" in valueToTest &&
      typeof valueToTest["model"] === "string" &&
      "year" in valueToTest &&
      typeof valueToTest["year"] === "number"
    )
  )
    throw new Error(`Value does not appear to be a CarLike${valueToTest}`);
}

assertsIsCarLike(maybeCar);
maybeCar; // now known type is CarLike
```

```ts
// !. tells ts to ignore possibility that value could null / undefined
cart.fruits!.push({ name: "kumkuat", qty: 1 });
```

- Generics: allow type parametrization and greater type reuse. Describe the minimum requirement for a type param

```ts
interface HasId {
  id: string;
}
interface Dict<T> {
  [k: string]: T;
}
function listToDict<T extends HasId>(list: T[]): Dict<T> {}
// generic constraints
```

- Best Practices: Use each type parameter at least twice. Any less and you might be casting with the as keyword

```ts
function returnAs<T>(arg: any): T {
  return arg // 🚨 an `any` that will _seem_ like a `T`

(parameter) arg: any
}

// 🚨 DANGER! 🚨
const first = returnAs<number>(window)
// const first: number
// const sameAs = window as any as number
```

<br/>
<br/>

[Making TypeScript Stick](https://www.typescript-training.com/course/making-typescript-stick)

## 6 significant TS updates

```ts

```

[TS](https://www.typescript-training.com/course/making-typescript-stick/03-recent-updates-to-typescript/)
tuple type: an ordered collection (often of known length), with the type of each member known as well.

---
tags: Programing, Typescript
---
# TypeScript 學習筆記
[TOC]

# `第一章` Basic types
# `第二章` Interfaces
# `第三章` Funcions
# `第四章` Literal Types
# `第五章` Union and Intersection Types

<font size=5>透過Intersection 以及 Union 
可以提供一個方式使我們能夠組成types</font>

## Union
<font size=5>當遇到一個函式需要number以及string時，如:</font>
```typescript=
/**
 * Takes a string and adds "padding" to the left.
 * If 'padding' is a string, then 'padding' is appended to the left side.
 * If 'padding' is a number, then that number of spaces is added to the left side.
 */
function padLeft(value: string, padding: any) {
  if (typeof padding === "number") {
    return Array(padding + 1).join(" ") + value;
  }
  if (typeof padding === "string") {
    return padding + value;
  }
  throw new Error(`Expected string or number, got '${padding}'.`);
}

padLeft("Hello world", 4); // returns "    Hello world"
```
<font size=5>
在上面的狀況下，若傳入的 padding 型別不是 number 或 string 仍然有效
改法如下
</font>

```typescript=
/**
 * Takes a string and adds "padding" to the left.
 * If 'padding' is a string, then 'padding' is appended to the left side.
 * If 'padding' is a number, then that number of spaces is added to the left side.
 */
function padLeft(value: string, padding: string | number) {
  if (typeof padding === "number") {
    return Array(padding + 1).join(" ") + value;
  }
  if (typeof padding === "string") {
    return padding + value;
  }
  throw new Error(`Expected string or number, got '${padding}'.`);
}

padLeft("Hello world", 4); // returns "    Hello world"
```

## Unions with Common Fields

<font size=5>
若有一個value為union type，我們只能存取這些type共同的members
</font>

```typescript=
interface Bird {
  fly(): void;
  layEggs(): void;
}

interface Fish {
  swim(): void;
  layEggs(): void;
}

declare function getSmallPet(): Fish | Bird;

let pet = getSmallPet();
pet.layEggs();

// Only available in one of the two possible types
pet.swim(); //Error happended
```

## Discriminating Unions
```typescript=
type NetworkLoadingState = {
  state: "loading";
};

type NetworkFailedState = {
  state: "failed";
  code: number;
};

type NetworkSuccessState = {
  state: "success";
  response: {
    title: string;
    duration: number;
    summary: string;
  };
};

// Create a type which represents only one of the above types
// but you aren't sure which it is yet.
type NetworkState =
  | NetworkLoadingState
  | NetworkFailedState
  | NetworkSuccessState;
```
<font size=5>
在上面我們定義出三種State的type <br>
並且他們有一個共同的元素 state <br>
在這之後我們可以透過當前state的值來得知現在狀態為何
</font>

```typescript=
type NetworkState =
  | NetworkLoadingState
  | NetworkFailedState
  | NetworkSuccessState;

function networkStatus(state: NetworkState): string {
  // Right now TypeScript does not know which of the three
  // potential types state could be.

  // Trying to access a property which isn't shared
  // across all types will raise an error
  state.code; //error
  
  // Property 'code' does not exist on type 'NetworkState'.
  // Property 'code' does not exist on type 'NetworkLoadingState'.


  // By switching on state, TypeScript can narrow the union
  // down in code flow analysis
  switch (state.state) {
    case "loading":
      return "Downloading...";
    case "failed":
      // The type must be NetworkFailedState here,
      // so accessing the `code` field is safe
      return `Error ${state.code} downloading`;
    case "success":
      return `Downloaded ${state.response.title} - ${state.response.summary}`;
  }
}
```

## Union Exhaustiveness Checking
<font size=5>
當我們新增union type的型別時
我們會希望compiler能告訴我們曾經使用到的地方需要更新
<br>
<br>
且若沒有使用到時，傳的值應該會與原先定義的不同
</font>

```typescript=
type NetworkFromCachedState = {
  state: "from_cache";
  id: string
  response: NetworkSuccessState["response"]
}

type NetworkState =
  | NetworkLoadingState
  | NetworkFailedState
  | NetworkSuccessState
  | NetworkFromCachedState;

function logger(s: NetworkState) {
  switch (s.state) {
    case "loading":
      return "loading request";
    case "failed":
      return `failed with code ${s.code}`;
    case "success":
      return "got response"
  }
}
```
<font size=5>
透過never型別來偵測型別完不完整
</font>

```typescript=
function assertNever(x: never): never {
  throw new Error("Unexpected object: " + x);
}

function logger(s: NetworkState): string {
  switch (s.state) {
    case "loading":
      return "loading request";
    case "failed":
      return `failed with code ${s.code}`;
    case "success":
      return "got response";
    default: 
      return assertNever(s)
  }
}
```

## Intersection Types
<font size=5>
Intersection 語法為 & <br>
將多個type 合成為一個 大type <br>
且每個type有的member 大type都得涵蓋住 <br>
底下則是把共同的type取出來<br>
使得單元變得更小，模組化更容易<br>
</font>

```typescript=
interface ErrorHandling {
  success: boolean;
  error?: { message: string };
}

interface ArtworksData {
  artworks: { title: string }[];
}

interface ArtistsData {
  artists: { name: string }[];
}

// These interfaces are composed to have
// consistent error handling, and their own data.

type ArtworksResponse = ArtworksData & ErrorHandling;
type ArtistsResponse = ArtistsData & ErrorHandling;

const handleArtistsResponse = (response: ArtistsResponse) => {
  if (response.error) {
    console.error(response.error.message);
    return;
  }

  console.log(response.artists);
};
```

# Class

## Classes
<font size=5>
typescript 也支援Class 
</font>

```typescript=
class Greeter {
  greeting: string;

  constructor(message: string) {
    this.greeting = message;
  }

  greet() {
    return "Hello, " + this.greeting;
  }
}

let greeter = new Greeter("world");
```

## Inheritance
<font size=5>
觀念相同就不花篇幅說了
</font>

```typescript=
class Animal {
  move(distanceInMeters: number = 0) {
    console.log(`Animal moved ${distanceInMeters}m.`);
  }
}

class Dog extends Animal {
  bark() {
    console.log("Woof! Woof!");
  }
}

const dog = new Dog();
dog.bark();
dog.move(10);
dog.bark();
```

## Public, private, and protected modifiers
### public by default
<font size= 5>
在上面的篇幅中，Class沒有特別宣告public<br>
但如果需要的話，仍然可以在變數前方增添public keyword
</font>

```typescript=
class Animal {
  public name: string;

  public constructor(theName: string) {
    this.name = theName;
  }

  public move(distanceInMeters: number) {
    console.log(`${this.name} moved ${distanceInMeters}m.`);
  }
}
```

### ECMAScript Private Fields

<font size=5>
在 TypeScript 3.8 支援最新的JavaScript語法定義 private
</font>

```typescript=
class Animal {
  #name: string
  constructor(theName: string) {
    this.#name = theName
  }
}

new Animal("Cat").#name
```

### Understanding TypeScript's `private`

<font size=5>
TypeScript 本身提供關鍵字 private 來宣告私有變數
</font>

```typescript=
class Animal {
  private name: string;

  constructor(theName: string) {
    this.name = theName;
  }
}

new Animal("Cat").name;
```

<font size=5>
private Class 繼承實例
</font>

```typescript=
class Animal {
  private name: string;
  constructor(theName: string) {
    this.name = theName;
  }
}

class Rhino extends Animal {
  constructor() {
    super("Rhino");
  }
}

class Employee {
  private name: string;
  constructor(theName: string) {
    this.name = theName;
  }
}

let animal = new Animal("Goat");
let rhino = new Rhino();
let employee = new Employee("Bob");

animal = rhino;
animal = employee; // 會出錯 兩者皆有自己的 name
```


### Understanding `protected`

<font size=5>
宣告為protected的變數<br>
在繼承後的subclass能取出<br>
</font>

```typescript=
class Person {
  protected name: string;
  constructor(name: string) {
    this.name = name;
  }
}

class Employee extends Person {
  private department: string;

  constructor(name: string, department: string) {
    super(name);
    this.department = department;
  }

  public getElevatorPitch() {
    return `Hello, my name is ${this.name} and I work in ${this.department}.`;
  }
}

let howard = new Employee("Howard", "Sales");
console.log(howard.getElevatorPitch());
console.log(howard.name);
```

### Readonly modifier
<font size=5>
Readonly 變數在initial的時候就應該賦予值<br>
只有在 宣告時 以及 constuctor() 內可以賦予<br>
而 Readonly 的變數被視為public的 <br>
若要私有化必須加 private 
</font>

```typescript=
class Octopus {
  readonly name: string;
  readonly numberOfLegs: number = 8;

  constructor(theName: string) {
    this.name = theName;
  }
}

let dad = new Octopus("Man with the 8 strong legs");
dad.name = "Man with the 3-piece suit";// 出錯
```

### Parameter properties

<font size=5>
我們可以在constructor的參數名加上readonly<br>
這樣的話我們就可以減少原先版本宣告的那行了
</font>

```typescript=
class Octopus {
  readonly numberOfLegs: number = 8;
  constructor(readonly name: string) {}
}

let dad = new Octopus("Man with the 8 strong legs");
dad.name;
```

### Accessors

<font size=5>
描述有關get() 跟 set()
</font>

```typescript=
const fullNameMaxLength = 10;

class Employee {
  private _fullName: string = "";

  get fullName(): string {
    return this._fullName;
  }

  set fullName(newName: string) {
    if (newName && newName.length > fullNameMaxLength) {
      throw new Error("fullName has a max length of " + fullNameMaxLength);
    }

    this._fullName = newName;
  }
}

let employee = new Employee();
employee.fullName = "Bob Smith";

if (employee.fullName) {
  console.log(employee.fullName);
}
```

### Static Properies
<font size=5>
`Static` 類別的靜態屬性

類別的靜態屬性與方法
不需要經由建構物件的過程
而是直接從類別本身提供的屬性與方法
稱之為靜態屬性與方法
或者被稱為靜態成員（Static Members）
具有固定、單一版本、不變的原則

通常會使用靜態成員的狀況：
    
1. 靜態成員不會隨著物件建構的不同而隨之改變
        
2. 靜態成員可以作為類別本身提供的工具，不需要經過建構物件的程序；換句話說：類別提供之靜態成員本身就是可被操作的介面


</font>

```typescript=
class Grid {
  static origin = { x: 0, y: 0 };

  calculateDistanceFromOrigin(point: { x: number; y: number }) {
    let xDist = point.x - Grid.origin.x;
    let yDist = point.y - Grid.origin.y;
    return Math.sqrt(xDist * xDist + yDist * yDist) / this.scale;
  }

  constructor(public scale: number) {}
}

let grid1 = new Grid(1.0); // 1x scale
let grid2 = new Grid(5.0); // 5x scale

console.log(grid1.calculateDistanceFromOrigin({ x: 10, y: 10 }));

```

### Abstract Classes

<font size=5>
描述Class藍圖不需要特地實作
</font>


```typescript=
abstract class Animal {
  abstract makeSound(): void;

  move(): void {
    console.log("roaming the earth...");
  }
}
```

### Advanced Techniques

#### Constructor Functions

<font size=5>
Because classes create types, you can use them in the same places you would be able to use interfaces.
</font>

```typescript=
class Point {
  x: number;
  y: number;
}

interface Point3d extends Point {
  z: number;
}

let point3d: Point3d = { x: 1, y: 2, z: 3 };
```

# Enums

## Numeric enums
<font size=5>
常見於其他程式語言中
</font>

```typescript=
enum Direction {
  Up = 1,
  Down,
  Left,
  Right
}
```
<font size=5>
在使用上也十分容易
</font>

```typescript=
enum UserResponse {
  No = 0,
  Yes = 1
}

function respond(recipient: string, message: UserResponse): void {
  // ...
}

respond("Princess Caroline", UserResponse.Yes);
```

## String enums
<font size=5>
觀念相同，只是需要傳入的值為string
</font>

```typescript=
enum Direction {
  Up = "UP",
  Down = "DOWN",
  Left = "LEFT",
  Right = "RIGHT"
}
```

## Heterogeneous enums
<font size=5>
enums 可以把上面的混用，但這樣的行為不建議
</font>

```typescript=
enum BooleanLikeHeterogeneousEnum {
  No = 0,
  Yes = "YES"
}
```

## Computed and constant members
<font size=5>
如果enum的第一個元素沒有initializer 則，他的值會為0
</font>

```typescript=
// E.X is constant:
enum E {
X
}
// All enum members in 'E1' and 'E2' are constant.

enum E1 {
X,
Y,
Z
}

enum E2 {
A = 1,
B,
C
}
```
<font size=5>
It is a compile time error for constant enum expressions to be evaluated to NaN or Infinity.
</font>
```typescript=
enum FileAccess {
  // constant members
  None,
  Read = 1 << 1,
  Write = 1 << 2,
  ReadWrite = Read | Write,
  // computed member
  G = "123".length
}
```
## Union enums and enum member types
<font size=5>
用Enum來當Type
</font>

```typescript=
enum ShapeKind {
  Circle,
  Square
}

interface Circle {
  kind: ShapeKind.Circle;
  radius: number;
}

interface Square {
  kind: ShapeKind.Square;
  sideLength: number;
}

let c: Circle = {
  kind: ShapeKind.Square, // 將會出錯, kind應該是Circle
  radius: 100
};

```
## Enums at runtime
## Enums at compile time 
<font size=5>
用keyof typeof來獲得Enum的Type時，會發現全部的keys都用string表示
</font>

```typescript=
enum LogLevel {
  ERROR,
  WARN,
  INFO,
  DEBUG
}

/**
 * This is equivalent to:
 * type LogLevelStrings = 'ERROR' | 'WARN' | 'INFO' | 'DEBUG';
 */
type LogLevelStrings = keyof typeof LogLevel;

function printImportant(key: LogLevelStrings, message: string) {
  const num = LogLevel[key];
  if (num <= LogLevel.WARN) {
    console.log("Log level key is:", key);
    console.log("Log level value is:", num);
    console.log("Log level message is:", message);
  }
}
printImportant("ERROR", "This is a message");
```
## Reverse mappings
<font size=5>
除了順向的從Enum取值外，也可以逆向的傳值獲得key的名稱
</font>

```typescript=
enum Enum {
  A
}

let a = Enum.A;
let nameOfA = Enum[a]; //'A'
```

<font size=5>
Keep in mind that string enum members do not get a reverse mapping generated at all.
</font>
## const enums
## Ambient enums
<font size=5>
奇葩的寫法XD
</font>

```typescript=
declare enum Enum {
  A = 1,
  B,
  C = 2
}
```

# Generics

## Hello World of Generics
<font size=5>
很久之前看到的東西了，基本上他會去抓取使用者傳進來的參數type<br>
然後create出一個同樣參數型別的function
</font>

```typescript=
function identity<T>(arg: T): T {
  return arg;
}
```

<font size=5>
上面這段函式，只要使用者輸入甚麼型別，他回傳的就是該型別<br>
因此可以方便我們去傳遞從其他地方來的型別成為我們要的<br>
以下為實際應用狀況
</font>

```typescript=
let output = identity<string>("myString");
//       ^ = let output: string
```

```typescript=
let output = identity("myString");
//       ^ = let output: string
```
## Working with Generic Type Variable
## Generic Types

```typescript=
interface GenericIdentityFn {
  <T>(arg: T): T;
}

function identity<T>(arg: T): T {
  return arg;
}

let myIdentity: GenericIdentityFn = identity;
```

## Generic Classes

```typescript=
class GenericNumber<T> {
  zeroValue: T;
  add: (x: T, y: T) => T;
}

let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function(x, y) {
  return x + y;
};
```

## Generic Constraint
<font size=5>
當我們要對Generic進行限制時，可透過建立出擁有必要元素的interface <br>
再透過Extends 去繼承他，如此一來便可以篩除不符合條件的參數了
</font>

```typescript=
interface Lengthwise {
  length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
  console.log(arg.length); // Now we know it has a .length property, 
  // so no more error
  return arg;
}
```

## Using Type Parameters in Generic Constraints
<font size=5>
底下這個比較難理解，首先我們知道傳入的參數一個是Object 另一個則是 key <br>
因此在Generic 參數設定上, K extends T的key們 <br>
因此K的型別會屬於T這個Object內容物的型態 <br>
因此可確保傳入的obj以及key都是有效參數 <br>
</font>

```typescript=
function getProperty<T, K extends keyof T>(obj: T, key: K) {
  return obj[key];
}

let x = { a: 1, b: 2, c: 3, d: 4 };

getProperty(x, "a");
// getProperty(x, "m");
```

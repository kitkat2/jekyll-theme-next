# typescript



### 数据类型

##### 原始类型：

Boolean Number String undefined null

##### any类型

##### 数组：数据类型相同

```typescript
let arrOfNumbers: number[] = [1,2,3]
```

##### 类数组

##### 元组：数据类型可不同

```typescript
let u: [string, number] = ['finn', 20]
u.push(true) // 报错 只能添加string或number类型
```

##### Object类型

###### interface：

```typescript
interface Person {
    readonly id: number; // readonly表示只读属性，赋值后不可被更改
    name: string;
    age?: number; // ?表示可选属性
}

// 定义对象时必须与interface的定义一致
let finn: Person = {
    id: 1,
    name: "finn",
    age: 20, // 该字段为可选属性，可不添加
}

finn.id = 123 // 报错
```

readonly 用于属性， const 用于变量

###### 函数 Function

```typescript
function add(x: number, y: number, z?: number): number{
    if (typeof z === 'number') {
        return x + y + z;
    } else {
        return x + y;
    }
}

// 表达式
const add = (x: number, y: number, z?: number): number => {
    if (typeof z === 'number') {
        return x + y + z;
    } else {
        return x + y;
    }
}

```



类型推论

不用声明类型 直接赋值，通过赋值判断变量类型。类似python

联合类型

```typescript
let numOrString:number | string
// 在不确定具体类型时，只能使用共有属性和方法
```

类型断言

```typescript
function getLength(input: string | number): number {
    const str = input as string
    if(str.length) {
        return str.length
    } else {
        const number = input as number
        return number.toString().length
    }
}

```

type guard

```typescript
function getLength(input: string | number): number {
    if (typeof input === 'string') {
        return input.length
    } else {
        return input.toString().length
    }
}
// 在条件分支语句中会自动的缩小类型范围
```



##### 类

权限访问修饰符

public

修饰的属性或方法是共有的、外部可使用、默认修饰

private、

修饰的属性或方法是私有的、外部不可用、子类不可使用

protected

修饰的属性或方法是受保护的、外部不可用、子类可使用

```typescript
class Animal {
    name: string;

    constructor(name:string) {
        this.name = name;
    }
    protected run() {
        return `${this.name} is running`
    }
}

class Dog extends Animal {
    bark() {
        return `${this.name} is barking`
    }
}

class Cat extends Animal {
    static categories = ['mammal']
    constructor(name) {
        super(name)
        console.log(this.name)
    }
    run() {
        return 'Meow,' + super.run() // 父类run方法是private则报错
    }
}

const snake = new Animal('lily')
console.log(snake.run())
const taxi = new Dog('taxi')
console.log(taxi.bark())
console.log(taxi.run()) // 父类run方法是private protected 则报错
const maomao = new Cat('maomao')
console.log(maomao.run()) // 父类run方法是private protected 则报错
console.log(Cat.categories)
```

##### 类与接口

接口： 对类进行抽象

```typescript
interface Radio {
  switchRadio(trigger: boolean): void;
}
interface Battery {
  checkBatteryStatus(): void;
}
interface RadioWithBattery extends Radio {
  checkBatteryStatus(): void;
}

class Car implements Radio{
  switchRadio(trigger: boolean) {

  }
}

class Cellphone implements RadioWithBattery{
  switchRadio(trigger: boolean) {
    
  }
  checkBatteryStatus() {

  }
}
```

##### 枚举类

```typescript
enum Direction {
  Up,
  Down,
  Left,
  Right
}

console.log(Direction.Up) // 0
console.log(Direction[0]) // Up
```

1. 可以赋值字符串类型

```typescript
enum Direction {
  Up = 'UP',
  Down = 'DOWN',
  Left = 'LEFT',
  Right = 'RIGHT'
}

const value = 'UP'
if (value === Direction.Up) {
  console.log('go up!') // go up!
}

```

2. 使用常量枚举可以提升性能 添加const关键字

```javascript
// 使用const进行编译的结果
var value = 'UP';
if (value === "UP" /* Up */) {
    console.log('go up!');
}
// 不使用const
var Direction;
(function (Direction) {
    Direction["Up"] = "UP";
    Direction["Down"] = "DOWN";
    Direction["Left"] = "LEFT";
    Direction["Right"] = "RIGHT";
})(Direction || (Direction = {}));
var value = 'UP';
if (value === Direction.Up) {
    console.log('go up!');
}
```

3. 枚举有两种类型

   常量枚举

   计算枚举

##### 泛型 generics

1. 解决使用函数时参数类型丢失问题

```typescript
function echo <T>(arg : T): T{
  return arg
}

const str: string = '123' 
const result =  echo(str)  // result: string
```

2. 传入元组

![image-20200930112954887](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200930112954887.png)

3. 泛型约束

```typescript
function echoWithArr <T>(arg: T[]) :T[] {
  console.log(arg.length)
  return arg 
}
const arrs = echoWithArr([1, 2, 3]) // 只能传数组，但还有其他类型的数据也含有length属性
```

```typescript
interface IWithLength {
  length: number
}

function echoWithLength<T extends IWithLength>(arg: T): T {
  console.log(arg.length); // 只要含有length属性就能传递
  return arg 
}

const str1 = echoWithLength('str')
const obj = echoWithLength({ length: 10 })
const arr1 = echoWithLength([1,2,3])
```

4. 泛型在类中的应用

```typescript

class Queue<T> {
  private data = []; 
  push(item: T) {
    return this.data.push(item)
  }
  pop():T {
    return this.data.shift()
  }
}
const queue = new Queue<number>()
queue.push(1)
queue.push('str') // 报错 该队列类只能传入数字类型
console.log(queue.pop().toFixed())
console.log(queue.pop().toFixed())
```

5. 泛型在interface中的应用

```typescript
interface KeyPair<T, U> {
  key: T
  value: U
}

let kp1: KeyPair<number, string> = { key: 1, value: "string" }
let kp2: KeyPair<string, number> = { key: 'string', value: 2 }
```

6. 利用泛型定义数组

```typescript
let arr: number[] = [1, 2, 3]
let arrTwo: Array<number> = [1,2,3,'str'] // 报错 只能传入number类型
```

##### 类型别名、字面量、交叉类型

类型别名：给变量类型别名

```typescript
type StrOrNumber = string | number
let result3: StrOrNumber = '123'
result3 = 123
```

字面量

```typescript
const str: 'name' = 'name'
const number: 1 = 1
type Directions = 'Up' | 'Down' | 'Left' | 'Right'
let toWhere: Directions = 'Down'

interface IName {
  name: string
}

type IPerson = IName & {age: 123}
let person: IPerson = { name: '123', age: 123}
```

##### 第三方库

通常放在单独的文件中 命名为XXX.d.ts

```typescript
declare var jQuery: (selector: string) => any; // 只是声明 并不具体地说明内容
jQuery('#foo')
```

[官方有列出第三方库的使用](https://www.npmjs.com/~types)

##### 内置对象

[工具类型](https://www.typescriptlang.org/docs/handbook/utility-types.html)

```typescript
// global objects
const a: Array<number> = [1, 2, 3]
const date = new Date()
date.getTime()
const reg = /abc/
reg.test('abc')
// build-in object
Math.pow(2, 2)
// DOM and BOM 
let body = document.body
let allLis = document.querySelectorAll('li')
allLis.keys()

document.addEventListener('click', (e) => {
})
// Utility Types
interface IPerson1 {
  name: string,
  age: number
}

let finn: IPerson1 = {
  name: 'finn',
  age: 20
}

type IPartial = Partial<IPerson1> // 属性可选
let demon: IPartial = { name: 'viking' } 
type IOmit = Omit<IPerson1, 'name'> // 忽略属性
let alex : IOmit = {age: 20}
```


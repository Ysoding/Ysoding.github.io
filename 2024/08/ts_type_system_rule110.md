
#typescript

## TS Conditional Types 介绍

去了解[ts中的conditional types](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html)  
会发现extend，infer语法，很好理解extend用于判断类型是什么，然后结合`? :`做逻辑  
例如，

```ts
type Test<T> = T extends any[] ? 1 : 2;

// 如果T是数组类型，则返回1，否则返回2；

let x: Test<string[]> = 1; // x的类型是1
let y: Test<string> = 2; // y的类型是2

```

那么如果T是数组怎么获得T里的元素类型？是string还是number

```ts
type Test<T> = T extends any[] ? T[number] : never;

// ERROR: x类型应该是string 
// Type 'number' is not assignable to type 'string'.
// let x: Test<string[]> = 1;

let x: Test<string[]> = "1";



```

还可以使用infer语法

```ts
type Test<T> = T extends Array<infer E> ? E : never;

let x: Test<string[]> = "1";

```

有什么用？对于数组可能有`T[number]`访问元素的方法，但是如果是别的复杂类型呢？

好了简单介绍完，来看几个典型函数式编程中的函数实现。

### Tail

对一个数组，取排除第一个元素后的元素集合。

```ts
type Tail<Lst extends Array<any>> = Lst extends [infer head, ...infer Rest]
  ? Rest
  : never;

var x: Tail<[1, 2, 3]> = [2, 3];
```

### Head

取数组每一个元素。

```ts
type Head<Lst extends Array<any>> = Lst extends [infer head, ...infer Rest]
  ? head
  : never;

var y: Head<["a", "b", "c"]> = "a";

```

### Cons

向数组头部添加元素。

```ts
type Cons<E, Lst extends Array<E>> = [E, ...Lst];

var z: Cons<1, [1, 1]> = [1, 1, 1];

```

### Reverse

```ts
type Reverse<
  Lst extends Array<any>,
  Acc extends Array<any> = []
> = Lst extends [] ? Acc : Reverse<Tail<Lst>, Cons<Lst[0], Acc>>;

var a: Reverse<[1, 2, 3]> = [3, 2, 1];
```

## Rule110

[Wiki](https://en.wikipedia.org/wiki/Rule_110)

### 什么是Rule110?

Rule 110 是一种著名的一维元胞自动机规则，由数学家斯蒂芬·沃尔夫拉姆（Stephen Wolfram）在他的研究中提出。元胞自动机是一种离散数学模型，规则110属于其中的一维元胞自动机类别。

在一维元胞自动机中，每个单元（称为元胞）处于有限数量的状态之一（通常是0或1），并且根据与相邻元胞的状态及一个预定的规则进行状态更新。Rule 110具体规定了在一维数组中，每个元胞在下一时刻的状态是由它当前的状态以及其左邻和右邻元胞的状态共同决定的。

具体来说，Rule 110的更新规则如下表所示（当前状态表示当前元胞和它左右两个邻居的状态，下一状态表示该元胞下一时刻的状态）：

| 当前状态 (左邻, 自身, 右邻) | 下一状态 |
| --------------------------- | -------- |
| 111                         | 0        |
| 110                         | 1        |
| 101                         | 1        |
| 100                         | 0        |
| 011                         | 1        |
| 010                         | 1        |
| 001                         | 1        |
| 000                         | 0        |

Rule 110因其复杂性和行为特性而广受关注。尽管规则本身看似简单，但它展示了复杂的演化行为，并且已经被证明是图灵完备的（即它可以模拟任何计算机的计算过程）。

### 实现

用TS类型系统实现，很方便的在编译时刻计算出结果，不是。

```ts
// 建立Rul110表
type Cell = 0 | 1;
type Rule110<X extends Cell, Y extends Cell, Z extends Cell> = {
  "000": 0;
  "001": 1;
  "010": 1;
  "011": 1;
  "100": 0;
  "101": 1;
  "110": 1;
  "111": 0;
}[`${X}${Y}${Z}`];

// 根据State生成下一个State，第一个元素不变
type NextState<State extends Array<Cell>> = State extends [
  infer X extends Cell,
  ...infer Rest extends Array<Cell>
]
  ? [X, ...Next<X, Rest>]
  : [];

type Next<X extends Cell, Lst extends Array<Cell>> = Lst extends [
  infer Y extends Cell,
  infer Z extends Cell,
  ...infer Rest extends Array<Cell>
]
  ? [Rule110<X, Y, Z>, ...Next<Y, [Z, ...Rest]>]
  : Lst extends [infer Y extends Cell]
  ? [Y]
  : [];

// 执行N次，返回包含N个State的数组
type GenNTime<N extends Array<any>, State extends Array<Cell>> = N extends []
  ? []
  : [State, ...GenNTime<Tail<N>, NextState<State>>];

type N = [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0];

let k: GenNTime<N, [0, 1, 0, 0, 1, 0, 0, 1, 0, 0, 1, 0, 1, 0]> = 69;

```

### 运行

```sh
± % make run                                                                                                                                                                              !3359
npm install

added 2 packages, and audited 3 packages in 822ms

found 0 vulnerabilities
./node_modules/typescript/bin/tsc  -noErrorTruncation rule110.ts | sed 's/\[/\n[/g'
rule110.ts(62,5): error TS2322: Type 'number' is not assignable to type '
[
[0, 1, 0, 0, 1, 0, 0, 1, 0, 0, 1, 0, 1, 0], 
[0, 1, 0, 1, 1, 0, 1, 1, 0, 1, 1, 1, 1, 0], 
[0, 1, 1, 1, 1, 1, 1, 1, 1, 1, 0, 0, 1, 0], 
[0, 1, 0, 0, 0, 0, 0, 0, 0, 1, 0, 1, 1, 0], 
[0, 1, 0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 0], 
[0, 1, 0, 0, 0, 0, 0, 1, 1, 0, 0, 0, 1, 0], 
[0, 1, 0, 0, 0, 0, 1, 1, 1, 0, 0, 1, 1, 0], 
[0, 1, 0, 0, 0, 1, 1, 0, 1, 0, 1, 1, 1, 0], 
[0, 1, 0, 0, 1, 1, 1, 1, 1, 1, 1, 0, 1, 0], 
[0, 1, 0, 1, 1, 0, 0, 0, 0, 0, 1, 1, 1, 0], 
[0, 1, 1, 1, 1, 0, 0, 0, 0, 1, 1, 0, 1, 0], 
[0, 1, 0, 0, 1, 0, 0, 0, 1, 1, 1, 1, 1, 0], 
[0, 1, 0, 1, 1, 0, 0, 1, 1, 0, 0, 0, 1, 0], 
[0, 1, 1, 1, 1, 0, 1, 1, 1, 0, 0, 1, 1, 0]]'.
```

[Full Code](https://github.com/Ysoding/ts-type-system)

## References

[I Wrote Superhuman Code By Hands - YouTube](https://www.youtube.com/watch?v=6DEr82sj4zM&t=2623s)  
[🌳 A tiny language interpreter implemented purely in TypeScript's type-system](https://github.com/ronami/typelang)  
[rule110.ts](https://gist.github.com/rexim/7c966737e9a2737c7e17bdfc97ebc43a)  

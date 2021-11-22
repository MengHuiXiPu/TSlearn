# TS中泛型基础知识

## 联合类型vs交叉类型

```
// 联合类型
interface Bird {
  name: string;
  fly(): void;
}
interface Person {
  name: string;
  talk(): void;
}
type BirdPerson = Bird | Person;
let p: BirdPerson = { name: "zfeng", fly() {} }; 
let p1: BirdPerson = { name: "zfeng", talk() {} };
```

联合类型使用 “｜”表示或的关系， 满足其中的一个情况即可。

```
interface Bird {
  name: string;
  fly(): void;
}
interface Person {
  name: string;
  talk(): void;
}
type BirdPerson = Bird & Person;
let p: BirdPerson = { name: "zhufeng", fly() {}, talk() {} };
```

交叉类型使用“&”，表示与的关系，需要满足所有的情况。

## 内置条件类型

```
type Extract<T, U> = T extends U ? T : never;
type Exclude<T, U> = T extends U ? never : T;
type NonNullable<T> = T extends null | undefined ? never : T;

type N = NonNullable<string | number | null | undefined>;// 删除null和undifined;
type E = Exclude<string | number, string>; // 排除关系 输出 string;
type I = Extract<string | number, string>; // 包含关系 输出 number;
```

## 函数的类型推断

### 获取函数返回值的类型

```
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : any;

function getUserInfo(name: string, age: number) {
  return { name, age };
}
type UserInfo = ReturnType<typeof getUserInfo>;

const userA: UserInfo = {
  name: "zhufeng",
  age: 10,
};
```

### 获取函数参数的类型

```
type Parameters<T> = T extends (...args: infer R) => any ? R : any;
function getUserInfo(name: string, age: number) {
  return { name, age };
}
type T1 = Parameters<typeof getUserInfo>;  // [name: string, age: number]
```



# 泛型进阶

很多人对于泛型的理解还停留在基础的层面，我讲站在**集合的视角**去理解一下什么叫泛型。

## 案例一：字段的提取

给定一个接口 Persion,  里面有name,age,visiable,三个字段，现在的**要求是：得到一个新的接口，里面只有name,age。****一般人常见的思路：**

```
interface Person {
  name: string;
  age: number;
  visiable: boolean;
}

interface Person1 {
  name: string;
  age: number;
}
```

我们从写一个接口，就可以达到要求。但是这样子的写法，显得十分冗余。其实ts提供了方法，让我们可以实现，让我们一起看一下的例子。

### 方式一：Pick 提取字段

![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

```
// pick 的原理
// type Pick<T, K extends keyof T> = { [P in K]: T[P] };
interface Person {
  name: string;
  age: number;
  visiable: boolean;
}
type Person1 = Pick<Person, 'name'|'age'> ;
```

Person1 就包含 name,age 字段。

### 方式二：Omit 反向获取

![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

```
interface Person {
  name: string;
  age: number;
  visiable: boolean;
}
type Exclude<T, U> = T extends U ? never : T;
type Omit<T, K extends keyof T> = Pick<T, Exclude<keyof T, K>>;
type Person2 = Omit<Person, "age">;
```

## 案例二：两个接口的操作

我们把一个接口当作一个集合，那么两个集合的操作主要有：**并集，交集，差集。**

### 交集

![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

```
type Extract<T, U> = T extends U ? T : never;
type Intersection<T extends object, U extends object> = Pick<
  T,
  Extract<keyof T, keyof U> & Extract<keyof U, keyof T>
>;

type C1 = { name: string; age: number; visible: boolean };
type C2 = { name: string; age: number; sex: number };

type C3 = Intersection<C1, C2>;
```

**交集的定义：对于给定的两个集合，返回一个包含两个集合中共有元素的新集合。**通过Intersection实现交集，可以获得一个新接口，C3只包含 name.age。如上图。

### 差集

![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

```
type Exclude<T, U> = T extends U ? never : T;
type Diff<T extends object, U extends object> = Pick<
  T,
  Exclude<keyof T, keyof U>
>;

type C1 = { name: string; age: number; visible: boolean };
type C2 = { name: string; age: number; sex: number };

type C11 = Diff<C1, C2>;
```

**差集的定义：对于给定的两个集合，返回一个包含所有存在于第一个集合且不存在于第二个集合的元素的新集合。**通过Diff实现差集，可以获得一个新接口，接口只有visiable。如上图。

## 并集

![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

**并集的定义：对于给定的两个集合，返回一个包含两个集合中所有元素的新集合。**通过Merge实现并集，可以获得一个新接口，接口包含C1，C2 的所有属性。如上图。

```
//Compute的作用是将交叉类型合并
type Compute<A extends any> = A extends Function ? A : { [K in keyof A]: A[K] };
type Omit<T, K> = Pick<T, Exclude<keyof T, K>>;
type Merge<O1 extends object, O2 extends object> = Compute< O1 & Omit<O2, keyof O1>>;
type C1C2 = Merge<C1, C2>;
```

### 特殊的情况：Overwrite（覆盖）

```
type C1 = { name: string; age: number; visible: boolean };
type C2 = { name: string; age: string; sex: number };
```

C1,C2做merge, C1中有age,类型为number，C2中有age,类型为string,**那么合并之后，age是string,还是number类型呢？**

**Overwrite 泛型，解决了谁覆盖谁的问题。**

![图片](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

```
type C1 = { name: string; age: number; visible: boolean };
type C2 = { name: string; age: string; sex: number };

type Overwrite<
  T extends object,
  U extends object,
  I = Diff<T, U> & Intersection<U, T>
> = Pick<I, keyof I>;
  
type overwrite = Overwrite<C1, C2>;
```


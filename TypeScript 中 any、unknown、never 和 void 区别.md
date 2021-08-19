## TypeScript 中 any、unknown、never 和 void 区别



![图片](https://mmbiz.qpic.cn/mmbiz_png/bwG40XYiaOKmoYQYIcct3u37ogUSBqVjfAibibDFI8J0D6HAyhS6CJFcibSQuZmhSDRlsH11nmickcD5I6icWeFSby2Q/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

## any、unknown、never、void

### any

`any` 类型用于描述一个我们根本不知道类型的变量，或者说可以是任意类型的变量，不作任何约束，编译时会跳过对其的类型检查

```
let notSure: any

// 可以被赋值任意类型
notSure = 'sisterAn!'
notSure = 512
notSure = { hello: () => 'Hello sisterAn!' }

// 它也兼容任何类型
let num: number = 12
notSure = num
num = notSure
```

### unknown

`unknown` 表示未知类型，即写代码的时候还不知道具体会是怎样的数据类型，是 `typescript 3.0` 中引入的新类型， 与 `any` 类似，所有类型都可以分配给`unknown` 类型

```
let notSure: unknown = 'sisterAn!'

// 可以被赋值任意类型
notSure = 'sisterAn!'
notSure = 512
notSure = { hello: () => 'Hello sisterAn!' }
```

但与 `any` 不同的是， `unknown` 类型的变量不允许被 `any` 或 `unknown` 以外的变量赋值，也不允许执行 `unknown` 类型变量的方法

```
let notSure: unknown = 'sisterAn'
let notSure1: unknown = 'Hello'
let any1: any = 12
let num: number = 12

notSure = notSure1
notSure = any1

num = notSure
// error: Type 'unknown' is not assignable to type 'number'.

notSure.toLowerCase()
// error: Object is of type 'unknown'.
```

这种限制有很强的防御性，但如果我们要对未知类型执行某些操作，也不是没有办法

#### 方式一：使用类型断言缩小未知范围

```
let notSure: unknown = 'sisterAn'

console.log((notSure as string).toLowerCase())
```

#### 方式二：使用类型守卫进行类型收缩

```
let notSure: unknown = 'sisterAn'

if (typeof notSure === 'string') {
    console.log((notSure as string).toLowerCase())
}
// 或使用 instanceof 来缩小变量的类型
```

我们仅在 `notSure` 为 `string` 类型时，才执行 `toLowerCase` 方法，TypeScript 编译器会理解这一点，并假设类型

### never

`never` ，永不存在的值的类型，是 typescript 2.0 中引入的新类型，那什么是永不存在的类型，我们知道变量一旦声明，都会默认初始化为 `undefined` ，也不是永不存在的值，但其实有一些场景，值会永不存在，例如，那些总是会抛出异常或函数中执行无限循环的代码（死循环）的函数返回值类型

```
// 抛出异常
function error(msg: string): never {
    throw new Error(msg);
} // 抛出异常会直接中断程序运行，这样程序就运行不到返回值那一步了，即具有不可达的终点，也就永不存在返回了

// 死循环
function loopForever(): never {
    while (true) {};
} //同样程序永远无法运行到函数返回值那一步，即永不存在返回
```

变量也可以声明为 `never` 类型，因为它是永不存在值的类型，所以任何类型都不能赋值给 `never` 类型（除了`never`本身之外）。即使 `any` 也不可以赋值给 `never`

```
let never1: never

// any 也不能分配给 never
let any1: any = 'sisterAn'
never1 = any1 // Error

// 作为函数返回类型的 never
let never2: never = (() => {
  throw new Error('Throw error');
})();

never1 = never2
```

### void

`void` 某种程度上来说正好与 `any` 相反，表示无任何类型，没有类型，如果是函数则应没有返回值或者返回 `undefined` ：

```
function hello(): void {
    console.log("Hello sisterAn");
}
```

也可以声明一个 `void` 类型的变量，不过你只能为它赋予 `undefined` 、 `null` （注意，`"strictNullChecks": true` 时会报错）和 `void` 类型的值

```
let void1: void
let null1: null = null
let und1: undefined = undefined
let void2: void

void1 = void2
void1 = und1 
void1 = null1 // Type 'null' is not assignable to type 'void'.
```

## any、unknown、never、void 区别

#### 1. 定义

- `any` ：用于描述任意类型的变量，不作任何约束，编译时会跳过对其的类型检查
- `unknown` ：表示未知类型，即写代码的时候还不知道具体会是怎样的数据类型
- `never` ：永不存在的值的类型，常用于表示永不能执行到终点的函数返回值，例如抛出异常或函数中执行无限循环的代码（死循环）的函数返回值类型
- `void` ：表示无任何类型，没有类型，例如没有返回值的函数的返回值类型

**any 与 unknown 的区别：**

`unknown` 与 `any` 类似，但使用前必须进行断言或守卫

**never 与 void 的区别：**

用于函数时， `never` 表示函数用于执行不到返回值那一步（抛出异常或死循环）的返回值类型，即永不存在的值的类型，而 `void` 则表示没有返回值，不返回或返回 `undefined`

#### 2. 使用

- `any` 类型导致问题太多了，如类型污染，使用不存在的属性或方法而不报错等，而且不不方便后期维护，所以，建议能不用 `any` 就不用 `any` ，但是如果声明时并不确定具体的类型，则可以使用 `unknown` 代替，在使用时用类型断言或类型守卫进行类型收缩
- `never` 类型用于表示永不存在的值的类型，所以常用于构造条件类型来组合出更灵活的类型定义

```
// never: 从未出现的值的类型
// 如果 T 是 U 的子类型的话，那么就会返回 X，否则返回 Y
// 构造条件类型 : T extends U ? X : Y

type Exclude<T, U> = T extends U ? never : T;

// 相当于: type A = 'a'
type A = Exclude<'x' | 'a', 'x' | 'y' | 'z'> 
```

- `void` 常用于表示函数没有返回值

**参考链接**

- ts官网
- TypeScript 中类型 any，void，unknown，never之间的区别
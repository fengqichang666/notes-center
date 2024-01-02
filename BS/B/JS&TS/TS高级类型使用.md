# TS高级类型使用

## 常用特殊写法

```typescript
type LabelValueOptions = {
  label: string;
  value: any;
  [key: string]: string | number | boolean;
}[];

type Mutable<T> = { -readonly [P in keyof T]: T[P] };

type Dog = { name: string; age: number;  };
type D = keyof Dog; //type D = "name" | "age"

type Animals = 'pig' | 'cat' | 'dog'
type animals = {
    [key in Animals]: string
}
// type animals = {
//     pig: string; //第一次迭代
//     cat: string; //第二次迭代
//     dog: string; //第三次迭代
// }
// extends前面的类型能够赋值给extends后面的类型，那么表达式判断为真，否则为假。
type OptionalKeys<T extends Record<string, unknown>> = {
  [K in keyof T]: T extends Record<K, T[K]> ? never : K;
}[keyof T];
//[keyof T]: 这使用 TypeScript 的索引类型语法来获取映射类型的结果类型。它表示一个包含所有键名的联合类型。
```

## Record

以 typeof 格式快速创建一个类型，此类型包含一组指定的属性且都是必填。

```typescript
/**
 * Construct a type with a set of properties K of type T
 */
type Record<K extends keyof any, T> = {
    [P in K]: T;
};

type Coord = Record<'x' | 'y', number>;

// 等同于
type Coord = {
	x: number;
	y: number;
}
```

## Partial

将类型定义的所有属性都修改为可选。

```typescript
/**
 * Make all properties in T optional
 */
type Partial<T> = {
    [P in keyof T]?: T[P];
};


type Coord = Partial<Record<'x' | 'y', number>>;

// 等同于
type Coord = {
	x?: number;
	y?: number;
}

```

## Required

与 `Partial<T>` 程序类型的作用相反，将类型属性都变成必填。

```typescript
/**
 * Make all properties in T required
 */
type Required<T> = {
    [P in keyof T]-?: T[P];
};

type Coord = Required<{ x: number, y?:number }>;

// 等同于
type Coord = {
	x: number;
	y: number;
}
```

## Pick

从类型定义的属性中，选取指定一组属性，返回一个新的类型定义。

```typescript
/**
 * From T, pick a set of properties whose keys are in the union K
 */
type Pick<T, K extends keyof T> = {
    [P in K]: T[P];
};

type Coord = Record<'x' | 'y', number>;
type CoordX = Pick<Coord, 'x'>;

// 等用于
type CoordX = {
	x: number;
}

```

## Omit

排除**接口**中指定的属性

```typescript
/**
 * Construct a type with the properties of T except for those in type K.
 */
type Omit<T, K extends keyof any> = Pick<T, Exclude<keyof T, K>>;

interface I1 {
	a: number;
	b: string;
	c: boolean;
}

type AC = Omit<I1, 'b'>;     // { a:number; c:boolean } 
type C = Omit<I1, 'a' |'b'>  // { c: boolean }

```

## Readonly

```typescript
/**
 * Make all properties in T readonly
 */
type Readonly<T> = {
    readonly [P in keyof T]: T[P];
};

type Coord = Readonly<Record<'x' | 'y', number>>;

// 等同于
type Coord = {
    readonly x: number;
    readonly y: number;
}

// 如果进行了修改，则会报错：
const c: Coord = { x: 1, y: 1 };
c.x = 2; // Error: Cannot assign to 'x' because it is a read-only property.

```

## Exclude<T, U>

排除一个 **联合类型** 中指定的子类型：

```typescript
/**
 * Exclude from T those types that are assignable to U
 */
type Exclude<T, U> = T extends U ? never : T;

type T0 = Exclude<'a' | 'b' | 'c', 'b'> // 'a' | 'c'
type T1 = Exclude<string | number | boolean, boolean> // string | number


```

## Extract<T, U>

与 `Exclude<T, U>` 完全相反的功能，用于提取指定的 **联合类型**，如果不存在提取类型，则返回never。可以用在判断一个复杂的 联合类型 中是否包含指定子类型：

```typescript
/**
 * Extract from T those types that are assignable to U
 */
type Extract<T, U> = T extends U ? T : never;

type T0 = Extract<'a' | 'b' | 'c', 'a'> // 'a'
type T1 = Extract<string | number | boolean, boolean> // boolean
```

## NonNullable< T >

过滤掉 联合类型 中的 `null` 和 `undefined` 类型：

```typescript
/**
 * Exclude null and undefined from T
 */
type NonNullable<T> = T extends null | undefined ? never : T;

// 关闭 --strictNullChecks
let s: string = "foo";
s = null; // 正常

//因为 null 和 undefined 类型的特殊性，他们可以赋值给任何类型，这往往会带来意料之外的错误。当你开启 --strictNullChecks 设置后，TS 就会严格检查，只有被声明 null 后才能被赋值：
// 开启 --strictNullChecks
s = null; // Error: Type 'null' is not assignable to type 'string'. 

```

## Parameters<T extends (...args: any) => any>

获取函数的全部参数类型，以 **元组类型** 返回

```typescript
/**
 * Obtain the parameters of a function type in a tuple
 */
type Parameters<T extends (...args: any) => any> = T extends (...args: infer P) => any ? P : never;

type F1 = (a: string, b: number) => void;

type F1ParamTypes = Parameters(F1);  // [string, number]

```

## ConstructorParameters<T extends new (...args: any) => any>

获取**构造函数** 的全部参数

```typescript
/**
 * Obtain the parameters of a constructor function type in a tuple
 */
type ConstructorParameters<T extends new (...args: any) => any> = T extends new (...args: infer P) => any ? P : never;

interface IEntity {
    count?: () => number
}

interface IEntityConstructor {
    new (a: boolean, b: string): IEntity;
}

class Entity implements IEntity {
    constructor(a: boolean, b: string) { }
}

type EntityConstructorParamType = ConstructorParameters<IEntityConstructor>; // [boolean, string]


// 这里的 IEntityConstructor 接口用来干什么的呢，当基于 创建实例函数 时就派上了用场：
function createEntity(ctor: IEntityConstructor, ...arg: EntityConstructorParamType): IEntity {
    return new ctor(...arg);
}

const entity = createEntity(Entity, true, 'a');

```

## ReturnType<T extends (...args: any) => any>

接收函数声明，返回函数的返回值类型，如果多个类型则以 联合类型 方式返回

```typescript
/**
 * Obtain the return type of a function type
 */
type ReturnType<T extends (...args: any) => any> = T extends (...args: any) => infer R ? R : any;

type F1 = () => Date;

type F1ReturnType = ReturnType<F1>; // Date
```

## InstanceType<T extends new (...args: any) => any>

获取 构造函数 的返回类型，如果是多个就以 联合类型 的方式返回

```typescript
/**
 * Obtain the return type of a constructor function type
 */
type InstanceType<T extends new (...args: any) => any> = T extends new (...args: any) => infer R ? R : any;

type EntityType = InstanceType<IEntityConstructor>; // IEntity
```

## ThisParameterType< T >

获取函数中 `this` 的数据类型，如果没有则返回 `unknown` 类型

```typescript
/**
 * Extracts the type of the 'this' parameter of a function type, or 'unknown' if the function type has no 'this' parameter.
 */
type ThisParameterType<T> = T extends (this: infer U, ...args: any[]) => any ? U : unknown;

interface Foo {
    x: number
};

function fn(this: Foo) {}

type Test = ThisParameterType<typeof fn>; // Foo
                              
fn.bind({ x: 1 });   // 正常

fn.bind({ x: '1' }); // Error: ...Type 'string' is not assignable to type 'number'...


```

## OmitThisParameter< T >

移除函数中的 `this` 数据类型

```typescript
/**
 * Removes the 'this' parameter from a function type.
 */
type OmitThisParameter<T> = unknown extends ThisParameterType<T> ? T : T extends (...args: infer A) => infer R ? (...args: A) => R : T;

interface Foo {
    x: number
};

type Fn = (this: Foo) => void

type NonReturnFn = OmitThisParameter<Fn>; // () => void

// 声明此类的函数类型效果如下：
function f(this: void) {} // 此声明在函数内不可使用 this

```


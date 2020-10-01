# TypeScript 中的数组操作

## 介绍

我们在编码时，总会用到数组/列表这种类型，用于在单个对象中存储多个内容。在 TypeScript 中，也已经内置了该类型，方便我们来使用。

## 操作

### 初始化

使用下面的代码即可创建并初始化一个数组类型的对象。其中，在构造函数中不输入参数即表示创建一个空数组。

```typescript
let array : string[] = ["1", "2", "3"];

let numberArray : Array<number> = [1, 2, 3];

let anyArray : Array<any> = new Array<any>();
```

### 修改

```typescript
let array:Array<number> = [1,2,3,4];
console.log(array)      // [1, 2, 3, 4]

array[0] = 20;          // 修改
console.log(array)      // [20, 2, 3, 4]

array[4] = 5;           // 赋值
console.log(array)      // [20, 2, 3, 4, 5]

array.push(6);          // 添加
console.log(array)      // [20, 2, 3, 4, 5, 6]

array.unshift(8, 0);    // 在第一个位置依次添加
console.log(array);     // [8, 0, 20, 2, 3, 4, 5, 6]
```

### 删除

```typescript
let array:Array<number> = [1,2,3,4];
console.log(array)      // [1, 2, 3, 4]
let popValue = array.pop();     // 弹出
console.log(array)      // [1, 2, 3]
array.splice(0, 1);     // 删除元素（index, deleteCount）
console.log(array)      // [2, 3]
array.shift();          // 删除第一个元素
console.log(array);     // [3]
```

### 合并、断开数组

```typescript
/**
  * Combines two or more arrays.
  * @param items Additional items to add to the end of array1.
  */
concat(...items: T[][]): T[];
/**
  * Combines two or more arrays.
  * @param items Additional items to add to the end of array1.
  */
concat(...items: (T | T[])[]): T[];
/**
 * 该方法返回指定起始位置的一个新的数组
 */
slice(start?: number, end?: number): T[];
let array: Array<number> = [1, 2, 3];
let array2: Array<number> = [4, 5, 6];
let arrayValue = 7;
array = array.concat( array2);
console.log(array)          // [1, 2, 3, 4, 5, 6]
array = array.concat(arrayValue);
console.log(array)          // [1, 2, 3, 4, 5, 6, 7]
let newArray = array.slice(2, 4);
console.log(newArray)      // [3, 4]
```

### 查找数组元素位置

```typescript
/**
  * 返回查找到的第一个元素所在位置
  */
indexOf(searchElement: T, fromIndex?: number): number;
/**
  * 返回反序查找的第一个元素所在位置
  */
lastIndexOf(searchElement: T, fromIndex?: number): number;

let array: Array<string> = ["a","b","c","d","c","a"];
let indexC = array.indexOf("c");
console.log(indexC);            // 2
let lastA = array.lastIndexOf("a");
console.log(lastA);             // 5
```

### 连接数组元素

```typescript
let array: Array<string> = ["a","b","c","d","c","a"];
let result = array.join();
console.log(result);            // a,b,c,d,c,a
result = array.join("+");
console.log(result);            // a+b+c+d+c+a
result = array.join("");
console.log(result);            // abcdca
```

### 排序、反序

```typescript
let array:Array<number> = [3, 2, 1, 8, 7, 0, 4];
console.log(array);             // [3, 2, 1, 8, 7, 0, 4]
array.sort();
console.log(array);             // [0, 1, 2, 3, 4, 7, 8]
array.reverse();
console.log(array);             // [8, 7, 4, 3, 2, 1, 0]
```
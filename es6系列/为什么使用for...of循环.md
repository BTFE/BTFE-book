# 为什么使用for...of循环

> 作者：杨勇

## 简介

ES6 借鉴 C++、Java、C# 和 Python 语言，引入了`for...of`循环，作为遍历所有数据结构的统一的方法。

一个数据结构只要部署了`Symbol.iterator`属性，就被视为具有 iterator 接口，就可以用`for...of`循环遍历它的成员。也就是说，`for...of`循环内部调用的是数据结构的`Symbol.iterator`方法。

`for...of`循环可以使用的范围包括数组、Set 和 Map 结构、某些类似数组的对象（比如arguments对象、DOM NodeList 对象）、Generator 对象，以及字符串。

## Iterator接口

ES6 规定，默认的 `Iterator` 接口部署在数据结构的`Symbol.iterator`属性，或者说，一个数据结构只要具有`Symbol.iterator`属性，就可以认为是“可遍历的”（iterable）

`Symbol.iterator`属性本身是一个函数，就是当前数据结构默认的遍历器生成函数。执行这个函数，就会返回一个遍历器。至于属性名`Symbol.iterator`，它是一个表达式，返回Symbol对象的iterator属性，这是一个预定义好的、类型为 Symbol 的特殊值，所以要放在`方括号`内。

```javascript
const obj = {
  data: [ 'hello', 'world' ],
  [Symbol.iterator]() {
    const self = this;
    let index = 0;
    return {
      next() {
        if (index < self.data.length) {
          return {
            value: self.data[index++],
            done: false
          };
        } else {
          return { value: undefined, done: true };
        }
      }
    };
  }
};

let iter = obj[Symbol.iterator]();

iter.next() // {value: "hello", done: false}
iter.next() // {value: "world", done: false}
iter.next() // {value: undefined, done: true}
```

上面代码中，对象obj是可遍历的（iterable），因为它具有`Symbol.iterator`属性。执行这个属性，会返回一个`遍历器对象`。

> 该对象的根本特征就是具有`next`方法。每次调用`next`方法，都会返回一个代表当前成员的信息对象，具有value和done两个属性。

## 为什么使用for...of？

#### for循环

```javascirpt
for (var index = 0; index < myArray.length; index++) {
  console.log(myArray[index]);
}
```

这种写法比较麻烦。

#### forEach

```javascript
myArray.forEach(function (value) {
  console.log(value);
});
```

这种写法的问题在于，无法中途跳出`forEach`循环，`break`命令或`return`命令都不能奏效。

#### for...in

```javascript
for (var index in myArray) {
  console.log(myArray[index]);
}
```

for...in循环可以遍历**数组**或**对象**的键名，但是它有几个缺点：

1. 数组的键名是数字，但是`for...in`循环是以字符串作为键名“0”、“1”、“2”等等。
2. `for...in`循环不仅遍历数字键名，还会遍历手动添加的其他键，甚至包括**原型链上的键**。
3. 某些情况下，`for...in`循环会以任意顺序遍历键名。

总之，`for...in`循环主要是为遍历对象而设计的，不适用于遍历数组。

#### for...of

```javascript
for (let value of myArray) {
  console.log(value);
}
```

`for...of`循环相比上面几种做法，有一些显著的优点。

1. 有着同`for...in`一样的简洁语法，但是没有`for...in`那些缺点。
2. 不同于`forEach`方法，它可以与`break`、`continue`和`return`配合使用。
3. 提供了遍历所有数据结构的统一操作接口。

> continue意味着调用.next()方法

> 注意：return语句只能出现在函数体内，出现在代码中的其他任何地方造成语法错误！

举个例子：

```javascript
// wrong
let arr = [1, 2, 3]

for(let item of arr) {
    if(item === 2) {
        return item;
        continue;
    }
    console.log(item)
}
// ❌Uncaught SyntaxError: Illegal return statement

// right
let arr = [1, 2, 3]

for(let item of arr) {
    if(item === 2) {
        () => {return item};
        continue;
    }
    console.log(item)
}
// 1 3
```


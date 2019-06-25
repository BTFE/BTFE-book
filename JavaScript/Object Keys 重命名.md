# Object Keys 重命名

![image](https://i.loli.net/2019/06/22/5d0debf1432d278016.jpeg)

## 前情提要

在前后端并行开发的时候，前端按照自己的数据结构进行数据渲染，待后端接口完成后，你会发现数据结构
差异会很大，这时需要进行 `数据的适配` ，那么这就离不开，Object键名的重命名操作。

---

如果你允许数据本身的变化，那么重命名对象的键很容易。

```javascript
obj = { name: 'Bobo' }
obj.somethingElse = obj.name
delete obj.name
```

如果你不想改变数据本身，并且编写逼格高的代码，请继续向下阅读。



## Object Key 重命名

```javascript
renameProp = (
    oldProp,
    newProp,
{ [oldProp]: old, ...others }
) => ({
    [newProp]: old,
    ...others
})
```

假如我们有一个object，`obj`:

```javascript
obj = {
    name: 'YangYong',
    job: 'Front-End Master',
    age: 26
}
```

并且我们想要将 `obj` 的 `name` 改为 `firstName` ，那么我们使用 `renameProp` 函数处理：

```javascript
renameProp('name', 'firstName', obj)
```

> 注意！！！这里使用解构赋值，**oldProps**是匹配的模式，**old**才是变量，真正被赋值的是变量**old**，而不是模式**oldProps**。

## Object Keys 重命名

如果我们想对Obeject的 `多个Key` 进行重命名，我们可以使用：

```javascript
renameKeys = (keysMap, obj) => Object
    .keys(obj)
    .reduce((acc, key) => ({
        ...acc,
        ...{ [keysMap[key] || key]: obj[key] }
    }), {});
```

这个是受 [Ramda Adjunct](https://char0n.github.io/ramda-adjunct/2.6.0/RA.html#.renameKeys)的 `renameKeys` 函数的启发。

- `keysMap`: 新旧key对象形式的键值对。
- `obj`: 原始要改变的对象。

你可以像这样使用它：

```javascript
keysMap = {
    name: 'firstName',
    job: 'passion'
};
obj = {
    name: 'YangYong',
    job: 'Front-End Master'
};
renameKeys(keysMap, obj);
// { firstName: 'YangYong', passion: 'Front-End Master' }
```

## 直接解构的方式

```javascript
obj = {
    name: 'YangYong',
  	look: 'handsome',
    job: 'Front-End Master'
};

let { name: firstName, look: lookLike, ...others } = obj;
obj = { firstName, lookLike, ...others };
// { firstName: 'YangYong', passion: 'Front-End Master' }
```

## 参考

1. [Immutably Rename Object Keys in Javascript](https://medium.com/front-end-weekly/immutably-rename-object-keys-in-javascript-5f6353c7b6dd)

2. [30 Seconds of Code: How to rename multiple object keys in JavaScript](https://medium.com/free-code-camp/30-seconds-of-code-rename-many-object-keys-in-javascript-268f279c7bfa)
# 深入理解Promise执行顺序

> 作者：杨勇

#### 题目一：
```javascript
const p1 = new Promise((resolve, reject) => {
    console.log(1);
    setTimeout(() => {
        resolve();
        console.log(2);
    }, 3000)
})

const p2 = new Promise((resolve, reject) => {
    console.log(3);
    setTimeout(() => {
        resolve();
        console.log(4);
    }, 1000)
})

p1.then(result => {console.log(5)});
p2.then(result => {console.log(6)});
```

#### 题目二：
```javascript
const p1 = new Promise((resolve, reject) => {
    console.log(1);
    setTimeout(() => {
        resolve();
        console.log(2);
    }, 3000)
})

const p2 = new Promise((resolve, reject) => {
    console.log(3);
    setTimeout(() => {
        resolve(p1);
        console.log(4);
    }, 1000)
})

p1.then(result => {console.log(5)});
p2.then(result => {console.log(6)});
```

#### 题目三：
```javascript
const p1 = new Promise((resolve, reject) => {
    console.log(1);
    setTimeout(() => {
        resolve();
        console.log(2);
    }, 3000)
})

const p2 = new Promise((resolve, reject) => {
    console.log(3);
    setTimeout(() => {
        resolve(p1);
        console.log(4);
    }, 1000)
})

const p3 = Promise.resolve();

p1.then(result => console.log(5));
p2.then(result => console.log(6));
p3.then(result => console.log(7));

console.log(8)
```

#### 参考答案
1.  1 3 4 6 2 5
2. 1 3 4 2 5 6
3. 1 3 8 7 4 2 5 6

#### 解析：
- 题目一：

> new Promise内部回调函数在实例化时会立刻执行，setTimeout为异步 **[宏任务](http://blog.yangyong.io/archives/e9baa1a.html#宏任务)** 放入到下次循环队列，promise实例.then的回调函数为异步 **[微任务](http://blog.yangyong.io/archives/e9baa1a.html#微任务)** 同样放入下次循环队列。若一次循环同时存在两种任务，微任务会先于宏任务执行

- 题目二：

> 如果resolve参数是一个promise实例，那么直到该promise实例改变状态，其本身才相应改变。

- 题目三：

> 使用Promise.resolve()返回的实例，其.then的回调函数不会放入下次任务队列，但是会在本次循环末尾执行。
# javascript实现一个带并发限制的异步调度器，保证同时最多运行2个任务

面试题目：javascript实现一个带并发限制的异步调度器，保证同时最多运行2个任务
##### **大佬镇楼：**
![image](https://github.com/BTFE/BTFE-blog/blob/master/img/CAO.jpg?raw=true)
#### 分析题目

* 要点一：最多运行2个任务，需要把其他的任务缓存起来，所以需要一个数组缓存起来
* 要点二：当运行的两个任务执行完毕，如何进行缓存任务的执行，此时需要在任务执行完毕后在次去缓存列表中拿到任务继续执行

```
class Scheduler {
    constructor() {
        this.task = [];
        this.curringRuning = 0;
    }
    add(promiseCreator) {
        return new Promise((resolve, reject) => {
            this.task.push(() => promiseCreator().then(()=>resolve()))
            if(this.curringRuning < 2) this.doTask()
        })
    }
    //  todo
    doTask() {
        if(this.task.length > 0 && this.curringRuning < 2) {
            let runTask = this.task.slice(0, 1)[0]
            this.task = this.task.slice(1)
            this.curringRuning += 1
            runTask().then(() => {
                this.curringRuning -= 1
                this.doTask()
            })
        }    
    }
}
const timeout = (time) => new Promise(resolve => {
    setTimeout(resolve, time)
})
const scheduler = new Scheduler()
const addTask = (time, order) => {
    scheduler.add(() => timeout(time))
        .then(() => console.log(order))
}
addTask(1000, '1');
addTask(500, '2')
addTask(300, '3')
addTask(400, '4')
//  output: 2 3 1 4
```
### 理解Promise

##### 1. 创建promise
   
```
    let p = new Promise() 
    console.log(p) // TypeError: Promise resolver undefined is not a function
```
运行结果如下：
    

##### 2. 参数
  报错信息告诉我们，Promise需要一些参数，这里需要一个函数（我们叫它执行器）作为参数，该函数有两个参数————resolve和reject，这两个参数也是函数（由js引擎提供），我们可以在Promise内部调用，当异步操作成功时，调用resolve，否则reject。

```
    let p =new Promise(function(resolve, reject){ 
        if(/* 异步操作成功 */){
             resolve(data) 
        }else{
             reject(err) 
          } 
      })
```

运行结果如下：

##### 3. State 
  现在我们需要知道一个重要概念，Promise是有“状态”的，分别是pending（等待态）、fulfilled（成功态）、rejected（失败态），pending可以转换为fulfilled或rejected，但fulfilled和rejected不可相互转化。

* resolve/reject 方法
  resolve方法可以将pending转为fulfilled，reject方法可以将pending转为rejected。

* then方法
  通过给Promise示例上的then方法传递两个函数作为参数，可以提供改变状态时的回调，第一个函数是成功的回调，第二个则是失败的回调。

```
    p.then(function(data){ // resolve方法会将参数传进成功的回调 
          console.log(data)
        }, function(err){ // reject方法会将失败的信息传进失败的回调
          console.log(err)  
      })
```

#### demo

```
    let p = new Promise(function(resolve, reject){
        setTimeout(function(){
            let num = Math.random()
            if (num > 0.5) {
                resolve(num)
            }else{
                reject(num)
            }
        }, 1000)
    })
    p.then(function(num){
        console.log('大于0.5的数字：', num)
    },function(num){
        console.log('小于等于0.5的数字', num)
    })
    // 运行第一次：小于等于0.5的数字 0.166162996031475
    // 运行第二次：大于0.5的数字： 0.6591451548308984
    ...
```
在Promise执行器中我们进行了一次异步操作，并在我们觉得合适的时候调用成功或失败的回调函数，并拿到了想要的数据以进行下一步操作

* 链式调用

  除此之外，每一个then方法都会返回一个新的Promise实例（不是原来那个），让then方法支持链式调用，并可以通过返回值将参数传递给下一个then
    
* 实现then的链式调用（难点）
  jquery实现链式调用是return了一个this，但Promise不行,所以Promise的then方法实现链式调用的原理是：返回一个新的Promise

```
    p.then(function(num){
        return num
    },function(num){
        return num
    }).then(function(num){
        console.log('大于0.5的数字：', num)
    },function(num){
        console.log('小于等于0.5的数字', num)
    })
```
* catch方法
  catch方法等同于.then(null, reject)，可以直接指定失败的回调（支持接收上一个then发生的错误）

* Promise.all()
  这可能是个很有用的方法，它可以统一处理多个Promise。
  Promise.all能将多个Promise实例包装成一个Promise实例。
```
    let Promise1 = new Promise(function(resolve, reject){})
    let Promise2 = new Promise(function(resolve, reject){})
    let Promise3 = new Promise(function(resolve, reject){})

    let p = Promise.all([Promise1, Promise2, Promise3])

    p.then(funciton(){
      // 三个都成功则成功
    }, function(){
      // 只要有失败，则失败
    })
```
* Promise.race()
  与all方法类似，也可以讲多个Promise实例包装成一个新的Promise实例。
  不同的是，all时大Promise的状态由多个小Promise共同决定，而race时由第一个转变状态的小Promise的状态决定，第一个是成功态，则转成功态，第一个失败态，则转失败态。
* Promise.resolve()
  可以生成一个成功的Promise
  Promise.resolve('成功')等同于new Promise(function(resolve){resolve('成功')})。

* Promise.reject()
  可以生成一个失败的Promise.
  Promise.reject('出错了')等同于new Promise((resolve, reject) => reject('出错了’))。

### 如何手动实现Promise
内容可见 myPromise.js，还有待完善
           
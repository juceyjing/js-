##### cookie详谈
cookie被用来存储回话的信息，来弥补http的无状态的请求响应。
cookie用来跟踪会话信息，第一次响应设置cookie，以后每次请求都会发送改cookie。

set-cookie响应头
对于一个http请求，服务器可以通过set-cookie响应头设置cookie：
HTTP/1.1 200 ok 
set-cookie:key=val
浏览器通过在请求头中添加cookie字段，携带信息给服务器：
GET /index.html HTTP/1.1
Cookie:key=val

cookie的限制
1.同源策略
2.数量和大小的限制
在操作cookie时，document.cookie属性，需要编解码。

##### 异步处理
现在主流的处理异步的方案主要有：
回调函数(callback)
promise
Generator函数
async/await

    function getData(url,callback){
    setTimeout(()=>{
        var res={
            url:url,
            data:Math.random();
        }
        callback(res)

    },2000)

}

    getData('/page/1?param=123',(res1)=>{
    console.log(res1)
    getData('/page/2?param=${res1.data}',(res2)=>{
        console.log(res2)
        getData('/page/3?param=${res2.data}',(res3)=>{
            console.log(res3)
        })
    })
})
形成回调地狱
使用promise
promise就是解决了回调地狱的问题，为异步编程提供统一接口而提出的。

   

```
先对数据进行封装
 function getDataAsync(url){
    return new promise((resolve,reject)=>{
        setTimeout(()=>{
            var res={
                url:url,
                data:Math.random()
            }
            resolve(res)
        },1000)
    })
	}
```

```
那么请求的代码应该这样写
getDataAsync('/page/1?param=123').then(res1=>{
        console.log(res1)
        return getDataAsync('/page/2?param=${res1.data}')
    }).then(res2=>{
        console.log(res2)
    return getDataAsync('/page/3?param=${res2.data}')
}).then(res3=>{
    console.log(res3);
})
```
then方法返回一个新的promise对象，then方法的链式调用避免了callback的回调地狱。但是不够完美，比如我们需要很多的then语句，每个then还要写一个回调。

缺点：比如后面每一个请求依赖前面所有请求的结果。
async/await
因为使用 async 修饰的方法最终返回一个 Promise， 实际上，async/await 可以看做是使用 Generator 函数处理异步的语法糖

```
    function getData(){
    return new Promise((resolve,reject)=>{
        setTimeout(()=>{
            var res={
                url:url,
                data:Math.random()
            }
            resolve(res)
        },1000)

    })
}
async function getData(){
    var res1=await getDataAsync('/page/1?param=123')
    console.log(res1)
    var res2=await  getDataAsync('/page/2?param=$(res1.data)')
    console.log(res2)
    var res3=await getDataAsync('/page/3?param=$(res2.data)')
}
```
用Generator函数
我们逐步调用遍历器的next()方法，由于每一个next()方法返回值的value属性为一个promise对象，所以对其添加then方法，在then方法里面接着运行next方法移动遍历器指针，知道generator函数运行结束为止。
```
function getData(url){
    return new Promise((resolve,reject)=>{
        setTimeout(()=>{
            var res={
                url:url,
                data:Math.random()
            }
            resolve(res)
        },1000)
    })
}
function * getData(){
    var res1=yield getDataAsync('/page/1?param=123')
    var res2=yield getDataAsync('/page/2?param=${res1.data}')
    var res3=yield getDataAsync('/page/2?param=${res2.data}')
}
var g=getData()
g.next().value.then(res1=>{
    g.next(res1).value.then(res2=>{
        g.next(res2).value.then()=>{
            g.next()
        }
    })
})
```
简单的执行器//generator函数执行器

```
function run(gen){
    var g=gen()
    function next(data){
        var res=g.next(data)
        if(res.done){return res.value}
        res.value.then((data)=>{
            next(data)
        })

    }
    next()
}
```
DOM中的js
在DOM解析时遇到script标签，将停止解析文档，并执行js脚本，如果是外部脚本，必须先下载在解析，这将当值性能问题。
**defer** 可以使用defer属性推迟外部脚本的下载与执行，直到html文档解析完成。
**async**使用async属性异步下载并执行外部js文件
1.不会阻塞DOM的解析与其他资源(如：图片、样式表)的下载
2.如果有多个外部JavaScript脚本拥有 async 属性，那么他们的执行顺序很可能不按照DOM中的顺序执行。先下载完的先执行
3.如果一个 <script> 元素同时存在 defer 和 async ，async 的优先级高
4注意：使用JavaScript动态创建的 <script> 元素，并添加到DOM，那么该脚本将强制按照 async 的规则下载与执行
5.通过 <script> 元素的 onload、onerror、load、error 等事件，可以监听异步下载的JavaScript的下载情况

DOM事件
HTML内联绑定

    <div onclick="alert(1)"></div>
js获取DOM元素添加事件属性：

    document.getElementById('id').onclick=function(){}
addEventListener(type,listener,option);
removeEventListener(type,listener,option);
取消事件的时候，传递的两个参数必须与addEventListener前两个参数完全相同。
这意味着，给一个元素绑定匿名事件处理函数将无法被移除

##### 事件流页面中接收事件的顺序，分为三个阶段
1.事件捕获阶段
2.处于目标阶段
3.事件冒泡阶段

事件冒泡
事件首先由嵌套层次最深的节点接收，然后沿DOM树依次逐级向父节点传播。

事件捕获
不太具体的节点(或嵌套层次最浅的节点，通常是document)应该最先接收事件，然后沿DOM树依次向子代节点传递直到一个具体的子节点

##### 绑定事件的方法
标准方法：
el.addEventListener(eventName,handle,useCapture);
eventName:事件名称
handle:事件函数
useCapture:是否在事件捕获阶段触发，false代表在冒泡阶段触发。
el.removeEventListener(eventName,handle)
描述：移除通过 addEventListener 添加的事件处理函数

#### 如果要移除一个通过 addEventListener 添加的事件处理函数，那么给 removeEventListener 传递的两个参数必须与 addEventListener 的前两个参数完全相同。这意味着，给一个元素绑定匿名事件处理函数将无法被移除
事件对象：属性
event.bubbles
event.cancelable
event.currentTarget：**currentTarget的值始终等于this，即指向事件所绑定到的元素**
event.target:**真正触发事件的元素**
event.defaultPrevented
event.detail
event.eventPhase
event.trusted
方法：
event.preventDefault()//阻止事件的默认行为
只有 event.cancelable 属性为 true 的事件，才能够通过 preventDefault() 方法取消默认行为

五、事件类型及讲解
UI事件：**window上触发**

load  :当页面完全加载完成，包括所有的img js  css <object>内嵌对象
resize:当窗口的大小改变时
scroll：滚动页面时


焦点事件：
event.clientX/Y
event.pageX/Y
event.screenX/Y





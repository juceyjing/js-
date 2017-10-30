##### 蝴蝶树js基础知识点
在for循环中为false的值有：
1.undefined
2.null
3.""--空字符串
4.false---boolean
5.NAN
6.0
**但是字符串“fasle”表示的是true**

for循环三要素：
```javascript
	for(var i=0;i<len;i++){
		//...
	}
```
for循环的三要素：
1.初始化从句  var i=0
2.条件限定    i<len
3.增量从句 i++
最重要的是中间的条件限定：
普通的for循环针对数组比较多，for-in 循环遍历对象的属性比较多。

    for(myvar in obj){
		if(obj.hasOwnproperty(myvar)){
		//---
		}
	}

do-while 循环

    do{
	i+=1
	}while(i<=5)
do-while语句会先执行do中的代码，然后检查condition的真假；
此举意味着代码会至少执行一次。

try-catch语句

    try{
    }catch(err){
    }
catch从句会重新定义一个新的变量err来接受抛出的异常。

throw 
throw抛错之后，会自动跳到catch页面，默认抛错。
throw(err)  err 通常是一个对象字面量，中间包含name和message。
异常捕获器可以捕获信息决定来干什么。

return:会导致函数提前返回，可以指定返回值，没有返回值的情况下返回undefined;

typeof运算符：
产生的值有：
1.object
2.undefined
3.boolean
4.number
5.string
6.function
如果运算符后面是null或者是arr，运算结果是object,这结果是不对的。
##### 所以用typeof来检测对象的类型是不靠谱的，object可能是null或者array或者object。

| Col1      |     Col2 |   Col3   |Col4|Col5
| :-------- | --------:| :------: |
| * ,/,%   |   +,- |  >=,<=,<,>  |!====|&& 

##### 函数
函数有一个可选的名字，可以递归的调用自己。
函数的参数列表，在实际参数传入后进行参数初始化。

js的基本数据类型
undefined null string boolean number
存在栈中
其他的值都是object，存的地址，堆的形式存在。
基本的数据类型不可变。
js中的对象是可变的键控集合。
#####  对象
对象是属性的容器，其中的每个属性都有名字和值。
属性的名字是可以包括空字符串在内的任意字符串，属性值是可以除过undefined以外的任意值。

js包含一种原型链的特性，允许对象继承另一个对象的属性。

    object.create=function(o){
		var F=function(){},
		F.prototype=o;
		return new F()
}

原型链接只会在检索属性值时才会被自动调用到。

##### 当我们尝试获取对象的某个属性值，在自身没有这个属性值的话，会寻着原型链向上一级查找，
查找完整个原型链，没有的话报undefined,这个过程称为委托。

原型关系是一种动态的关系，在原型链上添加一个属性，该属性会立马变成共有属性，同步到其他子类公用。


##### 心得：
原型对象(prototype)是动态存在的一个对象，原型上的属性可以修改和删除，但是原型对象的每一次改变，牵动着每一个基于他存在的实例对象。

# 理解JavaScript中的闭包

### 1、从JavaScript的作用域开始谈起

JavaScript与C、Java等语言在作用域方面有着显著的差异。在Java等语言中，作用域由大括号界定，而JavaScript中并非如此。让我们看看下面两段代码：

```javascript
for(var i = 0; i < 10; i++){};
console.log(i);
```

```java
for(int i = 0; i < 10; i++){};
System.out.println(i);
```

两段代码在语义上大体一致：第一行开启了一个空的for循环，第二行尝试将for循环定义的变量i输出。结果是JavaScript代码段中的i能够被成功输出，而Java代码段中的第二行将会报错。这也正好印证了我们之前的观点：<u>JavaScript中的作用域并非由大括号界定。</u>那既然如此，JavaScript中的作用域是由什么决定的呢？——答案是：函数。**<u>在一个函数内定义的变量将只在这个函数内有效，而定义在所有函数之外的变量则是全局变量</u>**。

```javascript
function f() {
   console.log(y);
   var x = "hello,world";
}
console.log(x);
```

若是像上面这样企图直接跨函数域使用变量，JavaScript就会不干了，直接报错。而说起JavaScript的函数作用域，在这里再提及一个有趣的现象（虽然它与闭包并没有什么直接关系），这个现象在JS中被称为“变量提升”；大体意思是：**<u>我们在一个函数内定义的所有变量，在执行函数时都会被提升到函数的开始处</u>**。比如：

```javascript
var x = "hello,y";
(
    function () {
    	var y = "hello,x";
    	console.log(x);
    	var x = "hello!"	
	}
)(); //函数定义后马上执行
```

我们会发现`console.log(x);`不会输出任何东西，这正是“变量提升”在从中作梗，以上的代码段在变量提升后其实类似于如下的代码：

```javascript
var x = "hello,y";
(
    function () {
   		var y = "hello,x";
     	var x;
    	console.log(x);
    	var x = "hello!"	
	}
)(); //函数定义后马上执行
```

变量x被提升到函数开始处，只不过它尚未执行赋值语句，值仍为undefined，但它却覆盖了全局变量x，从而让`console.log(x)`无法输出我们想要输出的全局变量。

### 2、突破函数作用域——闭包！

前文我们讲了JS中的变量都只在函数作用域内有效。而函数本身作为JS对象的一员自然也无法例外，这就意味着如果存在嵌套的函数：例如函数C在函数B内部定义，函数B在函数A内部定义，在正常情况下我们将无法在函数A内调用函数C。但**<u>如果我们能够通过某种手段，让函数C能在函数B的外部函数（比如函数A）中被调用，我们便称这种情况为闭包。</u>**这时候也许我们会疑惑，为什么要苦心孤诣地在函数B的外部去调用函数C呢？答案是我们要通过这种手段**<u>让函数B中的变量得以被它的外部函数访问，以及延长函数B中变量的生命周期。</u>**

听起来比较抽象，让我们作个比喻：函数就像一座座昏暗潮湿的地牢，监押着一组组变量，而如果变量想逃脱这座地牢呢？它们或许需要挖一个地道，而闭包，正是送它们前往广阔牢外世界的地道。也许有人会说：直接调用某个函数并将想要的局部变量返回不是也能让函数中的变量突破函数作用域吗？但这种做法下，函数返回的只是局部变量的副本，真正的局部变量在函数调用完毕后便随着函数一起消失了。而<u>闭包能让你真真正正地去访问函数作用域中鲜活存在的局部变量，而非那些拙劣的复制品！</u>而访问函数体中“鲜活”的变量的前提是这些变量不会被销毁，因此闭包的存在将使得这些变量的存活时间得以延长。概括起来说，闭包是各个函数作用域之间沟通的桥梁，也是函数作用域中局部变量得以续命的呼吸机。

说了这么多，闭包是如何实现的呢？我们还记得<u>闭包的基本概念是让函数在定义它的函数作用域的外部能被调用。</u>而做到这点大体有两种方式：**<u>一是将函数体内的函数变量提升为全局变量，二是将函数体内的函数作为返回值传递出去。</u>**

接下来对两种方式各展示一个简单的示例：

```JavaScript
var x;
(function () {
    var localVarible = "hello,world";
    x = function () {
        return localVarible;
    }  //将内层匿名函数赋值给全局变量
}());  //匿名函数即时调用
var y = x();
console.log(y);
```

以上代码执行完毕后，我们将看到匿名函数中的localVarible变量的值被打印了出来。原因是我们调用了匿名函数后，原先定义的全局变量得到了内层匿名函数的引用。这将使得外层匿名函数在调用完毕后它的作用域和作用域内的局部变量依然存活。（外层函数：我的一个宝贝儿子还被全局变量挟持着呢，我怎么能带着其他儿子女儿撤退呢？一定要把它救出来以后再一起撤退！😂）

```javascript
var x = (function() {
    		var localVarible = "hello,world";
    		return function() {
                return localVarible;
           	}
		}());
var y = x();
console.log(y);
```

这段代码在执行完毕后，外层匿名函数作用域中的localVarible变量的值也会被打印出来。原因是外层匿名函数执行后返回了一个内层函数，而该内层函数被全局变量所持有，该内层函数有访问localVarible变量的权限，从而使得全局变量也能够访问localVarible变量。(外层函数：x娶了我的女儿，在你们没有离婚前，我家的门你随便进！她的兄弟姐妹你随意使唤！🤭)

### 3、牛刀小试——闭包示例展示

在厘清了闭包的概念，闭包的作用以及如何实现闭包之后，我们来看几个应用了闭包的代码示例，从而进一步熟悉闭包。

```javascript
function iterator(x) {
    var i = 0;
	function ite() {
		return x[i++];
	}
	return ite;
};
var next = iterator([1,2,3,4,5,6]);
for(var x = 0; x < 6; x++)console.log(next());
```

以上代码通过闭包实现了类似迭代器的功能，我们只需要重复调用next函数便可以逐一遍历数组中的每个元素。

```javascript
var getter,setter;
(function f() {
    var name;
    getter = function() {
        return name;
    }
    setter = function(n) {
        name = n;
    }
}());
setter("Jalinyiel");
console.log(getter());
```

以上代码通过将函数f中的匿名函数提升为全局变量，让我们得以在函数f外部对f中的局部变量进行访问和修改。

最后再看一个令人疑惑的案例：

```javascript
var arr = (function() {
	var arr = [],i;
	for(i = 0; i < 3; i++) {
		arr[i] = function() {
			return i;
		}
	}
	return arr;
}());
console.log(arr[0]());
console.log(arr[1]());
console.log(arr[2]());
```

如果没有真正理解JS的函数作用域和闭包的概念，很有可能会觉得三条控制语句应该分别输出：0，1，2。但实际情况是三条控制语句都输出了3。这是怎么回事呢？我们在前文说过，闭包能让我们访问函数作用域中“鲜活”的变量，而实际上当全局数组arr完成赋值后，匿名函数内的局部变量值i已经是3，for循环中的三次赋值在本质上是没有区别的，只是分别让arr的第0个元素、第1个元素和第2个元素获得了访问局部变量i的权力。当局部数组arr被返回到函数外部时，便形成了闭包，从而使得i的值在匿名函数调用完毕后不会马上被回收。但我们在后续利用全局数组arr中的任何一个元素对i进行访问时，i的值显然都是3。

### 4、参考资料

- 《JavaScript面向对象编程指南》

- [JS函数作用域及作用域链理解](<https://www.cnblogs.com/mrzl/p/4415149.html>)

- [全面理解Javascript闭包和闭包的几种写法及用途](https://www.cnblogs.com/yunfeifei/p/4019504.html)

- [闭包是什么？用处如何?](<https://www.jianshu.com/p/87762b8864a8>)
- [学习Javascript闭包(Closure)](<http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html>)


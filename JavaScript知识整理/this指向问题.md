## 背景
<font style="color:black;">this是JavaScript中的一个关键字，但是又一个相对比较特别的关键字，不像function、var、for、if这些关键字一样，可以很清楚的搞清楚它到底是如何使用的。</font>

<font style="color:black;">this会在执行上下文中绑定一个对象，但是是根据什么条件绑定的呢？在不同的执行条件下会绑定不同的对象，这也是让人捉摸不定的地方。</font>

## <font style="color:rgb(0, 0, 0);">为什么使用this</font>
> 💡 Tips：<font style="color:rgb(0, 0, 0);">在常见的编程语言中，几乎都有this这个关键字（Objective-C中使用的是self），但是JavaScript中的this和常见的面向对象语言中的this不太一样：</font>
>

+ <font style="color:rgb(1, 1, 1);">常见面向对象的编程语言中，比如Java、C++、Swift、Dart等等一系列语言中，this通常只会出现在</font>`<font style="color:rgb(239, 112, 96);">类的方法</font>`<font style="color:rgb(1, 1, 1);">中。</font>
+ <font style="color:rgb(1, 1, 1);">也就是你需要有一个类，类中的方法（特别是实例方法）中，this代表的是当前调用对象。</font>
+ <font style="color:rgb(1, 1, 1);">但是JavaScript中的this更加灵活，无论是它出现的位置还是它代表的含义。</font>

<font style="color:rgb(0, 0, 0);">使用this有什么意义呢？下面的代码中，我们通过对象字面量创建出来一个对象，当我们调用对象的方法时，希望将对象的名称一起进行打印。</font>

<font style="color:rgb(0, 0, 0);">如果没有this，那么我们的代码会是下面的写法：</font>

+ <font style="color:rgb(1, 1, 1);">在方法中，为了能够获取到name名称，必须通过obj的引用（变量名称）来获取。</font>
+ <font style="color:rgb(1, 1, 1);">但是这样做有一个很大的弊端：如果我将obj的名称换成了info，那么所有的方法中的obj都需要换成info。</font>

```javascript
let obj = {
  name: "why",
  running: function() {
    console.log(obj.name + " running");
  },
  eating: function() {
    console.log(obj.name + " eating");
  },
  studying: function() {
    console.log(obj.name + " studying");
  }
}
```

<font style="color:rgb(0, 0, 0);">事实上，上面的代码，在实际开发中，我们都会使用this来进行优化：</font>

+ <font style="color:rgb(1, 1, 1);">当我们通过obj去调用running、eating、studying这些方法时，this就是指向的obj对象</font>
+ <font style="color:rgb(0, 0, 0);">所以我们会发现，在某些函数或者方法的编写中，this可以让我们更加便捷的方式来引用对象，在进行一些API设计时，代码更加的简洁和易于复用。</font>

```javascript
let obj = {
  name: "why",
  running: function() {
    console.log(this.name + " running");
  },
  eating: function() {
    console.log(this.name + " eating");
  },
  studying: function() {
    console.log(this.name + " studying");
  }
}
```

## <font style="color:rgb(0, 0, 0);">this指向什么</font>
<font style="color:rgb(0, 0, 0);">我们先说一个最简单的，this在全局作用域下指向什么？</font>

+ <font style="color:rgb(1, 1, 1);">这个问题非常容易回答，在浏览器中测试就是指向window</font>
+ <font style="color:rgb(1, 1, 1);">所以，在全局作用域下，我们可以认为this就是指向的window</font>

```javascript
console.log(this); // window

let name = "why";
console.log(this.name); // why
console.log(window.name); // why
```

<font style="color:rgb(0, 0, 0);">但是，开发中很少直接在全局作用域下去使用this，通常都是在</font>**<font style="color:rgb(0, 0, 0);">函数中使用</font>**<font style="color:rgb(0, 0, 0);">。</font>

<font style="color:rgb(0, 0, 0);">所有的函数在被调用时，都会创建一个执行上下文：</font>

+ <font style="color:rgb(1, 1, 1);">这个上下文中记录着函数的调用栈、函数的调用方式、传入的参数信息等；</font>
+ <font style="color:rgb(1, 1, 1);">this也是其中的一个属性；</font>

<font style="color:rgb(0, 0, 0);">我们先来看一个让人困惑的问题：</font>

+ <font style="color:rgb(1, 1, 1);">定义一个函数，我们采用三种不同的方式对它进行调用，它产生了三种不同的结果</font>

```javascript
// 定义一个函数
function foo() {
  console.log(this);
}

// 1.调用方式一: 直接调用
foo(); // window

// 2.调用方式二: 将foo放到一个对象中,再调用
let obj = {
  name: "why",
  foo: foo
}
obj.foo() // obj对象

// 3.调用方式三: 通过call/apply调用
foo.call("abc"); // String {"abc"}对象
```

由上面可以得出结论

+ <font style="color:rgb(1, 1, 1);">1.函数在调用时，JavaScript会默认给this绑定一个值；</font>
+ <font style="color:rgb(1, 1, 1);">2.this的绑定和定义的位置（编写的位置）没有关系；</font>
+ <font style="color:rgb(1, 1, 1);">3.this的绑定和调用方式以及调用的位置有关系；</font>
+ <font style="color:rgb(1, 1, 1);">4.this是在运行时被绑定的；</font>

## this的绑定规则
### <font style="color:rgb(0, 0, 0);">默认绑定</font>
+ <font style="color:rgb(1, 1, 1);">独立的函数调用我们可以理解成函数没有被绑定到某个对象上进行调用；</font>

**<font style="color:rgb(0, 0, 0);">案例一：普通函数调用</font>**

+ <font style="color:rgb(1, 1, 1);">该函数直接被调用，并没有进行任何的对象关联；</font>
+ <font style="color:rgb(1, 1, 1);">这种独立的函数调用会使用默认绑定，通常默认绑定时，函数中的this指向全局对象（window）；</font>

```javascript
function foo() {
  console.log(this); // window
}

foo();
```

**<font style="color:rgb(0, 0, 0);">案例二：函数调用链（一个函数又调用另外一个函数）</font>**

+ <font style="color:rgb(1, 1, 1);">所有的函数调用都没有被绑定到某个对象上；</font>

```javascript
// 2.案例二:
function test1() {
  console.log(this); // window
  test2();
}

function test2() {
  console.log(this); // window
  test3()
}

function test3() {
  console.log(this); // window
}
test1();
```

**<font style="color:rgb(0, 0, 0);">案例三：将函数作为参数，传入到另一个函数中</font>**

```javascript
function foo(func) {
  func()
}

function bar() {
  console.log(this); // window
}

foo(bar);
```

<font style="color:rgb(0, 0, 0);">我们对案例进行一些修改，考虑一下打印结果是否会发生变化：</font>

+ <font style="color:rgb(1, 1, 1);">这里的结果依然是window，为什么呢？</font>
+ <font style="color:rgb(1, 1, 1);">原因非常简单，在真正函数调用的位置，并没有进行任何的对象绑定，只是一个独立函数的调用；</font>

```javascript
function foo(func) {
  func()
}

var obj = {
  name: "why",
  bar: function() {
    console.log(this); // window
  }
}

foo(obj.bar);
```

### <font style="color:rgb(0, 0, 0);">隐式绑定</font>
<font style="color:rgb(0, 0, 0);">另外一种比较常见的调用方式是通过某个对象进行调用的：</font>

+ <font style="color:rgb(1, 1, 1);">也就是它的调用位置中，是通过某个对象发起的函数调用。</font>

**<font style="color:rgb(0, 0, 0);">案例一：通过对象调用函数</font>**

+ <font style="color:rgb(1, 1, 1);">foo的调用位置是obj.foo()方式进行调用的</font>
+ <font style="color:rgb(1, 1, 1);">那么foo调用时this会隐式的被绑定到obj对象上</font>

```javascript
function foo() {
  console.log(this); // obj对象
}

var obj = {
  name: "why",
  foo: foo
}

obj.foo();
```

**<font style="color:rgb(0, 0, 0);">案例二：案例一的变化</font>**

+ <font style="color:rgb(1, 1, 1);">我们通过obj2又引用了obj1对象，再通过obj1对象调用foo函数；</font>
+ <font style="color:rgb(1, 1, 1);">那么foo调用的位置上其实还是obj1被绑定了this；</font>

```javascript
function foo() {
  console.log(this); // obj对象
}

var obj1 = {
  name: "obj1",
  foo: foo
}

let obj2 = {
  name: "obj2",
  obj1: obj1
}

obj2.obj1.foo();
```

**<font style="color:rgb(0, 0, 0);">案例三：隐式丢失</font>**

+ <font style="color:rgb(1, 1, 1);">结果最终是window，为什么是window呢？</font>
+ <font style="color:rgb(1, 1, 1);">因为foo最终被调用的位置是bar，而bar在进行调用时没有绑定任何的对象，也就没有形成隐式绑定；</font>
+ <font style="color:rgb(1, 1, 1);">相当于是一种默认绑定；</font>

```javascript
function foo() {
  console.log(this);
}

let obj1 = {
  name: "obj1",
  foo: foo
}

// 讲obj1的foo赋值给bar
var bar = obj1.foo;
bar();
```

### <font style="color:rgb(0, 0, 0);">显示绑定</font>
<font style="color:rgb(0, 0, 0);">隐式绑定有一个前提条件：</font>

+ <font style="color:rgb(1, 1, 1);">必须在调用的</font>`<font style="color:rgb(239, 112, 96);">对象内部</font>`<font style="color:rgb(1, 1, 1);">有一个对函数的引用（比如一个属性）；</font>
+ <font style="color:rgb(1, 1, 1);">如果没有这样的引用，在进行调用时，会报找不到该函数的错误；</font>
+ <font style="color:rgb(1, 1, 1);">正是通过这个引用，间接的将this绑定到了这个对象上；</font>

<font style="color:rgb(0, 0, 0);">如果我们不希望在</font><font style="color:rgb(0, 0, 0);"> </font>**<font style="color:rgb(0, 0, 0);">对象内部</font>**<font style="color:rgb(0, 0, 0);"> </font><font style="color:rgb(0, 0, 0);">包含这个函数的引用，同时又希望在这个对象上进行强制调用，该怎么做呢？</font>

+ <font style="color:rgb(1, 1, 1);">JavaScript所有的函数都可以使用call和apply方法（这个和Prototype有关）。</font>
    - <font style="color:rgb(1, 1, 1);">它们两个的区别这里不再展开；</font>
    - <font style="color:rgb(1, 1, 1);">其实非常简单，第一个参数是相同的，后面的参数，apply为数组，call为参数列表；</font>
+ <font style="color:rgb(1, 1, 1);">这两个函数的第一个参数都要求是一个对象，这个对象的作用是什么呢？就是给this准备的。</font>
+ <font style="color:rgb(1, 1, 1);">在调用这个函数时，会将this绑定到这个传入的对象上。</font>

<font style="color:rgb(0, 0, 0);">因为上面的过程，我们明确的绑定了this指向的对象，所以称之为 </font>**<font style="color:rgb(0, 0, 0);">显示绑定</font>**<font style="color:rgb(0, 0, 0);">。</font>

#### <font style="color:rgb(0, 0, 0);">call、apply</font>
**<font style="color:rgb(0, 0, 0);">通过call或者apply绑定this对象</font>**

+ <font style="color:rgb(1, 1, 1);">显示绑定后，this就会明确的指向绑定的对象</font>

```javascript
function foo() {
  console.log(this);
}

foo.call(window); // window
foo.call({name: "why"}); // {name: "why"}
foo.call(123); // Number对象,存放时123
```

#### <font style="color:rgb(0, 0, 0);">bind函数</font>
**<font style="color:rgb(0, 0, 0);">如果我们希望一个函数总是显示的绑定到一个对象上，可以怎么做呢？</font>**

<font style="color:rgb(0, 0, 0);">方案一：自己手写一个辅助函数（了解）</font>

+ <font style="color:rgb(1, 1, 1);">我们手动写了一个bind的辅助函数</font>
+ <font style="color:rgb(1, 1, 1);">这个辅助函数的目的是在执行foo时，总是让它的this绑定到obj对象上</font>

```javascript
function foo() {
  console.log(this);
}

let obj = {
  name: "why"
}

function bind(func, obj) {
  return function() {
    return func.apply(obj, arguments);
  }
}

let bar = bind(foo, obj);

bar(); // obj对象
bar(); // obj对象
bar(); // obj对象
```

<font style="color:rgb(0, 0, 0);">方案二：使用Function.prototype.bind</font>

```javascript
function foo() {
  console.log(this);
}

let obj = {
  name: "why"
}

var bar = foo.bind(obj);

bar(); // obj对象
bar(); // obj对象
bar(); // obj对象
```

#### <font style="color:rgb(0, 0, 0);">内置函数</font>
<font style="color:rgb(0, 0, 0);">有些时候，我们会调用一些JavaScript的内置函数，或者一些第三方库中的内置函数。</font>

+ <font style="color:rgb(1, 1, 1);">这些内置函数会要求我们传入另外一个函数；</font>
+ <font style="color:rgb(1, 1, 1);">我们自己并不会显示的调用这些函数，而且JavaScript内部或者第三方库内部会帮助我们执行；</font>
+ <font style="color:rgb(1, 1, 1);">这些函数中的this又是如何绑定的呢？</font>

**<font style="color:rgb(0, 0, 0);">案例一：setTimeout</font>**

+ <font style="color:rgb(1, 1, 1);">setTimeout中会传入一个函数，这个函数中的this通常是window</font>

```javascript
setTimeout(function() {
  console.log(this); // window
}, 1000);
```

<font style="color:rgb(0, 0, 0);">为什么这里是window呢？</font>

+ <font style="color:rgb(1, 1, 1);">这个和setTimeout源码的内部调用有关；</font>
+ <font style="color:rgb(1, 1, 1);">setTimeout内部是通过apply进行绑定的this对象，并且绑定的是全局对象；</font>

**<font style="color:rgb(0, 0, 0);">案例二：数组的forEach</font>**

<font style="color:rgb(0, 0, 0);">数组有一个高阶函数forEach，用于函数的遍历：</font>

+ <font style="color:rgb(1, 1, 1);">在forEach中传入的函数打印的也是Window对象；</font>
+ <font style="color:rgb(1, 1, 1);">这是因为默认情况下传入的函数是自动调用函数（默认绑定）；</font>

```javascript
let names = ["abc", "cba", "nba"];
names.forEach(function(item) {
  console.log(this); // 三次window
});
```

<font style="color:rgb(0, 0, 0);">我们是否可以改变该函数的this指向呢？</font>

```javascript
let names = ["abc", "cba", "nba"];
let obj = {name: "why"};
names.forEach(function(item) {
  console.log(this); // 三次obj对象
}, obj);
```

**<font style="color:rgb(0, 0, 0);">案例三：div的点击</font>**

<font style="color:rgb(0, 0, 0);">如果我们有一个div元素：</font>

+ <font style="color:rgb(1, 1, 1);">注意：省略了部分代码</font>

```javascript
  <style>
    .box {
      width: 200px;
      height: 200px;
      background-color: red;
    }
  </style>

  <div class="box"></div>
```

<font style="color:rgb(0, 0, 0);">获取元素节点，并且监听点击：</font>

+ <font style="color:rgb(1, 1, 1);">在点击事件的回调中，this指向谁呢？box对象；</font>
+ <font style="color:rgb(1, 1, 1);">这是因为在发生点击时，执行传入的回调函数被调用时，会将box对象绑定到该函数中；</font>

```javascript
let box = document.querySelector(".box");
box.onclick = function() {
  console.log(this); // box对象
}
```

<font style="color:rgb(0, 0, 0);">所以传入到内置函数的回调函数this如何确定呢？</font>

+ <font style="color:rgb(1, 1, 1);">某些内置的函数，我们很难确定它内部是如何调用传入的回调函数；</font>
+ <font style="color:rgb(1, 1, 1);">一方面可以通过分析源码来确定，另一方面我们可以通过经验（见多识广）来确定；</font>
+ <font style="color:rgb(1, 1, 1);">但是无论如何，通常都是我们之前讲过的规则来确定的；</font>

#### <font style="color:rgb(0, 0, 0);">new绑定</font>
<font style="color:rgb(0, 0, 0);">JavaScript中的函数可以当做一个类的构造函数来使用，也就是使用new关键字。</font>

<font style="color:rgb(0, 0, 0);">使用new关键字来调用函数时，会执行如下的操作：</font>

+ <font style="color:rgb(1, 1, 1);">1.创建一个全新的对象；</font>
+ <font style="color:rgb(1, 1, 1);">2.这个新对象会被执行Prototype连接；</font>
+ <font style="color:rgb(1, 1, 1);">3.这个新对象会绑定到函数调用的this上（this的绑定在这个步骤完成）；</font>
+ <font style="color:rgb(1, 1, 1);">4.如果函数没有返回其他对象，表达式会返回这个新对象；</font>

```javascript
// 创建Person
function Person(name) {
  console.log(this); // Person {}
  this.name = name; // Person {name: "why"}
}

let p = new Person("why");
console.log(p);
```

#### <font style="color:rgb(0, 0, 0);"> 规则优先级</font>
<font style="color:rgb(0, 0, 0);">学习了四条规则，接下来开发中我们只需要去查找函数的调用应用了哪条规则即可，但是如果一个函数调用位置应用了多条规则，优先级谁更高呢？</font>

**<font style="color:rgb(0, 0, 0);">1.默认规则的优先级最低</font>**

<font style="color:rgb(0, 0, 0);">毫无疑问，默认规则的优先级是最低的，因为存在其他规则时，就会通过其他规则的方式来绑定this</font>

**<font style="color:rgb(0, 0, 0);">2.显示绑定优先级高于隐式绑定</font>**

<font style="color:rgb(0, 0, 0);">显示绑定和隐式绑定哪一个优先级更高呢？这个我们可以测试一下：</font>

+ <font style="color:rgb(1, 1, 1);">结果是obj2，说明是显示绑定生效了</font>

```javascript
function foo() {
  console.log(this);
}

let obj1 = {
  name: "obj1",
  foo: foo
}

let obj2 = {
  name: "obj2",
  foo: foo
}

// 隐式绑定
obj1.foo(); // obj1
obj2.foo(); // obj2

// 隐式绑定和显示绑定同时存在
obj1.foo.call(obj2); // obj2, 说明显式绑定优先级更高
```

**<font style="color:rgb(0, 0, 0);">3.new绑定优先级高于隐式绑定</font>**

+ <font style="color:rgb(1, 1, 1);">结果是foo，说明是new绑定生效了</font>

```javascript
function foo() {
  console.log(this);
}

let obj = {
  name: "why",
  foo: foo
}

new obj.foo(); // foo对象, 说明new绑定优先级更高
```

**<font style="color:rgb(0, 0, 0);">4.new绑定优先级高于bind</font>**

<font style="color:rgb(0, 0, 0);">new绑定和call、apply是不允许同时使用的，所以不存在谁的优先级更高</font>

```javascript
function foo() {
  console.log(this);
}

let obj = {
  name: "obj"
}

let foo = new foo.call(obj);
```

<font style="color:rgb(0, 0, 0);">优先级总结：</font>

+ <font style="color:rgb(1, 1, 1);">new绑定 > 显示绑定（bind）> 隐式绑定 > 默认绑定</font>

## 其他规则
> 💡 Tips：游戏和小说中总有设定为跳出三界外不在五行中的
>

### <font style="color:rgb(0, 0, 0);">忽略显示绑定</font>
<font style="color:rgb(0, 0, 0);">如果在显示绑定中，我们传入一个null或者undefined，那么这个显示绑定会被忽略，使用默认规则：</font>

```javascript
function foo() {
  console.log(this);
}

let obj = {
  name: "why"
}

foo.call(obj); // obj对象
foo.call(null); // window
foo.call(undefined); // window

let bar = foo.bind(null);
bar(); // window
```

### <font style="color:rgb(0, 0, 0);">间接函数引用</font>
<font style="color:rgb(0, 0, 0);">另外一种情况，创建一个函数的</font><font style="color:rgb(0, 0, 0);"> </font>`<font style="color:rgb(239, 112, 96);">间接引用</font>`<font style="color:rgb(0, 0, 0);">，这种情况使用默认绑定规则。</font>

<font style="color:rgb(0, 0, 0);">我们先来看下面的案例结果是什么？</font>

+ <font style="color:rgb(1, 1, 1);">(num2 = num1)的结果是num1的值；</font>

```javascript
let num1 = 100;
let num2 = 0;
let result = (num2 = num1);
console.log(result); // 100
```

<font style="color:rgb(0, 0, 0);">我们来下面的函数赋值结果：</font>

+ <font style="color:rgb(1, 1, 1);">赋值(obj2.foo = obj1.foo)的结果是foo函数；</font>
+ <font style="color:rgb(1, 1, 1);">foo函数被直接调用，那么是默认绑定；</font>

```javascript
function foo() {
  console.log(this);
}

let obj1 = {
  name: "obj1",
  foo: foo
}; 

let obj2 = {
  name: "obj2"
}

obj1.foo(); // obj1对象
(obj2.foo = obj1.foo)();  // window
```

### <font style="color:rgb(0, 0, 0);">ES6箭头函数</font>
<font style="color:rgb(0, 0, 0);">在ES6中新增一个非常好用的函数类型：箭头函数</font>

+ <font style="color:rgb(1, 1, 1);">这里不再具体介绍箭头函数的用法，可以自行学习。</font>

<font style="color:rgb(0, 0, 0);">箭头函数不使用this的四种标准规则（也就是不绑定this），而是根据外层作用域来决定this。</font>

<font style="color:rgb(0, 0, 0);">我们来看一个模拟网络请求的案例：</font>

+ <font style="color:rgb(1, 1, 1);">这里我使用setTimeout来模拟网络请求，请求到数据后如何可以存放到data中呢？</font>
+ <font style="color:rgb(1, 1, 1);">我们需要拿到obj对象，设置data；</font>
+ <font style="color:rgb(1, 1, 1);">但是直接拿到的this是window，我们需要在外层定义：</font>`<font style="color:rgb(239, 112, 96);">var _this = this</font>`
+ <font style="color:rgb(1, 1, 1);">在setTimeout的回调函数中使用_this就代表了obj对象</font>

```javascript
var obj = {
  data: [],
  getData: function() {
    var _this = this;
    setTimeout(function() {
      // 模拟获取到的数据
      var res = ["abc", "cba", "nba"];
      _this.data.push(...res);
    }, 1000);
  }
}

obj.getData();
```

<font style="color:rgb(0, 0, 0);">上面的代码在ES6之前是我们最常用的方式，从ES6开始，我们会使用箭头函数：</font>

+ <font style="color:rgb(1, 1, 1);">为什么在setTimeout的回调函数中可以直接使用this呢？</font>
+ <font style="color:rgb(1, 1, 1);">因为箭头函数并不绑定this对象，那么this引用就会从上层作用域中找到对应的this</font>

```javascript
var obj = {
  data: [],
  getData: function() {
    setTimeout(() => {
      // 模拟获取到的数据
      var res = ["abc", "cba", "nba"];
      this.data.push(...res);
    }, 1000);
  }
}

obj.getData();
```

<font style="color:rgb(0, 0, 0);">思考：如果getData也是一个箭头函数，那么setTimeout中的回调函数中的this指向谁呢？</font>

+ <font style="color:rgb(1, 1, 1);">答案是window；</font>
+ <font style="color:rgb(1, 1, 1);">依然是不断的从上层作用域找，那么找到了全局作用域；</font>
+ <font style="color:rgb(1, 1, 1);">在全局作用域内，this代表的就是window</font>



## 文本绘图
> 💡 Tips：输入`/文本绘图`点击上方工具栏![](https://cdn.nlark.com/yuque/0/2022/png/519985/1646896088287-c8e7ef6c-2748-40d7-b53f-223b99fc5be5.png)，选择「文本绘图」、插入文本绘图卡片。  
支持 [plantuml](https://plantuml.com/)、[mermaid](https://mermaid-js.github.io/mermaid/#/) 等多种格式，点击`预览`可看到图形。具体代码样式见[说明文档](https://www.yuque.com/yuque/gpvawt/gantt)。
>








## React类组件化渲染原理
> 💡 Tips：root.render(),函数传入一个组件或者HTML元素的话，react就会帮我们渲染。react中如何封装组件
>

+ 定义一个类，该类继承自React.Component
+ 实现当前组件的render函数render中返回的jsx内容就是React帮我们渲染的内容

## 数据定义的位置
> 💡 Tips：组件中的数据分为：参与界面更新的数据：数据变化更新渲染内容、不参与界面更细的数据：数据变化渲染内容不必更新
>

+ 参与界面更新的数据：在构造函数中 this.state={'定义的数据'}，定义完成后通过调用this.setState(),重新设置数据更新界面；
+ 不参与界面更细的数据：放在this 上，或者函数内部都可以；

## 事件的绑定
> 💡 Tips：在react 类组件中，如果直接给组件定义一个实例方法并且给html 元素或者组件绑定该方法，由于js的this 指向问题会报错，原因是由于React内部实现了类似于将函数绑定给方法后实现了一个全局调用的方式，由于用了bable插件在全局严格模式下 this 指向了undefined。
>
> 正常的操作DOM 时候，监听函数绑定的this对象是节点对象（比如button）
>
> React并不是直接渲染成真实DOM,对事件的监听是一个语法糖，本质是React的Element对象
>
> 在事件监听触发时候，react在执行函数时候并没有帮开发人员绑定this,所以是undefined
>

+  事件绑定中的this  默认是undefined， 
+ 因为在正常的DOM操作中，监听点击，监听函数中的this其实是节点对象（比如说是button对象）；   
+ 这次因为React并不是直接渲染成真实的DOM，我们所编写的button只是一个语法糖，它的本质React的Element对象； 
+  那么在这里发生监听的时候，react在执行函数时并没有绑定this，默认情况下就是一个undefined；   



```arkts
<button onClick={this.btnClick}></button>
```

+ 解决方式1，使用bind强行将this绑定当前组件实例

```arkts
<button onClick={this.btnClick.bind(this)}></button>
```

+ 解决方式2，在构造数先为调用的方法绑定this

```arkts
construrtor(){
  this.btnClick=this.btnClick.bind(this)
}
<button onClick={this.btnClick}></button>
```

+ 解决方式3， 使用 ES6 class fields 语法  在定义函数时候利用箭头函数的this是父级作用域的this的绑定规则给绑定上组件的构造函数（类组件上）

```arkts
btnClick=()=>{}
<button onClick={this.btnClick}></button>
```

+ 解决方式4  事件监听时传入箭头函数（个人推荐）  传参以及默认事件的传递更加方便

```arkts
<button onClick={()=>{this.btnClick()}></button>
```

+  在执行事件函数时，有可能我们需要获取一些参数信息：比如event对象、其他参数  

```arkts
<button onClick={(e,aaa)=>{this.btnClick(e,aaa)}></button>
```

## jsx语法
> 💡  Tips：看起来是一段HTML元素，但是我们能在js中直接给一个变量赋值html吗？   其实是不可以的，如果我们将 type="text/babel" 去除掉，那么就会出现语法错误；其实它是一段jsx的语法；  
>

![](https://cdn.nlark.com/yuque/0/2024/png/26570921/1733317577585-fcb79040-3d98-4ba3-b519-e71ee2bcffdc.png)

1. 是什么？
+ JSX是一种JavaScript的语法扩展（eXtension），也在很多地方称之为JavaScript XML，因为看起就是一段XML语法； 
+   它用于描述我们的UI界面，并且其完成可以和JavaScript融合在一起使用；  
+  它不同于Vue中的模块语法，你不需要专门学习模块语法中的一些指令（比如v-for、v-if、v-else、v-bind）；
2. 为什么React选择了JSX  
+ React认为渲染逻辑本质上与其他UI逻辑存在内在耦合 （ UI需要绑定事件  、 UI中需要展示数据状态  、 在某些状态发生改变时，又需要改变UI  ）
+  它们之间是密不可分，所以React没有将标记分离到不同的文件中，而是将它们组合到了一起，这个地方就是组件（Component）；
+  JSX其实是嵌入到JavaScript中的一种结构语法；  
3. JSX的书写规范：
+  JSX的顶层只能有一个根元素，所以我们很多时候会在外层包裹一个div元素（或者Fragment）； 
+  为了方便阅读，我们通常在jsx的外层包裹一个小括号()，这样可以方便阅读，并且jsx可以进行换行书写；  
+  JSX中的标签可以是单标签，也可以是双标签；   
4.  JSX的使用  
+  jsx中的注释  需要用大括号括起来
+  JSX嵌入变量作为子元素  
    -  情况一：当变量是Number、String、Array类型时，可以直接显示 ；
    -  情况二：当变量是null、undefined、Boolean类型时，内容为空（如果实在要显示的话转化为字符串显示）；
    -   情况三：Object对象类型不能作为子元素（not valid as a React child）
+   JSX嵌入表达式  
    -  运算表达式  、 三元运算符  、 执行一个函数   

###  JSX的本质  
1.  实际上，jsx 仅仅只是 React.createElement(component, props, ...children) 函数的语法糖。实际上，jsx 仅仅只是 React.createElement(component, props, ...children) 函数的语法糖。 
    1.   createElement需要传递三个参数：  
    2.  参数一：type 当前ReactElement的类型, 如果是标签元素，那么就使用字符串表示 “div”；如果是组件元素，那么就直接使用组件的名称；
    3.  参数二：config   所有jsx中的属性都在config中以对象的属性和值的形式存储；比如传入className作为元素的class；
    4.  参数三：children   存放在标签中的内容，以children数组的方式进行存储；   当然，如果是多个元素呢？React内部有对它们进行处理，处理的源码在下方    

![](https://cdn.nlark.com/yuque/0/2024/png/26570921/1733319657136-e5e36367-9a3c-46f9-a984-440ff9fef006.png)



##  虚拟DOM的创建过程  
###  我们通过 React.createElement 最终创建出来一个 ReactElement对象：  
![](https://cdn.nlark.com/yuque/0/2024/png/26570921/1733319851040-497152f6-fd51-4b7d-bd6f-7c959f71a575.png)

###  这个ReactElement对象是什么作用呢？React为什么要创建它呢？  
1.  原因是React利用ReactElement对象组成了一个JavaScript的对象树；  
2.  JavaScript的对象树就是虚拟DOM（Virtual DOM）；
3. 打印jsx的返回结果就能看到 ReactElement的树结构    
4.  而ReactElement最终形成的树结构就是Virtual DOM；  

###  声明式编程  
1.  虚拟DOM帮助我们从命令式编程转到了声明式编程的模式  
2.  React官方的说法：Virtual DOM 是一种编程理念。  
3.  在这个理念中，UI以一种理想化或者说虚拟化的方式保存在内存中，并且它是一个相对简单的JavaScript对象  
4.  我们可以通过ReactDOM.render让 虚拟DOM 和 真实DOM同步起来，这个过程中叫做协调（Reconciliation）；  
5.  这种编程的方式赋予了React声明式的API：  
    1.  你只需要告诉React希望让UI是什么状态；  
    2.  React来确保DOM和这些状态是匹配的；  
    3.  你不需要直接进行DOM操作，就可以从手动更改DOM、属性操作、事件处理中解放出来；  

#### 
## React条件渲染  
> 💡 Tips： 某些情况下，界面的内容会根据不同的情况显示不同的内容，或者决定是否渲染某部分内容; 在vue中，我们会通过指令来控制：比如v-if、v-show; 在React中，所有的条件判断都和普通的JavaScript代码一致；
>
>   
>

+  常见的条件渲染的方式有哪些呢？  
    1.  条件判断语句  if else
    2.  三元运算符  
    3.  与运算符&&  
    4.   v-show的效果  主要是控制display属性是否为none 

##  React列表渲染   
React 是一个用于构建用户界面的JavaScript库，列表渲染是React中常见的需求。在React中，你可以使用`.map()`方法对数组进行遍历，并为数组的每个元素渲染一个组件或者HTML元素。  
以下是在React中进行列表渲染的基本步骤：

### 1. 定义数据源
首先，你需要定义一个数组作为数据源。例如：

```javascript
const numbers = [1, 2, 3, 4, 5];
```

### 2. 使用`.map()`方法
然后，在组件的`render`方法中或者在一个函数组件内部，使用`.map()`方法遍历数组：

```jsx
function NumberList() {
  const numbers = [1, 2, 3, 4, 5];
  return (
    <ul>
      {numbers.map((number) => (
        <li key={number}>{number}</li>

      ))}
    </ul>

  );
}
```

### 3. 指定key属性
当你在渲染列表时，每个列表项都应该有一个独特的`key`属性。这个`key`帮助React识别哪些项被改变、添加或者删除，从而优化性能。 key主要的作用是为了提高diff算法时的效率；  

```jsx
<li key={number}>{number}</li>

```

以下是一个完整的例子：

```jsx
import React from 'react';
function App() {
  const items = [
    { id: 1, text: 'Learn React' },
    { id: 2, text: 'Read a book' },
    { id: 3, text: 'Go shopping' }
  ];
  return (
    <ul>
      {items.map((item) => (
        <li key={item.id}>{item.text}</li>

      ))}
    </ul>

  );
}
export default App;
```

在这个例子中，我们有一个`items`数组，每个元素都有一个唯一的`id`和一个`text`属性。我们使用`.map()`来遍历这个数组，并为每个元素渲染一个`<li>`标签，使用`id`作为`key`。

### 注意事项
+ 确保`key`是唯一的。
+ `key`不需要在全局范围内唯一，只需要在兄弟元素中唯一即可。
+ 不要使用数组的索引作为`key`，除非列表项可能不会改变顺序。  
以上就是React列表渲染的基本方法。



  



##  React脚手架  
1.   create-react-app 项目名称  创建项目
2.  目录结构分析  ![](https://cdn.nlark.com/yuque/0/2024/png/26570921/1733320447470-c99307f7-9ff5-4827-bd9b-8b4b2a002f99.png?x-oss-process=image%2Fformat%2Cwebp%2Fresize%2Cw_1049%2Climit_0)
3.  PWA    Progressive Web App，即渐进式WEB应用；  
    1.  一个 PWA 应用首先是一个网页, 可以通过 Web 技术编写出一个网页应用；  
    2.  随后添加上 App Manifest 和 Service Worker 来实现 PWA 的安装和离线等功能；  
    3.  这种Web存在的形式，我们也称之为是 Web App；  
    4.  可以添加至主屏幕，点击主屏幕图标可以实现启动动画以及隐藏地址栏；  
    5.  实现离线缓存功能，即使用户手机没有网络，依然可以使用一些离线功能；  
    6.  实现了消息推送；  
    7.  一系列类似于Native App相关的功能；



4.   脚手架中的webpack  
    1.  React脚手架默认是基于Webpack来开发的；React脚手架将webpack相关的配置隐藏起来了（其实从Vue CLI3开始，也是进行了隐藏）所以我们在项目的根目录是看不到项目的webpack配置的；
    2.    我们希望看到webpack的配置信息 ， 可以执行一个package.json文件中的一个脚本："eject": "react-scripts eject" ，  这个操作是不可逆的  








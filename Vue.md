## 0000000000000

## 2-1.前端知识

### 1.1 前端三要素

- **HTML（结构）**：超文本标记语言，决定网页的结构和内容。
- **CSS（表现）**：层叠样式表，设定网页的表现形式。
- **JavaScript（行为）**：是一种弱脚本语言，其源代码不需经过编译，而是由浏览器解释运行，用于控制网页的行为。

### 1.2 三种色调

​		主色调、主字体、辅字体。



### 1.3 CSS

#### 1.3.1 CSS的缺陷

- 语法不够强大，比如无法嵌套书写，导致模块化开发中需要书写很多重复的选择器；
- 没有变量和合理的样式复用机制，使得逻辑上相关的属性值必须以字面的形式重复输出，导致难以维护。

​       前端开发人员会使用一种称之为【CSS预处理器】的工具，提供CSS缺失的样式层复用机制、减少冗余代码，提高样式代码的可维护性。大大提高了 前端在样式上的开发效率。



#### 1.3.2 CSS预处理器

​        CSS预处理器定义了一种新的语言，用一种专门的编程语言，为CSS增加一些编程的特性，将CSS作为目标生成文件，然后开发者就只要使用这种语言进行CSS的编码工作。简而言之，用一种专门的编程语言，进行Web页面样式设计，再通过编译器转化为正常的CSS文件，以供项目使用。

#### 1.3.3 常见的CSS预处理器

- SASS：基于Ruby，通过服务端处理，功能强大。解析效率高，需要学习Ruby语言，上手难度高于LESS。
- LESS：基于NodeJS，通过客户端处理，使用简单，功能比SASS简单，解析效率也低于SASS，但在实际开发中足够了，后台人员可以使用LESS。

### 1.4 JavaScript                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        

#### 1.4.1 JavaScript框架

- **jQuery**：简化DOM操作，缺点是DOM操作台频繁，影响前端性能（操作一次DOM渲染一次）；在前端里使用仅仅是为了兼容IE6、7、8。
- **Angular**： Google收购的前端框架，由java程序员开发，将后台的MVC模式搬到了前端并增加了**模块化开发理念**，与微软合作，采用TypeScript语法开发，对后台程序员友好，对前端程序员不友好，最大的缺点是迭代不合理。
- **React**： Facebook出品，一款高性能的前端框架，特点是提出了新概念**【虚拟DOM】**用于减少真实DOM操作，在内存中模拟DOM操作，有效提升了效率，缺点是复杂，因为需要额外学习一门【JSX】语言。
- **Vue**：一款渐进式JavaScript框架，所谓渐进式就是逐步实现新特性的意思，如实现模块化开发、路由、状态管理等新特性。其特点是综合了Angular（模块化）和React（虚拟DOM）的优点。同时具备angular和react的优点,轻量级，api简单，文档齐全。
- **Axios**：前端通信框架，因为Vue的边界很明确，为了处理DOM，所以不具备通信能力，此时就需要额外使用一个通信框架与服务器交互，当然也可以直接选择使用jQuery提供的Ajax通信功能。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     

#### 1.4.2 JavaScript构建工具

- Babel： JS编译工具，主要用于浏览器不支持的ES特性，比如用于编译TypeScript。
- WebPack：模块打包器，主要是打包、压缩、合并和按序加载。

#### 1.4.3 什么是虚拟DOM

​        一种可以预先通过JavaScript进行各种计算，把最终的DOM操作计算出来并优化的技术，由于这个DOM操作属于预处理操作，并没有真实的操作DOM，所以叫做虚拟DOM。

### 1.5 UI框架

- **Ant-Design**：阿里巴巴基于React的UI框架
- **ElementUI**：饿了么基于Vue的UI框架
- **Boostrap**： Twitter退出的一个用于前端开发的开源工具包
- **AmazeUI**：又叫妹子UI，一款HTML5跨屏前端框架

### 1.6 混合开发（Hybrid App）

主要目的实现一套代码三端统一（PC、Android、iOS）并能够调用到设备底层硬件（如：传感器、GPS、摄像头等），打包方式主要有以下两种：

云打包：HBuild -> HBuildX，DCloud出品；API Cloud

本地打包：Cordova（前身是PhoneGap）

Flutter： Google出品，主要特点是快速构建原生APP应用程序，如做混合应用该框架为必选框架

Flutter是谷歌的移动端UI框架，可在极短的时间内构建Android和iOS上高质量的原生级应用。Flutter可与现有代码一起工作，它被世界各地的开发者和组织使用，并且Flutter是免费和开源的。



### 1.7 当前主流前端框架

iView：主要特点是移动端支持较多，一个强大的基于Vue的UI库，有很多实用的基础组件比ElementUI的组件更丰富，主要服务于PC界面的中后台产品，使用单文件的Vue组件化开发模式就与npm+webpack+babel开发，支持ES2015高质量/功能丰富友好的API，自由灵活地使用空间。

ElementUI：主要特点是桌面端支持比较多，饿了么前端开源维护的Vue UI组件，组件基本涵盖后台所需的所有组件

ICE：飞冰是阿里团队基于React、Angular、Vue的中后台应用解决方案，主要组件是以React为主。

VantUI：有赞前端团队Vue组件库，可以快速大家风格统一的页面，提升开发效率。

### 1.8 微信小程序

mpvue：完备的Vue开发体验，并且支持多平台的小程序开发

mpvue是美团开发的一个使用Vue.js开发小程序的前端框架，目前支持微信小程序、百度智能小程序、头条小程序和支付宝小程序。框架基于Vue.js，修改了运行时框架runtime和代码编译器complier实现，使其可运行在小程序环境中，从而为小程序开发引入Vue.js开发体验。

## 2.  前后分离

### 2.1 后端为主的MVC时代

为了降低开发复杂度，以后端为出发点的MVC时代，Structs、SpringMVC等框架。

![avatar](.\picture\springMVC流程.png)

1. 发起请求到前端控制器（DispatcherServlet）
2. 前端控制器请求HandlerMapping查找Handler，可以根据xml配置、注解进行查找
3. 处理器映射器HandlerMapping向前端控制器返回Handler
4. 前端控制器调用处理器适配器去执行Handler
5. 处理器适配器去执行Handler
6. Handler执行完成给适配器返回ModelAndView
7. 处理器适配器向前端控制器返回ModelAndView，ModelAndView是springMVC框架的一个底层对象，包括Model和View
8. 前端控制器请求视图解析器去进行视图解析，根据逻辑视图名解析真正的视图（JSP）
9. 视图解析器向向前端控制器返回View
10. 前端控制器进行视图渲染，视图渲染将模型数据（在ModelAndView对象中）填充到request域
11. 前端控制器向用户响应结果 

#### 2.1.1 优点

​        MVC能有效降低代码耦合度，从架构上能让开发者明白代码应该写在哪里。为了让view更纯粹，还可以使用Thymeleaf、Freemarker等引擎模板，让前后端分工更清晰。



#### 2.1.2 缺点

- 前端开发重度依赖开发环境，开发效率低。
- 前后端职责纠缠不清：模板引擎功能强大，依旧可以通过拿到的上下文变量来实现各种业务逻辑。
- 对前端发挥的局限性：性能优化如果在前端做空间非常有限，常需要后端合作。

### 2.2 什么是前后端分离

#### 2.2.1 基于Ajax带来的SPA时代

AJAX（Asynchronous JavaScript And XML，异步JavaScript和XML，老技术新用法）被正式提出并开始使用CDN作为静态资源存储，出现了JavaScript王者归来（在此之前JavaScript是用来网页上贴狗皮膏药广告的）的SPA（Single Page Application）单页面应用时代。

#### 2.2.2 优点

前后端分工清晰，前后端的关键协作点是AJAX。

#### 2.2.3 缺点

- **前后端接口的约定：**如果后端的接口一塌糊涂，如果后端的业务模型不够稳定，那么前端开发会很痛苦。有了和后端一起沉淀的接口规则，还可以用来模拟数据，使得前后端可以在约定接口后实现高效并行并发。
- **前端开发的复杂度控制：**SPA应用大多以功能交互型为主，JavaScript代码超过十万行很正常，大量JS代码的组织，与View层绑定等，都不是容易的事。

### 2.3 前端为主的MV*时代

#### 2.3.1 MV*模式

- MVC（同步通信为主）：Model、View、Controller
- MVP（异步通信为主）：Model、View、Presenter
- MVVM（异步通信为主）：Model、View、ViewModel

Angular、React、Vue等前端框架原则是先按类型分层，比如Template、Controllers、Models，然后再在内层做切分。

![avatar](.\picture\SPA组件化.png)

模板：就是视图

控制器：负责页面与页面之前跳转，前端页面的路由

模型：就是模型

#### 2.3.2 优点

- **前后端工作清晰：**前端工作在浏览器端，后端工作在服务端。清晰的分工，可以让开发并行，测试数据的模拟并不难，前端可以本地开发。后端可以专注于业务逻辑的处理，输出Restful等接口。
- **前端开发的复杂度可控：**前端代码很重，很合理的分层，让前端代码能各司其职。
- **部署相对独立：**可以快速改进产品体验。

#### 2.3.3 缺点

- 代码不能重用，不如后端依旧需要对数据做各种校验，校验逻辑无法复用浏览器端的代码。
- 全异步，对SEO不利。往往还需要服务端做同步渲染的降级方案。
- 性能并非最佳，特别是移动互联网环境下。
- SPA不能满足需求，依旧存在大量多页面应用。URL Design需要后端配合，前端无法完全掌控。

### 2.4 MVVM

​        相比MVC，多了一个VM层，由它来做数据和视图之间的双向绑定。

![avatar](.\picture\mvvm.png)

#### 2.4.1 为什么要用MVVM

MVVM模式和MVC一样，主要的目的是分离视图（View）和模型（Model），有几大好处：

- **低耦合：**视图（View）可以独立于Model变化和修改，一个ViewModel可以绑定到不同的View上，当View变化的时候Model可以不变，当Model变化的时候View也可以不变。
- **可复用：**你可以把一些视图逻辑放在一个ViewModel里，让很多的View重用这段视图逻辑。
- **独立开发：**开发人员可以专注于业务逻辑和数据的开发（ViewModel），设计人员可以专注于页面设计。
- **可测试：**界面素来是难以测试的，而现在测试可以针对ViewModel来写。

## 2. Vue基础

https://cn.vuejs.org/v2/guide/

​		在MVVM架构中，是不允许数据和视图直接通信的，只能通过ViewModel来通信，而ViewModel就是定义了一个Observer观察者。Vue是一个MVVM模式的实现者，MVVM最重要的是ViewModel，ViewModel最重要的是实现数据的双向绑定，其核心实实现了**DOM监听**和**数据绑定**。

- ViewModel能观察到数据的变化，并对视图对应的内容进行更新。
- ViewModel能够监听到视图的变化，并能够通知数据发生变化。

其它MVVM实现者：Angularjs、Reactjs、微信小程序。

![avatar](.\picture\Vue.png)

### 2.1 为什么使用Vue

- 轻量级，体积小，压缩后只有20多kb。
- 移动优先，更适合移动端，比如移动端的touch事件。
- 易上手，学习曲线平稳，文档齐全。
- 吸取了Angular（模块化）和React（虚拟DOM）的长处，并拥有自己独特的功能，如：计算属性。
- 开源，社区活跃度高。

### 2.2 Vue两大核心要素

#### 2.2.1 数据驱动

![avatar](.\picture\Vue数据驱动.png)

​       把一个普通的JavaScript对象传给Vue实例的data选项，Vue将遍历此对象所有的属性，并使用Object.defineProperty把这些属性全部转为getter/setter。*Object.defineProperty是ES5中一个无法shim的特性，这也就是为什么Vue不支持IE8及以下的浏览器。*

​        这些getter/setter对用户来说是不可见的，但是在内部他们让Vue追踪依赖，在属性被访问和修改时通知变化。这里需要注意的是浏览器控制台在打印数据对象时getter/setter的格式化并不相同，需要安装vue-devtool来获取更加友好的检查接口。

​        每个组件实例都有相应的watcher实例对象，它会在组件渲染的过程中把属性记录为依赖，之后当依赖项的setter被调用时，会通知watcher重新计算，从而致使它关联的组件得以更新。

#### 2.2.2 组件化

- 页面上每个独立的可交互的区域视为一个组件；
- 每个组件对应一个工程目录，组件所需的各种资源在这个目录下就近维护；
- 页面不过是组件容器，组件可以自由嵌套组合（复用）形成完整的页面。

### 2.3 Vue程序

#### 2.3.1  第一个程序

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title></title>
<script src="vue.js" type="text/javascript" charset="utf-8"></script>
</head>
<body>
	<div id="app">
	  {{ message }} {{name}}
	</div>
	
	<script type="text/javascript">
	var app = new Vue({
		el: '#app',
		data: {
			message: 'Hello Vue!',
			name : "Vue"
		}
	});
	</script>

</body>
</html>
```

#### 2.3.2 生命周期

​		开始创建、初始化数据、编译模板、挂在DOM、渲染更新渲染、卸载等一系列过程，从创建到销毁的过程，就是生命周期。整个生命周期中，提供一系列方法，钩子函数。

​		面向对象封装的是属性和行为，行为有时就是业务，不可能完全相同，因此提供了回调函数。

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title></title>
<script src="vue.js" type="text/javascript" charset="utf-8"></script>
</head>
<body>
<div id="app">
	{{msg}}
</div>
<script type="text/javascript">
var vm = new Vue({
	el : "#app",
	data : {
		msg : "hi vue",
	},
	//在实例初始化之后，数据观测 (data observer) 和 event/watcher 事件配置之前被调用。
	beforeCreate:function(){
		console.log('beforeCreate');
	},
	/* 在实例创建完成后被立即调用。
	在这一步，实例已完成以下的配置：数据观测 (data observer)，属性和方法的运算，watch/event 事件回调。
	然而，挂载阶段还没开始，$el 属性目前不可见。 */
	created	:function(){
		console.log('created');
	},
	//在挂载开始之前被调用：相关的渲染函数首次被调用
	beforeMount : function(){
		console.log('beforeMount');

	},
	//el 被新创建的 vm.$el 替换, 挂在成功	
	mounted : function(){
		console.log('mounted');
	
	},
	//数据更新时调用
	beforeUpdate : function(){
		console.log('beforeUpdate');
			
	},
	//组件 DOM 已经更新, 组件更新完毕 
	updated : function(){
		console.log('updated');
			
	}
});
setTimeout(function(){
	vm.msg = "change ......";
}, 3000);
</script>
</body>
</html>
```



### 2.4 Vue通信

#### 2.4.1 Axios

Axios是一个开源的可以用在浏览器端和NodeJS的异步通信框架，他的主要作用是实现Ajax异步通信，其功能特点如下：

- 从浏览器中创建XMLHttpRequest
- 从NodeJS创建http请求
- 支持Promise API
- 拦截请求和响应
- 转换请求数据和响应数据
- 取消请求
- 自动转换json数据
- 客户端支持防御XSRF（跨站请求伪造）

#### 2.4.2 为什么使用Axios

​		由于Vue是一个视图层框架并且作者尤雨溪严格遵守soc（关注度分离原则），所以Vue.js并不包含Ajax通信功能，为了解决通信问题，引入Axios。

### 2.5 表单绑定

双向绑定指的是，当数据发生变化时，视图发生变化，当视图发生变化时，数据跟随发生变化。所以数据双向绑定一定是针对UI来说的。用v-model来绑定。

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title></title>
<script src="vue.js" type="text/javascript" charset="utf-8"></script>
</head>
<body>
<div id="app">
	<div id="example-1">
		<input v-model="message" placeholder="edit me">
		<p>Message is: {{ message }}</p>
		<textarea v-model="message2" placeholder="add multiple lines"></textarea>
		<p style="white-space: pre-line;">{{ message2 }}</p>
		<br />
		
		<div style="margin-top:20px;">
			<input type="checkbox" id="jack" value="Jack" v-model="checkedNames">
			<label for="jack">Jack</label>
			<input type="checkbox" id="john" value="John" v-model="checkedNames">
			<label for="john">John</label>
			<input type="checkbox" id="mike" value="Mike" v-model="checkedNames">
			<label for="mike">Mike</label>
			<br>
			<span>Checked names: {{ checkedNames }}</span>
		</div>
		
		<div style="margin-top:20px;">
			<input type="radio" id="one" value="One" v-model="picked">
			<label for="one">One</label>
			<br>
			<input type="radio" id="two" value="Two" v-model="picked">
			<label for="two">Two</label>
			<br>
			<span>Picked: {{ picked }}</span>
		</div>
		<button type="button" @click="submit">提交</button>
	</div>
	
</div>
<script type="text/javascript">
var vm = new Vue({
	el : "#app",
	data : {
		message : "test",
		message2 :"hi",
		checkedNames : ['Jack', 'John'],
		picked : "Two"
	},
	methods: {
		submit : function () {
			console.log(this.message);
			
		}
	}
});
</script>
<style type="text/css">

</style>
</body>
</html>
```

### 2.6 组件基础

组件即一组可以重复使用的模板。组件之间可以嵌套，页面是组件的容器，组件可以复用。

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title></title>
<script src="vue.js" type="text/javascript" charset="utf-8"></script>
</head>
<body>
<div id="app">
	<button-counter title="title1 : " @clicknow="clicknow">
		<h2>hi...h2</h2>
	</button-counter>
	<button-counter title="title2 : "></button-counter>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue"></script>
<script type="text/javascript">
Vue.component('button-counter', {
	props: ['title'],
	data: function () {
		return {
		  count: 0
		}
	},
	template: '<div><h1>hi...</h1><button v-on:click="clickfun">{{title}} You clicked me {{ count }} times.</button><slot></slot></div>',
	methods:{
		clickfun : function () {
			this.count ++;
			this.$emit('clicknow', this.count);
		}
	}
})
var vm = new Vue({
	el : "#app",
	data : {
		
	},
	methods:{
		clicknow : function (e) {
			console.log(e);
		}
	}
});
</script>
<style type="text/css">

</style>
</body>
</html>
```

### 2.7 计算属性

计算走cpu，属性走内存，目的是为了提高性能。

```html
<!DOCTYPE html>
<html>
<head>
	<meta charset="utf-8">
    <meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0" >
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
	<title>Document</title>
</head>
<body>
<div id="app">
    <!--方法带括号，属性不带括号-->
    <div>获取当时时间方法：{{getCurrentTime1()}}</div>
    <div>获取当时时间属性：{{getCurrentTime2}}</div>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue"></script>
<script type="text/javascript">
    var vm = new Vue({
        el: "#app",
        methods: {
            getCurrentTime1: function () {
                return Date.now();
            }
        },
        computed: {
            getCurrentTime2: function () {
                return Date.now();
            }
        }
    });
</script>
</body>
</html>
```

### 2.8 内容分发与自定义事件

```html
<!DOCTYPE html>
<html>
<head>
	<meta charset="utf-8">
    <meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0" >
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
	<title>Document</title>
</head>
<body>
<div id="app">
    <todo>
        <todo-title slot="todo-title" v-bind:title="title"></todo-title>
        <todo-items slot="todo-items" v-for="(item, index) in items" v-bind:item="item" v-bind:index="index" v-on:remove="removeItems(index)"></todo-title>
    </todo>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue"></script>
<script type="text/javascript">
    Vue.component("todo", {
        template: "<div>\
        		      <slot name='todo-title'></slot>\
					 <ul>\
					     <slot name='todo-items'></slot>\
        			  </ur>\
				 </div>
        "
    });
    Vue.component("todo-title", {
        props: ["title"],
        template: "<h1>{{title}}</h1>"
    });
    Vue.component("todo-items", {
        props: ["item", "index"],
        template: "<li>{{index}}.{{item}} <button @click='remove(index)'>删除</button></li>",
        methods: {
            remove: function (index) {
                this.$emit("remove", index);
            }
        }
    });
    
    var vm = new Vue({
        el: "#app",
        data: {
            title: "测试",
            items: ["sss","ddd","eee"]
        }
        methods: {
            removeItems: function (index) {
                this.items.splice(index, 1);
            }
        }
    });
</script>
</body>
</html>
```



## 3. VueCli

​		vue-cli官方提供的一个脚手架（预先定义好的目录结构及基础代码，创建maven项目时可以选择创建一个骨架项目，这个骨架项目就是脚手架）用于快速生成一个vue的项目模板

- 统一的目录结构
- 本地调试
- 热部署
- 单元测试
- 集成打包上限

### 3.1 VueCli安装及运行

```powershell
#安装nodejs，检测是否安装
npm -v
node -v

#安装vue-cli
npm install vue-cli -g -registry=https://registry.npm.taobao.org
#检查安装是否成功
vue list
#初始一个项目
vue init webpack hello-vue-cli
? Project name hello-vue-cli
? Project description A Vue.js project
? Author 小新哥 <403713133@qq.com>
? Vue build (Use arrow keys)
? Vue build standalone
? Install vue-router? No
? Use ESLint to lint your code? No
? Set up unit tests No
? Setup e2e tests with Nightwatch? No
? Should we run `npm install` for you after the project has been created? (recommended) no

   vue-cli · Generated "hello-vue-cli".

# Project initialization finished!
# ========================

To get started:

  cd hello-vue-cli
  npm install (or if using yarn: yarn)
  npm run dev

Documentation can be found at https://vuejs-templates.github.io/webpack

cd hello-vue-cli
npm install --registry=https://registry.npm.taobao.org

#启动该项目
npm run dev
#访问
localhost:8080
```



## 4. Vue程序

### 4.1 简单Vue程序

页面是组件的容器，实现组件与组件之间的跳转。

index.js

```javascript
import Vue from 'Vue'
import Router from 'vue-router'
import Content from '@/components/Content'

Vue.use(Router);

export default new Router({
    routes: [
        name: 'content',
        path: '/content',
        component: Content
    ]
})
```

main.js

```javascript
import Vue from 'vue'
import App from './App'
import router from './router'
Vue.config.productionTip = false
new Vue({
    el: '#app',
    components: {App},
    router,
    template: '<App/>'
})


```

App.vue

```vue
<template>
    <div id="app">
        <router-link to="/">首页</router-link>
        <router-link to="/content">内容</router-link>
    </div>
</template>
<script>
    export default {
        name: 'App'
    }
</script>
<style>
#app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
}
</style>

```

Content.vue

```vue
<template>
	<div>
        我是内容页
    </div>
</template>

<script>
    export default {
        name: "Content"
    }
</script>

<style scoped>
    
</style>
```





### 4.2 使用框架ElementUI

https://element.eleme.cn/#/zh-CN/component/installation

```powershell
#安装
npm i element-ui -S

#创建一个名为vue-element的工程
vue init webpack hello-vue-element

#安装依赖
#vue-router、element-ui、sass-loader和node-sass四个插件
cd vue-element
#安装vue-router
npm install --registry=https://registry.npm.taobao.org
#安装element-ui
npm i element-ui -S --registry=https://registry.npm.taobao.org
#安装asaa加载器
npm install sass-loader node-sass --save-dev --registry=https://registry.npm.taobao.org
#安装依赖
npm install --registry=https://registry.npm.taobao.org
#启动项目
npm run dev
```

在App.vue中加入以下内容：

```vue
<template>
    <div id="app">
        <router-view/>
    </div>
</template>
```

在 main.js 中写入以下内容：

```javascript
import Vue from 'vue';
import App from './App.vue';
import ElementUI from 'element-ui';
import 'element-ui/lib/theme-chalk/index.css'

Vue.use(ElementUI);

new Vue({
  el: '#app',
  components: {App},
  template: '<App/>',
  render: h => h(App)
});
```

在src/views下创建Login.vue

```vue
<template>
	<div>
        <el-form ref="form" :model="form" label-width="80px">
            <el-form-item label="账号">
    			<el-input v-model="form.username"></el-input>
			</el-form-item>
            <el-form-item label="密码">
    			<el-input type="password" v-model="form.password"></el-input>
  			</el-form-item>
            <el-form-item>
                <el-button type="primary" @click="login(form)">登录</el-button>
    		</el-form-item>
    	</el-form>
    </div>
</template>


<script>
    export default {
        name: "Login.vue"
        data() {
            return {
                form: {
                    username: '',
                    password: ''
                },
                rules: {
                    username: [
                        {required: true, message: '请输入账号', trigger: 'blur'}
                    ],
                    password: [
                        {required: true, message: '请输入密码', trigger: 'blur'}
                    ],
                }  
            }
        },
        methods: {
            login: function(formName) {
                this.$refs[formName].validate((valid) => {
                    if (valid) {
                        this.$router.push("/main");
                    } else {
                        this.$message.error('请输入账号密码！');
                        return false;
                    }
                });
            }
        }
    }
</script>
<style scoped>
    .login-box {
        width: 400px;
        border: 1px solid black;
        margin: 0 auto;
        padding: 20px 50px 20px 20px;
    }
</style>
```

在src/views下创建Main.vue

```javascript
import Vue from 'Vue'
import App from './App'
import router from './router'

import ElementUI from 'element-ui'
import 'element-ui/lib/theme-chalk/index.css'

Vue.config.productionTip = false

Vue.use(ElementUI);

new Router({
    el: '#app',
    components: {App},
    template: '<App/>',
    router,
    render: h => h(App)
});
```

在src/router下创建index.js

```javascript
import Vue from 'Vue'
import Router from 'vue-router'
import Content from '@/views/Main'
import Content from '@/views/Login'

Vue.use(Router);

export default new Router({
    routes: [
        {
            name: 'Login',
       		path: '/login',
        	component: Login
        },
        {
            name: 'Main',
        	path: '/main',
        	component: Main
        }
    ]
});
```



## 5. VueRouter嵌套路由

​		嵌套路由又称子路由，在实际应用中，通常由多层嵌套的组件组合而成。同样的，URL中各段动态路径也按某种结构对应嵌套的各层组件。

### 5.1 简单实例

```html
<script src="https://unpkg.com/vue/dist/vue.js"></script>
<script src="https://unpkg.com/vue-router/dist/vue-router.js"></script>
 
<div id="app">
  <h1>Hello App!</h1>
  <p>
    <!-- 使用 router-link 组件来导航. -->
    <!-- 通过传入 `to` 属性指定链接. -->
    <!-- <router-link> 默认会被渲染成一个 `<a>` 标签 -->
    <router-link to="/foo">Go to Foo</router-link>
    <router-link to="/bar">Go to Bar</router-link>
  </p>
  <!-- 路由出口 -->
  <!-- 路由匹配到的组件将渲染在这里 -->
  <router-view></router-view>
</div>
```

```javascript
// 0. 如果使用模块化机制编程，导入 Vue 和 VueRouter，要调用 Vue.use(VueRouter)
 
// 1. 定义（路由）组件。
// 可以从其他文件 import 进来
const Foo = { template: '<div>foo</div>' }
const Bar = { template: '<div>bar</div>' }
 
// 2. 定义路由
// 每个路由应该映射一个组件。 其中"component" 可以是
// 通过 Vue.extend() 创建的组件构造器，
// 或者，只是一个组件配置对象。
// 我们晚点再讨论嵌套路由。
const routes = [
  { path: '/foo', component: Foo },
  { path: '/bar', component: Bar }
]
 
// 3. 创建 router 实例，然后传 `routes` 配置
// 你还可以传别的配置参数, 不过先这么简单着吧。
const router = new VueRouter({
  routes // （缩写）相当于 routes: routes
})
 
// 4. 创建和挂载根实例。
// 记得要通过 router 配置参数注入路由，
// 从而让整个应用都有路由功能
const app = new Vue({
  router
}).$mount('#app')
 
// 现在，应用已经启动了！

```



### 5.2 路由模式

两种路由模式：

- hash：带路径#符号，如：http://localhost/#/login
- history：路径不带#符号，如：http://localhost/login



### 5.3路由钩子与异步请求

路由中的钩子函数：

- beforeRouterEnter：在进入路由前执行
- beforeRouterLeave：在离开路由前执行

```javascript
export default {
	props: ['id'],
	name: "UserProfile",
    beforeRouterEnter: (to, from, next) {
    	console.log("准备进入个人信息");
	},
    beforeRouterLeave: (to, from, next) {
        console.log("准备离开个人信息");
    }
}
```

### 5.4 在钩子函数中使用异步请求

```shell
#安装Axios
npm install axios -s --registry=https://registry.npm.taobao.org
```

```javascript
//引用Axios
import axios from 'axios'
Vue.prototype.axios = axios;
```

```
<script>
	export default {
		mounted: {
			getData: function () {
				this.axios({
					type: 'get',
					url: 'http://localhost:8080/static/data.json'
				}).then(response => {
					console.log(response);
				}).catch(error => {
					console.log(error);
				});
			}
		}
	
	}
</script>
```



## 6. Vuex状态管理

​		Vuex是一个专为Vue. js应用程序开发的状态管理模式。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可以预测的方式发生变化。前端无状态，后台session分离，session不能放大量数据，             

安装

```
npm install vuex -save --registry=https://registry.npm.taobao.org
```

修改main.js文件，导入Vuex，关键代码如下：

```javascript
import App from './App'
import router from './router'
import Element from 'element-ui'
import 'element-ui/lib/theme-chalk/index.css'
import axios from 'axios'
import Vuex from 'vuex'

/*原型链*/
Vue.prototype.axios = axios;
Vue.config.productionTip = false

Vue.use(ElementUI);
Vue.use(Vuex);

router.beforeEach( (to, from, next) => {
    
    let isLogin = sessionStorage.getItem("isLogin");
    
    if (to.path == "/logout") {
        sessionStorage.clear();
        next({path: "/login"});
    }
    
    else if (to.path == "/login") {
        if (isLogin == "true") {
            next({path: "/main"});
        }
    }
    
    else if (isLogin == null) {
        next({path: "/login"});
    }  
    
})
```



```javascript
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex);
/*状态存储*/
const state = sessionStorage.getItem("state") ? JSON.parse(sessionStorage.getItem("state")) : {
    user: {
        username: ""
    }
}
/*取值*/
const getters = {
    getUser(state) {
        return state.user;
    }
}
/*更新*/
const mutations = {
    updateUser(state, user) {
        state.user = user;
    }
}
/*异步更新*/
const action = {
    asyncUpdateUser(context, user) n
} 
/*暴露*/
export default new Vuex.Store({
    state,
    getters,
    mutations,
    actions
})
```








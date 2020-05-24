## 创建基本的webpack4.x项目

1. 运行 `npm init -y` 快速初始化项目，生成一个包管理的配置文件

2. 在项目根目录下创建 `src` 源代码目录和 `dist` 产品目录

3. 在src目录下创建 `index.html` 

4. 使用cnpm安装webpack，运行 `cnpm i webpack webpack-cli -D` 

   - cnpm的安装：全局运行 `npm i cnpm -g` 
   - 安装了两个包webpack和cli，其中cli是脚手架，在命令行中提供 `webpack` 用来执行打包命令
   - 运行后提示the mode option has not been set，在根目录下创建webpack配置文件 `webpack.config.js` 

   ```js
   // 向外暴露一个打包的配置对象；因为webpack是基于Node构建的，所以webpack支持所有Node API和语法
   module.exports = {
       mode: 'development', // production模式下，main.js会被压缩
   }
   ```

5. 在webpack 4.x中，有一个很大的特性，就是约定大于配置，目的是为了减少配置文件体积

   - 约定默认的打包入口路径是 src -> index.js

   - 打包成功后，输出目录是 dist -> main.js
   - 在src下的index.html中手动引入main.js

   ```html
   <!DOCTYPE html>
   <html>
   <head>
   	<meta charset="utf-8">
   	<meta http-equiv="X-UA-Compatible" content="IE=edge">
   	<title>首页</title>
   	<link rel="stylesheet" href="">
       <script src="../dist/main.js"></script> <!--引入打包后的文件-->
   </head>
   <body>
   	
   </body>
   </html>
   ```

6. 避免每次都重新打包，安装dev-server

   - 运行 `cnpm i webpack-dev-server -D` 

   - 在package.json配置文件中添加配置

     ```json
     {
         ...
         "scripts": {
             ...
             "dev": "webpack-dev-server --open --port 3000 --hot --host 127.0.0.1"
         }
         ...
     }
     ```

   - 执行dev脚本进行实时编译，运行 `npm run dev` 

     > 项目默认运行地址 http://localhost:8080
     >
     > 输出文件托管与根路径下（实际上是在内存中，没有写入物理磁盘）
     >
     > 可以认为项目根目录中有一个看不见的main.js
     >
     > 这里有个问题，进来以后只能看到文件目录
     >
     > 可选参数：open使用默认浏览器自动打开，port指定端口号，host指定域名

   - 运行命令 `cnpm i html-webpack-plugin -D` ，安装在内存中自动生成index页面的插件，在webpack.config.js中添加配置

     ```js
     const path = require('path')
     const HtmlWebPackPlugin = require('html-webpack-plugin') // 导入安装的插件
     
     // 创建一个插件的实例化对象
     const htmlPlugin = new HtmlWebPackPlugin({
         template: path.join(__dirname, './src/index.html'), // 源文件，__dirname表示当前文件所在目录，即根目录
         filename: 'index.html' // 内存中首页的名称
     })
     
     // 向外暴露一个打包的配置对象；因为webpack是基于Node构建的，所以webpack支持所有Node API和语法
     module.exports = {
         mode: 'development', // production模式下，main.js会被压缩
         plugins: [
             htmlPlugin // 把插件放到plugin下
         ]
     }
     ```

   - 再次运行 `npm run dev` 命令，直接就显示页面，而且自动引入main.js，无需手动引入



## 在项目中使用react

1. 运行 `cnpm i react react-dom` 安装包

   - react: 专门用于创建组件和虚拟DOM的，同时组件的生命周期都在这个包中

   - react-dom: 专门进行DOM操作的，最主要的应用场景，就是 `ReactDOM.render()`

2. 在 `index.html` 页面中，创建容器：

   ```html
   <!--容器，将来使用React创建的虚拟DOM元素，都会被渲染到这个指定的容器中-->
   <div id="app"></div>
   ```

3. 导入包：

   ```jsx
   import React from 'react'
   import ReactDOM from 'react-dom'
   ```

4. 创建虚拟DOM元素：

   ```jsx
   // 创建虚拟DOM元素的API    <h1 title="好的" id="myh1">今日头条</h1>
   // 什么是虚拟DOM：用JS对象的形式，来表示 DOM 和 DOM 之间的嵌套关系
   // 第一个参数：字符串类型的参数，表示要创建的标签的名称
   // 第二个参数：对象类型的参数，表示创建的元素的属性节点，如暂时没有样式可设为null
   // 第三个参数：子节点
   // 参数n：其他子节点
   const myh1 = React.createElement('h1', {title: '好的', id: 'myh1'}, '今日头条')
   ```

5. 渲染：

   ```jsx
   // 3. 渲染虚拟DOM元素
   // 参数1：表示要渲染的虚拟DOM对象
   // 参数2：指定容器，注意：这里不能直接用id选择器，需要放一个容器的DOM对象
   ReactDOM.render(myh1, document.getElementById('app'))
   ```

6. 运行：

   在命令行中输入`npm run dev` 运行项目，安装模块用cnpm，运行还是用npm



## JSX语法

在 JS 文件中，默认不能写类似于 HTML 的标记，否则会打包失败；

可以使用 babel 来转换 JS 中的标签；

在 JS 中混合写入类似于 HTML 的语法，叫做 JSX 语法（符合 XML 规范的 JS ）；

```jsx
// 注意：JSX语法的本质，还是在运行的时候，被转换成了 React.createElement 的形式来执行的，不是把标签直接丢到页面上显示；
const mydiv = <div id="mydiv" title="div aaa">这是一个div元素</div>
```

> 什么是 JSX 语法：就是符合 xml 规范的 JS 语法（xml比HTML要严谨很多）

1. **如何启用JSX语法**

   - 安装 `babel` 插件

     - 运行 `cnpm i babel-core babel-loader babel-plugin-transform-runtime -D` 
     - 运行 `cnpm i babel-preset-env babel-preset-stage-0 -D` 

   - 安装能够识别转换 JSX 语法的包 `babel-preset-react` 

     - 运行 `cnpm i babel-preset-react -D` 

   - 在webpack.config.js中添加babel-loader配置项

     ```js
     // 向外暴露一个打包的配置对象；因为webpack是基于Node构建的，所以webpack支持所有Node API和语法
     // webpack 默认只能打包处理 .js 后缀名类型的文件；像 .png .vue 无法主动处理，所以需要配置第三方的loader；
     module.exports = {
         mode: 'development', // production模式下，main.js会被压缩
         plugins: [
             htmlPlugin // 把插件放到plugin下
         ],
         module: { // 所有第三方模块的配置规则
         	rules: [ //第三方匹配规则
         		// 添加exclude排除项，否则报错
         		{ test: /\.js|jsx$/, use: 'babel-loader', exclude: /node_modules/ }, 
         	]
     	}
     }
     ```

   - 在根目录中创建 `.babelrc` 配置文件（json配置文件，不能用单引号，不能写注释）

     ```json
     {
         "presets": ["env", "stage-0", "react"],
         "plugins": ["transform-runtime"]
     }
     ```

2. **JSX语法本质：** 并不是直接把 jsx 渲染到页面上，而是先在内部转换为 React.createElement 的形式，再渲染的；

3. **在 JSX 中混合写入 JS 表达式：** 在 JSX 语法中，需要把 JS 代码嵌入到 `{ }` 内

   ```jsx
   import React from 'react'
   import ReactDOM from 'react-dom'
   
   // 1.渲染数字
   let a = 10
   // 2.渲染字符串
   let str = 'Hello World'
   // 3.渲染布朗值
   let boo = true
   // 4.为属性绑定值
   let title = '111'
   // 5.渲染 JSX 元素
   const h1 = <h1>蛤蛤哈</h1>
   // 6.渲染 JSX 元素数组，如果直接渲染会出现警告，缺少key
   const arr = [
       <h2>这是h2</h2>,
       <h3>这是h3</h3>
   ]
   // 7.将普通字符串数组，转换为 JSX 数组并渲染到页面上【两种方案】
   const arrStr = ['毛利兰', '柯南', '小五郎', '灰原哀']
   // 【方案一】手动在外部进行for循环（forEach没有返回值）
   const nameArr = []
   arrStr.forEach(item => {
       const temp = <h5>{ item }</h5>
       nameArr.push(temp)
   })
   // 【方案二】数组的 map 方法（也是遍历数组的每一项，并生成一个新的数组返回）
   const nameArr = arrStr.map(item => {
       //return item + '~~'
       return <h3>{ item }</h3>
   })
   // 在React中，key需要添加给被 forEach 或 map 或 for 循环直接控制的元素
   // 如果外面只有h3，key就加在h3
   const nameArr = arrStr.map(item => <h3 key={item}>{ item }</h3>)
   // 如果h3外面套了div，key需要加到div上
   const nameArr = arrStr.map(item => <div key={item}><h3>{ item }</h3></div>)
   
   
   const div = <div>
             { a }
             <hr />
             { a + 2 }
             <hr />
             { str }
             <hr />
             { boo.toString() }
             {/* 布朗值直接没法显示，需要转换为字符串 */}
             <hr />
             { boo ? '条件为真' : '条件为假' }
             {/* 布朗值可以进行条件判断 */}
             <hr />
             <p title={ title }>这是p标签</p>
             { h1 }
             <hr />
             { arr }
             <hr />
             { nameArr }
             <hr />
             { arrStr.map(item => {
                 return <h3>{ item }</h3>
             }) }
             {/* 可以直接在括号里面写循环 */}
             <hr />
             { arrStr.map(item => <h3>{ item }</h3>) }
             {/* 括号里面如果只有一行，那么括号可以去掉，顺便把return也给干掉 */}
         </div>
   
   // 什么情况下需要使用 {} 呢？当需要在 JSX 控制的区域内写 JS 表达式，就需要把 JS 代码写到 {} 中
   ReactDOM.render(div, document.getElementById('app'))
   ```

4. **在 JSX 中写注释：** 推荐使用 `{/* 这是注释 */}` 

5. **为 JSX 元素添加 class 类名：** 需要使用 `classname` 来替代 `class` ，`htmlFor` 替换label的 `for` 属性

   ```html
   <p className="myele">!!!!!!!!!!!!!</p>
   <label htmlFor="dbydm">111</label>
   ```

6. 在 JSX 创建 DOM 的时候，必须有唯一的根元素进行包裹；

7. 在 JSX 语法中，标签必须成对出现，如果是单标签则必须自闭合

> 当编译引擎在编译 JSX 代码的时候，如果遇到了 `<` 那么就把它当作 HTML 代码去编译，如果遇到了 `{}` 就把括号内部的代码当作普通的 JS 代码去编译



## React中创建组件

### 第1种 - 创建组件的方式

> **使用构造函数来创建组件**，如果要接受外界传递的数据，需要在构造函数的参数列表中使用 `props` 来接收；
>
> 必须要向外return一个合法的JSX创建的虚拟DOM;

- 创建组件：

  ```jsx
  function Hello () {
      // 如果什么都不return就会报错
      // 如果在组件中return一个null，表示此组件是空的，什么都不渲染
      // 在组件中必须返回一个合法的 JSX 虚拟DOM元素
      // return null
      
      return <div>Hello 组件</div>
  }
  ```

- 使用组件：

  ```jsx
  ReactDOM.render(<div>
      	{/* 直接把组件的名称，以标签形式，丢到页面上即可 */}
      	<Hello></Hello>
      </div>, document.getElementById('app'))
  ```

- 为组件传递参数：

  ```jsx
  // 使用组件，并为组件传递 props 数据
  <Hello name={dog.name} age={dog.age} gender={dog.gender}></Hello>
  // 当对象里面属性很多的时候，可以使用ES6的展开运算符
  <Hello {...dog}></Hello>
  
  // 在构造函数中，使用 props 形参，接收外界传递过来的数据
  function Hello(props){
      // props.name = 'zs'
      console.log(props)
      // 注意：React组件中的 props 永远都是只读的，不能重新赋值
      
      return <div>这是 Hello 组件 --- {props.name} --- {props.age} --- {props.gender}</div>
  }
  ```

- index.js完整代码：

  ```jsx
  import React from 'react'
  import ReactDOM from 'react-dom'
  
  function Hello(props){
      
      console.log(props) // {name: '大黄', age: 3, gender: '熊'}
      
      return <div>这是 Hello 组件 --- {props.name} --- {props.age} --- {props.gender}</div>
  }
  
  // 创建一个dog对象，用来传递参数
  // 直接把属性写到标签里就可以传递，浏览器装个react插件可以看到传过来的参数
  // 在组件里用形参去接收，就可以使用
  const dog = {
      name: '大黄',
      age: 3,
      gender: '雄'
  }
  
  ReactDOM.render(<div>
          {/* <Hello name={dog.name} age={dog.age} gender={dog.gender}></Hello> */}
          <Hello {...dog}></Hello>
      </div>, document.getElementById('app'))
  ```

  

1. 父组件向子组件传递数据

2. 使用{...obj}属性扩散传递数据

   ```jsx
   // 有这样一个需求，需要把o2的属性放到o1里面
   var o2 = {
       age: 22,
       address: '中国北京',
       phone: '139999'
   }
   
   var o1 = {
       name: '张三'
   }
   
   // 方案一
   var o1 = {
       name: '张三'，
       age: o2.age,
       address: o2.address,
       phone: o2.phone
   }
   
   // 方案二
   var o1 = {
       name: '张三',
       ...o2
   }
   
   console.log(o1)
   ```

3. 将组件封装到单独的文件中

   - 在 src 目录下新建 components 目录（所有的源代码都在 src 下）

   - 在 components 目录下新建 Hello.jsx 文件（组件一般都放到 jsx 里面）

   - 确保在 webpack.config.js 中添加了 jsx 的匹配规则

   - 在 Hello.jsx 中添加如下代码：

     ```jsx
     import React from 'react'
     
     // 创建并导出组件
     export default function Hello(props){
         
         console.log(props) // {name: '大黄', age: 3, gender: '熊'}
         
         return <div>这是 Hello 组件 --- {props.name} --- {props.age} --- {props.gender}</div>
     }
     
     // 把组件暴露出去
     // export default Hello
     ```

   - 在 index.js 中导入 Hello 组件：

     ```jsx
     // 导入 Hello 组件
     // 默认。如果不做单独配置的话，不能省略 .jsx 后缀名
     // import Hello from './components/Hello.jsx'
     import Hello from './components/Hello'
     ```

   - 如何省略后缀？打开 webpack.config.js ，并在导出的配置对象中，新增 resolve 节点：

     ```js
     // 向外暴露一个打包的配置对象；因为webpack是基于Node构建的，所以webpack支持所有Node API和语法
     // webpack 默认只能打包处理 .js 后缀名类型的文件；像 .png .vue 无法主动处理，所以需要配置第三方的loader；
     module.exports = {
         mode: 'development', // production模式下，main.js会被压缩
         plugins: [
             htmlPlugin // 把插件放到plugin下
         ],
         module: { // 所有第三方模块的配置规则
         	rules: [ //第三方匹配规则
         		// 添加exclude排除项，否则报错
         		{ test: /\.js|jsx$/, use: 'babel-loader', exclude: /node_modules/ }, 
         	]
     	},
     	resolve: {
     		extensions: ['.js', '.jsx', '.json'] // 表示文件的后缀名，默认有js和json
     	}
     }
     ```

   - 修改完配置文件后，需要 `Ctrl + C` 终止项目并重新运行 `npm run dev` 

   - 如何用@表示项目根目录？

     ```jsx
     // 看到@符号，肯定是通过alias进行配置的
     import Hello from '@/components/Hello'
     ```

   - 还是在 webpack.config.js 里的 resolve 节点下，新增 alias 别名：

     ```
     	resolve: {
     		extensions: ['.js', '.jsx', '.json'], // 表示文件的后缀名，默认有js和json
     		alias: {
     			'@': path.join(__dirname, './src') // 表示项目根目录中 src 这一层路径
     		}
     	}
     ```

   - 修改完配置文件后，需要 `Ctrl + C` 终止项目并重新运行 `npm run dev` 

4. 注意：组件的名称首字母必须大写

   - 小写会被识别为合法的HTML标签

### 第2种 - 创建组件的方式

> 使用 class 关键字来创建组件
>
> ES6 中的 class 关键字，是实现面向对象的新形式

#### 了解ES6中 class 关键字的使用

1. 传统通过构造函数来实现面向对象：

   ```js
   function Person () {}
   const p1 = new Person()
   console.log(p1) // Person {}
   ```
   
2. 通过 class 关键字实现面向对象：

   ```js
   class Animal {}
   const a1 = new Animal()
   console.log(a1) // Animal {}
   ```
   
3. class 中 `constructor` 的基本使用：

   ```js
   // 通过形参接收参数，并用 this 分配
   function Person (name, age) {
       this.name = name
       this.age = age
   }
   
   const p1 = new Person('王多多', 18)
   console.log(p1)
   
   // ------------------这是分割线--------------------
   
   class Animal {
   	// 每个类中，都有一个构造器，如果没有手动指定，那么可以认为类内部有一个隐形的，看不见的空构造器，类似于 constructor () {}
       // 构造器的作用，就是每当 new 这个类的时候，优先执行构造器内部的代码
       constructor (name, age) {
   		this.name = name
   		this.age = age
   	}
   }
   
   const a1 = new Animal('大黄', 3)
   console.log(a1)
   ```
   
4. 实例属性和静态属性：

   ```js
   function Person (name, age) {
       this.name = name
       this.age = age
   }
   
   const p1 = new Person('王多多', 18)
   console.log(p1)
   // 通过 new 出来的实例，访问到的属性，叫做【实例属性】
   console.log(p1.name) // 王多多
   console.log(p1.age) // 18
   
   // info 属性，直接挂载给了构造函数，所以它是静态属性
   // 不需要被new，直接通过构造函数来访问
   Person.info = 'aaa'
   // 通过 构造函数 直接访问到的属性，叫做【静态属性】
   console.log(p1.info) // undefined
   console.log(Person.info) // aaa
   
   // ------------------这是分割线--------------------
   
   class Animal {
       constructor (name, age) {
           this.name = name
           this.age = age
       }
       // 在 class 内部，通过 static 修饰的属性，就是静态属性
       static info = 'eee'
   }
   const a1 = new Animal('大黄', 3)
   console.log(a1)
   // 在构造器中，通过 this 分配的属性，也可以叫【实例属性】
   console.log(a1.name) // 大黄
   console.log(a1.age) // 3
   
   // info 是 Animal 的【静态属性】
   console.log(a1.info) // undefined
   console.log(Animal.info) // eee
   ```

5. 实例方法和静态方法：

   ```js
   function Person (name, age) {
       this.name = name
       this.age = age
   }
   
   Person.info = 'aaa'
   
   // 这是 Person 的实例方法
   Person.prototype.say = function () {
       console.log('这是 Person 的实例方法')
   }
   
   // 这是 Person 的静态 show 方法
   Person.show = function () {
       console.log('这是 Person 的静态 show 方法')
   }
   
   const p1 = new Person('王多多', 18)
   p1.say() // 这是 Person 的实例方法（挂载在原型对象上）
   Person.show() // 这是 Person 的静态 show 方法（跟静态属性 info 一起挂载在构造函数上）
   
   // ------------------这是分割线--------------------
   
   // 注意1：在 class 的 { } 区间内，只能写 构造器，静态属性，实例方法和静态方法
   // 如果写类似 var a = 10 就会报错
   // 注意2：class 关键字只是在语法层面做了优化，但是在内部走的还是构造函数的形式
   // 我们把 class 关键字，称作语法糖，用起来比较方便，但是本质没有改变
   class Animal {
       // 实例属性（构造器用得也比较多）
       constructor (name, age) {
           this.name = name
           this.age = age
       }
       // 静态属性（用得也不多）
       static info = 'eee'
   	// 这是 Animal 的实例方法（经常用到）
   	jiao() {
           console.log('这是 Animal 的实例方法')
       }
   	// 这是 Animal 的静态 show 方法（用得不多）
   	static show() {
           console.log('这是 Animal 的静态 show 方法')
       }
   }
   
   const a1 = new Animal('大黄', 3)
   a1.jiao() // 这是 Animal 的实例方法（挂载在原型对象上）
   Animal.show() // 这是 Animal 的静态 show 方法（跟静态属性 info 一起挂载在构造函数上）
   ```

6. 使用 `extends` 关键字实现继承：

   ```js
   class Person {
       // 实例属性
       constructor (name, age) {
           this.name = name
           this.age = age
       }
       
       // 打招呼的实例方法
       sayHello () {
           console.log('大家好')
       }
   }
   
   class American extends Person {
       
   }
   
   const a1 = new American('Jack', 20)
   console.log(a1)
   a1.sayHello() // 子类访问父类的实例方法
   
   class Chinese extends Person {
       
   }
   
   const c1 = new Chinese('张三', 22)
   console.log(c1)
   c1.sayHello() // 子类访问父类的实例方法
   ```

7. constructor 构造器中的 super 函数说明：

   ```js
   class Person {
       constructor (name, age) {
           this.name = name
           this.age = age
       }
   }
   
   class American extends Person {
       constructor (name, age) {
           // 注意1：如果一个子类通过 extends 关键字继承了父类，那么在子类的 constructor 构造函数中，必须优先调用一些 super() ，也就是说第一行必须是 super
           // 注意2：super 是个什么东西？ super 是一个函数，它是父类的构造器，子类中的 super ,其实就是父类中 constructor 构造器的一个引用
           // 注意3：如果在子类中没有使用 constructor ，那么在 new 的时候会默认帮你传递参数，在子类中使用了 constructor 那就必须手动传递参数，否则返回的属性都是 undefined
       	super (name, age)
       }
   }
   
   
   // 在 new 一个类的时候，会立即调用自己的 constructor ，所以必须要在构造器中传递参数
   // 参数传递顺序：首先在 new 的位置->子类构造器->子类super->父类构造器->最后分配到属性
   const a1 = new American('Jack', 20)
   console.log(a1)
   ```

8. 为子类挂载独有的实例属性和实例方法

   ```js
   class Person {
       constructor (name, age) {
           this.name = name
           this.age = age
       }
   }
   
   class Chinese extends Person {
       // name 姓名
       // age 年龄
       // IDNumber 身份证号 【中国人独有的】 既然是独有的，就不适合挂载到父类上
       constructor (name, age, IDNumber) {
           super (name, age)
           // 语法规范，在子类中 this 只能放到 super 之后使用
           this.IDNumber = IDNumber
       }
   }
   
   const c1 = new Chinese('张三', 22, '130258********')
   console.log(c1)
   ```

#### 基于class关键字创建组件

1. 最基本的组件结构：

   ```jsx
   // 如果要使用 class 定义组件，必须让自己的组件继承自 React.Component
   class 组件名称 extends React.Component {
       // 在组件内部，必须有 render 函数，用于渲染当前组件对应的虚拟DOM结构
       // render 实际上是 class 内的实例方法
       render(){
           // render 函数中，必须返回合法的 JSX 虚拟DOM结构
           return <div>这是 class 创建的组件</div>
       }
   }
   ```

2. class 组件的调用及参数传递：

   ```jsx
   import React from 'react'
   import ReactDOM from 'react-dom'
   
   class Movie extends React.Component {
       render(){
           // 在 function 创建的组件中，需要通过形参来接受参数
           // 在 class 关键字创建的组件中，如果想使用外界传递过来的 props 参数，不需要接受，直接通过 this.props.*** 就能访问
           
           // 注意：无论是 class 还是普通 function 创建的组件，它们的 props 都是只读的；
           // this.props.name = '李四'
           
           return <div>
               {/*注意：在 class 组件内部， this 表示当前组件的实例对象*/}
               这是 Movie 组件 -- { this.props.name } -- { this.props.age }
           </div>
       }
   }
   
   const user = {
       name: '张三',
       age: 22,
       gender: '男'
   }
   
   ReactDOM.render(<div>
       	
           {/*这里的 Movie 标签，其实就是 Movie 类的实例对象*/}
           <Movie name={user.name} age={user.age}></Movie>
           
           <hr />
           
           {/*可以多次调用，每次调用相当于 new 了一个对象*/}
           <Movie></Movie>
           
       </div>, document.getElementById('app'))
   ```



## 两种创建组件方式的对比

> 注意：使用 `class` 关键字创建的组件，有自己的私有数据（this.state） 和 生命周期函数；
>
> 注意：使用 function 创建的组件，只有props，没有自己的私有数据 和 生命周期函数；

1. 通过 this.state 给组件绑定私有数据：

   ```jsx
   import React from 'react'
   import ReactDOM from 'react-dom'
   
   class Movie extends React.Component {
       constructor(){
           super()
           // 只有调用了 super() 以后，才能使用 this 关键字
           this.state = {
               msg: '大家好，我是 class 创建的 Movie 组件'
           }
           // 在 state 绑定数据之后，打开浏览器 react 调试工具，之前上面有一个只读的 props，现在下面多了一个 state
       }
       
       render(){
           // 注意：无论是 class 还是普通 function 创建的组件，它们的 props 都是只读的；
           // this.props.name = '李四'
           // state 是可读可写的
           this.state.msg = 'msg 的值被我修改了！'
           
           return <div>
               这是 Movie 组件 -- { this.props.name } -- { this.props.age }
               {/*把state的内容在页面上显示出来*/}
               <h3>{ this.state.msg }</h3>
           </div>
       }
   }
   
   const user = {
       name: '张三',
       age: 22,
       gender: '男'
   }
   
   ReactDOM.render(<div>
       	
           <Movie name={user.name} age={user.age}></Movie>
           
       </div>, document.getElementById('app'))
   ```

2. 用**构造函数**创建出来的组件，叫做“无状态组件”【用的不多】

3. 用**class关键字**创建出来的组件，叫做“有状态组件”【用的最多】

4. 什么情况下使用有状态组件？什么情况下使用无状态组件？

   - 如果一个组件需要有自己的私有数据，则推荐使用：class创建的有状态组件；
   - 如果一个组件不需要有私有数据，则推荐使用：无状态组件；
   - React官方说：无状态组件，由于没有自己的 state 和生命周期函数，所以运行效率会比有状态组件稍高一些；

> 有状态组件和无状态组件**本质区别**就是：有无 state 属性 和 有无生命周期函数；

 	5. 组件中的 `props` 和 `state` 之间的区别
      - props 中的数据都是外界传递过来的；
      - state 中的主角，都是组件私有的（通过 Ajax 获取回来的数据，一般都是私有数据）；
      - props 中的数据都是只读的，不能重新赋值；
      - state 中的数据，都是可读可写的；



## 参考文档

[Ant Design - 一套企业级 UI 设计语言和 React 组件库](https://ant.design/index-cn)

[Ant Design 实战教程（beta 版）](https://www.yuque.com/ant-design/course/sc1lvc)
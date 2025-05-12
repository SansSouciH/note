# Vue3

## 基础知识

> webpack和vite都是项目构建工具。vite轻量，对TypeScript、JSX、CSS原生支持

**创建项目**

```shell
# 在指定目录的命令行中创建前端工程（vite构建）
npm create vue@latest
准备就绪后：
cd 创建好的项目文件夹
npm install
npm run dev
> 启动vue3脚手架
# 发布到生产环境
npm run build
```

**src目录**

```csharp
my-vue3-app/
│
├── public/                # 公共资源目录
│   ├── index.html         # 入口 HTML 文件
│   └── ...                # 其他静态文件 (例如图片、图标等)
│
├── src/                   # 源代码目录 (核心代码都在这里)
│   ├── assets/            # 资源文件 (如图片、样式等)
│   ├── components/        # Vue 组件目录
│   ├── router/            # 路由配置 (如果使用 Vue Router)
│   ├── store/             # 状态管理 (如果使用 Vuex 或 Pinia)
│   ├── views/             # 页面视图文件 (与 router 配合使用)
│   ├── App.vue            # 根组件 (应用的入口组件)
│   ├── main.ts / main.js  # 应用入口文件 (引导应用启动)
│   └── ...                # 其他配置文件
│
├── .gitignore             # Git 忽略文件
├── package.json           # 项目依赖和脚本
├── vite.config.js         # Vite 配置文件 (如使用 Vite)
├── tsconfig.json          # TypeScript 配置文件 (如使用 TypeScript)
└── ...                    # 其他配置文件
```

**常用属性**

| 指令        | 功能                                                         | 简写   |
| ----------- | ------------------------------------------------------------ | ------ |
| `v-bind`    | 动态绑定属性或 prop（单项绑定数据）                          | `:`    |
| `v-model`   | 双向数据绑定，用于表单控件                                   | 无简写 |
| `v-if`      | 条件渲染元素（在HTML代码中不显示）                           | 无简写 |
| `v-else-if` | 条件链中的额外条件                                           | 无简写 |
| `v-else`    | 默认条件处理                                                 | 无简写 |
| `v-for`     | 循环渲染列表"(item,indx) in items"或"item in items"          | 无简写 |
| `v-show`    | 根据条件显示或隐藏 DOM 元素display=none（频繁切换显示的场景） | 无简写 |
| `v-on`      | 绑定事件监听器（@click）                                     | `@`    |
| `v-slot`    | 定义插槽，父组件向子组件传递页面模板内容                     | #      |
| `v-html`    | 将字符串渲染为 HTML（小心 XSS）                              | 无简写 |
| `v-text`    | 动态插入纯文本内容                                           | 无简写 |
| `v-pre`     | 跳过编译，保留原始模板                                       | 无简写 |
| `v-cloak`   | 避免未编译模板闪现                                           | 无简写 |
| `v-once`    | 只渲染一次，提升性能                                         | 无简写 |
| `{{}}`      | 插值`{{person.name}}`                                        | 无简写 |

**生命周期**

> 创建期、挂载期、更新期、销毁期

![vue3生命周期](.\img\vue3生命周期.png)

## 内置函数

**常用函数**

* ref()
* reactive()
* computed()
* watch()
* watchEffect()

**生命周期函数**

* onBeforeMount()：在Dom属性挂载之前执行，此时HTML标签都未渲染
* onMounted()：在Dom属性挂载后执行，此时HTML标签刚渲染
* onBeforeUpdate()：在API数据更新前执行，此时Dom未更新
* onUpDated()：API数据更新后执行，此时Dom刚更新

**组件传值函数**

* defineProps()

  ```html
  //Father.vue
  const data = reactive({
      money: 100,
      books: ["1", "2"]
  })
  <Son v-bind="data"></Son>
  //Son.vue
  //1.
  let props1 = defineProps(["money", "books"])
  //2.
  let props2 = defineProps({
  	money:	{
  		type: Number,
  		required: true,
  		default: 200
  	},
  	books: Array
  })
  ```

* defineEmits()

  ```html
  //Son.vue
  let emits = defineEmits(['buy']);
  function buy(){
  	emits('buy', -5);
  }
  ...
  <button @click="buy"></button>
  //Father.vue
  function moneyMinis(args) {
  	alert("感知到Son组件调用" + args);
  	data.money += args;
  }
  ...
  <Son @buy = "moneyMinis"></Son>
  ```

## Router

> Vue-Router官方中文文档：https://router.vuejs.org/zh/introduction.html

* 路由配置

  * routes：路由表
  * createRouter()：创建路由

* 标签

  * `<router-link>`
  * `<router-view>`

* 函数

  * `useRoute()`：提供当前路由的信息（只读）

  * `useRouter()`：提供路由器实例，用于控制路由跳转（可写）

  | 方法/属性     | 所属            | 类型/功能                | 作用                         |
  | ------------- | --------------- | ------------------------ | ---------------------------- |
  | `useRoute()`  | Composition API | 获取当前路由信息（只读） | 例如获取 path、query、params |
  | `useRouter()` | Composition API | 控制路由跳转（可写）     | 例如使用 push、go 进行导航   |
  | `path`        | route           | 当前路径                 | `/user/123`                  |
  | `params`      | route           | 动态参数                 | `{ id: "123" }`              |
  | `query`       | route           | 查询参数                 | `{ type: "admin" }`          |
  | `name`        | route           | 当前路由名称             | `"UserDetail"`               |
  | `push()`      | router          | 跳转到某个路由           | 可用于页面跳转               |
  | `go(n)`       | router          | 控制浏览器历史前进后退   | 类似 `window.history.go(n)`  |

* 导航守卫

  * `router.beforeEach(async (to, from) => {})`：每次路由跳转前执行，用来控制页面访问权限、登录验证、数据准备等

简单实例

```html
=============================== ./src/router/index.js ===============================
import Home from "../views/Home.vue";

const SimpleRoutes = [
	{ path: '/', component: Home},
    { path: '/hello', component: Hello},
	{ path: '/haha/:id', component: ()=> import('../views/Haha.vue')}
]
const router = createRouter({
    history: createWebHistory(),
    routes:SimpleRoutes
})
export default router
=============================== ./src/main.js ===============================
...
import App from './App.vue'
import {createApp} from 'vue'
import router from './router'

let app = createApp(App);
app.use(router);
app.mount('#app');
=============================== ./src/App.vue ===============================
<template>
    <router-link to="/">首页</router-link>
    <router-link to="/hello">hello</router-link>
    <router-view/>
</template>

=============================== ./src/view/Haha.vue ===============================
<template>
    <h1>{{$route}}</h1>
</template>
```

## Axios

> Axios官方中文文档：https://www.axios-http.cn/docs/intro

常用基本配置

```javascript
//基本定义
import axios from 'axios'
axios.
const http = axios.create({
    baseURL: import.
    timeout: 10000,
});
export defualt http;

//配置拦截器，只拦截http请求
http.interceptors.request.use(function (config) {
    // 在发送请求之前做些什么
    return config;
  }, function (error) {
    // 对请求错误做些什么
    return Promise.reject(error);
  });

// 添加响应拦截器，只拦截http响应
http.interceptors.response.use(function (response) {
    // 2xx 范围内的状态码都会触发该函数。
    // 对响应数据做点什么
    return response;
  }, function (error) {
    // 超出 2xx 范围的状态码都会触发该函数。
    // 对响应错误做点什么
    return Promise.reject(error);
  });
```

## Pinia

> Pinia官方中文文档：https://pinia.vuejs.org/zh/getting-started.html

常用基本配置：

```javascript
// ./store/index.js
import {createPinia} from 'pinia'
const store = createPinia();
export defualt store;

//main.js
const app = createApp();
app.use(store);

//选项式写法（option）
//  ./store/user.js
import { login, logout, getInfo } from '@/api/login'
defineStore('user', {
    state: () => ({
        token: getToken(),
        id: '',
        name: '',
        avatar: '',
        roles: [],
        permissions: []
    }),
    actions: {
        login(userInfo) {
            const username = userInfo.username.trim();
            const password = userInfo.password;
            const code = userInfo.code;
            const uuid = userInfo.uuid;
            return new Promise((resolve, reject) => {
                login(username, password, code, uuid).then(resp => {
                    setToken(resp.token);
                    this.token = resp.token;
                    resolve();
                }).catch(err => {
                    reject(err)
                })
            })
        }
    }
});
```

# ES6

> ECMAScript2015标准

原始数据类型：

| 数据类型  | 示例                                   | 说明                                            |
| --------- | -------------------------------------- | ----------------------------------------------- |
| String    | const str1 = 'Hello';                  | ''或""定义，不可修改                            |
| Number    | const num1 = 42.14;                    | 整数或浮点数                                    |
| Boolean   | const isTrue = true;                   | 只有true和false两个值                           |
| null      | let empty = null;                      | 表示”无“或”空“；类型为Object                    |
| undefined | let x;                                 | 声明变量但未赋值；函数没有返回值时返回undefined |
| Symbol    | const sym1 = Symbol('description')     | 唯一且不可变；两个Symbol值一样时===返回false    |
| BigInt    | const big1 = BigInt("123456789101112") | 任意精度；数字后加`n`或BigInt()创建             |

对象类型：

| 数据类型                 | 示例                                                         | 说明                                                         |
| ------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Object                   | const person = {name:"tom", age:19, greet(){console.log('hello, ${this.name}')}} | 可定义任意初始类型和方法                                     |
| Array                    | const arr = ['a', 'b', 'c'];  arr.push('d')                  | 有序的数据集合                                               |
| Set                      | const set = new Set();                                       | 值唯一，按照插入顺序排序                                     |
| Map                      | const map = new Map();                                       | 键值对，键可为任意类型                                       |
| Date                     | const now = new Date();                                      | 时期和时间                                                   |
| RegExp                   | const regex = /ab+c/i;   匹配abc、aBc、abbc                  | 用于模式匹配和文本搜索替换                                   |
| WeakSet & WeakMap        | const weakSet = new WeakSet();   const weakMap = new WeakMap(); | 弱引用集合，键必须是对象                                     |
| TypedArray & ArrayBuffer | const buffer = new ArrayBuffer(8);    const int32View = new Int32Array(buffer); | 处理二进制数据 TypedArray是类数组视图，ArrayBuffer是原始二进制缓冲区 |
| DataView                 | const buffer = new ArrayBuffer(16);      const view = new DataView(buffer); | 另一种访问ArrayBuffer的方式                                  |

## 基础语法

* **let、const用法、解构表达式**

```javascript
let a = 1;    //局部变量，不可重复声明
const b = 2;  //局部常量，不可重复赋值

//解构表达式
let arr = [1,2,3];
let [x,y,z] = arr;

let person = {
    name:"zhangsan",
    age:18,
    email:"xxxxxxxxxx"
}
let {name, age} = person
```

* **链判断**

```javascript
let message = null;
let content = message?.body?.user?.firstName || 'defualt';
console.log(content)
```

* **参数默认值**

```javascript
function add(a, b = 5){
    return a + b;
}
console.log(add(1))  //b为默认值5，有默认值的参数只能在函数声明的最后面
```

* **箭头函数**

```javascript
let print1 = (arg) => {
    console.log(arg)
    console.log(arg)
}
print1(3333)

let sum = (a,b) => {
    let x = a + b;
    console.log(x);
}
sum(1, 5)
```

* **模板字符串**

```javascript
let info = `你好，我的名字是：${name}, 年龄是：${age}，邮箱是：${person.email}`
```

* **Promise API**：待定（pending）、已兑现（fulfilled）、已拒绝（rejected）

```javascript
//异步对象，声明后在后台运行，运用 .then 接收异步返回数据。类似CompletableFuture
let promise = fetch("www.baidu.com");
promise.catch();   //操作失败后
promise.then(response => {
    //操作成功后
    response.json().then(data => {
        //json()同样也是返回promise对象
        console.log(data)
    })
});

//自定义Promise
function get(url) {
    //案例：改造同步请求为异步请求
    return new Promise((resolve, reject) => {
        $.ajax({
            url: url,
            type: "GET",
            success(result) {
                resolve(result);
            },
            error(error) {
                reject(error);
            }
        })
    })
}
get("www.baidu.com").then(resp => {}).catch(err => {})
```

* **async 与 await**

```javascript
//普通方法变异步方法
async function func1() {
    let x = 101;
    if (x % 2 === 0) {
        return x;
    } else {
        throw new Error("x不是偶数")
    }
}
func1().then(resp => {}).catch(err => {})

//普通方法中的异步方法变同步方法
async function func2() {
    let promise1 = await fetch(url);
    let promise2 = await promise1.json();
    console.log(promise2.data)
}
//无优化方法
function func3() {
    let promise = fetch(url);
    promise.then(resp => {
        let jPromise = resp.json();
        jPromise.then(jdata => {
            console.log(jdata)
        })
    })
}
```

* **export 和 export default**

```javascript
//export
export function func(){}
export const a = {}

//export defualt
const store = createPinia();
export defualt store
```

# Vite

> 快速搭建前端项目脚手架、统一工程化规范、自动化构建和部署
>
> 官方网站：https://cn.vitejs.dev

```shell
npm create vite
```

* package.json：包管理json文件

```json
{
    ...,
    "scripts":{
    	"dev": "vite",
        "build:prod": "vite build",
        "build:stage": "vite build --mode staging",
        "preview": "vite preview"
	},
    ...
}
```

* vite.config.js：服务器配置、别名配置、构建配置、环境变量配置、其他配置

# HTML

### 1. **基础结构标签**

- `<html>`：HTML 文档的根元素，包含整个网页内容。
- `<head>`：包含文档的元数据（如标题、链接、脚本等）。
- `<title>`：定义网页的标题（显示在浏览器标签页中）。
- `<body>`：定义网页的主体，所有可见的内容都放在此标签中。
- `<meta>`：定义文档的元信息（如字符集、描述、关键字等）。

### 2. **文本格式化标签**

- `<h1> - <h6>`：定义标题（`h1` 最大，`h6` 最小）。
- `<p>`：定义段落。
- `<br>`：换行（无闭合标签）。
- `<hr>`：水平分割线（无闭合标签）。
- `<strong>`：加粗文本。
- `<em>`：斜体文本，表示强调。
- `<b>`：加粗文本（无强调语义）。
- `<i>`：斜体文本（无强调语义）。
- `<u>`：下划线文本。
- `<small>`：定义较小的文本。
- `<mark>`：定义标记或高亮文本。

### 3. **列表标签**

- `<ul>`：无序列表。
- `<ol>`：有序列表。
- `<li>`：列表项，用于 `<ul>` 或 `<ol>` 标签中。
- `<dl>`：定义列表（用于描述术语列表）。
- `<dt>`：定义列表中的项目。
- `<dd>`：定义项目的描述。

### 4. **链接与媒体标签**

- `<a>`：定义超链接。
- `<img>`：定义图像。
- `<audio>`：嵌入音频文件。
- `<video>`：嵌入视频文件。
- `<source>`：指定媒体（如视频或音频）的多个资源。
- `<iframe>`：嵌入其他网页。

### 5. **表格标签**

- `<table>`：定义表格。
- `<tr>`：定义表格行。
- `<td>`：定义表格单元格。
- `<th>`：定义表头单元格（通常加粗）。
- `<thead>`：定义表格头部。
- `<tbody>`：定义表格主体。
- `<tfoot>`：定义表格尾部。
- `<caption>`：为表格添加标题。

### 6. **表单标签**

- `<form>`：定义用户输入表单。
- `<input>`：定义输入字段。
- `<label>`：为输入元素定义标签。
- `<textarea>`：多行文本输入框。
- `<button>`：定义按钮。
- `<select>`：下拉列表。
- `<option>`：下拉列表中的选项。
- `<fieldset>`：将表单元素分组。
- `<legend>`：为 `<fieldset>` 定义标题。

### 7. **语义标签**

- `<header>`：定义页面或区域的头部内容。
- `<nav>`：定义导航链接。
- `<article>`：定义文章内容。
- `<section>`：定义页面中的一个独立的内容块。
- `<footer>`：定义页面或区域的底部内容。
- `<aside>`：定义侧栏内容。
- `<main>`：定义文档的主要内容。
- `<figure>`：用于组合图像与其说明。
- `<figcaption>`：定义图像的说明。

### 8. **多媒体与嵌入**

- `<canvas>`：用于绘制图形的画布元素。
- `<svg>`：用于绘制矢量图形。
- `<embed>`：嵌入外部内容，如插件。
- `<object>`：用于嵌入多媒体内容或其他网页对象。

### 9. **表格相关标签**

- `<colgroup>`：定义表格中列的组。
- `<col>`：定义表格中单列的属性。

### 10. **其他常用标签**

- `<div>`：定义一个块级元素，用于布局或包裹内容。
- `<span>`：定义一个行内元素，用于包裹小范围的文本或其他行内元素。
- `<script>`：定义客户端脚本（如 JavaScript）。
- `<link>`：定义与外部资源的关系（如样式表）。
- `<style>`：用于定义页面内联的 CSS 样式。

## 标签常用属性

### 1. **通用属性**（适用于大多数 HTML 元素）
- `id`：定义元素的唯一标识符。
  
  ```html
  <div id="header"></div>
  ```
- `class`：为元素定义一个或多个类名，通常用于 CSS 和 JavaScript。
  ```html
  <p class="text-large text-bold"></p>
  ```
- `style`：用于内联定义 CSS 样式。
  ```html
  <div style="color: red;"></div>
  ```
- `title`：为元素添加说明信息，当鼠标悬停时显示。
  ```html
  <button title="Submit the form">Submit</button>
  ```
- `data-*`：定义自定义属性，用于存储与页面或应用逻辑相关的私有数据。
  ```html
  <div data-user-id="12345"></div>
  ```

### 2. **表单元素属性**
- `type`：定义 `<input>` 元素的输入类型（如 `text`、`password`、`email`、`number` 等）。
  ```html
  <input type="email" />
  ```
- `name`：定义表单元素的名称，在表单提交时作为键名使用。
  ```html
  <input type="text" name="username" />
  ```
- `value`：为表单元素设置初始值。
  ```html
  <input type="text" value="John" />
  ```
- `placeholder`：为输入框定义提示文本。
  ```html
  <input type="text" placeholder="Enter your name" />
  ```
- `required`：设置表单元素为必填项。
  ```html
  <input type="text" required />
  ```
- `disabled`：禁用表单元素，使其不可交互。
  
  ```html
  <input type="text" disabled />
  ```
- `readonly`：设置输入框为只读状态。
  ```html
  <input type="text" readonly />
  ```
- `maxlength`：限制输入框可输入的最大字符数。
  ```html
  <input type="text" maxlength="10" />
  ```
- `checked`：预选中的选项（用于 `<input type="checkbox">` 或 `<input type="radio">`）。
  ```html
  <input type="checkbox" checked />
  ```

### 3. **链接和图片属性**
- `href`：定义超链接的目标 URL（用于 `<a>` 标签）。
  ```html
  <a href="https://example.com">Visit Example</a>
  ```
- `target`：定义链接的打开方式，常用值 `_blank`（在新窗口打开）和 `_self`（在当前窗口打开）。
  ```html
  <a href="https://example.com" target="_blank">Visit Example</a>
  ```
- `src`：定义图片或媒体文件的路径（用于 `<img>`、`<audio>`、`<video>` 标签）。
  ```html
  <img src="image.jpg" alt="Sample image" />
  ```
- `alt`：为图片提供替代文本，适用于图片无法显示时或用于屏幕阅读器。
  
  ```html
  <img src="image.jpg" alt="A beautiful sunset" />
  ```
- `width` 和 `height`：设置图片或视频的宽度和高度。
  ```html
  <img src="image.jpg" width="300" height="200" />
  ```

### 4. **多媒体属性**
- `autoplay`：设置音频或视频文件自动播放。
  ```html
  <video src="movie.mp4" autoplay></video>
  ```
- `controls`：为音频或视频元素显示播放控件。
  ```html
  <audio src="song.mp3" controls></audio>
  ```
- `loop`：让音频或视频文件循环播放。
  
  ```html
  <video src="movie.mp4" loop></video>
  ```
- `muted`：静音播放音频或视频文件。
  ```html
  <video src="movie.mp4" muted></video>
  ```

### 5. **表格属性**
- `colspan`：让单元格跨越多列（用于 `<td>` 和 `<th>` 标签）。
  
  ```html
  <td colspan="2">Merged cell</td>
  ```
- `rowspan`：让单元格跨越多行。
  
  ```html
  <td rowspan="2">Merged cell</td>
  ```

### 6. **事件属性**
- `onclick`：在点击时触发 JavaScript 函数。
  
  ```html
  <button onclick="alert('Button clicked!')">Click me</button>
  ```
- `onchange`：在表单元素的值发生变化时触发 JavaScript 事件。
  ```html
  <input type="text" onchange="console.log('Changed!')" />
  ```
- `onmouseover`：在鼠标悬停时触发事件。
  ```html
  <div onmouseover="console.log('Mouse hovered')">Hover over me</div>
  ```

### 7. **语言和方向属性**
- `lang`：定义文档或元素的语言。
  
  ```html
  <p lang="en">This is an English text.</p>
  ```
- `dir`：定义文本的方向，常用于 RTL（从右到左的语言，如阿拉伯语）。
  ```html
  <p dir="rtl">مرحبا بك</p>
  ```

### 8. **SEO 和文档属性**
- `meta charset`：定义页面的字符集（通常设置为 UTF-8）。
  
  ```html
  <meta charset="UTF-8" />
  ```
- `meta name="description"`：提供页面的简短描述，用于搜索引擎优化。
  ```html
  <meta name="description" content="A brief description of the page" />
  ```

### 9. **其他常用属性**
- `hidden`：将元素隐藏（仍然在 DOM 中，但不会显示）。
  
  ```html
  <p hidden>This text is hidden</p>
  ```
- `draggable`：指定元素是否可以被拖动。
  
  ```html
  <img src="image.jpg" draggable="true" />
  ```

# CSS

### 1. 布局相关

- **`display`**: 控制元素的显示方式。
  - **`block`**: 元素占据一整行，后面的元素会换行。
  - **`inline`**: 元素只占据其内容的宽度，不会换行。
  - **`flex`**: 使容器成为 Flexbox 布局，允许子元素灵活排列。
  - **`grid`**: 使容器成为 Grid 布局，使用行和列进行布局。
  - **`none`**: 隐藏元素，不占据空间。

- **`position`**: 控制元素的定位方式。
  - **`static`**: 默认值，元素按照文档流定位。
  - **`relative`**: 相对定位，元素相对于其正常位置偏移。
  - **`absolute`**: 绝对定位，元素相对于最近的定位祖先进行定位。
  - **`fixed`**: 固定定位，元素相对于浏览器窗口进行定位。
  - **`sticky`**: 结合相对和固定定位，滚动到特定位置时固定。

- **`margin`**: 设置元素的外边距，控制元素之间的间距。
  - 例：`margin: 10px;` 表示所有方向的外边距为 10px。

- **`padding`**: 设置元素的内边距，控制内容与边框之间的间距。
  - 例：`padding: 10px;` 表示所有方向的内边距为 10px。

- **`width` 和 `height`**: 设置元素的宽度和高度。
  - 例：`width: 100px; height: 50px;`，设置具体的尺寸。

- **`overflow`**: 控制内容溢出时的处理方式。
  - **`visible`**: 内容溢出时可见。
  - **`hidden`**: 溢出内容不可见。
  - **`scroll`**: 溢出内容可滚动。
  - **`auto`**: 根据需要显示滚动条。

### 2. 背景与边框

- **`background-color`**: 设置元素的背景颜色。
  - 例：`background-color: red;` 表示背景为红色。

- **`background-image`**: 设置元素的背景图像。
  - 例：`background-image: url('image.jpg');`。

- **`border`**: 设置元素的边框。
  - 例：`border: 1px solid black;` 表示 1 像素的黑色实线边框。

- **`border-radius`**: 设置元素的圆角。
  - 例：`border-radius: 5px;` 表示边角圆润 5 像素。

### 3. 文本相关

- **`color`**: 设置文本的颜色。
  - 例：`color: blue;` 表示文本颜色为蓝色。

- **`font-size`**: 设置字体大小。
  - 例：`font-size: 16px;`。

- **`font-family`**: 设置字体类型。
  - 例：`font-family: 'Arial', sans-serif;`。

- **`font-weight`**: 设置字体的粗细。
  - 例：`font-weight: bold;` 表示粗体。

- **`line-height`**: 设置行高，控制文本行之间的间距。
  - 例：`line-height: 1.5;`。

- **`text-align`**: 设置文本对齐方式。
  - 例：`text-align: center;` 表示居中对齐。

### 4. 列表与表格

- **`list-style`**: 设置列表样式，如项目符号或数字。
  - 例：`list-style: disc;` 表示无序列表的项目符号为圆点。

- **`border-collapse`**: 控制表格边框的合并方式。
  - 例：`border-collapse: collapse;` 表示合并相邻的单元格边框。

### 5. 特效与变换

- **`opacity`**: 设置元素的不透明度。
  - 例：`opacity: 0.5;` 表示元素半透明。

- **`transform`**: 应用二维或三维转换，如旋转、缩放等。
  - 例：`transform: rotate(45deg);` 表示顺时针旋转 45 度。

- **`transition`**: 设置过渡效果，控制属性变化时的动画效果。
  - 例：`transition: all 0.3s ease;` 表示所有属性变化时，持续 0.3 秒，使用平滑过渡。

- **`box-shadow`**: 添加阴影效果，增加视觉深度。
  - 例：`box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.5);` 表示添加一个 2 像素偏移、5 像素模糊的半透明黑色阴影。

### 6. Flexbox 与 Grid

- **Flexbox**:
  - **`display: flex;`**: 启用 Flexbox 布局，使子元素自动对齐和分配空间。
  - **`flex-direction`**: 设置主轴方向（`row` 横向，`column` 纵向）。
  - **`justify-content`**: 设置主轴对齐方式，如 `center` 居中、`space-between` 均匀分布。
  - **`align-items`**: 设置交叉轴对齐方式，如 `flex-start` 上对齐、`center` 垂直居中。

- **Grid**:
  - **`display: grid;`**: 启用 Grid 布局，使用行和列布局。
  - **`grid-template-columns`**: 定义列的大小，如 `grid-template-columns: 1fr 2fr;` 表示第一列占 1 份，第二列占 2 份。
  - **`grid-template-rows`**: 定义行的大小。
  - **`grid-area`**: 定义元素在网格中的位置。

### 7. 伪类与伪元素

- **`:hover`**: 当鼠标悬停在元素上时应用样式，如 `color: red;`。
- **`:active`**: 当元素被激活（点击时）应用样式。
- **`:focus`**: 当元素获得焦点时应用样式。
- **`::before` 和 `::after`**: 在元素前后插入内容，常用于装饰性效果。

### 8. 媒体查询

- **`@media`**: 用于响应式设计，根据设备特性（如屏幕宽度）应用样式。
  - 例：
    ```css
    @media (max-width: 600px) {
      body {
        background-color: lightblue; /* 在宽度小于 600px 时改变背景色 */
      }
    }
    ```

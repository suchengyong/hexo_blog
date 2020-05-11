---
title: 如何使用Nuxt.js入门
date: 2018-03-02 15:03:12
tags: Nuxt
categories: 前端开发 
thumbnail: http://pic29.photophoto.cn/20131204/0034034499213463_b.jpg
---

* 初学者模板或使用vue-cli安装它：

```
vue init nuxt/starter project-name

```

* 如果vue-cli没有安装，请安装npm install -g vue-cli

* 然后安装依赖关系：

```
$ cd <project-name>
$ npm install 或者用 yarn install

```

* 并通过以下方式启动项目

```
npm run dev

```

* 该应用程序现在在http：// localhost：3000上运行

* Nuxt.js将监听pages目录中的文件更改，因此在添加新页面时不需要重新启动应用程序。

* 目录结构介绍

 ### 资产目录

 * 该assets目录包含您未编译的资产，如Less，Sass或JavaScript
 
 * 默认情况下，vue-loader会使用css-loader和Vue模板编译器自动处理样式和模板文件。在此编译过程中，所有资产的URL，例如<img src="...">，background: url(...)和CSS @import的解析为模块依赖关系。 


 * 如有下目录结构：

```
-| assets/
----| image.png
-| pages/
----| index.vue

```

* 你的CSS中，如果你使用url('~/assets/image.png')，它会被翻译成require('~/assets/image.png')。

* 或者如果在pages/index.vue你使用

```
<template>
  <img src="~/assets/image.png">
</template>

```

### 静态的

* 如果您不想使用该assets目录中的webpacked Assets，则可以static在项目根目录中创建并使用该目录。

* 这些文件将由Nuxt自动提供，并可在您的项目根URL中访问。

* 此选项对于像文件有帮助robots.txt，sitemap.xml或CNAME（像GitHub的页面）。

* 从您的代码中，您可以使用/URL 来引用这些文件：

```
<!-- 这是来自Static目录中图片使用方法 image from static directory -->
<img src="/my-image.png"/>

<!-- 这是来自assets目录中图片使用方法 webpacked image from assets directory -->
<img src="~/assets/my-image-2.png"/>

```

### 组件目录

* 该components目录包含您的Vue.js组件。Nuxt.js不会增加这些组件的数据方法

### 布局目录

* 该layouts目录包含您的应用程序布局。该目录不能被重命名。视图部分描述了在Nuxt.js应用程序（文档，布局，页面和HTML头）中为特定路线配置数据和视图所需的全部内容。

![图片](https://nuxtjs.org/nuxt-views-schema.png)

### 文件

* 要扩展HTML模板，请app.html在项目的根目录下创建。默认模板是：

```
<!DOCTYPE html>
<html {{ HTML_ATTRS }}>
  <head>
    {{ HEAD }}
  </head>
  <body {{ BODY_ATTRS }}>
    {{ APP }}
  </body>
</html>

```

* 一个例子是为IE添加条件CSS类html模板：

```
<!DOCTYPE html>
<!--[if IE 9]><html lang="en-US" class="lt-ie9 ie9" {{ HTML_ATTRS }}><![endif]-->
<!--[if (gt IE 9)|!(IE)]><!--><html {{ HTML_ATTRS }}><!--<![endif]-->
  <head>
    {{ HEAD }}
  </head>
  <body {{ BODY_ATTRS }}>
    {{ APP }}
  </body>
</html>

```
### 布局

* Nuxt.js允许您扩展主布局或通过在layouts目录中添加它们来创建自定义布局。

### 默认布局

* 您可以通过添加layouts/default.vue文件来扩展主布局。

* 确保<nuxt/>在创建布局以显示页面组件时添加组件。

* 默认的布局源代码是：

```
<template>
  <nuxt/>
</template>

```

### 错误页面

* 您可以通过添加layouts/error.vue文件来自定义错误页面。

* 这种布局是特殊的，因为你应该不包括<nuxt/>它的模板中。必须看到这布局发生错误时（一个组件显示404，500等）。

* GitHub上提供了默认的错误页面源代码。

```
<template>
<div class="__nuxt-error-page">
<div class="error">
  <svg xmlns="http://www.w3.org/2000/svg" width="90" height="90" fill="#DBE1EC" viewBox="0 0 48 48"><path d="M22 30h4v4h-4zm0-16h4v12h-4zm1.99-10C12.94 4 4 12.95 4 24s8.94 20 19.99 20S44 35.05 44 24 35.04 4 23.99 4zM24 40c-8.84 0-16-7.16-16-16S15.16 8 24 8s16 7.16 16 16-7.16 16-16 16z"/></svg>

  <div class="title">{{ message }}</div>
  <p class="description" v-if="statusCode === 404">
    <nuxt-link class="error-link" to="/"><%= messages.back_to_home %></nuxt-link>
  </p>
  <% if(debug) { %>
  <p class="description" v-else><%= messages.client_error_details %></p>
  <% } %>

  <div class="logo">
    <a href="https://nuxtjs.org" target="_blank" rel="noopener"><%= messages.nuxtjs %></a>
  </div>
</div>
</div>
</template>

<script>
export default {
  name: 'nuxt-error',
  props: ['error'],
  head () {
    return {
      title: this.message,
      meta: [
        {
          name: 'viewport',
          content: 'width=device-width,initial-scale=1.0,minimum-scale=1.0,maximum-scale=1.0,user-scalable=no'
        }
      ]
    }
  },
  <% if(debug) { %>
  // Only on debug mode
  data () {
    return {
      mounted: false
    }
  },
  mounted () {
    this.mounted = true
  },
  created () {
    console.error(this.error)
  },
  watch: {
    error(newErr) {
      if(newErr) {
        console.error(newErr)
      }
    }
  },
  <% } %>
  computed: {
    statusCode () {
      return (this.error && this.error.statusCode) || 500
    },
    message () {
      return this.error.message || '<%= messages.client_error %>'
    }
  }
}
</script>

<style>
.__nuxt-error-page {
  padding: 1rem;
  background: #F7F8FB;
  color: #47494E;
  text-align: center;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  font-family: sans-serif;
  font-weight: 100 !important;
  -ms-text-size-adjust: 100%;
  -webkit-text-size-adjust: 100%;
  -webkit-font-smoothing: antialiased; 
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
}
.__nuxt-error-page .error {
  max-width: 450px;
}
.__nuxt-error-page .title {
  font-size: 1.5rem;
  margin-top: 15px;
  color: #47494E;
  margin-bottom: 8px; 
}
.__nuxt-error-page .description {
  color: #7F828B;
  line-height: 21px;
  margin-bottom: 10px;
}
.__nuxt-error-page a {
  color: #7F828B !important;
  text-decoration: none;
}
.__nuxt-error-page .logo {
  position: fixed;
  left: 12px;
  bottom: 12px;
}
</style>

```

* 自定义错误页面的示例layouts/error.vue：

```
<template>
  <div class="container">
    <h1 v-if="error.statusCode === 404">Page not found</h1>
    <h1 v-else>An error occurred</h1>
    <nuxt-link to="/">Home page</nuxt-link>
  </div>
</template>

<script>
export default {
  props: ['error'],
  layout: 'blog' // you can set a custom layout for the error page
}
</script>

```

### 自定义布局

* 目录中的每个文件（第一级）layouts都将创建一个可通过layout页面组件中的属性访问的自定义布局。

* 确保<nuxt/>在创建布局以显示页面组件时添加组件。

* 例子layouts/blog.vue：

```
<template>
  <div>
    <div>My blog navigation bar here</div>
    <nuxt/>
  </div>
</template>

```

* 然后pages/posts.vue，你可以告诉Nuxt.js使用你的自定义布局：

```
<script>
export default {
  layout: 'blog'
}
</script>

```
### 布局属性

* 布局目录中的每个文件（第一层）都将创建一个可通过页面组件中的布局属性访问的自定义布局。
* 类型： String或者Function（默认：'default'）
* 使用layout页面组件中的键来定义要使用的布局：

```
export default {
  layout: 'blog',
  // OR
  layout (context) {
    return 'blog'
  }
}

```

### 网页

* 每个页面组件都是一个Vue组件，但是Nuxt.js添加了特殊键以尽可能简化通用应用程序的开发

```
<template>
  <h1 class="red">Hello {{ name }}!</h1>
</template>

<script>
export default {
  asyncData (context) {
    // 每次加载组件之前调用
    return { name: 'World' }
  },
  fetch () {
    // 用于在呈现页面之前填充数据中状态管理vuex中数据仓库store。它就像data方法，但它不设置组件数据。请参阅API页面fetch文档
  },
  head () {
    // 为当前页面设置特定的标签<meta>
  },
  // and more functionality to discover
  ...
}
</script>

<style>
.red {
  color: red;
}
</style>

```

### 具体的页面方法api

| 属性 | 描述 |
| :-: | :-: | 
| asyncData |	最重要的一个键, 支持 异步数据处理，另外该方法的第一个参数为当前页面组件的 上下文对象。|
| fetch |与 asyncData 方法类似，用于在渲染页面之前获取数据填充应用的状态树（store）。不同的是 fetch 方法不会设置组件的数据。详情请参考 关于fetch方法的文档。|
| head | 配置当前页面的 Meta 标签, 详情参考 页面头部配置API。|
| layout | 指定当前页面使用的布局（layouts 根目录下的布局文件）。详情请参考 关于 布局 的文档。|
| transition |指定页面切换的过渡动效, 详情请参考 页面过渡动效。|
| scrollToTop |	布尔值，默认: false。 用于判定渲染页面前是否需要将当前页面滚动至顶部。这个配置用于 嵌套路由的应用场景。|
| validate |	校验方法用于校验 动态路由的参数。|
| middleware | 指定页面的中间件，中间件会在页面渲染之前被调用， 请参考 路由中间件。|

### 异步数据

* Nuxt.js 扩展了 Vue.js，增加了一个叫 asyncData 的方法，使得我们可以在设置组件的数据之前能异步获取或处理数据。

* asyncData 方法

------------------------------------------------

* asyncData方法会在组件（限于页面组件）每次加载之前被调用。它可以在服务端或路由更新之前被调用。 在这个方法被调用的时候，第一个参数被设定为当前页面的上下文对象，你可以利用 asyncData方法来获取数据，Nuxt.js 会将 asyncData 返回的数据融合组件 data 方法返回的数据一并返回给当前组件。


* <p style="color:#fff;background-color:red;">注意：由于asyncData方法是在组件 初始化 前被调用的，所以在方法内是没有办法通过 this 来引用组件的实例对象</p>。

* Nuxt.js 提供了几种不同的方法来使用 asyncData 方法，你可以选择自己熟悉的一种来用：

>1.返回一个 Promise, nuxt.js会等待该Promise被解析之后才会设置组件的数据，从而渲染组件.
>2.使用 async 或 await 
>3.为第二个参数指定一个回调函数. 注：该回调函数需符合通用的 NodeJs 回调函数的形式: callback(err, data)

* 返回 Promise

```
export default {
  asyncData ({ params }) {
    return axios.get(`https://my-api/posts/${params.id}`)
    .then((res) => {
      return { title: res.data.title }
    })
  }
}

```
* 使用 async或await

```
export default {
  async asyncData ({ params }) {
    let { data } = await axios.get(`https://my-api/posts/${params.id}`)
    return { title: data.title }
  }
}

```
* 使用 回调函数

```
export default {
  asyncData ({ params }, callback) {
    axios.get(`https://my-api/posts/${params.id}`)
    .then((res) => {
      callback(null, { title: res.data.title })
    })
  }
}

```
* 返回 对象
* 如果组件的数据不需要异步获取或处理，可以直接返回指定的字面对象作为组件的数据

```
export default {
  data () {
    return { foo: 'bar' }
  }
}

```
# 错误处理

* Nuxt.js 在上下文对象context中提供了一个 error(params) 方法，你可以通过调用该方法来显示错误信息页面。params.statusCode 可用于指定服务端返回的请求状态码。

```
export default {
  asyncData ({ params, error }) {
    return axios.get(`https://my-api/posts/${params.id}`)
    .then((res) => {
      return { title: res.data.title }
    })
    .catch((e) => {
      error({ statusCode: 404, message: 'Post not found' })
    })
  }
}

```
* 如果你使用 回调函数 的方式, 你可以将错误的信息对象直接传给该回调函数， Nuxt.js 内部会自动调用 error 方法：

```
export default {
  asyncData ({ params }, callback) {
    axios.get(`https://my-api/posts/${params.id}`)
    .then((res) => {
      callback(null, { title: res.data.title })
    })
    .catch((e) => {
      callback({ statusCode: 404, message: 'Post not found' })
    })
  }
}

```
### 插件

* Nuxt.js允许您在实例化根vue.js应用程序之前定义要运行的JavaScript插件。这在使用自己的库或外部模块时特别有用。

### 注意

>知道在任何Vue 实例生命周期中.
>仅从客户端和服务器端调用钩子
>beforeCreate和created钩子是非常重要的。
>所有其他的钩子只能从客户端调用。

### 使用第三方模块

* 我们可以在应用中使用第三方模块，一个典型的例子是在客户端和服务端使用 axios 做 HTTP 请求。

* 首先我们需要安装 npm 包：

```
npm install --save axios

```
* 然后在页面内可以这样使用：

```
<template>
  <h1>{{ title }}</h1>
</template>

<script>
import axios from 'axios'

export default {
  async asyncData ({ params }) {
    let { data } = await axios.get(`https://my-api/posts/${params.id}`)
    return { title: data.title }
  }
}
</script>

```

* 有一个值得注意的问题是，如果我们在另外一个页面内也引用了 axios，那么在应用打包发布的时候 axios 会被打包两次，而实际上我们只需要打包一次。这个问题可以通过在 nuxt.config.js 里面配置 build.vendor 来解决：

```
module.exports = {
  build: {
    vendor: ['axios']
  }
}

```

* 经过上面的配置后，我们可以在任何页面里面引入 axios 而不用担心它会被重复打包。

### 使用 Vue 插件

* 假如我们想使用 vue-notifications 显示应用的通知信息，我们需要在程序运行前配置好这个插件。

* 首先增加文件 plugins/vue-notifications.js：

```
import Vue from 'vue'
import VueNotifications from 'vue-notifications'

Vue.use(VueNotifications)

```

* 然后, 在 nuxt.config.js 内配置 plugins 如下：

```
module.exports = {
  plugins: ['~/plugins/vue-notifications']
}

```

* 实际上， vue-notifications 会被打包至应用的脚本代码里， 但是它属于第三方库，我们理应将它打包至库文件里以获得更好的缓存效果。（译者注：应用代码比库文件修改频繁，应尽量将第三方库打包至单独的文件中去）。

* 我们可以更新 nuxt.config.js 文件，在 vendor 构建配置项里添加 vue-notifications：

```
module.exports = {
  build: {
    vendor: ['~/plugins/vue-notifications']
  },
  plugins: ['~/plugins/vue-notifications']
}

```

* 只在浏览器里使用的插件

----------------------------------------------------

* 有些插件可能只是在浏览器里使用，所以你可以用 ssr: false 变量来配置插件只从客户端还是服务端运行。

* 举个例子：

* nuxt.config.js:

```
module.exports = {
  plugins: [
    { src: '~/plugins/vue-notifications', ssr: false }
  ]
}

```

* plugins/vue-notifications.js:

```
import Vue from 'vue'
import VueNotifications from 'vue-notifications'

Vue.use(VueNotifications)

```

* 同样地，如果有些脚本库你只想在服务端使用，在 Webpack 打包 server.bundle.js 文件的时候会将 process.server 变量设置成 true。

### Vuex 状态树

* 对于每个大项目来说，使用状态树 (store) 管理状态 (state) 十分有必要。这就是为什么 Nuxt.js 内核实现了 Vuex。

### 使用状态树

* Nuxt.js 会尝试找到应用根目录下的 store 目录，如果该目录存在，它将做以下的事情：

>1.引用 vuex 模块
>2.将 vuex 模块 加到 vendors 构建配置中去
>3.设置 Vue 根实例的 store 配置项
>4.Nuxt.js 支持两种使用 store 的方式，你可以择一使用：

** 普通方式：**  store/index.js 返回一个 Vuex.Store 实例
** 模块方式：** store 目录下的每个 .js 文件会被转换成为状态树指定命名的子模块 （当然，index 是根模块）

### 普通方式

* 使用普通方式的状态树，需要添加 store/index.js 文件，并对外暴露一个 Vuex.Store 实例：

```
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

const store = () => new Vuex.Store({

  state: {
    counter: 0
  },
  mutations: {
    increment (state) {
      state.counter++
    }
  }
})

export default store

```

* Nuxt.js 内置引用了 vuex 模块，所以不需要额外安装。

* 现在我们可以在组件里面通过 this.$store 来使用状态树：

```
<template>
  <button @click="$store.commit('increment')">{{ $store.state.counter }}</button>
</template>

```

### 模块方式

* 状态树还可以拆分成为模块，store 目录下的每个 .js 文件会被转换成为状态树指定命名的子模块

* 使用状态树模块化的方式，store/index.js 不需要返回 Vuex.Store 实例，而应该直接将 state、mutations 和 actions 暴露出来：

```
export const state = () => ({
  counter: 0
})

export const mutations = {
  increment (state) {
    state.counter++
  }
}

```

* 其他的模块文件也需要采用类似的方式，如 store/todos.js 文件：

```
export const state = () => ({
  list: []
})

export const mutations = {
  add (state, text) {
    state.list.push({
      text: text,
      done: false
    })
  },
  remove (state, { todo }) {
    state.list.splice(state.list.indexOf(todo), 1)
  },
  toggle (state, todo) {
    todo.done = !todo.done
  }
}

```

* 最终的状态树大概如下：

```
new Vuex.Store({
  state: { counter: 0 },
  mutations: {
    increment (state) {
      state.counter++
    }
  },
  modules: {
    todos: {
      state: {
        list: []
      },
      mutations: {
        add (state, { text }) {
          state.list.push({
            text,
            done: false
          })
        },
        remove (state, { todo }) {
          state.list.splice(state.list.indexOf(todo), 1)
        },
        toggle (state, { todo }) {
          todo.done = !todo.done
        }
      }
    }
  }
})

```

* 在页面组件 pages/todos.vue， 可以像下面这样使用 todos 模块：

```
<template>
  <ul>
    <li v-for="todo in todos">
      <input type="checkbox" :checked="todo.done" @change="toggle(todo)">
      <span :class="{ done: todo.done }">{{ todo.text }}</span>
    </li>
    <li><input placeholder="What needs to be done?" @keyup.enter="addTodo"></li>
  </ul>
</template>
<script>
import { mapMutations } from 'vuex'

export default {
  computed: {
    todos () { return this.$store.state.todos.list }
  },
  methods: {
    addTodo (e) {
      this.$store.commit('todos/add', e.target.value)
      e.target.value = ''
    },
    ...mapMutations({
      toggle: 'todos/toggle'
    })
  }
}
</script>

<style>
.done {
  text-decoration: line-through;
}
</style>

```

* <P style='color:#fff;background-color:#35495e;padding:20px'>你也可以在模块文件里返回Vuex.Store实例,但是这种情况下你需要手工设置应用的状态树</p>


### fetch方法

* fetch方法会在渲染页面前被调用，作用是填充状态树 (store) 数据，与 asyncData 方法类似，不同的是它不会设置组件的数据。

### nuxtServerInit方法

* 如果在状态树中指定了 nuxtServerInit 方法，Nuxt.js 调用它的时候会将页面的上下文对象作为第2个参数传给它（服务端调用时才会酱紫哟）。当我们想将服务端的一些数据传到客户端时，这个方法是非常好用的。

* 举个例子，假设我们服务端的会话状态树里可以通过req.session.user 来访问当前登录的用户。将该登录用户信息传给客户端的状态树，我们只需更新 store/index.js 如下

```
actions: {
  nuxtServerInit ({ commit }, { req }) {
    if (req.session.user) {
      commit('user', req.session.user)
    }
  }
}

```

* 如果你使用状态树模块化的模式，只有主模块（即 store/index.js）适用设置该方法（其他模块设置了也不会被调用）。

* nuxtServerInit 方法接收的上下文对象和 fetch 的一样，但不包括 context.redirect() 和 context.error()。

### 命令列表

| 命令 | 描述 |
| :-: | :-: | 
| nuxt |	启动一个热加载的Web服务器（开发模式） localhost:3000。|
| nuxt build |	利用webpack编译应用，压缩JS和CSS资源（发布用）。|
| nuxt start |	以生成模式启动一个Web服务器 (nuxt build 会先被执行)。|
| nuxt generate |	编译应用，并依据路由配置生成对应的HTML文件 (用于静态站点的部署) |


* 你可以将这些命令添加至 package.json：

```
"scripts": {
  "dev": "nuxt",
  "build": "nuxt build",
  "start": "nuxt start",
  "generate": "nuxt generate"
}

```
* 这样你可以通过 npm run <command> 来执行相应的命令。如: npm run dev。

### 发布部署

* Nuxt.js 提供了两种发布部署应用的方式：服务端渲染应用部署 和 静态应用部署

### 服务端渲染应用部署

* 部署 Nuxt.js 服务端渲染的应用不能直接使用 nuxt 命令，而应该先进行编译构建，然后再启动 Nuxt 服务，可通过以下两个命令来完成：

```
nuxt build
nuxt start

```
* 推荐的 package.json 配置如下：

```
{
  "name": "my-app",
  "dependencies": {
    "nuxt": "latest"
  },
  "scripts": {
    "dev": "nuxt",
    "build": "nuxt build",
    "start": "nuxt start"
  }
}

```

### 静态应用部署

* Nuxt.js 可依据路由配置将应用静态化，使得我们可以将应用部署至任何一个静态站点主机服务商。

* 可利用下面的命令生成应用的静态目录和文件：

```
npm run generate
```
* 这个命令会创建一个 dist 文件夹，所有静态化后的资源文件均在其中。

* 如果你的项目需要用到动态路由，请移步 generate配置API 了解如何让 Nuxt.js 生成此类动态路由的静态文件。

* <P style="color:#fff;background-color:#35495e;padding:20px;">注意：使用 nuxt generate 静态化应用的时候, 传给 asyncData() 和 fetch() 方法的上下文对象 不会包含 req 和 res 两个属性。</p>

### API学习应用

### 1.asyncData 方法

>你可能想要在服务器端获取并渲染数据。Nuxt.js添加了asyncData方法使得你能够在渲染组件之前异步获取数据。
>类型： Function
>asyncData方法会在组件（限于页面组件）每次加载之前被调用。它可以在服务端或路由更新之前被调用。在这个方法被调用的时候，第一个参数被设定为当前页面的上下文对象，你可以利用 asyncData方法来获取数据并返回给当前组件

```
export default {
  data () {
    return { project: 'default' }
  },
  asyncData (context) {
    return { project: 'nuxt' }
  }
}

```
<p style="color:#fff;background-color:#e67e22;padding:20px">注意：由于asyncData方法是在组件 初始化 前被调用的，所以在方法内是没有办法通过 this 来引用组件的实例对象。</P>

### 上下文对象

* context 变量的可用属性一览：

| 属性字段 | 类型 | 可用 | 描述 |
|  :-:    | :-:  | :-:  | :-:  | 
|app|根Vue实例|客户端服务器|包含所有插件的根Vue实例。例如，使用时axios，您可以访问$axios通过context.app.$axios。|
| isClient |	Boolean	| 客户端 & 服务端 |	是否来自客户端渲染|
| isServer |	Boolean	| 客户端 & 服务端	| 是否来自服务端渲染|
| isDev |	Boolean |	客户端 & 服务端	| 是否是开发(dev) 模式，在生产环境的数据缓存中用到|
| route	| vue-router 路由 |	客户端 & 服务端	| vue-router 路由实例。|
| store	| vuex 数据流	| 客户端 & 服务端	| Vuex.Store 实例。只有vuex 数据流存在相关配置时可用。|
| env	| Object |	客户端 & 服务端	| nuxt.config.js 中配置的环境变量, 见 环境变量 api |
| params |	Object	| 客户端 & 服务端	|route.params 的别名|
|query |	Object |	客户端 & 服务端	|route.query 的别名|
| req	| http.Request |	服务端	| Node.js API 的 Request 对象。如果 nuxt 以中间件形式使用的话，这个对象就根据你所使用的框架而定。nuxt generate 不可用。|
| res	| http.Response	|服务端	|Node.js API 的 Response 对象。如果 nuxt 以中间件形式使用的话，这个对象就根据你所使用的框架而定。nuxt generate 不可用。|
| redirect |	Function	|客户端 & 服务端	|用这个方法重定向用户请求到另一个路由。状态码在服务端被使用，默认 302。redirect([status,] path [, query])|
| error	| Function |	客户端 & 服务端	 | 用这个方法展示错误页：error(params)。params 参数应该包含 statusCode 和 message 字段。|

### fetch 方法

* fetch 方法用于在渲染页面前填充应用的状态树（store）数据， 与 asyncData 方法类似，不同的是它不会设置组件的数据
* 类型： Function
* 如果页面组件设置了 fetch 方法，它会在组件每次加载前被调用（在服务端或切换至目标路由之前）。

* fetch 方法的第一个参数是页面组件的上下文对象 context，我们可以用 fetch 方法来获取数据填充应用的状态树。为了让获取过程可以异步，你需要返回一个 Promise，Nuxt.js 会等这个 promise 完成后再渲染组件。

* 例如 pages/index.vue：

```
<template>
  <h1>Stars: {{ $store.state.stars }}</h1>
</template>

<script>
export default {
  fetch ({ store, params }) {
    return axios.get('http://my-api/stars')
    .then((res) => {
      store.commit('setStars', res.data)
    })
  }
}
</script>
```
* 你也可以使用 async 或 await 的模式简化代码如下：

```
<template>
  <h1>Stars: {{ $store.state.stars }}</h1>
</template>

<script>
export default {
  async fetch ({ store, params }) {
    let { data } = await axios.get('http://my-api/stars')
    store.commit('setStars', data)
  }
}
</script>

```
### head 方法

* Nuxt.js 使用了 vue-meta 更新应用的 头部标签(Head) 和 html 属性
* 类型： Object 或 Function
* 使用 head 方法设置当前页面的头部标签。

* 在 head 方法里可通过 this 关键字来获取组件的数据，你可以利用页面组件的数据来设置个性化的 meta 标签

```
<template>
  <h1>{{ title }}</h1>
</template>

<script>
export default {
  data () {
    return {
      title: 'Hello World!'
    }
  },
  head () {
    return {
      title: this.title,
      meta: [
        { hid: 'description', name: 'description', content: 'My custom description' }
      ]
    }
  }
}
</script>

```

* <p style="color:#fff;background-color:#e67e22;padding:20px">注意：为了避免子组件中的meta标签不能正确覆盖父组件中相同的标签而产生重复的现象，建议利用 hid 键为meta标签配一个唯一的标识编号。请阅读关于 vue-meta 的更多信息</P>

### layout 属性

* layouts 根目录下的所有文件都属于个性化布局文件，可以在页面组件中利用 layout 属性来引用。

* 类型： String 或 Function (默认值： 'default')
* 使用 layout 属性来为页面指定使用哪一个布局文件：在上面的例子中,Nuxt.js 会使用 layouts/blog.vue 作为当前页面组件的布局文件。

```
export default {
  layout: 'blog',
  // 或
  layout (context) {
    return 'blog'
  }
}

```

### middleware 属性
* 中间件属性
>类型：String或Array
>项目： String
>为应用程序的特定页面设置中间件。

* 例：pages/secret.vue：

```
<template>
  <h1>Secret page</h1>
</template>

<script>
export default {
  middleware: 'authenticated'
}
</script>
```

* middleware/authenticated.js：

```
export default function ({ store, redirect }) {
  // If the user is not authenticated
  if (!store.state.authenticated) {
    return redirect('/login')
  }
}
```

### scrollToTop属性

* 使用scrollToTop属性可以告诉nuxt.js在呈现页面之前滚动到顶部。

* 类型： Boolean（默认：false）
* 默认情况下，当你转到另一个页面时，Nuxt.js会滚动到顶部，但是对于子路径，Nuxt.js将保持滚动位置。如果你想告诉Nuxt.js在渲染子路径时滚动到顶部，请设置scrollToTop: true：

```
<template>
  <h1>My child component</h1>
</template>

<script>
export default {
  scrollToTop: true
}
</script>
```

### transition 属性

* Nuxt.js 使用 Vue.js 的<transition>组件来实现路由切换时的过渡动效。

* 类型： String 或 Object 或 Function
* 如果想给某个页面自定义过渡特效的话，只要在该页面组件中配置 transition 字段即可：

```
export default {
  // 可以是字符
  transition: ''
  // 或对象
  transition: {}
  // 或函数
  transition (to, from) {}
}
```

### String 字符

* 如果 transition 属性的值类型是字符类型， 相当于设置了动效配置对象的 name 属性：transition.name。

```
export default {
  transition: 'test'
}

```
* Nuxt.js 将使用上面的配置来设置 Vue.js transition 组件，如下：

```
<transition name="test">
```
### Object对象 

* 如果 transition 属性的值类型是对象类型：

```
export default {
  transition: {
    name: 'test',
    mode: 'out-in'
  }
}

```

* Nuxt.js 将使用上面的配置来设置 Vue.js transition 组件，如下：

```
<transition name="test" mode="out-in">
```

* transition 允许配置的字段介绍：


| 属性字段 | 类型 | 默认值 | 描述 |
|  :-:    | :-:  | :-:  | :-:  | 
| name  |String | "page" | 所有路由过渡都会用到的过渡名称。|
| mode  |String | "out-in"  |所有路由都用到的过渡模式，见 Vue.js transition 使用文档。|
| css| Boolean |true | 是否给页面组件根元素添加 CSS 过渡类名。如果值为false，路由过渡时将触发页面组件事件注册的 Javascript 钩子方法。|
| type |  String  | n/a | 指定过滤动效事件的类型，用于判断过渡结束的时间点。值可以是 "transition" 或 "animation"。 默认情况下, Nuxt.js 会自动侦测动效事件的类型。|
| enterClass |  String |  n/a | 目标路由动效开始时的类名。 详情请参考 Vue.js transition 使用文档 。|
| enterToClass  | String |  n/a|  目标路由动效结束时的类名。 详情请参考 Vue.js transition 使用文档 。|
| enterActiveClass  | String |  n/a|  目标路由过渡过程中的类名。详情请参考 Vue.js transition 使用文档 |
| leaveClass  | String |  n/a | 当前路由动效开始时的类名。 详情请参考 Vue.js transition 使用文档 。|
| leaveToClass |  String |  n/a | 当前路由动效结束时的类名。 详情请参考 Vue.js transition 使用文档 。|
| leaveActiveClass  | String |  n/a | 当前路由动效过程中的类名。详情请参考 Vue.js transition 使用文档 |

### validate 方法

* Nuxt.js 可以让你在动态路由对应的页面组件中配置一个校验方法用于校验动态路由参数的有效性。
* 类型： Function

``` 
validate({ params, query }) {
  return true // 如果参数有效
  return false // 参数无效，Nuxt.js 停止渲染当前页面并显示错误页面
}
```

* Nuxt.js 可以让你在动态路由对应的页面组件（本例为： pages/users/_id.vue）中配置一个校验方法
* 如果校验方法返回的值不为 true， Nuxt.js 将自动加载显示 404 错误页面

```
export default {
  validate ({ params }) {
    // Must be a number
    return /^\d+$/.test(params.id)
  }
}

```

* 你也可以在validate 方法中校验 store 的数据 (如果 store 此前在 nuxtServerInit 方法 中被设置了的话):

* 你也可以在validate 方法中校验 store 的数据 (如果 store 此前在 nuxtServerInit 方法 中被设置了的话):
```
export default {
  validate ({ params, store }) {
    // 校验 `params.id` 是否存在
    return store.state.categories.some((category) => category.id === params.id)
  }
}
```
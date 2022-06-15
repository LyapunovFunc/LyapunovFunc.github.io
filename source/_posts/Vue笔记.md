---
title: Vue笔记
data: '2022-6-15'
description: '这里是description'
copyright_author: wxb
copyright_author_href: https://xxxxxx.com
copyright_url: https://xxxxxx.com
copyright_info: 此文章版权归wxb所有，如有转载，请註明来自原作者
---

#  笔记

开启服务器：npm run serve

关闭服务器:两次ctrl+c 

## 脚手架文件结构

	├── node_modules 
	├── public
	│   ├── favicon.ico: 页签图标
	│   └── index.html: 主页面
	├── src
	│   ├── assets: 存放静态资源
	│   │   └── logo.png
	│   │── component: 存放组件
	│   │   └── HelloWorld.vue
	│   │── App.vue: 汇总所有组件
	│   │── main.js: 入口文件
	├── .gitignore: git版本管制忽略的配置
	├── babel.config.js: babel的配置文件
	├── package.json: 应用包配置文件 
	├── README.md: 应用描述文件
	├── package-lock.json：包版本控制文件

## 关于不同版本的Vue

1. vue.js与vue.runtime.xxx.js的区别：
    1. vue.js是完整版的Vue，包含：核心功能 + 模板解析器。
    2. vue.runtime.xxx.js是运行版的Vue，只包含：核心功能；没有模板解析器。
2. 因为vue.runtime.xxx.js没有模板解析器，所以不能使用template这个配置项，需要使用render函数接收到的createElement函数去指定具体内容。

## vue.config.js配置文件

1. 使用vue inspect > output.js可以查看到Vue脚手架的默认配置。
2. 使用vue.config.js可以对脚手架进行个性化定制，详情见：https://cli.vuejs.org/zh

## ref属性

1. 被用来给元素或子组件注册引用信息（id的替代者）
2. 应用在html标签上获取的是真实DOM元素，应用在组件标签上是组件实例对象（vc）
3. 使用方式：
    1. 打标识：```<h1 ref="xxx">.....</h1>``` 或 ```<School ref="xxx"></School>```
    2. 获取：```this.$refs.xxx```

## props配置项

1. 功能：让组件接收外部传过来的数据，传过来的数据都会存储在vc(组件实例对象)身上，可以直接通过this.name获取

2. 传递数据：```<Demo name="xxx"/>```  直接在标签里面传递数据即可

3. 接收数据：

    1. 第一种方式（只接收）：```props:['name'] ```

    2. 第二种方式（限制类型）：```props:{name:String}```

    3. 第三种方式（限制类型、限制必要性、指定默认值）：

    3. 且required和default不会同时限定的
    
        ```js
        props:{
        	name:{
        	type:String, //类型
        	required:true, //必要性
        	default:'老王' //默认值
        	} 
        }
        ```
    
    > 1. 备注：props是只读的，Vue底层会监测你对props的修改，如果进行了修改，就会发出警告，若业务需求确实需要修改，那么请复制props的内容到data中一份，然后去修改data中的数据。
    > 2. props的优先级要高于data，Vue会先读取props中的数据
    >
    > 

## mixin(混入)

1. 功能：可以把多个组件共用的配置提取成一个混入对象，就是有多个子组件中具有相同的功能或者属性时，我们可以将这些共用的配置封装成一个mixin.js，并将其暴露，最后可以被各个组件导入并使用

2. 使用方式：

    第一步定义混合：

    ```js
    {
        data(){....},
        methods:{....}
        ....
    }
    ```

    第二步导入 

    ```js
    import {xxx,xxx} from '../mixin'
    ```
    
    第三步使用混入：
    
    ​	全局混入：```Vue.mixin(xxx)``` ，全局混入是在main.js中导入并使用的
    ​	局部混入：```mixins:['xxx']	``` ，局部混入是在应用到这个共用配置的子组件中导入并使用的

## 插件

1. 功能：用于增强Vue

2. 本质：包含install方法的一个对象，install的第一个参数是Vue，第二个以后的参数是插件使用者传递的数据。

3. 步骤：a. 首先定义一个插件plugins.js

    ​		   b. 在main.js中导入import

    ​		   c. 在main.js中应用导入后的组件 Vue.use(plugins,参数1，参数2···)

3. 定义插件：

    ```js
    对象.install = function (Vue, options) {
        // 1. 添加全局过滤器
        Vue.filter(....)
    
        // 2. 添加全局指令
        Vue.directive(....)
    
        // 3. 配置全局混入(合)
        Vue.mixin(....)
    
        // 4. 添加实例方法
        Vue.prototype.$myMethod = function () {...}
        Vue.prototype.$myProperty = xxxx
    }
    ```

4. 使用插件：```Vue.use(插件名,使用者传入的参数)```

## scoped样式

1. 作用：让样式在局部生效，防止冲突。因为多个子组件中的样式类名可能会有相同的，那么这时加上了scoped后，该样式只在该子组件内生效
2. 写法：```<style scoped>```

## 总结TodoList案例(组件间的通信)

1. 组件化编码流程：

    ​	(1).拆分静态组件：组件要按照功能点拆分，命名不要与html元素冲突。

    ​	(2).实现动态组件：考虑好数据的存放位置，数据是一个组件在用，还是一些组件在用：

    ​			1).一个组件在用：放在组件自身即可。

    ​			2). 一些组件在用：放在他们共同的父组件上（<span style="color:red">状态提升</span>）例子中表现为将todoList数据放在总组件App中，供下面的子组件来使用。

    ​	(3).实现交互：从绑定事件开始。

2. props适用于：

    ​	(1).父组件 ==> 子组件 通信 (爷爷组件==>子组件时，需要爷爷组件先传给父组件，再通过父组件传给子组件)

    ​	(2).子组件 ==> 父组件 通信（要求父先给子一个函数），这个函数定义在父组件中，并通过子组件标签来将这个函数传给子组件，

    ​							子组件再通过props将该函数接收，接收之后子组件的vc身上就会多出来这一个函数，直接可以利用this.func(para)调用。

3. 使用v-model时要切记：v-model绑定的值不能是props传过来的值，因为props是不可以修改的！

4. props传过来的若是对象类型的值，修改对象中的属性时Vue不会报错，但不推荐这样做。

## webStorage

1. 存储内容大小一般支持5MB左右（不同浏览器可能还不一样）

2. 浏览器端通过 Window.sessionStorage 和 Window.localStorage 属性来实现本地存储机制。

3. 相关API：

    1. ```xxxxxStorage.setItem('key', 'value');```
        				该方法接受一个键和值作为参数，会把键值对添加到存储中，如果键名存在，则更新其对应的值。不论是sessionStorage还是localStroage都是以key：value键值对的形式来进行数据存储的

    2. ```xxxxxStorage.getItem('person');```

        ​		该方法接受一个键名作为参数，返回键名对应的值。

    3. ```xxxxxStorage.removeItem('key');```

        ​		该方法接受一个键名作为参数，并把该键名从存储中删除。

    4. ``` xxxxxStorage.clear()```

        ​		该方法会清空存储中的所有数据。

4. 备注：

    1. SessionStorage存储的内容会随着浏览器窗口关闭而消失。
    
    2. LocalStorage存储的内容，需要手动清除才会消失(1.用户自主地清空浏览器地缓存 2.用户点击了实现清空clear()的操作)。
    
    3. ```xxxxxStorage.getItem(xxx)```如果xxx对应的value获取不到，那么getItem的返回值是null。
    
    4. ```JSON.parse(null)```的结果依然是null。
    
    5. 本地存储和会话存储都不能存储对象，不过可以通过JSON.stringify()将对象先转化为字符串存储到xxxStroage中
    
       获取的时候再通过 JSON.parse()将存储的字符串再转化为对象

## 组件的自定义事件

1. 一种组件间通信的方式，适用于：<strong style="color:#3cb371">子组件 ===> 父组件</strong>

2. 使用场景：A是父组件，B是子组件，B想给A传数据，那么就要在A中给B绑定自定义事件（<span style="color:#3cb371">事件的回调函数在A中，因为这个回调函数就是用来接收子组件传过来的数据</span>）。

3. 绑定自定义事件：

    1. 第一种方式，在父组件中：```<Demo @atguigu="test"/>```  或 ```<Demo v-on:atguigu="test"/>``` 第一种直接在标签里面绑定自定义事件 注意这里的回调函数test是不需要写参数的，他需要在methods中实现的时候才加上参数实现test(data)

    2. 第二种方式，在父组件中：第二种首先需要在目标的子组件上定义ref属性，再在本组件的mounted钩子中完成事件绑定

        ```js
        <Demo ref="demo"/>
        ......
        mounted(){
           this.$refs.xxx.$on('atguigu',this.test)
        }
        ```

    3. 若想让自定义事件只能触发一次，可以使用```.once```修饰符，或```$once```方法。

4. 触发自定义事件：```this.$emit('atguigu',要传递的数据)```		要传递的数据就是在触发自定义事件的时候，以参数的形式传给对应的回调函数

5. 解绑自定义事件```this.$off('atguigu')```

6. 组件上也可以绑定原生DOM事件（例如@click），需要使用```.native```修饰符（@click.native）。否则，会默认他是一个自定义事件！

7. 注意：通过```this.$refs.xxx.$on('atguigu',回调)```绑定自定义事件时，回调<span style="color:#3cb371">要么配置在methods中</span>，<span style="color:#3cb371">要么用箭头函数</span>，否则this指向会出问题！

## 全局事件总线（GlobalEventBus）

1. 一种组件间通信的方式，适用于<span style="color:#3cb371">任意组件间通信</span>。

2. 安装全局事件总线：<span style="color:#3cb371">事件总线有两个要求：1. 需要让所有的组件都能够访问的到    2. 且身上具有$on,$emit等api</span>

   ```js
   new Vue({
   	......//beforeCreate()注意在这个创建之前钩子中进行设置
   	beforeCreate() {
   		Vue.prototype.$bus = this //安装全局事件总线，$bus就是当前应用的vm
       //1.因为将这个总线(傀儡)$bus放在Vue的原型对象上，那么所有的vc组件实例对象都能够通过原型链向上找到这个总线$bus
       //2.且这个$bus就是vm，因为这样他身上才会有$on,$off,$emit等api
   	},
       ......
   }) 
   ```

3. 使用事件总线：

   1. 接收数据组件<span style="color:#3cb371">(绑定事件)</span>：A组件想接收数据，则在A组件中给$bus绑定自定义事件<span style="color:#3cb371">(在mounted钩子中进行绑定，一挂载就绑定)</span>，事件的<span style="color:#3cb371">回调留在A组件自身。</span>

      ```js
      methods(){
        demo(data){......}
      }
      ......
      mounted() {
        this.$bus.$on('xxxx',this.demo)
      }
      ```

   2. 提供数据组件<span style="color:#3cb371">(触发事件)</span>：```this.$bus.$emit('xxxx',数据)``` <span style="color:#3cb371">提供数据的组件，需要在本组件方法内$emit触发事件总线中绑定的事件，并通过参数来传送数据</span>

4. 接收数据的组件最好在beforeDestroy钩子中，用this.$bus.$off去解绑<span style="color:#3cb371">当前组件所用到的</span>事件。

## 消息订阅与发布（pubsub）

1.   一种组件间通信的方式，适用于<span style="color:#3cb371">任意组件间通信</span>。

2. 使用步骤：

   1. 安装pubsub：```npm i pubsub-js```

   2. 引入: ```import pubsub from 'pubsub-js'``` 用到pubsub的vue组件都需要引入

   3. 接收数据<span style="color:#3cb371">(订阅消息)</span>：A组件想接收数据，则在A组件中订阅消息，订阅的<span style="color:#3cb371">回调留在A组件自身。</span>

      ```js
      methods(){
          //一定要注意订阅消息时，后面的回调函数中第一个参数是固定的msgName为本次消息名，这里用_来占位，后面的data才是真正的获取的数据
        demo(_,data){......}
      }
      ......
      //消息的订阅也是在组件挂载完毕的钩子中完成的
      mounted() {
        this.pid = pubsub.subscribe('消息名',this.demo) //订阅消息
	     //注意订阅消息后，会返回一个订阅id，这个id是用来找到这个消息，并取消订阅
      }
	   ```
   
	4. 提供数据<span style="color:#3cb371">(发布消息)</span>：```pubsub.publish('消息名',数据)```
	
	5. 最好在beforeDestroy钩子中，用```pubsub.unsubscribe(this.pid)```去<span style="color:#3cb371">取消订阅。</span>
	
## nextTick

1. 语法：```this.$nextTick(回调函数)```
2. 作用：在下一次 DOM 更新结束后执行其指定的回调。在对原dom做出修改后，Vue重新解析模板后生成的新dom，这时我们对这个新dom进行一些操作时，需要通过$nextTick(function{})中的回调来实现
3. 什么时候用：当改变数据后<span style="color:#3cb371">(例如修改了data中的值)</span>>，要基于更新后的新DOM进行某些操作时<span style="color:#3cb371">(例如修改后，会有一个input框的生成，我们想要在input框生成后就立即获取焦点focus())</span>>，这时要在nextTick所指定的回调函数中执行(完成获取焦点的操作)。

## Vue封装的过渡与动画

1. 作用：在插入、更新或移除 DOM元素时，在合适的时候给元素添加样式类名。

2. 动画可以引入第三方的动画库(animate.css) 添加指定的name，appear-active-class，leave-active-class即可完成动画效果

3. 写法：

   1. 准备好样式：

      - 元素进入的样式：
        1. v-enter：进入的起点
        2. v-enter-active：进入过程中
        3. v-enter-to：进入的终点
      - 元素离开的样式：
        1. v-leave：离开的起点
        2. v-leave-active：离开过程中
        3. v-leave-to：离开的终点

   2. 使用```<transition>```包裹要过渡的元素，并配置name属性：

      ```vue
      <transition name="hello">
      	<h1 v-show="isShow">你好啊！</h1>
      </transition>
      ```

   3. 备注：若有多个元素需要过渡，则需要使用：```<transition-group>```，且每个元素都要指定```key```值。

## vue脚手架配置代理

以下都通过axios来发送ajax请求：1.首先先安装axios包： npm i axios

​													   2.导入axios：import axios from 'axios'

实现跨域：1.cors <span style="color:#3cb371">真正解决了跨域问题</span>

​				 2.jsonp <span style="color:#3cb371">jsonp实现跨域的本质是通过script标签的src属性在引入外部资源时，不受到同源限制的特点来实现的，但是只能解决get请求，要求前后端配合写法</span>

​				 3.利用代理服务器<span style="color:#3cb371">（开启代理服务器的两种方式：1.通过Nginx     2.通过vue-cli脚手架）</span>

文件中的两个服务器开启方式：文件中打开cmd，命令node server1/server2   

关闭服务器：ctrl+c



### 方法一

​	在vue.config.js中添加如下配置：

```js
devServer:{
  proxy:"http://localhost:5000"
}
```

说明：

1. 优点：配置简单，请求资源时直接发给前端（8080）即可。
2. 缺点：不能配置多个代理，不能灵活的控制请求是否走代理。
3. 工作方式：若按照上述配置代理，当请求了前端不存在的资源时，那么该请求会转发给服务器 （如果前端有这个资源，也就是根目录public下有的资源，那么就优先匹配前端资源，不会再去转发给目标服务器了）

### 方法二

​	编写vue.config.js配置具体代理规则：

```js
module.exports = {
	devServer: {
      proxy: {
      '/api1': {// 匹配所有以 '/api1'开头的请求路径
        target: 'http://localhost:5000',// 代理目标的基础路径，也就是只写到端口号即可，不用写到具体的资源路径
        changeOrigin: true,
        pathRewrite: {'^/api1': ''}// pathRewrite路径重写，里面是一个key:value键值对形式
          //   ^/api1表示把所有的以api1开头的路径，把这段路径修改为空，即没有了/api1
      },
      '/api2': {// 匹配所有以 '/api2'开头的请求路径
        target: 'http://localhost:5001',// 代理目标的基础路径
        changeOrigin: true,
        pathRewrite: {'^/api2': ''}
      }
    }
  }
}
/*
   changeOrigin设置为true时(代理服务器撒谎了，说自己的host与代理服务器的目标服务器相同都为localhost:5000)，服务器收到的请求头中的host为：localhost:5000
   changeOrigin设置为false时(代理服务器如实回答)，服务器收到的请求头中的host为：localhost:8080
   changeOrigin默认值为true
*/
```

说明：

1. 优点：可以配置多个代理，且可以灵活的控制请求是否走代理。
2. 缺点：配置略微繁琐，请求资源时必须加前缀。

## 插槽

1. 作用：让父组件可以向子组件指定位置插入html结构，也是一种组件间通信的方式，适用于 <strong style="color:#3cb371">父组件 ===> 子组件</strong> 。

2. 分类：默认插槽、具名插槽、作用域插槽

3. 使用方式：

   1. 默认插槽：

      ```vue
      父组件中：
              <Category>
                 <div>html结构1</div>
              </Category>
      子组件中：
              <template>
                  <div>
                     <!-- 定义插槽 -->
                     <slot>插槽默认内容...</slot>
                  </div>
              </template>
      ```

   2. 具名插槽：

      ```vue
      父组件中：
              <Category>
                  <template slot="center">
                    <div>html结构1</div>
                  </template>
      		<!--- 当我使用了template时，指定插槽也可以用下面的方式v-slot:footer-->
                  <template v-slot:footer>
                     <div>html结构2</div>
                  </template>
              </Category>
      子组件中：
              <template>
                  <div>
                     <!-- 定义插槽 -->
                     <slot name="center">插槽默认内容...</slot>
                     <slot name="footer">插槽默认内容...</slot>
                  </div>
              </template>
      ```

   3. 作用域插槽：

      1. 理解：<span style="color:#3cb371">数据在组件的自身，但根据数据生成的结构需要组件的使用者来决定。</span>（games数据在子组件Category中，但使用数据所遍历出来的结构由父组件App决定）

      1. 作用域插槽必须通过template标签进行包裹

      2. 具体编码：
      
         ```vue
         父组件中：
         		<Category>
                     <!--- 这里可以利用对象的解构赋值，这样就直接可以使用games获取到scopeData.games的属性值-->
                     <!--- scope="{games}"-->
         			<template scope="scopeData">
         				<!-- 生成的是ul列表 -->
         				<ul>
         					<li v-for="g in scopeData.games" :key="g">{{g}}</li>
         				</ul>
         			</template>
         		</Category>
         
         		<Category>
         			<template slot-scope="scopeData">
         				<!-- 生成的是h4标题 -->
         				<h4 v-for="g in scopeData.games" :key="g">{{g}}</h4>
         			</template>
         		</Category>
         子组件中：
                 <template>
                     <div>
                         <slot :games="games"></slot>
                     </div>
                 </template>
         		
                 <script>
                     export default {
                         name:'Category',
                         props:['title'],
                         //数据在子组件自身
                         data() {
                             return {
                                 games:['红色警戒','穿越火线','劲舞团','超级玛丽']
                             }
                         },
                     }
                 </script>
         ```

## Vuex

### 1.概念

​		在Vue中实现集中式状态（数据）管理的一个Vue插件，对vue应用中多个组件的共享状态进行集中式的管理（读/写），也是一种组件间通信的方式，且适用于任意组件间通信。

### 2.何时使用？

​		多个组件需要共享数据时

### 3.搭建vuex环境

1. 创建文件：```src/store/index.js```

   ```js
   //引入Vue核心库
   import Vue from 'vue'
   //引入Vuex
   import Vuex from 'vuex'
   //应用Vuex插件
   Vue.use(Vuex)
   
   //准备actions对象——响应组件中用户的动作，相当于服务员
   const actions = {}
   //准备mutations对象——修改state中的数据，相当于厨师
   const mutations = {}
   //准备state对象——保存具体的数据，相当于最后的菜品
   const state = {}
   
   //创建并暴露store
   export default new Vuex.Store({
   	actions,
   	mutations,
   	state
   })
   ```

2. 在```main.js```中创建vm时传入```store```配置项

   ```js
   //引入Vue
   import Vue from 'vue'
   //引入App
   import App from './App.vue'
   //引入插件
   import vueResource from 'vue-resource'
   //关闭Vue的生产提示
   Vue.config.productionTip = false
   //使用插件
   Vue.use(vueResource)
   //1.引入store
   import store from './store'
   
   //创建vm
   new Vue({
   	el: '#app',
   	//注意一定要在vm里面定义使用
   	store,//2.定义store，这样vm以及所有的vc身上都会有$store了，都可以通过this.$store来调用
   	render: h => h(App),
   	beforeCreate() {
   		Vue.prototype.$bus = this
   	}
   })
   ```

###    4.基本使用

1. 初始化数据、配置```actions```、配置```mutations```，操作文件```store.js```

   ```js
   //引入Vue核心库
   import Vue from 'vue'
   //引入Vuex
   import Vuex from 'vuex'
   //引用Vuex,因为vuex本身是一个插件plguins，所以导入后需要通过Vue.use()使用
   //因为只有先使用Vue.use(Vuex)，才能去创建new Vuex.Store()
   Vue.use(Vuex)
   
   //actions是用来接收vc通过this.$dispatch('操作名add',参数)，这时里面操作名add需要在actions中配置
   //并接收参数value
   const actions = {
       //🌈context为上下文，其中有commit(),dispatch(),state....等方法和属性
       //context.commit()用来将接收到的数据进一步呈递给mutations处理
       //context.dispatch()当业务逻辑很复杂时，在一个操作函数中并不能完成逻辑，那么就需要context.dispatch('demo',value)进一步分发给
       //同在actions中的其他方法demo()，demo会对进一步完成逻辑操作，使最终满足业务逻辑要求
       //context中具有state属性，所以它可以直接访问到里面的状态(数据)sum
       //🌟当数据需要进行一些逻辑操作时，就需要放在actions执行
       addWhenOdd(context, value) {
           if (context.state.sum % 2) {
               context.commit('ADD', value)//commit传给mutations时的方法名一般要全大写ADD
           }
       },
       addWait(context, value) {
           setTimeout(() => {
               context.commit('ADD', value)
           }, 500);
       }
   }
   //mutations是用来处理数据的，数据通过actions中的对应操作的函数调用commit方法来传递给mutaiton
   const mutations = {
       ADD(state, value) {
           console.log('mutations中的+被调用了');
           state.sum += value
       },
       SUBTRACT(state, value) {
           console.log('mutations中的-被调用了');
           state.sum -= value
       }
   }
   //所共享的数据都会放在state中
   const state = {
       sum: 0
   }
   
   //创建并暴露store
   export default new Vuex.Store({
       //这因为key:value值相同，所以都使用简写形式
       actions,
       mutations,
       state,
   
   })
   ```
   
2. 组件中读取vuex中的数据：```$store.state.sum```

3. 组件中修改vuex中的数据：```$store.dispatch('action中的方法名',数据)``` 或 ```$store.commit('mutations中的方法名',数据)```

   >  <span style="color:#3cb371">备注：若没有网络请求或其他业务逻辑，组件中也可以越过actions，即不写```dispatch```，直接编写```commit```</span>

![](C:\Users\24825\Desktop\Vue资料\02_原理图\vuex.png)

### 5.getters的使用

1. 概念：当state中的数据需要经过加工后再使用时，可以使用getters加工。<span style="color:#3cb371">它其实可以理解为Vuex中的计算属性computed,对于state中的数据进一步加工，最后通过return返回加工后的值。</span>

2. 在```store.js```中追加```getters```配置

   ```js
   ......
   
   const getters = {
   	bigSum(state){
   		return state.sum * 10//需要return返回处理后的值
   	}
   }
   
   //创建并暴露store
   export default new Vuex.Store({
   	......
   	getters
   })
   ```

3. 组件中读取数据：```$store.getters.bigSum```

### 6.四个map方法的使用

> 备注：mapState, mapGetters这两个是帮我们生成一个计算属性的（配置在computed中）
>
> ​	 mapMutations, mapActions这两个是帮我们生成一个方法的（配置在methods中）

首先需要在组件中导入mapState, mapGetters, mapMutations, mapActions

```js
import {mapState, mapGetters, mapMutations, mapActions} from 'vuex'
```

1. <strong>mapState方法：</strong>用于帮助我们映射```state```中的数据为<span style="color:#3cb371">计算属性</span>,mapXxx就是从Xxx取东西

   ```js
   computed: {
       //借助mapState生成计算属性：sum、school、subject（对象写法）
       //...{}可以将一个对象中的key:value分解出来，形成单独的key:value
       //前面的sum表示的计算属性的函数名（本身对象的key可以简写不用加引号），后面的'sum'表示要从state中读取名为sum这个属性值
        ...mapState({sum:'sum',school:'school',subject:'subject'}),
            
       //借助mapState生成计算属性：sum、school、subject（数组写法）
       //写成数组时['sum']，这时'sum'有两种用途，也就是计算属性名和读取数据名要相同！！
            //1. 将sum作为计算属性sum(){}
            //2. 从state中读取名为'sum'的属性值
       ...mapState(['sum','school','subject']),
   },
   ```

2. <strong>mapGetters方法：</strong>用于帮助我们映射```getters```中的数据为计算属性

   ```js
   computed: {
       //借助mapGetters生成计算属性：bigSum（对象写法）
       ...mapGetters({bigSum:'bigSum'}),
   
       //借助mapGetters生成计算属性：bigSum（数组写法）
       ...mapGetters(['bigSum'])
   },
   ```

3. <strong>mapActions方法：</strong>用于帮助我们生成与```actions```对话的方法，即：包含```$store.dispatch(xxx)```的函数

   ```js
   methods:{
       //靠mapActions生成：incrementOdd、incrementWait（对象形式）
       ...mapActions({incrementOdd:'jiaOdd',incrementWait:'jiaWait'})
   
       //靠mapActions生成：incrementOdd、incrementWait（数组形式）
       ...mapActions(['jiaOdd','jiaWait'])
   }
   ```

4. <strong>mapMutations方法：</strong>用于帮助我们生成与```mutations```对话的方法，即：包含```$store.commit(xxx)```的函数

   ```js
   methods:{
       //靠mapActions生成：increment、decrement（对象形式）
       //其中increment就会作为methods中的方法名,形成一个increment(vlaue){方法内部就会调用commit去联系mutations}的方法，'JIA'是会去mutations中找名为JIA的函数并执行它
       //这样的话就不需要我们通过$store.commit('JIA'，value)来去和mutations进行对话了
       //注意这里无法传入increment方法所需的参数，所以需要在模板中绑定事件触发该increment方法时就传入参数value
       ...mapMutations({increment:'JIA',decrement:'JIAN'}),
       
       //靠mapMutations生成：JIA、JIAN（对象形式）
       ...mapMutations(['JIA','JIAN']),
   }
   ```

> 备注：mapActions与mapMutations使用时，若需要传递参数需要：在模板中绑定事件时传递好参数，否则参数是事件对象。

### 7.模块化+命名空间

1. 目的：让代码更好维护，让多种数据分类更加明确。

2. 修改```store.js```

   ```javascript
   const countAbout = {
     namespaced:true,//开启命名空间
     state:{x:1},
     mutations: { ... },
     actions: { ... },
     getters: {
       bigSum(state){
          return state.sum * 10
       }
     }
   }
   
   const personAbout = {
     namespaced:true,//开启命名空间
     state:{ ... },
     mutations: { ... },
     actions: { ... }
   }
   
   const store = new Vuex.Store({
     modules: {
       countAbout,
       personAbout
     }
   })
   ```

3. 开启命名空间后，组件中读取state数据：

   ```js
   //方式一：自己直接读取
   this.$store.state.personAbout.list
   //方式二：借助mapState读取：
   ...mapState('countAbout',['sum','school','subject']),
   ```

4. 开启命名空间后，组件中读取getters数据：

   ```js
   //方式一：自己直接读取
   this.$store.getters['personAbout/firstPersonName']
   //方式二：借助mapGetters读取：
   ...mapGetters('countAbout',['bigSum'])
   ```

5. 开启命名空间后，组件中调用dispatch

   ```js
   //方式一：自己直接dispatch
   this.$store.dispatch('personAbout/addPersonWang',person)
   //方式二：借助mapActions：
   ...mapActions('countAbout',{incrementOdd:'jiaOdd',incrementWait:'jiaWait'})
   ```

6. 开启命名空间后，组件中调用commit

   ```js
   //方式一：自己直接commit
   this.$store.commit('personAbout/ADD_PERSON',person)
   //方式二：借助mapMutations：
   ...mapMutations('countAbout',{increment:'JIA',decrement:'JIAN'}),
   ```

 ## 路由

1. 理解： 一个路由（route）就是一组映射关系（key - value），多个路由需要路由器（router）进行管理。
2. 前端路由：key是路径，value是组件。

### 1.基本使用

1. 安装vue-router，命令：```npm i vue-router```

2. 应用插件：```Vue.use(VueRouter)``` 在main.js中应用路由插件VueRouter，并在vm中配置路由器属性router

3. 编写router路由器配置项: 路由器会独立写在一个目录为router下的index.js文件中

   ```js
   //引入VueRouter
   import VueRouter from 'vue-router'
   //引入Luyou 组件
   import About from '../components/About'
   import Home from '../components/Home'
   
   //创建router实例对象，去管理一组一组的路由规则
   const router = new VueRouter({
   	routes:[
   		{
   			path:'/about',
   			component:About
   		},
   		{
   			path:'/home',
   			component:Home
   		}
   	]
   })
   
   //暴露router
   export default router
   ```

4. 实现切换（active-class可配置高亮样式）

   ```vue
   <router-link active-class="active" to="/about">About</router-link>
   ```

5. 指定展示位置

   ```vue
   <router-view></router-view>
   ```

### 2.几个注意点

1. 路由组件通常存放在```pages```文件夹，一般组件通常存放在```components```文件夹。
2. 通过切换，“隐藏”了的路由组件，默认是被销毁掉的，需要的时候再去挂载。
3. 每个组件都有自己的```$route```属性，里面存储着自己的路由信息。
4. 整个应用只有一个router，可以通过组件的```$router```属性获取到。

### 3.多级路由（多级路由）

1. 配置路由规则，使用children配置项：

   ```js
   routes:[
       //一级路由
   	{
   		path:'/about',
   		component:About,
   	},
   	{
   		path:'/home',
   		component:Home,
        	//二级路由 
   		children:[ //通过children[]配置子级路由
   			{
   				path:'news', //🌈此处一定不要写：/news
   				component:News
   			},
   			{
   				path:'message',//此处一定不要写：/message
   				component:Message
   			}
   		]
   	}
   ]
   ```

2. <span style="color:#3cb371">跳转（要写完整路径）：</span>

   ```vue
   <router-link to="/home/news">News</router-link>
   ```

### 4.路由的query参数

因为组件的this.$route.query上包含着传过来的query参数信息

1. 传递参数

   ```vue
   <!-- 跳转并携带query参数，to的字符串写法 -->
   当使用to的字符串写法时，如果传递的参数时动态获取的，这时需要通过模板字符串``和${}进行操作，但注意to要加冒号绑定，且要有"`${}`"包裹
   <router-link :to="`/home/message/detail?id=${js语句}&title=${js语句}`">跳转</router-link>
   				
   <!-- 跳转并携带query参数，to的对象写法 -->
   <router-link 
   	:to="{
   		path:'/home/message/detail',
   		query:{
   		   id:666,
               title:'你好'
   		}
   	}"
   >跳转</router-link>
   ```

2. 接收参数：

   ```js
   $route.query.id
   $route.query.title
   ```

### 5.命名路由

1. 作用：可以简化路由的跳转。<span style="color:#3cb371">但注意一定是当路径复杂，也就是到了三级四级之后的子路由，可以通过name来直接跳转</span>

2. 如何使用

   1. 给路由命名：

      ```js
      {
      	path:'/demo',
      	component:Demo,
      	children:[
      		{
      			path:'test',
      			component:Test,
      			children:[
      				{
                            name:'hello' //给路由命名
      					path:'welcome',
      					component:Hello,
      				}
      			]
      		}
      	]
      }
      ```

   2. 简化跳转：<span style="color:#3cb371">通过name跳转时，:to="{name:xxx}"一定要加:绑定，且name写在对象中</span>

      ```vue
      <!--简化前，需要写完整的路径 -->
      <router-link to="/demo/test/welcome">跳转</router-link>
      
      <!--简化后，直接通过名字跳转 -->
      <router-link :to="{name:'hello'}">跳转</router-link>
      
      <!--简化写法配合传递参数 -->
      <router-link 
      	:to="{
      		name:'hello',
      		query:{
      		   id:666,
                  title:'你好'
      		}
      	}"
      >跳转</router-link>
      ```

### 6.路由的params参数（需要占位）

1. 配置路由，声明接收params参数

   ```js
   {
   	path:'/home',
   	component:Home,
   	children:[
   		{
   			path:'news',
   			component:News
   		},
   		{
   			component:Message,
   			children:[
   				{
   					name:'xiangqing',
   					path:'detail/:id/:title', //🌈使用占位符声明接收params参数
   					component:Detail
   				}
   			]
   		}
   	]
   }
   ```

2. 传递参数

   ```vue
   <!-- 跳转并携带params参数，to的字符串写法 -->
   <router-link :to="`/home/message/detail/${m.id}/${m.title}`">{{m.title}}</router-link>
   				
   <!-- 跳转并携带params参数，to的对象写法 -->
    <router-link :to="{
             name:'xiangqing',
             params:{
               id:m.id,
               title:m.title
             }
             }">{{m.title}}</router-link>
   ```
   
   > 特别注意：路由携带params参数时，若使用to的对象写法，则不能使用path配置项 path:'/home/message/detail'，必须使用name配置 name:'xiangqing'！

3. 接收参数：

   ```js
   $route.params.id
   $route.params.title
   ```

### 7.路由的props配置

​	作用：让路由组件更方便的收到参数

> <span style="color:#3cb371">注意以下的三种写法，他们都是通过pros给Detail组件传递参数，所以一定要在Detail组件中用props['xx']接收</span>

```js
{
	name:'xiangqing',
	path:'detail/:id',
	component:Detail,

	//第一种写法：props值为对象，该对象中所有的key-value的组合最终都会通过props传给Detail组件
	// props:{a:900}

	//第二种写法：props值为布尔值，布尔值为true，则把路由收到的所有params参数通过props传给Detail组件
    //第二种仅仅只能将params参数通过prpos传递
	// props:true
	
	//第三种写法：props值为函数，该函数返回的对象中每一组key-value都会通过props传给Detail组件
	props($route){
		return {
			id:$route.query.id,
			title:$route.query.title
            //第三种写法也可以传递其他类型的数据，同样需要在Detail组件中props接收
            a: 123,
            b: '我是b'
		}
	}
    //第三种中利用解构赋值简化写法，解构出$route对象中的{query}
    props({query}){
		return {
			id:query.id,
			title:query.title
		}
	}
    //第三种中利用解构赋值的嵌套写法，再次简化写法，继续解构处query对象中的id和title {query:{id,title}}
    props({query:{id,title}}){
		return {
			id,//简写形式
			title
		}
	}
}
```

### 8.```<router-link>```的replace属性

1. 作用：控制路由跳转时操作浏览器历史记录的模式，可以通过浏览的前进和后退指针进行跳转
2. 浏览器的历史记录有两种写入方式：分别为```push```（它就相当于入栈，点击一次router-link，就会把当前的跳转的路径压入栈中）和```replace```（它相当于入栈的时候把栈顶元素替换掉，就是把当前栈顶的路径替换为本次跳转的路径），```push```是追加历史记录，```replace```是替换当前记录。路由跳转时候默认为```push```
3. 如何开启```replace```模式：```<router-link replace .......>News</router-link>```

### 9.编程式路由导航

1. 作用：不借助```<router-link> ```实现路由跳转，让路由跳转更加灵活

2. 具体编码：

   ```js
   //$router的两个API
   this.$router.push({
   	name:'xiangqing',
   		params:{
   			id:xxx,
   			title:xxx
   		}
   })
   
   this.$router.replace({
   	name:'xiangqing',
   		params:{
   			id:xxx,
   			title:xxx
   		}
   })
   this.$router.forward() //前进
   this.$router.back() //后退
   this.$router.go() //可前进go(3)前进3步 也可后退go(-3)后退三步
   ```

### 10.缓存路由组件

1. 作用：让不展示的路由组件保持挂载，不被销毁。<span style="color:#3cb371">（一般想要保留数据的组件）</span>

2. 具体编码：

   ```vue
   <keep-alive include="News"> 
       <!-- 一定是包在<router-view>标签的外部 --> 
       <router-view></router-view>
   </keep-alive>
   ```

> <span style="color:#3cb371">1. 上述中指定include="xxx" 这个名字是组件的名字，也就是vc中的name属性配置</span>
>
> <span style="color:#3cb371">2. 也可以不指定include，这时展示在router-view中的组件都会保持挂载，不会销毁</span>
>
> 3. 如果需要在多个组件中挑选出其中的几个组件来使其保持挂载，那么可以用冒号绑定还有数组包裹<span style="color:#3cb371"> :include="['组件名1','组件名2']"</span>

### 11.两个新的生命周期钩子

1. 作用：路由组件所独有的两个钩子，用于捕获路由组件的激活状态。
2. 具体名字：
   1. ```activated```路由组件被激活时触发。
   2. ```deactivated```路由组件失活时触发。
3. 小结生命钩子：beforeCreate(),creared(),beforeMount(),mounted(),beforeUpdate(),updated(),beforeDestory(),destoryed(),<span style="color:#3cb371">activated(),deactivated(),$nextTick()</span>

### 12.路由守卫

1. 作用：对路由进行权限控制

2. 分类：全局守卫、独享守卫、组件内守卫

3. 全局守卫:

   1. 全局前置守卫和后置守卫，两个函数都有to,from参数，to{name:"xxx",path:"/xxx",meta:{}}表示的目的路径信息 <span style="color:#3cb371">(这个组件的路由信息其实和目的组件中的$route信息相同)，</span>from{}表示的是起点路径信息
   2. 前置守卫的next参数表明 是否放行该路径,只有next()放行了才会真正到达目的组件<span style="color:#3cb371">（注意如果没有放行该路由，也就是没有到达目的to组件，这时全局后置路由是不会执行的！只有全局前置路由执行！）</span>
   3. to.meta{}，meta对象中是包含的是路由的元信息，也就是coder自己定义的一些数据 
   4. 对于全局守卫而言，他是router身上的函数，所以需要在index.js接收这个路由器router,而不是直接将路由器暴露

   ```js
   //全局前置守卫：初始化时执行、每次路由切换前执行
   router.beforeEach((to,from,next)=>{
   	console.log('beforeEach',to,from)
       //这里需要在路由器的routes的配置中，对需要判断是否要有权限的路由，给他的meta自定义一组key:value 
       //meta:{isAuth:true}
   	if(to.meta.isAuth){ //判断当前路由是否需要进行权限控制
   		if(localStorage.getItem('school') === 'atguigu'){ //权限控制的具体规则
   			next() //放行
   		}else{
   			alert('暂无权限查看')
   			// next({name:'guanyu'})
   		}
   	}else{
   		next() //放行
   	}
   })
   
   //全局后置守卫：初始化时执行、每次路由切换后执行
   //⭐注意如果前置路由中没有放行，那么这里的后置路由也不会执行
   router.afterEach((to,from)=>{
   	console.log('afterEach',to,from)
   	if(to.meta.title){ 
   		document.title = to.meta.title //修改网页的title
   	}else{
   		document.title = 'vue_test'
   	}
   })
   ```

4. 独享守卫: 他写在该组件的路由配置中 <span style="color:#3cb371">独享只有前置守卫！他可以和全局后置守卫搭配使用</span>

   beforeEnter(){}他是一个函数，不像全局守卫类似定时器回调

   ```js
   beforeEnter(to,from,next){
   	console.log('beforeEnter',to,from)
   	if(to.meta.isAuth){ //判断当前路由是否需要进行权限控制
   		if(localStorage.getItem('school') === 'atguigu'){
   			next()
   		}else{
   			alert('暂无权限查看')
   			// next({name:'guanyu'})
   		}
   	}else{
   		next()
   	}
   }
   ```

5.  组件内守卫：

   1. 组件内守卫首先是组件独有的，进入守卫和离开守卫都是定义在组件的vc中
   2. 两个守卫都是需要通过路由规则，也就是需要经过路由器指定匹配的路径的组件
   3. 两个守卫都有next参数，进入和离开都需要设置是否放行next()
   4. 进入守卫的to和离开守卫的from对应的路由信息都是本次组件的路由信息
   4. 当进入守卫中没有放行next()该路由时，那么页面是进入不到该路径里的！
   4. 同样的，它也可以和全局后置守卫搭配使用
   
   ```js
   //进入守卫：通过路由规则，进入该组件之前被调用
   beforeRouteEnter (to, from, next) {
   },
   //离开守卫：通过路由规则，离开该组件时被调用，注意他和全局后置守卫的区别，离开守卫是当离开了该组件时(路由已经切换完了)，他才会调用
   //而全局后置守卫是只要我本次路由切换完毕我就会调用
   beforeRouteLeave (to, from, next) {
   }
   ```

### 13.路由器的两种工作模式



1. 对于一个url来说，什么是hash值？—— #及其后面的内容就是hash值。
2. hash值不会包含在 HTTP 请求中，即：hash值不会带给服务器。
3. hash模式：
   1. 地址中永远带着#号，不美观 。
   2. 若以后将地址通过第三方手机app分享，若app校验严格，则地址会被标记为不合法。
   3. 兼容性较好。
4. history模式：
   1. 地址干净，美观 。
   2. 兼容性和hash模式相比略差。
   3. 应用部署上线时需要后端人员支持，解决刷新页面服务端404的问题。
5. 在路由器router配置中加上 mode:'history' 就可以把路由器的工作模式转换为history模式，默认是hash模式
5. 项目的最终打包：npm run build 该命令会把vue文件都转换成为静态的前端代码html+css+js
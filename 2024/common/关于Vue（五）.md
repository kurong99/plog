### 什么是组件？

​	  实现应用中部分功能代码和资源的集合。组件的本质是一个拥有预定义选项的vue实例。分为单文件组件(一个文件中只包含一个组件)和非单文件组件(一个文件中包含n个组件)。

​	单文件组件：后缀名为 .vue 的单文件 里面包含了组件的描述。可能需要用到cli工具。  

​	非单文件组件：一个 .js 后缀的文件 里面同样包含了组件的描述，在HTML文件中引入后 即可使用。

​	单文件组件格式：

```vue
<template>
  <div>
    <MyComponent></MyComponent>
  </div>
</template>


<script>
import MyComponent from './components/MyComponent.vue';


export default {
  components: {
    MyComponent
  }
};
</script>
<style>
/* 样式 */
</style>
```

​	组件的本质就是：一个名为VueComponent()的构造函数。它不是由程序员定义的，而是由Vue.extend()生成的。

```JavaScript
const component = Vue.extend({
    template: `
        <div>
            <span>这是一个组件<span/>     
        </div>
    `
})
console.log(component);
// VueComponent(options) {
//      this._init(options);
//}
```


​	当我们使用组件标签使用组件时，Vue会自动调用new VueComponent()构造函数,帮我们生成一个组件实例对象。每次调用Vue.extend()时返回的都是全新的VueComponent()构造函数

```JavaScript
const component = Vue.extend({
    template: `
        <div>
            <span>这是一个组件<span/>     
        </div>
    `
});

const component2 = Vue.extend({
    template: `
        <div>
            <span>这是另一个组件<span/>     
        </div>
    `
});

console.log(component === component2); // false

// 在源码中 每次调用Vue.extend都会重新声明一个Sub
// 这说明每次返回的Sub都不一样
Vue.extend = function(extendOptions){
    ......
    var Sub = function VueComponent(options){
        this._init(options);
    };
    .......
    return Sub;
};
```

#### 关于this指向

​	const vm =  new Vue(options)，配置项里面的this均是指向Vue的实例对象vm。

​	const vc= new VueComponent(options)中，组件配置项中的this均是指向的是VueComponent的组件实例对象vc

```JavaScript
const mycomponent = Vue.extend({
    template: `
        <div>
            <span>这是一个组件</span>
                <button @click="show">点击出现弹窗</button>   
        </div>
    `,
    data(){
        return {
            msg: '你好'
        }
    },
    methods: {
        show(){
            console.log(this);
        }
    }
});
// 打印的内容 组件实例“vc”
//VueComponent {
//    _uid: 2, _isVue: true, __v_skip: true, 
//    _scope: EffectScope, $options: {…}
//}
```


#### vm 和 vc的关系

​	   vm 的身上有一个$children属性(是一个数组)，里面包含了vc(不止一个)。vm管理着vc。

​		实例是Vue的基本概念，代表一个Vue的根实例，页面中只能存在一个根实例；而组件是基于实例的概念，可以对应一个或多个实例，将页面拆分成多个独立的部分。 实例可以直接在根节点下进行挂载和渲染，而组件需要在实例中先进行注册后才能进行使用。

![7f92ad47-9d3a-49e4-9737-13af146231d5](https://raw.githubusercontent.com/kurong99/plog/master/pic/7f92ad47-9d3a-49e4-9737-13af146231d5.png)

​	  由于VueComponent.prototype.__proto__ === Vue.prototype，所以才使得组件实例对象也可以访问到Vue原型上的属性和方法。

### 如何使用组件？

​	使用组件的几个步骤：

		1. 创建非单文件组件

     ​    使用Vue.extend({})创建一个组件，传入一个对象。传入的对象表示对组件的描述。

     ​    由于组件是一个可复用的Vue实例，所以与Vue实例接受相同的选项如：data method computed watchd 等。除了 el 是根实例特有的选项外。

     ​    **需要注意的是： 这里面的data选项必须是一个函数，这个函数返回一个对象包含了实例所需要的数据。这样使得组件在复用的时候每一个实例都会拥有一个被返回对象的拷贝，保证了各组件之间的数据互不干扰。(使得组件在复用的时候数据之间不存在引用关系)**

     ```JavaScript
     Vue.extend({
         data: function () {
             return {
                 count: 0
             }
         },
         template: '<button @click="count++">你点击了{{ count }} 次</button>'
     });    
     ```

     ​		template表示组件的模板 也就是组件的html结构

     ​    也可以不使用Vue.extend({})这个API，直接创建一个对象，对象包含对于组件的描述。

     ```JavaScript
     const counter = {
         data: function () {
             return {
                 count: 0
             }
         },
         template: '<button @click="count++">你点击了{{ count }} 次</button>'
     }
     ```

		2. 创建单文件组件

     创建 .vue 后缀的文件，在里面添加以下内容

     ```
     <template>
       <div>
         <MyComponent></MyComponent>
       </div>
     </template>
     
     <script>
     import MyComponent from './components/MyComponent.vue';
     
     export default {
       components: {
         MyComponent
       }
     };
     </script>
     
     <style>
     /* 样式 */
     </style>
     ```

		3. 非单文件注册组件

     		1. 全局注册

          ​     使用 Vue.component(tagName, options)。全局注册的组件在当前Vue应用全局可用。

          ​      接收两个参数：第一个参数表示组件的名称，遵循命名规范：要么始终是单词大写开头 (PascalCase)，要么始终是横线连接 (kebab-case)。

          ```JavaScript
          Vue.component('button-counter',counter);
          ```

     		2. 局部注册

          ​	     在Vue实例中通过 `components` 选项进行局部注册，将组件注册到父组件的局部组件列表中。局部注册的组件只能在其父组件的模板中使用，无法在其他组件和其后代组件中使用。只有在当前组件的作用域内才能使用该组件，无法在其他地方使用。适用于当前组件私有的子组件，以及仅在某个特定页面或组件中使用的组件。

          ```JavaScript
          const Component = {
              template: `<h2>局部注册的组件</h2>`
          };
          new Vue({
              el: "#app1",
              components: {
                  'component-a': Component
              },
          });
          ```

		4. 单文件组件注册

     		1. 全局注册

          在项目中的 'main.js' 中全局注册组件。

          ```JavaScript
          import Vue from 'vue'
          import App from './App.vue'
          import ButtonCounter from './components/ButtonCounter.vue'
          
          Vue.config.productionTip = false
          // 全局注册
          Vue.component('button-counter', ButtonCounter);
          
          new Vue({
            render: h => h(App),
          }).$mount('#app')
          ```

     		2. 局部注册

          使用 `components` 选项来进行注册：

          ```JavaScript
          import ComponentA from './ComponentA.js'
          
          export default {
            components: {
              ComponentA
            },
            setup() {
              // ...
            }
          }
          ```

		5. 使用组件

     根据你的需求使用组件标签将组件放入对应的位置即可

     ```JavaScript
     <div id="app">
         <button-counter></button-counter>
     </div>
     ```

### 父子组件

​	非单文件组件如何书写父子组件				

```JavaScript
<div id="example">
  <parent></parent>
</div>

<script>
var childNode = {
  template: '<div>childNode</div>',
}
var parentNode = {
  template: `
  <div class="parent">
    <child></child>
    <child></child>
  </div>
  `,
  components: {
    'child': childNode
  } 
};
// 创建根实例
new Vue({
  el: '#example',
    components: {
    'parent': parentNode
  }  
})
</script>
```

​	单文件组件书写父子组件

```vue
<template>
  <article>
    <CarouselComponent/>
    <ArticleList/>
  </article>
</template>

<script>
import CarouselComponent from './CarouselComponent.vue'
import ArticleList from './ArticleList.vue'

export default {
    name: 'MainComponent',
    components: {
        CarouselComponent,
        ArticleList
    }
}
</script>

<style scoped></style>
```

	#### 组件传值

	##### 父传子：使用props实现

	1. 父组件上定义要传递的值
	1. 在子组件定义的时候添加 props 选项
	1. 将这个 props 动态绑定到子组件标签上 也就是props的值与父组件内定义要传的值相绑定

```JavaScript
// 全局注册父组件
        Vue.component('school',{
            data: function(){
                return {
                    schoolName: "nifgdg",
                    schoolAddress: "fgdsg",
                    newValue: "原始值",
                    childValue: "接收到了"
                }
            },
            template: `
                <div>
                    <p>学校名称：{{ schoolName }}</p>
                    <p>学校地址：{{ schoolAddress }}</p>
                    <p>{{ newValue }}</p>
                    <student @send="getValue" :data="childValue"></student>
                </div>
            `,
            methods: {
                getValue: function(value) {
                    this.newValue = value;
                }
            }
        });
        // 全局注册子组件
        Vue.component('student',{
            props: {
                data: String
            },
            data: function(){
                return {
                    studentName: "tom",
                    studentAge: 12,
                    tranValue: "子组件的传值",
                }
            },
            template: `
                <div>
                    <p>学生名称：{{ studentName }}</p>
                    <p>学生名称：{{ studentAge }}</p>
                    <button @click="sendValue">点击传值		</button>
                    <p>接收父组件的值: {{ data }}</p>
                </div>
            `,
            methods: {
                sendValue: function() {
                    this.$emit('send',this.tranValue);
                }
            }
        });
        // 挂载到根组件上
        new Vue({
            el: "#app13"
        });
```

##### 		子组件向父组件传值（使用自定义事件实现）

		1. 子组件内定义好要传递的值
		1. 子组件定义自定义事件，使用$emit向父组件传值
		1. 父组件监听这个自定义事件，获取子组件传递的值


```JavaScript
Vue.component('father-compoent',{
            data: function(){
                // 在父组件内 定义要传递的值
                return {
                    name: "父组件向子组件传值",
                    msg: ""
                }
            },
            template: `
                <div>
                    <p>我是父组件</p>
                    <p>这是子组件传递过来的值：{{ msg }}</p>
                    <!-- 在子组件上动态绑定prop名 -->
                    <child-compoent :name="name" v-on:inputHander="getMessage"></child-compoent>
                </div>
            `,
            methods: {
                getMessage: function (data) {
                    // console.log(data);
                    this.msg = data;
                }
            }
        });

        Vue.component('child-compoent',{
            // 子组件内定义prop名
            props: ['name'],
            // 定义要向父组件传递的值
            data: function(){
                return {
                    sendMessage: ""
                }
            },
            template: `
                <div>
                    <p>我是子组件</p>
                    <input type="text" v-model="sendMessage" v-on:input="messagehander">
                    <p>这里是父组件传过来的值: {{ name }}</p>
                </div>
            `,
            methods: {
                messagehander: function(){
                    this.$emit('inputHander',this.sendMessage);
                },
            }
        });

        new Vue({ el: "#interaction" })
```
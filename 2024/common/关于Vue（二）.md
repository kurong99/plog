### 动态定属性的方式

	#### 1. 对象语法

​	可以传给 `v-bind:class` 一个对象，以动态地切换 class：当isActive为true的时候 dom元素将会显示active这个class类。

```JavaScript
<div v-bind:class="{ active: isActive }"></div>

//或者定义多个class
<div
  class="static"
  v-bind:class="{ active: isActive, 'text-danger': hasError }"
></div>

// 也可以在这里绑定一个返回对象的计算属性
data: {
  isActive: true,
  error: null
},
computed: {
  classObject: function () {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal'
    }
  }
}
```

#### 2.  数组语法

​	 可以把一个数组传给 `v-bind:class`，以应用一个 class 列表：

```JavaScript
<div v-bind:class="[activeClass, errorClass]"></div>

data: {
  activeClass: 'active',
  errorClass: 'text-danger'
}

// 在数组语法中也可以使用对象语法：
<div v-bind:class="[{ active: isActive }, errorClass]"></div>
```

#### 3.  用在组件上

​	 在一个自定义组件上使用 `class` property 时，这些 class 将被添加到该组件的根元素上面。这个元素上已经存在的 class 不会被覆盖。

```JavaScript
Vue.component('my-component', {
  template: '<p class="foo bar">Hi</p>'
})
<my-component class="baz boo"></my-component>
// 渲染为
<p class="foo bar baz boo">Hi</p>

<my-component v-bind:class="{ active: isActive }"></my-component>
```

### 绑定内联样式

	#### 1. 对象语法

​	 `v-bind:style` 的对象语法十分直观——看着非常像 CSS，但其实是一个 JavaScript 对象。CSS property 名可以用驼峰式 (camelCase) 或短横线分隔 (kebab-case，记得用引号括起来) 来命名：

```JavaScript
<div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>

data: {
  activeColor: 'red',
  fontSize: 30
}

//直接绑定到一个样式对象通常更好，这会让模板更清晰：
<div v-bind:style="styleObject"></div>
data: {
  styleObject: {
    color: 'red',
    fontSize: '13px'
  }
}
```

#### 2.  数组语法

​	  当 `v-bind:style` 使用需要添加[浏览器引擎前缀](https://developer.mozilla.org/zh-CN/docs/Glossary/Vendor_Prefix)的 CSS property 时，如 `transform`，Vue.js 会自动侦测并添加相应的前缀。

 `v-bind:style` 的数组语法可以将多个样式对象应用到同一个元素上：

```JavaScript
<div v-bind:style="[baseStyles, overridingStyles]"></div>

data: {
    baseStyles: {
        color: red;
        fontSize: 16px;
    },
    overridingStyles: {
        margin: 0 auto;
        ...
    }
}
```

### 绑定元素的attribute

​	使用v-bind指令 将元素的attribute与vue实例的数据绑定

```JavaScript
<div id="app-2">
    <span v-bind:title="message">
        鼠标悬停几秒钟查看此处动态绑定的提示信息！
    </span>
</div>
const app = new Vue({
    el: "#app-2",
    data: {
        message: "Hello World",
    }
});
```

### attribute 和 property 的区别

​	Attribute：是HTML标签上的特性，它的值只能是字符串，是表示用户自定义的属性

 如<div titles = "exp">, 这里的titles是用户自定义的属性 被称为attribute。

Property: 它是DOM元素的属性，是JS里面的对象，是表示DOM元素自身的属性

 如<div id= "exp">, 这里的id是DOM元素的属性 被称为property。

 它们之间的关系是：一个DOM元素的property中包含一个attribute属性,类型是NamedNodeMap 里面是用户自定义的属性的键值对。

 由于Property和Attribute是从属关系，所以 Property 能够从 Attribute 中得到同步 ，Attribute 不会同步 Property 上的值。v-bind发生的是对 Attribute 的绑定，同步不了property，所以v-bind不能实现双向绑定，而v-model是对property的绑定，它能够从attribute上获得值得同步 所以能实现双向绑定。

 详情：https://www.cnblogs.com/lmjZone/p/8760232.html


### 控制DOM结构

	#### 使用v-if指令 控制DOM元素的显示与隐藏

```JavaScript
<div id="app-3">
    <p v-if="seen">显示</p>
</div>
```

	#### 使用v-show指令也可以达到相同效果

	<div id="app-3">
		<p v-if="seen">显示</p>
	</div>
	#### v-if 和 v-show的区别

​		使用v-if不显示元素的时候，它是直接不渲染元素，适用于切换显隐频率比较低的场景，而v-show在不显示元素的时候，是在元素上添加了display:none样式，元素依旧会渲染，适用于切换频率比较高的场景

### 循环指令渲染数据

​	使用v-for指令 循环渲染数据 注意为了避免重复渲染 一般会绑定一个key。这样做的原因是 ：Vue 会尽可能高效地渲染元素，通常会复用已有元素而不是从头开始渲染  有些场景我们希望它不要复用已有元素，于是在使用循环指令的时候用 key 来管理可复用元素 key="..." 表达 “这两个元素是完全独立的，不要复用它们”

```JavaScript
<div id="app-4">
    <ol>
        <li v-for="(todo,index) in todos" :key="todo.id">{{ todo.date }} 用时 {{ todo.time }}</li>
    </ol>
</div>
```

 #### 为什么不用index作为key值？

​	若对数据进行：逆序增加，逆序删除等破坏顺序的操作，会产生没有必要的DOM更新，影响效率。

​	如果页面结构还存在输入类的DOM，会产生错误的DOM更新，导致页面出问题。

 而使用数据唯一的 id 则不会。

####  在虚拟DOM中key的作用？

​	 key是虚拟DOM对象的标识，当数据发生变化时，Vue会根据【新数据】生成新的虚拟DOM，随后Vue会进行新旧虚拟DOM的差异比较，这个比较的规则就叫做Diff算法。

#### Diff算法规则：

​	旧虚拟DOM中找到了与新虚拟DOM相同的key：

​		若虚拟DOM中的内容没变，则直接使用之前生成的真实DOM。

​		若虚拟DOM中的内容改变了，则生成新的真实DOM替换掉之前的真实DOM。

​	旧虚拟DOM中没有找到与新虚拟DOM相同的key：

​		  创建新的真实DOM，并渲染到页面上。

详情见：https://juejin.cn/post/6997792097084375054

### 事件监听器

​	使用v-on指令添加监听器 处理用户交互事件，监听点击事件

```JavaScript
<div class="app-5">
    <p>{{ message }}</p>
    <button v-on:click="reverMessage">反转消息</button>
</div>
```

​	

### 一些Vue内部指令

1.  v-text：元素的textContent

2.  v-html：元素的innerHTML

3.  v-show：通过样式display改变显隐

4.  v-if：通过操作DOM改变显隐

5.  v-else：配合v-if

6.  v-else-id：配合v-else

7.  v-for：循环渲染

8.  v-on：绑定事件，缩写@

9.  v-bind：绑定变量，缩写:

10.  v-model：双向绑定

11.  v-slot：插槽

12.  v-once：只渲染一次

13.  v-pre：跳过元素编译

14.  v-cloak：页面重新渲染时避免出现Vue源代码，有值再显示
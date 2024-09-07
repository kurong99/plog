### Vue中的数据双向绑定

	####  什么是vue的双向绑定？

​	数据与视图的响应式设计，具体表现为：模型层(model)的改变能实时的更新视图层(view)，而视图层(view)的变化也能实时更新模型层(model)。

####  Vue双向绑定的原理？

​	通过数据劫持 + 数据发布 -- 订阅者模式 的方式来实现的。首先是通过ES5的Object.defineProperty() 方法来劫持（监听）各属性的 **getter、setter。**当监听的属性发生变化的时候通知订阅者是否需要更新 如果需要则执行对应的更新函数。

​	常见的数据劫持有两种实现：

​		通过ES5提供的Object.defineProperty()

​		使用ES6新增的proxy【Vue3.0已经使用它替代了Object.defineProperty()】

####  如何实现双向绑定？

​	 使用v-model指令实现数据的双向绑定。当输入框输入的时候 p标签中的内容会随着输入的内容同步更新


```JavaScript
<div id="app-6">
    <p>{{ message }}</p>
    <input type="text" v-model="message">
</div>
```
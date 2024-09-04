### 声明式渲染

​	Vue允许使用简洁的模板语法来声明式的将数据嵌入到DOM中

```JavaScript
<div id="app">
    {{ message }}
</div>
```

### 响应式原理

	#### 1. 响应式原理

​	 数据的变化会引发页面的变化，数据决定了页面。

​	个人理解：   创建一个观察数据对象的函数，观测哪些地方改动了数据，随之告	知渲染页面的函数重新渲染页面，达到页面及时更新的目的。

```JavaScript
<body>

    <div id="app">
        <p class="firstName">静态数据</p>
        <p class="lastName">静态数据</p>
        <p class="age">静态数据</p>
    </div>

</body>

<script>

// 一个数据对象
const data = {
    name: '诸葛孔明',
    birthDay: '1999-12-01'
}

// 展示姓
function showFirstName() {
    let firstName = '';
    if (data.name.length < 4) {
        firstName = data.name[0];
    } else {
        firstName = data.name.substring(0, 2);
    }
    document.querySelector('.firstName').textContent = '姓名：' + firstName;
}
// 展示名
function showLastName() {
    let lastName = '';
    if (data.name.length < 4) {
        lastName = data.name[1];
    } else {
        lastName = data.name.substring(2);
    }
    document.querySelector('.lastName').textContent = '姓名：' + lastName;
}

// 展示年龄
function showAge() {
    let nowYear = new Date().getFullYear();
    let age = nowYear - data.birthDay.split('-')[0];
    document.querySelector('.age').textContent = '年龄：' + age;
}

observe(data);
// 在函数执行之前 将函数保存在全局变量中 用这个全局变量来告诉观察函数是谁在用这个属性
autoRun(showFirstName);
autoRun(showLastName);
autoRun(showAge);

//  改变数据 此时页面响应式更新
data.name = "张三";
data.birthDay = '1990-12-01';

// 创建一个观察数据对象的函数
function observe(obj) {
    for (const key in obj) {
        let initialValue = obj[key];
        // let funcs = new Set();  为什么这里用set不行
        let funcs = [];
        Object.defineProperty(obj, key, {
            get() {
                // 依赖收集 收集哪些函数用到了这个属性
                if(window.__func && !funcs.includes(window.__func)) {
                    funcs.push(window.__func);
                }
                return initialValue;
            },
            set(val) {
                initialValue = val;
                // 派发更新 执行渲染函数
                for (var i = 0; i < funcs.length; i++) {
                    funcs[i]();
                }
            }
        });
    }
}

// 创建一个自动运行函数
function autoRun(fn) {
    window.__func = fn;
    fn();
    window.__func = null;
}
</script>
```

#### 2.   官方解释

	##### 1. 什么是MVVM模型

​    全称是 Model--View--ViewModel ，也就是数据--视图--视图数据。与MVC不同的地方是视图和数据进行了双向绑定。ViewModel负责把Model的数据同步到View显示出来，还负责把View的修改同步回Model。

​    简单来说：View 和 Model 通过 ViewModel 实现数据同步，不需要手动更新。

​    注意：Vue不严格符合MVVM，因为MVVM规定Model和View不能直接通信，而Vue可以使用`ref`进行通信。

##### 2. 实现方式

​	   Vue 采用 **数据劫持** 结合 **发布者-订阅者模式** 的方式来实现数据的响应式，通过 Object.defineProperty 来劫持数据的 setter 和 getter，在数据变动时发布消息给订阅者，订阅者收到消息后进行相应的处理。在使用Object.defineProperty劫持数据时，当对这个数据进行取值操作的时候，就会在get方法中对这个数据进行依赖收集，依赖收集的核心是两个类dep和Watcher，dep的作用是存储依赖，Watcher的作用是记录视图更新的函数，哪些地方使用了这个数据就会从Watcher中拿到关于这个数据更新视图的函数，如果这个数据发生了修改，就会在set方法中自动调用这个更新视图的函数。

### Vue中的数据代理

	##### 1. 实现过程是什么？

​	  是通过 vm (vue实例) 来代理data对象(间接的代理了data对象，因为_data是从data选项中获得的)。数据代理会将data选项(options)中的所有属性映射到Vue实例中去，相当于可以直接通过Vue实例来访问 data 中的所有属性。然后通过Object.defineProperty()这个API将每个属性设置 getter/setter用来操作这些属性。

 也就是说：你访问的proxy.name 其实是访问的 _data.name。

```JavaScript
let vm= {
        el: "#app",
        _data: {
            name: "Joune",
            age: 18
        }
    }

    Object.defineProperty(vm,"name",{
        get(){
            console.log(this); // 这里的this指向的是代理的这个对象 vm
            return this._data.name;
        },
        set(value){
            this._data.name = value;
        }
    });
    
    vm.name === vm._data.name === _data.name
```

![79df3431-07b3-4aaf-ab03-0ae729e3ac98](https://raw.githubusercontent.com/kurong99/plog/master/pic/79df3431-07b3-4aaf-ab03-0ae729e3ac98.png)

##### 2.  Vue如何监测对象

​	 首先创建一个观察者函数Observe，将要监测的对象作为参数传入这个函数，然后在函数内部拿到这个对象的所有键key值保存到数组中，然后给数组每一个元素添加Object.defineProperty()，有人读取调用getter方法，修改调用setter方法(页面发生变化)。通过这种方式监测到对象数据的变化并在页面做出响应。

```JavaScript
const vm = new Vue({
    el: '#app',
    data: {
        name: "xxx",
        age: "xx"
    }
});

vm._data = data  // 在这个过程发生之前，发生了一件事： 加工data。

所以：_data: {__ob__: Observer} 看起来是这个样子。

如何加工？
function Observer(obj) {
    const arr = Object.keys(obj);
    arr.forEach(k => {
        // 这里的this指向的是这个构造函数的实例对象
        // 相当于向这个构造函数的实例对象上添加obj上的所有属性
        Object.defineProperty(this,k,{
            get(){
                return obj[k];
            },
            set(val){
                obj[k] = val;
            }
        })
    })
}

const person = {
    name: 'foo',
    age: 36
}
const obs = new Observer(person);

所以加工之后就变成了这样：_data: {__ob__: Observer} 
展开之后：
_data{
    ...
    name: "xxx",
    age: "xx",
    get age: ƒ get(),
    set age: ƒ set(val),
    get name: ƒ get(),
    set name: ƒ set(val),
    ....
}
然后：vm._data = obs;

最后通过vm代理这个_data:
Objcet.defineProperty(vm,"name",{
    ....
});
使得vm上拥有name，age属性，并且返回的值是从_data中拿到的，设置的也是_data的值。

vm实例上的data选项中的属性 都会被映射到_data中去，然后vm代理了这个_data中的所有属性。
```

##### 3.  Vue如何监测数组

  	出于性能考虑，Vue没有对数组使用Objcet.defineProperty进行劫持。所以Vue 无法检测到以下数组变动：当你使用索引直接设置一项时 或者 当你修改数组长度时。

  解决方法：

1. 先获取原生 Array 的原型方法（push、pop、splice、shift、unshift、reverse、sort），因为拦截后还是需要原生的方法帮我们实现数组的变化。
2. 然后使用重写后的方法去劫持到数组的变化，从而达到响应式的视图更新。

##### 4.  为什么要使用数据代理？

 	为了更方便的管理数据。通过将数据存储在Vue实例的属性中，并使用特定的方式来访问和修改这些数据，从而确保视图与数据的同步更新。

 也就是说：当我们在视图渲染要使用这个属性的时候，不需要用：_data.name; 而是直接使用：name; 
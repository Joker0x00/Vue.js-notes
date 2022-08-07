# 1. Vue核心

## 1.1 模板语法

1. 插值语法

   功能：用于解析标签体的内容

   写法：{{xxx}}, xxx是 `js` 表达式，且可以直接读取到 `data` 中的所有属性

2. 指令语法：

   功能：用于解析标签，（包括标签属性，标签体内容，绑定事件...)

   形式都是 `v-???`

### v-band

```html
<a v-bind:href="url">baidu</a>
```

`v-band` 相当于把后面引号内的属性当作 `js` 表达式，可以简写为 `:`



## 1.2 数据绑定

**Vue中有2中数据绑定的方式：**

1. 单向绑定 `v-bind` : 数据只能从 `data` 流向页面
2. 双向绑定 `v-model`: 数据不仅能从 `data` 流向页面，还可以从页面流向 `data` 
   * 双向绑定一般都应用在表单类元素上
   * `v-model:value`  可以简写为 `v-model` 

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>初识Vue</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
    <div id="root">
        <div>
            单向数据绑定：<input type="text" :value="data1">
        </div>
        <div>
            双向数据绑定：<input type="text" v-model:value="data1">
        </div>
    </div>
    <script type="text/javascript">
        Vue.config.productionTip = false // 阻止 vue 启动时生成生产提示
        const x = new Vue({
            el: '#root', // el 用于指定当前 Vue 实例为哪个容器服务，值通常为css选择器字符串
            data: { // data中用于存储数据，数据供el所指定的容器去使用，值我们暂定先写成一个对象
                data1: '中国',
            }
        })
    </script>
</body>
</html>
```

`v-model` 只能用在表单，输入类元素中

**简写**

```html
<div>
    单向数据绑定：<input type="text" :value="data1">
</div>
<div>
    双向数据绑定：<input type="text" v-model="data1">
</div>
```



## 1.3 el 与 data 两种写法

### el

```javascript
Vue.config.productionTip = false // 阻止 vue 启动时生成生产提示
const x = new Vue({
    // el: '#root', // 方式一
    data: { // data中用于存储数据，数据供el所指定的容器去使用，值我们暂定先写成一个对象
        data1: '中国',
    }
})

setTimeout(() => {
    x.$mount('#root') // 方式二
}, 1000);
```

### data

**对象式**

```js
data:{
    data1:'xxxx'
}
```

**函数式**

```js
data:function(){
    return{
        data1:'xfdsaf'
    }
}

data(){
    return{
        data1:'fdsfa'
    }
}
```

**一个重要原则：**

由 `Vue` 管理的函数，一定不要写箭头函数，一旦写了箭头函数，`this` 就不再是 `Vue` 的实例了

## 1.4 MVVM模型

1. M：模型：data中的数据
2. V：视图：模板代码
3. VM：视图模型：Vue实例

![image-20220804094917992](https://gitee.com/ahaccmt/cloud-notes-typora/raw/master/typora-images/20220804094919.png)

**观察发现：**

1. `data` 中所有属性，最后都出现在 `vm` 上
2. `vm` 上所有的属性及 Vue原型上所有属性，在 Vue 模板中都可以直接使用



## 1.5 数据代理

### Object.defineProperty

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>初识Vue</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
    <script type="text/javascript">
        let number = 18
        let person = {
            name:'wy',
            sex:'male'
        }

        Object.defineProperty(person, 'age',{
            // value:13,
            enumerable:true, // 控制属性是否可以枚举，默认值为false
            // writable:true, // 控制属性是否可以被修改，默认值为false
            // configurable:true, // 控制属性是否可以被删除，默认值为false
            
            // 当 age 属性被读取时，get函数会被调用，且返回值是 age
            get(){
                return number
            },

            // 当 age 属性被修改时，set函数会被调用，且会收到修改的值
            set(value){
                number = value;
            }
        
        })
        console.log(person);

        console.log(Object.keys(person));

    </script>
</body>
</html>
```

### 深入理解

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vue核心</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
    <script type="text/javascript">
        let obj1 = {x:1}
        let obj2 = {y:2}

        Object.defineProperty(obj2, 'x',{
            get(){
                return obj1.x
            },

            set(value){
                obj1.x = value;
            }
        
        })

    </script>
</body>
</html>
```

**控制台验证**

![image-20220804102505512](https://gitee.com/ahaccmt/cloud-notes-typora/raw/master/typora-images/20220804102507.png)

![image-20220804102635176](https://gitee.com/ahaccmt/cloud-notes-typora/raw/master/typora-images/20220804102636.png)

### Vue中的数据代理

1. 通过 `vm` 对象来代理 `data` 对象中属性的操作 (读/写)

2. 更加方便地操作 `data` 中的数据

3. 基本原理

   通过 `Object.defineProperty()` 把 `data` 对象中所有属性添加到 `vm` 上。为每一个添加到 `vm` 上的属性，都指定一个 `getter/setter` ，在 `getter/setter` 内部去操作 `data` 中对应的属性

   

## 1.6 事件处理

### 事件基本使用

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vue核心</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
    <div id="root">
        <h1>{{name}}</h1>
        <button v-on:click="showInfo1">点我提示信息1</button>
        <button @click="showInfo2(666, $event)">点我提示信息2</button>
    </div>
    <script type="text/javascript">
        const vm = new Vue({
            el:'#root',
            data:{
                name:'hh',
            },
            methods:{
                showInfo1(event){
                    console.log(event.target.innerText);
                },
                showInfo2(number, event){
                    console.log(number);
                    console.log(event);
                }
            }
        })
    </script>
</body>
</html>
```

1. 使用 `v-on:xxx` 或 `@xxx` 绑定事件, xxx为事件名
2. 事件的回调需要配置在 `methods` 对象中，最终会在 `vm` 上
3. `methods` 中配置的函数，都是被 `Vue` 所管理的函数，`this` 的指向是 `vm` 或组件实例对象
4. `@click="demo"` 和 `@click="demo($event)"` 效果一致，但后者可以传递参数
5. 绑定事件时，`xxx` 可以写一些简单的语句，不过最好在 `methods` 中写函数封装。

### 事件修饰符

1. `prevent` : 阻止默认事件（常用）

   ```html
   <!DOCTYPE html>
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <meta http-equiv="X-UA-Compatible" content="IE=edge">
       <meta name="viewport" content="width=device-width, initial-scale=1.0">
       <title>事件修饰符/title>
       <script type="text/javascript" src="../js/vue.js"></script>
   </head>
   <body>
       <div id="root">
           <h1>{{name}}</h1>
           <a href="https://www.baidu.com" @click="showInfo">点击</a>
       </div>
   
       
       <script type="text/javascript">
           const vm = new Vue({
               el:'#root',
               data:{
                   name:'hh',
               },
               methods:{
                   showInfo(event){
                       alert('你好')
                   },
               }
           })
   
       </script>
   </body>
   </html>
   ```

   可以发现，`a` 标签的默认行为不会被执行

2. `stop` : 阻止事件冒泡（常用）

   ```html
   <script type="text/javascript">
       const vm = new Vue({
           el:'#root',
           data:{
               name:'hh',
           },
           methods:{
               showInfo(t){
                   console.log(t);
               },
           }
       })
   </script>
   ```

   **未添加阻止冒泡时**

   ```html
   <div id="root">
       <h1>{{name}}</h1>
       <div @click="showInfo(1)">
           <button @click="showInfo(2)">点击</button>
       </div>
   </div>
   ```

   ![image-20220805093803034](https://gitee.com/ahaccmt/cloud-notes-typora/raw/master/typora-images/20220805093805.png)

   **阻止冒泡后**

   ```html
   <div id="root">
       <h1>{{name}}</h1>
       <div @click="showInfo(1)">
           <button @click.stop="showInfo(2)">点击</button>
       </div>
   </div>
   ```

   ![image-20220805093939494](https://gitee.com/ahaccmt/cloud-notes-typora/raw/master/typora-images/20220805093941.png)

3. `once`: 事件只触发一次（常用）

   ```html
   <div id="root">
       <h1>{{name}}</h1>
       <div>
           <button @click.once="showInfo(2)">点击</button>
       </div>
   </div>
   ```

   多次点击后，只会执行一次 `showInfo(2)` 

4. `capture`: 使用事件的捕获模式

5. `self` : 只有 `event.target` 是当前操作的元素时才触发事件

6. `passive `:事件的默认行为立即执行，无需等待事件回调执行完毕

### 键盘事件

`Vue` 常用按键别名

![image-20220805094420703](https://gitee.com/ahaccmt/cloud-notes-typora/raw/master/typora-images/20220805094422.png)

触发条件: `keydown` 和 `keyup` 

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>键盘事件</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
    <div id="root">
        <input type="text" @keydown.tab="showInfo">
    </div>
    <script type="text/javascript">
        const vm = new Vue({
            el:'#root',
            data:{
                name:'hh',
            },
            methods:{
                showInfo(e){
                    console.log(e.target.value);
                },
            }
        })
    </script>
</body>
</html>
```

**补充**

组合按键: `<input type="text" @keydown.ctrl.y="showInfo">`

修饰符组合：

```html
<div id="root">
        <h1>{{name}}</h1>
        <div @click="showInfo(1)">
            <a href="https://baidu.com" @click.stop.prevent="showInfo(2)">点击</button>
        </div>
    </div>
```

不会跳转

![image-20220805102131673](https://gitee.com/ahaccmt/cloud-notes-typora/raw/master/typora-images/20220805102133.png)



## 1.7 计算属性

### 基本使用

1. 定义：要用的属性不存在，要通过已有属性计算得来
2. 原理：底层借助了 `Object.defineproperty` 方法提供的 `getter` 和 `setter` 
3. `get` 函数什么时候执行?
   * 初次读取时会执行一次
   * 当计算属性所依赖的数据发生改变时会被调用
4. 优势：与 `methods` 实现对比，内部有缓存机制（可复用），效率更高，调试方便
5. 备注：
   * 计算属性最终会出现在 `vm` 上，直接读取使用即可
   * 如果计算属性被修改，那必须写 `set` 函数去响应修改，且 `set` 中要引起计算时依赖的属性的数据发生改变



```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>计算属性</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
    <div id="root">
        <input type="text" v-model="firstName">
        <br/>
        <input type="text" v-model="lastName">
        <br/>
        <div>
            <h3>{{fullName}}</h3>
        </div>
    </div>
    <script type="text/javascript">
        const vm = new Vue({
            el:'#root',
            data:{
                firstName:'张',
                lastName:'三',
            },
            computed:{
                fullName:{
                    get(){
                        console.log('调用get函数')
                        return this.firstName + '-' + this.lastName
                    },

                    set(value){
                        console.log('调用set函数')
                        const arr = value.split('-')    
                        this.firstName = arr[0]
                        this.lastName = arr[1]
                    }
                }
            }
        })
    </script>
</body>
</html>
```

修改姓

![image-20220805111415798](https://gitee.com/ahaccmt/cloud-notes-typora/raw/master/typora-images/20220805111417.png)

![image-20220805111432175](https://gitee.com/ahaccmt/cloud-notes-typora/raw/master/typora-images/20220805111433.png)

修改计算属性

![image-20220805111508199](https://gitee.com/ahaccmt/cloud-notes-typora/raw/master/typora-images/20220805111509.png)

![image-20220805111503343](https://gitee.com/ahaccmt/cloud-notes-typora/raw/master/typora-images/20220805111504.png)

### 简写

计算属性只考虑读取，不考虑修改时，才可以进行简写

```js
computed:{
    fullName(){
        console.log('调用get函数')
        return this.firstName + '-' + this.lastName
    }
}
```

## 监视属性

1. 当被监视的属性变化时，回调函数自动调用，进行相关操作
2. 监视的属性必须存在，才能进行监视
3. 监视的两种写法：
   * `new Vue` 时传入 `watch` 配置
   * 通过 `vm.$watch` 监视

### 基本使用

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>监视属性</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
    <div id="root">
        <div>
            <h2>今天天气很{{info}}</h2>
            <button @click="change">点击切换天气</button>
        </div>
    </div>
    <script type="text/javascript">
        const vm = new Vue({
            el:'#root',
            data:{
                ishot:true,
            },
            methods: {
                change(){
                    this.ishot = !this.ishot
                }
            },
            computed:{
                info(){
                    return this.ishot ? '炎热' : '凉爽'
                }
            },
            // 第一种写法
            watch:{
                ishot:{
                    immediate:true, // 初始化时会被调用一次
                    handler(newValue, oldValue){ // 当 ishot 被修改时会被调用
                        console.log('ishot被修改', newValue, oldValue);
                    }
                }
            }
        })
        // 第二种写法
        vm.$watch('ishot', {
            immediate:true,
            handler(newValue, oldValue){
                console.log('ishot被修改', newValue, oldValue);
            }
        })
    </script>
</body>
</html>
```

### 深度监视

1. `Vue` 中的 `watch` 默认不监测对象内部值的改变（一层）
2. 配置 `deep:true` 可以监测对象内部值改变（多层）

备注：

1. `Vue` 自身可以监测到对象内部值的改变，但 `Vue` 提供的 `watch` 默认不可以
2. 使用 `watch` 时根据数据的具体结构，决定是否采用深度监视

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>监视属性</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
    <div id="root">
        <div>
            <h2 @click="number.a++">{{number.a}}</h2>
            <h2 @click="number.b++">{{number.b}}</h2>
        </div>
    </div>
    <script type="text/javascript">
        const vm = new Vue({
            el:'#root',
            data:{
                number:{
                    a:1,
                    b:1
                }
            },
            watch:{
                // 多级结构时写字符串形式
                'number.a':{
                    handler(){
                        console.log('a变化');
                    }
                }
            }
        })
    </script>
</body>
</html>
```

此时点击 `a` 会输出 a变化

```js
watch:{
    number:{
        deep:true, // 深度监视配置deep属性
            handler(){
            console.log('number变化');
        }
    }
}
```



### 简写

当 `wathch` 配置项只有 `handler` 时可以简写

```js
vm.$watch('ishot',function(newValue, oldValue){
    console.log('ishot被修改', newValue, oldValue)
})
```

```js
watch:{
    ishot(newValue, oldValue){
        console.log('ishot被修改', newValue, oldValue);
    }
}
```



### Watch vs Computed

**二者区别：**

1. computed 能完成的功能，watch 都能完成
2. watch 能完成的功能，computed 不一定能完成，例如：watch 可以进行异步操作

**两个重要的小原则：**

1. 被 `Vue` 管理的函数，最好写成普通函数，这样 `this` 的指向才是 `vm` 或组件实例对象
2. 所有不被 `Vue` 管理的函数（定时器的回调函数、ajax的回调函数、promise的回调函数等），最好写成箭头函数，这样 `this` 的指向才是 `vm` 或 组件实例对象



## 绑定class和style

### class

**基本写法**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>绑定样式</title>
    <script type="text/javascript" src="../js/vue.js"></script>
    <style>
        .happy{
            background-color: red;
        }

        .sad{
            background-color: blue;
        }

        .normal{
            background-color: wheat;
        }

        .basic{
            height: 100px;
            width: 200px;
            border: solid red 1px;
        }
    </style>
</head>
<body>
    <div id="root">
        <div class="basic" :class="css" @click="change"></div>
    </div>
    <script type="text/javascript">
        const vm = new Vue({
            el:'#root',
            data:{
                css:'normal'
            },
            methods: {
                change(){
                    styles = ['normal', 'sad', 'happy']
                    this.css = styles[Math.floor(Math.random()*3)]
                }
            },
        })
    </script>
</body>
</html>
```

以上示例可以通过点击实现样式随机切换

和绑定属性值类似

`:class="xxx"`

`xxx` 可填变量，数组或对象

**数组：**

```html
<body>
    <div id="root">
        <div class="basic" :class="arr"></div>
    </div>
    <script type="text/javascript">
        const vm = new Vue({
            el:'#root',
            data:{
                css:'normal',
                arr:['normal', 'sad', 'happy'],
            },
            methods: {
 
            },
        })
    </script>
</body>
```

**对象：**

```html
<body>
    <div id="root">
        <div class="basic" :class="cssObj"></div>
    </div>
    <script type="text/javascript">
        const vm = new Vue({
            el:'#root',
            data:{
                cssObj:{
                    'normal':true, // 使用 normal样式
                    'sad':false,
                    'happy':false,
                }
            },
        })
    </script>
</body>
```

### style

**原始写法**

```html
<div class="basic" style="font-size: 50px;">你好</div>
```

绑定后

```html
<div class="basic" :style="{fontSize: size + 'px'}">你好</div>
```

![image-20220806103853268](https://gitee.com/ahaccmt/cloud-notes-typora/raw/master/typora-images/20220806103855.png)

![image-20220806103912733](https://gitee.com/ahaccmt/cloud-notes-typora/raw/master/typora-images/20220806103914.png)

**对象写法**

```js
data:{
    styleObj:{
        fontSize: '60px',
        color: 'green',
        backgroundColor: 'yellow'
    }
},
```

前面的 key 不能乱写，一个单词正常写，两个单词用 `-` 连接的需要将第二个单词首字母大写，然后去掉 `-` 



![image-20220806104143764](https://gitee.com/ahaccmt/cloud-notes-typora/raw/master/typora-images/20220806104145.png)

style中也可以写 **数组**，但不常用



## 条件渲染

1. `v-if` 
   * 写法： 
     1. `v-if="表达式"`
     2. `v-else-if="表达式"` 
     3. `v-else="表达式"`
   * 适用于：切换频率较低的场景
   * 特点：不展示的 `DOM` 元素直接被移除
   * 注意：`v-if` 可以和 `v-else-if` 、`v-else` 一起使用，但要求结构不能被打断
2. `v-show`
   * 写法：`v-show="表达式"`
   * 适用于：切换频率较高的场景
   * 特点：不展示的 `DOM` 元素未被移出，仅仅是使用样式隐藏掉
3. 备注：使用 `v-if` 时，元素可能无法获取到，而使用 `v-show` 时一定可以获取到

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>条件渲染</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
    <div id="root">
        <h1 v-show="0">你好</h1>
        <h1 v-if="1 === 3">hello</h1>
    </div>
    <script type="text/javascript">
        const vm = new Vue({
            el:'#root',
            data:{

            },
        })
    </script>
</body>
</html>
```



![image-20220806110424753](https://gitee.com/ahaccmt/cloud-notes-typora/raw/master/typora-images/20220806110426.png)



## 列表渲染

`v-for` 指令

1. 用于展示列表数据
2. 语法：`v-for="(item, index) in xxx" :key="yyy"`
3. 可遍历：数组、对象、字符串（使用较少）、指定次数（使用较少）



```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>列表渲染</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
    <div id="root">
        <ul>
            <li v-for="(val, k) in persons" :key="k">
                {{val.name}}--{{val.age}}
            </li>
        </ul>
    </div>
    <script type="text/javascript">
        const vm = new Vue({
            el:'#root',
            data:{
                persons:[
                    {'name':'wy', 'age':20},    
                    {'name':'wm', 'age':16},
                    {'name':'xx', 'age':10},
                ]
            }
        })
    </script>
</body>
</html>
```

### Key作用

![image-20220806123457645](https://gitee.com/ahaccmt/cloud-notes-typora/raw/master/typora-images/20220806123459.png)

### 列表过滤

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>列表过滤</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
    <div id="root">
        <input type="text" v-model="keyword">
        <ul>
            <li v-for="(val, k) in persons_fill" :key="k">
                {{val.name}}--{{val.age}}
            </li>
        </ul>
    </div>
    <script type="text/javascript">
        // const vm = new Vue({
        //     el:'#root',
        //     data:{
        //         keyword:'',
        //         persons:[
        //             {'name':'wyy', 'age':20},    
        //             {'name':'wmx', 'age':16},
        //             {'name':'xxw', 'age':10},
        //             {'name':'adsf', 'age':20},    
        //             {'name':'vcx', 'age':16},
        //             {'name':'xwe', 'age':10},
        //             {'name':'wyy', 'age':20},    
        //             {'name':'wmx', 'age':16},
        //             {'name':'xxw', 'age':10},
        //             {'name':'adsf', 'age':20},    
        //             {'name':'vcx', 'age':16},
        //             {'name':'xwe', 'age':10},
        //         ],
        //         persons_fill:[

        //         ]
        //     },
        //     watch:{
        //         keyword:{
        //             immediate:true,
        //             handler(val){
        //                 this.persons_fill = this.persons.filter((p)=>{
        //                     return p.name.indexOf(val) !== -1
        //                 })
        //             }
                    
        //         }
        //     }
        // })
        
        const vm = new Vue({
            el:'#root',
            data:{
                keyword:'',
                persons:[
                    {'name':'wyy', 'age':20},    
                    {'name':'wmx', 'age':16},
                    {'name':'xxw', 'age':10},
                    {'name':'adsf', 'age':20},    
                    {'name':'vcx', 'age':16},
                    {'name':'xwe', 'age':10},
                    {'name':'wyy', 'age':20},    
                    {'name':'wmx', 'age':16},
                    {'name':'xxw', 'age':10},
                    {'name':'adsf', 'age':20},    
                    {'name':'vcx', 'age':16},
                    {'name':'xwe', 'age':10},
                ],
            },
            computed:{
                persons_fill(){
                    return this.persons.filter((p)=>{
                        return p.name.indexOf(this.keyword) !== -1
                    })
                }
            }
        })
    </script>
</body>
</html>
```

**测试结果：**

![image-20220806131131980](https://gitee.com/ahaccmt/cloud-notes-typora/raw/master/typora-images/20220806131133.png)

​	

### 列表排序

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>列表排序</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
    <div id="root">
        <h1>人员列表</h1>
        <input type="text" v-model="keyword">
        <button @click="sortType = 1">升序</button>
        <button @click="sortType = 2">降序</button>
        <button @click="sortType = 0">原序</button>

        <ul>
            <li v-for="(val, k) in persons_fill" :key="k">
                {{val.name}}--{{val.age}}
            </li>
        </ul>
    </div>
    <script type="text/javascript">
        
        const vm = new Vue({
            el:'#root',
            data:{
                keyword:'',
                sortType:0,
                persons:[
                    {'name':'wyy', 'age':20},    
                    {'name':'wmx', 'age':16},
                    {'name':'xxw', 'age':10},
                    {'name':'adsf', 'age':20},    
                    {'name':'vcx', 'age':16},
                    {'name':'xwe', 'age':10},
                    {'name':'wyy', 'age':20},    
                    {'name':'wmx', 'age':16},
                    {'name':'xxw', 'age':10},
                    {'name':'adsf', 'age':20},    
                    {'name':'vcx', 'age':16},
                    {'name':'xwe', 'age':10},
                ],
            },
            computed:{
                persons_fill(){
                    const a = this.persons.filter((p)=>{
                        return p.name.indexOf(this.keyword) !== -1
                    })

                    if (this.sortType)
                    {
                        a.sort((a, b)=>{
                            return this.sortType === 1 ? a.age - b.age : b.age - a.age;
                        })
                    }
                    return a;
                }
            }
        })
    </script>
</body>
</html>
```

**测试结果：**

![image-20220807093103341](https://gitee.com/ahaccmt/cloud-notes-typora/raw/master/typora-images/20220807093105.png)



**升序和降序**

![image-20220807093203721](https://gitee.com/ahaccmt/cloud-notes-typora/raw/master/typora-images/20220807093205.png)

![image-20220807093210080](https://gitee.com/ahaccmt/cloud-notes-typora/raw/master/typora-images/20220807093211.png)

### 模拟Vue进行数据监测（对象）

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>模拟数据监测</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
    <div id="root">
    </div>
    <script type="text/javascript">
        
        let data = {
            name:'wy',
            age:23,
        }
        // 创建一个监视的实例对象，用于监视data中属性的变化
        const obs = new Observer(data)

        let vm = {}
        vm._data = data = obs

        function Observer(obj) {
            const keys = Object.keys(obj)
            // 汇总对象中所有属性形成一个数组
            keys.forEach((k)=>{
                Object.defineProperty(this, k, {
                    get(){
                        return obj[k]
                    },

                    set(val){
                        obj[k] = val
                    }
                })
            })
        }
    </script>
</body>
</html>
```

**测试结果**

![image-20220807101633970](https://gitee.com/ahaccmt/cloud-notes-typora/raw/master/typora-images/20220807101635.png)



### Vue.set使用

向 `Vue` 中添加属性

**注意：`tartget` 不能为 `vm` 本身或者 `vm._data`** 

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vue.set使用</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
    <div id="root">
        <h1>name: {{name}}</h1>
        <h1>age: {{age}}</h1>
        <h2>school</h2>
        <h3>name: {{school.name}}</h3>
        <h3 v-if="school.loc">loc: {{school.loc}}</h3>
    </div>
    <script type="text/javascript">
        
        const vm = new Vue({
            el:'#root',
            data:{
                name:'wy',
                age:18,
                school:{
                    name:'zzu',
                }
            }
        })
        
    </script>
</body>
</html>
```

方式一：

```js
Vue.set(vm.school, 'loc', 'zz')
```

方式二：

```js
vm.$set(vm.school, 'loc', 'zz')
```

执行结果：

![image-20220807103941825](https://gitee.com/ahaccmt/cloud-notes-typora/raw/master/typora-images/20220807103943.png)



### Vue监测数组

![image-20220807114034420](https://gitee.com/ahaccmt/cloud-notes-typora/raw/master/typora-images/20220807114036.png)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>模拟数据监测数组</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
    <div id="root">
        <h1>爱好</h1>
        <ul>
            <li v-for="(val, k) in hobby" :key="k">
                {{val}}
            </li>
        </ul>
    </div>
    <script type="text/javascript">
        const vm = new Vue({
            el:'#root',
            data:{
                hobby:['听音乐', '打游戏', '唱歌', '跑步']
            }
        })
    </script>
</body>
</html>
```

数组中的元素没有 `getter` 和 `setter` 方法

![image-20220807113100914](https://gitee.com/ahaccmt/cloud-notes-typora/raw/master/typora-images/20220807113102.png)

直接使用数组下标对其修改在页面上不会体现效果

```js
vm._data.hobby[0] = '睡觉'
```

`Vue` 将被侦听的数组的变更方法进行了包裹，所以它们也将会触发视图更新。这些被包裹过的方法包括：

- `push()`

- `pop()`

- `shift()`

- `unshift()`

- `splice()`

  ```js
  vm.hobby.splice(0, 1, '吃饭')
  ```

- `sort()`

- `reverse()`



## 收集表单数据



![image-20220807115903853](https://gitee.com/ahaccmt/cloud-notes-typora/raw/master/typora-images/20220807115905.png)

实例

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>收集表单数据</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
    <div id="root" @submit.prevent="submit">
        <form>
            <div>
                账户: <input type="text" v-model.trim="userinfo.username">
            </div>
            <div>
                密码: <input type="password" v-model="userinfo.password">
            </div>
            <div>
                性别: 男<input type="radio" name="sex" v-model="userinfo.sex" value="male">女<input type="radio" name="sex" v-model="userinfo.sex" value="female">
            </div>
            <div>
                年龄: <input type="number" v-model.number="userinfo.age">
            </div>
            <div>
                爱好:
                <input type="checkbox" v-model="userinfo.hobby" value="eat">吃饭
                <input type="checkbox" v-model="userinfo.hobby" value="sleep">睡觉
                <input type="checkbox" v-model="userinfo.hobby" value="drink">喝酒
                <input type="checkbox" v-model="userinfo.hobby" value="gameing">打游戏
            </div>
            <div>
                其他: <textarea name="" id="" cols="30" rows="10" v-model.lazy="userinfo.other"></textarea>
            </div>
            <button>提交</button>
        </form>
    </div>
    <script type="text/javascript">
        
        const vm = new Vue({
            el:'#root',
            data:{
                userinfo:{
                    username:'',
                    password:'',
                    sex:'',
                    age:'',
                    hobby:[],
                    other:'',
                }
            },
            methods: {
                submit(){
                    console.log(JSON.stringify(this.userinfo));
                }
            },
        })
        
    </script>
</body>
</html>
```



## 待更新...

## 参考资料

> 尚硅谷Vue2.0+Vue3.0全套教程丨vuejs从入门到精通
> https://www.bilibili.com/video/BV1Zy4y1K7SH?p=15&share_source=copy_web&vd_source=d3c9ceb0642f45fbe95f795c0d074040
>
> Vue 官方文档
>
> https://cn.vuejs.org/v2/guide/
>
> Vue api 文档
>
> https://cn.vuejs.org/v2/api/
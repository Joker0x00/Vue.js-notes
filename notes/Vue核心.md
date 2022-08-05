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



## 待更新...

## 参考资料

> 尚硅谷Vue2.0+Vue3.0全套教程丨vuejs从入门到精通
> https://www.bilibili.com/video/BV1Zy4y1K7SH?p=15&share_source=copy_web&vd_source=d3c9ceb0642f45fbe95f795c0d074040
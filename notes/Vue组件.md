# 2. Vue 组件化编程

## 2. 1 非单文件组件

![image-20220809103737820](https://gitee.com/ahaccmt/cloud-notes-typora/raw/master/typora-images/20220809103740.png)

### 快速入门

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>非单文件组件</title>
    <script type="text/javascript" src="../js/vue.js"></script>
    <script src="https://cdn.bootcdn.net/ajax/libs/dayjs/1.11.4/dayjs.min.js"></script>

</head>
<body>
    <div id="root">
        <school></school>
        <student></student>
        <hello></hello>
    </div>
    <script type="text/javascript">
        const school = Vue.extend({
            template:`
                <div>
                    <h1>{{name}}</h1>
                    <h1>{{loc}}</h1>
                </div>
            `,
            data(){
                return {
                    name:'AAA',
                    loc:'BBB',
                }
            }
        })
        const student = Vue.extend({
            data(){
                return {
                    name:'王二',
                    age:18
                }
            },
            template:`
                <div>
                    <h1>{{name}}</h1>
                    <h1>{{age}}</h1>
                </div>
            `,
        })

        const hello = Vue.extend({
            data(){
                return {
                    name:'王二',
                }
            },
            template:`
                <div>
                    <h1>hello, {{name}}</h1>
                </div>
            `,
        })
        // 注册为全局组件
        Vue.component('hello', hello)
        const vm = new Vue({
            el:'#root',
            // 配置局部组件
            components:{
                school,
                student
            }
        })
    </script>
</body>
</html>
```

### 组件命名注意点

![image-20220809105645016](https://gitee.com/ahaccmt/cloud-notes-typora/raw/master/typora-images/20220809105646.png)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>命名的注意点</title>
    <script type="text/javascript" src="../js/vue.js"></script>
    <script src="https://cdn.bootcdn.net/ajax/libs/dayjs/1.11.4/dayjs.min.js"></script>

</head>
<body>
    <div id="root">
        <!-- 注册时写什么名字，这里用的时候就写什么名字 -->
        <my-school></my-school> 
        <student></student>
        <hello></hello>
    </div>
    <script type="text/javascript">
        const school = {
            template:`
                <div>
                    <h1>{{name}}</h1>
                    <h1>{{loc}}</h1>
                </div>
            `,
            data(){
                return {
                    name:'AAA',
                    loc:'BBB',
                }
            }
        }
        const student = Vue.extend({
            name:'Wanger', // 在Vue开发者工具显示的名字，如不定义，就显示注册的名字
            data(){
                return {
                    name:'王二',
                    age:18
                }
            },
            template:`
                <div>
                    <h1>{{name}}</h1>
                    <h1>{{age}}</h1>
                </div>
            `,
        })

        const hello = Vue.extend({
            data(){
                return {
                    name:'王二',
                }
            },
            template:`
                <div>
                    <h1>hello, {{name}}</h1>
                </div>
            `,
        })
        // 注册为全局组件
        Vue.component('hello', hello)
        const vm = new Vue({
            el:'#root',
            // 配置局部组件
            components:{
                'my-school':school,
                student
            }
        })
    </script>
</body>
</html>
```



### 组件的嵌套

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>组件的嵌套</title>
    <script type="text/javascript" src="../js/vue.js"></script>
    <script src="https://cdn.bootcdn.net/ajax/libs/dayjs/1.11.4/dayjs.min.js"></script>

</head>
<body>
    <div id="root">
        <app></app>
    </div>
    <script type="text/javascript">
        const student = Vue.extend({
            name:'student',
            data(){
                return {
                    name:'王二',
                    age:18
                }
            },
            template:`
                <div>
                    <h1>{{name}}</h1>
                    <h1>{{age}}</h1>
                </div>
            `,
        })
        
        const school = {
            name:'school',
            template:`
                <div>
                    <h1>{{name}}</h1>
                    <h1>{{loc}}</h1>
                    <student></student>
                </div>
            `,
            data(){
                return {
                    name:'AAA',
                    loc:'BBB',
                }
            },
            components:{
                student
            }
        }
        

        const hello = Vue.extend({
            name:'hello',
            data(){
                return {
                    name:'王二',
                }
            },
            template:`
                <div>
                    <h1>hello, {{name}}</h1>
                </div>
            `,
        })
        const app = {
            template:`
                <div>
                    <school></school> 
                    <hello></hello>    
                </div>
            `,
            components:{
                school,
                hello
            }
        }
        const vm = new Vue({
            el:'#root',
            // 配置局部组件
            components:{
                app
            }
        })
    </script>
</body>
</html>
```



![image-20220809111535409](https://gitee.com/ahaccmt/cloud-notes-typora/raw/master/typora-images/20220809111537.png)

### VueComponent构造函数

![image-20220809113541190](https://gitee.com/ahaccmt/cloud-notes-typora/raw/master/typora-images/20220809113542.png)



## 持续更新中...

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


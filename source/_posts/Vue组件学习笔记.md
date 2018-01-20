---
title: Vue组件学习笔记
---
###### Vue父组件向子组件传递数据
```
<div id="app">
    <my-component :init-count="count"></my-component>
</div>
<script>
    const Child = {
    props: {
          initCount: Number
    },
    template: '<div>{{ count }}</div>',
    data () {
         return {
             count: this.initCount
         }
     },
     methods: {
          change () {
              this.count = 2;
          }
      }
    };
    new Vue({
        el: '#app',
        components: {
            'my-component': Child
        },
        data: {
            count: 1
        }
    });
</script>
```
这里子组件里的:init-count指的是props中的initCount，其对应的值是count(父组件中的count)，指定是Number类型。
###### Vue的is特性
包括自定义元素和特殊标签，如<component>、<template>、<paetial>不能用在ul,select,table等对内部元素有限制的标签内。放在这些元素内部的自定义标签将被提到元素的外面，因而渲染不正确。对于自定义元素，应当使用is特性：
```
<div id="app">
     <table>
         <tbody is="my-component"></tbody>
     </table>
 </div>
 <script>
     Vue.component('my-component', {
         template: '<div>Table</div>'
     });
     new Vue({
         el: '#app'
     })
 </script>
```
###### 使用v-model进行组件通讯
子组件接受一个value属性，在有新的value时触发input事件，在子组件接受父组件的方法时，使用watch来监听接收的value的变化。
```
<div id="app">
    {{ value }}
    <my-com v-model="value"></my-com>
    <button @click="handleMinus">-1</button>
</div>
<script>
    Vue.component('my-com', {
        props: {
            value: {
                type: Number
            }
        },
        template: '<div>{{ currentValue }}<button @click="handleClick">+1</button></div>',
        data () {
            return {
                currentValue: this.value
            }
        },
        watch: {
            value (val) {
                this.currentValue = val;
            }
        },
        methods: {
            handleClick () {
                this.currentValue ++;
                this.$emit('input', this.currentValue);
            }
        }
    });
    new Vue({
        el: '#app',
        data: {
            value: 1
        },
        methods: {
            handleMinus () {
                this.value --;
            }
        }
    });
</script>
```
###### slot用法
```
<div id="app">
    <app>
        {{ message }}
    </app>
</div>
<script>
    Vue.component('app', {
        template: '<div>Hello<slot>来自子组件的slot默认内容</slot></div>',
        data () {
            return {
                message: '你好'
            }
        }
    });
    new Vue({
        el: '#app',
        data: {
            message: 'world'
        },
        mounted(){
            this.$slots.default;
        }
    });
</script>
```
原本app的message为world，我们需要在子组件中使用slot标签显示父组件中的message，当app中没有内容的时候，就会显示slot标签中的默认内容。
多个slot用法：使用具名方法<slot name="a"></slot>，在app标签中则对应显示<div slot="a"></div>里面的内容。
访问slot：在vue2.0中，使用$slots来访问，单个slot时default为默认，使用具名时则填写对应的具名。
###### 内联模板
```
<com1 inline-template>
    <!-- 内容 -->
</com1>
```
当组件使用了inline-template后，内容 将不再是 slot ，而是这个 com1 组件的 template ，也就是会渲染出内容。
###### 手动挂载
```
<div id="app">
    Hello Vue
</div>
<script>
    const myComponent = Vue.extend({
        template: '<div>from extend</div>'
    });
    new myComponent().$mount('#app');
</script>
```
使用$.mount()手动挂载到div中

###### vue-router技巧
```
const router = new Router({
    mode: 'history',
    routes : [
    {
        path: '/index',
        meta: {
            title: '首页'
        },
        component: (resolve) => require(['./index.vue'], resolve)
    },
    {
        path: '*',
        redirect: '/index'
    },
    {
         path: '/',
         component: {
             default: Defaultvue, //这里是默认路由
             title: Title
         } 
     }]
});
router.beforeEach((to, from, next) => {
    window.document.title = to.meta.title;
    next();
});
router.afterEach((to, from, next) => {
    window.scrollTo(0, 0);
});
```
1.这里设置了history模式,官方上给出的介绍是:vue-router 默认 hash 模式 —— 使用 URL 的 hash 来模拟一个完整的 URL，于是当 URL 改变时，页面不会重新加载。如果不想要很丑的 hash，我们可以用路由的 history 模式，这种模式充分利用 history.pushState API 来完成 URL 跳转而无须重新加载页面。
2.设置path:'*',没有匹配到的路由,将跳转到'/index'中
3.使用router.beforeEach改变其网页的title,使用next();进入到下一个钩子当中
4.使用router.afterEach让跳转后的回到页面顶部
5.component里的default:默认路由写法对应视图中同时有两个<router-view> 其中一个为<router-view name="title"> 没有则为默认

#### 新版vue-cli本地服务器加载本地资源
1.在webpack.dev.conf.js文件``` const portfinder = require('portfinder') ``` 下面引入express
```
const express = require('express')
const app = express()
var appData = require('../mock/goods.json')
var apiRoutes = express.Router()
app.use('/api', apiRoutes)
```
2.在``` devServer ``` 对象中写入传输地址
```
    before(apiRoutes) {
      apiRoutes.get('/api/goods', function(req, res, next) {
        res.json(appData)
      })
    }
```

(简书笔记搬运 写于2017.06.29 13:45)
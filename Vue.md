# 认识Vue

是一套用于构建用户界面的渐进式JavaScript框架，基于HTML、CSS和JS，并提供了一套声明式的、组件化的编程模型。渐进表示在项目中一点点引入使用Vue。

目前最流行的前端三大框架：Vue(必学，国内占比最高)、React(必学框架，国外最常见)、Angular(入门门槛较高、需先学会TS)

Vue的本质就是一个JavaScript库

```JS
//通过CDN引入
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vue</title>
</head>

<body>
    <div id="title"></div>
    <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
    <script>
        const title = Vue.createApp({
            template: `<h2>hello</h2>`
        })
        title.mount("#title")
    </script>
</body>

</html>
```

```js
//本地引入，将文件复制给本地js文件
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>

<body>
    <div class="title"></div>
    <script src="./libraries/Vue.js"></script>
    <script>
        const title = Vue.createApp({
            template: `<h2>hello</h2>`
        })
        title.mount(".title")
    </script>
</body>

</html>
```

template的内容会覆盖掉选择器里面的内容

## 计时器案例

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>计时器</title>
</head>

<body>
    <div class="time"></div>
    <script src="./libraries/Vue.js"></script>
    <script>
        const app = Vue.createApp({
            template: `
            <div>{{count}}</div>
            <button @click='increment'>+1</button>
            <button @click='decrement'>-1</button>
            `,
            data() {
                return { count: 0 }
            },
            methods: {
                increment() {
                    this.count++
                },
                decrement() {
                    this.count--
                }
            }
        })
        app.mount('.time')
    </script>
</body>

</html>
```

重构，若没有template，则直接把选择器元素里面的内容作为template。

```js
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>计时器</title>
</head>

<body>
    <div class="time">
        <div>{{count}}</div>
        <button @click='increment'>+1</button>
        <button @click='decrement'>-1</button>
    </div>
    <script src="./libraries/Vue.js"></script>
    <script>
        const app = Vue.createApp({
            data: function () {
                return { count: 0 }
            },
            methods: {
                increment() {
                    this.count++
                },
                decrement() {
                    this.count--
                }
            }
        })
        app.mount('.time')
    </script>
</body>

</html>
```

原生的JS是命令式编程：机器执行一条条指令。

MVC:model-view-controller

MVVM:model-view-viewmodel,Vue受MVVM启发，vue充当viewmodel，有DOM监听绑定和data绑定功能。

## data

```js
const app = Vue.createApp({
    data: function(){
        return{
            message: 'lll'
        }
    }
})
```

vue3要求data必须返回一个函数，函数返回对象，vue2可以直接是一个对象

data中返回的对象会被Vue响应式系统劫持，之后对该对象的修改或者访问都会在劫持中被处理

## methdos

```js
const app = Vue.createApp({
    methods:{
        increment: function(){},
        decrement: function(){}
    }
    
})
```

methods是一个对象，里面写的函数可以添加至模板中

method中函数的this是指向一个代理，该代理管理着data返回对象

method中的函数不能用箭头函数，因为箭头函数的this指向父级函数作用域

# this

预编译的时候会产生AO和GO（global object）

# 模板语法

## mustache插值语法

```html
{{}}里面可以写表达式，还可以包含methods里面的函数
<h2>
    信息：{{message}}
    数量：{{number * 2}}
    展示：{{info.split(" ")}} //info: "how are you"
    函数：{{mymethod()}}
</h2>
```

## v-once指令

写在元素的<>里面

作用：当前元素以及其子元素作为静态内容只渲染一次

```JS
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>v-once</title>
</head>

<body>
  <div class='info'>
    <p v-once>{{message}}</p>
    <p>{{message}}</p>
    <button @click="change">变</button>
  </div>
  <script src="./libraries/vue.js"></script>
  <script>
    const app = Vue.createApp({
      data: function () {
        return {
          message: 'hello'
        }
      },
      methods: {
        change: function () {
          this.message = '你好'
        }
      }
    })
    app.mount('.info')
  </script>
</body>

</html>
```

## v-text

```js
//文本内容，会覆盖元素原本内容。hello是data里面的
<p v-text="hello">你好</p>
```

## v-html

```js
//会覆盖元素原本内容。hello是data里面的。会解析为html格式
<p v-html="message">你好</p>
//message : `<span style="color : red;">hello</span>`
```

## v-pre

```js
//跳过元素和它子元素的编译过程，显示原始的Mustache标签
```

## v-cloak

```js
//浏览器先加载html元素内容，再加载文件，运行脚本
//v-cloak配合着css的style，不展示没渲染好的v-cloak元素，当完全渲染后删除v-cloak
//避免网络延迟暴露源码
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>v-once</title>
  <style>
    [v-cloak] {
      display: none;
    }
  </style>
</head>

<body>
  <div class='info'>
    <p v-cloak>{{message}}</p>
    <button @click="change">变</button>
  </div>
  <script src="./libraries/vue.js"></script>
  <script>
    const app = Vue.createApp({
      data: function () {
        return {
          message: 'hello'
        }
      },
      methods: {
        change: function () {
          this.message = '你好'
        }
      }
    })
    setTimeout(() => {
      app.mount('.info')

    }, 3000)
  </script>
</body>

</html>
```

## v-memo

**缓存一个模板的子树**。在元素和组件上都可以使用。为了实现缓存，该指令需要传入一个固定长度的依赖值数组进行比较。**如果数组里的每个值都与最后一次的渲染相同**，**那么整个子树的更新将被跳过**。

<div v-memo="[valueA, valueB]">
  ...
</div>

当组件重新渲染，如果 `valueA` 和 `valueB` 都保持不变，这个 `<div>` 及其子项的所有更新都将被跳过。实际上，甚至虚拟 DOM 的 vnode 创建也将被跳过，因为缓存的子树副本可以被重新使用。

## v-bind

```html
动态绑定基本属性
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>v-bind</title>

</head>

<body>
  <div class='info'>
    <img v-bind:src="img" />
    <a :href="baidu">百度</a>
  </div>
  <script src="./libraries/vue.js"></script>
  <script>
    const app = Vue.createApp({
      data: function () {
        return {
          baidu: 'https://www.baidu.com/',
          img: "https://gips0.baidu.com/it/u=1690853528,2506870245&fm=3028&app=3028&f=JPEG&fmt=auto?w=1024&h=1024s",
        }
      },
    })
    app.mount('.info')
  </script>
</body>

</html>
```

```html
//动态绑定对象，类的对象中属性值是true/false，表示是否存在
//与写死的类兼容
//可以写多个属性{isActive: false,isBold: true}
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>v-bind绑定对象</title>
  <style>
    .bold {
      font-weight: 400;
    }

    .active {
      color: red;
    }
  </style>

</head>

<body>
  <div class='info'>
    <p class="bold" :class="{active : isActive}">你好</p>
    <button @click='switchColor'>切换</button>
  </div>
  <script src="./libraries/vue.js"></script>
  <script>
    const app = Vue.createApp({
      data: function () {
        return {
          isActive: false,
        }
      },
      methods: {
        switchColor: function () {
          this.isActive = !this.isActive
        }
      }
    })
    app.mount('.info')
  </script>
</body>

</html>
```

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>v-bind绑定对象</title>
  <style>
    .bold {
      font-weight: 400;
    }

    .active {
      color: red;
    }
  </style>

</head>

<body>
  <div class='info'>
    <p class="bold" :class="getIsActive()">你好</p>
    <button @click='switchColor'>切换</button>
  </div>
  <script src="./libraries/vue.js"></script>
  <script>
    const app = Vue.createApp({
      data: function () {
        return {
          isActive: false,
        }
      },
      methods: {
        switchColor: function () {
          this.isActive = !this.isActive
        },
        getIsActive: function () {
          return { active: this.isActive }
        }
      }
    })
    app.mount('.info')
  </script>
</body>

</html>
```

```html
//动态绑定数组
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>v-bind绑定数组</title>
  <style>
    .bold {
      font-weight: 400;
    }

    .active {
      color: red;
    }
  </style>

</head>

<body>
  <div class='info'>
    <p class="bold" :class="['asd',classname,{active : isActive}]">你好</p>
    <button @click='switchColor'>切换</button>
  </div>
  <script src="./libraries/vue.js"></script>
  <script>
    const app = Vue.createApp({
      data: function () {
        return {
          isActive: false,
          classname: 'erw'
        }
      },
      methods: {
        switchColor: function () {
          this.isActive = !this.isActive
        }
      }
    })
    app.mount('.info')
  </script>
</body>

</html>
```

```html
//动态绑定style，使用数组和对象
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>v-bind绑定数组、对象</title>
</head>

<body>
  <div class='info'>
    <p :style="[objstyle, { color : 'red','font-size': '30px'}]">你好</p>

  </div>
  <script src="./libraries/vue.js"></script>
  <script>
    const app = Vue.createApp({
      data: function () {
        return {
          objstyle: {

          }
        }
      },
    })
    app.mount('.info')
  </script>
</body>

</html>
```

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>v-bind绑定属性名、属性值</title>
</head>

<body>
  <div class='info'>
    <p v-bind="obj">你好</p>

  </div>
  <script src="./libraries/vue.js"></script>
  <script>
    const app = Vue.createApp({
      data: function () {
        return {
          obj: {
            name: 'l',
            age: 18,
          }
        }
      },
    })
    app.mount('.info')
  </script>
</body>

</html>
```

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>v-bind绑定属性名、属性值</title>
</head>

<body>
  <div class='info'>
    <p :[name]="l">你好</p>

  </div>
  <script src="./libraries/vue.js"></script>
  <script>
    const app = Vue.createApp({
      data: function () {
        return {
          name: 'say',
          l: 'l'
        }
      },
    })
    app.mount('.info')
  </script>
</body>

</html>
```

## v-on

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>v-on</title>
</head>
<style>
  .inner {
    width: 100px;
    height: 100px;
    background-color: orange;
  }
</style>

<body>
  <div class='info'>
    <div class="inner" v-on:click="divclick" @mousemove="divmousemove"></div>
    <div class="inner" v-on="{click : divclick}"></div>
    <div class="inner" @click="count++">{{count}}</div>
  </div>
  <script src="./libraries/vue.js"></script>
  <script>
    const app = Vue.createApp({
      data: function () {
        return {
          count: 1
        }
      },
      methods: {
        divclick() {
          console.log('click')
        },
        divmousemove() {
          console.log('mousemove')
        }
      }
    })
    app.mount('.info')
  </script>
</body>

</html>
```

```html
//参数不带引号即为变量，会在data里面找，带了引号是字符串
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>v-on参数</title>
</head>
<style>
  .inner {
    width: 100px;
    height: 100px;
    background-color: orange;
  }
</style>

<body>
  <div class='info'>
    <div class="inner" v-on:click="divclick" @mousemove="divmousemove('ll',count)"></div>
  </div>
  <script src="./libraries/vue.js"></script>
  <script>
    const app = Vue.createApp({
      data: function () {
        return {
          count: 1
        }
      },
      methods: {
        //不带参数，默认传了event事件
        divclick() {
          console.log(event)
        },
        divmousemove(a, b,$event) {
          console.log(a, b,event)
        }
      }
    })
    app.mount('.info')
  </script>
</body>

</html>
```

```html
修饰符
<div v-on:click.stop="">
    .stop阻止冒泡==event.stopPropagation
</div>
<button @click.prevent="">
    阻止按钮默认行为==event.preventDefault()
</button>
```

## v-if、v-else-if、v-else

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title></title>
</head>

<body>
  <div class='info'>
    <h2 v-if="score >= 90">优秀</h2>
    <h2 v-else-if="score >= 60">及格</h2>
    <h2 v-else>不及格</h2>
  </div>
  <script src="./libraries/vue.js"></script>
  <script>
    const app = Vue.createApp({
      data() {
        return {
          score: 20
        }
      },
      methods: {
      }
    })
    app.mount('.info')
  </script>
</body>

</html>
```

```html
//Object.keys(obj)是一个对象的属性组成的数组
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title></title>
</head>

<body>
  <div class='info'>
    <p v-if="Object.keys(obj).length">{{obj.name}}</p>

    <p v-else>查无此人</p>
  </div>
  <script src="./libraries/vue.js"></script>
  <script>
    const app = Vue.createApp({
      data() {
        return {
          obj: {
            name: 'l'
          }
        }
      },
      methods: {
      }
    })
    app.mount('.info')
  </script>
</body>

</html>
```

v-if是惰性的：但条件为false时，其判断的内容完全不会被渲染或者直接被销毁

```html
//template当作不可见的包裹元素，在v-if上使用，最终template不会被渲染出来
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title></title>
</head>

<body>
  <div class='info'>
    <template v-if="Object.keys(obj).length">
      <p>{{obj.name}}</p>
      <p>{{obj.age}}</p>
    </template>
    <template v-else>
      <p>抱歉</p>
      <p>查无此人</p>
    </template>

  </div>
  <script src="./libraries/vue.js"></script>
  <script>
    const app = Vue.createApp({
      data() {
        return {
          obj: {

          }
        }
      },
      methods: {
      }
    })
    app.mount('.info')
  </script>
</body>

</html>
```

## v-show

v-show：用法和v-if一样，不能和v-else连用。**不支持template**，因为v-show元素无论是否需要显示到浏览器上，它的DOM实际都是存在的，只是通过CSS的display属性来进行切换；当条件为false时，v-if对应的元素不会被渲染到DOM中。

当我们需要频繁切换元素的显示和隐形，推荐使用v-show；不频繁切换使用v-if

## v-for

in 也可以写成of

```html
<!--遍历数组-->
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>v-for</title>
</head>

<body>
  <div id='info'>
    <!--index是索引号，可省略-->
    <div v-for="(item,index) in products">
      <h2>{{item.name}}</h2>
      <p>价格：{{item.price}}</p>
    </div>
  </div>

  <script src="./libraries/vue.js"></script>
  <script>
    const app = Vue.createApp({
      data() {
        return {
          products: [
            { id: '1', name: 'mi', price: 1999 },
            { id: '2', name: 'vivo', price: 1998 }
          ]
        }
      },
    })
    app.mount('#info')
  </script>
</body>

</html>
```

```html
<!--遍历对象、字符串等可迭代对象、数字-->
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>v-for</title>
</head>

<body>
  <div id='info'>
    <!--index是索引号，可省略-->
    <div v-for="(value,key,index) in person">
      <strong>{{key}}:</strong>
      <span>{{value}}</span>
    </div>
    <template v-for="i in 10">
      <!--依次遍历1 2 3 4....-->
      <span>{{i}}</span>
    </template>
    <template v-for="i in 'asd'">
      <!--依次遍历a s d-->
      <span>{{i}}</span>
    </template>
  </div>

  <script src="./libraries/vue.js"></script>
  <script>
    const app = Vue.createApp({
      data() {
        return {
          person: { name: 'mi', age: '19' },
        }
      },
    })
    app.mount('#info')
  </script>
</body>

</html>
```

```
name:mi
age:19
12345678910asd
```

```html
<!--vue包装一批侦听数组的变更方法，以至于这些方法可以触发试图更新-->
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>v-for</title>
</head>

<body>
  <div id='info'>
    <div v-for="item in arr">
      {{item}}
    </div>
    <button @click="change">变</button>
  </div>

  <script src="./libraries/vue.js"></script>
  <script>
    const app = Vue.createApp({
      data() {
        return {
          arr: ['a', 'b', 'd', 'w']
        }
      },
      methods: {
        change() {
          this.arr.splice(3, 2, 'p', 'o', 'i')
        }
      }
    })
    app.mount('#info')
  </script>
</body>

</html>
```

方法包括有push()、pop()、shift()、unshift()、splice()、sort()、reverse()

```
splice(start, deleteCount, item1, item2, /* …, */ itemN)
splice既可以添加元素，也可以删除元素item1, item2, /* …, */ itemN是添加的元素
```

### 虚拟DOM

```html
<!--使用v-for时，通常会给元素、组件绑定一个key属性-->
<!--key属性用在虚拟DOM算法，在新旧nodes对比时辨识VNodes-->
<!--不使用key，vue尽可能就地修改/复用相同类型元素的算法-->
<!--使用key，vue会基于key的变化重新排列元素顺序，移除/销毁不存在的元素-->
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>v-for</title>
</head>

<body>
  <div id='info'>
    <div v-for="item in arr" :key="item">
      {{item}}
    </div>
    <button @click="change">变</button>
  </div>

  <script src="./libraries/vue.js"></script>
  <script>
    const app = Vue.createApp({
      data() {
        return {
          arr: ['a', 'b', 'd', 'w']
        }
      },
      methods: {
        change() {
          this.arr.splice(3, 0, 'p', 'o', 'i')
        }
      }
    })
    app.mount('#info')
  </script>
</body>

</html>
```

vnode的v是virtual，表示虚拟节点。本质是一个JS对象。

template->VNode->真实DOM.

实际情况是一大堆元素形成一个VNode Tree，叫做虚拟DOM。

template->虚拟DOM->真实DOM

虚拟DOM存在的好处就是跨平台。

虚拟DOM->真实DOM,渲染在浏览器上

虚拟DOM->移动端原生控件

虚拟DOM->桌面控件

虚拟DOM->VR设备

# options

### computed

插值语法可以显示data的数据，可以对数据写一些简单的运算。当我们对数据要求有一定的计算时，在模板中放入太多逻辑会使模板过重，难以维护。若使用methods，则data的时候就变成了方法的调用。这个时候使用computed计算属性。

**对于任何包含响应式数据的复杂逻辑，应该使用计算属性computed**

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>v-for</title>
</head>

<body>
  <div id='info'>
    <div>{{fullname}}</div>
    <div>{{level}}</div>
    <div>{{reverseMessage}}</div>
  </div>

  <script src="./libraries/vue.js"></script>
  <script>
    const app = Vue.createApp({
      data() {
        return {
          firstname: 'kobe',
          lastname: 'bryant',
          score: 100,
          message: 'hello world'
        }
      },
      methods: {
        change() {
          this.arr.splice(3, 0, 'p', 'o', 'i')
        }
      },
      computed: {
        fullname() {
          return this.firstname + ' ' + this.lastname
        },
        level() {
          return this.score === 100 ? '优秀' : '一般'
        },
        reverseMessage() {
          return this.message.split(' ').reverse().join(' ')
        }
      }
    })
    app.mount('#info')
  </script>
</body>

</html>
```

computed会根据依赖数据进行缓存，当依赖数据发生改变时，computed会重新计算。相比methods，调用依次方法，就会计算一次。

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>computed</title>
</head>

<body>
  <div id='info'>
    <div>{{fullname}}</div>
    <button @click="change">变</button>
  </div>

  <script src="./libraries/vue.js"></script>
  <script>
    const app = Vue.createApp({
      data() {
        return {
          firstname: 'kobe',
          lastname: 'bryant',
        }
      },
      computed: {
        // computed完整写法
        fullname: {
          get: function () {
            return this.firstname + '' + this.lastname
          },
          set: function (value) {
            const name = value.split(" ")
            this.firstname = name[0]
            this.lastname = name[1]
          }
        }
      },
      methods: {
        change() {
          this.fullname = 'a b'
        }
      }
    })
    app.mount('#info')
  </script>
</body>

</html>
```

## watch

监听数据

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>watch</title>
</head>

<body>
  <div id='info'>
    <div>{{obj.firstname}}</div>
    <div>{{obj.lastname}}</div>
    <button @click="change">变</button>
  </div>

  <script src="./libraries/vue.js"></script>
  <script>
    const app = Vue.createApp({
      data() {
        return {
          obj: {
            firstname: 'kobe',
            lastname: 'bryant',
          }
        }
      },
      methods: {
        change() {
          this.obj = {
            firstname: 'abble',
            lastname: 'bryant',
          }
        }
      },
      watch: {
        obj(newObj, oldObj) {
           //输出的是代理对象
          console.log(oldObj, newObj)
        }
      }
    })
    app.mount('#info')
  </script>
</body>

</html>
```

```
Proxy(Object) {firstname: 'kobe', lastname: 'bryant'} Proxy(Object) {firstname: 'abble', lastname: 'bryant'}
```

```js
watch: {
        obj(newObj, oldObj) {
            //Vue.toRaw返回newObj原生数据
          console.log(Vue.toRaw(newObj))
        }
      }
```

```
{firstname: 'abble', lastname: 'bryant'}
```



### 深度监听

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>watch</title>
</head>

<body>
  <div id='info'>
    <div>{{obj.firstname}}</div>
    <div>{{obj.lastname}}</div>
    <button @click="change">变</button>
  </div>

  <script src="./libraries/vue.js"></script>
  <script>
    const app = Vue.createApp({
      data() {
        return {
          obj: {
            firstname: 'kobe',
            lastname: 'bryant',
          }
        }
      },
      methods: {
        change() {
          this.obj.firstname = 'abble'
        }
      },
      watch: {
        obj: {
          handler: function (newObj, oldObj) {
            console.log(oldObj)
            console.log(Vue.toRaw(newObj))
          },
          // 深度监听，对象的属性值改变也算数
          deep: true,
          //第一次渲染的时候就执行一次handler
          immediate: true
        },
        'obj.firstname'(newN, oldN) {
          console.log("firstname改变", oldN, newN)
        }
      }
    })
    app.mount('#info')
  </script>
</body>

</html>
```

通过$watch的api来调用监听，比如在create（）生命周期中调用。第一个参数：监听对象，第二个：回调函数，第三个：其他参数。

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>watch</title>
</head>

<body>
  <div id='info'>
    <div>{{message}}</div>
    <button @click="change">变</button>
  </div>

  <script src="./libraries/vue.js"></script>
  <script>
    const app = Vue.createApp({
      data() {
        return {
          message: 'll'
        }
      },
      methods: {
        change() {
          this.message = 'abble'
        }
      },
      created() {
        this.$watch('message', (newM, oldM) => {
          console.log('create中', oldM, newM)
        }, { deep: true, immediate: true })
      }
    })
    app.mount('#info')
  </script>
</body>

</html>
```

# 表单案例

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>表单案例</title>
  <style>
    table {
      border-collapse: collapse;
      text-align: center;
    }

    th,
    td {
      border: 1px solid black;
    }

    th {
      background-color: #8a8989;
    }

    .active {
      background-color: #b5b5b5;
    }
  </style>
</head>

<body>
  <div id='info'>
    <template v-if="books.length">
      <table>
        <thead>
          <tr>
            <th>序号</th>
            <th>书名</th>
            <th>单价</th>
            <th>购买数量</th>
            <th>删除</th>
          </tr>
        </thead>
        <tbody>
          <tr :class="{active : index === currentIndex}" @click="clickTr(index)" v-for="(item,index) in books"
            :key="item.id">
            <td>{{item.id}}</td>
            <td>{{item.name}}</td>
            <td>{{formatPrice(item.price)}}</td>
            <td>
              <button :disabled="item.count <= 1" @click="decrement(item)">-</button>
              {{item.count}}
              <button @click="increment(index)">+</button>
            </td>
            <td><button @click="removeBook(index)">删除</button></td>
          </tr>
        </tbody>
      </table>
      <h2>总计：{{formatPrice(totalPrice)}}</h2>
    </template>

    <template v-else>
      <h2>购物车为空</h2>
    </template>
  </div>

  <script src=" ./libraries/vue.js"></script>
  <script>
    const app = Vue.createApp({
      data() {
        return {
          books: [
            {
              id: 1,
              name: '《算法导论》',
              price: 20,
              count: 1
            },
            {
              id: 2,
              name: '《7天会算法》',
              price: 30,
              count: 1
            },
            {
              id: 3,
              name: '《JS》',
              price: 40,
              count: 1
            }
          ],
          currentIndex: 0
        }
      },
      computed: {
        totalPrice() {
          return this.books.reduce((pre, item) => {
            return pre + item.price * item.count
          }, 0)
        }
      },
      methods: {
        formatPrice(price) {
          return '￥' + price
        },
        decrement(item) {
          item.count--
        },
        increment(index) {
          this.books[index].count++
        },
        removeBook(index) {
          this.books.splice(index, 1)
        },
        clickTr(index) {
          this.currentIndex = index
        }
      }
    })
    app.mount('#info')
  </script>
</body>

</html>
```

# v-moel

表单双向绑定相当于：

```html
<input :value="username" @input="this.username=$event.target.value">
```



```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>v-model</title>

</head>

<body>
  <div id='info'>
    <label for="username">用户名：</label>
    <input type="text" id="username" v-model="username">
    <label for="password">密码：</label>
    <input type="password" id="password" v-model="password">
    <button @click="submit">提交</button>
  </div>

  <script src=" ./libraries/vue.js"></script>
  <script>
    const app = Vue.createApp({
      data() {
        return {
          username: 'colon',
          password: '11111'
        }

      },
      methods: {
        submit() {
          console.log(this.username)
          console.log(this.password)
        }
      }
    })
    app.mount('#info')
  </script>
</body>

</html>
```



## textarea

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>v-model</title>

</head>

<body>
  <div id='info'>
    <textarea v-model="content" cols="30" rows="10" id=""></textarea>
    <p>内容：{{content}}</p>
  </div>

  <script src=" ./libraries/vue.js"></script>
  <script>
    const app = Vue.createApp({
      data() {
        return {
          content: '',
        }

      },
      methods: {
      }
    })
    app.mount('#info')
  </script>
</body>

</html>
```



## checkbox

单选框双向绑定的是一个布尔值

复选框绑定的是一个数组，数组里面的值是元素的value值

所以复选框必须写value

```HTML
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>v-model</title>

</head>

<body>
  <div id='info'>
    <label for="lunch">
      吃饭吗
      <input type="checkbox" id="lunch" v-model="isEat" />
    </label>
    <p>{{isEat}}</p>
    <hr>
    <label for="meat">肉</label>
    <input type="checkbox" id="meat" v-model="lunchArr" value="meat" />
    <label for="veg">蔬菜</label>
    <input type="checkbox" id="veg" v-model="lunchArr" value="veg" />
    <P>{{lunchArr}}</P>
  </div>

  <script src=" ./libraries/vue.js"></script>
  <script>
    const app = Vue.createApp({
      data() {
        return {
          isEat: '',
          lunchArr: []
        }

      },
      methods: {
      }
    })
    app.mount('#info')
  </script>
</body>

</html>
```

## select下拉选项

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>下拉菜单</title>

</head>

<body>
  <div id='info'>
    <select v-model="fruit">
      <option value="apple">苹果</option>
      <option value="orange">橘子</option>
    </select>
    <p>{{fruit}}</p>
    <hr>
    <select multiple size="2" v-model="fruits">
      <option value="apple">苹果</option>
      <option value="orange">橘子</option>
    </select>
    <p>{{fruits}}</p>
  </div>

  <script src=" ./libraries/vue.js"></script>
  <script>
    const app = Vue.createApp({
      data() {
        return {
          fruit: '',
          fruits: []
        }

      },
      methods: {
      }
    })
    app.mount('#info')
  </script>
</body>

</html>
```

## 值绑定

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>下拉菜单</title>

</head>

<body>
  <div id='info'>
    <select v-model="fruit">
      <option v-for="item in allFruits" :key="item.value" :value="item.value">{{item.text}}</option>
    </select>
    <p>{{fruit}}</p>
    <hr>
    <select multiple size="2" v-model="fruits">
      <option v-for="item in allFruits" :key="item.value" :value="item.value">{{item.text}}</option>
    </select>
    <p>{{fruits}}</p>
  </div>

  <script src=" ./libraries/vue.js"></script>
  <script>
    const app = Vue.createApp({
      data() {
        return {
          allFruits: [{ value: 'apple', text: "苹果" }, { value: 'orange', text: "橘子" }],
          fruit: '',
          fruits: []
        }

      },
      methods: {
      }
    })
    app.mount('#info')
  </script>
</body>

</html>
```

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>v-model</title>

</head>

<body>
  <div id='info'>
    <label for="lunch">
      吃饭吗
      <input type="checkbox" id="lunch" v-model="isEat" />
    </label>
    <p>{{isEat}}</p>
    <hr>

    <label v-for="item in cat" :key="item.value" :for="item.value">
      {{item.text}}
      <input type="checkbox" :id="item.value" v-model="lunchArr" :value="item.value" />
    </label>

    <P>{{lunchArr}}</P>
  </div>

  <script src=" ./libraries/vue.js"></script>
  <script>
    const app = Vue.createApp({
      data() {
        return {
          cat: [{ value: 'meat', text: "肉" }, { value: 'veg', text: "蔬菜" }],
          isEat: '',
          lunchArr: []
        }

      },
      methods: {
      }
    })
    app.mount('#info')
  </script>
</body>

</html>
```

## 修饰符

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>v-model</title>

</head>

<body>
  <div id='info'>
    <!--默认是input事件，lazy使编程change事件，按回车或者失去光标才触发-->
    <input type="text" v-model.lazy="content">
    <P>{{content}}</P>
    <!--number转换为数字类型，为空仍是string类型-->
    <input type="text" v-model.number="num1">
    <P>{{typeof num1}}</P>
    <!--若input是number，则输入不了符号-->
    <input type="number" v-model="num2">
    <hr>
    <!--trim去两边的空格，-->
    <input type="text" v-model.trim="text1">
  </div>

  <script src=" ./libraries/vue.js"></script>
  <script>
    const app = Vue.createApp({
      data() {
        return {
          content: '',
          num1: 0,
          num2: 0,
          text1: ''
        }

      },
      methods: {
      }
    })
    app.mount('#info')
  </script>
</body>

</html>
```

# 组件化

createApp函数传入一个对象 App，这个对象其实本质上就是一个组件，是应用程序的跟组件；

组件化提供一种抽象，使开发出一个个独立可复用的小组件，来构造应用；

任何应用都会被抽象成一颗组件树

## 全局组件

```html
<!--全局组件，全局组件可以在任意组件中使用-->
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>v-model</title>

</head>

<body>
  <div id='info'>
    <list1></list1>
    <list2></list2>
    <list2></list2>
  </div>
  <!--注册模板，不显示 -->
  <template id="list2">
    <ul>
      <li>哇哈哈</li>
      <li>哈娃娃</li>
    </ul>
  </template>
  <script src=" ./libraries/vue.js"></script>
  <script>
    const app = Vue.createApp({})
    const list1 = {
      template: `
          <ul>
            <li>哆啦A梦</li>
            <li>穿越火线</li>
          </ul>
      `
    }
    const list2 = {
      template: '#list2'
    }
    // 组件注册
    app.component('list2', list2)
    app.component('list1', list1)
    app.mount('#info')

  </script>
</body>

</html>
```

组件也可以有自己的逻辑，data、methods、computed等等

组件命名：短横线分隔与大驼峰命名（首字母大写），但是html标签不分大小写，所以用短横线表示最好

## 局部组件

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>局部组件</title>

</head>

<body>
  <div id='info'>
    <component-a></component-a>
    <component-b></component-b>
  </div>
  <!--注册模板，不显示 -->
  <template id="componentA">
    <hr>
    <p>componentA</p>
    <hr>
  </template>
  <template id="componentB">
    <hr>
    <p>componentB</p>
    <component-a></component-a>
    <hr>
  </template>
  <script src=" ./libraries/vue.js"></script>
  <script>
    const componentA = {
      template: '#componentA'
    }
    const componentB = {
      template: '#componentB',
      // 局部组件
      components: {
        componentA
      }
    }
    const app = Vue.createApp({
      // 局部组件
      components: {
        componentA,
        componentB
      }

    })
    app.mount('#info')

  </script>
</body>

</html>
```

因为代码写一个文件不易阅读和编写，于是采用单文件的方式开发

单文件组件single-file component文件，后缀.vue，可以使用Vue CLI（Command-line interface）来创建项目，项目会默认设置配置。第二种方式是使用webpack或rollup或vite这类打包工具。

脚手架是建筑工程的一个概念，在软件工程中也会将一些帮助我们搭建项目的工具称为脚手架。

Vue CLI已经内置webpack相关配置，不需从零配置
# vue3学习笔记

## 插槽的使用

### 匿名插槽基本使用

``` vue
//父组件
<template>
  <div>
    <Slot>
      <h3>匿名插槽</h3>
    </Slot>
  </div>
</template>
//子组件
<template>
  <div>
    <slot>这里是默认内容</slot> 
  </div>
</template>

```

### 具名插槽的使用

```vue
//父组件
<template>
  <div id="app">
    <Slot :name="name1">
      <!-- v-slot -> # -->
      <template #1>
        <button>左边按钮</button>
      </template>
      <template v-slot:2>
        <button>中间按钮</button>
      </template>
      <template v-slot:3>
        <button>右边按钮</button>
      </template>

      <template v-slot:[name1]>
        <strong>动态插槽名</strong>
      </template>
    </Slot>
  </div>
</template>

//子组件
<template>
  <div class="slot">
    <slot name="1">默认内容</slot>
    <slot name="2">默认内容</slot>
    <slot name="3">默认内容</slot>

    <slot :name="name">默人内容</slot>
  </div>
</template>
			
```

### 作用域插槽

```vue
//父组件使用子组件数据
<template>
  <div id="app">
    <Slot :data="data">
      <template v-slot="slotProps">
        <button>
          {{ slotProps.item }}
        </button>
      </template>
    </Slot>

	//独占默认插槽
    <Slot :data="data" v-slot="slotProps">
      <h1>{{ slotProps.item }}</h1>
    </Slot>

  </div>
</template>

//子组件
<template>
  <div class="slot">
    <template v-for="item in data">
      <slot class="author" :item="item">
        {{ item }}
      </slot>
    </template>
  </div>
</template>
```

## hook

```js
//对具有相同逻辑的代码进行抽取,提高代码的可复用性
import {ref} from 'vue'
export default function () {
  const num = ref(0)
  const changeNum = () => num.value++
  const sum = (num1, num2) => num1 + num2

  return {
    changeNum,
    sum,
    num,
  }
}

import useCounter from './hooks/useCounter';
export default {
    setup(props, context) {
        const { changeNum, sum, num } = useCounter()


        return {
            changeNum, sum, num
        }
    }
}
```

## 自定义指令

### 基本使用

```js
export default {
    setup() {
        return {}
    },
    directives: {
        focus: {
            mounted(el, bindings, vnode, prevnode) {
                el.focus()
            }
        }
    }
}
```



### 进阶使用

```js
import dayjs from 'dayjs'
export default function (app) {
  app.directive('time', {
    mounted(el, bindings, vnode, prevode) {
      const time = el.textContent
      let formate = dayjs(time * 1000).format(bindings.value)
      el.textContent = formate
    },
  })
}

```

## vue-router

### 基本使用

```js
import {createRouter, createWebHashHistory} from 'vue-router'

const routes = [
  {path: '/', redirect: '/home'},
  {
    path: '/home',
    component: () => import(/*webpackChunkName:'home'*/ '../15_router路由/components/Home.vue'),  /**/为webpack语法对打包以后的js包命名
    name:'home',
    meta:{}
    children: [
      {path: '', redirect: '/home/message'},
      {path: 'message', component: () => import('../15_router路由/components/home-message')},
      {path: 'about', component: () => import('../15_router路由/components/home-about')},
    ],
  },
   
  {
    path: '/about',
    component: () => import(/*webpackChunkName:"about"*/ '../15_router路由/components/About.vue'), //import函数导入的组件会自动打包  返回一个promise
    name:'about',
    meta:{}
  },

  {
    path: '/user/:name/id/:id',  //动态路由匹配    
    component: () => import(/*webpackName:"user"*/ '../15_router路由/components/User.vue'),
  },
      
  //匹配不存在的页面 not found
  {path: '/:pathMatch(.*)', component: () => import('../15_router路由/components/NotFound.vue')},    
]

const router = createRouter({
  routes,
  history: createWebHashHistory(),
})

export default router


//App.vue组件配置
 <router-link to="/home">首页</router-link>
 <router-link to="/about">about</router-link>
 <router-link :to="('/user/' + name + '/id/' + id)">user</router-link>
 <!-- 出口必配 -->
 <router-view />

```




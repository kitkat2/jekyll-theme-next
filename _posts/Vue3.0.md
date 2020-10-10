# Vue3.0

[TOC]



### 特点

1. 性能提升

2. Composition API
   1. ref和reactive
   2. computed和watch
   3. 新的生命周期函数
   4. 自定义函数 - Hooks函数
   5. Teleport - 瞬移组件的位置
   6. Suspense - 异步加载组件
   7. 全局API的修改和优化

3. 更好的TypeScript支持

   

### 基础

#### ref和computed

```vue
<template>
  <div id="app">
      <img alt="Vue logo" src="./assets/logo.png">
      <h1>{{count}}</h1>
      <h1>{{double}}</h1>
      <button @click="increase">👍+1</button>
  </div>
</template>

<script lang="ts">
import { defineComponent , ref, computed, reactive} from 'vue';
export default defineComponent({
  name: 'App',
  setup() { // 利用setup进行配置
    const count = ref(0) // 使用ref对基础类型数据赋值
    const double = computed(() =>{
      return count.value * 2
    })
    const increase = () => {
      count.value++
    }
    return { // 所有的变量必须return后才能使用
      count,
      increase,
      double
    }
  }
});
</script>
```

#### reactive

```typescript
setup() {
    const data: DataProps = reactive({
      count : 0,
      increase: () => { data.count ++},
      double: computed(() => data.count * 2)
    })
    const refData = toRefs(data) // 用toRefs 解决将Reactive对象中属性展开拿出缺失响应性的问题
    return {
      ...refData
    }
  }
```

#### 生命周期

初始化事件/生命周期

beforeCreate

初始化注入/检验

created

创建vm.$el，并替换el 实例挂载到dom

beforeMounte  <--  data被修改

虚拟dom重新渲染应用更新

mounted

beforeUpdate

updated 

beforeDestroy  --> beforeUnmounted  接触绑定 销毁子组件以及事件监听

destroyed --> unmounted 销毁完毕



vue3中的生命周期钩子函数



钩子函数都在setUp()函数中使用

debug钩子函数：

onRenderTracked

onRenderTriggered 数据更新时会触发， 记录rerender时发生变化的值

#### watch的使用

setUp函数只调用一次，对Dom的更新操作不会触发该函数，使用watch进行监听

watch：

接收参数： 响应式对象（即ref对象），变化时执行的函数（回调函数，参数：newVal，oldVal）4

监听多个值：接收的第一参数可以是数组，将多个值作为数组传入即可

在监听reactive对象时监听到的值是一个proxy对象

监听reactive对象内的值，通过getter函数取出，否则无法监听

```typescript
watch([greetings, ()=>data.count], (newVal, oldVal) =>{
    console.log(newVal, oldVal)
})
```

#### compositionAPI进行模块化和逻辑重用

##### 鼠标追踪事件

1. 创建x、y 两个响应式对象用于记录鼠标当前位置

2. 定义鼠标事件
3. 在生命周期的钩子函数（onMounted和onUnmounted）上添加和删除监听事件，
4. 返回x、y，在模板上使用xy
5. 逻辑抽离成外部文件 以use开头 代表抽离出的功能 （自定义hook）

reactive对象改写该hook？

#### useURLLoader

泛型改造!!!

####  对typescript的支持

defineComponent: 服务ts

#### teleport

emits 组件通信 可以添加校检

```typescript
 emits: {
    'close-modal': (payload: any) => {
      return payload.type === 'close'
    }
  },
 // 通过context emit事件
 setup(props, context) {
    context.emit('close-modal', {
      type: "close"
    })
  }
```

#### suspense 异步请求状态

suspense 异步内容渲染，要返回一个promise对象

```html
<Suspense>
    <template #default>
        <async-show/>
    </template> // 成功显示async-show组件
    <template #fallback>
        <h1>Loading...</h1>
    </template>
</Suspense>
```

```typescript
export default defineComponent ({
  setup() {
    return new Promise((resolve) => {
      setTimeout(()=> {
        return resolve({
          result: 42
        })
      }, 3000)
    })
  }
})
// asyncshow组件返回的是一个异步对象
```

常用实现异步的写法： async/await

#### 全局配置的修改

```typescript
newVue({render: h=> h(App)}).$mount('#app') - > createApp({}).mount('#app')
```





Vue2 入口文件

<img src="C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20201010150944958.png" alt="image-20201010150944958" style="zoom: 70%;" />

Vue3 入口文件

<img src="C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20201010150922951.png" alt="image-20201010150922951" style="zoom:67%;" />

配置修改： Vue.config  -> app.config

1. productionTip被删除
2. ignoredElements --> isCustomElement
3. keyCodes 被删除

注册类修改 

1. Vue.component --> app.component
2. Vue.directive --> app.directive

行为扩展类

1. Vue.mixin --> app.mixin
2. Vue.use --> app.use

其他通过单独import --> treeshaking

	1. Vue.nextTick()  --> import {nextTick} from 'vue'
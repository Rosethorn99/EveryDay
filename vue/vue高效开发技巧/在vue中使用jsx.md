## 安装babel支持Vue的jsx插件

```
pnpm i @vue/babel-plugin-jsx -D
```

## 在babel.config.js配置文件中配置插件

```js
module.exports = {
	prsets: [
		'@vue/cli-plugin-babel/preset'
	],
	plugins: [
		'@vue/babel-plugin-jsx'
	]
}
```

### vue2写法

```vue
<script>
  import HelloWorld from './HelloWorld.vue';

  export default {
    data() {
      return {
        counter: 0
      }
    },
    render() {
      const increment = () => this.counter++;
      const decrement = () => this.counter--;
      return (
        <div>
          <h2>当前计数: {this.counter}</h2>
          <button onClick={increment}>+1</button>
          <button onClick={decrement}>-1</button>
          <HelloWorld>
          </HelloWorld>
        </div>
      )
    }
  }
</script>

<style lang="scss" scoped>

</style>

// HelloWorld.vue
<script>
  export default {
    render() {
      return (
        <div>
          <h2>HelloWorld</h2>
          {this.$slots.default ? this.$slots.default(): <span>哈哈哈</span>}
        </div>
      )
    }
  }
</script>
<style scoped>
</style>
```

## vue3写法

```vue
// 非setup语法
<script lang="jsx">
  import { ref } from 'vue'
  export default {
    setup() {
      const counter = ref(0)
      const increment = () => {
        counter.value++
      }
      const decrement = () => {
        counter.value--
      }
      return () => {
        <div class="app">
          <h2>当前计数: { counter.value }</h2>
          <button onClick={ increment }>+1</button>
          <button onClick={ decrement }>-1</button>
        </div>
      }
    }
  }
</script>

<style scoped>
</style>

// 顶层setup写法
<template>
  <jsx />
</template>

<script lang="jsx" setup>
  import { ref } from 'vue'
  const counter = ref(0)
  const increment = () => {
    counter.value++
  }
  const decrement = () => {
    counter.value--
  }
  const jsx = () => (
    <div class="app">
      <h2>当前计数: { counter.value }</h2>
      <button onClick={ increment }>+1</button>
      <button onClick={ decrement }>-1</button>
    </div>
  )
</script>

<style scoped>
</style>
```

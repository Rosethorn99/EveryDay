## 1. provide /inject

`provide/inject` 是 Vue.js 中用于跨组件传递数据的一种高级技术，它可以将数据注入到一个组件中，然后让它的所有子孙组件都可以访问到这个数据。通常情况下，我们在父组件中使用 `provide` 来提供数据，然后在子孙组件中使用 `inject` 来注入这个数据。

使用 `provide/inject` 的好处是可以让我们在父组件和子孙组件之间传递数据，而无需手动进行繁琐的 `props` 传递。它可以让代码更加简洁和易于维护。但需要注意的是，`provide/inject` 的数据是非响应式的，这是因为 provide /inject是一种更加底层的 API，它是基于依赖注入的方式来传递数据，而不是通过响应式系统来实现数据的更新和同步。

具体来说， provide 方法提供的数据会被注入到子组件中的inject属性中，但是这些数据不会自动触发子组件的重新渲染，如果 provide 提供的数据发生了变化，子组件不会自动感知到这些变化并更新。

如果需要在子组件中使用 provide /inject提供的数据，并且希望这些数据能够响应式地更新，可以考虑使用Vue的响应式数据来代替 provide /inject。例如，可以将数据定义在父组件中，并通过props将其传递给子组件，子组件再通过$ emit 来向父组件发送数据更新的事件，从而实现响应式的数据更新。

下面是一个简单的例子，展示了如何在父组件中提供数据，并在子孙组件中注入这个数据：

```html
<!-- 父组件 -->
<template>
  <div>
    <ChildComponent />
  </div>
</template>

<script>
import ChildComponent from './ChildComponent.vue';

export default {
  provide: {
    message: 'Hello from ParentComponent',
  },
  components: {
    ChildComponent,
  },
};
</script>

//上面provide还可以写成函数形式
export default {
    provide(){
        return {
              message: this.message
        }
    }
}
```

```html
<!-- 子组件 -->
<template>
  <div>
    <GrandchildComponent />
  </div>
</template>

<script>
import GrandchildComponent from './GrandchildComponent.vue';

export default {
  inject: ['message'],
  components: {
    GrandchildComponent,
  },
};
</script>
```

```html
<!-- 孙子组件 -->
<template>
  <div>
    <p>{{ message }}</p>
  </div>
</template>

<script>
export default {
  inject: ['message'],
};
</script>
```

在上面的例子中，父组件中提供了一个名为 `message` 的数据，子孙组件中都可以使用 `inject` 来注入这个数据，并在模板中使用它。注意，子孙组件中的 `inject` 选项中使用了一个数组，数组中包含了需要注入的属性名。在这个例子中，我们只注入了一个 `message` 属性，所以数组中只有一个元素。

## 2. 自定义v-model

要使自定义的Vue组件支持v-model，需要实现一个名为`value`的prop和一个名为`input`的事件。在组件内部，将`value` prop 绑定到组件的内部状态，然后在对内部状态进行修改时触发`input`事件。

下面是一个简单的例子，展示如何创建一个自定义的输入框组件并支持v-model：

```html
<template>
  <input :value="value" @input="$emit('input', $event.target.value)" />
</template>

<script>
export default {
  name: 'MyInput',
  props: {
    value: String
  }
};
</script>
```

在上面的组件中，我们定义了一个`value` prop，这是与v-model绑定的数据。我们还将内置的`input`事件转发为一个自定义的`input`事件，并在事件处理程序中更新内部状态。现在，我们可以在父组件中使用v-model来绑定这个自定义组件的值，就像使用普通的输入框一样：

```html
<template>
  <div>
    <my-input v-model="message" />
    <p>{{ message }}</p>
  </div>
</template>

<script>
import MyInput from './MyInput.vue';

export default {
  components: {
    MyInput
  },
  data() {
    return {
      message: ''
    };
  }
};
</script>
```

在上面的代码中，我们通过使用`v-model`指令来双向绑定`message`数据和`MyInput`组件的值。当用户在输入框中输入文本时，`MyInput`组件会触发`input`事件，并将其更新的值发送给父组件，从而实现了双向绑定的效果。

## 3. 事件总线（EventBus）

Vue事件总线是一个事件处理机制，它可以让组件之间进行通信，以便在应用程序中共享信息。在Vue.js应用程序中，事件总线通常是一个全局实例，可以用来发送和接收事件。

以下是使用Vue事件总线的步骤：

### 3.1 创建一个全局Vue实例作为事件总线：

```js
import Vue from 'vue';
export const eventBus = new Vue();
```

### 3.2 在需要发送事件的组件中，使用`$emit`方法触发事件并传递数据：

```js
eventBus.$emit('eventName', data);
```

### 3.3 在需要接收事件的组件中，使用`$on`方法监听事件并处理数据：

```js
eventBus.$on('eventName', (data) => {
  // 处理数据
});
```

需要注意的是，事件总线是全局的，所以在不同的组件中，需要保证事件名称的唯一性。

另外，需要在组件销毁前使用`$off`方法取消事件监听：

```js
eventBus.$off('eventName');
```

这样就可以在Vue.js应用程序中使用事件总线来实现组件之间的通信了。

## 4. render 方法

Vue 的 `render` 方法是用来渲染组件的函数，它可以用来替代模板语法，通过代码的方式来生成 DOM 结构。相较于模板语法，`render` 方法具有更好的类型检查和代码提示。

下面详细介绍 Vue 的 `render` 方法的使用方法：

### 4.1 基本语法

`render` 方法的基本语法如下：

```js
render: function (createElement) {
  // 返回一个 VNode
}
```

其中 `createElement` 是一个函数，它用来创建 VNode（虚拟节点），并返回一个 VNode 对象。

### 4.2 创建 VNode

要创建 VNode，可以调用 `createElement` 函数，该函数接受三个参数：

-   标签名或组件名
-   可选的属性对象
-   子节点数组

例如，下面的代码创建了一个包含文本节点的 `div` 元素：

```js
render: function (createElement) {
  return createElement('div', 'Hello, world!')
}
```

如果要创建一个带有子节点的元素，可以将子节点作为第三个参数传递给 `createElement` 函数。例如，下面的代码创建了一个包含两个子元素的 `div` 元素：

```js
render: function (createElement) {
  return createElement('div', [
    createElement('h1', 'Hello'),
    createElement('p', 'World')
  ])
}
```

如果要给元素添加属性，可以将属性对象作为第二个参数传递给 `createElement` 函数。例如，下面的代码创建了一个带有样式和事件处理程序的 `button` 元素：

```js
render: function (createElement) {
  return createElement('button', {
    style: { backgroundColor: 'red' },
    on: {
      click: this.handleClick
    }
  }, 'Click me')
},
methods: {
  handleClick: function () {
    console.log('Button clicked')
  }
}
```

### 4.3 动态数据

`render` 方法可以根据组件的状态动态生成内容。要在 `render` 方法中使用组件的数据，可以使用 `this` 关键字来访问组件实例的属性。例如，下面的代码根据组件的状态动态生成了一个带有计数器的 `div` 元素：

```js
render: function (createElement) {
  return createElement('div', [
    createElement('p', 'Count: ' + this.count),
    createElement('button', {
      on: {
        click: this.increment
      }
    }, 'Increment')
  ])
},
data: function () {
  return {
    count: 0
  }
},
methods: {
  increment: function () {
    this.count++
  }
}
```

### 4.4 JSX

在使用 Vue 的 `render` 方法时，也可以使用 JSX（JavaScript XML）语法，这样可以更方便地编写模板。要使用 JSX，需要在组件中导入 `Vue` 和 `createElement` 函数，并在 `render` 方法中使用 JSX 语法。例如，下面的代码使用了 JSX 语法来创建一个计数器组件：

```js
import Vue from 'vue'

export default {
    render() {
        return (
            <div>
                <p>Count:{this.count}</p>
                <button onClick={this.increment}>Increment</button>
            </div>
        )
    },
    data() {
        return { count: 0 }
    },
    methods: {
        increment() {
            this.count++
        }
    }
}
```

注意，在使用 JSX 时，需要使用 `{}` 包裹 JavaScript 表达式。

### 4.5 生成函数式组件

除了生成普通的组件，`render` 方法还可以生成函数式组件。函数式组件没有状态，只接收 `props` 作为输入，并返回一个 VNode。因为函数式组件没有状态，所以它们的性能比普通组件更高。

要生成函数式组件，可以在组件定义中将 `functional` 属性设置为 `true`。例如，下面的代码定义了一个函数式组件，用于显示列表项：

```javascript
export default {
  functional: true,
  props: ['item'],
  render: function (createElement, context) {
    return createElement('li', context.props.item);
  }
}
```

注意，在函数式组件中，`props` 作为第二个参数传递给 `render` 方法。
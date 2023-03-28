`Vue`中组件间通信包括父子组件、兄弟组件、跨级组件、非嵌套组件之间通信。

## props $emit

`props $emit`适用于父子组件的通信，这种组件通信的方式是我们运用的非常多的一种，`props`以单向数据流的形式可以很好的完成父子组件的通信，所谓单向数据流，就是数据只能通过`props`由父组件流向子组件，而子组件并不能通过修改`props`传过来的数据修改父组件的相应状态，所有的`props`都使得其父子`props`之间形成了一个单向下行绑定，父级`props`的更新会向下流动到子组件中，但是反过来则不行，这样会防止从子组件意外改变父级组件的状态，导致难以理解数据的流向而提高了项目维护难度。实际上如果传入一个基本数据类型给子组件，在子组件中修改这个值的话`Vue`中会出现警告，如果对于子组件传入一个引用类型的对象的话，在子组件中修改是不会出现任何提示的，但这两种情况都属于改变了父子组件的单向数据流，是不符合可维护的设计方式的。  
正因为这个特性，而我们会有需要更改父组件值的需求，就有了对应的`$emit`，当我们在组件上定义了自定义事件，事件就可以由`vm.$emit`触发，回调函数会接收所有传入事件触发函数的额外参数，`$emit`实际上就是是用来触发当前实例上的事件，对此我们可以在父组件自定义一个处理接受变化状态的逻辑，然后在子组件中如若相关的状态改变时，就触发父组件的逻辑处理事件。

### 父组件向子组件传值

父组件向子组件传值通过`props`传递值即可。

```vue
<!-- 子组件 -->
<template>
    <div>

        <div>我是子组件，接收：{{ msg }}</div>

    </div>
</template>

<script>
    export default {
        name: "child",
        components: {},
        props: ["msg"],
        data: () => ({
            
        }),
        beforeCreate: function() {},
        created: function() {},
        filters: {},
        computed: {},
        methods: {}
    }
</script>

<style scoped>
 
</style>
```

```vue
<!-- 父组件 -->
<template>
    <div>

        <child :msg="msg"></child>

    </div>
</template>
<script>
    import child from "./child";
    export default {
        components: { child },
        data: () => ({
            msg: "父组件 Msg"
        }),
        beforeCreate: function() {},
        created: function() {},
        filters: {},
        computed: {},
        methods: {}
    }
</script>

<style scoped>
    
</style>
```

### 子组件向父组件传值

子组件向父组件传值需要通过事件的触发，将更改值的行为传递到父组件去执行。

```vue
<!-- 子组件 -->
<template>
    <div>

        <div>我是子组件，接收：{{ msg }}</div>
        <button @click="$emit('changeMsg', '子组件传值 Msg')">触发事件并传递值到父组件</button>

    </div>
</template>

<script>
    export default {
        name: "child",
        components: {},
        props: ["msg"],
        data: () => ({
            
        }),
        beforeCreate: function() {},
        created: function() {},
        filters: {},
        computed: {},
        methods: {}
    }
</script>

<style scoped>
 
</style>
```

```vue
<!-- 父组件 -->
<template>
    <div>

        <child 
            :msg="msg"
            @changeMsg="changeMsg"
        ></child>

    </div>
</template>

<script>
    import child from "./child";
    export default {
        components: { child },
        data: () => ({
            msg: "父组件 Msg"
        }),
        beforeCreate: function() {},
        created: function() {},
        filters: {},
        computed: {},
        methods: {
            changeMsg: function(msg){
                this.msg = msg;
            }
        }
    }
</script>

<style scoped>
    
</style>
```

### v-model

`v-model`通常称为数据双向绑定，也可以称得上是一种父子组件间传值的方式，是当前组件与`input`等组件进行父子传值，其本质上就是一种语法糖，通过`props`以及`input`(默认情况下)的事件的`event`中携带的值完成，我们可以自行实现一个`v-model`。
<template>
    <div>

        <div>{{msg}}</div>
        <input :value="msg" @input="msg = $event.target.value">

    </div>
</template>

```vue
<script>
    export default {
        data: () => ({
            msg: "Msg"
        }),
        beforeCreate: function() {},
        created: function() {},
        filters: {},
        computed: {},
        methods: {}
    }
</script>

<style scoped>
    
</style>
```

### sync修饰符

`sync`修饰符也可以称为一个语法糖，在`Vue 2.3`之后新的`.sync`修饰符所实现的已经不再像`Vue 1.0`那样是真正的双向绑定，而是和`v-model`类似，是一种语法糖的形式，也可以称为一种缩写的形式，在下面父组件两种写法是完全等同的。

```vue
<!-- 子组件 -->
<template>
    <div>

        <div>我是子组件，接收：{{ msg }}</div>
        <button @click="$emit('update:msg', '子组件传值 Msg')">触发事件并传递值到父组件</button>

    </div>
</template>

<script>
    export default {
        name: "child",
        components: {},
        props: ["msg"],
        data: () => ({
            
        }),
        beforeCreate: function() {},
        created: function() {},
        filters: {},
        computed: {},
        methods: {}
    }
</script>

<style scoped>
 
</style>

```

```vue
<!-- 父组件 -->
<template>
    <div>

        <child 
            :msg="msg1"
            @update:msg="value => msg1 = value"
        ></child>
        
        <child
            :msg.sync="msg2"
        ></child>
        

    </div>
</template>

<script>
    import child from "./child";
    export default {
        components: { child },
        data: () => ({
            msg1: "父组件 Msg1",
            msg2: "父组件 Msg2",
        }),
        beforeCreate: function() {},
        created: function() {},
        filters: {},
        computed: {},
        methods: {
            changeMsg: function(msg){
                this.msg = msg;
            }
        }
    }
</script>

<style scoped>
    
</style>
```
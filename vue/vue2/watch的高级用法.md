**watch的高级使用**watch 在监听器属性发生变化时触发，有时我们希望 watch 在组件创建后立即执行。可能想到的方式是在创建生命周期中调用它一次，但这不是一种优雅的编写方式，所以也许我们可以使用这样的东西。

```js
export default {  
	data() {  
		return {  
			name: Joe  
		}  
	},  
	watch: {  
		name: {  
			handler: sayName ,  
			immediate: true  
		}  
	},  
	methods: {  
		sayName() {  
			console.log(this.name)  
		}  
	}  
}
```

**Deep Listening**监听一个对象时，当对象内部的属性发生变化时，watch是不会被触发的，所以我们可以为它设置深度监听。

```js
export default {  
data: {  
	studen: {  
		name: Joe ,  
		skill: {  
			run: {  
				speed: fast  
			}  
		}  
	}  
},  
	watch: {  
		studen: {  
			handler: sayName ,  
			deep: true  
		}  
	},  
	methods: {  
		sayName() {  
			console.log(this.studen)  
		}  
	}  
}
```

**触发监听器执行多个方法**使用数组，您可以设置多个形式，包括字符串、函数、对象。

```js
export default {  
    data: {  
        name:  Joe  
    },  
    watch: {  
        name: [  
             sayName1 ,  
            function(newVal, oldVal) {  
                this.sayName2()  
            },  
            {  
                handler:  sayName3 ,  
                immaediate: true  
            }  
        ]  
    },  
    methods: {  
        sayName1() {  
            console.log( sayName1==> , this.name)  
        },  
        sayName2() {  
            console.log( sayName2==> , this.name)  
        },  
        sayName3() {  
            console.log( sayName3==> , this.name)  
        }  
    }  
}
```

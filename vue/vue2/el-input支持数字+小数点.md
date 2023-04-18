```vue
// 使用原生的oninput属性
<el-input

	v-model.trim="data"

	oninput="value=value.replace(/[^0-9.]/g,'')"


/>

// 如果仅仅限制只能输入数字, 直接使用修饰符.number即可
```
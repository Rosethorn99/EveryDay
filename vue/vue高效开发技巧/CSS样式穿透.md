**样式范围**开发中修改第三方组件样式很常见，但是由于scoped属性的样式隔离，可能需要去掉scoped或者另起一个样式。这些做法有副作用（组件样式污染，缺乏优雅），在css预处理器中使用样式渗透来生效。我们可以使用 >>> 或者 /deep/ 来解决这个问题：

```css
<style scoped>  
Outer layer >>> .el-checkbox {  
  display: block;  
  font-size: 26px;  
  
  .el-checkbox__label {  
    font-size: 16px;  
  }  
}  
</style>

<style scoped>  
/deep/ .el-checkbox {  
  display: block;  
  font-size: 26px;  
  
  .el-checkbox__label {  
    font-size: 16px;  
  }  
}  
</style>
```
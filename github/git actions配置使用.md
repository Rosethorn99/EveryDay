### 1. 在github下创建对应的仓库

![[Pasted image 20230404135919.png]]

### 2. 创建项目, 提交对应代码到远程仓库

```shell
// 创建项目
pnpm create vite@latest
// git初始化
git init
git add .
git commit -m "init"
git branch -M main
git remote add origin https://github.com/Rosethorn99/vite-vue3-ts-actions.git
git push -u origin main
```

### 3. 在github上配置

![[Pasted image 20230404142046.png]]

### 6. 配置运行目录

![[Pasted image 20230404142316.png]]

![[Pasted image 20230404142345.png]]

![[Pasted image 20230404142435.png]]

### 7. 本地拉取代码

![[Pasted image 20230404143431.png]]

### 8. 确保gitignore没有打包的目录被忽略, 这里需要去掉dist忽略目录

![[Pasted image 20230404143556.png]]

### 9. 配置运行指向目录, 这项打包目录即可

![[Pasted image 20230404143724.png]]

### 10. 打包配置

- 第一次打包, 可以看到根目录没有指向项目目录, 这样会无法找到目录, 需要配置上项目名称作为根目录
![[Pasted image 20230404143909.png]]

- 配置打包输出目录
![[Pasted image 20230404144055.png]]

- 再次打包
![[Pasted image 20230404144149.png]]

### 11. 重新提交代码, 会自动触发actions, 点击查看效果即可

![[Pasted image 20230404144300.png]]

![[Pasted image 20230404144439.png]]

![[Pasted image 20230404144705.png]]
### git配置用户名和邮箱

```bash
git config --global user.name "Rose_thorn99"

git config --global user.email "1137164395@qq.com"
```

### 配置 SSH

- ## **配置** **SSH**      **选择下面代码的其中一个就可以了**

```bash
~/.ssh
~/.ssh ls
如果电脑上有就会显示 ：bash: /c/Users/****/.ssh: Is a directory

如果电脑上没有那就显示：bash: /c/Users/****/.ssh: No such file or directory
```

- ## 创 SSH Key    在 Git Bash 输入（电脑有上面的**bash: /c/Users/****/.ssh: Is a directory 就跳过这步**）

```bash
ssh-keygen -t rsa -C " 邮箱"
```

``` shell
1、Generating public/private rsa key pair.
2、Enter file in which to save the key (/c/Users/*****/.ssh/id_rsa):（输入文件名，用来保存  SSH Key 代码，新手建议直接回车 Enter(回车） 就会默认生成id_rsa和id_rsa.pub两个秘钥文件）
3、自动创建好.ssh这个文件夹，会提示：Created directory ‘/c/Users/*****/.ssh’.
继续按 Enter
出现 passphrase (empty for no passphrase):（设置SSH传输文件时要的密码，新手建议直 接按  Enter回车键)
4、Enter same passphrase again:（再次输入SSH传输文件时要的密码（未设置的直接按 Enter））
5、Your identification has been saved in /c/Users/****/.ssh/id_rsa    (表示在这个文件夹里)
6、Your public key has been saved in /c/Users/*****/.ssh/id_rsa.pub
7、最后成功就会展示 一个代码图片
```

**再次输入**

```bash
~/.ssh
~/.ssh ls
```

两个其中一个, 可以看到代码了

![[Pasted image 20230323090329.png]]

### 添加SSH Key到GitHub 中

1. **进入GitHub 网页 ，右上角**

![[Pasted image 20230323090504.png]]

![[Pasted image 20230323090603.png]]

![[Pasted image 20230323090917.png]]


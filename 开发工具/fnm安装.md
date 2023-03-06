> ## windows平台

### 安装chocolatey

1. 用管理员模式打开 windows Terminal
2. 1.  执行下面命令
```shell
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```
```
3.  输入choco -v，测试是否安装成功
> choco安装软件,都要用管理员模式打开 windows Terminal
```

### 安装fnm

1.  以管理员模式打开Terminal
2. 执行命令:
```shell
choco install fnm
```
### 测试fnm命令

1. 打开Powershell
2. 输入 `fnm -h` 测试命令是否正常
```shell
fnm -h
```
### 环境变量配置

#### Powershell

1. 在下面的目录新建profile.ps1文件
```shell
%USERPROFILE%\Documents\WindowsPowerShell\profile.ps1
```
> %USERPROFILE%: 表示用户目录，直接在文件管理的地址栏输入 %USERPROFILE%，然后回车

> WindowsPowerShell为新建的目录, 如果安装node后命令仍然无法识别，将文件夹名称改为PowerShell

2. 将下面的代码写入到上面的配置文件里面
```shell
fnm env --use-on-cd | Out-String | Invoke-Expression
```

#### cmd

1. 搜索cmd
2. 打开文件所在位置
3. 对 “命令提示符” 右键，点击属性
4. 修改 目标 为下面的值

```shell
%windir%\system32\cmd.exe /k %USERPROFILE%\bashrc.cmd
```

5. 进入用户目录，添加文件 bashrc.cmd
6. 将下面的代码写入到上面的配置文件里面
```shell
@echo off FOR /f "tokens=*" %%z IN ('fnm env --use-on-cd') DO CALL %%z
```

#### git bash

进入用户目录，在git bash的配置文件 .bash_profile 添加下面的代码
```shell
eval $(fnm env | sed 1d) export PATH=$(cygpath $FNM_MULTISHELL_PATH):$PATH if [[ -f .node-version || -f .nvmrc ]]; then fnm use fi
```

#### VSCode内置的cmd

在配置文件settings.json里面添加如下代码：
```shell
"terminal.integrated.defaultProfile.windows": "Default Cmd", "terminal.integrated.profiles.windows": { "Default Cmd":{ "path": "C:\\Windows\\System32\\cmd.exe", "args": ["/k", "%USERPROFILE%\\bashrc.cmd"] } }
```

### fnm使用

#### 安装NodeJS

```shell
fnm install 16 
fnm install 14 
fnm install 12
```

#### 使用NodeJS

```shell
fnm use 16 
fnm use 14 
fnm use 12
```

#### fnm切换node默认版本

```shell
fnm default 14 #默认使用版本14，每次打开terminal的node版本就是14
```

> [github.com/Schniz/fnm](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2FSchniz%2Ffnm "https://github.com/Schniz/fnm")
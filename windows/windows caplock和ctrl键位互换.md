更改 新建一个文本文件，命名为caplock_leftctrl_switch.reg，将如下内容复制进去，双击执行即可

```text
Windows Registry Editor Version 5.00
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Keyboard Layout]
"Scancode Map"=hex:00,00,00,00,00,00,00,00,03,00,00,00,1d,00,3a,00,3a,00,1d,00,00,00,00,00
```

取消 cancel_switch.reg，将如下内容复制进去，双击执行

```text
Windows Registry Editor Version 5.00
[HKEY_LOCAL_MACHINE\SYSTEM\ControlSet\Control\Keyboard Layout]
"Scancode Map"=-
```
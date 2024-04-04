### 新版windows

win10 21H2, win11

可以通过按住**shift+重启** ---》选择**高级选项** ---》**命令提示符**。

```bash
copy C:\windows\system32\utilman.exe C:\windows\system32\utilman.exebak

copy C:\windows\system32\cmd.exe C:\windows\system32\utilman.exe
```

关闭掉终端命令框，**继续，重启**windows。

重启后，点击**辅助功能**按钮

找回电脑登陆账号：

```bash
net localgroup administrators # 查找管理员账号
# 重置登陆密码
net user 管理员账号 * # * 是小写的 a *; A *
# 然后输入新的密码
```



### 旧版windows



hiren's boot cd，并使用rufus刻录入u盘中，使用u盘启动，

开机，allprogram，security，password，NT password Edit确保pass路径为

```
C:\WINDOWS\SYSTEM32\CONFIG\SAM
```


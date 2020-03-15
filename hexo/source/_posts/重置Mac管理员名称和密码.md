---
title: 重置Mac管理员名称和密码
date: 2019-09-23 10:55:53
tags: 软件、工具的使用
---

# 问题

使用mac系统，忘记了管理员名称或密码。

<!-- more -->

# 解决方法

1. 将电脑关机。

2. 按住 `command + s`，并按下开机键。

3. 屏幕进度条跑完，会自动执行指令等待执行完成。

4. 在光标处键入` /sbin/mount -uaw `，回车。等待命令执行完成。

5. 在光标处键入`rm var/db/.applesetupdone `，回车。等待命令执行完成。

6. 在光标处键入`reboot  `，回车。页面跳转。

7. 按照系统给出的步骤，重置管理员名称和密码。

   ![步骤](https://raw.githubusercontent.com/Xiahaiquan/BlogGraphBed/master/ReSetAdministratorNameAndPS/1-1.png)



# 总结

1. 该是重置管理名称和密码，也是相当于新建了一个mac账号，该账号是管理员账号。

2. 经测试，当前不是管理管理员账户时，该操作不会删除任何东西。

   
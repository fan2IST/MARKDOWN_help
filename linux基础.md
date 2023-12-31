# 总览

## 基础部分

### 计算机基础
#### 单位相关
Sleep 休眠计算默认单位<br>
>Unix Linux -S <br>
>Windows    -ms <br>

> char类型占用字节数区分
>+ char-1字节
>+ unix turbo c C51 -2字节
>+ VC++ DevC gcc java -4字节

#### UEFI启动流程

![](https://pic4.zhimg.com/80/v2-73874edaf72a5f01f3c0b3ae5d388cff_720w.webp)

#### linux下各个上级目录作用

 目录名  |	解析 
 ---		|---
/bin	    | 二进制可执行命令。该目录下存放着普通用户的命令
/boot	| 启动Linux的核心文件
/data	| 用户用于存放日志等数据的目录
/dev   |	系统的设备文件，即设备的驱动程序
/etc	   | 系统所有的配置文件都在这个目录中
/home	|用户主目录的基点
/lib	    |  存放着和系统运行相关的库文件
/lost-found  |	这个目录平时是空的，当系统非正常关机而留下的“无家可归”的文件便会储存在这里
/media	|  存放着可移除的设备，比如软盘，光盘
/misc	|  储存着一些特殊的字符的定义
/mnt	    |    挂载目录，是系统管理员临时安装文件的系统安装点
/net	      |   存放着和网络相关的一些文件
/opt	(option : 自由选择) | 主要给源码安装软件时选择的安装目录位置
/proc	|存放着用户与内核的交互信息
/root |	超级用户的目录
/sbin |	系统的管理命令，这里存放的是系统管理员使用的程序
/selinux	 | 主要用来加固操作系统，提高系统的安全性
/srv	 |  系统启动服务时可以访问的数据库目录
/sys	  | 管理设备文件
/tmp	 | 临时文件，重启后自动清空
/var	 | 某些大文件的溢出区，比如各种服务的日志文件
/usr	 | 最大的目录，存放着应用程序和文件

#### linux组成
##### 内核流程 <br>
![](https://picx.zhimg.com/80/v2-7b34506fc68b87847b3450057dc5eeb2_720w.webp?source=2c26e567)
##### 总体组成
linux系统的组成：linux内核、GNU组件、其他的必要应用程序<br>
GNU全称：GNU's not unix<br>
GNU的重要组件（Emacs,gcc,bash,gawk等）加上自己的内核构成了GNU自己的系统--->没用<br>
目前所广泛使用的linux实际上全称应该称呼为GNU/LINUX 其中linux本身以及大部分软件遵循GPL<br>
GPL全称：General Public License 通用公共许可证，GPL的核心是保证任何人有共享和修改自由软件的自由，linux内核遵循GPL协议但不是GNU项目的部分



### linux启动细则
#### 启动项硬盘挂载
修改文件 ``/etc/fstab ``以进行硬盘自动挂载
查询uuid方法:使用*blkid*进行查询
#### 添加windows启动项支持
1. 在grub界面按下c进入命令行模式,使用``ls -l``进行对EFI分区的uuid的查询，记录uuid
2. 进入 **\boot\grub\grub.cfg** 进行编辑
3. 加入以下代码进行添加,
```
menuentry "Windows"{
	insmod part_gpt
	insmod search_fs_uuid
	insmod chain
	search --fs-uuid --no-floppy --set=root XXXX-XXXX
	chainloader ($root)/EFI/MICROSOFT/BOOT/bootmgfw.efi
	}
```

---

## 系统使用细则
### 系统备份相关
#### timeshift相关
1. 如果timeshift自动备份不生效请使用以下代码启用定时任务，timeshift依赖于*cronie*服务
``` 
sudo systemctl start --now cronie.service 
```
2. 请勿在备份期间使用vscode,会发生未知问题导致桌面崩坏
3. 如果发生滚挂等现象请使用
```
sudo timeshift --restore --snapshot '2019-07-16_16-35-42' --skip-grub
```
进行恢复
### 系统启动相关优化

#### 换一个更好的系统内核(<font color="red">linux-zen</font>)
1. 使用``uname -r``进行内核的查询
2. 安装新内核``sudo pacman -S linux linux-headers/sudo pacman -S linux-zen``(第二种)
3. 更新 boot loader 的配置<br>
使用以下命令进行更新
```
sudo grub-mkconfig -o /boot/grub/grub.cfg
```
4. 重新启动后可以在grub的选项之中去进行内核的切换，删除内核详细见yay/pacman的使用

额外内容
打开/etc/default/grub文件进行更加细致化的修改
>GRUB_DEFAULT=saved <br>
>GRUB_SAVEDEFAULT=true <br>
>GRUB_DISABLE_SUBMENU=y 

含义解释
>GRUB_DEFAULT — Default boot selection. <br>
>GRUB_SAVEDEFAULT — GRUB to remember the last selection. <br>
>GRUB_DISABLE_SUBMENU — Disable submenus.

#### 完善你的开机启动服务管理

&emsp; 使用``systemctl list-unit-files --type=service|grep enabled``进行对systemd管理下的开机启动状态的查询<br>
进行服务的基本修改:<br>
&emsp; 使用``systemctl enable/disenable XXXX.service``进行服务开机启动的设置<br>
&emsp; 使用``systemctl start/stop XXXX.service``进行服务运行的设置 

#### 
 
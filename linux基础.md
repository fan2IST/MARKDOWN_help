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
 
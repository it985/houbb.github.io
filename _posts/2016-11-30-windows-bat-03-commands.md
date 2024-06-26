---
layout: post
title: windows bat 脚本教程-03-常见 command 命令 
date:  2016-11-30 14:14:36 +0800
categories: [Windows]
tags: [windows, shell, bat]
published: true
---


# 常用批处理命令概览

以下是一些常用的批处理命令及其描述的列表：

| 序号 | 命令及描述 |
|------|------------|
| 1    | **VER** 此批处理命令显示你正在使用的MS-DOS版本。 |
| 2    | **ASSOC** 这是一个批处理命令，用于将扩展名与文件类型（FTYPE）关联，显示现有的关联，或删除关联。 |
| 3    | **CD** 此批处理命令有助于更改到不同的目录，或显示当前目录。 |
| 4    | **CLS** 此批处理命令清除屏幕。 |
| 5    | **COPY** 此批处理命令用于将文件从一个位置复制到另一个位置。 |
| 6    | **DEL** 此批处理命令用于删除文件，而不是目录。 |
| 7    | **DIR** 此批处理命令列出目录的内容。 |
| 8    | **DATE** 此批处理命令帮助查找系统日期。 |
| 9    | **ECHO** 此批处理命令显示消息，或打开或关闭命令回显。 |
| 10   | **EXIT** 此批处理命令退出DOS控制台。 |
| 11   | **MD** 此批处理命令在当前位置创建一个新目录。 |
| 12   | **MOVE** 此批处理命令在目录之间移动文件或目录。 |
| 13   | **PATH** 此批处理命令显示或设置路径变量。 |
| 14   | **PAUSE** 此批处理命令提示用户并等待输入一行文本。 |
| 15   | **PROMPT** 此批处理命令可用于更改或重置cmd.exe提示符。 |
| 16   | **RD** 此批处理命令删除目录，但在删除之前目录需要为空。 |
| 17   | **REN** 重命名文件和目录 |
| 18   | **REM** 此批处理命令用于批处理文件中的备注，防止备注内容被执行。 |
| 19   | **START** 此批处理命令在新窗口中启动程序，或打开文档。 |
| 20   | **TIME** 此批处理命令设置或显示时间。 |
| 21   | **TYPE** 此批处理命令将文件或文件的内容打印到输出。 |
| 22   | **VOL** 此批处理命令显示卷标。 |
| 23   | **ATTRIB** 显示或设置当前目录中文件的属性 |
| 24   | **CHKDSK** 此批处理命令检查磁盘是否有任何问题。 |
| 25   | **CHOICE** 此批处理命令为用户提供选项列表。 |
| 26   | **CMD** 此批处理命令调用另一个命令提示符实例。 |
| 27   | **COMP** 此批处理命令基于文件大小比较2个文件。 |
| 28   | **CONVERT** 此批处理命令将卷从FAT16或FAT32文件系统转换为NTFS文件系统。 |
| 29   | **DRIVERQUERY** 此批处理命令显示所有已安装的设备驱动程序及其属性。 |
| 30   | **EXPAND** 此批处理命令从压缩的.cab柜员文件中提取文件。 |
| 31   | **FIND** 此批处理命令在文件或输入中搜索字符串，输出匹配的行。 |
| 32   | **FORMAT** 此批处理命令格式化磁盘以使用Windows支持的文件系统，如FAT、FAT32或NTFS，从而覆盖磁盘的先前内容。 |
| 33   | **HELP** 此批处理命令显示Windows提供的命令列表。 |
| 34   | **IPCONFIG** 此批处理命令显示Windows IP配置。按连接显示配置和该连接的名称。 |
| 35   | **LABEL** 此批处理命令添加、设置或删除磁盘标签。 |
| 36   | **MORE** 此批处理命令一次显示一个屏幕的文件或文件内容。 |
| 37   | **NET** 根据使用的命令提供各种网络服务。 |
| 38   | **PING** 此批处理命令通过网络向指定地址发送ICMP/IP "echo"数据包。 |
| 39   | **SHUTDOWN** 此批处理命令关闭计算机，或注销当前用户。 |
| 40   | **SORT** 此批处理命令从源文件获取输入并按字母顺序排序其内容，从A到Z或Z到A。它在控制台上打印输出。 |
| 41   | **SUBST** 此批处理命令给本地文件夹分配一个驱动器字母，显示当前分配，或删除分配。 |
| 42   | **SYSTEMINFO** 此批处理命令显示计算机及其操作系统的配置。 |
| 43   | **TASKKILL** 此批处理命令结束一个或多个任务。 |
| 44   | **TASKLIST** 此批处理命令列出任务，包括任务名称和进程ID（PID）。 |
| 45   | **XCOPY** 此批处理命令以更高级的方式复制文件和目录。 |
| 46   | **TREE** 此批处理命令显示当前目录的所有子目录的树形结构，到任何递归或深度级别。 |
| 47   | **FC** 此批处理命令列出两个文件之间的实际差异。 |
| 48   | **DISKPART** 此批处理命令显示和配置磁盘分区的属性。 |
| 49   | **TITLE** 此批处理命令设置在控制台窗口中显示的标题。 |
| 50   | **SET** 显示当前系统上的环境变量列表。 |

请注意，以上命令在不同版本的Windows操作系统中可能有所差异，具体功能和使用方式请参考相应系统的官方文档。

# 参考资料

https://www.tutorialspoint.com/batch_script/batch_script_commands.htm

* any list
{:toc}
# Linux

## 目录结构

```yaml
/bin 常用命令，存放最经常使用的命令
/home 用户目录
/root 管理员目录
/etc 用户自己安装的应用程序的配置文件
/usr 安装程序默认文件夹，类似program files
/mnt 挂在别的文件系统，可以和windows互通
/opt 软件安装包目录
/usr/local 软件安装目录
/var 放经常会变化的文件，如日志文件
/sbin 管理员使用的系统管理程序
/lib 动态链接共享库
/lost+found 非法关机后，才存放文件
/boot linux启动的相关文件
/proc 虚拟目录，系统内存的映射，用来获取系统信息
/src 存放一些服务启动后需要提取的数据
/sys 与系统有关的文件
/tmp 存放临时文件
/dev 设备管理器，硬件设备以文件的形式存储
/media 自动识别设备，U盘、光驱等识别后挂载在此目录下
/selinux 安全子系统
```



## Vi & Vim 编辑器

```yaml
正常模式：以Vim打开一个档案就直接进入一般模式了（这是默认的模式）。在这个模式中，使用上下左右按键来移动光标，可以使用删除字符或删除整行来处理档案内容，也可以使用复制、粘贴来处理你的文件数据。
编辑模式：按下 i,I,o,O,a,A,r,R 等任何一个字母之后会进入编辑模式,，一般来说按i即可。
命令行模式：键入Esc再输入 : ，在这个模式当中，可以提供你相关指令，完成读取、存盘、替换、离开Vim、显示行号等的动作则是在此模式中达成的。
```

### Vi & Vim 常用快捷键

```yaml
拷贝当前行：yy，拷贝当前行向下的5行：5yy，粘贴：p。（一般模式）
删除当前行：dd，删除当前行向下的5行：5dd。（一般模式）
在文件中查找某个单词：/单词，键入n查找下一个。
```



## 文件目录

```yaml
pwd: 显示当前目录的绝对路径 print working directory
ls: 基本语法：ls [选项] 目录或文件
		-a: 显示当前的目录的所有文件，包括隐藏文件
		-l: 以列表的形式显示信息
cd: 切换到指定目录
		cd ..: 回到当前目录的上一级目录
mkdir: 创建目录
rmdir: 删除空目录
		rm -rf <file_path>: 如果需要删除非空目录
touch: 创建空文件 使用: touch <file_name>
mv: 移动或重命名文件与目录
cp: copy文件到指定目录
		cp [选项] <file_path> <target_path>
cat: 查看文件内容  使用: cat 选项 <file_name>
echo: 终端输出指令,输出内容到控制台
head: 用于显示文件开头部分的内容，默认显示前十行
tail: 用于显示文件尾部部分的内容，默认显示后十行
> 和 >> 指令: > 表示输出重定向，>> 表示追加
		ls -l > 文件 	写入到文件
		ls -al >> 文件 	内容追加到文件中
history: 查看已经执行过的命令历史
```



## 时间日期

```yaml
date: 显示当前时间
cal: 查看日历
```



## 搜索查找

```yaml
find: 将从指定目录向下递归地遍历其各个子目录，将满足条件的文件或者目录显示在终端。
		find -name: 	按照指定的文件名查找文件
locate: 快速定位文件路径
```



## 进程管理

```yaml
ps -a: 显示当前终端的所有进程信息
	 -e: 显示所有进程
终止进程: kill & killall
kill -9 <process_id>: 根据进程号杀死进程, 强迫进程立即停止
killall <process_name>: 根据进程名杀死该进程及其子进程(支持通配符)
```



## Shell 编程

```yaml

```



### Reference：

1. https://www.hostinger.com/tutorials/linux-commands
2. https://www.loggly.com/wp-content/uploads/2015/05/Linux-Cheat-Sheet-Sponsored-By-Loggly.pdf
3. https://cheatography.com/davechild/cheat-sheets/linux-command-line/





Typora：Remember to save

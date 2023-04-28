# Chapter 01

## Vim

### 正则表达式查找替换

#### 格式

```
:[range]s/from/to/[flags]
```

- `range`:搜索范围，默认为当前行
    - `num1,num2`:两行之间
    - `num`:限于某行
    - `%`:在所有行 $\equiv$ `0,$`
- `flags`可合并使用
    - `c`: confirm，执行前询问
    - `e`: error-ignore，不显示错误
    - `g`: global，不询问，替换整行，否则只替换首次匹配
    - `i`: ignore case，忽略大小写

#### 应用

##### 示例1

对于文本：

```txt
123456aaaaaaaaaaaaaa
bbbbbbbbbbbbbbbbbcccccccccccccc
ddddddddddddeeeeeeeeeeeee
fffffffffffffffflllllllllllll
jjjjjjjjjjjkkkkkkkkkkkk
yyyyyyy654321
```

1. 删除数字之间的字母:

```
:%s/\(\d*\)[a-z]*\(\d*\)\n/\1\2/i
:%s/\(\d*\)[a-z\n]+\(\d*\)/\1\2/i
```

2. 修改文件内容，在字母两端加`{}`:

```
:%s/\(\d\+\)\([a-z\n]\+\)\(\d\+\)/\1{\2}\3/i
```

##### 示例2

对于文本：

```txt
.123
.456
.789
.321
.654
.987
```

在所有小数点前面加0

1. `Ctrl+v`进入快选择模式，选择需要编辑的块，按`I`(大写)进入行首插入模式，输入`0`按``ESC`。

2. 输入指令：

```
:%s/\(\.\)/0\1/gi
```

## APT(Advanced Packaging Tool)

Debian包管理工具，有很多子工具与APT交互以操作包，apt-get完成底层操作，可以支持其他工具，具有很多不常用命令；apt作为面向用户端的命令行工具，支持apt-get, apt-cache以及新增的一些指令，这些指令都非常常用。

|apt command|the command it replaces|function of the command|
|:-:|:-:|:-:|
|apt install|apt-get install|Installs a package|
|apt remove|apt-get remove|Removes a package|
|apt purge|apt-get purge|Removes package with configuration|
|apt update|apt-get update|Refreshes repository index|
|apt upgrade|apt-get upgrade|Upgrades all upgradable packages|
|apt autoremove|apt-get autoremove|Removes unwanted packages|
|apt full-upgrade|apt-get dist-upgrade|Upgrades packages with auto-handling of dependencies|
|apt search|apt-cache search|Searches for the program|
|apt show|apt-cache show|Shows package details|

|new apt command|function of the command|
|:-:|:-:|
|apt list|Lists packages with criteria (installed, upgradable etc)|
|apt edit-sources|Edits sources list|

## Linux file attributes

<img src="photos/file-ls.jpeg" style="width:50%"/>

查看文件属性：

```sh
ls -al <file-directory> | grep <file-name>
```

更改文件属性：`-R`操作皆为递归更改子目录

1. `chgrp`:更改文件属组

```
chgrp [-R] 属组名 文件名
```

2. `chown`:更改文件属主

```
chown [-R] 属主名:属组名 文件名
```

3. `chmod`:更改文件读写属性

```
chmod [-R] [0-7][0-7][0-7] 文件或目录
```

```
chmod [ugoa] [+-=] [rwx] 文件或目录
```

- `r`: 0x100
- `w`: 0x10
- `x`: 0x1

### 文件样例

```
/etc/apt/sources.list
```

```
➜  ~ ls -l /etc/apt | grep sources.list
-rw-rw-r-- 1 root root 3252 Oct  3  2021 sources.list
```

该文件所有用户具有读权限，因此cat命令能够执行

## Linux user level

- `su`: switche user command，使用者非root时需要知道目标用户的密码
- `sudo`: super user do，无需知晓root用户密码，只要本用户是sudoer则只需本用户密码

配置文件`/etc/sudoers`

在实验中，Ubuntu上从普通用户无法通过普通用户命令切换到root，需要`sudo su`或`sudo su -`，前者切换用户但不切换环境变量，后者将会同时切换环境变量
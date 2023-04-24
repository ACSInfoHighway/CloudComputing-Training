# 探索更多内容

## 熟悉基本开发流程

在完成`环境准备`和`基础教程`之后，你已经具备在linux环境下进行开发的基础，并且了解如何通过手册和互联网解决可能遇到的问题。

思考：  

1. 在环境下分别使用golang和c语言编一个`Hello world`项目，并编译运行；
2. 分别为`1`中的``Hello World`编写`Makefile`并成功编译；
3. 使用`git`创建一个项目，并完成下面几次commit：  
    * 提交`Hello-World-C`文件夹及源码；
    * 提交`Hello-World-go`文件夹及源码；
    * 提交`Hello-World-C`的`Makefile和`和`.gitignore`（git不跟踪编译产生的文件）；
    * 提交`Hello-World-go`的`Makefile和`和`.gitignore`（git不跟踪编译产生的文件）；
    * 分别编译两个项目，确认编译产生的文件是否被跟踪；

## tmux

`tmux`是CLI下的终端复用器，在一个`tmux`中可以同时使用多个/组`terminal`，并且可以将打开的`terminal`常驻后台，避免关闭`terminal`导致任务提前终止。

**在进行长时间作业任务时，tmux是很好的辅助工具，它脱离传统tty终端而存在，使得需要运行的命令或进程能够不受外界影响运行**

安装`tmux`：

```sh
sudo apt-get install tmux
```

为当前用户创建一个`tmux`配置文件

```sh
vim ~/.tmux.conf
```

关于如何进行配置，这里不再进行赘述，`RTFM` & `STFW`，再根据自己的习惯配置即可。

## Tips

熟悉一些常用的命令行工具, 并强迫自己在日常操作中使用它们：

* 文件管理 - `cd`, `pwd`, `mkdir`, `rmdir`, `ls`, `cp`, `rm`, `mv`, `tar`
* 文件检索 - `cat`, `more`, `less`, `head`, `tail`, `file`, `find`
* 输入输出控制 - `重定向`,` 管道`, `tee`, `xargs`
* 文本处理 - `vim`, `grep`, `awk`, `sed`, `sort`, `wc`, `uniq`, `cut`, `tr`
* 正则表达式
* 系统监控 - `jobs`, `ps`, `top`, `kill`, `free`, `dmesg`, `lsof`
* 上述工具覆盖了程序员绝大部分的需求
    * 可以先从简单的尝试开始, 用得多就记住了, 记不住就`man`

*思考：*

```sh
find . -name "*.[ch]" | xargs grep "#include" | sort | uniq
```

*上面这条命名的功能是什么？*
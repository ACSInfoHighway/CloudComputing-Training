# man入门教程

以下内容引用自yzh的[PA实验课程网站](https://nju-projectn.github.io/ics-pa-gitbook/ics2022/man.html)，并有少量修改和补充。
这是一个man的使用教程，同时给出了一个如何寻找帮助的例子。

## 初识man
因为课程实验所迫，你不得不使用Linux，不得不使用十分落后的命令行。实验内容大多数都要在命令行里进行，面对着一大堆陌生的命令和参数。

Linux提供了丰富的帮助手册，当你需要查看某个命令的参数时不必到处上网查找，只要`man`一下即可。
```
$ man
What manual page do you want?
For example, try 'man man'.
```

根据提示输入
```
man man
```
敲了回车之后，`man`展示了关于`man`的全部文档。
```
MAN(1)                        Manual pager utils                        MAN(1)

NAME
       man - an interface to the system reference manuals

SYNOPSIS
       man [man options] [[section] page ...] ...
       man -k [apropos options] regexp ...
       man -K [man options] [section] term ...
       man -f [whatis options] page ...
       man -l [man options] file ...
       man -w|-W [man options] page ...
...
```

## 寻找帮助
很快，你已经看到"最后一行"了。"最后一行"里面含有一些信息:

> Manual page man(1) line 1 (press h for help or q to quit)

按下`h`进入"SUMMARY OF LESS COMMANDS"，文档详细描述了如何使用man，带有'\*'标记的命令可以在前面跟一个数，这时命令的行为在括号里给出。

## 探索man
一边阅读帮助，一边尝试新的命令，虽然记不住这么多命令，但可以随时来查看帮助。掌握了一些基本的命令之后，按`q`可以离开帮助。
整个manual可以分成9大类，每个manual page都属于其中的某一类，一个manual page主要包含以下的小节:

* NAME - 命令名
* SYNOPSIS - 使用方法大纲
* CONFIGURATION - 配置
* DESCRIPTION - 功能说明
* OPTIONS - 可选参数说明
* EXIT STATUS - 退出状态，这是一个返回给父进程的值
* RETURN VALUE - 返回值
* ERRORS - 可能出现的错误类型
* ENVIRONMENT - 环境变量
* FILES - 相关配置文件
* VERSIONS - 版本
* CONFORMING TO - 符合的规范
* NOTES - 使用注意事项
* BUGS - 已经发现的bug
* EXAMPLE - 一些例子
* AUTHORS - 作者
* SEE ALSO - 功能或操作对象相近的其它命令

在EXAMPLES小节中有一个使用-k的例子:
```
man -k printf
```
你阅读这个例子的解释: 搜索和printf相关的manual page。还是不太明白这是什么意思，于是你退出`man`，在命令行中输入
```
man -k printf
```
发现输出了很多和`printf`相关的命令或库函数，括号里面的数字代表相应的条目属于`manual`的哪一个大类。例如`printf (1)`是一个shell命令，而`printf (3)`是一个库函数。 要访问库函数`printf`的manual page，你需要在命令行中输入
```
man 3 printf
```
当你想做一件事的而不知道用什么命令的时候，`man`的`-k`参数可以用来列出候选的命令，然后再通过查看这些命令的manual page来学习怎么使用它们。

接下来，你又开始学习man的其它功能...

## 开始旅程
到这里，你应该掌握`man`的用法了。 你应该经常来拜访ta，因为在很多时候，ta总能给你提供可靠的帮助。

在这个励志的故事中，你学会了:

阅读程序输出的提示和错误信息
通过搜索来定位你关心的内容
动手实践是认识新事物的最好方法
独立寻找帮助，而不是一有问题就问班上的大神
于是，你就这样带着`man`踏上了Linux之旅...
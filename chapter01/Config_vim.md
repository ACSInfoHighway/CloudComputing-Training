# 配置vim
```
sudo apt install vim
```
> Vim是什么？  
Vim是从vi发展出来的一个文本编辑器。具有代码补完、编译及错误跳转等方便编程的功能特别丰富，在程序员中被广泛使用。和Emacs并列成为类Unix系统用户最喜欢的编辑器。

## Vim基础操作
根据[镜像网站](https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/)的提示，使用vim修改文件`/etc/apt/sources.list`。如果你还不熟悉vim的操作，请通过一下几种方式进行学：
* 在CLI中执行`vimtutor`，打开vim的tutorial。（推荐）
* 执行在网络上搜索相关学习材料。
* [openvim](https://www.openvim.com/tutorial.html)、[vimgenius](http://www.vimgenius.com/lessons/vim-intro)
> 注意：这里不需要完整掌握Vim全部的知识，这也不现实，先学习创建、修改、保存文件，光标移动，搜索&替换，模式切换等基础操作即可。目的是快速上手和满足开发需要。其他进阶功能可以帮助提行开发效率，可以根据需求进行学习。

### vim示例1
现有如下文件`test.txt`，如果要删除数字中间的字母应该如何操作？
```
123456aaaaaaaaaaaaaa
bbbbbbbbbbbbbbbbbcccccccccccccc
ddddddddddddeeeeeeeeeeeee
fffffffffffffffflllllllllllll
jjjjjjjjjjjkkkkkkkkkkkk
yyyyyyy654321
```
修改文件内容如下，输入da{：
```
123456{aaaaaaaaaaaaaa
bbbbbbbbbbbbbbbbbcccccccccccccc
ddddddddddddeeeeeeeeeeeee
fffffffffffffffflllllllllllll
jjjjjjjjjjjkkkkkkkkkkkk
yyyyyyy}654321
```
### vim示例2
现有如下文件num.txt，将所有.x形式的小数比表示为0.x形式
```
.123
.456
.789
.321
.654
.987
```
Ctrl+v 进入快选择模式，选择需要编辑的块，按`I`(大写)进入行首插入模式，输入0按ESC。
> 思考：针对上述两个示例，是否有其他操作方案？

## 配置vimrc
vim提供了比vi更多的功能，但是大部分功能默认是关闭的，因此需要根据需求进行配置。
通常情况下通过`.vimrc`文件进行vim配置管理，Linux的每一个用户都可以拥有自己的配置，一般情况下路径为`~/.vimrc`。
首先，准备一份默认的`.vimrc`
```
cp /etc/vim/vimrc ~/.vimrc
```
然后，使用vim修改`.vimrc`，可以根据需求决定是否打开以下配置
```
syntax on                    " 高亮显示
filetype plugin indent on    " 开启文件类型自动识别
set showmatch                " 显示括号匹配
set ignorecase               " 不全分大小写
set smartcase
set incsearch
setlocal noswapfile          " 不生成swap文件
set bufhidden=hide           
set number                   " 显示行号
set cursorline               " 突出显示当前行
set ruler                    " 打开状态栏标尺
set softtabstop=4            " 按退格键时删掉4个空格
set tabstop=4                " 设定 tab 长度为4
set hlsearch                 " 搜索时高亮显示被找到的文本
set magic                    " 设置魔术
set smartindent              " newline使用智能自动缩进
```
> 提示：  
> * " 是注释符，表示该行设置已经被注释，修改配置时可以新增一行，如果已有被注释的相应配置，也可以删除"。  
> * 如果相同配置出现多次则以最后一个为准。
> * 对`.vimrc`修改保存后，需要重启打开vim才会生效，或者在当前vim窗口输入`:source ~/.vimrc`生效。
```
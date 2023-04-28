# 安装工具
在Linux环境下，通常需要通过命令行下载安装各种工具和应用，这些工具通常叫做包管理器。在Ubuntu中使用的包管理系统叫做Advanced Packaging Tool(APT)，通过apt/apt-get、apt-cache、apt-config可以和APT进行交互。
> 思考: apt和apt-*都可以和APT包管理系统交互，二者有什么差异？

## 检查网络状态
检查虚拟机网络是否正常，正常情况下应该得到如下反馈：
```shell
czh@vm:~$ ping mirrors.tuna.tsinghua.edu.cn -c 4
PING bfdmirrors.s.tuna.tsinghua.edu.cn (101.6.15.130) 56(84) bytes of data.
64 bytes from 101.6.15.130 (101.6.15.130): icmp_seq=1 ttl=63 time=4.87 ms
64 bytes from 101.6.15.130 (101.6.15.130): icmp_seq=2 ttl=63 time=4.01 ms
64 bytes from 101.6.15.130 (101.6.15.130): icmp_seq=3 ttl=63 time=4.47 ms
64 bytes from 101.6.15.130 (101.6.15.130): icmp_seq=4 ttl=63 time=5.61 ms
```
```shell
czh@vm:~$ ping mirrors.ustc.edu.cn -c 4
PING mirrors.ustc.edu.cn (202.141.176.110) 56(84) bytes of data.
64 bytes from mirrors.ustc.edu.cn (202.141.176.110): icmp_seq=1 ttl=63 time=24.6 ms
64 bytes from mirrors.ustc.edu.cn (202.141.176.110): icmp_seq=2 ttl=63 time=26.2 ms
64 bytes from mirrors.ustc.edu.cn (202.141.176.110): icmp_seq=3 ttl=63 time=24.7 ms
64 bytes from mirrors.ustc.edu.cn (202.141.176.110): icmp_seq=4 ttl=63 time=23.6 ms

--- mirrors.ustc.edu.cn ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3003ms
rtt min/avg/max/mdev = 23.629/24.773/26.155/0.902 ms
```
> 上面分别测试了USTC和TUNA两个镜像网站的联通情况，可以选取连通性较好的网站作为apt源镜像。

## 配置APT
```
echo "deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse" > /etc/apt/sources.list
echo "deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse" > /etc/apt/sources.list
```
但是CLI会提示:
```
bash: /etc/apt/sources.list: Permission denied
```
这是因为当前用户没有权限操作/etc/apt/sources.list文件，通过`su -`切换特权用户或者在命令前加上`sudo`可以解决，如果是第一次运行`sudo`，会提示输入当前用户的密码。
> 请了解Linux文件基本属性，并查找资料学习如何查看和修改文件基本属性。

对比以下两个命令的执行结果，思考添加sudo后为什么上述命令可以执行成功
```
whoami
sudo whoami
```

通过`cat`验证对文件的修改是否生效
```
cat /etc/apt/sources.list
```
> 思考：问什么通过cat命令对`/etc/apt/sources.list`操作时没有提示权限不允许。

## 更新APT包信息
```
sudo apt update
```
> 思考：apt/apt-*还有哪些命令，他们的作用分别是什么？

## 安装工具
```
apt-get install build-essential
apt-get install man
apt-get install git
```
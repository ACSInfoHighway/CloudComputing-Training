# Chapter 3

## Docker

`docker build -t`参数-t：`--target string`，设置目标

`docker run`参数：

- `-add-host list`客制化的IP映射
- `--cpu decimal`设置cpu数量等
- `--detach`，detach，任务后台运行并打印容器ID
- `--env list`，设置环境变量
- `--hostname string`，容器主机名
- `-i, --interactive`
- `--ip, --ipv6`
- `-m, --memory bytes`设置内存大小
- `--mount mount`
- `--name`，设置名称
- `--network`，容器联网
- `--pid string`
- `-p, --publish list`向主机公开端口
- `-t, --tty`分配启用伪shell
- `-U, --user string`
- `-w, --workdir string`

### 容器压缩

预编译go程序：

`GOOS=linux CGO_ENABLED=0 go build -o server server.go`

go env中：

```
GOOS="linux"
CGO_ENABLED="1"
```

因此删去`GOOS=linux`并不影响

而`CGO_ENABLED=0`表示不会链接C程序，注意到压缩时使用的是alpine轻量级linux，只有musl libc库，在运行时会有缺失的libc库动态链接实现，因此会报错：

```
server: line 1: ELF: not found
server: line 5: syntax error: unexpected ")"
```

这里有个bug是目标镜像无bash导致会出现无法直接运行二进制文件，显示file not found error，切换成`sh [binary]`后解决。

## Minikube

安装时bugs:

1. proxy，阿里云暂时不管用，需要全局设置的代理，并且kicbase镜像最好通过docker直接pull，而后minikube启动选项增加`--base-image`
2. minikube 26.1在启动时无法检测到已安装的cri-docker.socket，更换成23.1能够正常启动
3. coredns应用cm有bug，删除loop字段即可

Q&A:

> 1. 安装kubectl后，在并未配置Kubernetes集群信息的情况下，kubectl是如何集群的信息并与api-server成功通信？（提示：查看~/.kube目录）

按照官方文档：

> To access a cluster, you need to know the location of the cluster and have credentials to access it

如果按照正常流程启动minikube，配置会自动生成，kubectl也能够自动获取，通过`config`

> 2. minikube 支持dashboard命令，如何从外部（虚拟机外部）访问dashboard url？
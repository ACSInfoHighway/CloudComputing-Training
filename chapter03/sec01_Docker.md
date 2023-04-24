# Docker

## 容器

容器是一种操作系统虚拟化形式。可以使用一个容器来运行从小型微服务或软件进程到大型应用程序的所有内容。容器包含所有必要的可执行文件、二进制代码、库和配置文件。但是，与服务器或计算机虚拟化方法不同，容器不包含操作系统映像。因此，它们更轻便且可移植，其开销很小。在大型应用程序部署中，可以将多个容器部署为一个或多个容器集群。此类集群可由 Kubernetes 等容器编排程序管理。 --[NetApp](https://www.netapp.com/zh-hans/devops-solutions/what-are-containers/)

容器技术使用Linux内核和内核功能（例如 Cgroups 和 namespaces）来分隔进程，以便各进程相互独立运行。这种独立性正是采用容器的目的所在；它可以独立运行多种进程、多个应用，更加充分地发挥基础设施的作用，同时保持各个独立系统的安全性。

>详细介绍：https://en.wikipedia.org/wiki/OS-level_virtualization

## 容器与虚拟机

人们考虑虚拟化时，通常会想到虚拟机(VM)。事实上，虚拟化可以采用多种形式，容器就是其中之一。那么，VM和容器之间的区别是什么?

大体说来，VM虚拟化基础硬件，让多个操作系统(OS)实例可以在相同的硬件上运行。每个VM运行一个OS，并有权访问表示基础硬件的虚拟化资源。VM有许多好处。其中包括，能够在同一服务器上运行不同操作系统，还可以更高效、更经济地利用物理资源，更快完成服务器配置。另一方面，每个VM都包含OS映像、库、应用程序等，因此可能会变得相当大。

容器虚拟化基础OS，并使容器化应用能够感知其本身具备OS，包括CPU、内存、文件存储和网络连接。由于对基础OS和基础结构的差异进行了抽象，因此只要基本映像一致，就可以在任何地方部署并运行容器。对于开发人员来说，这有很大的吸引力。

由于容器共享主机OS，因此它们不需要启动OS或加载库。这使得容器更加高效和轻量。容器化应用程序可以在几秒钟内启动，与VM方案相比，应用程序的更多实例可以适应计算机。共享OS方法具有额外的好处，即减少维护(如修补和更新)开销。

尽管容器是可移植的，但它们被限制于为其定义的操作系统。例如，适用于Linux的容器无法在Windows上运行，反之亦然。 --[Microsoft](https://azure.microsoft.com/zh-cn/overview/what-is-a-container/#overview)
![虚拟机与容器](https://www.redhat.com/cms/managed-files/styles/wysiwyg_full_width/s3/virtualization-vs-containers_transparent.png?itok=q-E2I2-L)

## Docker

Docker在容器的基础上，进行了进一步的封装，从文件系统、网络互联到进程隔离等等，极大的简化了容器的创建和维护。使得Docker技术比虚拟机技术更为轻便、快捷。

Docker使用Google公司推出的Go语言进行开发实现，基于Linux内核的cgroup，namespace，以及OverlayFS类的UnionFS等技术，对进程进行封装隔离，属于操作系统层面的虚拟化技术。由于隔离的进程独立于宿主和其它的隔离的进程，因此也称其为容器。最初实现是基于LXC，从0.7版本以后开始去除LXC，转而使用自行开发的libcontainer，从1.11版本开始，则进一步演进为使用runC和containerd。

> runc 是一个 Linux 命令行工具，用于根据 OCI容器运行时规范 创建和运行容器。
> containerd 是一个守护程序，它管理容器生命周期，提供了在一个节点上执行容器和管理镜像的最小功能集。

![docker-on-linux](https://docs.microsoft.com/en-us/virtualization/windowscontainers/deploy-containers/media/docker-on-linux.png)

## 制作一个容器

> 请参照文档自行安装docker, https://docs.docker.com/engine/install/ubuntu/

1. 创建工作目录

```sh
$ mkdir golang-server
$ cd golang-server
```

2. 用golang编写一个`server.go` 

```go
package main
import (
    "fmt"
    "net/http"
)
func indexHandler(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Hello Docker!")
}
func main() {
    http.HandleFunc("/", indexHandler)
    http.ListenAndServe(":8080", nil)
}
```

3. 编写`Dockerfile`

```Dockerfile
FROM golang:1.18
WORKDIR /build
COPY server.go .
RUN go build -o server server.go
CMD ["./server"]
```

4. 构建镜像

```sh
docker build -t [ID]/hello-world-golang .
```

> [ID]是个人Docker hub的id，如果id不对会提示:  `denied: requested access to the resource is denied`

5. 测试容器
```sh
$ docker run --rm -it --name hello-world-golang  -p 8888:8080 [ID]/hello-world-golang
Serving HTTP on 0.0.0.0 port 8080 (http://0.0.0.0:8080/) ...
# 另一个shell
$ curl http://localhost:8888
Hello Docker!
```

curl返回`Hello Docker!`，构建容器成功。

*思考：*
1. *docker的功能非常强大，通过docker help可以看到docker支持的全部命令。*
2. *步骤`4.`中，-t参数的作用是什么？*
3. *步骤`5.`中，-t参数的作用是什么？其他参数的作用是什么？*


## 进阶-压缩镜像

在上个小结中，我们制作了镜像`[ID]/hello-world-golang`

```sh
$ docker images  | grep hello-world-golang
hello-world-golang    latest     bd2875867667   31 minutes ago   970MB
```

一个10行左右golang代码，编译后竟然有`970MB`，是否可以减少镜像的大小？

1. 在`golang-server`目录下预先编译`server.go`

```sh
GOOS=linux CGO_ENABLED=0 go build -o server server.go
```

*思考：*
- *这里为什么需要增加`GOOS=linux`?*
- *尝试删除编译参数`CGO_ENABLED=0`，测试容器是否可以正常运行？*

2. 修改`Dockerfile`

```Dockerfile
FROM alpine
WORKDIR /build
COPY server .
CMD ["./server"]
```

3. 构建镜像并测试

```sh
docker build -t [ID]/hello-world-golang .
docker run --rm -it --name hello-world-golang -p 8888:8080 [ID]/hello-world-golang
```

*思考：在上一节中已经制作了名为`[ID]/hello-world-golang`的镜像，这里再次构建会出现什么问题？如何避免？*

4. 查询镜像大小

```sh
$ docker images  | grep hello-world-golang
hello-world-golang    latest     5c48069c1c8e   3 minutes ago   11.6MB
```

这里我们构建的镜像已经只有`11.6MB`，大小约为之前的1%.

*思考：镜像大小减小为1%的原因是什么？(注意对比两次的Dockerfile)*

5. 登录docker hub，上传镜像

```sh
docker login
docker push [ID]/hello-world-golang
```

> 提示：上传到Dockerhub是为了方便将服务部署到Kubernetes中。

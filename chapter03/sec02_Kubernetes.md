# Kubernetes

## 什么是Kubernetes

Kubernetes，又称为k8s（首字母为 k、首字母与尾字母之间有 8 个字符、尾字母为 s，所以简称 k8s）或者简称为 "kube"，是一种可自动实施 Linux 容器操作的开源平台。它可以帮助用户省去应用容器化过程的许多手动部署和扩展操作。也就是说，可以将运行 Linux 容器的多组主机聚集在一起，由Kubernetes帮助您轻松高效地管理这些集群。而且，这些集群可跨公共云、私有云或混合云部署主机。因此，对于要求快速扩展的云原生应用而言（例如借助 Apache Kafka 进行的实时数据流处理），Kubernetes是理想的托管平台。

Kubernetes最初由Google的工程师开发和设计。Google是最早研发Linux 容器技术的企业之一（组建了cgroups）曾公开分享介绍Google如何将一切都运行于容器之中（这是Google云服务背后的技术）。Google每周会启用超过 20 亿个容器——全都由内部平台Borg支撑。Borg是Kubernetes的前身，多年来开发Borg的经验教训成了影响Kubernetes中许多技术的主要因素。    --[RedHat](https://www.redhat.com/zh/topics/containers/what-is-kubernetes)

### 相关术语

和其它技术一样，Kubernetes 也会采用一些专用的词汇，这可能会对初学者理解和掌握这项技术造成一定的障碍。为了帮助您了解 Kubernetes，我们在下面来解释一些常用术语。  

- **主机（Master）** ：用于控制 Kubernetes 节点的计算机。所有任务分配都来自于此。  
- **节点（Node）**：负责执行请求和所分配任务的计算机。由 Kubernetes 主机负责对节点进行控制。  
- **容器集（Pod）**：被部署在单个节点上的，且包含一个或多个容器的容器组。同一容器集中的所有容器共享同一个 IP 地址、IPC、主机名称及其它资源。容器集会将网络和存储从底层容器中抽象出来。这样，您就能更加轻松地在集群中移动容器。  
- **复制控制器（Replication controller）：**用于控制应在集群某处运行的完全相同的容器集副本数量。  
- **服务（Service）**：将工作内容与容器集分离。Kubernetes 服务代理会自动将服务请求分发到正确的容器集——无论这个容器集会移到集群中的哪个位置，甚至可以被替换掉。  
- **Kubelet**：运行在节点上的服务，可读取容器清单（container manifest），确保指定的容器启动并运行。  
- **kubectl**： Kubernetes 的命令行配置工具。

### 系统架构

![plantform](../pic/03/k8s-arch.png)

> 关于组件的介绍请参考：<https://kubernetes.io/zh/docs/concepts/overview/components/>

## 部署kubernetes

### minikube

Minikube是一种轻量化的Kubernetes集群，是Kubernetes社区为了帮助开发者和学习者能够更好学习和体验k8s功能而推出的，借助个人PC的虚拟化环境就可以实现Kubernetes的快速构建启动。目前已支持在macOS、Linux、Windows平台上利用各类本地虚拟化环境作为驱动运行。

> <https://minikube.sigs.k8s.io/docs/>  

#### 环境要求：

* 2 CPUs or more
* 2GB of free memory
* 20GB of free disk space
* Internet connection
* Container or virtual machine manager, such as: Docker, Hyperkit, Hyper-V, KVM, Parallels, Podman, VirtualBox, or VMware Fusion/Workstation


1. 安装`minikube`

```sh
#获取安装包
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
# 选用docker安装模式
sudo usermod -aG docker $USER && newgrp docker
minikube start --driver=docker
# 验证是否安装成功，所有pod的STATUS应为Running
minikube kubectl -- get po -A
```

> 安装流程可能需要访问404网站，掌握访问404网站和相关规避手段是学习Kubernetes的必备技能。(提示：minikube start --help | grep mirror)

2. 安装`kubectl`

```sh
# 根据实际安装的版本号替换v1.24.0
curl -LO https://dl.k8s.io/release/v1.24.0/bin/linux/amd64/kubectl
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
kubectl get pod -A
```

*思考：*
- *安装kubectl后，在并未配置Kubernetes集群信息的情况下，kubectl是如何集群的信息并与api-server成功通信？（提示：查看~/.kube目录）*
- *minikube支持dashboard命令，如何从外部（虚拟机外部）访问dashboard url？*

2. 部署`hello-world` 

```yaml
# hello-world.yaml文件内容如下：
apiVersion: v1
kind: Service
metadata:
  name: helloworld
  labels:
    app: helloworld
spec:
  ports:
  - name: http
    port: 8000
    targetPort: 8080
    nodePort: 32123
  selector:
    app: helloworld
  type: LoadBalancer
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: helloworld
  labels:
    app: helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: helloworld
  template:
    metadata:
      labels:
        app: helloworld
    spec:
      containers:
      - name: helloworld
        image: [ID]/hello-world-golang:[tag]
        imagePullPolicy: IfNotPresent
        args:
        # Address for the app to listen on
        ports:
        - containerPort: 8080
```

```sh
kubectl apply -f hello-world.yaml
```

> 提示：请替换yaml文件中image字段为Docker小结中的容器  

*思考：*
- *hello-world.yaml部署了哪些资源？作用分别是什么？*
- *上述文件中共部署了几个hello-world pod，如何在不删除资源的情况增加hello-world pod数量？*
- *Service中包含了port、targetPort、nodePort，分别定义什么？含义的是什么？*

4. 验证hello-world

```
$ minikube service --all
😿  service default/kubernetes has no node port
|-----------|------------|--------------|---------------------------|
| NAMESPACE |    NAME    | TARGET PORT  |            URL            |
|-----------|------------|--------------|---------------------------|
| default   | helloworld | http/8000    | http://192.168.49.2:32123 |
| default   | kubernetes | No node port |
|-----------|------------|--------------|---------------------------|
# 根据查询结果更新URL
$ curl http://192.168.49.2:32123
Hello Docker!
```

*思考：是否有其他方式可以访问hello-world服务？他们的区别是什么？（minikube tunnel、kubectl port-forward等不少于3种）*

5. 使能metrics server

对比使能`metrics-server`前后`kubectl top node`返回的结果

```
$ kubectl top node
$ minikube addons enable metrics-server
$ kubectl top node
```

> 提示：metrics-server对应的镜像为k8s.gcr.io/metrics-server/metrics-server:v0.4.2@sha256:[Hash-id]，如何解决该问题？(--images)  

*思考：*
- *熟悉使用kubectl（--watch效果是什么？），参考：<https://kubernetes.io/zh/docs/reference/kubectl/overview/>*
- *熟悉使用minikube，参考：<https://minikube.sigs.k8s.io/docs/handbook/controls/>*
- *如何通过进入运行minikube的容器？有几种方式？*

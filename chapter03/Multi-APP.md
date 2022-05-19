# 多应用服务

在`Kubernets`章节中介绍了如何构建单应用服务`hello-world`，对更加复杂服务需要多种应用协同工作（如：网站的前端和后台）该如何处理？本节将介绍如何使用Docker和Kubernetes分别构建多应用服务。

## 多应用服务
对应相对复杂服务，通常会将服务拆分为功能独立的组件，将每个组件放在单独的容器向外提供服务，这样不仅解决了单体式应用的诸多不足，也使得应用的开发、部署、测试和服务化更加方便。
> 详细介绍请自行了解：微服务 。

本节使用的多应用服务是Todolist应用，分为前端界面(Todo App)和后端存储(MySQL)，架构图下图。前端是node.js实现的静态页面，通过该页面可以管理Todolist的内容，并将结果保存在后端MySQL数据库中。

![multi-all](../pic/03/multi-app-architecture.png)

## Docker部署Todo-App

### 构建Todo-App
1. 获取前端源码
```shell
$ git clone https://github.com/docker/getting-started.git
```

2. 构建应用镜像
```shell
$ cd $GETTING_STARTED_DIR/app
$ docker build -t [ID]/todo-app:v1 .
```
Dockerfile
```
# syntax=docker/dockerfile:1
FROM node:12-alpine
RUN apk add --no-cache python2 g++ make
WORKDIR /app
COPY . .
RUN yarn install --production
CMD ["node", "src/index.js"]
EXPOSE 3000
```
> 注意：请自行上传容器

3. 验证应用容器
```shell
$  docker run -dp 3000:3000 [ID]/todos:v1
```
随后访问http://[IP]:3000，观察到如下页面表示应用容器构建成功
![](../pic/03/todo-list-empty.png)
> 注意：请执行删除相关容器，以免导致端口冲突

### 运行Start MySQL
1. 创建应用网络
```shell
$  docker network create todo-app
```

2. 启动一个MYSQL容器，将该容器连接到上一步创建的`network`上面。
```shell
docker run -d \
    --network todo-app --network-alias mysql \
    -v todo-mysql-data:/var/lib/mysql \
    -e MYSQL_ROOT_PASSWORD=secret \
    -e MYSQL_DATABASE=todos \
    mysql:5.7
```

3. 确认`mysql`容器是否正常运行
```shell
docker exec -it <mysql-container-id> mysql -p
```
连接到`mysql`之后运行
```
mysql> SHOW DATABASES;
```
可以看到`todos`库：
```
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| todos              |
+--------------------+
5 rows in set (0.00 sec)
```

### 运行Todo-App
1. 启动Todo-App，接入MySQL
```shell
docker run -dp 3000:3000 \
   -w /app  \
   --network todo-app \
   -e MYSQL_HOST=mysql \
   -e MYSQL_USER=root \
   -e MYSQL_PASSWORD=secret \
   -e MYSQL_DB=todos \
   [ID]/Todos:v1
```

2. 用命令 docker logs -f <container-id> 查看日志，如果你看到以下内容代表启动成功：
```
Waiting for mysql:3306.
Connected!
Connected to mysql db at host mysql
Listening on port 3000
```

3. 访问http://[IP]:3000， 在页面中增加todo元素

4. 验证Todo-App服务是否部署成功
运行命令连接到mysql
```shell
docker exec -it <mysql-container-id> mysql -p todos
```
运行如下命令查看是否有新增的数据：
```
mysql> select * from todo_items;
```
确认新添加的todo元素是否在数据库中。

> 思考：每个容器单独部署非常麻烦，是否有更简便的方案可以同时部署两个应用？


## Kubernetes部署Todo-App
1. 创建namespace
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: myapp
```
### 部署MySQL服务
1. 创建持久卷(pv)  
```yaml
# mysql-pv.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  namespace: myapp
  name: mysql-pv
  labels:
    type: local
spec:
  storageClassName: manual
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"
```
这个 yaml 文件一旦在 kubernetes 中应用，将为MySQL数据库服务Pod提供一个Persistent Volume。PV不依赖于pod的生命周期。这意味着任何时候pod由于崩溃或故障而重新启动，配置的存储都将继续存在。

2. 创建Persistent Volume Claim(PVC)
```yaml
# mysql-pvc.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  namespace: myapp
  name: mysql-pv-claim
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

3. 部署MySQL服务
```yaml
# mysql-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  namespace: myapp
  name: mysql
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
        - image: mysql:5.7
          name: mysql
          env:
            - name: MYSQL_ROOT_PASSWORD
              value: secret
            - name: MYSQL_DATABASE
              value: todos
          ports:
            - containerPort: 3306
              name: mysql
          volumeMounts:
            - name: mysql-persistent-storage
              mountPath: /var/lib/mysql
      volumes:
        - name: mysql-persistent-storage
          persistentVolumeClaim:
            claimName: mysql-pv-claim
```

```yaml
# mysql-service.yaml
apiVersion: v1
kind: Service
metadata:
  namespace: myapp
  name: mysql
spec:
  ports:
    - port: 3306
  selector:
    app: mysql
  clusterIP: None
```

### 部署Todo-App服务
创建Todo-App的deployment和service
```yaml
# todos-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  namespace: myapp
  name: todos
  labels:
    app: todos
spec:
  selector:
    matchLabels:
      app: todos
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: todos
    spec:
      containers:
      - image: changzihao/todos:v1
        name: todos
        env:
        - name: MYSQL_HOST
          value: mysql
        - name: MYSQL_USER
          value: root
        - name: MYSQL_PASSWORD
          value: secret
        - name: MYSQL_DB
          value: todos
        ports:
        - containerPort: 3000
          name: todos
```

```yaml
# todos-service.yaml
apiVersion: v1
kind: Service
metadata:
  namespace: myapp
  name: todos
  labels:
    app: todos
spec:
  ports:
    - port: 3000
      targetPort: 3000
      nodePort: 32000
  selector:
    app: todos
  type: LoadBalancer
```

### 验证应用是否部署成功
1. 查看所有容器的状态是否为`Running`
```shell
$ kubectl get pod -n myapp
NAME                     READY   STATUS    RESTARTS   AGE
mysql-849b85c5f8-svgth   1/1     Running   0          3h46m
todos-7b7f4896d8-676js   1/1     Running   0          167m
```
2. `kubectl logs`查看日志
```shell
$ kubectl logs pod/todos-[ID] -n myapp
Waiting for mysql:3306.
Connected!
Connected to mysql db at host mysql
Listening on port 3000
```

3. 验证服务是否部署成功
请根据Docker部署中验证步骤和以下提示验证服务是否部署成功
* kubectl port-forward，转发服务端口便于在minikube外部访问Todo-App的web页面
* kubectl exec，进行pod内部访问对应的应用

> 提示：
> 1. 本章节涉及很多Docker和Kubernetes新内容，请读者执行学习了解。

> 思考：  
> 1. 以上两种模式中，网络是如何管理？ 不同应用间是如何通信的？
> 2. 请对比两种部署模式的异同
> 3. 那种部署模式更有优势？为什么？
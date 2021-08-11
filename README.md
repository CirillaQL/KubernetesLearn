# Kubernetes学习

## 1. Kubernetes介绍及理解

### 1.1 Kubernetes的功能作用

当今时代，大型单体应用正在被逐渐分解成小的、可独立运行的组件，我们称之为微服务。微服务彼此之间解耦，所以它们可以被独立开发、部署、升级、伸缩。这使得我们可以对每一个微服务实现快速迭代，并且迭代的速度可以和市场需求变化的速度一致。

但是随着部署组件的增多和数据中心的增长，配置、管理并保持系统的正常运行变得越来越困难。如果我们想要获得足够高的资源利用率并降低硬件成本，把组件部署在什么地方变得越来越难以决策。手动做所有的事情，显然不太可行。因此我们需要包括自动调度、配置、监管和故障处理的自动化措施，这正是Kubernetes的用武之地。

**Kubernetes使开发者可以自主部署应用，并且控制部署的频率，完全脱离运维团队的帮助。**Kubernetes同时能让运维团队监控整个系统，并且在硬件故障时重新调度应用。系统管理员的工作中心，从监管应用转移到了监管Kubernetes，以及剩余的系统资源，因为Kubernetes会帮助监管所有的应用。

**Kubernetes抽象了数据中心的硬件基础设施，使得对外暴露的只是一个巨大的资源池。它让我们在部署和运行组件时，不用关注底层的服务器。**

### 1.2 容器

**Kubernetes使用Linux容器技术来提供应用的隔离，所以在钻研Kubernetes之前，需要通过熟悉容器的基本知识来深入理解Kubernetes。**目前开发者不是使用虚拟机来隔离每个微服务环境，而是正在转向Linux容器技术。容器允许你在同一台机器上运行多个服务，不仅提供不同的环境给每个服务，而且将他们互相隔离。容器类似于虚拟机，但开销小很多。一个容器里运行的进程实际上运行在宿主机的操作系统上，就像所有其他进程一样（不同于虚拟机，进程是运行在不同的操作系统上的）。但在容器里的进程仍然是和其他进程隔离的。对于容器内进程本身而言，就好像是在机器和操作系统上运行的唯一一个进程。

容器技术出现很久了，却是随着Docker容器平台的出现而变得广为人知。Docker是第一个使容器能在不同机器之间移植的系统。它不仅简化了打包应用的流程，也简化了打包应用的库和依赖，甚至整个操作系统的文件系统能被打包成一个简单的可移植的包，这个包可以被用在任何支持Docker的机器上。

### 1.3 Kubernetes正式介绍

**Kubernetes是一个软件系统，它允许你在其上很容易地部署和管理容器化的应用。**它依赖于Linux的容器特性来运行异构应用，而无须知道这些应用的内部详情，也不需要手动将这些应用部署到每台机器。因为这些应用运行在容器里，它们不会影响运行在同一台服务器上的其他应用。**Kubernetes使你在数以千计的电脑节点上运行软件时就像所有这些节点是单个大节点一样。它将底层基础设施抽象，这样做同时简化了应用的开发、部署，以及对开发和运维团队的管理。**

#### 1.3.1 Kubernetes集群架构

在硬件级别，一个Kubernetes集群由很多节点组成，这些节点被分成以下两种类型：

- 主节点，它承载着Kubernetes控制和管理整个集群系统的控制面板。
- 工作节点，它们运行用户实际部署的应用。

#### 1.3.2 控制面板

控制面板用于控制集群并使它工作。它包含多个组件，组件可以运行在单个主节点上或者通过副本分别部署在多个主节点以确保高可用性。这些组件是：

- Kubernetes API服务器，和其他面板组件需要靠它通信。
- Scheculer， 它负责调度应用。
- Controller Manager， 它执行集群级别的功能，如复制组件、持续跟踪工作节点、处理节点失败等。
- etcd，一个可靠的分布式数据存储，能持久化存储集群配置

控制面板的组件持有并控制集群状态，但是它们并不运行你的应用程序，应用程序部分由工作节点完成。

#### 1.3.3 工作节点

工作节点是运行容器化应用的机器。运行、监控和管理应用服务的任务是通过以下组件完成的：

- Docker、rtk或其他的容器类型。
- kubelet，它与API服务器通信，并管理它所在节点的容器。
- Kubernetes Service Proxy(kube-proxy)，它负责组件之间的负载均衡网络流量。

### 1.4 在Kubernetes中运行应用

**为了在Kubernetes中运行应用，首先需要将应用打包进一个或多个容器镜像，再将那些镜像推送到镜像仓库，然后将应用的描述发布到Kubernetes API服务器。**

## 2. 使用Kubernetes和Docker

### 2.1 Docker的基本使用

Docker的命令格式为docker <命令>，比如docker run、docker push。

[Docker基本命令详解](https://www.runoob.com/docker/docker-command-manual.html)

### 2.2 Dockerfile详解

Dockerfile是一个包含用于组合镜像的命令的文本文档。可以在命令行中调用任何命令。Docker通过读取Dockerfile中的指令自动生成映像。

docker build命令用于从Dockerfile构建镜像。可以在docker build命令中使用 -f 标志指向文件系统中任何位置的Dockerfile

Dockerfile详细教程：[Dockerfile教程](https://blog.csdn.net/atlansi/article/details/87892016)

### 2.3 配置Kubernetes集群

此时应用被打包在一个容器镜像中，并通过Docker Hub供大家使用，可以将它部署到Kubernetes集群中，而不是在Docker中运行。

#### 2.3.1 Kubernetes集群是什么

k8s 集群是一组运行容器化应用程序的节点计算机。如果你在运行 Kubernetes，实际上就是在运行集群 。

集群至少包含一个控制平面，以及一个或多个计算机器或节点。控制平面负责维护集群的理想状态，例如运行哪个应用以及使用哪个容器镜像。节点则负责应用和工作负载的实际运行。

[Kubernetes](https://www.redhat.com/zh/topics/containers/what-is-kubernetes) 的核心优势之一就是集群，无论是物理机还是虚拟机，K8s 集群可以使其在本地或云中跨机调度和运行[容器](https://www.redhat.com/zh/topics/containers/whats-a-linux-container)从而让 Kubernetes 容器不再受单台计算机的束缚，而是跨整个集群进行抽象。

k8s 集群具备所需的理想状态 ，定义了应该运行的应用程序或其他工作负载、应该使用的镜像、提供的资源 ，以及其他的配置细节。

理想状态是由配置文件定义的，而后者 JSON 或 YAML 等清单文件组成，这些文件用于声明要运行的应用类型以及运行一个正常系统所需的副本数。

#### 2.3.2 Kubernetes集群的配置

配置Kubernetes时需要安装相应的软件包：

- Kubeadm: 用来初始化集群的指令。
- Kubelet: 在集群中的每个节点上用来启动Pod和容器等。
- Kubectl: 用来与集群通信的命令行工具

在第一台Master上执行初始化，执行初始化使用Kubeadm init命令。初始化首先会执行一系列的运行前检查来确保机器满足运行Kubernetes的条件，这些检查会抛出警告并在发现错误的时候终止整个初始化进程。 然后 kubeadm init 会下载并安装集群的 Control-plane 组件。

在初始化后，集群必须安装Pod网络插件，以使Pod可以相互通信，只需要在Master节点操作，其他新加入的节点会自动创建相关Pod。

详细操作见链接：[Kubernetes集群搭建超详细总结](https://segmentfault.com/a/1190000040107263)

​								[手把手从零搭建与运营生产级的Kubernetes集群](https://www.kubernetes.org.cn/7315.html)

### 2.4 在Kubernetes上运行第一个应用

部署应用程序最简单的方式是使用Kubectl run命令，该命令可以创建所有必要的组件而无需JSON或YAML文件。

```shell
kubectl run kubia  --image=luksa/kubia --port=8080 --generator=run/v1
```

而在通常情况下，构建Kubernetes的应用往往通过编写YAML文件，以下是构建一个gin项目容器的YAML文件。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: gindemo
  namespace: frankcox
spec:
  replicas: 4
  selector:
    matchLabels:
      app: gin-app
  template:
    metadata:
      labels:
        app: gin-app
    spec:
      containers:
        - name: gintestdemo
          image: frankcox/gintestdemo:v1
          resources:
            limits:
              memory: "128Mi"
              cpu: "500m"
          ports:
            - containerPort: 8080
```

在使用YAML文件的时候，使用Kubectl create -f *.yaml来构建对应的资源。

## 3. Pod

### 3.1 概念

Pod是Kubernetes中能够创建和部署的最小单元，是Kubernetes集群中的一个应用实例，总是部署在同一个节点Node上。Pod中包含了一个或多个容器，还包括了存储、网络等各个容器共享的资源。Pod支持多种容器环境，Docker则是最流行的容器环境。根据容器数目，Pod可以分为两种：

- 单容器Pod——常见的应用方式
- 多容器Pod——对于多容器PodKubernetes会保证所有的容器都在同一台物理主机或虚拟主机中运行。

Pod好处：

- Pod做为一个可以独立运行的服务单元，简化了应用部署的难度，以更高的抽象层次为应用部署管提供了极大的方便。
- Pod做为最小的应用实例可以独立运行，因此可以方便的进行部署、水平扩展和收缩、方便进行调度管理与资源的分配。
- Pod中的容器共享相同的数据和网络地址空间，Pod之间也进行了统一的资源管理与分配。

### 3.2 Pod生命周期

像单独的容器应用一样，Pod并不是持久运行的。Pod创建后，Kubernetes为其分配一个UID，并且通过Controller调度到Node中运行，然后Pod一直保持运行状态直到运行正常结束或者被删除。在Node发生故障时，Controller负责将其调度到其他的Node中。Kubernetes为Pod定义了几种状态，分别如下：

- Pending，Pod已创建，正在等待容器创建。经常是正在下载镜像，因为这一步骤最耗费时间
- Running，Pod已经绑定到某个Node并且正在运行。或者可能正在进行意外中断后的重启。
- Succeeded，表示Pod中的容器已经正常结束并且不需要重启。
- Failed，表示Pod中的容器遇到了错误而终止。
- Unknown，因为网络或其他原因，无法获取Pod的状态。

### 3.3 Pod创建流程

（1）客户端提交创建请求，可以通过API Server的Restful API，也可以使用kubectl命令行工具。支持的数据类型包括JSON和YAML。

（2）API Server处理用户请求，存储Pod数据到etcd。

（3）调度器通过API Server查看未绑定的Pod。尝试为Pod分配主机。

（4）过滤主机 (调度预选)：调度器用一组规则过滤掉不符合要求的主机。比如Pod指定了所需要的资源量，那么可用资源比Pod需要的资源量少的主机会被过滤掉。

（5）主机打分(调度优选)：对第一步筛选出的符合要求的主机进行打分，在主机打分阶段，调度器会考虑一些整体优化策略，比如把容一个Replication Controller的副本分布到不同的主机上，使用最低负载的主机等。

（6）选择主机：选择打分最高的主机，进行binding操作，结果存储到etcd中。

（7）kubelet根据调度结果执行Pod创建操作： 绑定成功后，scheduler会调用APIServer的API在etcd中创建一个boundpod对象，描述在一个工作节点上绑定运行的所有pod信息。运行在每个工作节点上的kubelet也会定期与etcd同步boundpod信息，一旦发现应该在该工作节点上运行的boundpod对象没有更新，则调用Docker API创建并启动pod内的容器。

详情：[当创建Pod时到底发生了什么](https://github.com/jamiehannaford/what-happens-when-k8s/tree/master/zh-cn)

### 3.4 Label: 组织Pod的利器

#### 3.4.1 为什么需要Label

当资源变得非常多的时候，如何分类管理就非常重要了，Kubernetes提供了一种机制来为资源分类，那就是Label(标签)。Kubernetes中几乎所有资源都可以用Label来组织。Label的具体形式是key-value的标记对，可以在创建资源的时候设置，也可以在后期添加和修改。

没有分类的Pod

![没有分类组织的Pod](https://support.huaweicloud.com/basics-cce/zh-cn_image_0262265504.png)

如果为Pod打上不同标签，那么情况就不同了。

使用Label组织的Pod

![使用Label组织的Pod](https://support.huaweicloud.com/basics-cce/zh-cn_image_0262267029.png)

#### 3.4.2 添加Label

Label的形式为Key-Value形式，使用非常简单，如下，为Pod设置了app=nginx和env=prod两个Label。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:                     # 为Pod设置两个Label    
    app: nginx    
    env: prod
spec:
  containers:
  - image: nginx:alpine
    name: container-0
    resources:
      limits:
        cpu: 100m
        memory: 200Mi
      requests:
        cpu: 100m
        memory: 200Mi
  imagePullSecrets:
  - name: default-secret

```

### 3.5 Namespace

Label虽然好，但只用Label的话，那Label会非常多，有时候会有重叠，而且每次查询之类的动作都带一堆Label非常不方便。Kubernetes提供了Namespace来做资源组织和划分，使用多Namespace可以将包含很多组件的系统分成不同的组。Namespace也可以用来做多租户划分，这样多个团队可以共用一个集群，使用的资源用Namespace划分开。

不同的Namespace下面可以有相同的名字，Kubernetes中大部分资源可以用Namespace划分，不过有些资源不行，它们属于全局资源，不属于某一个Namespace，后面会逐步接触到。

## 4. Pod的编排与调度

### 4.1 Deployment

#### 4.1.1 什么是Deployment

Pod是Kubernetes创建或部署的最小单位，但是Pod是被设计为相对短暂的一次性实体，Pod可以被驱逐（当节点资源不足时）、随着集群的节点崩溃而消失。Kubernetes提供了Controller（控制器）来管理Pod，Controller可以创建和管理多个Pod，提供副本管理、滚动升级和自愈能力，其中最为常用的就是Deployment。

![Deployment](https://support.huaweicloud.com/basics-cce/zh-cn_image_0258095884.png)

一个Deployment可以包含一个或多个Pod副本，每个Pod副本的角色相同，所以系统会自动为Deployment的多个Pod副本分发请求。

Deployment集成了上线部署、滚动升级、创建副本、恢复上线的功能，在某种程度上，Deployment实现无人值守的上线，大大降低了上线过程的复杂性和操作风险。

#### 4.1.2 创建Deployment

以下为创建一个名为nginx的Deployment负载，使用nginx:latest镜像创建两个Pod，每个Pod占用100m core CPU、200Mi内存。

```yaml
apiVersion: apps/v1      # 注意这里与Pod的区别，Deployment是apps/v1而不是v1
kind: Deployment         # 资源类型为Deployment
metadata:
  name: nginx            # Deployment的名称
spec:
  replicas: 2            # Pod的数量，Deployment会确保一直有2个Pod运行         
  selector:              # Label Selector
    matchLabels:
      app: nginx
  template:              # Pod的定义，用于创建Pod，也称为Pod template
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx:latest
        name: container-0
        resources:
          limits:
            cpu: 100m
            memory: 200Mi
          requests:
            cpu: 100m
            memory: 200Mi
      imagePullSecrets:
      - name: default-secret
```

从这个定义中可以看到Deployment的名称为nginx，spec.replicas定义了Pod的数量，即这个Deployment控制2个Pod；spec.selector是Label Selector（标签选择器），表示这个Deployment会选择Label为app=nginx的Pod；spec.template是Pod的定义，内容与[Pod](https://support.huaweicloud.com/basics-cce/kubernetes_0006.html)中的定义完全一致。

将上面Deployment的定义保存到deployment.yaml文件中，使用kubectl创建这个Deployment。

使用kubectl get查看Deployment和Pod，可以看到**READY**值为2/2，前一个2表示当前有2个Pod运行，后一个2表示期望有2个Pod，**AVAILABLE**为2表示有2个Pod是可用的。

```shell
$ kubectl create -f deployment.yaml
deployment.apps/nginx created

$ kubectl get deploy
NAME           READY     UP-TO-DATE   AVAILABLE   AGE
nginx          2/2       2            2           4m5s
```

#### 4.1.3 Deployment控制Pod

```shell
$ kubectl get pods
NAME                     READY     STATUS    RESTARTS   AGE
nginx-7f98958cdf-tdmqk   1/1       Running   0          13s
nginx-7f98958cdf-txckx   1/1       Running   0          13s
```

如果删掉一个Pod，您会发现立马会有一个新的Pod被创建出来，如下所示，这就是前面所说的Deployment会确保有2个Pod在运行，如果删掉一个，Deployment会重新创建一个，如果某个Pod故障或有其他问题，Deployment会自动拉起这个Pod。

会发现这两个后缀中前面一部分是相同的，都是7f98958cdf，这是因为Deployment不是直接控制Pod的，Deployment是通过一种名为ReplicaSet的控制器控制Pod，通过如下命令可以查询ReplicaSet，其中rs是ReplicaSet的缩写。

Deployment控制Pod的方式如图所示，Deployment控制ReplicaSet，ReplicaSet控制Pod。

![Deployment通过ReplicaSet控制Pod](https://support.huaweicloud.com/basics-cce/zh-cn_image_0258097483.png)

在实际应用中，升级是一个常见的场景，Deployment能够很方便的支撑应用升级。

Deployment可以设置不同的升级策略，有如下两种。

- RollingUpdate：滚动升级，即逐步创建新Pod再删除旧Pod，为默认策略。
- Recreate：替换升级，即先把当前Pod删掉再重新创建Pod。

Deployment的升级可以是声明式的，也就是说只需要修改Deployment的YAML定义即可，比如使用kubectl edit命令将上面Deployment中的镜像修改为nginx:alpine。修改完成后再查询ReplicaSet和Pod，发现创建了一个新的ReplicaSet，Pod也重新创建了。

回滚也称为回退，即当发现升级出现问题时，让应用回到老的版本。Deployment可以非常方便的回滚到老版本。

例如上面升级的新版镜像有问题，可以执行kubectl rollout undo命令进行回滚。

Deployment之所以能如此容易的做到回滚，是因为Deployment是通过ReplicaSet控制Pod的，升级后之前ReplicaSet都一直存在，Deployment回滚做的就是使用之前的ReplicaSet再次把Pod创建出来。Deployment中保存ReplicaSet的数量可以使用revisionHistoryLimit参数限制，默认值为10。

### 4.2 StatefulSet

#### 4.2.1 为什么需要StatefulSet

在Deployment中讲到了Deployment，Deployment控制器下的Pod都有个共同特点，那就是每个Pod除了名称和IP地址不同，其余完全相同。需要的时候，Deployment可以通过Pod模板创建新的Pod；不需要的时候，Deployment就可以删除任意一个Pod。

但是在某些场景下，这并不满足需求，比如有些分布式的场景，要求每个Pod都有自己单独的状态时，比如分布式数据库，每个Pod要求有单独的存储，这时Deployment就不能满足需求了。

详细分析下有状态应用的需求，分布式有状态的特点主要是应用中每个部分的角色不同（即分工不同），比如数据库有主备，Pod之间有依赖，对应到Kubernetes中就是对Pod有如下要求：

- Pod能够被别的Pod找到，这就要求Pod有固定的标识。
- 每个Pod有单独存储，Pod被删除恢复后，读取的数据必须还是以前那份，否则状态就会不一致。

Kubernetes提供了StatefulSet来解决这个问题，其具体如下：

1. StatefulSet给每个Pod提供固定名称，Pod名称增加从0-N的固定后缀，Pod重新调度后Pod名称和HostName不变。
2. StatefulSet通过Headless Service给每个Pod提供固定的访问域名，Service的概念会在Service中详细介绍。
3. StatefulSet通过创建固定标识的PVC保证Pod重新调度后还是能访问到相同的持久化数据。

![](https://support.huaweicloud.com/basics-cce/zh-cn_image_0258203193.png)

#### 4.2.2 创建StatefulSet

Statefulset的YAML定义与其他对象基本相同，主要有两个差异点：

- serviceName指定了Statefulset使用哪个Headless Service，需要填写Headless Service的名称。
- volumeClaimTemplates是用来申请持久化声明PVC，这里定义了一个名为data的模板，它会为每个Pod创建一个PVC，storageClassName指定了持久化存储的类型，在PV、PVC、StorageClassName会详细介绍；volumeMounts是为Pod挂载存储。当然如果不需要存储的话可以删除volumeClaimTemplates和volumeMounts字段。

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: nginx
spec:
  serviceName: nginx                             # headless service的名称
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: container-0
          image: nginx:alpine
          resources:
            limits:
              cpu: 100m
              memory: 200Mi
            requests:
              cpu: 100m
              memory: 200Mi
          volumeMounts:                           # Pod挂载的存储
          - name:  data
            mountPath:  /usr/share/nginx/html     # 存储挂载到/usr/share/nginx/html
      imagePullSecrets:
        - name: default-secret
  volumeClaimTemplates:
  - metadata:
      name: data
    spec:
      accessModes:
      - ReadWriteMany
      resources:
        requests:
          storage: 1Gi
      storageClassName: csi-nas                   # 持久化存储的类型
```

#### 4.2.3 StatefulSet的网络标识

StatefulSet创建后，可以看下Pod是有固定名称的，那Headless Service是如何起作用的呢，那就是使用DNS，为Pod提供固定的域名，这样Pod间就可以使用域名访问，即便Pod被重新创建而导致Pod的IP地址发生变化，这个域名也不会发生变化。

### 4.3 Job和CronJob

#### 4.3.1 Job

Job是Kubernetes用来控制批处理型任务的资源对象。批处理业务与长期伺服业务（Deployment、Statefulset）的主要区别是批处理业务的运行有头有尾，而长期伺服业务在用户不停止的情况下永远运行。Job管理的Pod根据用户的设置把任务成功完成就自动退出（Pod自动删除）。

**创建Job**

以下是一个Job配置，其计算π到2000位并打印输出。Job结束需要运行50个Pod，这个示例中就是打印π 50次，并行运行5个Pod，Pod如果失败最多重试5次。

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: pi-with-timeout
spec:
  completions: 50            # 运行的次数，即Job结束需要成功运行的Pod个数
  parallelism: 5             # 并行运行Pod的数量，默认为1
  backoffLimit: 5            # 表示失败Pod的重试最大次数，超过这个次数不会继续重试。
  activeDeadlineSeconds: 10  # 表示Pod超期时间，一旦达到这个时间，Job及其所有的Pod都会停止。
  template:                  # Pod定义
    spec: 
      containers:
      - name: pi
        image: perl
        command:
        - perl
        - "-Mbignum=bpi"
        - "-wle"
        - print bpi(2000)
      restartPolicy: Never
```

根据completions和parallelism的设置，可以将Job划分为以下几种类型:

|        Job类型        |                     说明                     |        使用示例         |
| :-------------------: | :------------------------------------------: | :---------------------: |
|       一次性Job       |          创建一个Pod直至其成功结束           |       数据库迁移        |
|   固定结束次数的Job   | 依次创建一个Pod运行直至completions个成功结束 |    处理工作队列的Pod    |
| 固定结束次数的并行Job | 依次创建多个Pod运行直至completions个成功结束 | 多个Pod同时处理工作队列 |
|        并行Job        |     创建一个或多个Pod直至有一个成功结束      | 多个Pod同时处理工作队列 |

#### 4.3.2 CronJob

相比Job，CronJob就是一个加了定时的Job，CronJob执行时是在指定的时间创建出Job，然后由Job创建出Pod。

```yaml
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: cronjob-example
spec:
  schedule: "0,15,30,45 * * * *"           # 定时相关配置
  jobTemplate:                             # Job的定义
    spec:
      template:
        spec:
          restartPolicy: OnFailure
          containers:
          - name: main
            image: pi
```

### 4.4 DaemonSet

#### 4.4.1 DaemonSet介绍

DaemonSet是这样一种对象（守护进程），它在集群的每个节点上运行一个Pod，且保证只有一个Pod，这非常适合一些系统层面的应用，例如日志收集、资源监控等，这类应用需要每个节点都运行，且不需要太多实例，一个比较好的例子就是Kubernetes的kube-proxy。

DaemonSet跟节点相关，如果节点异常，也不会在其他节点重新创建。

![](https://support.huaweicloud.com/basics-cce/zh-cn_image_0258871213.png)

#### 4.4.2 DaemonSet实例

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nginx-daemonset
  labels:
    app: nginx-daemonset
spec:
  selector:
    matchLabels:
      app: nginx-daemonset
  template:
    metadata:
      labels:
        app: nginx-daemonset
    spec:
      nodeSelector:                 # 节点选择，当节点拥有daemon=need时才在节点上创建Pod
        daemon: need
      containers:
      - name: nginx-daemonset
        image: nginx:alpine
        resources:
          limits:
            cpu: 250m
            memory: 512Mi
          requests:
            cpu: 250m
            memory: 512Mi
      imagePullSecrets:
      - name: default-secret
```

这里可以看出没有Deployment或StatefulSet中的replicas参数，因为是每个节点固定一个。

Pod模板中有个nodeSelector，指定了只在有“daemon=need”的节点上才创建Pod，如下图所示，DaemonSet只在指定标签的节点上创建Pod。如果需要在每一个节点上创建Pod可以删除该标签。

## 5. 配置管理

### 5.1 ConfigMap

ConfigMap是一种用于存储应用所需配置信息的资源类型，用于保存配置数据的键值对，可以用来保存单个属性，也可以用来保存配置文件。

通过ConfigMap可以方便的做到配置解耦，使得不同环境有不同的配置。

#### 5.1.1 创建ConfigMap

下面示例创建了一个名为configmap-test的ConfigMap，ConfigMap的配置数据在data字段下定义。

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: configmap-test
data:                     # 配置数据
  property_1: Hello
  property_2: World
```

#### 5.1.2 在环境变量中引用ConfigMap

ConfigMap最为常见的使用方式就是在环境变量和Volume中引用。

例如下面例子中，引用了configmap-test的property_1，将其作为环境变量EXAMPLE_PROPERTY_1的值，这样容器启动后里面EXAMPLE_PROPERTY_1的值就是property_1的值，即“Hello”。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - image: nginx:alpine
    name: container-0
    resources:
      limits:
        cpu: 100m
        memory: 200Mi
      requests:
        cpu: 100m
        memory: 200Mi
    env:
    - name: EXAMPLE_PROPERTY_1
      valueFrom:
        configMapKeyRef:          # 引用ConfigMap
          name: configmap-test
          key: property_1
  imagePullSecrets:
  - name: default-secret
```

#### 5.1.3 在Volume中引用ConfigMap

在Volume中引用ConfigMap，就是通过文件的方式直接将ConfigMap的每条数据填入Volume，每条数据是一个文件，键就是文件名，键值就是文件内容。

如下示例中，创建一个名为vol-configmap的Volume，这个Volume引用名为**“configmap-test”**的ConfigMap，再将Volume挂载到容器的“/tmp”路径下。Pod创建成功后，在容器的“/tmp”路径下，就有两个文件property_1和property_2，它们的值分别为“Hello”和“World”。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - image: nginx:alpine
    name: container-0
    resources:
      limits:
        cpu: 100m
        memory: 200Mi
      requests:
        cpu: 100m
        memory: 200Mi
    volumeMounts:
    - name: vol-configmap           # 挂载名为vol-configmap的Volume
      mountPath: "/tmp"
  imagePullSecrets:
  - name: default-secret
  volumes:
  - name: vol-configmap
    configMap:                      # 引用ConfigMap
      name: configmap-test
```

### 5.2 Secret

Secret是一种加密存储的资源对象，您可以将认证信息、证书、私钥等保存在Secret中，而不需要把这些敏感数据暴露到镜像或者Pod定义中，从而更加安全和灵活。

Secret与ConfigMap非常像，都是key-value键值对形式，使用方式也相同，不同的是Secret会加密存储，所以适用于存储敏感信息。

#### 5.2.1 创建Secret

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
data:
  key1: aGVsbG8gd29ybGQ=   # "hello world" Base64编码后的值
  key2: MzMwNg==           # "3306" Base64编码后的值
```

#### 5.2.2 在环境变量中引用Secret

Secret最常见的用法是作为环境变量注入到容器中，如下示例。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - image: nginx:alpine
    name: container-0
    resources:
      limits:
        cpu: 100m
        memory: 200Mi
      requests:
        cpu: 100m
        memory: 200Mi
    env:
    - name: key
      valueFrom:
        secretKeyRef:
          name: mysecret
          key: key1
  imagePullSecrets:
  - name: default-secret
```

#### 5.2.3 在Volume中引用Secret

在Volume中引用Secret，就是通过文件的方式直接将Secret的每条数据填入Volume，每条数据是一个文件，键就是文件名，键值就是文件内容。

如下示例中，创建一个名为vol-secret的Volume，这个Volume引用名为**“mysecret”**的Secret，再将Volume挂载到容器的“/tmp”路径下。Pod创建成功后，在容器的“/tmp”路径下，就有两个文件key1和key2。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - image: nginx:alpine
    name: container-0
    resources:
      limits:
        cpu: 100m
        memory: 200Mi
      requests:
        cpu: 100m
        memory: 200Mi
    volumeMounts:
    - name: vol-secret              # 挂载名为vol-secret的Volume
      mountPath: "/tmp"
  imagePullSecrets:
  - name: default-secret
  volumes:
  - name: vol-secret
    secret:                         # 引用Secret
      secretName: mysecret
```

进入Pod容器中，可以在/tmp目录下发现key1和key2两个文件，并看到文件中的值是base64解码后的值，分别为“hello world”和“3306”。

## 6. Kubernetes网络

### 6.1 容器网络

Kubernetes本身并不负责网络通信，Kubernetes提供了容器网络接口CNI（Container Network Interface），具体的网络通信交给CNI插件来负责，开源的CNI插件非常多，像Flannel、Calico等。

Kubernetes虽然不负责网络，但要求集群中的Pod能够互相通信，且Pod必须通过非NAT网络连接，即收到的数据包的源IP就是发送数据包Pod的IP。同时Pod与节点之间的通信也是通过非NAT网络。但是Pod访问集群外部时源IP会被修改成节点的IP。

Pod内部是通过虚拟Ethernet接口对（Veth pair）与Pod外部连接，Veth pair就像一根网线，一端留在Pod内部，一端在Pod之外。而同一个节点上的Pod通过网桥（Linux Bridge）通信，如下图所示。

![](https://support.huaweicloud.com/basics-cce/zh-cn_image_0264670294.png)

同一个节点中的Pod

不同节点间的网桥连接有很多种方式，这跟具体实现相关。但集群要求Pod的地址唯一，所以跨节点的网桥通常使用不同的地址段，以防止Pod的IP地址重复。

![](https://support.huaweicloud.com/basics-cce/zh-cn_image_0264671475.png)

不同节点上的Pod通信

### 6.2 Service

#### 6.2.1 直接访问Pod的问题

Pod创建完成后，如何访问Pod呢？直接访问Pod会有如下几个问题：

- Pod会随时被Deployment这样的控制器删除重建，那访问Pod的结果就会变得不可预知。
- Pod的IP地址是在Pod启动后才被分配，在启动前并不知道Pod的IP地址。
- 应用往往都是由多个运行相同镜像的一组Pod组成，逐个访问Pod也变得不现实。

举个例子，假设有这样一个应用程序，使用Deployment创建了前台和后台，前台会调用后台做一些计算处理，如图所示。后台运行了3个Pod，这些Pod是相互独立且可被替换的，当Pod出现状况被重建时，新建的Pod的IP地址是新IP，前台的Pod无法直接感知。

![](https://support.huaweicloud.com/basics-cce/zh-cn_image_0258894622.png)

Pod间访问

#### 6.2.2 使用Service解决Pod的访问问题

Kubernetes中的Service对象就是用来解决上述Pod访问问题的。Service有一个固定IP地址（在创建CCE集群时有一个服务网段的设置，这个网段专门用于给Service分配IP地址），Service将访问它的流量转发给Pod，具体转发给哪些Pod通过Label来选择，而且Service可以给这些Pod做负载均衡。

那么对于上面的例子，为后台添加一个Service，通过Service来访问Pod，这样前台Pod就无需感知后台Pod的变化，如图所示。

![](https://support.huaweicloud.com/basics-cce/zh-cn_image_0258889981.png)

#### 6.2.3 创建后台Pod

首先创建一个3副本的Deployment，即3个Pod，且Pod上带有标签“app: nginx”，具体如下所示。

```yaml
apiVersion: apps/v1      
kind: Deployment         
metadata:
  name: nginx            
spec:
  replicas: 3                    
  selector:              
    matchLabels:
      app: nginx
  template:              
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx:latest
        name: container-0
        resources:
          limits:
            cpu: 100m
            memory: 200Mi
          requests:
            cpu: 100m
            memory: 200Mi
      imagePullSecrets:
      - name: default-secret
```

#### 6.2.4 创建Service

下面示例创建一个名为“nginx”的Service，通过selector选择到标签“app:nginx”的Pod，目标Pod的端口为80，Service对外暴露的端口为8080。

访问服务只需要通过“服务名称:对外暴露的端口”接口，对应本例即“nginx:8080”。这样，在其他Pod中，只需要通过“nginx:8080”就可以访问到“nginx”关联的Pod。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx        # Service的名称
spec:
  selector:          # Label Selector，选择包含app=nginx标签的Pod
    app: nginx
  ports:
  - name: service0
    targetPort: 80   # Pod的端口
    port: 8080       # Service对外暴露的端口
    protocol: TCP    # 转发协议类型，支持TCP和UDP
  type: ClusterIP    # Service的类型
```

将上面Service的定义保存到nginx-svc.yaml文件中，使用kubectl创建这个Service。

```shell
$ kubectl create -f nginx-svc.yaml
service/nginx created

$ kubectl get svc
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
kubernetes   ClusterIP   10.247.0.1       <none>        443/TCP    7h19m
nginx        ClusterIP   10.247.124.252   <none>        8080/TCP   5h48m
```

您可以看到Service有个Cluster IP，这个IP是固定不变的，除非Service被删除，所以您也可以使用ClusterIP在集群内部访问Service。

下面创建一个Pod并进入容器，使用ClusterIP访问Pod，可以看到能直接返回内容。

```shell
$ kubectl run -i --tty --image nginx:alpine test --rm /bin/sh
If you don't see a command prompt, try pressing enter.
/ # curl 10.247.124.252:8080
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
...
```

#### 6.2.5 使用ServiceName访问Service

通过DNS进行域名解析后，可以使用**“ServiceName:Port”**访问Service，这也是Kubernetes中最常用的一种使用方式。在创建CCE集群的时候，会默认要求安装CoreDNS插件，在kube-system命名空间下可以查看到CoreDNS的Pod。

```shell
$ kubectl get po --namespace=kube-system
NAME                                      READY   STATUS    RESTARTS   AGE
coredns-7689f8bdf-295rk                   1/1     Running   0          9m11s
coredns-7689f8bdf-h7n68                   1/1     Running   0          11m
```

#### 6.2.6 Service是如何做到服务发现的

无论Pod如何变化，Service都能发现到Pod。而Kubernetes正是靠EndPoints监控到Pod的IP，从而让Service能够发现Pod。

```shell
$ kubectl get endpoints
NAME         ENDPOINTS                                     AGE
kubernetes   192.168.0.127:5444                            7h19m
nginx        172.16.2.132:80,172.16.3.6:80,172.16.3.7:80   5h48m

```

如果删除一个Pod，Deployment会将Pod重建，新的Pod IP会发生变化。再次查看Endpoints，会发现Endpoints的内容随着Pod发生了变化。

在[Kubernetes集群架构](https://support.huaweicloud.com/basics-cce/kubernetes_0003.html#kubernetes_0003__section94531832152913)中介绍过Node节点上的kube-proxy，实际上Service相关的事情都由节点上的kube-proxy处理。在Service创建时Kubernetes会分配IP给Service，同时通过API Server通知所有kube-proxy有新Service创建了，kube-proxy收到通知后通过iptables记录Service和IP/端口对的关系，从而让Service在节点上可以被查询到。

下图是一个实际访问Service的图示，Pod X访问Service（10.247.124.252:8080），在往外发数据包时，在节点上根据iptables规则目的IP:Port被随机替换为Pod1的IP:Port，从而通过Service访问到实际的Pod。

![](https://support.huaweicloud.com/basics-cce/zh-cn_image_0264605050.png)

除了记录Service和IP/端口对的关系，kube-proxy还会监控Service和Endpoint的变化，从而保证Pod重建后仍然能通过Service访问到Pod。

#### 6.2.7 Service的类型与使用场景

Service的类型除了ClusterIP还有NodePort、LoadBalancer和None，这几种类型的Service有着不同的用途。

- ClusterIP：用于在集群内部互相访问的场景，通过ClusterIP访问Service。
- NodePort：用于从集群外部访问的场景，通过节点上的端口访问Service，详细介绍请参见[NodePort类型的Service](https://support.huaweicloud.com/basics-cce/kubernetes_0024.html#kubernetes_0024__section1175215413159)。
- LoadBalancer：用于从集群外部访问的场景，其实是NodePort的扩展，通过一个特定的LoadBalancer访问Service，这个LoadBalancer将请求转发到节点的NodePort，而外部只需要访问LoadBalancer，详细介绍请参见[LoadBalancer类型的Service](https://support.huaweicloud.com/basics-cce/kubernetes_0024.html#kubernetes_0024__section7151144411279)。
- None：用于Pod间的互相发现，这种类型的Service又叫Headless Service，详细介绍请参见[Headless Service](https://support.huaweicloud.com/basics-cce/kubernetes_0024.html#kubernetes_0024__section10301171915541)。

#### 6.2.8 NodePort类型的Service

NodePort类型的Service可以让Kubemetes集群每个节点上保留一个相同的端口， 外部访问连接首先访问节点IP:Port，然后将这些连接转发给服务对应的Pod。如下图所示。

![](https://support.huaweicloud.com/basics-cce/zh-cn_image_0264667450.png)

#### 6.2.9 LoadBalancer类型的Service

LoadBalancer类型的Service其实是NodePort类型Service的扩展，通过一个特定的LoadBalancer访问Service，这个LoadBalancer将请求转发到节点的NodePort。

LoadBalancer本身不是属于Kubernetes的组件，这部分通常是由具体厂商（云服务提供商）提供，不同厂商的Kubernetes集群与LoadBalancer的对接实现各不相同。

![](https://support.huaweicloud.com/basics-cce/zh-cn_image_0264668224.png)

#### 6.2.10 Headless Service

上述解释了Service解决Pod的内外部访问问题，但是还有下面的问题没有解决。

- 同时访问所有Pod
- 一个Service内部的Pod互相访问

Headless Service正是解决这个问题的，Headless Service不会创建ClusterIP，并且查询会返回所有Pod的DNS记录，这样就可查询到所有Pod的IP地址。[StatefulSet](https://support.huaweicloud.com/basics-cce/kubernetes_0015.html)中StatefulSet正是使用Headless Service解决Pod间互相访问的问题。

```yaml
apiVersion: v1
kind: Service       # 对象类型为Service
metadata:
  name: nginx-headless
  labels:
    app: nginx
spec:
  ports:
    - name: nginx     # Pod间通信的端口名称
      port: 80        # Pod间通信的端口号
  selector:
    app: nginx        # 选择标签为app:nginx的Pod
  clusterIP: None     # 必须设置为None，表示Headless Service
```

### 6.3 Ingress

#### 6.3.1 Ingress的意义

Service是基于四层TCP和UDP协议转发的，而Ingress可以基于七层的HTTP和HTTPS协议转发，可以通过域名和路径做到更细粒度的划分。

![](https://support.huaweicloud.com/basics-cce/zh-cn_image_0258961458.png)

#### 6.3.2 Ingress工作机制

要想使用Ingress功能，必须在Kubernetes集群上安装Ingress Controller。Ingress Controller有很多种实现，最常见的就是Kubernetes官方维护的[NGINX Ingress Controller](https://github.com/kubernetes/ingress-nginx)；不同厂商通常有自己的实现，例如华为云CCE使用华为云弹性负载均衡服务ELB实现Ingress的七层负载均衡。

外部请求首先到达Ingress Controller，Ingress Controller根据Ingress的路由规则，查找到对应的Service，进而通过Endpoint查询到Pod的IP地址，然后将请求转发给Pod。

![](https://support.huaweicloud.com/basics-cce/zh-cn_image_0261720089.png)

### 6.4 就绪探针(Readiness Probe)

#### 6.4.1 就绪探针的功能

一个新Pod创建后，Service就能立即选择到它，并会把请求转发给Pod，那问题就来了，通常一个Pod启动是需要时间的，如果Pod还没准备好（可能需要时间来加载配置或数据，或者可能需要执行一个预热程序之类），这时把请求转给Pod的话，Pod也无法处理，造成请求失败。

Kubernetes解决这个问题的方法就是给Pod加一个业务就绪探针Readiness Probe，当检测到Pod就绪后才允许Service将请求转给Pod。

Readiness Probe同样是周期性的检测Pod，然后根据响应来判断Pod是否就绪，与[存活探针（Liveness Probe）](https://support.huaweicloud.com/basics-cce/kubernetes_0010.html)相同，就绪探针也支持如下三种类型。

- Exec：Probe执行容器中的命令并检查命令退出的状态码，如果状态码为0则说明已经就绪。
- HTTP GET：往容器的IP:Port发送HTTP GET请求，如果Probe收到2xx或3xx，说明已经就绪。
- TCP Socket：尝试与容器建立TCP连接，如果能建立连接说明已经就绪。

#### 6.4.2 Readiness Probe的工作原理

通过Endpoints就可以实现Readiness Probe的效果，当Pod还未就绪时，将Pod的IP:Port从Endpoints中删除，Pod就绪后再加入到Endpoints中。

![](https://support.huaweicloud.com/basics-cce/zh-cn_image_0259536449.png)

### 6.5 NetworkPolicy

NetworkPolicy是Kubernetes设计用来限制Pod访问的对象，通过设置NetworkPolicy策略，可以允许Pod被哪些地址访问（即入规则）、或Pod访问哪些地址（即出规则）。这相当于从应用的层面构建了一道防火墙，进一步保证了网络安全。

NetworkPolicy支持的能力取决于集群的网络插件的能力，如CCE的集群只支持设置Pod的入规则。

默认情况下，如果命名空间中不存在任何策略，则所有进出该命名空间中的Pod的流量都被允许。

NetworkPolicy的规则可以选择如下3种：

- namespaceSelector：根据命名空间的标签选择，具有该标签的命名空间都可以访问。
- podSelector：根据Pod的标签选择，具有该标签的Pod都可以访问。
- ipBlock：根据网络选择，网段内的IP地址都可以访问。（CCE当前不支持此种方式）

## 7. 持久化存储

### 7.1 Volume

容器中的文件在磁盘上是临时存放的，当容器重建时，容器中的文件将会丢失，另外当在一个Pod中同时运行多个容器时，常常需要在这些容器之间共享文件，这也是容器不好解决的问题。 Kubernetes抽象出了Volume来解决这两个问题，也就是存储卷，Kubernetes的Volume是Pod的一部分，Volume不是单独的对象，不能独立创建，只能在Pod中定义。

Pod中的所有容器都可以访问Volume，但必须要挂载，且可以挂载到容器中任何目录。

实际中使用容器存储如下图所示，将容器的内容挂载到Volume中，通过Volume两个容器间实现了存储共享。

![](https://support.huaweicloud.com/basics-cce/zh-cn_image_0259669841.png)

Volume的生命周期与挂载它的Pod相同，但是Volume里面的文件可能在Volume消失后仍然存在，这取决于Volume的类型。

#### 7.1.1 Volume的类型

Kubernetes的Volume有非常多的类型，在实际使用中使用最多的类型如下。

- emptyDir：一种简单的空目录，主要用于临时存储。
- hostPath：将主机某个目录挂载到容器中。
- ConfigMap、Secret：特殊类型，将Kubernetes特定的对象类型挂载到Pod。
- persistentVolumeClaim：Kubernetes的持久化存储类型。

#### 7.1.2 EmptyDir

EmptyDir是最简单的一种Volume类型，根据名字就能看出，这个Volume挂载后就是一个空目录，应用程序可以在里面读写文件，emptyDir Volume的生命周期与Pod相同，Pod删除后Volume的数据也同时删除掉。

emptyDir的一些用途：

- 缓存空间，例如基于磁盘的归并排序。
- 为耗时较长的计算任务提供检查点，以便任务能从崩溃前状态恢复执行。

emptyDir实际是将Volume的内容写在Pod所在节点的磁盘上，另外emptyDir也可以设置存储介质为内存，如下所示，medium设置为Memory。

#### 7.1.3 HostPath

HostPath是一种持久化存储，emptyDir里面的内容会随着Pod的删除而消失，但HostPath不会，如果对应的Pod删除，HostPath Volume里面的内容依然存在于节点的目录中，如果后续重新创建Pod并调度到同一个节点，挂载后依然可以读取到之前Pod写的内容。

HostPath存储的内容与节点相关，所以它不适合像数据库这类的应用，想象下如果数据库的Pod被调度到别的节点了，那读取的内容就完全不一样了。

记住永远不要使用HostPath存储跨Pod的数据，一定要把HostPath的使用范围限制在读取节点文件上，这是因为Pod被重建后不确定会调度哪个节点上，写文件可能会导致前后不一致。

### 7.2 PV、PVC和StorageClass

上一章节介绍的HostPath是一种持久化存储，但是HostPath的内容是存储在节点上，导致只适合读取。

如果要求Pod重新调度后仍然能使用之前读写过的数据，就只能使用网络存储了，网络存储种类非常多且有不同的使用方法，通常一个云服务提供商至少有块存储、文件存储、对象存储三种，如华为云的EVS、SFS和OBS。Kubernetes解决这个问题的方式是抽象了PV（PersistentVolume）和PVC（PersistentVolumeClaim）来解耦这个问题，从而让使用者不用关心具体的基础设施，当需要存储资源的时候，只要像CPU和内存一样，声明要多少即可。

- PV：PV描述的是持久化存储卷，主要定义的是一个持久化存储在宿主机上的目录，比如一个NFS的挂载目录。
- PVC：PVC描述的是Pod所希望使用的持久化存储的属性，比如，Volume存储的大小、可读写权限等等。

Kubernetes管理员设置好网络存储的类型，提供对应的PV描述符配置到Kubernetes，使用者需要存储的时候只需要创建PVC，然后在Pod中使用Volume关联PVC，即可让Pod使用到存储资源，它们之间的关系如下图所示。

![](https://support.huaweicloud.com/basics-cce/zh-cn_image_0261235726.png)

#### 7.2.1 CSI

Kubernetes提供了CSI接口（Container Storage Interface，容器存储接口），基于CSI这套接口，可以开发定制出CSI插件，从而支持特定的存储，达到解耦的目的。

#### 7.2.2 PV与PVC

PersistentVolume（**PV**）是集群中由管理员配置的**一段网络存储**。 它是集群中的资源，就像节点是集群资源一样。 PV是容量插件，如Volumes，但其生命周期独立于使用PV的任何单个pod。 此API对象捕获存储实现的详细信息，包括NFS，iSCSI或特定于云提供程序的存储系统。

　　PersistentVolumeClaim（**PVC**）是由**用户进行存储的请求**。 它类似于pod。 Pod消耗节点资源，PVC消耗PV资源。Pod可以请求特定级别的资源（CPU和内存）。声明可以请求特定的大小和访问模式（例如，可以一次读/写或多次只读）。

　　虽然PersistentVolumeClaims允许用户使用抽象存储资源，但是PersistentVolumes对于不同的问题，用户通常需要具有不同属性（例如性能）。群集管理员需要能够提供各种PersistentVolumes不同的方式，而不仅仅是大小和访问模式，而不会让用户了解这些卷的实现方式。对于这些需求，有**StorageClass 资源。**

　　StorageClass为管理员提供了一种描述他们提供的存储的“类”的方法。 不同的类可能映射到服务质量级别，或备份策略，或者由群集管理员确定的任意策略。 Kubernetes本身对于什么类别代表是不言而喻的。 这个概念有时在其他存储系统中称为“配置文件”。

　　**PVC和PV是一一对应的。**

#### 7.2.3 StorageClass

PV是运维人员来创建的，开发操作PVC，可是大规模集群中可能会有很多PV，如果这些PV都需要运维手动来处理这也是一件很繁琐的事情，所以就有了动态供给概念，也就是Dynamic Provisioning。而我们上面的创建的PV都是静态供给方式，也就是Static Provisioning。而动态供给的关键就是StorageClass，它的作用就是创建PV模板。

创建StorageClass里面需要定义PV属性比如存储类型、大小等；另外创建这种PV需要用到存储插件。最终效果是，用户提交PVC，里面指定存储类型，如果符合我们定义的StorageClass，则会为其自动创建PV并进行绑定。

## 8.认证和授权

### 8.1 ServiceAccount

Kubernetes中所有的访问，无论外部内部，都会通过API Server处理，访问Kubernetes资源前需要经过认证与授权。

- Authentication：用于识别用户身份的认证，Kubernetes分外部服务帐号和内部服务帐号，采取不同的认证机制，具体请参见[认证与ServiceAccount](https://support.huaweicloud.com/basics-cce/kubernetes_0032.html#kubernetes_0032__section12514659132115)。
- Authorization：用于控制用户对资源访问的授权，对访问的授权目前主要使用RBAC机制，将在[RBAC](https://support.huaweicloud.com/basics-cce/kubernetes_0033.html)介绍。

#### 认证与ServiceAccount

Kubernetes的用户分为服务帐户（ServiceAccount）和普通帐户两种类型。

- 服务帐户与Namespace绑定，关联一套凭证，存储在Secret中，Pod创建时挂载Secret，从而允许与API Server之间调用。
- Kubernetes中没有代表普通帐户的对象，这类帐户默认由外部服务独立管理，比如在华为云上CCE的用户是由IAM管理的。

ServiceAccount同样是Kubernetes中的资源，与Pod、ConfigMap类似，且作用于独立的命名空间，也就是ServiceAccount是属于命名空间级别的，创建命名空间时会自动创建一个名为default的ServiceAccount。同时Kubernetes还会为ServiceAccount自动创建一个Secret。在Pod的定义文件中，可以用指定帐户名称的方式将一个ServiceAccount赋值给一个Pod，如果不指定就会使用默认的ServiceAccount。当API Server接收到一个带有认证Token的请求时，API Server会用这个Token来验证发送请求的客户端所关联的ServiceAccount是否允许执行请求的操作。

Pod中使用ServiceAccount非常方便，只需要指定ServiceAccount的名称即可。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: sa-example
spec:  
  serviceAccountName: sa-example
  containers:
  - image: nginx:alpine             
    name: container-0               
    resources:                      
      limits:
        cpu: 100m
        memory: 200Mi
      requests:
        cpu: 100m
        memory: 200Mi
  imagePullSecrets:            
  - name: default-secret
```

### 8.2 RBAC

Kubernetes中完成授权工作的就是RBAC机制，RBAC授权规则是通过四种资源来进行配置。

- Role：角色，其实是定义一组对Kubernetes资源（命名空间级别）的访问规则。
- RoleBinding：角色绑定，定义了用户和角色的关系。
- ClusterRole：集群角色，其实是定义一组对Kubernetes资源（集群级别，包含全部命名空间）的访问规则。
- ClusterRoleBinding：集群角色绑定，定义了用户和集群角色的关系。

Role和ClusterRole指定了可以对哪些资源做哪些动作，RoleBinding和ClusterRoleBinding将角色绑定到特定的用户、用户组或[ServiceAccount](https://support.huaweicloud.com/basics-cce/kubernetes_0032.html)上。如下图所示。

![](https://support.huaweicloud.com/basics-cce/zh-cn_image_0261301557.png)


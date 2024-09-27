# Pod的亲和性和反亲和性

在某些应用场景中存在基于 Pod与Pod之间关系的调度需求：存在某些相互依赖、需要频繁互相调用的 Pod，它们需要被尽可能地部署在同一个节点、机架、机房、网段或者区域( Zone)内，这就是Pod之间的亲和性； 反之，出于避免竞争或者容错的需求，也可能使某些 Pod 尽可能地远离另外一些 Pod，这就是Pod之间的反亲和性或者互斥性。

&#x20;Pod间的亲和性与反亲和性调度策略从 <mark style="color:orange;">**Kubernetes v1.4**</mark>版本开始引入。简单地说， 就是几个 Pod是否可以在同一个拓扑域中共存或者互斥，前者被称为“ <mark style="color:yellow;">**Pod Affinity**</mark>”，后者被称为“ **Pod Anti Affinity**”。

## 拓扑域

那么，什么是拓扑域，如何理解这个新概念呢? 一个拓扑域由一些 Node组成，这些 Node通常有相同的地理空间坐标。

比如， 在同一个机架、机房或地区， 一般用 Region表示机架、机房等的拓扑区域，用Zone表示地区这样跨度更大的拓扑区域。在某些情况下，我们也可以认为一个 Node就是一个拓扑区域。为此，Kubernetes为 Node内置了一些常用的用于表示拓扑域概念的 Label。

* kubernetes. io/ hostname
* topology. kubernetes. io/ region
* topology. kubernetes. io/ zone

以上拓扑域是由 Kubernetes自己维护的, 在 Node初始化时，Controller Manager会为 Node 设置许多 Label。

比如, kubernetes. io/ hostname这个 Label 的值就会被设置为 Node 的 hostname。

另外, 云厂商提供的 Kubernetes 服务或者使用 cloud- controller- manager创建的集群, 还会给 Node 设置 topology. kubernetes. io/ region 和 topology. kubernetes. io/ zone 的 Label, 以确定各个 Node所属的拓扑域。

需要注意的是，对于 Pod反亲和性策略，要求目标 Node上需要存在相同的 Label，实际上就是要求每个节点都具有 topologyKey 指定的 Label, 如果某些 Node 不具有topologyKey指定的 Label，则可能会导致不可预估的调度结果。

另外，设置 Pod间的亲和性和反亲和性规则，会给调度器引入更多的计算量，在大规模集群中可能会造成性能问题，所以我们要根据集群规模需要谨慎配置 Pod间的亲和性策略。

### Zone和Region

通常一个 Zone代表一个逻辑故障域，Region代表一个管理区域。具体如何定义一个 Zone 或者 Region, 以及一个 Zone 应该包含哪些 Node, 并没有确切的标准, 但是有以下可参考的规则。

* 一个 Zone产生故障不会导致其他 Zone连带发生故障。
* 一个 Zone内部的带宽越高，网络延迟就越小，不同 Zone之间的带宽和延迟较大，不同 Zone直接的通信可能会涉及费用问题。
* 一个 Zone 内部的 Node可能共享同样的电力设备。

#### 举例

假设一个 Kubernetes集群分布在城市A的两个独立物理机房中，根据上面的规则，这两个物理机房可以被定义为两个 Zone。如果又在另外一个城市 B的某个机房扩展了 Kubernetes集群, 则又可以定义一个新的 Zone。

此时, 城市 A中的两个 Zone可以认为属于一个 Region, 城市 B 中的 Zone 属于另外一个 Region。通常一个 Region代表一个更大的域, 由一个或多个 Zone 组成, 如果把 Kubernetes集群扩展到公有云中的 Node上, 那么公有云的 Node也可以被视为在另一个 Region中。

如果 Kubernetes 集群在一个机房里，并且规模很大，则可以按业务需求划分逻辑的 Zone。例如， 可以把一个与交换机直连的服务器作为一个 Zone， 或者几个交换机级联的所有服务器作为一个 Zone，均符合 Zone规划的前两条规则。

在给 Zone命名时需要注意一点，Zone的名称是全局唯一的，不能重复，即使是不同 Region 内的 Zone, 也不能重名。

具体如何定义 Zone及确定 Zone包含的 Node，只要通过给 Node打标签即可完成。常规做法是给 Node 增加 zone= zonename的 Label, 当然也可以使用系统自动设置的 topology. kubernetes. io/ zone、 topology. kubernetes. io/ region 等标签。

例如，通过下面的 zone标签设置(如下代码所示)，可以认为集群中存在两个 zone，分别是 zoneA(包括node1 与node2)和zoneB(node3 与node4)。

```
NAME      STATUS    LABELS
nodel     Ready     node= nodel, zone=zoneA
node2     Ready     node=node2, zone=zoneA
node3     Ready     node=node3, zone=zoneB
node4     Ready     node=node4, zone=zoneB
```

## pod的亲和与反亲和的做法

Pod 亲和与互斥的调度具体做法， 就是通过在 Pod的定义上增加 topologyKey 属性，来声明对应的目标拓扑区域内几种相关联的 Pod 要“在一起或不在一起”。

与节点亲和相同, Pod亲和与互斥的条件设置也是通过requiredDuringSchedulingIgnoredDuringExecution 和preferredDuringSchedulingIgnoredDuringExecution 来完成的。

Pod 的亲和性被定义于PodSpec 的 affinity字段的podAffinity子字段中;

&#x20;Pod 间的互斥性则被定义于同一层次的podAntiAffinity子字段中。

{% tabs %}
{% tab title="1. 参照目标 Pod" %}
创建一个名为“ pod- flag”   的 Pod, 带有 Label “ security=S1” 和 “ app= nginx”,  后面的例子将使用 pod- flag作为 Pod亲和或反亲和的目标 Pod:

```
apiVersion: v1
 kind: Pod
 metadata:
 name: pod- flag
 labels:
 security: "S1"
 app: " nginx"
 spec:
 containers:
- name: nginx
 image: nginx
```
{% endtab %}

{% tab title="2. Pod的亲和性调度" %}
创建第2个 Pod 来说明 Pod的亲和性调度, 这里定义的亲和性 Label是“ security=S1”,对应目标 Pod“ pod- flag”, topologyKey 的值被设置为 “ kubernetes. io/ hostname”:

```
apiVersion: v1
 kind: Pod
 metadata:
 name: pod- affinity
 spec:
 affinity:
podAffinity:
requiredDuringSchedulingIgnoredDuringExecution:
- labelSelector:
matchExpressions:
- key: security
 operator: In
 values;
- $1
topologyKey: kubernetes, io/ hostname
 containers:
- name: pod- affinity
 image: nginx
```

创建 Pod之后,通过 kubectl get pods-o wide命令可以看到,新的 Pod将被调度到 Pod“ pod- flag”所在的同一个 Node上运行。

有兴趣的读者还可以测试一下,在创建这个 Pod之前,删掉这个 Node的“ kubernetes. io/ hostname” Label, 重复上面的创建步骤, 将会发现 Pod一直处于 Pending状态, 这是因为找不到满足条件的 Node了。
{% endtab %}

{% tab title="3. Pod的反亲和性调度" %}
创建第3个 Pod，并且要求它不能与目标 Pod在同一个 Node上运行：

```
apiVersion: v1
 kind: Pod
 metadata:
 name: pod- anti- affinity
 spec:
 affinity:
podAffinity:
requiredDuringSchedulingIgnoredDuringExecution:
- labelSelector:
matchExpressions:
= key: security
 operator: In
 values:
- S1
topologyKey: topology. kubernetes. io/ zone
podAntiAffinity:
requiredDuringSchedulingIgnoredDuringExecution:
- labelSelector:
matchExpressions:
- key: app
 operator: In
 values:
- nginx
topologyKey: kubernetes. io/ hostname
 containers:
- name: pod- anti- affinity
 image: nginx
```

这里要求这个新 Pod与具有 security=S1的 Label的 Pod在同一个 zone的 Node上运行,但是不能与具有 app= nginx的 Label的 Pod 在同一个 Node上运行。创建 Pod 之后, 同样通过 kubectl get pods -o wide命令来查看, 会看到新的 Pod 被调度到同一 Zone内的某个Node上。
{% endtab %}
{% endtabs %}

与节点亲和性类似, Pod 亲和性的操作符也包括 In、NotIn、Exists、DoesNotExist,但是不包括 Gt和 Lt。

### topologyKey限制

原则上，topologyKey可以使用任意合法 Label Key，但是出于性能和安全方面的考虑，对 topologyKey有如下限制。

* 对于 Pod 亲和性策略, 在 <mark style="color:purple;">**requiredDuringSchedulingIgnoredDuringExecution**</mark> 和<mark style="color:orange;">**preferredDuringSchedulingIgnoredDuringExecution**</mark>的定义中, topologyKey 不允许为空。
* 如果 Admission controller 包含了 LimitPodHardAntiAffinityTopology，那么针对requiredDuringSchedulingIgnoredDuringExecution 要求的 Pod 反亲和性策略，topologyKey 只能是 kubernetes. io/ hostname。如果希望使用其他 topologyKey, 则需要禁用LimitPodHardAntiAffinityTopology准入控制器。

### namespace级别限制

除了设置 Label Selector和 topologyKey, Kubernetes还支持通过指定**命名空间列表**进行限制, 同样使用 Label Selector字段设置命名空间的范围。对 Namespace的定义和 Label  Selector及topologyKey同级。省略 Namespace的设置,表示使用定义了 affinity/ anti- affinity 的 Pod所在的命名空间。

Kubernetes 从 v1.21 版本开始, 引入了一个新的字段 namespaceSelector, 用于匹配在指定命名空间中的 Pod，该特性到 v1.24 版本时达到 Stable阶段。

Pod 亲和性条件会选择满足全部匹配 namespaceSelector的标签选择器的命名空间，也可能选择集群内的所有命名空间，这可能干扰其他命名空间中 Pod的调度，也会引入更多的性能损耗，需要谨慎使用。例如, 一个 Pod需要与具有 type=HPC的 Label的命名空间中的其他 Pod 在一起运行, 则该 Pod的亲和性规则应设置为：

```
 spec:
 affinity:
podAffinity:
requiredDuringSchedulingIgnoredDuringExecution:
- namespaceSelector:
matchExpressions:
- key: type
 operator: In
 values:
- HPC
topologyKey: kubernetes. io/ hostname
```

## CrossNamespaceAffinity

此外, Kubernetes在 ResourceQuota资源中引入了一个新字段CrossNamespaceAffinity，用于限制命名空间中允许设置 namespaceSelector亲和性策略的 Pod数量(配额)。

例如,下例中的 ResourceQuota 限制了允许设置 namespaceSelector亲和性策略的 Pod数量为0,这表示不允许任何 Pod设置跨命名空间的亲和性策略：

```
apiVersion: v1
 kind: ResourceQuota
 metadata:
 name: disable- cross- namespace- affinity
 namespace: foo- ns
 spec:
 hard:
 pods: "0"
scopeSelector:
matchExpressions:
- scopeName: CrossNamespaceAffinity
```

&#x20;Pod 的亲和性和反亲和性调度策略在作用于工作负载管理器(如 Deployment、ReplicaSet、StatefulSet等)时,可以为一组 Pod副本集需要的亲和性或反亲和规则进行方便的配置。

## 举例

例如，希望同一组 Pod副本在相同的拓扑域中运行，或者多个Pod副本不要同时运行在一个 Node上。

下面的示例代码中设置了反亲和性策略，要求不能与具有 app= nginx的 Label 的 Pod 运行在同一个 Node上, 而该 Deployment的 Pod本身都具有 app= nginx的 Label, 所以调度器会选择不同的 Node来运行3个 Pod副本，从而实现了在同一个 Node上不要运行多个 Pod副本的需求：

```
# app- with- anti- affinity. yaml
apiVersion: apps/v1
 kind: Deployment
 metadata:
 name: app- with- anti- affinity
 spec:
 selector:
matchLabels:
 app: nginx
 replicas: 3
 template:
  metadata:
 labels:
 app: nginx
 spec:
 affinity:
podAntiAffinity:
requiredDuringSchedulingIgnoredDuringExecution:
- labelSelector:
matchExpressions:
- key: app
 operator: In
 values:
- nginx
topologyKey: " kubernetes. io/ hostname"
 containers:
- name: nginx
 image: nginx
```

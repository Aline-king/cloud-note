# Node亲和性调度策略

为了实现更加灵活和精细的策略，Kubernetes提供了**亲和性**和**反亲和性**的机制，扩展了 Pod 的调度能力。使用亲和性和反亲和性的优点如下。

1. 更强的选择逻辑控制能力（不仅仅是“符合全部”的简单情况）。
2. 可以设置为**软性限制**或者**优选**，使得调度器在无法找到完全满足要求的 Node时，也会寻找其他 Node来调度 Pod。
3. 除了基于 Node的 Label，还可以依据 Node上正在运行的其他 Pod的 Label来进行亲和性和反亲和性设置，这样就可以定义一种规则来描述 Pod 之间的亲和或互斥关系。

亲和性调度功能包括<mark style="color:purple;">**节点亲和性(NodeAffinity)**</mark>和 <mark style="color:purple;">**Pod亲和性(PodAffinity)**</mark>两种类型的设置

{% hint style="info" %}
节点亲和性与NodeSelector类似，但表达能力更强，并且允许设置**软性**匹配规则。

&#x20;Pod间可以设置亲和性规则或者反亲和性规则。
{% endhint %}

## 节点亲和性

下面对节点亲和性调度策略进行详细说明。

节点亲和性调度策略与 **NodeSelector**比较类似，也是通过 Node上的 Label来设置 Pod 是否可以调度到目标 Node， 具体可以通过`.spec.affinity.nodeAffinity` 字段进行设置。节点亲和性的配置类型有以下两种。

1. <mark style="color:purple;">**requiredDuringSchedulingIgnoredDuringExecution：**</mark>必须满足指定的规则才可以调度 Pod到 Node上, 是硬性要求, 它的功能与nodeSelector很像, 但是使用的是更加灵活的语法。
2. <mark style="color:purple;">**preferredDuringSchedulingIgnoredDuringExecution：**</mark>强调优先满足指定规则, 调度器会尝试调度 Pod 到Node上，但并不强求，是软性限制。可以设置多个规则，还可以设置每个规则的权重( weight)值，以定义判断的优先级顺序。

这两种策略名称中的“**IgnoredDuringExecution**”的意思是：如果一个 Pod所在的节点在 Pod 运行期间其标签发生了变更，不再符合该 Pod 的节点亲和性需求，则系统将忽略 Node的 Label变化, 让 Pod在原 Node上继续运行。

### 举例

在下面的例子（pod- with- node- affinity. yaml）中, 设置了如下两个节点亲和性调度规则。

* <mark style="color:orange;">**requiredDuringSchedulingIgnoredDuringExecution ：**</mark>要 求 运 行 在 具 有 标 签“<mark style="color:purple;">**kubernetes. io/ arch=amd64**</mark>”( 意为系统架构为 amd64) 的节点上，需要在nodeSelectorTerms字段下设置一个或多个条件表达式。
* <mark style="color:orange;">**preferredDuringSchedulingIgnoredDuringExecution**</mark><mark style="color:orange;">：</mark>要求尽量运行在具有 Label “ disk- type= ssd”(意为磁盘类型为 ssd)的 Node上,但不强制要求,如果没有 Node 具有这个 Label，则将尝试调度。需要在 preference 字段下设置一个或多个条件表达式。

```yaml
# pod- with- node- affinity, yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod- with- node- affinity
 spec:
 affinity:
nodeAffinity:
requiredDuringSchedulingIgnoredDuringExecution:
nodeSelectorTerms:
- matchExpressions:
- key: kubernetes. io/ arch
 operator: In
 values:
- amd64
preferredDuringSchedulingIgnoredDuringExecution:
- weight: 1
 preference:
matchExpressions:
- key: disk- type
 operator: In
 values:
- ssd
 containers:
- name: nginx
 image: nginx
```

在 Label的匹配规则中，使用逻辑操作符( operator)进行设置，可以使用的操作符包括 In、 NotIn、Exists、DoesNotExist、Gt和 Lt。 这几个操作符的含义如下。

* <mark style="color:purple;">**In**</mark>：Label的值( Value)在给定的集合中。
* <mark style="color:purple;">**NotIn**</mark>: Label的值( Value) 不在给定的集合中。
* <mark style="color:purple;">**Exists**</mark>: 需要具有此 Label Key, 此时不需设置 Value。
* <mark style="color:purple;">**DoesNotExist**</mark>: 需要不存在此 Label Key, 此时不需设置 Value。
* <mark style="color:purple;">**Gt**</mark>: Label的值( Value)的整数值大于给定的值。
* <mark style="color:purple;">**Lt**</mark>: Label的值( Value)的整数值小于给定的值。

## node反亲和性

对于 Node亲和性来说,虽然没有反亲和性的字段,但是可以用 NotIn 和 DoesNotExist 就能实现反亲和性的要求。

NodeAffinity规则设置的注意事项如下。

* 如果在 Pod 配置中同时使用了 <mark style="color:red;">**nodeSelector**</mark>和<mark style="color:blue;">**nodeAffinity**</mark>，那么必须两个条件都得到满足，才能将 Pod 调度到指定的 Node 上。
* 如果 nodeAffinity 指定了多个 <mark style="color:green;">**nodeSelectorTerms**</mark>，则只要其中一个条件能匹配成功就能完成 Pod的调度，即多个条件之间是逻辑<mark style="color:purple;">**或(OR)**</mark> 的关系。
* 如果 matchExpressions在 nodeSelectorTerms中设置了多个条件表达式，那么必须满足所有条件才能完成 Pod的调度，即多个条件之间是逻辑<mark style="color:purple;">**与(AND)**</mark>的关系。

在使用 preferredDuringSchedulingIgnoredDuringExecution 类型时, 可以为每个规则设置一个1\~100的权重值( weight)，用于给调度器提供一个类似于优先级的分数，调度器会为满足条件的 Node加上 weight设置的分值，以选出最终得分最高的 Node。

### 举例

在下面的例子( pod- with- node- affinity- weight. yaml)中, 设置了两个希望满足的条件(preferredDuringSchedulingIgnoredDuringExecution)。

<pre class="language-yaml"><code class="lang-yaml"># pod- with- node- affinity- weight. yaml
apiVersion: v1
 kind: Pod
 metadata:
   name: pod- with- node- affinity- weight
 spec:
   affinity:
     nodeAffinity:
       preferredDuringSchedulingIgnoredDuringExecution:
       - weight: 1
         preference:
<strong>           matchExpressions:
</strong>           - key: zone
             operator: In
             values:
             - north
       - weight: 50
         preference:
         matchExpressions:
           - key: disk- type
             operator: In
             values:
             - ssd
   containers:
<strong>   - name: nginx
</strong>     image: nginx
</code></pre>

作用：对于具有 Label“ zone= north”的 Node, 系统会在调度算法得分上+1( weight=1)，对于具有 Label“ disk- type= ssd”的 Node，系统会在调度算法得分上+50( weight=50)，然后还会根据其他条件(如资源请求等)综合计算出2个 Node的得分, 最终将 Pod调度到得分最高的 Node上。

## 调度器配置

另外，如果为 Pod设置了多个调度器，则可以为其中某个调度器设置节点亲和性，这需要通过创建一个“**调度器配置**”(KubeSchedulerConfiguration)资源来进行设置,&#x20;

其中通过pluginConfig字段为某个调度器配置节点亲和性。这种配置方法只适用于某种调度器(如用户自行开发的外部调度器)在某些特殊 Node的场景。

### 举例

在下面的示例中先配置了两个调度器( default- scheduler 和 foo- scheduler), 然后为 foo- scheduler 调度器再指定一种额外的(addedAffinity)节点亲和性的规则，要求 Node具有 <mark style="color:purple;">**Label “ scheduler- profile= foo”**</mark>。如果在 Pod配置中也设置了节点亲和性规则，那么系统要**同时**满足 Pod 的亲和性(nodeAffinity)和这个补充规则(addedAffinity)，才能进行调度。

```
apiVersion: kubescheduler. config. k8s. io/v1beta3
 kind: KubeSchedulerConfiguration
 profiles:
- schedulerName: default- scheduler
- schedulerName: foo- scheduler
pluginConfig:
- name: NodeAffinity
 args:
addedAffinity:
requiredDuringSchedulingIgnoredDuringExecution:
nodeSelectorTerms:
- matchExpressions:
- key: scheduler- profile
 operator: In
 values:
- foo
```

需要注意的是,DaemonSet工作负载类型不支持使用 KubeSchedulerConfiguration这种多调度器配置。系统使用默认的调度器调度 DaemonSet类型的 Pod,并且支持 NodeAffinity 亲和性调度规则。

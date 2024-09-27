# 污点和容忍度

前面介绍的NodeAffinity节点亲和性，是在 Pod中定义的一种属性，使得 Pod能够被调度到某些 Node上运行(优先选择或强制要求)。

污点( Taint) 则正好相反，它让 Node 拒绝一些具有某些特性的 Pod在其上运行。

容忍度( Toleration)则是 Pod中的配置，用于告诉系统允许调度到具有指定污点的节点上运行。当然，调度器也会评估其他的调度要求，综合评估之后才会选择合适的目标 Node。

污点( Taint)和容忍度( Toleration)配合使用, 可以避免将 Pod调度到不合适的 Node 上。例如，某个 Node存在问题(磁盘空间不足、计算资源不足、存在安全隐患要进行更新维护)，希望新的 Pod不会被调度过来，就可以通过设置某些污点( Taint)来实现。但被标记为 Taint 的 Node并非不可用，仍是有效的 Node，所以对于可以在这些 Node上运行的 Pod 来说, 可以通过给 Pod 设置与污点( Taint) 匹配的容忍度( Toleration)来实现调度。

## 污点设置

在默认情况下，在 Node上设置一个或多个 Taint之后，除非 Pod明确声明能够容忍这些污点,否则无法在这些 Node上运行。可以首先通过 kubectl taint命令为 Node设置 Taint 信息：

&#x20;`kubectl taint nodes nodel key= value:NoSchedule`

这个设置为 nodel加上了一个 Taint。该 Taint的键为 key, 值为 value, Taint的效果是NoSchedule。这意味着除非 Pod 明确声明可以容忍这个 Taint, 否则不会被调度到 nodel 上。

## 容忍设置

然后, 需要在 Pod 中声明 Toleration。下面的两个 Toleration都被设置为可以容忍( Tolerate) 具有该 Taint的 Node, 使得 Pod能够被调度到 Nodel 上:

```
 tolerations:
- key: "key1"
 operator: " Equal"
 value: " value"
 effect: "NoSchedule"
```

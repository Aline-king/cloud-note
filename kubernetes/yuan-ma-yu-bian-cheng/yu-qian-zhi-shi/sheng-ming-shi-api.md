# 声明式API

声明式就是“告诉 K8s 你要什么，而不是告诉它怎么做”，

在 K8s 里面，声明式的体现就是 kubectl apply 命令，在对象创建和后续更新中一直使用相同的 apply 命令，告诉 K8s 对象的终态（yaml清单文件属性）即可，底层是通过执行了一个对原有 API 对象的相关操作来实现的，可以一次性处理多个操作。声明式 API 让 K8s 的“大规模容器编排”工作看起来很轻松。
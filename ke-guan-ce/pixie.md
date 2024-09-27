# Pixie

## 一、Pixie介绍

> 参考网址为：[https://docs.px.dev/](https://docs.px.dev/)

### Pixie是什么？

Pixie 是一个面向 Kubernetes 应用的开源可观测性工具。Pixie 使用 [eBPF](https://docs.px.dev/about-pixie/pixie-ebpf) 自动捕获遥测数据，无需手动进行插桩。

开发者可以使用 Pixie 查看他们集群的高层状态（服务地图、集群资源、应用流量），同时还能深入查看更详细的视图（Pod 状态、火焰图、单个完整的应用请求）。

Pixie 由 [New Relic, Inc.](https://newrelic.com/) 在 2021 年 6 月贡献给 [云原生计算基金会](https://www.cncf.io/)，作为一个沙箱项目。

### 1.2 特点

* **自动遥测**：Pixie 使用 eBPF 自动收集遥测数据，如完整请求体、资源和网络指标、应用程序概况等，详情请见[这里](https://docs.px.dev/about-pixie/data-sources)。
* **集群内边缘计算**：Pixie 在[集群本地](https://docs.px.dev/about-pixie/faq/#data-collection-where-does-pixie-store-its-data)收集、存储和查询所有遥测数据。Pixie 的集群 CPU 使用率不超过 5%，在大多数情况下不超过 2%。
* **可编程性**：Pixie 的灵活的 Python 式查询语言 [PxL](https://docs.px.dev/reference/pxl/)，可以在 Pixie 的用户界面、命令行界面和客户端 API 中使用。Pixie 提供了一套[社区脚本](https://github.com/pixie-io/pixie/tree/main/src/pxl\_scripts)，用于常见的[使用场景](https://docs.px.dev/tutorials/pixie-101)。



问：Pixie是否有可能取代Prometheus监控系统？

答：Pixie是一款专注于为Kubernetes集群提供实时可见性的监控和故障排查工具。它通过直接从集群收集和分析遥测数据来实现这一目标，无需显式的instrumentation或配置。 相比之下，Prometheus是一款流行的开源监控系统和时间序列数据库，主要依赖于从已instrumented服务拉取的预定义指标。 虽然这两个工具都服务于监控目的，但它们有着不同的方法和优势： Pixie更侧重于Kubernetes环境的动态实时分析和故障排查，而Prometheus则设计用于基于预定义指标的监控和警报。 Pixie自动收集遥测数据，无需大量配置，而Prometheus需要手动设置和配置目标进行抓取。 Prometheus拥有强大的生态系统，包含许多集成和用于分析收集数据的强大查询语言（PromQL），而Pixie提供了可视化界面和自己的脚本语言（.pxl）用于数据分析。 Pixie不太可能完全取代Prometheus作为监控系统，因为它们服务于不同的使用场景并具有独特的功能。相反，它们在组织的监控堆栈中有可能被一起使用，其中Pixie提供深度的Kubernetes可见性和故障排查能力，而Prometheus处理更广泛的系统监控和警报需求。在两者之间选择或一起使用将取决于组织的具体要求、基础设施和工作流程。



\

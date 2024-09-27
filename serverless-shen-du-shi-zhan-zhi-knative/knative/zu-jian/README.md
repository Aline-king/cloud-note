# 组件

## Knative组件

Knative的三个组件（Serving、Build、Eventing）正是遵循这三个最佳实践的设计实现。

### 2.1 Serving

####

<figure><img src="../../../.gitbook/assets/image (53).png" alt=""><figcaption></figcaption></figure>

#### 2.1.3&#x20;

### 2.2 Tekton



<figure><img src="../../../.gitbook/assets/image (54).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (55).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (56).png" alt=""><figcaption></figcaption></figure>

### 2.3 Eventing

Knative Eventing的核心功能是对发布/订阅细节进行抽象处理，帮助开发人员摆脱相关负担一种开发模型，由于前后端实现松耦合。

Eventing组件的特性如下：

* 声明式地绑定event sources, triggers和services
* 从少量事件到实时stream pipelines动态扩展
* 采用CloudeEvents标准
* 抽象的事件来源，解耦具体事件源类型(eg. Kafka,CronJob,Github,kubernetes … )
* Channel处理缓冲和持久性，即使该服务已被关闭时也确保将事件传递到其预期的服务。另外，Channel是我们代码和底层消息传递解决方案之间的一个抽象层。这意味着可以像 Kafka 和 RabbitMQ 一样在某些服务之间进行消息交换，但在这两种情况下我们都不需要编写特定的实现代码。(目前Channel的实现有InMemory, Kafka, Nats, PubSub )

<figure><img src="../../../.gitbook/assets/image (57).png" alt=""><figcaption></figcaption></figure>

## 三、关于CloudEvent

由于Serverless 平台和产品众多，支持的事件来源和事件格式定义也是五花八门，CNCF serveless工作组提出CloudEvnets项目，试图对事件进行标准化。

CloudEvents是以通用格式描述事件数据的规范，以提供跨服务、平台和系统的互操作性。CloudEvents当前已经发布1.0版。

<figure><img src="../../../.gitbook/assets/image (58).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (59).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (60).png" alt=""><figcaption></figcaption></figure>

## 四、整体结构图

<figure><img src="../../../.gitbook/assets/image (291).png" alt=""><figcaption></figcaption></figure>

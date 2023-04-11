# Prometheus组件

![Prometheus架构](https://www.prometheus.wang/quickstart/static/prometheus_architecture.png)

#### Prometheus Server

Prometheus Server是Prometheus组件中的**核心**部分，负责实现对监控数据的**获取**、**存储**以及**查询**。

**获取**：Prometheus可以通过静态配置管理监控目标，也可以使用Service Discovery的方式进行动态管理监控目标，并从这些监控目标中获取数据。

**存储**：其次，Prometheus需要对采集到的监控数据进行存储，Prometheus Server本身就是一个时序数据库，将采集到的数据按照时间序列的方式存储在本地磁盘当中。

**查询**：最后Prometheus Server对外提供自定义的PromQL语言，实现对数据的查询以及分析。

**拓展**：Prometheus Server**内置**的Express Browser UI，通过这个UI可以直接通过PromQL实现数据的**查询**以及**可视化**。

​			Prometheus的**联邦集群**能力可以使其从其他Prometheus Server实例中获取数据，因此再大规模监控的情况下，可以通过联邦集群以及功能分区的方式对Prometheus Server进行拓展。



#### Exports

Export将监控数据采集的端点通过http的方式暴露给Prometheus Server，Prometheus Server通过访问改Exporter提供的Endpoint端点，即可获取需要采集的监控数据。

一般来说Exporter分为2类：

​	直接采集：这一类Exporter直接内置了对Prometheus监控的支持，都直接内置了用于向prometheus暴露监控数据的端点。比如cAdvisor，Kubernetes，Etcd，Gokit等。

​	间接采集：原有监控目标并不直接支持prometheus，因此我们需要通过Prometheus提供的Client Library编写该监控目标的监控采集程序。例如Mysql Exporter，JMX Exporter，Consul Exporter等。



#### AlertManger

在Prometheus Server中支持基于PromQL创建告警规则，如果满足PromQL定义的规则，则会产生一条告警，而告警的后续处理流程则由AlertManger进行管理。在AlertManger进行管理。在AlertManager中我们可以与邮件、Slack等等内置的通知方式进行集成，也可以通过Webhook自定义告警处理方式。AlertManager即Prometheus体系中的告警处理中心。



#### PushGateway

由于Prometheus数据采集基于Pull模型进行设计，因此在网络环境的配置上必须要让Prometheus Server能够直接与Export进行通信。当这种网络需求无法直接满足时，就可以利用PushGateway来进行中转。可以通过PushGateway将内部网络的监控数据主动Push到Gateway当中。而Prometheus Server则可以采用同样Pull的方式从PushGateway中获取到监控数据。
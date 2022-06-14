# Service Mesh

## 什么是Service Mesh
  Service Mesh是一种软件架构，专指用于搭建基础服务中服务到服务之间通讯的一种架构模式，通过代理的方式，拦截服务之间的流量信息，进行加工处理，从而达到管理服务之间交互的目的。这里服务间的通讯包括了负载均衡，熔断，安全，追踪，观测等等常用的服务治理功能，Service Mesh通过将这些服务治理相关的功能独立到一种专门的架构，也就是ServiceMesh，中处理，从而让原本的服务专注于业务实现。从目前的资料来看，ServiceMesh是一种软件架构的模式，与单一架构或微服务类似，他们没有统一的，明确的标准，只是提供一种架构模式，而实现方式则根据不同厂商而不同，例如Istio， Linkerd，Consul，Kuma，AWS App Mesh，Open Service Mesh等等

## Service Mesh的架构方式
  ServiceMesh的架构主要由**data plane(数据平面**)和**control plane（控制平台）**组成。数据平面由很多个**network proxy（网络代理）**组成，控制平面则有一组控制程序组成。代理与服务之间一一组对，每一个代理都会拦截搭配服务的进出网络流程，控制平面则负责协调这些代理，从代理中收集数据，并且提供接口来控制代理的行为。
  [service mesh architecture]

  服务与服务之间的交流被各自的proxy节点拦截了，service mesh实现时，在这些proxy内就可以针对服务治理相关的特性进行处理。


## 什么时候需要ServiceMesh
  ServiceMesh的架构方式看起来与Kubernetes的kube-proxy和kubelet的模式很相似，只是kube-proxy工作在node层面，而kubelet则是管理整个集群节点的apiserver层面，而且不仅从架构模式上来看，从功能上来看，似乎servicemesh和kubernetes也有一些重合，例如服务发现，负载均衡这些技术在kubernetes中就已经由平台提供，对于业务服务无感了。如果我们已经有了kubernetes，不禁要问我们还需要servicemesh么？对于这个问题，我认为可以从以下几个方面考虑。
  首先从历史发展的角度，servicemesh大约在2010年左右就有Twitter的Finagle实践，之后的几年里又分别出现了HashiCorp Consul (2014), Istio (2017), Linkerd (2018)和 Kuma (2019)。而Kubernetes则是在2014年由Google创建，而其前身在google内部可以追溯到2003年的Borg System，可见servicemesh的出现实在类似kubernetes这样的容器编排平台之后。事实上服务治理的问题，从单体架构，到微服务再到kubernetes本身就是一个不断演进的过程，例如我之前纪录过关于[kubernetes服务发现的机制]()，而ServiceMesh则是在kubernetes之后的进一步演进，正式由于容器和kubernetes的出现，解决了大批量部署服务的运维成本问题之后，才使得像servicemesh这样依赖为每一个服务做代理搭配的架构方式才成为可能。而但从功能特性本身来说，虽然servicemesh提供的功能特性部分与kubernetes这样的平台有重合，但得意与servicemesh的proxy架构工作在service层级，这使得servicemesh可以对服务间通讯做出更全面的控制，例如我们可以增加更多定制化的负载均衡策略，增加内部服务于服务之间的安全控制。
  因此如果我们已经在使用kubernetes，并且需要更多定制化或kubernetes不具备的服务间治理特性的时候，无疑servicemesh是一个不错的选择。这比我们给应用服务添加额外的依赖和代码来实现与业务无关的功能特性要好得多。

## 一些常见的ServiceMesh特性
  因为ServiceMesh并不是一个明确的标准，只是一种架构方式，因此由于厂商不同可能实现方式都会有一些差异，因此这里以Istio为例，列举一些常见的和ServiceMesh相关的特性和其大致原理。

**关于Istio**
  Istio是一个ServiceMesh框架，通过‘Sidecar’的模式，对服务进行代理，从而使服务具备相关的功能特性。主要包括以下几个功能特性：
  - 服务与服务之间的安全认证，包括TLS加密，身份认证和授权
  - 7层（HTTP，gRPC,WebSocket）或4层(TCP)的负载均衡
  - 对于服务间流量的细粒度控制，例如丰富的路由规则，重试机制，故障注入(Fault Injection)
  - 插件式的控制策略层和可配置的API用于访问控制，限流等
  - 服务可观测性，自带指标，日志采集以及整个集群的进出口流量监控

[Istio架构图]

### Data plane: Envoy Proxy
  Envoy是一种服务间的通讯代理，是Istio data plane中唯一控制数据流量的组件。实际上Envoy本身就已经提供了许多内建的功能，例如动态的服务发现，负载均衡，TLS，熔断，健康检查，丰富的指标数据等等特性，而Istio主要通过控制中心，将更高抽象层次的配置信息，翻译成Envoy格式的配置信息，通过Envoy特定的协议更新到集群中相应的Envoy代理中。
  Envoy通过对端口的监听对网络流量的拦截和处理，和大多数代理一样，Envoy也使用了non-blocking的线程机制，但和我们常见的一个accpet线程多个worker的模式不同，Envoy使用了多个accept线程，默认和硬件线程数相等，来监听所有已配置的端口情况，当有请求进入时，由操作系统来决定应该由哪个worker thread接收，而之后被接收链接的处理将一直在这个worker线程中直至连接终止。这样一来，Envoy的事件处理接近于单线程模式，易于水平扩展。
  **Envoy的线程模型**

  []
  - Main Thread: 主线程，负责提供控制接口，运行时更新配置，协调worker线程等
  - Worker: 工作线程，每个工作线程都负责监听所有可用的listener配置，并且负责每一次连接的完整生命周期
  - File Flush: 负责Envoy的相关文件操作，避免由于文件写入造成的线程阻塞

  **Worker的主要构成部分**
  [Worker main parts]
  Worker内部主要分为**Listener**和**Cluster**两部分，分别用于处理下游(downstream)和上游(upstream)方向流量。例如当服务A的请求通过Envoy转向服务B时，此时A发出的数据对于Envoy来说就是下游数据，
  而目标服务B则是上游服务。当Envoy接收到来自下游数据时，接收链接的Worker将会根据一些匹配规则创建对应的‘过滤链’(Filter Chain)，过滤链由多个Filter组成，例如TLS Filter，RateLimit Filter，RBAC Network Filter以及 HTTP connection manager等等，每一种Filter都会负责特定的事务，需要指出的是，鉴于HTTP在如今的服务架构中的重要地位，HTTP connection manager是Envoy中最核心的Filter之一，他负责将链接中的原始信息转换成Http层面的数据结构，纪录相关日志，生成Trace信息，路由，统计等等核心功能。
  
  **Sidecar**
  Sidecar是一种部署模式，Istio中所有的Envoy代理都采用这种模式和目标服务一起部署。Sidecae模式将组建部署在一个单独的进程或者容器中并对特性功能进行封装，从而达到对目标服务的免侵入性与隔离性。Sidecar本意是指老式边三轮摩托车旁边的那个小边斗，用来形容这种部署模式再合适不过了。Sidecar部署的组件和目标服务通常存在紧密的关联关系。

### Istio主要特性概念
  Envoy作为data plane的核心组件，已经实现了大部分功能，而Istiod则作为control plane，主要由4个部分组成
  - Pilot 实现服务发现
  - Galley 提供配置相关功能
  - Citadel 证书管理
  - Mixer 支持扩展 
  Istio的'd'指的是daemon，**Good teams look back upon their choices and, with the benefit of hindsight, revisit them**，Istio在官方blog中对Istiod的设计思想做了介绍并列举了设计Istiod的原因。

### 流量控制 Traffic Management
 
### 安全 Security

### 可观测 Observability

### 拓展性 Extensibility


##参阅
[ServiceMesh wiki](https://en.wikipedia.org/wiki/Service_mesh)
[Linkerd, What is ServiceMesh](https://linkerd.io/what-is-a-service-mesh/)
[The History of Kubernetes on a Timeline](https://blog.risingstack.com/the-history-of-kubernetes/)
[Concepts of Istio](https://istio.io/latest/docs/concepts/)
[Envoy threading model](https://blog.envoyproxy.io/envoy-threading-model-a8d44b922310)
[Sidecar pattern](https://docs.microsoft.com/en-us/azure/architecture/patterns/sidecar)
[Introducing istiod: simplifying the control plane](https://istio.io/latest/blog/2020/istiod/)
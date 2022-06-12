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

### 关于Istio

### Envoy代理

### 流量控制 Traffic Management
 
### 安全 Security

### 可观测 Observability

### 拓展性 Extensibility


##参阅
[ServiceMesh wiki](https://en.wikipedia.org/wiki/Service_mesh)
[Linkerd, What is ServiceMesh](https://linkerd.io/what-is-a-service-mesh/)
[The History of Kubernetes on a Timeline](https://blog.risingstack.com/the-history-of-kubernetes/)
[Concepts of Istio](https://istio.io/latest/docs/concepts/)
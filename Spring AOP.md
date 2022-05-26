# How does Spring AOP work

## 什么是AOP
  AOP，Aspect Oriented Programming，一般译作“面向切面编程”，从名字上来看，显然这是一种编程理念，那既然已经有了Procedure-Oriented（面向过程）和Object-Oriented（面向对象），为什么还需要Aspect-Oriented呢？ 首先AOP并不是一个新鲜的概念，他也不是只从属于Spring框架，早在1997年就有关于aop的论述，可能实际还有更早的可查文章，可以理解为对Object-Oriented一种扩充。Object-Oriented的编程方式引入了Object，通过对象对编程中的相关业务逻辑（procedure）进行了封装，例如通过class的方式，这样可以提高代码的复用率，然后不相关的代码模块之间皆耦合，提高整个系统的可维护性。在通过Object进行一些封装之后，通常会出现一种难以理解和维护的“分散”（scattered）或“聚合”（tangled）在一起的代码，这些代码模块就是“aspect”。“分散”指的是一切相对独立但是公用的模块，这些模块可能是需要跨系统或者跨编程语言公用的，例如日志记录，鉴权等等，如果我们要改动这些代码模块的话，几乎要改动相关的所有调用模块。“聚合”指的是多个不相关的公共模块，可能会由于业务逻辑的需要，被串联在同一个业务流程中，这样我们在对这个公共模块的功能代码进行改动时，就需要理解所有相关的调用逻辑以避免任何由于改动造成的对原有业务逻辑的影响。

  针对这些aspect进行编程设计，就是Aspect Oriented Programming。

## AOP的基本概念
**concern**
  程序中，每个模块都有不同的侧重点，有的是数据库存储，有的是数据校验，这个不同的侧重点成为*concern*，针对这些concern进行模块抽象和提炼，有利于对整个系统的功能拆分，使得模块之间不会相互耦合相互影响，便于维护。
**cross-cutting concerns**
  日志记录，权限校验这些自然也属于concern的一种，但是这些concern又不同于普通的concern，他们很几乎所有其他的concern有交互和影响，类似于公共的concern模块，那么这种类型的concern就称为*cross-cutting concerns*，也就是所谓的*横切关注点*。以往在OOP中，cross-cutting concerns往往都是被封装成单独的对象，然后在业务concern代码中进行组装。而AOP则是将这种cross-cutting concerns独立出来，成为一个单独的模块。
**advice**
  AOP中，针对cross-cutting concerns封装出来的单独模块中，实际需要添加到目标concern中的代码逻辑称为advice，如果日志记录，事物管理，身份校验相关的代码等
**pointcut**
  point指代的是应用的执行点，pointcut也就是切点，用于告知应用，我们需要在哪些执行点切入，添加我们相关的advice代码。
**aspect**
  pointcut和advice的组合在一起，就是aspect。这通常被用作关键字来概括aop

## AOP的实现
  AOP实际要做的就是将一些程序关注点独立出来，再把这些独立的代码块使用一种低侵入性的方式和目标代码组织起来。通常有两种方式，一种是使用和目标模块一致的语言和环境，再将两者整合生成一个完整的应用，这种方式也被称为**weaving**，也就是织入的方式。另一种就是创建一个新的代码解释器和运行时环境，使得这种新的解释器和环境可以理解AOP的语法和实现相关的特性。

## 什么是Spring AOP

>Spring作为一个优秀的java框架，经常用于构建复杂的业务应用，而且其核心之一就是提供spring container用来管理java对象，AOP自然也是其支持的特性之一。

### Spring AOP的实现方式
  显然，要创建一个新的代码解释器和运行时环境的工作实在太繁琐，而且存在太多兼容性问题，大多数框架都选择了weaving的方式来实现AOP特性，Spring AOP也不例外，而且Spring使用了基于Proxy（代理）的方式实现AOP，通过AOP Proxy来实现对目标对象的aspect。由于是通过代理的方式，那么一个普通的不属于spring container管理的对象，spring自然无法对其进行代理动作，这也就罢spring aop的作用范围限定在spring bean内，代理的方式也只能作用于run-time（运行时）。
  这里可以和AspectJ做一个简单的比较，AspectJ并不使用Proxy的方式，而是直接使用代码织入的方式，因为他必须有AspectJ compiler的支持，也不支持运行时weaving。由于有了编译器的支持，AspectJ虽然体量上更臃肿一些，但功能显然会更加强大一些，支持的切入点会更加全面。
  //基于代理的Spring AOP

### Spring如何生成Proxy Class




## 参阅
[1.Aspect Oriented Programming](https://en.wikipedia.org/wiki/Aspect-oriented_programming)

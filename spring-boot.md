## Spring Boot Main ##


### SpringApplication
通常一个springboot应用，是从springapplication开始，默认情况下， springapplication将会执行以下步骤以引导启动一个springboot应用程序
1. 基于classpath，创建一个合适的ApplicationContext
2. 注册一个CommandLinePropertySource用于以SpringProperty的方式暴露命令行参数
3. 刷新application context，加载所有单例bean
4. 触发任何CommandLineRuner标识的Bean

**构建SpringApplication**
>SpringApplication(ResourceLoader resourceLoader, Class<?>... primarySources)
1. 创建一个SpringApplication对象，初始化该对象得resourceLoader。spring application context将从primarySources加载beans信息，通常推荐用一个带有@Configuration注解得class来启动项目。

2. this.webApplicationType = WebApplicationType.deduceFromClasspath();
设置webapp类型，deduceFromClasspath会根据当前项目中是否加载了特定得class来判断webapp类型是REACTIVE，SERVERLET或者是NONE

3. this.bootstrapRegistryInitializers = getBootstrapRegistryInitializersFromSpringFactories();
从SpringFactoryies中获取BootstrapRegistryInitializers。
 

**引导初始化**
>public ConfigurableApplicationContext run(String... args)的执行流程,SpringApplication.run方法将负责初始化并启动一个应用

1.启动计时器
```
    StopWatch stopWatch = new StopWatch();
    stopWatch.start();
```
springframework/util中的一个计时器，通过start/stop对一段流程的执行时间进行计时，可以同时对多个任务进行计时，并内置了一些方便的函数对结果进行输出或打印，这里StopWatch主要用于对SpringBoot的引导启动过程进行计时

2.创建BootstrpContext
```
		DefaultBootstrapContext bootstrapContext = createBootstrapContext();
		ConfigurableApplicationContext context = null;
		configureHeadlessProperty();
		SpringApplicationRunListeners listeners = getRunListeners(args);
		listeners.starting(bootstrapContext, this.mainApplicationClass);
```
创建一个DefaultBootstrapContext，该context是一个简单的上下文用于启动过程，直至ApplicationContext准备就绪。该上下文维护生效期内的对象注册，创建和获取。DefaultBootstrapContext实现了BootstrapRegistry和BootstrapContext接口。
BootstrapContext主要提供对一些需要消耗大量资源创建的单例或者需要在Application Context之前共享对象提供lazy access(延迟初始化)。
BootstrapRegistry提供工厂方法对实例进行创建


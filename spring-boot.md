## Spring Boot Main ##


### SpringApplication
通常一个springboot应用，是从springapplication开始，默认情况下， springapplication将会执行以下步骤以引导启动一个springboot应用程序
1. 基于classpath，创建一个合适的ApplicationContext
2. 注册一个CommandLinePropertySource用于以SpringProperty的方式暴露命令行参数
3. 刷新application context，加载所有单例bean
4. 触发任何CommandLineRuner标识的Bean

**构建SpringApplication**
>SpringApplication(ResourceLoader resourceLoader, Class<?>... primarySources)

 

**引导初始化**
>public ConfigurableApplicationContext run(String... args)的执行流程,SpringApplication.run方法将负责初始化并启动一个应用

1.启动计时器
```
    StopWatch stopWatch = new StopWatch();
    stopWatch.start();
```
springframework/util中的一个计时器，通过start/stop对一段流程的执行时间进行计时，可以同时对多个任务进行计时，并内置了一些方便的函数对结果进行输出或打印，这里StopWatch主要用于对SpringBoot的引导启动过程进行计时

2.
```
		DefaultBootstrapContext bootstrapContext = createBootstrapContext();
		ConfigurableApplicationContext context = null;
		configureHeadlessProperty();
		SpringApplicationRunListeners listeners = getRunListeners(args);
		listeners.starting(bootstrapContext, this.mainApplicationClass);
```
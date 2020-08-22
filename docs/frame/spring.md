# Spring

## Bean 生命周期

```java
1.实例化    createBeanInstance()
2.属性赋值   populateBean()
3.初始化    initializeBean()
4.销毁      ConfigurableApplicationContext#close()
```

## Bean 拓展点

```java
BeanDefinition
				postProcessBeforeInstantiation				
实例化     					InstantiationAwareBeanPostProcessor extends BeanPostProcessor
  			postProcessAfterInstantiation
属性赋值
  			postProcessBeforeInitialization
初始化 							BeanPostProcessor
  			postProcessAfterInitialization
销毁
```

## 核心模块

```
spring-core   		基础 API 模块，资源管理（Resource），范型处理(GenericTypeResolver)
spring-beans   		spring Bean 相关，依赖查找(beanFactory)，依赖注入(AutowiredAnnotationBeanPostProcessor)
spring-aop 	   		Spring Aop 处理，动态代理,Aop 字节码提升
spring-context 		事件驱动、注解驱动、模块驱动 ，提供辅助功能企业定制化
spring-expression spring 表达式语言模块
```

## IOC

``Inversion of Control``

``好莱坞原则,容器托管对象的声明周期，对象的获取方式反转``

### 职责

- 通用职责
- 依赖处理
  - 依赖查找
  - 依赖注入
- 生命周期管理
  - 容器
  - 托管的资源（Java Beans 或其他资源）
- 配置
  - 容器
  - 外部化配置
  - 托管的资源（Java Beans 或其他资源 ）

## DI

``Dependency injection``

### 实现方式

- 接口注入
- 构造
- setter  【<bean  ---> <property --> <ref 】

## 自动注入

- Autowired
- Inejct
- Resource

## Bean初始化拓展

- postprocessor
- init/destroy method
- postconstruct
- Initializing 
- aware

## 循环依赖

```java
todo
```

# Springboot

## 约定

- Maven 目录结构
- 默认配置文件以及配置属性
- Mvc 依赖，embedded tomcat
- Starter 自动装配

## 自动装配

```
@EnableAutoConfiguration
@EnableAutoConfiguration 扫描使用该注解的类所在包以及包下所有组件
@Import AutoConfigurationImportSelector 选择性批量导入
```

```
META-INF/spring-autoconfigure-metadata.properties 加载条件元数据，过滤
收集所有满足条件的配置类 
@see 
org.springframework.context.annotation
ConfigurationClassPostProcessor.processConfigBeanDefinitions
```

```
去除重复的配置项 list=new list(new linkedset(list))
移除 exclude 
```

```
@interface EnableAutoConfiguration
Auto-configuration is always
applied after user-defined beans have been registered.

Auto-configuration classes are regular Spring
{@link Configuration @Configuration} beans. 
They are located using the {@link SpringFactoriesLoader} 
mechanism (keyed against this class).
Generally auto-configuration beans are
{@link Conditional @Conditional} beans (most often using
{@link ConditionalOnClass @ConditionalOnClass} and
{@link ConditionalOnMissingBean @ConditionalOnMissingBean} annotations).
```

```java
@see SPI
org.springframework.core.io.support
SpringFactoriesLoader
  
public final class SpringFactoriesLoader {
    public static final String FACTORIES_RESOURCE_LOCATION = "META-INF/spring.factories";
    private static final Log logger = LogFactory.getLog(SpringFactoriesLoader.class);
    private static final Map<ClassLoader, MultiValueMap<String, String>> cache = new ConcurrentReferenceHashMap();
}
```

```java
@ConditionOnClass({}) 当有classpath 里面有{} 里面的类时，才将当前配置类注入到容器
@EnableConfigurationProperties 属性配置，按照约定在 application.properteis 配置的环境参数会被加载到相应的 xxxProperties 中
```

```
@Conditional
ConditionalOnBean/ConditionalOnMissiongBean
...
ConditionalOnWebApplicaiton/ConditionalOnNotWebApplication web 应用
ConditionOnJava 指定版本
ConditionOnResouce(resource="/aa.properties")
ConditionalOnProperty(value="xx.xx.xx",havingValue="true",matchifMissing=true) 指定 application.yml 属性
ConditionalOnSingleCandidate 确定单个后端项
```

```
/META-INF/spring-autoconfigure-metadata.properties
降低启动水间，过滤减少配置类的加载数量，这个过滤发生在配置类的装载之前
```


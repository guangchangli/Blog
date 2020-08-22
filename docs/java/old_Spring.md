# Spring

## 上下文和容器

![ApplicationContext](picture-md/ApplicationContext.png)

### BeanFactory

```
定义一些多维度获取 bean 方法，和一些判断 (是否是单例，获取类型以及类型匹配，别名)
```

### ListableBeanFactory

**interface ListableBeanFactory extends BeanFactory **

```java
拓展统计 bean 方法，判断
多唯独获取 beanNames(类型，注解),获取 bean 上注解
boolean containsBeanDefinition(String beanName)
int getBeanDefinitionCount()
  
String[] getBeanDefinitionNames()
  
String[] getBeanNamesForType(@Nullable Class<?> type, boolean includeNonSingletons, boolean allowEagerInit)
<T> Map<String, T> getBeansOfType(@Nullable Class<T> type, boolean includeNonSingletons, boolean allowEagerInit)
			throws BeansException;
String[] getBeanNamesForAnnotation(Class<? extends Annotation> annotationType);

Map<String, Object> getBeansWithAnnotation(Class<? extends Annotation> annotationType) throws BeansException;
<A extends Annotation> A findAnnotationOnBean(String beanName, Class<A> annotationType)
			throws NoSuchBeanDefinitionException;
```

### EnvironmentCapable

```
Environment getEnvironment();
```

### Environment

```
interface Environment extends PropertyResolver
定义获取 activeProfile 和默认的环境配置
判断是否环境配置激活
```

### DefaultListableBeanFactory

```
class DefaultListableBeanFactory extends AbstractAutowireCapableBeanFactory
		implements ConfigurableListableBeanFactory, BeanDefinitionRegistry, Serializable
```















## TODO PropertyResolver
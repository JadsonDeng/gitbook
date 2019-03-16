# Spring11: IOC 之解析自定义标签
---
在[IOC之注册 BeanDefinition](spring5-ioc5-beandefinition.md)中提到：获取 Document 对象后，会根据该对象和 Resource 资源对象调用 registerBeanDefinitions() 方法，开始注册 BeanDefinitions 之旅。在注册 BeanDefinitions 过程中会调用 parseBeanDefinitions() 开启 BeanDefinition 的解析过程。在该方法中，它会根据命名空间的不同调用不同的方法进行解析，如果是默认的命名空间，则调用 parseDefaultElement() 进行默认标签解析，否则调用 parseCustomElement() 方法进行自定义标签解析。前面 6 篇博客都是分析默认标签的解析工作，这篇博客分析自定义标签的解析过程。

默认标签的解析博客如下：
+ [IOC 之解析Bean：解析 import 标签](spring7-ioc7-import.md)
+ [IOC 之解析 bean 标签：开启解析进程](spring8-ioc8-bean-resolve1.md)
+ [IOC 之解析 bean 标签：BeanDefinition](spring9-ioc9-bean-resolve2.md)
+ [IOC 之解析 bean 标签：meta、lookup-method、replace-method](spring10-ioc10-bean-resolve3.md)
+ [IOC 之解析 bean 标签：constructor-arg、property 子元素]()
+ [IOC 之解析 bean 标签：解析自定义标签]()
+ [IOC 之注册解析的 BeanDefinition]()
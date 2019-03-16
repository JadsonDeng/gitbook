# Spring13: IOC 之解析自定义标签
---
在[IOC之注册 BeanDefinition](spring5-ioc5-beandefinition.md)中提到：获取 Document 对象后，会根据该对象和 Resource 资源对象调用 registerBeanDefinitions() 方法，开始注册 BeanDefinitions 之旅。在注册 BeanDefinitions 过程中会调用 parseBeanDefinitions() 开启 BeanDefinition 的解析过程。在该方法中，它会根据命名空间的不同调用不同的方法进行解析，如果是默认的命名空间，则调用 parseDefaultElement() 进行默认标签解析，否则调用 parseCustomElement() 方法进行自定义标签解析。前面 6 篇博客都是分析默认标签的解析工作，这篇博客分析自定义标签的解析过程。

默认标签的解析博客如下：
+ [IOC 之解析Bean：解析 import 标签](spring7-ioc7-import.md)
+ [IOC 之解析 bean 标签：开启解析进程](spring8-ioc8-bean-resolve1.md)
+ [IOC 之解析 bean 标签：BeanDefinition](spring9-ioc9-bean-resolve2.md)
+ [IOC 之解析 bean 标签：meta、lookup-method、replace-method](spring10-ioc10-bean-resolve3.md)
+ [IOC 之解析 bean 标签：constructor-arg、property 子元素](spring11-ioc11-bean-resolve4-construct.md)
+ [IOC 之解析 bean 标签：解析自定义标签](spring12-ioc12-bean-resolve5-custom1.md)
+ [IOC 之注册解析的 BeanDefinition]()

在分析自定义标签的解析之前，我们有必要了解自定义标签的使用。

## 使用自定义标签
---
扩展 Spring 自定义标签配置一般需要以下几个步骤：

1. 创建一个需要扩展的组件
2. 定义一个 XSD 文件，用于描述组件内容
3. 创建一个实现 AbstractSingleBeanDefinitionParser 接口的类，用来解析 XSD 文件中的定义和组件定义
4. 创建一个 Handler，继承 NamespaceHandlerSupport ，用于将组件注册到 Spring 容器
5. 编写 Spring.handlers 和 Spring.schemas 文件

下面就按照上面的步骤来实现一个自定义标签组件。

**创建组件**
该组件就是一个普通的 JavaBean，没有任何特别之处。
```
public class User {
    private String id;

    private String userName;

    private String email;
}
```

**定义XSD文件**
```
<?xml version="1.0" encoding="UTF-8"?>
<xsd:schema xmlns:xsd="http://www.w3.org/2001/XMLSchema"
            xmlns="http://www.cmsblogs.com/schema/user" targetNamespace="http://www.cmsblogs.com/schema/user"
            elementFormDefault="qualified">
    <xsd:element name="user">
        <xsd:complexType>
            <xsd:attribute name="id" type="xsd:string" />
            <xsd:attribute name="userName" type="xsd:string" />
            <xsd:attribute name="email" type="xsd:string" />
        </xsd:complexType>
    </xsd:element>
</xsd:schema>
```
上面除了对 User 这个 JavaBean 进行了描述外，还定义了 `xmlns="http://www.cmsblogs.com/schema/user" targetNamespace="http://www.cmsblogs.com/schema/user"` 这两个值，这两个值在后面是有大作用的。

**Parse类**
定义一个 Parser 类，该类继承 AbstractSingleBeanDefinitionParser ，并实现 getBeanClass() 和 doParse() 两个方法。主要是用于解析 XSD 文件中的定义和组件定义。
```
public class UserDefinitionParser extends AbstractSingleBeanDefinitionParser {

    @Override
    protected Class<?> getBeanClass(Element element) {
        return User.class;
    }
    @Override
    protected void doParse(Element element, BeanDefinitionBuilder builder) {
        String id = element.getAttribute("id");
        String userName=element.getAttribute("userName");
        String email=element.getAttribute("email");
        if(StringUtils.hasText(id)){
            builder.addPropertyValue("id",id);
        }
        if(StringUtils.hasText(userName)){
            builder.addPropertyValue("userName", userName);
        }
        if(StringUtils.hasText(email)){
            builder.addPropertyValue("email", email);
        }

    }
}
```

**Handler类**
定义 Handler 类，继承 NamespaceHandlerSupport ,主要目的是将组件注册到 Spring 容器中。
```
public class UserNamespaceHandler extends NamespaceHandlerSupport {

    @Override
    public void init() {
        registerBeanDefinitionParser("user",new UserDefinitionParser());
    }
}
```

**Spring.handlers**
```
http\://www.cmsblogs.com/schema/user=org.springframework.core.customelement.UserNamespaceHandler
```

**Spring.schemas**
```
http\://www.cmsblogs.com/schema/user.xsd=user.xsd
```

经过上面几个步骤，就可以使用自定义的标签了。在 xml 配置文件中使用如下：
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:myTag="http://www.cmsblogs.com/schema/user"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.cmsblogs.com/schema/user http://www.cmsblogs.com/schema/user.xsd">

    <myTag:user id="user" email="12233445566@qq.com" userName="chenssy" />
</beans>
```

测试
```
public static void main(String[] args){
    ApplicationContext context = new ClassPathXmlApplicationContext("spring.xml");

    User user = (User) context.getBean("user");

    System.out.println(user.getUserName() + "----" + user.getEmail());
}
```


## 解析自定义标签
---



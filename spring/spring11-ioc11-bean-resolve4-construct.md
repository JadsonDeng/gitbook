# Spring11: IOC 之解析 bean 标签：constructor-arg、property 子元素
---
上篇博客[IOC 之解析 bean 标签：meta、lookup-method、replace-method](spring11-ioc11-bean-resolve4-construct.md)分析了 meta 、 lookup-method、replace-method 三个子元素,这篇文章分析 constructor-arg 、property、qualifier 三个子元素。


## constructor-arg 子元素
---
举个小栗子：
```
public class StudentService {
    private String name;

    private Integer age;

    private BookService bookService;

    StudentService(String name, Integer age, BookService bookService){
        this.name = name;
        this.age = age;
        this.bookService = bookService;
    }
}

<bean id="bookService" class="org.springframework.core.service.BookService"/>

<bean id="studentService" class="org.springframework.core.service.StudentService">
    <constructor-arg index="0" value="chenssy"/>
    <constructor-arg name="age" value="100"/>
    <constructor-arg name="bookService" ref="bookService"/>
</bean>
```

StudentService 定义一个构造函数，配置文件中使用 constructor-arg 元素对其配置，该元素可以实现对 StudentService 自动寻找对应的构造函数，并在初始化的时候将值当做参数进行设置。parseConstructorArgElements() 方法完成 constructor-arg 子元素的解析。

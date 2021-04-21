### Aop 面向切面编程

- 静态代理，手写继承或实现

- 动态代理

  - JDK实现：implements InvocationHandler，复写 invoke(Object proxy, Method method, Object[] args)方法

    ```java
    Interface proxy = (Interface) Proxy.newProxyInstance(Interface.class.getClassLoader(), new Class[]{Interface.class},
            new DynamicProxyHandler(object));
    ```
    
  - cglib：子类继承实现，字节码提升，但要注意是否是final类

- 术语

  - 连接点（Joinpoint）可以被增强的方法 add，update，select.....
  - 切入点（Pointcut）实际被增强的方法，条件表达式
  - 通知（Advice）增强的行为
    - 前置通知（befor）@Before 
    - 后置通知（after）@After
    - 返回通知（after-return）@AfterReturning
    - 异常通知（after-throwing）@AfterThrowing
    - 环绕通知（之前之后）@Around
  - 切面（Aspect）如日志切面，事务切面
  - 织入（Weaving）将增强添加到目标类的具体连接点上

- 表达式

  - execution(* com.yz.dao.BookDao.add(..))
  - @Aspect 增强逻辑对于的类上加上

- 顺序

  - 多个切面后，难免会有重复的切点，如果要指定优先级，则可以在增强类上使用 @Order(num)，num越小优先级越高

- aspect xml 配置

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns:context="http://www.springframework.org/schema/context"
         xmlns:aop="http://www.springframework.org/schema/aop"
         xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                              http://www.springframework.org/schema/context   http://www.springframework.org/schema/context/spring-context.xsd
                              http://www.springframework.org/schema/aop  http://www.springframework.org/schema/aop/spring-aop.xsd">
  
      <context:component-scan base-package="aop"></context:component-scan>
      <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
  </beans>	
  ```

- 完全注解开发  配置 @EnableAspectJAutoProxy(proxyTargetClass = true)

  ```java
  package aop;
  
  import org.springframework.context.annotation.ComponentScan;
  import org.springframework.context.annotation.Configuration;
  import org.springframework.context.annotation.EnableAspectJAutoProxy;
  
  @Configuration
  @ComponentScan(basePackages = {"aop"})
  @EnableAspectJAutoProxy(proxyTargetClass = true)
  public class AopConfig {
  
  }
  ```

  ```java
  import aop.AopConfig;
  import aop.Person;
  import org.springframework.context.ApplicationContext;
  import org.springframework.context.annotation.AnnotationConfigApplicationContext;
  import org.springframework.context.support.ClassPathXmlApplicationContext;
  
  public class AopRun {
      public static void main(String[] args) {
  //        case1();
          case2();
      }
      public static void case1() {
          ApplicationContext applicationContext = new ClassPathXmlApplicationContext("aop.xml");
          Person person = applicationContext.getBean("person", Person.class);
          person.sayHello();
      }
  
      public static void case2() {
          ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AopConfig.class);
          Person person = applicationContext.getBean("person", Person.class);
          person.sayHello();
      }
  }
  
  ```

  
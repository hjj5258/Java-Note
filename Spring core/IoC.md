### Spring core

- 将new 对象行为进行反转

- Bean：将所需的Class信息存储为Java对象

  - xml

    ```xml
    <bean id="user" class="entity.User"></bean>
    ```

  - BeanFactory
    
    - Spring内部使用，加载配置文件不会创建对象，在获取时才会创建对象（懒加载）
    
  - 作用域

    - scope

      ```xml
      <bean id="course2" class="entity.Course" scope="singleton">
          <property name="name" value="语文课"></property>
      </bean>
      ```

      - singleton 单例
      - prototype 多个实例，在spring配置文件加载时，不会自动创建，在getBean() 创建
      - ....

  - 生命周期 BeanLifeCycle

    ```xml
    <bean id="beanLifeCycle" class="entity.BeanLifeCycle" init-method="init" destroy-method="destroy">
        <property name="name" value="123"></property>
    </bean>
    ```

    - 1.无参构造器
    - 2.set()
    - 3.postProcessBeforeInitialization 初始化方法之前，实现BeanPostProcessor 接口，复写后，全局bean生效
    - 4.init-method 初始化方法
    - 5.postProcessAfterInitialization 初始化方法之前，实现BeanPostProcessor 接口，复写后，全局bean生效
    - 6.getBean() 获取bean
    - 7.destroy-method 销毁bean

  - ApplicationContext
    - ApplicationContext 继承 BeanFactory接口，扩展更多功能，一般开发都是使用这个
    - 加载配置文件就会创建对象
    - FileSystemXmlApplicationContext 系统路径
    - ClassPathXmlApplicationContext 类路径
    
  - 自动注入 autowire

    ```xml
    <bean id="teacher" class="autowired.Teacher" autowire="byName"></bean>
    <bean id="school" class="autowired.School"></bean>
    ```

    - no、**byName、byType**、constructor

  - 引入外部文件进行注入（context）

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:context="http://www.springframework.org/schema/context"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                                http://www.springframework.org/schema/context  http://www.springframework.org/schema/context/spring-context.xsd">
    <context:property-placeholder location="mysql.properties"></context:property-placeholder>
    <bean id="sqlConntion" class="sql.SqlConntion">
        <property name="driverClass" value="${sql.driverClass}"></property>
        <property name="url" value="${sql.url}"></property>
        <property name="username" value="${sql.username}"></property>
        <property name="password" value="${sql.password}"></property>
    </bean>
    ```

  - annotation 方式进行注入 （component-scan，需要依赖spring aop 包）

    @Component(value = "userService")  // 默认首字母小写userService

    context:include-filter

    context:exclude-filter

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:context="http://www.springframework.org/schema/context"
           xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/context  http://www.springframework.org/schema/context/spring-context.xsd">
        <context:component-scan base-package="packageAnnotation"></context:component-scan>
        
        <!-- 关闭默认的filter， include-filter 包含扫描-->
        <context:component-scan base-package="packageAnnotation" use-default-filters="false">
            <context:include-filter type="annotation" expression="org.springframework.stereotype.Service"/>
        </context:component-scan>
        
        <!-- 关闭默认的filter， include-filter 排除扫描-->
        <context:component-scan base-package="packageAnnotation">
            <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
        </context:component-scan>
    </beans>
    ```

    - 注解类型
      - 对象类型
        - @Autowired 自动装配
        - @Qualifier 指定名称，例如interfa 有多个 impl，需要配合@Autowired一起使用
        - @Resource 默认按类型输出，配置name 按照名称注入
      - 普通类型
        - @Value

- IoC：使用容器进行Bean信息的管理

- DI：注入，简称依赖注入，比如对象中依赖了其他对象

  - set()方法注入

  ```xml
  <bean id="user2" class="entity.User">
      <property name="id" value="1"></property>
      <property name="name" value="zhangsan"></property>
      <property name="age" value="18"></property>
  </bean>
  ```

  - 构造器注入

    ```xml
    <bean id="order" class="entity.Order">
        <constructor-arg name="id" value="1"></constructor-arg>
        <constructor-arg name="name">
            <value><![CDATA[<衣服订单>]]></value>
        </constructor-arg>
        <constructor-arg name="createdTime" >
            <null></null>
        </constructor-arg>
    </bean>
    ```

  - 多个值，array，list，map，set

    ```xml
    <bean id="multipleDi" class="entity.MultipleDi">
        <property name="array">
            <array>
                <value>1</value>
                <value>2</value>
                <value>3</value>
            </array>
        </property>
        <property name="list">
            <list>
                <value>3</value>
                <value>4</value>
                <value>5</value>
            </list>
        </property>
        <property name="map">
            <map>
                <entry key="1" value="6"></entry>
                <entry key="1" value="7"></entry>
                <entry key="1" value="8"></entry>
            </map>
        </property>
        <property name="set">
            <set>
              <value>mysql</value>
              <value>mysql</value>
            </set>
        </property>
    </bean>
    ```


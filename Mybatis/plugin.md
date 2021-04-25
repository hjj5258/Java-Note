###  插件（plugin）

原理：代理机制 

多个插件则是，顺序代理，逆序执行，A、B插件，A先创建代理C，B在根据C创建代理，所以执行顺序 B->A

```java
package com.yz.mybatis.plugin;

import org.apache.ibatis.executor.parameter.ParameterHandler;
import org.apache.ibatis.executor.statement.StatementHandler;
import org.apache.ibatis.plugin.*;
import org.apache.ibatis.reflection.MetaObject;
import org.apache.ibatis.reflection.SystemMetaObject;

import java.sql.Statement;
import java.util.Properties;

@Intercepts({@Signature(type = StatementHandler.class, method = "parameterize", args = Statement.class)})
public class PagePlugin implements Interceptor {
    /**
     * 拦截
     *
     * @param invocation
     * @return
     * @throws Throwable
     */
    public Object intercept(Invocation invocation) throws Throwable {
        System.out.println("PagePlugin intercept method");
        Object target = invocation.getTarget();
        MetaObject metaObject = SystemMetaObject.forObject(target);
        Object value = metaObject.getValue("parameterHandler.parameterObject");
        System.out.println("====value==== :" + value);
        Object proceed = invocation.proceed();
        return proceed;
    }

    /**
     * 包装对象
     *
     * @param target
     * @return
     */
    public Object plugin(Object target) {
        System.out.println("PagePlugin plugin method");
        // this 是当前代理对象
        Object wrap = Plugin.wrap(target, this);
        return wrap;
    }

    /**
     * 将插件注册时的property属性设置
     *
     * @param properties
     */
    public void setProperties(Properties properties) {
        System.out.println("PagePlugin setProperties method");
        System.out.println("插件配置" + properties);
    }
}

```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
    <settings>
        <setting name="logImpl" value="LOG4J"/>
    </settings>
    <typeAliases>
        <package name="com.yz.mybatis.model"/>
    </typeAliases>
    <!-- plugins 要注意放置顺序-->
    <plugins>
        <plugin interceptor="com.yz.mybatis.plugin.PagePlugin">
            <property name="username" value="xxx"/>
            <property name="password" value="123456"/>
        </plugin>
    </plugins>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?serverTimezone=UTC"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>

    <mappers>
        <package name="com.yz.mybatis.mapper"/>
    </mappers>

</configuration>


```


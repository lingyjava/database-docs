# 5·Redis配置

- [5·Redis配置](#5redis配置)
  - [Java整合Redis](#java整合redis)

## Java整合Redis
在Java中使用Redis缓存数据库，将短时间内需要经常查询的数据存到redis数据库，在service层将一次查询结果存储到redis数据库，此后每次先从redis数据库中获取数据，获取失败再通过dao层查询数据库。如果数据发生更新，在更新后把数据存入redis数据库。

1. 导入依赖（redis，spring）
2. 使用spring配置redis对象。
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:tx="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <!--开启注解扫描-->
    <tx:component-scan base-package="com.ly"/>

    <!--配置redis连接池配置对象-->
    <!-- 创建redis配置对象 -->
    <bean id="poolConfig" class="redis.clients.jedis.JedisPoolConfig">
        <property name="maxIdle" value="300" />
        <property name="maxTotal" value="600" />
        <property name="maxWaitMillis" value="10000" />
        <!--保证从连接池获取的redis连接都可用-->
        <property name="testOnBorrow" value="true" />
    </bean>

    <!--配置获取redis数据库连接的工厂类-->
    <!-- 配置redis连接工厂:JedisConnectionFactory  -->
    <bean id="jedisConnectionFactory" class="org.springframework.data.redis.connection.jedis.JedisConnectionFactory">
        <property name="hostName" value="127.0.0.1" />
        <property name="port" value="6379" />
        <!--用几号库-->
        <property name="database" value="0" />
        <!--配置属性-->
        <property name="poolConfig" ref="poolConfig" />
    </bean>

    <!--配置redis模板类-->
    <!-- 配置RedisTemplate -->
    <bean id="redisTemplate" class="org.springframework.data.redis.core.RedisTemplate">
        <property name="connectionFactory" ref="jedisConnectionFactory" />
    </bean>

    <!-- 配置RedisCacheManager
    <bean id="redisCacheManager" class="org.springframework.data.redis.cache.RedisCacheManager">
        <constructor-arg name="redisOperations" ref="redisTemplate" />
    </bean>-->

</beans>
```

3. 序列化实体类。
```java
@Data
public class Student implements Serializable {
    private String name;
    private Integer age;
    private String sex;
}
```

4. 在service层具体实现。
```java
package com.ly.service.serviceImpl;

import com.ly.dao.StudentDao;
import com.ly.pojo.Student;
import com.ly.service.RedisService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.stereotype.Service;

import java.util.ArrayList;
import java.util.List;

/**
 * @author ly
 * @Date: 2021/10/9 10:32
 */
@Service
public class RedisServiceImpl implements RedisService {

    //注入dao层
    @Autowired
    private StudentDao studentDao;
	
    //注入spring中配置的redis对象
    @Autowired
    private RedisTemplate redisTemplate;

    //查询所有学生
    @Override
    public List<Student> queryAllStudent() {
        List<Student> students = new ArrayList<>();
        //先尝试从redis中获取
        students = (List<Student>) redisTemplate.boundValueOps("students").get();
        if (students == null){
            //获取失败,通过dao层查询数据
            students = studentDao.queryAllStudent();
        }
        //将数据存到redis数据库
        redisTemplate.boundValueOps("students").set(students);
        return students;
    }
}

```

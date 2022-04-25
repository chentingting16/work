

#### MyBatis：持久层框架，用于简化JDBC开发

JDBC缺点：1）硬编码； 2）操作繁琐。

mybatis核心配置：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/exercise?useSSL=false&amp;serverTimezone=UTC"/>
                <property name="username" value="root"/>
                <property name="password" value="1164101128"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="com/ctt/mapper/EmpMapper.xml"/>
    </mappers>
</configuration>
```

测试代码：

```java
public class MyBatisDemo {
    public static void main(String[] args) throws IOException {
        /*
        每个基于 MyBatis 的应用都是以一个 SqlSessionFactory 的实例为核心的。
        SqlSessionFactory 的实例可以通过 SqlSessionFactoryBuilder 获得。
        SqlSessionFactoryBuilder 可以从 XML 配置文件或一个预先配置的 Configuration 实例来构建出 SqlSessionFactory 实例。
        */
        String resource = "mybatis-configure.xml";
        InputStream inputStream = Resources.getResourceAsStream(resource);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        
        //可以从SqlSessionFactory中获得SqlSession的实例。
        //SqlSession 提供了在数据库执行 SQL 命令所需的所有方法。可通过SqlSession实例来直接执行已映射的 SQL 语句。
        SqlSession sqlSession = sqlSessionFactory.openSession();
        EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
        mapper.selectAll();
    }
}
```

实体类：

```java
package com.ctt.pojo;
public class Emp {
    int id;
    String ename;
    Date joindate;
    Double salary;
    Double bouns;
    ...
}
```

Mapper接口：

```java
package com.ctt.mapper;
import com.ctt.pojo.Emp;
public interface EmpMapper {
    List<Emp> selectAll();
}
//注：mapper接口与mapper配置要放在一个路径下 
```

Mapper配置文件：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.ctt.mapper.EmpMapper">
    <select id="selectAll" resultType="com.ctt.pojo.Emp">
        select * from emp
    </select>
</mapper>
```




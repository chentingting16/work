### JDBC(Java DataBase Connectivity)：使用java操作关系数据库的API

1. 用同一套Java代码，操作不同的关系数据库。
2. 官方定义操作关系型数据库的规则（接口），各数据库厂商实现该接口(提供数据库驱动jar包)。真正执行的代码是驱动jar包中的实现类。
3. 优点：1）java不需要针对不同数据库分别开发；2）可随时替换底层数据库，java代码可基本不变。

```java
        //1、注册驱动
        Class.forName("com.mysql.jdbc.Driver");

        //2、获取连接
        String url = "jdbc:mysql://localhost:3306/exercise";
        String username = "root";
        String password = "1164101128";
        Connection conn = DriverManager.getConnection(url, username, password);

        //3、sql语句
        String sql = "update emp set ename = 'lisi' where id = 1";

        //4、获取执行sql的对象 Statement 定义了可用于发送SQL或者PL/SQL命令，并从数据库接收数据的方法和属性。
	   //Statement对象
        Statement stmt = conn.createStatement();

        //5、执行sql
        //返回受影响的行数
        int count = stmt.executeUpdate(sql);

        //6、处理结果
        System.out.println(count);

        //7、释放资源
        stmt.close();
        conn.close();
```

#### JDBC API

1. DriverManager 驱动管理对象

   1. 注册驱动

      ```java
      Class.forName(""); //该类包含静态代码块，DriverManager.registerDriver(new Driver());
      ```

   2. 获取数据库连接

      ```java
      Connection conn = DriverManager.getConnection(String url, String user, String password);
      ```

2. Connection 数据库连接对象

   1. 获取执行SQL的对象
   	  * 普通执行SQL对象   Statement createStatement();
   	  * 预编译SQL的执行SQL对象:防止SQL注入   PreparedStatement  prepareStatement(sql);
   	  * 执行存储过程的对象   CallableStatement prepareCall(sql);
   2. 管理事务

   `conn.setAutoCommit()`开启事务

   `conn.commit()`提交事务

   `conn.rollback()`回滚事务

   ```java
   	try {
               //开启事务
               conn.setAutoCommit(false);
   
               //获取Statement对象
               stat = conn.createStatement();
   
               String sql1 = "update emp set ename = 'lisi' where id = 1";
               String sql2 = "update emp set ename = 'haha' where id = 2";
               stat.executeUpdate(sql1);
               stat.executeUpdate(sql2);
   
               conn.commit();
         } catch (SQLException e) {
               conn.rollback();
         }
   ```

3. Statement作用:执行SQL语句

   1. int executeUpdate(sql)：执行DML，DDL语句

      返回值：1）DML影响的行数；2）DDL语句执行后，执行成功也可能返回0

   2. ResultSet executeQuery(sql)：执行DQL

      返回值：ResultSet结果集对象

4. ResultSet 结果集对象

   * boolean next() ：true 当前行有效；false 当前行无数据

     1）将光标从当前位置移动到下一个位置；

     2）判断当前行是否是有效行

   * XXX getXXX(参数)：获取数据

     XXX：数据类型，如 int getInt(参数)、String getString(参数)

     参数： int 列的编号； String 列的名称。

     ```java
     ResultSet resultSet = stmt.executeQuery(sql);
     while(resultSet.next())
     {
          System.out.println(resultSet.getString("ename"));
     }
     resultSet.close();
     ```

5. PreparedStatement作用：预编译SQL语句并执行，防止SQL注入

   1. 预编译，性能更高；

   2. 防止SQL注入：将敏感字符转义。

      

####  数据连接池：是一个容器，负责分配、管理数据库连接(Connection)

* 好处：
  1. 资源重用；
  2. 提升系统响应速度；
  3. 避免数据库连接遗漏。

* 官方(SUN)提供的数据库连接池标准接口，第三方组织实现此接口。

```java
Connetion getConnection()
```

* 常见数据库连接池

  1. DBCP
  2. C3P0
  3. Druid(阿里)

  












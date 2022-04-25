### Web服务器作用:

1. 封装HTTP协议操作，简化开发；
2. 将web项目部署到服务器，对外提供网上浏览服务。
3. Tomcat是一个轻量级Web服务器，支持Servlet/JSP少量JavaEE规范，也称Servlet容器。

### Tomcat使用

文件结构：

![image-20220323100219386](C:\Users\ctt\AppData\Roaming\Typora\typora-user-images\image-20220323100219386.png)



项目结构：

![image-20220323100345677](C:\Users\ctt\AppData\Roaming\Typora\typora-user-images\image-20220323100345677.png)

### Servlet使用

* Servlet是Java提供的一种动态web资源开发技术。

* 使用示例：

![image-20220323100748488](C:\Users\ctt\AppData\Roaming\Typora\typora-user-images\image-20220323100748488.png)

* 执行流程

![image-20220323100813085](C:\Users\ctt\AppData\Roaming\Typora\typora-user-images\image-20220323100813085.png)

* 声明周期

![image-20220323100847049](C:\Users\ctt\AppData\Roaming\Typora\typora-user-images\image-20220323100847049.png)

* Servlet方法

![image-20220323100913257](C:\Users\ctt\AppData\Roaming\Typora\typora-user-images\image-20220323100913257.png)

![image-20220323100934104](C:\Users\ctt\AppData\Roaming\Typora\typora-user-images\image-20220323100934104.png)

![image-20220323100943240](C:\Users\ctt\AppData\Roaming\Typora\typora-user-images\image-20220323100943240.png)

### 使用idea创建mavenWeb项目

1. New Project—— Maven——maven-archetype-webapp

![image-20220331105300950](C:\Users\ctt\AppData\Roaming\Typora\typora-user-images\image-20220331105300950.png)

2. 删除pom.xml中多余坐标

![image-20220331105342593](C:\Users\ctt\AppData\Roaming\Typora\typora-user-images\image-20220331105342593.png)

3. main路径下新建java和resources路径

![image-20220331105414049](C:\Users\ctt\AppData\Roaming\Typora\typora-user-images\image-20220331105414049.png)

#### IDEA中使用Tomcat maven插件


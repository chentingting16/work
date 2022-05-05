# Go介绍

## 特点：

Go保证了既能到达静态编译语言的安全和性能，又达到动态语言开发维护的高效率。Go=C+Python

1. 从C语言继承很多理念，指针等
2. 引入**包**的概念用于**组织程序结构**，Go语言的每个文件都要归属于一个包，不能单独存在
3. **垃圾回收**机制，内存自动回收，不需要开发人员管理
4. **天然并发（重要特点）**：1）语言层面支持并发，实现简单；2）goroutine，轻量级线程，可实现大并发处理，高效利用多核；3）基于CPS并发模型
5. 使用管道机制，实现不同goroutine通信
6. 函数可以返回多个值
7. 创新：如切片slice、延时执行defer等

![image-20220502214111640](C:\Users\ctt\AppData\Roaming\Typora\typora-user-images\image-20220502214111640.png)

![image-20220502214402795](C:\Users\ctt\AppData\Roaming\Typora\typora-user-images\image-20220502214402795.png)

## 开发注意事项

![image-20220502214720451](C:\Users\ctt\AppData\Roaming\Typora\typora-user-images\image-20220502214720451.png)

# 语法

## 变量声明与初始化

```go
var name type = exp
```

注：

1. `type`和`exp`可以省略一个；
2. `type`省略：由`exp`决定
3. `exp`省略（**零值保障机制**）：初始值为对应类型的“零值” 1）数字0、布尔false、字符串“”；2）接口和引用类型nil；3）数组或结构体，所有元素或成员为“零值”。

### 短变量声明

作用：声明和初始化局部变量。

```
name := exp
```

# 数据类型

![image-20220503153003292](C:\Users\ctt\AppData\Roaming\Typora\typora-user-images\image-20220503153003292.png)


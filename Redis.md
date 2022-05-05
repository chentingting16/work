# `Redis`

## [`Redis`和数据库的数据一致性问题](https://www.cnblogs.com/chanmufeng/p/15894471.html)

* 适用于数据读多写少的情况下作为缓存

## 一般查询情况（无数据一致性问题）

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7f7ad5d22f504ed58ab80fb3794638b7~tplv-k3u1fbpfcp-watermark.image?)

![image_1.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/eac95eb80c464183b62afdb41748c0d0~tplv-k3u1fbpfcp-watermark.image?)

```java
public User getUserInfo(String userName) {
      User user = redisCache.getName("user:" + userName);
      if (user != null) {
          return user;
      }
      // 从数据库中直接搜索
      user = selectUserByUserName(userName);
      // 将数据写入Redis，并设置过期时间
      redisCache.set("user:" + userName, user, 30000);
      // 返回数据
      return user;
}
```

## 一致性问题

在`Redis`的key值未过期的情况下，用户修改了个人信息，我们此时既要操作数据库数据，也要操作Redis数据。现在我们面临了两种选择：

1. 先操作`Redis`的数据，再操作数据库的数据
2. 先操作数据库的数据，再操作`Redis`的数据

无论选择哪种方法，最理想的情况下，两个操作要么同时成功，要么同时失败，否则就会出现`Redis`和数据库数据不一致的情况。遗憾的是，目前没有什么框架能够保证`Redis`的数据和数据库的数据的完全一致性。我们只能根据场景和所需要付出的代价来采取一定的措施降低数据不一致出现的概率，在一致性和性能之间取得一个折中。

### 是删除缓存还是更新缓存？

当数据库数据发生变化的时候，`Redis`的数据也需要进行相应的操作，那么这个「操作」到底是用「更新」还是用「删除」呢？

1. ~~先更新缓存，再更新数据库~~  数据库删除失败造成数据不一致性
2. 先更新数据库，再更新缓存

**结论：推荐直接使用「删除」操作**。

先更新数据库，再更新缓存。更新造成的数据不一致：

![image_2.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/eb62e973a5674922acbe226faa52b555~tplv-k3u1fbpfcp-watermark.image?)

此外，推荐使用「删除缓存」还有两点原因。

1. 如果写数据库的场景比读数据场景多，采用这种方案就会导致缓存就被频繁写入，浪费性能；
2. 如果缓存要经过一系列复杂的计算才能得到，那么每次写入数据库后，都再次计算写入的缓存无疑也是浪费性能的。

### （删除缓存）先更新数据库还是后更新数据库？

* 先更新数据库，再删数据库

这种方式可能存在以下两种异常情况

1. 更新数据库失败，这时可以通过程序捕获异常，直接返回结果，不再继续删除缓存，所以不会出现数据不一致的问题
2. 更新数据库成功，删除缓存失败。导致数据库是最新数据，缓存中的是旧数据，数据不一致

第2种情况应该怎么办呢？我们有两种方式：**失败重试**和**异步更新**。

总之，对于删除缓存失败的情况，我们的做法是不断地重试删除操作，直到成功。无论是重试还是异步删除，都是最终一致性的思想。

* 先删除缓存，再更新数据库（延迟双删）

![image_5.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bee4d1f2036e48d7926d6a299c9959c0~tplv-k3u1fbpfcp-watermark.image?)




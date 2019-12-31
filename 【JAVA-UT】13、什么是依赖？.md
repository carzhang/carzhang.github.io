**1、什么是依赖？**

一个类A，使用另一个类B，则说A依赖B。也说B是A的依赖(dependency)。

**2、为什么在单元测试中要消除依赖？**

因为依赖会增加资料开销，会降低代码重用性，。

例如，一个类NameChecker, 检查名字是否在某一服务器上。首先，它需要从服务器上获取名字。然后，进行比较。

从服务器获取名字的功能，假设由类NameLoader提供。

这时NameLoader就是NameChecker的依赖。

如果在单元测试中，不消除依赖，则需要使用NameLoader去连接服务器。

首先，找到一个服务器，预先存一些名字进去；

然后，连接这个服务器，得到这些名字；

最后，检查这串名字有没有指定的名字。

其中，这个服务器得专门为这个UT而存在。如果哪天服务器不在了，或者里面的数据被修改了，ut就不能如期运行了。

有这样的服务器吗？

即使有，也太奢侈了。

想象一下，如果几百个UT需要服务器呢！



假设让你为NameChecker写单元测试，你会怎么做呢？

```
public class NameChecker {
    private NameLoader nameLoader;

    public NameChecker(NameLoader loader_in) {
        nameLoader = loader_in;
    }

	// to check if server has name.
    public boolean isNameInServer(String addr, String name) {
        String nameInServer = nameLoader.download(addr);
        if (nameInServer == name) {
            return true;
        }
        return false;
    }
}
```

```
public class NameLoader {
    public String download(String addr) {
        //connect server by addr
        //then retrun value getting from server
        return "abc";
    }
}
```

将介绍两种方法，一种叫stub，一种是mock，并将重点介绍mock。




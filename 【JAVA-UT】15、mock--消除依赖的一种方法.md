文|码术张

消除依赖的另一种方法是：使用mock。

本节主题是在于mock可消除依赖 。至于mock的使用技巧，将在后面章节论述。

为下面这个类写ut。

```
public class NameChecker {
    private NameLoader nameLoader;

    public NameChecker(NameLoader loader_in) {
        nameLoader = loader_in;
    }

    public boolean isNameInServer(String addr, String name) {
        String nameInServer = nameLoader.download(addr);
        if (nameInServer == name) {
            return true;
        }
        return false;
    }
}
```

这个类的功能是，检查一个名字是不是在服务器上。addr是服务器地址，name是被检查的名字。

它使用到了类NameLoader。NameLoader提供获取服务器数据的功能。

```
public class NameLoader {
    public String download(String addr) {
        //connect server by addr
        //then retrun value getting from server
        return "abc";
    }
}
```



NameChecker依赖于类NameLoader。

如何在ut中使NameChecker不依赖于NameLoader呢？

用mock！

ut如下：
![1577789212230](C:\Users\carzhang\AppData\Roaming\Typora\typora-user-images\1577789212230.png)

1、2两处即是在使用mock。

这样的ut便摆脱了对NameLoader的依赖。不但不需要去连接服务器获取名字，而且NameLoader是如何实现的，也无需关注，即使将来NameLoader的实现改变了，这个ut也不用改变。



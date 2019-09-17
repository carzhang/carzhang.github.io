文|码术张

一个ut只测一个类。当被测类依赖其他类时，可消除依赖，使测试顺利进行。

消除依赖的一种方法是：使用mock。

本节主题在于使用mock可消除依赖 。至于mock的使用技巧，将在后面章节论述。

为下面这个类写ut。

```
public class NameChecker {
    private NameLoader nameLoader;
    public NameChecker(NameLoader in) {
        nameLoader = in;
    }

    public boolean nameIsOnServer(String addr, String name) {
        List nameList = nameLoader.download(addr);
        if (nameList.contains(name)) {
            return true;
        }
        return false;

    }
}
```

这个类的功能是，检查一个名字是不是在服务器上。addr是服务器地址，name是被检查的名字。

它使用到了类NameLoader。NameLoader提供获取服务器数据的功能。

NameChecker依赖于类NameLoader，但NameChecker的ut，又不应当依赖于NameLoader。

如何用mock实现？

ut如下：
![mock使用实例.png](https://upload-images.jianshu.io/upload_images/8093186-d6c8d5882c0daee0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


1、2两处即是在使用mock。

这样，NameChecker的ut，便摆脱了对NameLoader的依赖。NameLoader是如何实现的，无需关注。即使将来NameLoader的实现改变了，这个ut也不用改变。

运行结果如下：

![运行结果.png](https://upload-images.jianshu.io/upload_images/8093186-b00c6cab0149e8f4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

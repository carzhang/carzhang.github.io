埃德蒙对着下面这个类，皱着眉头，不知如何下手。

```
public class TimeZoneActivator {

    public void activate(String newConfigs, String oldConfigs) {

        if (newConfigs == oldConfigs) {
            System.out.print("configs are same.");
            return;
        }
        else {
            System.out.print("configs arenot same.");
            activateConfigs(newConfigs);
        }
    }

    public void activateConfigs(String newConfigs) {
        //do something
    }
}
```

这个类有一个方法activate，其功能是使配置生效。

activate会比较新配置与旧配置，如果不同，就激活新配置。

那如何写ut来验证是否调用了activateConfigs ？

这个类就是被测对象，并不是被测对象的辅助类，所以也不能使用mock。

怎么办？埃德蒙皱着眉头。

##### 一、如何使用spy

海黛见了，微微一笑，轻轻地说：

`真实对象的行为有没有被调用？mockito的spy方法能回答这一问题。`

埃德蒙很开心，沿着这个思路，最终写出了如下ut:

```
public class TimeZoneActivatorTest {
    @Test
    public void should_active() {
        // given
        TimeZoneActivator timeZoneActivator = spy(new TimeZoneActivator());

        String newConfig = "(GMT+8) Asia/Macao";
        String oldConfig = "(GMT+8) Asia/Shanghai";

        // when
        timeZoneActivator.activate(newConfig, oldConfig);

        // then
        verify(timeZoneActivator).activateConfigs(anyString());
    }
}
```

运行结果如下：

![1568106513227](C:\Users\carzhang\AppData\Roaming\Typora\typora-user-images\1568106513227.png)

这个ut，首先调用spy方法，获得一个spy对象。

`        TimeZoneActivator timeZoneActivator = spy(new TimeZoneActivator());`

然后调用activate方法。两个参数的内容是不一样，那么方法activateConfigs被调用的条件是满足的：

`        timeZoneActivator.activate(newConfig, oldConfig);`

最后，验证activateConfigs方法有没有被调用：

`verify(timeZoneActivator).activateConfigs(anyString());`

这就是spy对象的神奇之处。它好像是真实对象的记录仪。真实对象做了什么，可以问它。

注意运行结果，它的内容有“configs arenot same.”，表明代码被执行了。

将spy换成mock，比较一下运行结果，就会更清楚spy是什么。

##### 二、spy与mock的比较

将上面的ut，使用mock改写如下：

```
public class TimeZoneActivatorTest {
    @Test
    public void should_active() {
        // given
        TimeZoneActivator timeZoneActivator = mock(TimeZoneActivator.class);

        String newConfig = "(GMT+8) Asia/Macao";
        String oldConfig = "(GMT+8) Asia/Shanghai";

        // when
        timeZoneActivator.activate(newConfig, oldConfig);

        // then
        verify(timeZoneActivator).activateConfigs(anyString());
    }
}
```

注意，现在timeZoneActivator不是spy对象，而是一个mock对象。

在这个mock对象上调用activate，当两个参数内容不同时，会不会调用activateConfigs？

运行结果如下：

![1568108796184](C:\Users\carzhang\AppData\Roaming\Typora\typora-user-images\1568108796184.png)

也就是说，activateConfigs没有被调用。

而且从结果看，字符串“configs arenot same.”也没有打印。

这都说明，在mock对象上调用activate，实际上这个方法不会被调用。

而spy会调用。

这就是mock与spy的不同。
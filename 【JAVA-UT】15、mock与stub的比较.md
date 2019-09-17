文|码术张

本节通过比较stub与mock，加深对stub、mock的认识。

### 1、相同点

都可用于消除被测类对其他类的依赖。

如前两节，对同一个类分别使用了stub和mock方式消除依赖，达到同样的测试效果。

### 2、不同点

#### 2.1 实现方式不同

在《什么是stub》中，依赖是NameLoader，ut中实际使用的是NameLoaderStub。

NameLoaderStub是开发者一行行写出来的。

同样的依赖NameLoader，在《mock--消除依赖的一种方法》一文里的ut中，实际使用的是nameLoader。

`			nameLoader = mock(NameLoader.class);`			

它是Mockito mock类的一个实例。

#### 2.2 测试类型不同

使用stub的ut，只能做状态测试；

使用mock的ut，既可做状态测试，也可做行为测试。

##### 2.2.1 状态测试

什么是状态测试(**state verification**)？

即检查行为的结果。

例如，下面代码片段：

```
        boolean result = nameChecker.nameIsOnServer(ladp_addr, name);
        
        assertTrue(result)
```

assertTrue(result)的作用是验证result是否为true。result是行为nameIsOnServer的结果。

##### 2.2.2 行为测试

什么是行为测试(**behavior verification**)？

即测试是否存在某个行为。

举例如下。

NameChecker类中有一个方法nameIsOnServer。此方法用于检查一个名字是不是记录在服务器中。addr是服务器地址，name是被检查的名字。

如果name不在服务器中，则返回false，并且报警。

```
public class NameChecker {
    private NameLoader nameLoader;
    private Alarmer alarmer;
    
    public NameChecker(NameLoader loader_in, Alarmer alarmer_in) {
        nameLoader = loader_in;
        alarmer = alarmer_in;
    }

    public boolean nameIsOnServer(String addr, String name) {
        List nameList = nameLoader.download(addr);
        if (nameList.contains(name)) {
            return true;
        }
        alarmer.reportAlarm("name is not in server.");
        return false;
    }
}
```

报警方式是调用Alarmer的reportAlarm方法。reportAlarm方法的具体实现，不用关心。

```
public class Alarmer {

    public void reportAlarm(String text){
        ......
    }
}
```

现在为NameChecker类写ut。

ut面临两个依赖项NameLoader、Alarmer。

使用mock方法消除这两项依赖。

ut如下：

```
public class NameCheckerTest {
    private NameChecker nameChecker;
    private NameLoader nameLoader;
    private Alarmer     alarmer;

    @Before
    public void setUp() {
        nameLoader = mock(NameLoader.class);
        alarmer    = mock(Alarmer.class);
        nameChecker = new NameChecker(nameLoader, alarmer);
    }

    @Test
    public void shouldReportAlarm(){
        List<String> nameList = new ArrayList<String>();
        nameList.add("a");
        nameList.add("b");
        when(nameLoader.download(anyString())).thenReturn(nameList);

        String ladp_addr = "ldap://10.56.78.23:636";
        String name = "c";

        boolean result = nameChecker.nameIsOnServer(ladp_addr, name);

        assertFalse(result);
        verify(alarmer, times(1)).reportAlarm(anyString());
    }
}
```

运行结果如下：
![运行结果.png](https://upload-images.jianshu.io/upload_images/8093186-eb0067ced8dede6e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


`verify(alarmer, times(1)).reportAlarm(anyString());`

这verify语句的意思是，期望alarmer的reportAlarm方法，被调用了一次，参数为string类型。

这里只是验证reportAlarm这一行为是否被调用。而不是验证这一行为的结果如何。这就是行为验证。



##### 参考：

<https://www.martinfowler.com/articles/mocksArentStubs.html>


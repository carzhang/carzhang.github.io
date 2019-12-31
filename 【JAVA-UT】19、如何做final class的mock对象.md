文|码术张

这几天，埃德蒙的ut工作很顺利，风平浪静。

代码覆盖率不断提高，犹如正在加热的水温一样。
不过在水沸腾前，还要克服几个困难。
今天他遇到了一个困难：

如何mock一个final类？

NameLoader是一个final类，也就是一个不能被继承的类。

它有一个方法download, 用来从服务器上获取一个字符串。

参数addr是服务器地址。

假设服务器上只保存一个字符串。

```
public final class NameLoader{

    public String download(String addr) {
        String name = "";
        //...some code here
        return name;
    }
}


```

NameChecker类的方法nameIsOnServer用于检查一个字符串name是否在服务器上。

addr是服务器地址。

name是被检查的字符串。

它会用到NameLoader的download方法。

```
public class NameChecker{
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

他写出如下ut：

```
public class NameCheckerTest {
    private NameChecker nameChecker;
    private NameLoader nameLoader;

    @Before
    public void setUp() {
        nameLoader = mock(NameLoader.class);
        nameChecker = new NameChecker(nameLoader);
    }

    @Test
    public void shouldTrue(){
        String name_in_server = "zhang";
        when(nameLoader.download(anyString())).thenReturn(name_in_server);

        String server_addr = "ldap://10.56.78.23:636";
        String name = "zhang";

        boolean result = nameChecker.isNameInServer(server_addr, name);

        assertTrue(result);
    }

}
```

可是，运行失败：

![1577791601333](C:\Users\carzhang\AppData\Roaming\Typora\typora-user-images\1577791601333.png)

因为不能mock final class。

而NameLoader是一个final类

怎么办？

首先，在路径src/test下添加路径resources/mockito-extensions

其次，添加一个text文件，文件名为*org.mockito.plugins.MockMaker*

最后，在这个文件中，增加一条语句：mock-maker-inline

![1577791847330](C:\Users\carzhang\AppData\Roaming\Typora\typora-user-images\1577791847330.png)

![1577791878097](C:\Users\carzhang\AppData\Roaming\Typora\typora-user-images\1577791878097.png)

其他不变，你运行试试！

这方法是从Mockito v2开始引入的。

（参考Mock the unmockable: opt-in mocking of final classes/methods，<https://github.com/mockito/mockito/wiki/What%27s-new-in-Mockito-2>）




文|码术张

本系列所使用的mock是Mockito的mock。

Mockito是一个优秀的mocking framework。

使用mock这一原料做一份单元测试的菜时，总体上分为3个步骤。

以下面这个类为例:

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

NameChecker的方法nameIsOnServer，检查名字是否在服务器中。

NameChecker有一个合作伙伴：NameLoader。

NameLoader从server上下载名字。它的参数addr和name, 分别表示服务器地址和待检查的名字。

ut如下;

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
    public void shouldTrue() {
        when(nameLoader.download("123")).thenReturn("zhang");

        boolean result = nameChecker.isNameInServer("123", "zhang");

		verify(nameLoader, times(1)).download(anyString());
        assertTrue(result);
    }
}
```

***步骤1：调用Mockito mock语句，生成mock对象***

```
nameLoader = mock(NameLoader.class);
```

意思是生成NameLoader的mock对象。

***步骤2：调用Mockito when语句，设置mock对象的返回值***

`when(nameLoader.download(anyString())).thenReturn("zhang");`

意思是，mock对象nameLoader的download方法被调用、且参数为String类型时，返回值为"zhang"。

**步骤3：调用assert语句或Mockito verify语句，验证mock对象的调用情况**

```
verify(nameLoader, times(1)).download(anyString());
assertTrue(result);
```

verify用来验证nameLoader的download方法是否被调用了一次，且其参数为String类型。

assertTrue用来验证result的值是否为True。














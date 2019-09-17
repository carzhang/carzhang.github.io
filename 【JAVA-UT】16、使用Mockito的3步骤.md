文|码术张

如何使用Mockito mock？

使用mock的过程，好比做一颗假牙。

装假牙的过程是怎样的呢？让我们跟着埃德蒙一起走进牙科医院。

医生诊断出埃德蒙的一颗牙已坏，需用假牙替换掉它。

一星期后，假牙做好了，装入埃德蒙的口中。做了几次咬合动作，又被取出，打磨打磨。再装进去。

直到医生和病人认为满意为止。

整个配假牙的过程是：1 做假牙，2 配置假牙， 3 验证功能。

让我们跟着埃德蒙走出医院，走进下面这个类:

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

NameChecker类中有一个方法nameIsOnServer，用于检查一个名字是否在服务器中。

nameIsOnServer有两个参数，addr是服务器地址，name是待检查的名字。

如果name不在服务器中，则返回false，并且报警。

下面的这段ut，是对报警功能的验证。

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

        nameChecker.nameIsOnServer(ladp_addr, name);
        
        verify(nameLoader, times(1)).download(anyString());
        verify(alarmer, times(1)).reportAlarm(anyString());
    }
}
```

NameLoader和Alarmer是与NameChecker合作的类。在ut中，一般需要使用假的合作类，以摆脱对它们的依赖，达到测试当前类的目标。

***步骤1：调用Mockito mock语句***

```
nameLoader = mock(NameLoader.class);
alarmer    = mock(Alarmer.class);
```

这就好比是做假牙。

假牙=mock(真牙)

**步骤2：调用Mockito的when语句**

`when(nameLoader.download(anyString())).thenReturn(nameList);`



**步骤3：调用Mockito的verify语句**

```
verify(nameLoader, times(1)).download(anyString());
verify(alarmer, times(1)).reportAlarm(anyString());
```

这就是验证牙齿的功能。

期望nameLoader的download方法被调用一次，alarmer的reportAlarm方法被调用一次。

实际被调用了吗？

verify语句可以检查出来。若符合期望，则ut状态就是pass；不符合，则fail。














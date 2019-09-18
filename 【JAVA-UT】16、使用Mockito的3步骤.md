文|码术张

Mockito是一个优秀的mocking framework。

如何使用Mockito ？

`import static org.mockito.Mockito.*;`

在import后，就进入了Mockito大厅，接下来就可享用Mockito提供的美食了。

享用mockito的过程，一般分为3个步骤。

以下面这个类为例:

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

NameChecker有两个合作伙伴：NameLoader和Alarmer。

NameLoader提供从server上下载名字的功能；Alarmer提供报警功能。

NameChecker有方法nameIsOnServer。此方法用于检查名字是否在服务器中。

它有两个参数，addr和name, 分别表示服务器地址和待检查的名字。

若名字不在服务器中，会报警。

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

***步骤1：调用Mockito mock语句***

```
nameLoader = mock(NameLoader.class);
alarmer    = mock(Alarmer.class);
```

其表达的是，分别将一个mock对象作为NameLoader和Alarmer的实例，而不使用真实的实例。

***步骤2：调用Mockito when语句***

`when(nameLoader.download(anyString())).thenReturn(nameList);`

其表达的是，nameLoader对象的download方法被调用、且参数为String类型时，将nameList作为这个方法的返回值。

**步骤3：调用Mockito verify语句**

```
verify(nameLoader, times(1)).download(anyString());
verify(alarmer, times(1)).reportAlarm(anyString());
```

verify用于验证期望nameLoader的download方法，alarmer的reportAlarm方法是否被调用了一次，而且参数是String类型。

以上3步，是Mockito 语句的使用常见过程。












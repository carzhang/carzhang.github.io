文|码术张

约翰接到一个任务，为下面这个类写ut。

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

这个类的功能为检查一个名字是否在服务器上。addr是服务器地址，name是被检查的名字。

约翰认为很简单。

他的思路是：

首先，找到一个服务器，预先存一些名字进去；

然后，连接这个服务器，得到这些名字；

最后，检查这串名字有没有指定的名字。

要解决的第一个问题是，找到一个服务器。这个服务器还要满足条件：专门为这个UT而存在。因为ut会在jenkins上运行，每天运行。如果服务器被别人拿去用了，或者里面的数据被修改了，ut就不能如期运行了。

能找到符合这种条件的服务器吗？

即使有，这也太不合理了吧，太浪费了。

于是约翰将这方法上面画个了叉。

有没有其他办法？

他不知道。于是去请教珍妮。

珍妮一听，答道:  stub。

约翰问：什么是stub ？ 怎样用呢？ 

珍妮写出下面这个类，告诉约翰，这就是一个stub：

```
public class NameLoaderStub extends NameLoader {

    @Override
    public List download(String addr) {
        List<String> nameList = new ArrayList<String>();
        nameList.add("a");
        nameList.add("b");
        nameList.add("c");
        return nameList;
    }
}
```

原来的download方法被override。根据ut的目标，重写成特定的样子。

在ut中，使用NameLoaderStub，而不是NameLoader。

ut 如下：

```
public class NameCheckerTest {
    private NameChecker nameChecker;

    @Before
    public void setUp() {
        NameLoader nameLoader = new NameLoaderStub();
        nameChecker = new NameChecker(nameLoader);
    }

    @Test
    public void shouldTrue(){
        String ladp_addr = "ldap://10.56.78.23:636";
        String name = "c";
        boolean result = nameChecker.nameIsOnServer(ladp_addr, name);

        assertTrue(result);
    }

}
```

约翰一看，拍手称赞。他将ut补充完整后，总结如下：

当被测试的类A需要协作的类B、C、D时，使用协作类的stub类。一个stub类，它的行为是预先安排好了的。


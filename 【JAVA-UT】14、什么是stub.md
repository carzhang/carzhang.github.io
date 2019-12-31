文|码术张

约翰接到一个任务，为NameChecker类写ut。

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

```
public class NameLoader {
    public String download(String addr) {
        //connect server by addr
        //then retrun value getting from server
        return "abc";
    }
}
```



NameChecker用于检查一个名字是否在服务器上，addr是服务器地址，name是被检查的名字。

约翰的思路是：

首先，找到一个服务器，预先存一些名字进去；

然后，连接这个服务器，得到这些名字；

最后，检查这串名字有没有指定的名字。

其中，这服务器得专门伺候这个UT。如果服务器被别人拿去用了，或者里面的数据被修改了，ut就要罢工了。

可以申请这样的服务器吗？

即使可以，也太浪费了。

于是约翰将这方法上面画个了叉。

有没有其他办法？

他不知道。

于是去请教珍妮。

珍妮微笑道:  stub。

约翰问：什么是stub ？ 怎样用呢？ 

珍妮回答说，这就是一个stub：

```
public class NameLoaderStub extends NameLoader {

    @Override
    public List download(String addr) {
        return "zhang";
    }
}
```

NameLoader的download方法被override塑造成特定的模样，以切合ut的目标。

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
        String name = "Lee";
        
        boolean result = nameChecker.nameIsOnServer(ladp_addr, name);

        assertTrue(result);
    }

}
```

约翰一看，拍手称赞。

将ut补充完整后，他总结如下：

当被测试的类A需要协作的类B、C、D时，使用协作类的stub类。一个stub类，它的行为是通过override预先彩排好的。


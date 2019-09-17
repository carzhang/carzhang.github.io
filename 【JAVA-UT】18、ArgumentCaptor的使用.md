有时，不仅要验证对象的方法是否被调用，还要检查传给方法的参数是否正确。

今天，埃德蒙的工作是给类DownloadActive写ut。

DownloadActive的功能是下载软件，并将成功与否的结果通知Listener.

doTask首先做下载的动作，然后发通知。

发通知，即调用listener的report方法。

为了方便说明，将doTask简化，只是通知侦听者们一个字符串“OK”。

```
public class DownloadActive {
    private Listerner listerner;

    public DownloadActive(Listerner in) {
        listerner = in;
    }

    void doTask(String request) {
        // do something

        notifyListeners("OK");
    }


    public void notifyListeners(String status) {
        listerner.report(status);
    }
}
```

```
public class Listerner {
    void report(String notification) {
		// do something
    }
}
```

埃德蒙写出的ut如下：

```
public class DownloadActiveTest {
    private DownloadActive downloadActive;
    private Listener listener;

    @Before
    public void setUp() {
        listener = mock(Listener.class);
        downloadActive = new DownloadActive(listener);
    }

    @Test
    public void should_sendFeadback(){
        //given
        String request = "download";

        //when
        downloadActive.doTask(request);

        //then
        verify(listener).report(anyString());
    }
}
```

可是，代码review时，海黛的评价是-1，不能提交，需要修改。

哪里需要修改？

少一项要验证的功能。

当前这ut验证了给listener发通知的功能。

通知的内容是不是期望的内容，也需要验证。

比如按规定，成功时给对方一个大写的OK，如果给的是小写的ok，那功能就有缺陷。

埃德蒙表示理解，开始修改ut。

他写下今日工作目标：验证参数内容。

如何实现呢？

海黛告诉他，使用ArgumentCaptor。

**ArgumentCaptor是Mockito中用于捕获传递给mock对象的参数的工具。**

首先捕获参数，然后对其内容进行验证。

沿着这一思路，埃德蒙最终写出的ut如下：

```
public class DownloadActiveTest {
    private DownloadActive downloadActive;
    private Listener listener;
    private String NOTIFICATION = "OK";

    @Before
    public void setUp() {
        listener = mock(Listener.class);
        downloadActive = new DownloadActive(listener);
    }

    @Test
    public void should_sendSpecifiedNotification(){
        //given
        String request = "download";

        //when
        downloadActive.doTask(request);

        //then
        ArgumentCaptor<String> ac = ArgumentCaptor.forClass(String.class);
        verify(listener).report(ac.capture());
        String statusReport = ac.getValue();
        assertEquals(statusReport, NOTIFICATION);
    }

}
```

现在这ut就不仅验证了发通知的功能，还验证了通知的内容是否符合要求。

通知的内容是一个字符串“OK”，其中两个字母都是大写。

验证发送的内容是不是“OK”，步骤如下：

**第1步**：创建一个捕获器。

```ArgumentCaptor<String> ac = ArgumentCaptor.forClass(String.class);```

forClass方法，用于生成一个ArgumentCaptor对象。

因为report的参数是String类型，所以，forClass的参数也是String。

**第2步**：捕获参数

```verify(listener).report(ac.capture());```

capture方法用于获得传给mock对象的方法。

使用方法就如这个例子所示，与verify一起使用。

**第3步**：获取参数内容

```        String statusReport = ac.getValue();```

getValue方法用于从捕获器ArgumentCaptor中取出捕获的内容。

**第4步**：验证参数内容 

```assertEquals(statusReport, NOTIFICATION);```

比较statusReport的内容与期望的"OK"是否相等。



注意：

如果report方法被调用多次，ArgumentCaptor会捕获每一次调用的参数。

ArgumentCaptor的getValue方法，会返回最后一次调用的参数。

通过在doTask方法中增加一行代码`notifyListeners("NOK");`就可看到这一效果。




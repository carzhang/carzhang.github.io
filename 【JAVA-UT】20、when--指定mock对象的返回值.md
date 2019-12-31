![1577696818271](C:\Users\carzhang\AppData\Roaming\Typora\typora-user-images\1577696818271.png)



真实对象有自己的行为；mock对象没有。

于是拿到输入后，真实对象有输出，mock对象没有输出。

为了让mock对象模拟真实对象，就要给mock对象一个输出。

在Mockito中，如何给mock对象一个输出呢？

这就要用到when语句。

when语句常用的4种类型，一一介绍如下。

**1 when(mockObject.methodA(input1)).thenReturn(output1)**

意思是当mock对象的方法A遇到输入input1时，输出将是output1。

假如NameLoader有一个方法download：

`public String download(String addr)`

输入是一个server地址，输出是从server上取得的数据。

在ut中，首先生成一个mock对象：

```
nameLoader = mock(NameLoader.class);
```

nameLoader是NameLoader的mock对象。

然后，设置输出。

假设当输入为"http://10.0.1.1"时，让输出为“abc”，相应的when语句如下：

`when(nameLoader.download("http://10.0.0.1")）.thenReturn(“abc”);`

也可以使用matchers作为参数：

`when(nameLoader.download(anyString())).thenReturn(“abc”);`



**2 when(mockObject.methodA(input1)).thenReturn(output1).thenReturn(output2)**

意思是mock对象的某一方法第一次调用时，返回值为output1；第二次及更多次调用时，返回值为output2。

下面是工作中的一个例子。

console两次读入input后，不论input是什么，第一次返回"abc"，第二次返回"all"。

![img](C:\Users\carzhang\AppData\Local\YNote\data\350537540@163.com\c3c2bce7a8634754beae7db9e93126be\clipboard.png)



**3.when(mockObject.methodA(input1)).thenThrow(output1)**

当调用mock对象的方法A时，抛出一个异常。

 when(mock.someMethod("some arg")).thenThrow(new RuntimeException());



**4 doRetrun(output).when(mockObject).methodA(input)**

意思是当调用mock对象的方法A时，返回output。

在以下两种情形下，这种方式与方式1是不同的。

<1>When spying real objects and calling real methods on a spy brings side effects

KeystoreVisitor spyKeyStoreVisitor = spy(keystoreVisitor);

错误的设置返回值的方法，这时会真的去执行readKeystore，而不是返回keystore。

when(spyKeyStoreVisitor.readKeystore(any())).thenReturn(keystore);

正确的方法如下：

doReturn(keystore).when(spyKeyStoreVisitor).readKeystore(any());

这时不会调用真实的方法。

<2>Overriding a previous exception-stubbing:

```
when(mock.foo()).thenThrow(new RuntimeException());

//Impossible: the exception-stubbed foo() method is called so RuntimeException is //thrown.
when(mock.foo()).thenReturn("bar");

//You have to use doReturn() for stubbing:
doReturn("bar").when(mock).foo();
```

> <https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html#when-T->




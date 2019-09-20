本文将回答：如何指定一个mock对象的行为?

使用mock对象，可以验证对象是否做了某一行为。

待验证的行为，是mock对象的行为。

mock对象的行为，由when语句配置。

如果ut中需要使用mock对象的downLoad行为，那么，就为它配置这个行为。

![1568950071096](C:\Users\carzhang\AppData\Roaming\Typora\typora-user-images\1568950071096.png)

如图所示，真实对象会做一些动作；mock对象仅仅有输入输出。

假设输入是server地址，真实对象的行为是连接server并获取数据，输出是取得的数据。

那么mock对象是不会去连接server并取数据的。它有输入输出，看上去是有行为的。

when所做的，就是配置mock对象的输入输出。

若把真实对象看作一个人，mock对象则是它的模仿者。被模仿的行为，由when语句指定。

when好比教练，帮助mock对象模仿一些行为。一共有下面四位教练：

**1** when(something).thenReturn(somethingElse)

假如NameLoader有一个方法download：`public String download(String addr)`

而这方法在ut中会被执行，又不想真的被执行，那么就可

```
nameLoader = mock(NameLoader.class);
when(nameLoader.download(anyString())).thenReturn(“abc”);
```

nameLoader是NameLoader的mock对象。

NameLoader有一个方法download：`public String download(String addr)`

这方法将在ut中被调用。

而执行的mock对象的代码，那么

when语句指定当调用方法download时，输入是String，输出是一个字符串。

这样指定后，当单元测试代码执行到NameLoader的download方法时，就会

**2** when(something).thenReturn(somethingElse).thenReturn(somethingElse)

**3** doRetrun

**4** doThrow
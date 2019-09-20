如何指定一个mock对象的行为?
使用mock对象，可以进行行为验证。
待验证的行为，是mock对象的行为。
如果把真实对象看作一个人，mock对象则是它的模仿者。
要模仿的行为，由when语句指定。when好比模仿者的教练，帮助mock对象模仿一些行为。
when教练，是一个教练团。
下面分别介绍四位教练：
1 when(something).thenReturn(somethingElse)

2 when(something).thenReturn(somethingElse).thenReturn(somethingElse)

2 doRetrun

3 doThrow
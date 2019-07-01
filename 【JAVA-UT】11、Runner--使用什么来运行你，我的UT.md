文|码术张

#####一、什么是Runner
下面这个UT，对您来说，现在应该是小菜一碟了。

```
public class ResultTest {
  @Test
  public void should_returnString() {
    // given
    String expectedString = "downLoadStatus: Fail, failureReason: cannot connect";
    Result result = new Result("Fail", "cannot connect");

    // when
    String realString = result.toString();

    // then
    Assert.assertEquals(expectedString, realString);
  }
}
```
given、when、then下的语句，各自描述了：
条件的设置；
执行被测功能；
结果是否符合期望的验证。

不过，这还不是全部。
在这个“家庭”里，还有一个特别重要的成员。
是谁呢？
Runner。

每一个测试类，都有一个Runner。
什么是Runner？
Runner是"编译器"，也是"CPU"。

它是“编译器”，是因为Runner会做这两件事：
**1，预处理。**
判断语法是否规范。
比如@Test注释的方法，其返回值应当是void。
若违反了要求，将这“作品"退回作者，并附上评语。
如果合格，进入下一处理阶段。
**2，“翻译”。**
将测试类进行分解、排序。
分解为一个个执行单元，并排序。
这就好比将A语言的源程序翻译为B语言的等价程序。

它是"CPU"，是因为Runner的有一个方法，
**run方法**
`runner.run()`
调用这个方法后，代码才开始进入运行阶段。
这就是为什么称它为runner的原因吧。

#####二、如何使用Runner
Junit提供了三种Runner：
**BlockJUnit4ClassRunner**
**Suite**
**Parameterized**
第3种不常用，所以这里不介绍。

######1. BlockJUnit4ClassRunner的使用
   BlockJUnit4ClassRunner是Junit4默认的runner。
   使用方式1：
   `@RunWith(BlockJUnit4ClassRunner.class)`
   使用方式2：
   BlockJUnit4ClassRunner.class的别名是JUnit4.class。
   `@RunWith(JUnit4.class)`
   使用方式3：
   不使用RunWith语句。

开头的例子，使用了方式3。
分别用方式1、方式2改写如下：

```   
@RunWith(BlockJUnit4ClassRunner.class)
public class ResultTest {
 ...
}
```
```   
@RunWith(JUnit4.class)
public class ResultTest {
 ...
}
```

######2. Suite的使用
   Suite是将多个runner串联起来的一个runner。
   如何使用Suite呢？

1. 添加RunWith语句
`@RunWith(Suite.class)`
2. 添加SuiteClasses语句
  `@SuiteClasses({Test1.class, Test2.class...})`
比如有两个测试类，分别测试ipv4、ipv6模式下的ping功能。 
```
public class PingInIpv4modeTest {
  @Test
  public void printIpv4() {
    System.out.println("This is ipv4.");
  }
}
```

```
public class PingInIpv6modeTest {
  @Test
  public void printIpv6() {
    System.out.println("This is ipv6.");
  }
}
```
要将它们合并为一个整体，就需要用到Suite
```
@RunWith(Suite.class)
@Suite.SuiteClasses({
    PingInIpv4modeTest.class,
    PingInIpv6modeTest.class
})
public class PingTest {
	//nothing
}
```
当运行PingTest类时，就会运行SuiteClasses中所有的类。
如下图所示：
![result.PNG](https://upload-images.jianshu.io/upload_images/8093186-55e240278ae6a289.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




*需要注意的是：*
一个class声明为Suite后，其内部的方法已经无意义，也无法运行。
在上面的PingTest中，加入一个方法testInSuite，运行这个方法，会出现错误：
```
@RunWith(Suite.class)
@Suite.SuiteClasses({
    PingInIpv4modeTest.class,
    PingInIpv6modeTest.class
})
public class PingTest {
  @Test
  public void testInSuite() {
    System.out.println("it is testInSuite");
  }
}
```
![result-of-running-method-in-suite.PNG](https://upload-images.jianshu.io/upload_images/8093186-be1c482b1dfb2988.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)









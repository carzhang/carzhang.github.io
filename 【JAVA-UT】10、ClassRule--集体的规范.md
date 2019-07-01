文|码术张
#####一、ClassRule与Rule的比较
下面两个类ClassRuleTest、TimeoutTest，分别使用ClassRule、Rule，来判断两个Test方法的运行时间。

一个Test方法是should_timeout1，打印一个字母A后，睡眠2000ms。

一个Test方法是should_timeout2，打印一个字母B后，睡眠2000ms。

ClassRuleTest类：

```
public class ClassRuleTest {
  @ClassRule
  public  static Timeout timeout = Timeout.millis(3000);

  @Test
  public void should_timeout1() throws InterruptedException {
    System.out.println("A...");
    Thread.sleep(2000);
  }

  @Test
  public void should_timeout2() throws InterruptedException {
    System.out.println("B...");
    Thread.sleep(2000);
  }

}
```
运行结果为：

![1541493756208.png](https://upload-images.jianshu.io/upload_images/8093186-bf1d590589420065.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




TimeoutTest类：

```
public class TimeoutTest {
  @Rule
  public TestRule timeout = Timeout.millis(3000);

  @Test
  public void should_timeout1() throws InterruptedException {
     System.out.println("A...");
     Thread.sleep(2000);
  }

  @Test
  public void should_timeout2() throws InterruptedException {
     System.out.println("B...");
     Thread.sleep(2000);
  }
}
```

运行结果为：

![1541492955046.png](https://upload-images.jianshu.io/upload_images/8093186-ae33e94b6979f7b7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



运行结果一个失败，一个成功。

为什么？

使用Rule，意味着每一个测试方法的运行时间，不能超过设置的时间，如3000ms。

使用ClassRule，则是所有测试方法的运行时间的和，不能超过3000ms。

**Rule的作用范围是method-level.**

**ClassRule的作用范围是class level.**

另外在声明上不同：

```
@ClassRule
public static Timeout timeout = Timeout.millis(3000);
```

```
@Rule
public TestRule timeout = Timeout.millis(3000);
```

**使用ClassRule时，变量必须声明为public static，而使用Rule，不需要static。**




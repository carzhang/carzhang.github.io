文|码术张

######1、一个可以改进的测试类
类TimerService如下：

```
public class TimerService {
  private int flag = 0; // 1, open; 0, close.

  public boolean startTimer() {
    if(1 == flag) {
      return false;
    }
    //...  other operation

    flag = 1;

    return true;
  }

  public boolean stopTimer() {
    if (0 == flag) {
      return false;
    }

    //...  other operation

    flag = 0;
    return true;
  }
}
```

其私有成员flag，表示定时器是否开启。
1为开启；0为关闭。

成员方法startTimer，在flag为1的情况下，返回false，表示不能开启。

成员方法stopTimer，在flag为1的情况下，返回true，表示成功关闭。

测试类如下：

```
public class TimerServiceTest {
    @Test
    public void should_startTimerFailure() {
        // given
        TimerService timerService = new TimerService();
        timerService.startTimer(); //将flag置为1

        // when
        boolean result = timerService.startTimer(); //返回false
        timerService.stopTimer();

        // then
        assertFalse(result); //验证是否返回false
    }

    @Test
    public void should_stopTimerSuccessful() {
        // given
        TimerService timerService = new TimerService();
        timerService.startTimer();//将flag置为1

        // when
        boolean result = timerService.stopTimer(); //返回true

        // then
        assertTrue(result); //验证是否返回true
    }
}
```

运行结果如下：

![1539134680331.png](https://upload-images.jianshu.io/upload_images/8093186-fe5a443e67a0c69b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



######2、为什么不能用BeforeClass

这个测试类，每个Test都有下面这两条语句：

```
TimerService timerService = new TimerService();
timerService.startTimer();
```

可以用BeforeClass提取出来吗？

试试。

```
public class TimerServiceWithBeforeClassTest {
    private static TimerService timerService;

    @BeforeClass
    public static void setup() {
        timerService = new TimerService();
        timerService.startTimer(); //将flag置为1
    }

    //方法1
    @Test
    public void should_startTimerFailure() {    

        // when
        boolean result = timerService.startTimer();
        timerService.stopTimer();
        
        // then
        assertFalse(result);
    }

    //方法2
    @Test
    public void should_stopTimerSuccessful() {  

        // when
        boolean result = timerService.stopTimer();

        // then
        assertTrue(result);
    }
}
```

运行结果如下：
![1539076582838.png](https://upload-images.jianshu.io/upload_images/8093186-48c49af1e840c0ac.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

失败了，为什么？

方法1、2成功的前提是flag为1。

但现在有一个方法运行时，flag不为1。

BeforeClass执行后，flag不是为1了吗？

是的，执行后，flag为1。

但是，BeforeClass方法只执行一次。

当方法2执行后，flag从1变为0。

当方法1执行时，flag是0，于是导致失败。

怎么办？

在方法1、方法2运行前，分别设置flag一次。

这样，方法2修改了flag的值，也不会影响方法1的运行。

######3、如何使用Before

如何实现在方法1、方法2运行前，分别设置flag一次呢？

这就要使用Before。

如何使用呢？

第1步：写空方法

```
  public void setup() {
    
  }
```
Before所注解的方法，必须声明为public，返回void。

第2步：添加注解
```
  @Before
  public void setup() {
    
  }
```
第3步：填写语句
将重复的不变的语句，搬到这个方法中。
同时，增加相关语句。
```
  private TimerService timeService;

  @Before 
  public static void setup() {
    timeService = new TimerService();
  }
```

第4步：去掉语句
将重复语句从各个Test方法中去掉。
例如：
```
  @Test
  public void should_startTimerSuccessful() {

    // when
    boolean result = timeService.startTimer();

	// then
    assertTrue(result);
  }
```

代码如下所示：
```
public class TimerServiceWithBeforeTest {
  private TimerService timerService;

  @Before
  public void setup() {
    timerService = new TimerService();
    timerService.startTimer();
  }

  @Test
  public void should_startTimerFailure() {

    // when
    boolean result = timerService.startTimer();
    timerService.stopTimer();
    
    // then
    assertFalse(result);
  }

  @Test
  public void should_stopTimerSuccessful() {

    // when
    boolean result = timerService.stopTimer();

    // then
    assertTrue(result);
  }
}
```
运行结果：
![1539239433985.png](https://upload-images.jianshu.io/upload_images/8093186-3665f94f96a5f28e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

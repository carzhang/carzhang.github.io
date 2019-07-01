文|码术张

#####一、如何使用Rule

下面这个测试类TimeoutTest，有两个Test方法，分别在打印一个字母后，睡眠2000毫秒和4000毫秒。

```
public class TimeoutTest {
  @Test
  public void should_timeout1() throws InterruptedException {
    System.out.println("A...");
    Thread.sleep(2000);
  }

  @Test
  public void should_timeout2() throws InterruptedException {
    System.out.println("B...");
    Thread.sleep(4000);
  }
}
```
后来公司要求，每个Test方法运行时间不能超过3000毫秒，如果超过了3000毫秒，就算失败。

如何实现这一功能？

需两步:

1. 获得一个定时器对象

`public TestRule timeout = Timeout.millis(3000);`

2. 使用Rule注解

```
@Rule
public TestRule timeout = Timeout.millis(3000);
```

修改后，测试类如下：

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
     Thread.sleep(4000);
  }
}
```

运行结果为：
睡眠2000ms的Test方法成功，
睡眠4000ms的Test方法should_timeout2失败。
![1541388504275.png](https://upload-images.jianshu.io/upload_images/8093186-9d63490e37c16249.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

为什么呢？

因为本例子中，Rule限制了每一个Test方法运行时间不能超过3000ms。

可以看到Rule是如何使用的：

![1541388841122.png](https://upload-images.jianshu.io/upload_images/8093186-3ec7f59a3a53974b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

注意：
1. 所要注解的对象必须声明为public。
2. Rule只能注解TestRule类型的对象。

本例中Timeout类型是TestRule的子类型。

如果非TestRule类型或其子类型，运行时就会报错。

例如用Rule注解String类型对象。

```
public class TimeoutTest {
  @Rule
  public String name = "abc";

  @Test
  public void should_timeout1() throws InterruptedException {
    System.out.println("A...");
    Thread.sleep(4000);
  }
}
```

运行结果如下，提示@Rule要注解MethodRule或TestRule(实际上，MethodRule已经被TestRule代替了)。
![1539843982099.png](https://upload-images.jianshu.io/upload_images/8093186-abda2900568bd081.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#####二、 3种TestRule类的功能介绍

有哪些TestRule类呢？

当前，Junit4提供了8类TestRule：

```
ErrorCollector
ExpectedException
ExternalResource
TemporaryFolder
TestName
TestWatcher
Timeout
Verifier
```

下面介绍常用的3种。

1. Timeout

​      Timeout规定每个test方法的可运行时间，如果这么想，就错了。
​      Timeout只是设置了一个定时器，并不规定每个test方法的运行时间。
​      这定时器被Rule拿来使用，以达到规定每个test方法运行时间的目的。 

2. TestName

   获取当前运行的方法的名字。

   ```
   public class Sample {
     @Rule
     public TestName name = new TestName();
   
     @Test
     public void John() {
       System.out.println("my name is: " + name.getMethodName());
     }
   }
   ```

运行结果为：
![1539929275418.png](https://upload-images.jianshu.io/upload_images/8093186-fe571de1f6dc3931.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3. TemporaryFolderTemporaryFolder
   它是一个用于创建、删除临时文件或文件夹的工具。
   但何时创建、销毁，并不是这个工具的功能，而是Rule的功能。

   下面举例说明，如何创建文件。

```
public class RuleTest {
  @Rule
  public TemporaryFolder tmpFolder = new TemporaryFolder();

  @Test
  public void should_existFile() throws IOException {
    // when
    File file = tmpFolder.newFile("myfile.txt");

    // then
    assertTrue(file.isFile());
  }
}
```
运行中，Junit会自动创建一个文件夹junit2973493844983643175，创建的文件就在这一文件夹中。
运行结束，这个文件夹junit2973493844983643175及其中的文件会自动删除。
![1540791154867.png](https://upload-images.jianshu.io/upload_images/8093186-82aee87ff6284707.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![1540791187281.png](https://upload-images.jianshu.io/upload_images/8093186-6f88c10de92551a0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

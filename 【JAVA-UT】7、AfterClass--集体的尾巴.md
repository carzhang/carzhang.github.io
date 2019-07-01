文|码术张

在所有Test方法运行结束后，如果还要做点什么，怎么办？

使用AfterClass。

如何使用？

举例如下：

```
public class AfterClassTest {
    private static String names[] = new String[5];

    @Test
    public void apple() {
        names[0] = "apple";
    }

    @Test
    public void orange() {
        names[1] = "orange";
    }

    @AfterClass
    public static void printNames() {
        System.out.println("test name: " + names[0]);
        System.out.println("test name: " + names[1]);
    }
}
```

![1547184367558.png](https://upload-images.jianshu.io/upload_images/8093186-db768e8bc12a803a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


从运行结果中可看到，两个名字都被打印了出来。

为什么不是打印一个或零个？

因为，AfterClass注解的这个方法，是在其他两个方法执行后，才被执行的。

注意：

AfterClass注解的方法得声明为：

`public static void`

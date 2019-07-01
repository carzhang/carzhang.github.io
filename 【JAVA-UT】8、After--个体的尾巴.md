文|码术张

如果公共变量的值，在每个test运行后，需要归零，那么可以使用After。

After就是在每个test运行后做一些工作。

与AfterClass不同，AfterClass中的工作，是在所有test运行结束后才需要做。

如果一个测试类有9个test，那么AfterClass只会执行一次，而After会执行9次。

如何使用After?

```
public class myAfterTest {
    private static int count = 0;

    @Test
    public void test1() {
        while (count < 3) {
            count++;
            System.out.print("1 ,");
        }
    }

    @Test
    public void test2() {
        while (count < 3) {
            count++;
            System.out.print("2 ,");
        }
    }
    
    @After
    public void doSomethingAfterTest() {
        count = 0;
        System.out.println("do something after test");
    } 
}
```

![1547890902046.png](https://upload-images.jianshu.io/upload_images/8093186-adc71df733daef8b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


运行结果中，1打印了3次，2也打印了3次，“do something after test”打印了2次。

从“do something after test”打印了2次可知，After是在每一个test运行后，就会执行一次。

1、2分别打印了3次，说明while循环开始时, count的值都是0。

如果去掉After方法，其他不变，结果会如何?

![1547891287275.png](https://upload-images.jianshu.io/upload_images/8093186-7ecd65e76615d50d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


只有数值1被打印出来，数值2没有打印。

这是因为count是公共的变量，当test1运行后，count的值是3。当test2的while循环看到count是3，count<3不成立，于是退出循环，不打印数值2。




文|码术张

如何验证抛出的异常是指定的异常？

下面介绍两种方法。

##### **方法一：**

```
public class Test1 {
    @Test(expected = IllegalArgumentException.class)
    public void test1() {
        throw new IllegalArgumentException();
    }
}
```

期望的表达方式为

`(expected = IllegalArgumentException.class)`

抛异常的代码为：

`throw new IllegalArgumentException();`

意思为抛出一个异常IllegalArgumentException。

在实际代码中，将这句换成需要抛异常的代码即可。

如果抛出的异常与期望的一致，那么运行结果就是pass。

如果不一致，则会fail。

例如，正面这段代码，期望抛出异常IllegalArgumentException，实际抛的是NullPointerException。

```
public class Test2 {
    @Test(expected = IllegalArgumentException.class)
    public void test2() {
        throw new NullPointerException();
    }
}
```

运行结果会是fail，并且给出提示：

![result.png](https://upload-images.jianshu.io/upload_images/8093186-b2d84ef2cc03a988.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




##### **方法二**：

```
public class Test3 {
    @Rule
    public ExpectedException worker = ExpectedException.none();

    @Test
    public void test3() {
        worker.expect(IllegalArgumentException.class);
        throw new IllegalArgumentException();
    }
}
```

使用ExpectedException判断是否有期望的异常。

首先，声明一个ExpectedException类型的变量。

然后，告诉它期望的异常。

`worker.expect(IllegalArgumentException.class);`

意思是期望的异常为IllegalArgumentException。

`throw new IllegalArgumentException();`

抛出一个异常。写代码时，将这句换成实际要抛异常的代码。

如果抛出的与期望的异常一致，则pass。否则，fail，并给出错误提示。

例如，正面这段代码：

期望的是IllegalArgumentException。

实际的抛出的是NullPointerException。

```
public class Test4 {
    @Rule
    public ExpectedException worker = ExpectedException.none();

    @Test
    public void test4() {
        worker.expect(IllegalArgumentException.class);
        throw new NoSuchFieldError();
    }
}
```

运行结果如下：

![result 2.png](https://upload-images.jianshu.io/upload_images/8093186-708c6afab8002df7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



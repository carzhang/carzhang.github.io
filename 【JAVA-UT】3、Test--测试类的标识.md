文|码术张

##### 1、@Test的作用

如下有一个类，类中有两个方法:m1，m2。

如何将这个类变成一个测试类?

将其中一个方法声明为@Test。

比如，将m2声明为@Test。

这时，这个类，就不是普通的类，而是一个用于单元测试的类。

m2称为一个test case，能直接运行。按测试目的，添加方法体，运行，便会获得被测功能是否达到预期的信息。

```
public class ResultTest {
 
  public void m1() {

  }
  
  @Test
  public void m2() {

  }
}

```

添加@Test后，在idea中，会在类的左边，出现一个绿色三角形图标，点击，即可运行。

在m1左侧，没有这个图标，表示m1不能运行，除非在m2中调用了m1。
![image.png](https://upload-images.jianshu.io/upload_images/8093186-2f84c8e96c739350.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




##### 2、如何使用@Test

@Test声明的方法，不是任意的方法，需要符合以下条件：

*1. public*
*2. 返回值void* 
*3. 参数为空*

例如，下面的方法m3，有一个参数，

```
public class ResultTest {

    @Test
    public void m3(String input) {

    }
```
运行结果是失败的，提示不能有参数。
![image.png](https://upload-images.jianshu.io/upload_images/8093186-6741acd560fc8080.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




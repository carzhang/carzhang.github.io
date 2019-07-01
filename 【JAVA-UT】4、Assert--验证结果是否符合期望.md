文|码术张

被测功能运行结果是预期的结果吗？
用Assert类提供的方法进行验证。 
Assert类中，有很多种验证的方法。
按是否使用Matcher技术，可分为两类。

#####一、不使用Matcher技术的方法
Junit中，这类方法有近40多个。
按所比较对象是否为数组，分为两类：

1. 比较的对象是数组。
2. 比较的对象不是数组。

这两类中的每一类，可继续按比较对象的类型，划分为两类：
1. 基本数据类型
2. 其他类型

所以，一共有如下4组：
1. 比较的对象是数组，数组元素的类型是基本数据类型
```
assertArrayEquals(byte[] expecteds, byte[] actuals);
assertArrayEquals(char[] expecteds, char[] actuals);
assertArrayEquals(int[] expecteds, int[] actuals);
assertArrayEquals(long[] expecteds, long[] actuals);
assertArrayEquals(short[] expecteds, short[] actuals);

assertArrayEquals(java.lang.String message, byte[] expecteds, byte[] actuals);
assertArrayEquals(java.lang.String message, char[] expecteds, char[] actuals);
assertArrayEquals(java.lang.String message, int[] expecteds, int[] actuals);
assertArrayEquals(java.lang.String message, long[] expecteds, long[] actuals);
assertArrayEquals(java.lang.String message, short[] expecteds, short[] actuals);
```
2. 比较的对象是数组，元素的类型是其他数据类型
```
assertArrayEquals(java.lang.Object[] expecteds, java.lang.Object[] actuals);
assertArrayEquals(java.lang.String message, java.lang.Object[] expecteds, java.lang.Object[] actuals);
```

3. 比较的对象是单个元素，元素类型是基本数据类型
```
assertEquals(double expected, double actual, double delta);
assertEquals(long expected, long actual);
assertFalse(boolean condition);
assertTrue(boolean condition);

assertEquals(java.lang.String message, double expected, double actual, double delta);
assertEquals(java.lang.String message, long expected, long actual);
assertFalse(java.lang.String message, boolean condition);
assertTrue(java.lang.String message, boolean condition);

```

4. 比较的对象是单个元素，元素类型是其他数据类型
```
assertEquals(java.lang.Object expected, java.lang.Object actual);
assertNotNull(java.lang.Object object);
assertNotSame(java.lang.Object unexpected, java.lang.Object actual);
assertNull(java.lang.Object object);
assertSame(java.lang.Object expected, java.lang.Object actual);

assertEquals(java.lang.String message, java.lang.Object expected, java.lang.Object actual);
assertNotNull(java.lang.String message, java.lang.Object object);
assertNotSame(java.lang.String message, java.lang.Object unexpected, java.lang.Object actual);
assertNull(java.lang.String message, java.lang.Object object);
assertSame(java.lang.String message, java.lang.Object expected, java.lang.Object actual);

```
**如何使用呢？**
假设比较的数据为String类型，
目的是检查它们是否相等，
那么，就选择下面这个方法：
`assertEquals(java.lang.Object expected, java.lang.Object actual);`
例如：

```
public class AssertTests {
  @Test
  public void should_Equals() {
    String expectedResult = "him";

    String actualResult = "she";

    Assert.assertEquals(expectedResult, actualResult);
  }
}
```
运行结果如下：
![result-without-message.PNG](https://upload-images.jianshu.io/upload_images/8093186-53c5af87e0e5ed1f.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

若要从运行结果中获得额外信息，可以使用方法：
`assertEquals(java.lang.String message, java.lang.Object expected, java.lang.Object actual);`
参数message的信息，会在运行结果中打印出来。
将测试类修改如下：

```
public class AssertTests {
  @Test
  public void should_Equals() {
    String expectedResult = "him";
    String actualResult = "she";
    Assert.assertEquals("2018-05-04", expectedResult, actualResult);
  }
}

```
运行结果如下：
![result-with-message.PNG](https://upload-images.jianshu.io/upload_images/8093186-cfca73f801a7124f.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#####二、使用Matcher技术的方法
Assert类中，有一个方法使用了Matcher技术。它就是：

`assertThat(actualValue, Matcher);`

**什么是Matcher？**  
Matcher是一个接口，它有方法matches。
这个方法会被assertThat以actualValue作为参数调用matches。
不同的matches方法，功能不同，例如比较两个对象是否相等，判断对象是否为空。

**如何获得Matcher呢？**
可以使用下面这些方法获得：
`public static <T> Matcher<T> is(java.lang.Class<T> type)`
`public static <T> Matcher<T> equalTo(T operand)`
`public static <T> Matcher<T> not(T value)`
`public static Matcher<Object> nullValue()`
`public static Matcher<Object> notNullValue()`
`public static <T> Matcher<T> sameInstance(T target)`
`public static Matcher<String> containsString(String substring)`
`public static Matcher<String> containsStringIgnoringCase(String substring)`
`public static Matcher<String> startsWith(String prefix)`
`public static Matcher<String> startsWithIgnoringCase(String prefix)`
`public static Matcher<String> endsWith(String suffix)`
`public static Matcher<String> endsWithIgnoringCase(String suffix)`
`public static Matcher<String> matchesRegex(Pattern pattern)`
`public static Matcher<String> matchesRegex(String regex)`
`public CombinableMatcher<T> and(Matcher<? super T> other)`
`public CombinableMatcher<T> or(Matcher<? super T> other)`
`public static <LHS> CombinableBothMatcher<LHS> both(Matcher<? super LHS> matcher)`
`public static <LHS> CombinableEitherMatcher<LHS> either(Matcher<? super LHS> matcher)`

接下来，以equalTo为例，说明如何使用assertThat
```
import static org.hamcrest.CoreMatchers.equalTo;

import org.junit.Assert;
import org.junit.Test;


public class AssertThatTests {
  @Test
  public void should_Equals() {
    String expectedResult = "him";
    String actualResult = "she";
    Assert.assertThat(actualResult, equalTo(expectedResult));
  }
}
```
运行结果如下：
![result-used-assertThat.png](https://upload-images.jianshu.io/upload_images/8093186-46d33cacf67b0c45.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**使用assertThat的优点**
1. 信息更多
注意上面运行结果，被加了橙色框框的部分。
这是提示信息。
在分析运行结果时，很有用。
而这是使用assertEqual这样的方法，所不具备的。

2. 适用范围广
因为可以根据场景定制Matcher。


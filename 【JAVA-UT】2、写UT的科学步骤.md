文|码术张

在形式上，UT即是一个个测试类。
一个测试类，包含若干method，这些method，对被测试类的method的功能，进行验证。

一个测试类，是如何诞生的呢？本节将详细说明。
被测试类Result如下：

```
public Class Result {
  private String downLoadStatus;
  private String failureReason;

  public Result(String status, String reason) {
    downLoadStatus= status;
     failureReason= reason;
  }

  @Override
  public String toString() {
    return "downLoadStatus: "+ downLoadStatus+ ", "
               +  "failureReason: " + failureReason;
        }
}
```
类Result中：
属性downLoadStatus，表示下载状态，如“Ongoing”，“Ok”，“Fail”。
属性failureReason，表明失败原因，例如“cannot connect”。
方法toString，将上面两个属性值组合为一个string。


**第1步：确定目标。**
要测试什么？
在本例中，是对toString这一method进行测试。
测试toString方法，是否会能将两个值合并成预期的值。


**第2步：提出假设。**
假设toString的功能正常。

**第3步：推测结论。**                
按照假设，那么，执行toString后，其返回值应当与期望值expectedString相等。

**第4步：执行被测功能。**
执行toString方法，并记录实际值。
用java语句表示为：

```
   Result result = new Result("Fail", "cannot connect");
   String realString = result.toString();
```

**第5步：进行验证。**
将实际值与期望值进行相等比较，验证toString功能是否符合预期。
用java语句表示为：
```
String expectedString = "downLoadStatus: Fail, failureReason: cannot connect";
Assert.assertEquals(expectedString, realString);
```


**第6步：编写代码**
首先，创建一个类。
命名为ResultTest，表明这个类是为了测试Result。当然，取其他名字，也不是不行。

```
public class ResultTest {

}
```
其次，创建一个方法。
将1-5步中的java语句放在这个方法中：

```
  public void should_returnString() {
    // given
    Result result = new Result("Fail", "cannot connect");
    String expectedString = "downLoadStatus: Fail, failureReason: cannot connect";

    // when
    String realString = result.toString();

    // then
    Assert.assertEquals(expectedString, realString);
  }
```

再次，为这个方法添加@Test标识。

最终，代码如下：

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

一个测试方法，就是被@Test修饰的方法，如should_returnString，从逻辑上，可以划分为3部分：

前提条件部分；执行被测功能部分；验证部分。

如何设置前提条件？方法有@BeforeClass，@Before，@Rule，@ClassRule， @Runner。

如何验证？Assert.

按从易到难的顺序，依次说明@Test，Assert，及前提条件的设置。

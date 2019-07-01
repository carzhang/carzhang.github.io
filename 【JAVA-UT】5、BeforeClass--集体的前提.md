文|码术张

本节演示注解BeforeClass的使用，以及说明为什么要使用它。
介绍BeforeClass前，先来看个例子吧。
下面是类Result.

```
public Class Result {
  private String downLoadStatus;
  private String failureReason;

  public Result(String status, String reason) {
    downLoadStatus= status;
     failureReason= reason;
  }

  @Override
  public boolean equals(Object other) {
    if (this == other) {
      return true;
    }

    if (!(other instanceof Result)) {
      return false;
    }

    Result that = (Result ) other;
    
    if (!downLoadStatus.equals(that.downLoadStatus)) {
      return false;
    }

    if (!failureReason.equals(that.failureReason)) {
      return false;
    }

    return true;
  }

}
```
其中equals方法，用于比较两个Result实例是否相等。
它包含4个if语句，依次判断：
other与this是否同一个对象；
other是否Result类型；
downLoadStatus属性是否相等；
failureReason属性是否相等。

对equals方法写ut进行测试，即要测试这4个功能点。
按上节步骤，写出如下ut：
```
public class ResultTest() {
  @Test
  public void should_equal_when_sameOject() {
    // given
    Result result = new Result("Fail", "cannot connect");

    // then
    assertTrue(result.equals(result));
  }


  @Test
  public void should_equalsReturnFalse_when_otherHasDifferentType() {
    // given
    Result result = new Result("Fail", "cannot connect");
    Object other = new Object();
    
    // then
    assertFalse(result.equals(other));
  }

  @Test
  public void should_equal_when_haveSameContent() {
    // given
    Result result = new Result("Fail", "cannot connect");
    Result other = new Result("Fail", "cannot connect");        

    // then                                            
    assertTrue(result.equals(other));
  }


  @Test
  public void should_notEqual_when_haveDifferentStatus() {
   ...
  }

  @Test
  public void should_notEquals_when_haveDifferentReason() {
   ...
  }
}
```

这是一个正确的ut。
仔细观察，会发现每一个方法都有语句：
`Result result = new Result("Fail", "cannot connect");`
可以改进。
这就要用到@BeforeClass。
共同的不变的值，用static成员表示。
BeforeClass所注解的方法，可设置静态成员。
而且，这方法只会执行一次，在所有Test方法运行前执行。

**第1步：写空方法**
```
  public void setup() {
    
  }
```

**第2步：添加注解**
```
  @BeforeClass
  public void setup() {
    
  }
```

**第3步：调整声明**

BeforeClass注解的方法，声明必须是public static void。
```
  @BeforeClass
  public static void setup()() {
    
  }
```
**第4步：填写语句**
将重复的不变的语句，搬到这个方法中。
同时，增加相应语句。
```
  private static Result result;

  @BeforeClass
  public static void setup() {
    result = new Result("Fail", "cannot connect");
  }
```

**第5步：去掉语句**
将相应的语句从各个Test方法中去掉。
例如：
```
  @Test
  public void should_equalsReturnTrue_when_twoResultsHaveSameReference() {
    assertTrue(result.equals(result));
  }
```


最终，修改后的代码，如下所示：
```
public class ResultTest() {
  private static Result result;

  @BeforeClass
  public static void setup() {
    result = new Result("Fail", "cannot connect");
  }

  @Test
  public void should_equalsReturnTrue_when_twoResultsHaveSameReference() {
    assertTrue(result.equals(result));
  }
  
  // 省略了其他的方法
```




为什么要引入@BeforeClass呢?
**消除重复，以降低修改成本。**
如果将来Result构造函数的参数变成3个，那么，每一个Test方法都需要修改。
也就是说：1个变化，4处修改。
这还是一个很简单的例子。
在复杂的例子中，Test方法个数、重复语句个数，都会更多。
假设共有10个Test方法， 有重复语句5句，那最终可能会导致50个改动。
引入@BeforeClass的版本，你只需要修改1处：BeforeClass方法中的1处。





### java语言标签label

+ 通过标签标识语句块,然后使用break在语句块内指定的执行处退出,标签的名称需要符合正常的标识符的名称要求(
  字母、数字和下划线,但因为支持utf8,所以也可以使用中文)

```java
void test() {
    int a = 3;
    label_1:
    if (a > 2) {
        break label_1;
    }
}
```

+ 其实标签是标识在代码块上的,可以随意定义一个代码块,然后使用标签来标识,如下

```java
void test() {
  int a = 4;
  //当a>2时是直接跳出外面的代码块,不会输出下面的内容
  label_2:
  {
    if (a > 2) {
      break label_2;
    }
    System.out.println("输出标签2");
  }
}
```

+ 所以上面的`if`条件判断语句可以使用如下的例子替换,这个也是我看到`CopyOnWriteArrayList`的源码,
  `private boolean remove(Object o, Object[] snapshot, int index)`方法里的实现里使用了这个方式

```java
void test() {
  int a = 3;
  if (a > 2) label_1:{
    break label_1;
  }
}
```

+ 在使用continue的标签方式是跳过当前循环执行下一个循环,所以这种使用方式必须在循环体内,如果是`for`表达式则会先执行`for`
  语句的第三个表达式,但是`while`和`do-while`不会执行continue下面的代码,常使用`continue`+标签的方式的使用场景一般是嵌套循环的跳转,例子如下

```java
void test() {
    label_3:
    for (int i = 0; i < 10; i++) {
        for (int j = 0; j < 10; j++) {
            if (j == 8) {
                continue label_3;
            }
        }
    }
}
```

+ 但是`continue`并不支持上面的语句括号前置写法,会报`Not a loop label`,应该是这样写的话是无法识别语句是否是循环

### try-with-resources
根据定义`try-with-resources`会自动清理资源,但是需要清理的资源(或者说是对象)实现`java.lang.AutoCloseable`接口,如果是IO相关的资源可以实现`java.io.Closeable`接口(它也继承自`AutoClosebale`接口),强烈推荐的使用方式就是`try-with-resources`语法
```java
class TestAutoCloseable implements AutoCloseable {
    public void close() throws Exception {
        System.out.println("自动清理");
    }

    void show() {
        System.out.println("开始使用");
    }
}

public class TryWithResourcesTest {
    void show() {
        try (TestAutoCloseable test = new TestAutoCloseable()) {
            test.show();
        }
    }
}
```
上面的例子展示了使用一个`try-with-resources`的标准实现,前提是需要实现`java.lang.AutoCloseable`接口,在`try`块里的代码执行完毕之后会执行资源的`close`方法,所以需要实现者在`close`方法里实现自己的清理逻辑  
也可以在`try-with-resources`里加上`catch`子句捕获自己需要处理的异常(包括`close`方法抛出的异常),如果在关闭的时候也抛出了异常,将会被放置到实际抛出的异常的**抑制**列表里,jdk1.7在异常类里新增的api,这样新抛出的异常就不会覆盖掉之前抛出的异常,在输出异常的时候也会把该异常抑制的所有异常都一并输出
```java
public class TryWithResourcesTest {
    void show() {
        try (TestAutoCloseable test = new TestAutoCloseable()) {
            test.show();
        } catch (Exception e){
            log.error("处理自己的异常", e);
        }
    }
}
```
实现原理:反编译生成的代码可以找到代码的生成规律,每一个`try-with-resources`都会对应一个`try-catch-finally`,在之前会有2个变量的声明,一个是对应的**资源对象**,另一个是异常对象,用来获取代码中所抛出的实际异常,如果在`close`的时候也抛出了异常则会将该异常添加到声明的异常(如果不为`null`)的抑制列表里,
```java
public class TryWithResourcesTest {
  void show() {
    try (TestAutoCloseable test = new TestAutoCloseable()) {
      test.show();
    }
  }

  void show() {
    TestAutoCloseable test = new TestAutoCloseable();
    Throwable v1 = null;
    try {
      test.show();
    } catch (Exception e) {
      v1 = e;
      throw e;
    } finally {
      if (test != null) {
        if (v1 != null) {
          try {
            test.close();
          } catch (Throwable e) {
            v1.addSuppressed(e);
          }
        } else {
          test.close();
        }
      }
    }
  }
}
```
如果在`try-with-resources`里包含了`catch`或者`finally`子句,则会在生成的代码外围再包含一个try/catch/finally语句
```java
public class TryWithResourcesTest {
  void show() {
    try (TestAutoCloseable test = new TestAutoCloseable()) {
      test.show();
    } catch (Exception e) {
      log.error("捕获异常", e);
    } finally {
        //do finally
    }
  }

  void show() {
    try {
      TestAutoCloseable test = new TestAutoCloseable();
      Throwable v1 = null;
      try {
        test.show();
      } catch (Exception e) {
        v1 = e;
        throw e;
      } finally {
        if (test != null) {
          if (v1 != null) {
            try {
              test.close();
            } catch (Throwable e) {
              v1.addSuppressed(e);
            }
          } else {
            test.close();
          }
        }
      }
    } catch (Exception e) {
      log.error("捕获异常", e);
    } finally {
      //do finally
    }
  }
}
```
如果有多个资源在`try-with-resources`中,则会按照声明的顺序进行代码的生成如下,
```java
public class TryWithResourcesTest {
  void show() {
    try (TestAutoCloseable t = new TestAutoCloseable();
         TestAutoCloseable t1 = new TestAutoCloseable()) {
        t.show();
        t1.show();
    } catch (Exception e) {
      log.error("捕获异常", e);
    } finally {
        //do finally
    }
  }

  //这种方式生成的代码与上面的一样,但是可以单独增加try/catch/finally,所以在生成的内部的try/catch/finally外围有我们定义的try/catch/finally
  void show() {
    try (TestAutoCloseable t = new TestAutoCloseable()) {
        try(TestAutoCloseable t1 = new TestAutoCloseable()) {
            t.show();
            t1.show();
        }
    } catch (Exception e) {
      log.error("捕获异常", e);
    } finally {
      //do finally
    }
  }

  void show() {
    try {
      TestAutoCloseable t = new TestAutoCloseable();
      Throwable v1 = null;
      try {
        TestAutoCloseable t1 = new TestAutoCloseable();
        Throwable v2 = null;
        try {
          t.show();
          t1.show();
        } catch (Exception e) {
          v2 = e;
          throw e;
        } finally {
          if (t1 != null) {
            if (v2 != null) {
              try {
                t1.close();
              } catch (Throwable e) {
                v2.addSuppressed(e);
              }
            } else {
              t1.close();
            }
          }
        }
      } catch (Exception e) {
        v1 = e;
        throw e;
      } finally {
        if (t != null) {
          if (v1 != null) {
            try {
              t.close();
            } catch (Throwable e) {
              v1.addSuppressed(e);
            }
          } else {
            t.close();
          }
        }
      }
    } catch (Exception e) {
      log.error("捕获异常", e);
    } finally {
      //do finally
    }
  }
}
```
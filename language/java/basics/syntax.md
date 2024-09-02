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

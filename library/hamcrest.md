### Hamcrest 单元测试匹配器

#### 概念和介绍

**Hamcrest** 是一个用于编写匹配器对象的框架,允许以声明方式定义<*匹配*>规则. 在许多情况下,匹配器非常宝贵,例如 UI
验证或数据筛选,但在编写灵活测试的领域,匹配器是最常用的.

在编写测试时,有时很难在过度指定测试(并使其易受更改)和未指定足够(
使测试价值降低,因为即使被测试的东西被破坏,它也会继续通过)之间取得平衡.拥有一个工具,
可以让您精确地挑选出被测的方面并描述它应该具有的值,以达到可控的精度水平,这对编写"恰到好处"
的测试有很大帮助.当被测方面的行为偏离预期行为时,此类测试会失败,
但当对行为进行微小的、不相关的更改时,此类测试将继续通过.

现在一般是使用的2.x版本

```xml

<project>
    <!-- maven依赖-->
    <dependency>
        <groupId>org.hamcrest</groupId>
        <artifactId>hamcrest</artifactId>
        <version>2.x</version><!-- 当前最新版本2.2-->
        <scope>test</scope>
    </dependency>
    <!--如果是使用的旧的1.x版本的hamcrest,-->
    <!--1. 可以在依赖配置里显示剔除该版本的依赖,如下-->
    <dependency>
        <groupId>xx.xx.xx</groupId>
        <artifactId>xxx</artifactId>
        <version>${xxx.version}</version>
        <scope>test</scope>
        <exclusions>
            <exclusion>
                <groupId>org.hamcrest</groupId>
                <artifactId>hamcrest-core</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    <!--或者显示依赖一个指定版本的额外依赖 hamcrest-library 它会覆盖传递的旧依赖-->
    <!--虽然这个依赖里实际上什么都没有,所有的核心的和扩展的类都移到了hamcrest里-->
    <dependency>
        <groupId>org.hamcrest</groupId>
        <artifactId>hamcrest-library</artifactId>
        <version>2.x</version>
        <scope>test</scope>
    </dependency>
</project>
```

gradle同理

```groovy
    //gradle依赖
apply plugin: 'java'
dependencies {
    testImplementation 'org.hamcrest:hamcrest:2.x'
}

//显示覆盖旧依赖
apply plugin: 'java'
dependencies {
    testImplementation 'org.hamcrest:hamcrest:2.x'
    testImplementation 'org.hamcrest:hamcrest-library:2.x'
    testImplementation 'junit:junit:4.13.2'
}
```

```java
    import org.junit.jupiter.api.Test;//搭配junit5使用

import static org.hamcrest.MatcherAssert.assertThat;//MatcherAssert.assertThat是使用的核心方法
import static org.hamcrest.Matchers.*;//Matchers提供了很多静态实用方法

public class Hamcrest {
    @Test
    public void testEquals() {
        assertThat("hello hamcrest", equalTo("hello hamcrest"));
    }
}
```

上面的方法使用`junit5`+`hamcrest`
的很常规的例子,断言的主体作为第一个方法的实际参数,第二个方法参数是一个对象匹配器,它使用`Matchers.equalTo(Object.equals)`
匹配器来检查一个对象是否等于
另一个对象,因为`String`有自定义的`equals`方法的实现,因此测试通过
如果测试中有多个断言,则可以在断言中包含测试值的标识符:

```java

@Test
public void testEquals() {
    assertThat("test1", "hello hamcrest1", equalTo("hello hamcrest1"));
    assertThat("test2", "hello hamcrest2", equalTo("hello hamcrest2"));
}
```

`hamcrest`可以与`junit`(所有版本)和`TestNG`一起使用,在现有测试套件中迁移到使用`Hamcrest`
样式的断言很容易,因为其它断言样式可以与`Hamcrest`的断言样式共存
`hamcrest`还可以与模拟对象框架一起使用,使用适配器从模拟对象框架的匹配器概念桥接到`hamcrest`匹配器,例如: `Jmock1`
的约束是`hamcrest`匹配器,`hamcrest`提供了
一个`Jmock1`适配器,允许你在`Jmock1`测试中使用`hamcrest`匹配器,`Jmock2`不需要这样的适配器层,因为它被设计为使用`hamcrest`
作为其匹配库,`hamcrest`还为`EasyMock2`提供
适配器.

#### api方法整理

常用匹配器展示

+ core:

|     方法名     |            描述             |
|:-----------:|:-------------------------:|
|  anything   |  始终匹配,如果您不关心被测对象是什么,则很有用  |
| describedAs |      用于添加自定义故障描述的装饰器      |
|     is      | 提高可读性的装饰器 - 请参阅下面的"sugar" |

+ Logical:

|  方法名  |              描述               |
|:-----:|:-----------------------------:|
| allOf | 如果所有匹配器都匹配,则匹配,短路(如 Java &&)  |
| anyOf | 如果任何匹配器匹配，则匹配，短路(如 Java \|\|) |
|  not  |     如果包装的匹配器不匹配,则匹配,反之亦然      |

+ Object

|     方法名      |            描述            |
|:------------:|:------------------------:|
|   equalTo    | 使用 Object.equals 测试对象相等性 |
| hasToString  |    测试 Object.toString    |
|  instanceOf  |   测试类型isCompatibleType   |
| notNullValue |     测试 nullnullValue     |
| sameInstance |          测试对象标识          |

+ Beans

|     方法名     |       描述        |
|:-----------:|:---------------:|
| hasProperty | 测试 JavaBeans 属性 |

+ Collections

|      方法名       |             描述             |
|:--------------:|:--------------------------:|
|     array      |       根据匹配器数组测试数组的元素       |
|    hasEntry    | 测试地图包含条目、键或值hasKeyhasValue |
|    hasItem     |      测试集合包含元素hasItems      |
| hasItemInArray |        测试一个包含元素的数组         |

+ Number

|     方法名     |                         描述                          |
|:-----------:|:---------------------------------------------------:|
|   closeTo   |                     测试浮点值接近给定值                      |
| greaterThan | 测试排序greaterThanOrEqualTo,lessThan,lessThanOrEqualTo |

+ Text

|            方法名            |            描述             |
|:-------------------------:|:-------------------------:|
|    equalToIgnoringCase    |       测试字符串相等性忽略大小写       |
| equalToIgnoringWhiteSpace |    测试字符串相等性，忽略空格运行的差异     |
|      containsString       | 测试字符串匹配endsWithstartsWith |

+ Sugar
  `Hamcrest`努力使你的测试尽可能具有可读性.例如:
  匹配器是一个包装器,它不会向基础匹配器添加任何额外的行为.以下断言都是等效的:is

```java

@Test
public void testEquals() {
    assertThat(theBiscuit, equalTo(myBiscuit));
    assertThat(theBiscuit, is(equalTo(myBiscuit)));
    assertThat(theBiscuit, is(myBiscuit));
}
```

允许最后一种形式,因为重载形式实现是一样的: `is(T value)` =  `is(equalTo(value))`

#### 编写自定义匹配器

`Hamcrest`捆绑了许多有用的匹配器,但您可能会发现您还是需要创建自己的匹配器以满足您的测试需求.当你尝试一遍又一遍地(
以及在不同的测试中)测试同一组属性的代码片段时,
通常会发生这种情况,并且您希望将该片段捆绑到单个断言中.通过编写自己的匹配器,您将消除代码重复并使您的测试更具可读性!
以下是一个示例: 编写自定义测试匹配器来测试双精度值是否是`NaN`(not a number不是一个数),这是要编写的测试

```java

@Test
public void testSquareRootOfMinusOneIsNotANumber() {
    assertThat(Math.sqrt(-1), is(notANumber()));
}
```

这是实现:

```java
    package org.hamcrest.examples.tutorial;

import org.hamcrest.Description;
import org.hamcrest.Matcher;
import org.hamcrest.TypeSafeMatcher;

public class IsNotANumber extends TypeSafeMatcher<Double> {
    @Override
    public boolean matchesSafely(Double number) {
        return number.isNaN();
    }

    public void describeTo(Description description) {
        description.appendText("not a number");
    }

    public static Matcher notANumber() {
        return new IsNotANumber();
    }
} 
```

该方法是一种泛型方法,它采用由断言主题类型参数化的匹配器.我们正在断言关于双精度值的事情,所以我们知道我们需要一个.对于我们的`Matcher`
实现,最方便的是子类,
它为我们将`Double`转换为`Double`.我们只需要实现该方法(该方法只是检查`Double`是否为`NaN`)和该方法(
用于在测试失败时生成失败消息).下面是失败消息的外观示例:

该方法是一个泛型方法,它接受一个由断言主题类型参数化的`Matcher`
.我们在断言关于双精度值的事情,所以我们知道我们需要一个浮点数.对于我们的`Matcher`实现,
最方便的方法是子类化,它为我们将类型强制转换为`Double`类型.我们只需要实现该方法(该方法简单地检查Double是否是NaN)
和描述信息(该方法用于在测试失败时生成失败消息).
下面是失败消息外观的示例: `assertThat Matcher<Double> TypeSafeMatcher matchesSafely describeTo`

```java
    public void testEquals() {
    assertThat(1.0, is(notANumber()));
    // fails with the message
    // java.lang.AssertionError: Expected: is not a number got : <1.0>
}
```

目前已知的一些匹配器的基础类型(抽象类)

|                   类名                   |                                                     描述                                                     |
|:--------------------------------------:|:----------------------------------------------------------------------------------------------------------:|
|       org.hamcrest.CustomMatcher       |                         用于编写一次性匹配器的实用程序类,该类设计用于需要匿名内部类匹配器的场景.它不应该被实现匹配器的API设计人员使用                          |
|   org.hamcrest.CustomTypeSafeMatcher   | 用于编写一次性匹配器的实用程序类,这是CustomMatcher的一个变体,它首先对要匹配的参数进行类型检查.到那时`TypeSafeMatcher.matchesSafely`被调用时,参数保证非空且类型正确. |
|     org.hamcrest.DiagnosingMatcher     |                                                    TODO                                                    |
|      org.hamcrest.FeatureMatcher       |                              用于匹配对象特征的支持类.在子类中实现`featureValueOf()`以提取要匹配的特性.                               |
| org.hamcrest.core.ShortcutCombination  |                                                    TODO                                                    |
|   org.hamcrest.core.SubstringMatcher   |                                                    TODO                                                    |
| org.hamcrest.TypeSafeDiagnosingMatcher |           Matchers的方便基类,它需要特定类型的非空值,并将报告接收到的值被拒绝的原因.这实现了空检查,检查类型,然后强制转换.要使用,请实现`matchessafely()`           |
|      org.hamcrest.TypeSafeMatcher      |                                为需要特定类型的非空值的匹配器提供方便的基类.这只是实现空检查,检查类型,然后强制转换.                                |

我们匹配器中的第三种方法是便利工厂方法.我们静态导入此方法以在测试中使用匹配器:

```java
    import org.junit.jupiter.api.Test;

import static org.hamcrest.MatcherAssert.assertThat;
import static org.hamcrest.Matchers.*;
import static org.hamcrest.examples.tutorial.IsNotANumber.notANumber;

public class NumberTest {
    @Test
    public void testSquareRootOfMinusOneIsNotANumber() {
        assertThat(Math.sqrt(-1), is(notANumber()));
    }
}
```

即使该方法在每次调用时都会创建一个新的匹配器,也不应假定这是匹配器的唯一使用模式.因此,*应确保匹配器是无状态的*
,以便可以在匹配项之间重用单个实例.notANumber
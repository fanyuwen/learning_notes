### 赋值运算符
+ java语言里,赋值运算符作为表达式也是有值的,值就是等号右边的值,所以可以连续赋值
```java
    class Operate{
        public static void main(String[] args) {
            //先声明好变量
            int a,b,c;
            //可以连续赋值,每个变量都被赋值为1,因为赋值运算符是右结核性的,
            //所以是从右到左(先c = 1表达式的结果是1,再b = 1表达式的结果也是1,最后a = 1)
            a = b = c = 1;
        }
    }
```
+ 复合赋值运算符,java赋值运算符可以和所有算术运算符、二元位运算符结合成复合赋值运算符(**以下摘自《java编程语言(第三版)》**)  
  复合运算符有这样的概念:给定类型为T的变量var、值expr和二元运算符op有  
  var op= expr 等价于  
  var = (T)((var) op (expr))    *注意这边会有优先级和强制类型转换*
  只是上面的var只求值一次,意味着只有当op对相关的类型合法时,op=运算符才是合法的,
  例如: 不能把<<=(*按位左移赋值*)运算符作用于double变量,因为不能把<<运算符作用于double变量  
  注意: 上面的括号 表达式 `a *= b + 1`等同于`a = a * (b + 1)`但不等于`a = a * b + 1`  
  虽然`a += 1`与`++a`相同,但人们更常用也更愿意用`++`
```java
    class Operate{
        private static int index;
        public static void main(String[] args) {
            int[] arr = new int[10];
            //如下
            arr[index()] += 12;
            //和
            arr[index()] = arr[index()] + 12;
            //从表面的拆分来看表达式是等价的,但是上面的表达式index方法只执行了一次,下面的表达式index方法计算了2次
        }
        
        static int index(){
            return index++;
        }
    }
```
+ 字符串连接运算符(**java语法中唯一的一个运算符重载**)
  可以利用`+`把两个字符串连接在一起,如下:  
  `String boo = "boo";`  
  `String cry = boo + "hoo";`  
  `cry += "!";`  
  `System.out.println(cry);`  
  上面代码输出为: boohoo!
  只要有一个操作数是`String`,`+`运算符就被解释为字符串连接运算符,如果只有一个操作数是`String`,则另外一个就隐式转换为`String`类型

### 条件运算符
条件运算符根据一个布尔表达式的取值,让表达式的结果等于两个值中的一个,例如:  
`value = (useSetIt ? usersValue : defaultValue);`  
等价于  
``
if(useSetIt)
  value = usersValue;
else
  value = defaultValue;
``  
if语句和?:语句的主要区别在于后者有一个值(?:整体是一个表达式)
结果表达式(第二个和第三个)必须具有赋值相容的类型,一个结果表达式的类型必须不经显式强制转换就可以赋值给另一个类型,不管到底哪一个可以赋值给另一个  
条件运算符表达式的结果类型是两种结果表达式的类型中具有更大赋值相容性的哪一种,比如:  
`double scale = (haveIt ? 1 : 0.5);`  
两边是`int`(1)和`double`(0.5),因为`int`可以赋值给`double`,所以1被转换成1.0(`double`类型),整个条件表达式的结果是`double`类型  
对与引用类型也是适用该规则:如果一种类型可以赋值给另一种类型,结果类型是最小继承类型,如果两种类型赋值不相容,则运算不合法
```java
  class Father{}
  class Son extends Father{}
  class GrandSon extends Son{}
  class ConditionOperate{ 
    public static void main(String[] args) {
        //因为GrandSon的实例可以赋值给Son类型,所以这边整个表达式的类型是Son类型的
        //所以我理解的最小继承类型就是在整个继承结构(继承结构树)的靠上(越靠近父类)位置的类型
        Son son = bool() ? new Son() : new GrandSon();
    }
    static boolean bool(){
        return ((int)(Math.random() * 11) % 2) == 0;
    }
  }
```
结合性:**右结合性**,也就是说从右像左分组计算(是在`:`的右边的表达式是右表达式) 例如:  
`a ? b : c ? d : e` =  `a ? b : (c ? d : e)`所以要改变这个结合性只能通过括号提升`()` `(a ? b : c) ? d : e`  
稍微复杂一点的:  
`a ? b : c ? d : e ? f : g ? h : i` =  `a ? b : (c ? d : (e ? f : (g ? h : i)))`  
或者:  
`a ? b ? c ? d ? e ? f ? g ? h ? i : x8 : x7 : x6 : x5 : x4 : x3 : x2 : x1` = `a ? (b ? (c ? (d ? (e ? (f ? (g ? (h ? i : x8) : x7) : x6) : x5) : x4) : x3) : x2) : x1`  
***关于条件运算符的一个踩坑点***  
三目(条件)运算符的空指针问题(与java基本类型和包装类型拆装箱问题)

```java
    import java.util.HashMap;

public class ConditionExpressionProblem {
    //第一种情况
    void conditionExpressionNull_1() {
        //这个是阿里开发手册的一个规约的例子
        Integer a = 1;
        Integer b = 2;
        Integer c = null;
        Boolean flag = false;
        // a * b 的结果是int类型,那么c会强制拆箱成int类型,抛出NPE异常
        Integer result = (flag ? a * b : c);
    }

    //第二种情况(java 8以下会报错,java 8及以后的版本不会报错,详情见javase 8的语言规范(将条件表达式分成了3中表达式))
    void conditionExpressionNull_2() {
        Map<String, Boolean> map = new HashMap<>();
        //这里也会报NPE异常
        Boolean b = (map != null ? map.get("test") : false);
        //将上面那行代码用java7进行反编译
        // Boolean b = Boolean.valueOf(map == null ? false : ((Boolean)map.get("test")).booleanValue());
        //能看出上面空指针报错的点
        //===============================================
        //java 8的版本:
        Boolean b = (map != null ? map.get("test") : false);
        //首先第二个操作是'map.get("test")',虽然定义的泛型类型是Boolean,但是实际上在编译的时候泛型会被擦除,所以结果是Objec
        //那么根据规则,整个表达式会被判定为引用表达式,又根据定义:如果引用条件表达式出现在赋值上下文或调用上下文中,那么条件表达式就是
        //合成表达式,因为上面的语句就是一个赋值上下文,所以整个条件表达式就是合成表达式,又根据定义:合成的引用条件表达式的类型与其目标
        //类型相同,所以编译器可以推断出该表达式的第二个操作数和第三个操作数的结果应该都是Boolean类型.所以在编译的过程中,就都可以把他们
        //都转成Boolean类型即可,那么上面的代码在java8中反编译后的内容是: Boolean b = map == null ? Boolean.valueOf(false) : (Boolean)map.get("test");
        //但是在java7中没有这些规定,编译器只知道表达式的第二和第三位分别是基本类型和包装类型,而无法推断最终表达式类型,就会先把返回结果转换为基本类型,在变量赋值的时候再转换成包装类型
        //详细讲解参见java8的语言规范
    }
}
```
以下摘自java语法规范关于条件运算符的部分解释  
完整解释见  
[javase 7语言规范](https://docs.oracle.com/javase/specs/jls/se7/html/jls-15.html#jls-15.25)  
[javase 8语言规范](https://docs.oracle.com/javase/specs/jls/se8/html/jls-15.html#jls-15.25)  
[javase 21语言规范](https://docs.oracle.com/javase/specs/jls/se21/html/jls-15.html#jls-15.25)
以下摘自javase 8语言规范关于条件运算符的说明(部分):
> At run time, the first operand expression of the conditional expression is evaluated first. If necessary, unboxing conversion is performed on the result. 
> The resulting boolean value is then used to choose either the second or the third operand expression:  
> 在运行时,首先计算条件表达式的第一个操作数表达式. 如有必要,将对结果执行拆箱转换. 然后使用结果布尔值选择第二个或第三个操作数表达式:  
>> If the value of the first operand is true, then the second operand expression is chosen.  
>> 如果第一个操作数的值为真,则选择第二个操作数表达式.
>> If the value of the first operand is false, then the third operand expression is chosen.
>> 如果第一个操作数的值为false,则选择第三个操作数表达式.

> The chosen operand expression is then evaluated and the resulting value is converted to the type of the conditional expression as determined by the rules stated below.  
> 然后对选定的操作数表达式求值,并将结果值转换为条件表达式的类型,该类型由下面说明的规则确定.  
> This conversion may include boxing or unboxing conversion (§5.1.7, §5.1.8).  
> 这种转换可能包括装箱或拆箱转换(§5.1.7，§5.1.8).  
> The operand expression not chosen is not evaluated for that particular evaluation of the conditional expression.  
> **未选择的操作数表达式不会对条件表达式的特定求值进行求值.**

以下摘自javase 7语言规范关于条件运算符的说明(部分):
> If the second and third operands have the same type (which may be the null type), then that is the type of the conditional expression.
>> 如果第二个和第三个操作数具有相同的类型(可能是空类型),那么该类型就是条件表达式的类型.

> If one of the second and third operands is of primitive type T, and the type of the other is the result of applying boxing conversion (§5.1.7) to T, then the type of the conditional expression is T.  
>> 如果第二个和第三个操作数中的一个为基本类型T,另一个操作数的类型是对T应用装箱转换(第5.1.7节)的结果,则条件表达式的类型为T.  
>> eg. `a ? int : Integer`的结果就是int类型,基本类型

> If one of the second and third operands is of the null type and the type of the other is a reference type, then the type of the conditional expression is that reference type.  
>> 如果第二个和第三个操作数中的一个为null类型,而另一个操作数的类型为引用类型，则条件表达式的类型为该引用类型.

根据上面的描述总结:(由于条件运算符属于短路运算符,所以只有当走到那个分支的情况下才会算对应的值)
+ 当第二、第三位操作数分别为基本类型和包装类型时(无论是否是直接包装类型),其中包装对象会拆箱为基本类型(*如果不是直接包装类型然后升级到更大范围的基本类型*)做为整个表达式的类型**char(Character)遇到byte/short会提升为int(Integer)**  
  `a ? int : Integer` => int `a ? int : Double` => double `a ? double : Integer` => double
+ 当第二、第三位操作数都为包装类型,但是类型不一致,2个都会拆箱成基本类型再升级到更大范围的那个基本类型做为整个表达式的类型**char(Character)遇到byte/short会提升为int(Integer)**
  `a ? Double : Integer` => double `a ? Integer : Float` => float `a ? Integer : Long` => long
+ 其它情况(第二、第三位分别为基本类型和非包装类型对象、包装类型和非包装类型对象、为相同的包装类型、其它非包装对象类型)不受上面的`null`引用影响,但是还有其它情况
  + 第二、第三位分别为*基本类型*并且不相同,则整个表达式的类型为范围最大的基本类型**char(Character)遇到byte/short会提升为int(Integer)**
  + 第二、第三位分别为*基本类型*和*数字字面量*时,如果数字字面量的值在基本类型的表示范围之内,整个表达式的类型为该基本类型(如果用包装类型接收,则也会转换为包装类型)
    `a ? byte : 12` => byte `a ? short : 4443` => short `a ? char : 65539` => int

### 等号运算符
+ *判断相等或者不等的操作数都是引用类型的话,左右两边的操作数的类型必须是兼容类型(要么相同,要么有继承关系),即使都为null也不行*
```java
    void equalOperate(){
        Integer i = null;
        String s = null;
        System.out.println(i == s);//编译报错,两个类型不兼容
        Object o = null;
        System.out.println(s == o);//能够运行结果为`true`,因为String是Object的子类(兼容)
    }
```
+ 包装类型与基本类型进行比较,包装类型会拆包为基本类型(**要注意包装类型是否为null**),然后比较的左右两边的操作数会根据情况进行类型提升(双方数据类型不同,小范围的提升到大范围),再进行比较
```java
    void equalOperate(){
        Integer i = null;
        byte b = 1;
        System.out.println(i == b);//此处能够进行比较,但是因为i为null,所以会报NullPointException,
                                   // 实际上比较的时候b会被类型提升到int再比较
    }
```
+ 基本类型不能与非包装类型的引用类型进行比较
```java
    void equalOperate(){
        byte b = 1;
        String s = null;
        Object o = null;
        System.out.println(s == b);//编译报错,不能比较String和byte
        System.out.println(o == b);//编译报错,不能比较Object和byte
    }
```
+ 基本类型与基本类型,左右两边操作数会根据情况进行范围提升(双方数据类型不同,小范围的提升到大范围),再进行比较
```java
    void equalOperate(){
        int a = 1;
        short s = 2;
        System.out.println(a == s);//运行结果`false`,变量s会提升到int类型再比较
    }
```


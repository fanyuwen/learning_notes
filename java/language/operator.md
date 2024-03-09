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
***关于条件运算符的一个踩坑点***

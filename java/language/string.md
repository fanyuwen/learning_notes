# 字符串拼接  
  > 从java5开始字符串运算符+就会被编译器优化为使用StringBuilder  
  > 进行拼接,下面的代码
  ```java
    public class Main{
        public static void append(String s1,String s2,String s3) {
            String s = s1 + s2 + s3;
            //会被优化为 StringBuilder sb = new StringBuilder();
            //sb.append(s1).append(s2).append(s3);
            //String s = sb.toString();
        }
    }
  ```
  > 但是在循环里就不一样了
  ```java
    public class Main {
        public static void loopAppend(int num, String s1, String s2, String s3) {
            for (int i = 0; i < num; i++) {
                String s = s1 + s2 + i;
                //这个会被优化为StringBuilder,因为不涉及外部的字符串拼接,所以
                //直接用StringBuilder性能相差无几
            }
            //循环拼接外部字符串
            String str = "";
            for (int i = 0; i < num; i++) {
                str += s1 + s2 + s3 + i;
                //这个也会被优化为StringBuilder,但是是在每次循环的时候去创建StringBuilder
                //所以就回存在性能上的开销,本身StringBuilder创建和销毁的开销,还有toString方法调用的开销
            }
            //下面是面对循环拼接外部字符串的StringBuilder的替代
            StringBuilder sb = new StringBuilder();
            for (int i = 0; i < num; i++) {
                sb.append(s1).append(s2).append(s3).append(i);
            }
            str = sb.toString();
            System.out.println(str);
      }
    }
  ```
+ 编译器会将String拼接优化成使用StringBuilder,但是还是有一些缺陷的.
主要体现在循环内使用字符串拼接,编译器不会创建单个StringBuilder以复用
+ 对于多次循环内拼接一个字符串的需求:
StringBuilder很快,因为其避免了n次*new*对象、销毁对象的操作, 
n - 1次将StringBuilder转换成String的操作
+ StringBuilder拼接不适用于循环内每次拼接即用的操作方式.
因为编译器优化后的String拼接也是使用StringBuilder,两者的效率一样.
后者写起来方便
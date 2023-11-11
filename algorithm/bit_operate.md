- 异或 交换两个整数的值
  ```java
    public class Main{
        public static void xor(String[] args){
            int temp = a;
            a = b;
            b = temp;
            //
            a = a ^ b;// a = a ^ b;
            b = a ^ b;// b = (a ^ b) ^ b = a;
            a = a ^ b;// a = (a ^ b) ^ a = b;
        }
    } 
  ```
- 如何获取一个数字最右侧的1
  ```java
    public class Main(){
        public static int rightmost_bit(int a){
            return a & (~a + 1);
            // a & -a
            // 1010 -> 0010
            // a & (~a + 1)
            // 1010 & (0101 + 1)
            // 1010 & 0110
            // 0010
        }
    }
  ```
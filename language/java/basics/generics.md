# 方法范型
昨天看别人写的代码,发现了一个有趣的点
```java
class Generics {

    public static void main(String[] args) {
        //这里可以用任何类型的变量接收,者从侧面也能看出,范型方法的范型定义的约束似乎没什么用(可能也只是局限于java8以下吧)
        //如果实际返回的是非null对象,是会报ClassCastException
        List<String> stringList = get();
    }

    /**
     * 定义个范型方法,范型有一个上界约束,所以调用这个方法的返回结果必须要满足这个约束,
     * 但是实际的情况是,它可以返回任何类型而不会出现编译错误
     * 
     * @return T
     * @param <T> 范型
     */
    static <T extends Number> T get() {
        return null;
    }
}

```
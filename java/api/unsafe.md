## java中的Unsafe
java核心库(尤其是java.util.concurrent包)下很多底层都使用了unsafe类的方法,但是由于这个类的使用非常偏底层,就如它的名字一样不安全,除非
你知道自己要做什么并且明白如何使用它
> Unsafe是用于在实质上扩展Java语言表达能力、便于在更高层(Java层)代码里实现原本要在更低层(C层)实现的核心库功能用的,这些功能包括裸内存的申请/释放/访问,低层硬件的atomic/volatile支持,创建未初始化对象等,它原本的设计就只应该被标准库使用
>> 1. Unsafe有可能在未来的jdk版本移除或者不允许java应用代码使用,这一点可能导致使用了Unsafe的应用无法运行在高版本的jdk
>> 2. Unsafe的不少方法中必须提供原始地址(内存地址)和被替换对象的地址,偏移量要自己计算,一旦出现问题就是JVM崩溃级别的异常,会导致整个JVM实例崩溃,表现为应用程序直接crash掉
>> 3. Unsafe提供的直接内存访问的方法中使用的内存不受JVM管理(无法被GC),需要手动管理,一旦出现疏忽很有可能成为内存泄漏的源头

Unsafe类的静态初始化块代码里:
```java
    public final class Unsafe {
    /*
     * 定义了自己类型的静态属性
     */
    private static final Unsafe theUnsafe;

    //一个本地方法
    private static native void registerNatives();

    /**
     * 从该方法实现来看,如果要从外部获取Unsafe,就必须要对当前调用该方法的类的类加载器进行
     * 判断,如果不是系统类加载器(也就是不是java核心库里的类),会抛出一个SecurityException
     * @return Unsafe对象
     */
    @CallerSensitive
    public static Unsafe getUnsafe() {
        Class var0 = Reflection.getCallerClass();
        if (!VM.isSystemDomainLoader(var0.getClassLoader())) {
            throw new SecurityException("Unsafe");
        } else {
            return theUnsafe;
        }
    }

    static {
        registerNatives();
        Reflection.registerMethodsToFilter(Unsafe.class, new String[]{"getUnsafe"});
        theUnsafe = new Unsafe();//这边会对静态属性进行初始化
        //...下面省略...
    }
}
```
所以一般我们自己的应用要获取Unsafe实例,就只能通过反射获取了(这也是大部分第三方框架的实现方式)
```java
/*
 * 反射获取Unsafe
 */
static Unsafe getUnsafe() throws NoSuchFieldException, IllegalAccessException{
    Field field = Unsafe.class.getDeclaredField("theUnsafe");
    field.setAccessible(true);
    return (Unsafe) field.get(null);
}
```
## java Unsafe关于数组操作的api
这个是在很多第三方工具库的源代码里看到的(netty、reactor、disruptor),关于数组的底层操作
+ `public native int arrayBaseOffset(Class<?> var1);` 返回数组类型的第一个元素的偏移地址(基础偏移地址),如果`arrayIndexScale`方法返回的比例因子不为0,
   你可以通过结合基础偏移地址和比例因子访问数组的所有元素,Unsafe中已经初始化了很多类似的常量如 `ARRAY_BOOLEAN_BASE_OFFSET`
+ `public native int arrayIndexScale(Class<?> var1);` 返回数组类型的比例因子(另一个说法是数组当中元素的占用内存的大小)(其实就是数据中元素偏移地址的增量,因为数组中的元素的地址是连续的),
   此方法不适用于数组类型为**narrow**类型的数组,**narrow**类型的数组类型使用此方法会返回0(这里**narrow**应该是狭义的意思,但是具体指哪些类型暂时不明确,目前查了很多资料也没有结果)
   Unsafe中已经初始化了很多类似的常量如 `ARRAY_BOOLEAN_INDEX_SCALE`等
```java
    private final static Unsafe unsafe = getUnsafe();
    //如下代码展示字符串数组的示例
    String[] array = new String[10];
    //获取数组对象的基础偏移地址
    int arrayBaseOffset = unsafe.arrayBaseOffset(String[].class);
    //获取数组对象元素的偏移地址增量,根据之前的经验,发现基本上都是2的指数倍,大多数是4或者8
    int arrayIndexScale = unsafe.arrayIndexScale(String[].class);
    //通过unsafe的putObject、putOrderedObject、putObjectVolatile等方法实现对数组的写入
    long offset = arrayBaseOffset + 2L * arrayIndexScale;//计算数组下标第2个元素的偏移量
    unsafe.putObject(array, offset, "12312");//设置对应位置的数组元素
    //但是因为arrayIndexScale是2的指数倍,所以会有一个优化,如果arrayIndexScale是4,则意味着左移2位,是8,则左移3位
    int shift;
    if(arrayIndexScale ==  4){
        shift = 2;
    } else if(arrayIndexScale ==  8){
        shift = 3;
    } else {
        //一般情况下如果不是4也不是8,则默认为识别不了,所以就报错
        throw new IllegalStateException("Unknown pointer size: " + arrayIndexScale);
    }
    //上面的代码是我看netty和disruptor都是这么实现的,也可以用Integer.numberOfTrailingZeros()计算二进制尾部的0的个数来实现
    offset = arrayBaseOffset + (2L << shift);//通过左移位运算符去计算偏移量
    unsafe.putObject(array, offset, "12312");//设置对应位置的数组元素
    //获取对应数组位置的代码类似
    unsafe.getObject(array, arrayBaseOffset + 2L * arrayIndexScale);
    unsafe.getObject(array, arrayBaseOffset + 2L << shift);
```
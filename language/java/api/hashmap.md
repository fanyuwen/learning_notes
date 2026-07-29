### hashmap的扩容
hashmap本身有扩容机制,当存放的元素数量到达了阈值就会进行扩容,在底层会扩容为原来数据的2倍,扩容阈值为数组长度的0.75,所以在往hashmap中存放元素的高效做法是提前设置好hashmap的容量,比如:存放100个元素的话,就可以进行计算,如果想不进行扩容的话,则(默认扩容因子为0.75)需要100/0.75≈133,则可以将hashmap的容量设置为133,但实际的数组长度是256,因为hashmap需要保证数组的长度为2的整数幂,实际的计算是大于或者等于你输入的长度的最小2的整数幂值,所以是256,hashmap默认的初始容量是16,扩容阈值则为12
### map的entry
jdk类库中已经提供了`Map.Entry`的简单封装,是`java.util.AbstractMap.SimpleEntry`该实现为可变实现,还有一个`java.util.AbstractMap.SimpleImmutableEntry`顾名思义该实现是不可变的实现,调用`setValue`方法会报`UnsupportedOperationException`异常,以后在涉及需要使用`Map.Entry`的实现时,就不需要再自定义一个自己的实现了
### `merge`方法(1.8+新增的api)
+ 函数原型: `V merge(K key, V newValue, BiFunction<? super V, ? super V, ? extends V> remappingFunction)`
> 注意: 无论是`HashMap`还是`ConcurrentHashMap`都不允许`newValue`为空,`ConcurrentHashMap`同时不允许`key`为空

往`map`中根据`key`合并数据,如果`key`不存在则新增该`key`和`newValue`,如果原来`key`对应的`oldValue`为`null`则直接将`oldValue`替换为`newValue`,否则将`newValue`和`oldValue`传给`remappingFunction`函数式接口的`apply`方法,函数调用的结果不为空则替换`oldValue`,为空则删除该`key`

```java
public static void main(String[] args) {
    Map<String, Integer> map = new HashMap<>();
    //第一次调用因为不含有该key(字符串1),所以直接将key 1和value 1放入map,当前map("1" -> 1) 
    map.merge("1", 1, (oldValue, newValue) -> oldValue + newValue);
    //第二次调用因为已经存在了key(字符串1),所以会执行后面的lambda表达式,原来的值1 + 现在的新值1 = 2,将结果2更新到map中对应的key为"1",当前map("1" -> 2)
    map.merge("1", 1, (oldValue, newValue) -> oldValue + newValue);
}
```
### `computeIfAbsent`方法(1.8+新增的api)
+ 函数原型: `V computeIfAbsent(K key, Function<? super K, ? extends V> mappingFunction)`
> 从1.8开始,被hash到同一个table数组的不同key,创建的node是通过尾插法入队,但是`computeIfAbsent`方法里入队的方式还是老版的头插法,
> 还有一个点,从源码看,会在方法入口时判断容量阈值和扩容包括初始化

如果`map`中指定的`key`不存在或者对应的`value`为空,则会调用`mappingFunction`的`apply`方法传入`key`,该函数式接口调用返回值不为`null`,则会将该值和对应的`key`存入`map`中,
如果`map`中已经存在该`key`并且对应的值不为`null`则直接返回该值
```java
public static void main(String[] args) {
    Map<String, Integer> map = new HashMap<>();
    //第一次调用,因为不存在该key字符串"1",所以会调用lambda表达式,将key"1"传入,返回1,放入map,当前map("1" -> 1)
    map.computeIfAbsent("1", key -> 1);
    //第二次调用,因为已经存在了该key"1",所以lambda表达式不会执行,map不会变化,当前map("1" -> 1)
    map.computeIfAbsent("1", key -> 1);
}
```
### `computeIfPresent`方法(1.8+新增的api)
> 这里有个细节,在调用完`remappingFunction`函数式接口后,会判断一下`modCount`是否有更新过`map`

+ 函数原型: `V computeIfPresent(K key, BiFunction<? super K, ? super V, ? extends V> remappingFunction)`
如果`map`中指定的`key`存在并且对应的`oldValue`不为`null`,则会调用`remappingFunction`方法传入`key`和对应的`oldValue`,返回新的`newValue`,如果`newValue`为`null`则将该`key`从`map`中删除,
不为`null`则替换原来`oldValue`,最后返回该`newValue`,其它情况方法返回`null`
```java
public static void main(String[] args) {
    Map<String, Integer> map = new HashMap<>();
    //第一次调用,因为不存在该key字符串"1",所以不会调用lambda表达式,所以map不会变化,当前map(empty)
    map.computeIfPresent("1", (key, value) -> 1);
    //这里往map直接放入"1" -> 1
    map.put("1", 1);
    //第二次调用,因为已经存在了该key"1",所以lambda表达式会执行,传入对应的key("1"),value(1),返回1+1=2,结果用于更新到该key上,当前map("1" -> 2)
    map.computeIfPresent("1", (key, value) -> value + 1);
}
```
### `compute`方法(1.8+新增的api)
+ 函数原型: `V compute(K key, BiFunction<? super K, ? super V, ? extends V> remappingFunction)`
从函数原型看似乎是跟`computeIfPresent`方法一样,但是区别是,无论是否存在该`key`,或者说对应的`oldValue`是否为`null`,都会去调用`remappingFunction`,只是如果`map`不存在该`key`,调用的第二个参数是`null`
如果方法的返回值`newValue`为`null`,则会将该`key`从`map`中删除掉(如果存在的话),否则新增该`key`和`newValue`(不存在或者`oldValue`为`null`的情况),更新该`key`的`value`为`newValue`,最后返回该`newValue`
```java
public static void main(String[] args) {
    Map<String, Integer> map = new HashMap<>();
    //第一次调用,因为不论存不存在该key,都会调用lambda表达式,只是value传null,返回1,不为null会将该结果与key绑定放入map中,当前map("1" -> 1)
    map.compute("1", (key, value) -> 1);
    //第二次调用,不论存不存在该key"1",还是都会执行该lambda表达式,因为当前存在key"1",并且对应的value为1,传入表达式中结果为1+1=2,更新到当前的key中,当前map("1" -> 2)
    map.compute("1", (key, value) -> value + 1);
}
```
### `replace`方法(1.8+新增的api)
+ 函数原型: `V replace(K key, V value)`
对`map`里指定的`key`进行替换,只有当该`key`存在才会进行替换,并返回替换`key`之前的旧值,否则返回`null`
```java
public static void main(String[] args) {
    Map<String, Integer> map = new HashMap<>();
    map.put("1", 1);
    //因为前面已经往map中放置了key("1"),所以这里调用replace会将原来的值1替换为2,并返回旧值1,当前map("1" -> 2)
    map.replace("1", 2);
}
```
+ 函数原型: `boolean replace(K key, V oldValue, V newValue)`
> 该方法非常适合cas循环判断更新,但是hashMap感觉最好不要这么做,本身不是线程安全的

对`map`里指定的key进行替换,必须要对应的旧值等于参数中的`oldValue`才会将参数中的`newValue`替换旧值,并返回`true`,否则返回`false`
```java
public static void main(String[] args) {
    Map<String, Integer> map = new HashMap<>();
    map.put("1", 1);
    //因为已经存在key("1"),对应的值为1,所以这里会进行替换将旧值1替换为2,方法返回true,当前map("1" -> 2)
    map.replace("1", 1, 2);
}
```
### ``
+ 函数原型: `void replaceAll(BiFunction<? super K, ? super V, ? extends V> function)`
> 就算该函数返回`null`,也不会删除该`key`,而是将该`key`的值设置为`null`

对当前`map`的全部`key-value`对进行参数函数调用替换
```java
public static void main(String[] args) {
    Map<String, Integer> map = new HashMap<>();
    map.put("1", 1);
    map.put("2", 2);
    //会将map中的所有key-value对应用到参数函数中,返回的值会替换当前的value,该函数会将原来的value+1,所以当前的map("1" -> 2,"2" -> 3)
    map.replaceAll((k, v) -> v + 1);
}
```
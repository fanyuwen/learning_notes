### hashmap的扩容
hashmap本身有扩容机制,当存放的元素数量到达了阈值就会进行扩容,在底层会扩容为原来数据的2倍,扩容阈值为数组长度的0.75,所以在往hashmap中存放元素的高效做法是提前设置好hashmap的容量,比如:存放100个元素的话,就可以进行计算,如果想不进行扩容的话,则(默认扩容因子为0.75)需要100/0.75≈133,则可以将hashmap的容量设置为133,但实际的数组长度是256,因为hashmap需要保证数组的长度为2的整数幂,实际的计算是大于或者等于你输入的长度的最小2的整数幂值,所以是256,hashmap默认的初始容量是16,扩容阈值则为12
### map的entry
jdk类库中已经提供了`Map.Entry`的简单封装,是`java.util.AbstractMap.SimpleEntry`该实现为可变实现,还有一个`java.util.AbstractMap.SimpleImmutableEntry`顾名思义该实现是不可变的实现,调用`setValue`方法会报`UnsupportedOperationException`异常,以后在涉及需要使用`Map.Entry`的实现时,就不需要再自定义一个自己的实现了
### `merge`方法(1.8新增的api)
函数原型: `V merge(K key, V newValue, BiFunction<? super V, ? super V, ? extends V> remappingFunction)`
> 注意: 无论是`HashMap`还是`ConcurrentHashMap`都不允许`newValue`为空,`ConcurrentHashMap`同时不允许`key`为空

往`map`中根据`key`合并数据,如果`key`不存在则新增该`key`和`newValue`,如果原来`key`对应的`oldValue`为`null`则直接将`oldValue`替换为`newValue`,否则将`newValue`和`oldValue`传给`remappingFunction`函数式接口的`apply`方法,函数调用的结果不为空则替换`oldValue`,为空则删除该`key`
### `computeIfAbsent`方法(1.8新增的api)
函数原型: `V computeIfAbsent(K key, Function<? super K, ? extends V> mappingFunction)`
> 从1.8开始,被hash到同一个table数组的不同key,创建的node是通过尾插法入队,但是`computeIfAbsent`方法里入队的方式还是老版的头插法,
> 还有一个点,从源码看,会在方法入口时判断容量阈值和扩容包括初始化

如果`map`中指定的`key`不存在或者对应的`value`为空,则会调用`mappingFunction`的`apply`方法传入`key`,该函数式接口调用返回值不为`null`,则会将该值和对应的`key`存入`map`中,
如果`map`中已经存在该`key`并且对应的值不为`null`则直接返回该值
### `computeIfPresent`方法(1.8新增的api)
> 这里有个细节,在调用完`remappingFunction`函数式接口后,会判断一下`modCount`是否有更新过`map`

函数原型: `V computeIfPresent(K key, BiFunction<? super K, ? super V, ? extends V> remappingFunction)`
如果`map`中指定的`key`存在并且对应的`oldValue`不为`null`,则会调用`remappingFunction`方法传入`key`和对应的`oldValue`,返回新的`newValue`,如果`newValue`为`null`则将该`key`从`map`中删除,
不为`null`则替换原来`oldValue`,最后返回该`newValue`,其它情况方法返回`null`
### `compute`方法(1.8新增的api)
函数原型: `V compute(K key, BiFunction<? super K, ? super V, ? extends V> remappingFunction)`
从函数原型看似乎是根`computeIfPresent`方法一样,但是区别是,无论是否存在该`key`,或者说对应的`oldValue`是否为`null`,都会去调用`remappingFunction`,只是如果`map`不存在该`key`,调用的第二个参数是`null`
如果方法的返回值`newValue`为`null`,则会将该`key`从`map`中删除掉(如果存在的话),否则新增该`key`和`newValue`(不存在或者`oldValue`为`null`的情况),更新该`key`的`value`为`newValue`,最后返回该`newValue`
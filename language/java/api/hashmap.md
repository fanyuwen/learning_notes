### hashmap的扩容
hashmap本身有扩容机制,当存放的元素数量到达了阈值就会进行扩容,在底层会扩容为原来数据的2倍,扩容阈值为数组长度的0.75,所以在往hashmap中存放元素的高效做法是提前设置好hashmap的容量,比如:存放100个元素的话,就可以进行计算,如果想不进行扩容的话,则(默认扩容因子为0.75)需要100/0.75≈133,则可以将hashmap的容量设置为133,但实际的数组长度是256,因为hashmap需要保证数组的长度为2的整数幂,实际的计算是大于或者等于你输入的长度的最小2的整数幂值,所以是256,hashmap默认的初始容量是16,扩容阈值则为12
### map的entry
jdk类库中已经提供了`Map.Entry`的简单封装,是`java.util.AbstractMap.SimpleEntry`该实现为可变实现,还有一个`java.util.AbstractMap.SimpleImmutableEntry`顾名思义该实现是不可变的实现,调用`setValue`方法会报`UnsupportedOperationException`异常,以后在涉及需要使用`Map.Entry`的实现时,就不需要再自定义一个自己的实现了

### map的entry封装
jdk类库中已经提供了`Map.Entry`的简单封装,是`java.util.AbstractMap.SimpleEntry`该实现为可变实现,还有一个`java.util.AbstractMap.SimpleImmutableEntry`顾名思义该实现是不可变的实现,调用`setValue`方法会报`UnsupportedOperationException`异常,以后在涉及需要使用`Map.Entry`的实现时,就不需要再自定义一个自己的实现了

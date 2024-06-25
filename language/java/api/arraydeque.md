### jdk1.6新增的双端数组队列,可以再数组头和数组尾进行元素的新增和删除
可以作为经典的数据结构*队列*和*栈*的实现,对标`java.util.LinkedList`,底层实现存在区别,一般使用都是用`java.util.ArrayDeque`  
默认构造函数初始化数组长度为16
``public ArrayDeque() { elements = new Object[16]; }``

带有指定参数的构造参数``public ArrayDeque(int numElements) { allocateElements(numElements); }``
```java
    //创建指定数组,指定数组的长度会再调用calculateSize方法计算所得
    private void allocateElements(int numElements) {
        elements = new Object[calculateSize(numElements)];
    }
    //这是标准的计算指定数值最近的2的整数倍的数(向上取整)
    private static int calculateSize(int numElements) {
        int initialCapacity = MIN_INITIAL_CAPACITY;
        // Find the best power of two to hold elements.
        // Tests "<=" because arrays aren't kept full.
        if (numElements >= initialCapacity) {
            //通过不断地与自己右移指定位数的数进行或运算会不断地把低位的数据填满1,最后再加1,就是2的整数倍
            initialCapacity = numElements;
            initialCapacity |= (initialCapacity >>>  1);
            initialCapacity |= (initialCapacity >>>  2);
            initialCapacity |= (initialCapacity >>>  4);
            initialCapacity |= (initialCapacity >>>  8);
            initialCapacity |= (initialCapacity >>> 16);
            initialCapacity++;
            //溢出的情况
            if (initialCapacity < 0)   // Too many elements, must back off
                initialCapacity >>>= 1;// Good luck allocating 2 ^ 30 elements
        }
        return initialCapacity;
    }
```
+ 数组头添加元素
```java
    //从头添加元素,头索引减法
    public void addFirst(E e) {
        if (e == null)
            throw new NullPointerException();
        //head是头索引,计算下一个存放位置是通过减一,而且是先计算下一个位置,然后进行元素存放
        elements[head = (head - 1) & (elements.length - 1)] = e;
        //这里判断如果头索引和尾索引相等,意味着整个数组的位置都用完了,所以要进行扩容
        if (head == tail)
            doubleCapacity();
    }
```
+ 数组头删除元素
```java
    //从头删除元素,头索引加法
    public E pollFirst() {
        int h = head;
        @SuppressWarnings("unchecked")
        E result = (E) elements[h];
        // Element is null if deque empty
        if (result == null)
            return null;
        //先将对应的头索引位置的元素置为null,
        elements[h] = null;     // Must null out slot
        //然后计算回退到上一个索引,也就是头索引加一
        head = (h + 1) & (elements.length - 1);
        return result;
    }
```
+ 数组尾添加元素
```java
    //从尾添加元素,尾索引加法
    public void addLast(E e) {
        if (e == null)
            throw new NullPointerException();
        //先将元素放置到当前尾索引的位置
        elements[tail] = e;
        //然后去计算下一个尾索引的位置是否与头索引相等,相等就需要扩容
        if ( (tail = (tail + 1) & (elements.length - 1)) == head)
            doubleCapacity();
    }
```
+ 数组尾删除元素
```java
    //从尾删除元素,尾索引减法
    public E pollLast() {
        //计算尾索引的回退上一个值,通过在原来的索引上去减一
        int t = (tail - 1) & (elements.length - 1);
        @SuppressWarnings("unchecked")
        E result = (E) elements[t];
        if (result == null)
            return null;
        //将对应位置的元素置为null
        elements[t] = null;
        //将回退的索引赋值给尾索引
        tail = t;
        return result;
    }
```
+ 底层数组扩容
```java
    //将数组扩容为原来的2倍,然后进行数据的赋值
    private void doubleCapacity() {
        assert head == tail;//调用这个扩容方法的条件,头索引=尾索引
        //如果是需要扩容,必然是尾索引在前,头索引在后
        //将从头索引开始到数组尾部的数据赋值到新数组的开头,然后从0到尾索引赋值到新数组的中间(也就是原来数组的长度)
        int p = head;
        int n = elements.length;
        int r = n - p; // number of elements to the right of p
        int newCapacity = n << 1;
        if (newCapacity < 0)
            throw new IllegalStateException("Sorry, deque too big");
        Object[] a = new Object[newCapacity];
        System.arraycopy(elements, p, a, 0, r);
        System.arraycopy(elements, 0, a, r, p);
        elements = a;
        head = 0;
        tail = n;
    }
```

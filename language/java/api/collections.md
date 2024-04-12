# Collections工具类的使用
+ 在需要返回集合、列表、映射等结果的方法中,如果需要返回一个空的结果集,不建议返回`null`,而是可以调用empty开头的静态方法  
  `public static final <T> List<T> emptyList();`空列表  
  `public static final <T> Set<T> emptySet();`空集合  
  `public static final <K,V> Map<K,V> emptyMap();`空映射
+ 在需要返回集合、列表、映射等结果的方法中,如果返回的结果集里只有一个元素,可以调用singleton开头的静态方法  
  `public static <T> List<T> singletonList(T o);`单元素列表  
  `public static <K,V> Map<K,V> singletonMap(K key, V value);`单个键值对映射  
  `public static <T> Set<T> singleton(T o);`单个元素的集合
+ 在需要返回集合、列表、映射等结果的方法中,如果需要返回一个不可变的结果集,可以调用unmodifiable开头的静态方法  
  `public static <T> List<T> unmodifiableList(List<? extends T> list);`返回不可修改列表  
  `public static <T> Set<T> unmodifiableSet(Set<? extends T> s);`返回不可修改集合  
  `public static <T> Collection<T> unmodifiableCollection(Collection<? extends T> c);`返回不可修修改Collection  
  `public static <K,V> Map<K,V> unmodifiableMap(Map<? extends K, ? extends V> m);`返回不可修改映射
+ 在需要返回集合、列表、映射等结果的方法中,如果需要返回一个同步线程安全的结果集,可以调用synchronized开头的静态方法,
  这类方法有一类重载方法,第二个参数传入一个同步对象,默认一个参数的版本的同步对象是返回的(同步版本)对象(`this`),还要注意在使用迭代器iterator(也包括listIterator/分割器spliterator/stream)的时候,要在迭代器外部去synchronized同步对象
  ```java
    import java.util.Collection;
    import java.util.Collections;import java.util.Iterator;
    class CollectionsTest{
        private static final Object lock = new Object();
        static <T> void fun(){
            Collection<T> collection = generateCollection();
            Collection<T> synchronizedCollection = Collections.synchronizedCollection(collection);//单个参数的方法,同步对象就是参数集合
        
            T element = get();
            //多线程调用这些常规的方法都是线程安全的(还有其它方法)
            synchronizedCollection.add(element);
            element = synchronizedCollection.remove();
            //在获取迭代器或者分割器的时候需要在外部进行显示地获取锁
            synchronized (synchronizedCollection){
                Iterator<T> iterable = synchronizedCollection.iterator();
                while (iterable.hasNext()){
                    System.out.println(iterable.next());
                }
            }
  
            collection = generateCollection();
            //显示地传递一个锁对象
            synchronizedCollection = Collections.synchronizedCollection(collection, lock);
            //在进行迭代器获取的时候,也是先同步锁对象
            synchronized (lock){
                Iterator<T> iterable = synchronizedCollection.iterator();
                while (iterable.hasNext()){
                    System.out.println(iterable.next());
                }
            }
  
        }
    }
  ```
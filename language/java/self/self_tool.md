### 自定义实现
```java
import java.util.Objects;

/**
 * 这是参考的{@link java.lang.ThreadLocal}的ThreadLocalMap的里的map实现,采用的是开放寻址法
 * 这是固定容量的,不会扩容,创建时必须指定容量,性能这块还没有测试过
 *
 * @param <K>
 * @param <V>
 */
public final class CustomMap<K, V> {

    private static final class Node<K, V> {
        private final K key;
        private V value;
        private final int index;

        public Node(K key, V value, int index) {
            this.key = key;
            this.value = value;
            this.index = index;
        }

        @Override
        public String toString() {
            return key + "=" + value;
        }
    }

    private static final Object NULL = new Object();

    private Node<K, V>[] elements;

    private int size;

    @SuppressWarnings("unchecked")
    public CustomMap(final int size) {
        elements = new Node[len(size)];
    }

    public static void main(String[] args) {
        //简单的测试用例
        CustomMap<String, Integer> map = new CustomMap<>(10);
        map.put(null, 0);
        map.put("1", 1);
        map.put("2", 2);
        map.put("3", 3);
        map.put("4", 4);
        map.put("5", 5);
        map.put("6", 6);
        map.put("2", 7);

        System.out.println(map);
    }

    /**
     * 找寻指定的容量的最大的2的次幂
     *
     * @param size 指定的容量
     * @return 实际的容量(保证是2的次幂)
     */
    static int len(int size) {
        int s = size - 1;
        s |= s >>> 16;
        s |= s >>> 8;
        s |= s >>> 4;
        s |= s >>> 2;
        return (s | s >>> 1) + 1;
    }

    /**
     * 获取指定对象的hash值
     *
     * @param key 要获取hash值的对象
     * @return hash计算值
     */
    int hash(Object key) {
        int h = key == null ? 0 : key.hashCode();
        return h ^ (h >>> 16);
    }

    /**
     * 根据指定的key获取对应的值
     * 
     * @param key 对象key
     * @return 返回映射的值,如果不存在则返回NULL
     */
    public V get(K key) {
        int hash = hash(key);
        int index = hash & (elements.length - 1);
        Node<K, V> exist;
        while (Objects.nonNull(exist = elements[index])) {
            if (Objects.equals(key, exist.key)) {
                return exist.value;
            }
            index++;
        }
        return null;
    }

    /**
     * 放置键值对对象
     * 
     * @param key 对象键
     * @param value 映射值
     * @return 返回是否放置成功
     */
    public boolean put(K key, V value) {
        if (size == elements.length) {
            return false;
        }
        int hash = hash(key);
        int index = hash & (elements.length - 1);
        Node<K, V> exist;
        while (Objects.nonNull(exist = elements[index])) {
            if (Objects.equals(exist.key, key)) {
                exist.value = value;
                return true;
            }
            index++;
        }
        elements[index] = new Node<>(key, value, index);
        size++;
        return true;
    }

    @Override
    public String toString() {
        StringBuilder sb = new StringBuilder("{");
        int size;
        if ((size = this.size) <= 0) {
            return "{}";
        }
        for (int i = 0; i < size - 1; i++) {
            sb.append(elements[i].toString());
            sb.append(",");
        }
        sb.append(elements[size - 1].toString());
        return sb.append("}").toString();
    }
}
```

### 单例对象互相引用并初始化
两个单例对象之间需要互相引用,互相的引用也是需要引用对应的单例实例,目前的实现如下:
```java
    final class SingleObjA {
        static final SingleObjA objA = new SingleObjA();

        private final SingleObjB objB;

        private SingleObjA() {
            objB = SingleObjB.register(this);
        }

    }

    final class SingleObjB {
        //目前看来无法申明为final常量
        static SingleObjB objB;

        private final SingleObjA objA;

        static {
            //这是为了保证先访问SingleObjB实例的情况,需要确保初始化的顺序,因为实际并不使用,所以只是简单的忽略
            SingleObjA ignore = SingleObjA.objA;
        }

        private static SingleObjB register(SingleObjA a) {
            //这里需要判断一下,防止重复赋值
            if (objB != null) {
                return objB;
            }
            return SingleObjB.objB = new SingleObjB(a);
        }

        private SingleObjB(SingleObjA objA) {
            this.objA = objA;
        }
    }
```

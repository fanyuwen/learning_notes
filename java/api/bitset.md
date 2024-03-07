### 位向量 BitSet
所有的位由非负整数索引,单个索引位可以被检查、设置或清除,可以通过逻辑与(logical AND)、逻辑包容或(logical inclusive OR)和逻辑排外或(logical exclusive OR)操作来修改另一个BitSet的内容
默认情况下,集合中的所有位初始值都为false

因为java基本类型中内存占用最大的类型是`long`和`double`都是64位(8个字节),但由于底层实现有区别,所以就采用long类型来进行处理,一个long类型可以存储64个数字,也就是按照二进制位的索引去设置数字(第一位是1,第二位是2,以此类推)
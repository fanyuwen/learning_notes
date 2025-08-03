+ 如何快速合并2个字典 @3.5+
```python
# 相同的`key`按照右边覆盖左边,先后顺序
x = {'a':1, 'b':2}
y = {'b':3, 'c':4}
z = {**x, **y}
print(z) # {'c':4, 'a':1, 'b':3} 或 {'a': 1, 'b': 3, 'c': 4}
```
+ 用不同的方式同时判断多个值
```python
x, y, z = 0, 1, 0
if x == 1 or y == 1 or z == 1:
    print('passed')

if 1 in (x,y,z):
    print('passed')

# 下面的例子仅仅测试真值
if x or y or z:
    print('passed')
if any((x, y, z)):
    print('passed')
```
+ 如何根据字典值对字典进行排序
```python
xs = {'a':4, 'b':3, 'c':2, 'd':1}
sorted(xs.items(), key=lambda x: x[1]) #[('d', 1), ('c', 2), ('b', 3), ('a', 4)]

import operator
sorted(xs.items(), key=operator.itemgetter(1)) #[('d', 1), ('c', 2), ('b', 3), ('a', 4)]
```
+ `del`删除切片的结果
```python
a = [1, 2, 3]
del a[:]
# 属性引用、抽取和切片的删除会被传递给相应的原型对象,删除一个切片基本等价于赋值为一个右侧类型的空切片(但即便这一点也是由切片对象决定的)
# 在3.2版本发生变更: 在之前版本中,如果一个名称作为被嵌套代码块中的自由变量出现,则将其从局部命名空间中删除是非法的.
# del a[:] 相当于 a[:] = []
print(a) # []
```
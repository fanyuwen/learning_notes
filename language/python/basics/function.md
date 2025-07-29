### 指定参数
参数列表里`/`之前的参数必须按照位置索引去传递,`*`之后的参数必须按照名称去传递,`/`必须在`*`之前,这2个是可选的.
```python
# / 前面的 positional1 positional2 必须按照位置顺序传递,* 后面的 keyword1 keyword2 必须按照参数名传递,中间的2个按照位置或者名称传递都行
def methodparam(positional1, positional2, /, optionally1, optionally2, *, keyword1, keyword2):
    print(f"positional1 is {positional1}, positional2 is {positional2}, optionally1 is {optionally1}, optionally2 is {optionally2}, keyword1 is {keyword1} , keyword2 is {keyword2}.")
methodparam(1, 2, 3, 4, keyword1=5, keyword2=6)
```
考虑下面的函数定义,它在位置参数`name`和以`name`为键的`**kwds`之间存在潜在的冲突
```python
def foo(name, **kwds):
    return 'name' in kwds
# 没有可能的调用会使它返回True，因为关键字‘name’总是绑定到第一个参数。例如：
foo(1, **{'name': 2})
# Traceback (most recent call last):
#   File "<stdin>", line 1, in <module>
# TypeError: foo() got multiple values for argument 'name'

# 但是使用 / (位置参数)这是可能的,因为它允许`name`作为位置参数,'name'作为关键字参数的键,换句话说,仅位置参数的名称可以在 ** 参数中使用而不会产生歧义.
def foo(name, /, **kwds):
    return 'name' in kwds
foo(1, **{'name': 2}) # True
```
### bisect模块
> `bisect`是`Python`标准库中的一个模块,用于维护有序列表,它基于二分查找算法实现了高效的插入和查找操作.
```python
import bisect

data = [1, 3, 5, 7, 9]
# 在有序列表`data`中查找`x`的插入位置,返回的位置保证`x`插入后列表仍然有序,如果`x`已存在,则插入到最左边,`lo`和`hi`参数用于指定查找范围
bisect.bisect_left(data, x, lo=0, hi=len(a))
# 类似`bisect_left`,但如果 x 已存在,则插入到最右边
bisect.bisect_right(data, x, lo=0, hi=len(a))
bisect.bisect(data, x, lo=0, hi=len(a))
# 将`x`插入到有序列表`data`中,保持列表有序,如果`x`已存在,插入到最左边
bisect.insort_left(data, x, lo=0, hi=len(a))
# 类似`insort_left`,但如果`x`已存在,插入到最右边
bisect.insort_right(data, x, lo=0, hi=len(a))
bisect.insort(data, x, lo=0, hi=len(a))
```
### sorted
对可迭代对象进行排序
```python
l = [4, 2, 3, 1]
#按照自然排序规则进行排序
for e in sorted(l):
    print(e) # 1, 2, 3, 4
#可以指定排序规则,参数`key`传递一个函数对象,返回一个可比较值
for e in sorted(l, key=lambda x:-x):
    print(e) # 4, 3, 2, 1
```
### enumerate
`enumerate`函数,可以在遍历列表的时候同时能获取下标和元素
```python
l = [1, 2, 3]
for i, x in enumerate(l):
  print(i, x) #将打印 0,1 1,2 2,3

#指定初始计数
for i, x in enumerate(l, start=1):
  print(i, x) #将打印 1,1 2,2 3,3
```
### zip
`zip`函数,可以同时遍历2个或以上的可迭代对象,同时遍历相同位置的元素,如果到达任意一个迭代对象的末尾则结束
```python
l = [4, 5, 6]
a = (1, 2, 3)
for i, x in zip(l, a):
    print(i, x) #将打印 4,1 5,2 6,3

l = [4, 5, 6]
a = (1, 2)
for i, x in zip(l, a):
    print(i, x) #将打印 4,1 5,2
```
### reversed
```python
# 反转列表
l = [1, 2, 3, 4]
for e in reversed(l):
    print(e)  # 4,3,2,1
```
### 函数重载
```python
from functools import singledispatch

#使用`singledispatch`注解标注方法`process`
@singledispatch
def process(data):
    raise NotImplementedError("Unsupported type")
#再使用被标注的方法.`register`标注需要重载的方法
@process.register
def _(data: str):
    return f"Processing string {data}"
@process.register
def _(data: int):
    return f"Processing integer {data}"

process('123') #输出 Processing string 123
process(123) #输出 Processing integer 123
```

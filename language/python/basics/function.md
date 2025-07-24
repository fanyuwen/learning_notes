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

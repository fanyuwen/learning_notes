### collections
```python
# 命名元组
from collections import namedtuple
Card = namedtuple('Card', ['rand', 'suit'])
card = Card(rand="rand", suit="suit")
```

### itertools
+ `product`函数,可以组合多个可迭代对象,嵌套循环遍历,方法返回一个可迭代对象,可以去遍历获取结果
```python
from itertools import product

a = [1, 2, 3]
b = [4, 5, 6]
c = [7, 8, 9]

for a, b, c in product(a, b, c):
    print(a, b, c)

#等同于下面的写法
#for a_ in a:
#    for b_ in b:
#        for c_ in c:
#            print(a_, b_, c_)
```
+ `pairwise`函数,可以将一个可迭代对象的元素,相邻的2个元素进行组合,返回一个可迭代对象,
```python
from itertools import pairwise

l = [1, 2, 3, 4, 5]

for l, r in pairwise(l):
    print(l, r)

#等同于下面的写法
#for i in range(len(l) - 1):
#    l1 = l[i]
#    l2 = l[i + 1]
#    print(l1, l2)
```
+ `cycle`函数,将可迭代对象无限次的循环,返回一个可迭代对象
```python
from itertools import cycle

#遍历1~3之后,继续重新从1开始遍历,无休止
for n in cycle([1, 2, 3]):
    print(n)
```
+ `permutations`函数,第一个参数是一个可迭代对象,第2个参数指定组合的数量,结果是按照指定组合数量进行组合的所有可能数据结果(包括重复的,(m,n),(n,,m)都会返回),返回一个可迭代对象
```python
from itertools import permutations

#将输出2个元素组合的所有可能(包含重复)
for p in permutations([1, 2, 3, 4], 2):
    print(p)
```
+ `combinations`函数,第一个参数是一个可迭代对象,第2个参数指定组合的数量,结果是按照指定组合数量进行组合的所有可能数据结果(不包括重复的,(m,n),(n,,m)只会保留一个),返回一个可迭代对象
```python
from itertools import combinations

#将输出2个元素组合的所有可能(不包含重复)
for p in combinations([1,2,3,4], 2):
    print(p)
```

### timeit
```python
import timeit

def loop_list():
    lst = [i for i in range(1000)]
    for i in lst:
        pass

def list_comprehension():
    [i for i in range(1000)]

print("Loop List:", timeit.timeit(loop_list, number=1000))
print("List Comprehension:", timeit.timeit(list_comprehension, number=1000))
```

### cProfile
```python
import cProfile

def my_function():
    # 你的代码
    pass

#显示函数的调用次数和执行时间
cProfile.run('my_function()')
```

### random
```python
from random import choice
l = [1, 2, 3, 4]
print(choice(l))#随机选择一个元素
```
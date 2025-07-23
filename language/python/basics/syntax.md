# operate(运算符)

## arithmetic(算术运算符)



## assignment(赋值运算符) declaration(变量声明)

### 声明(赋值)变量

```python
# python声明变量,声明一个变量
s = 123
print(s)

# python声明多个变量
s1, s2 = 1, 2
# python赋值(交换2个变量的值)
s2, s1 = s1, s2
```

### 解构赋值
```python
#声明一个列表
list = [1, 2, 3]
#可以直接将它的元素解构赋值给多个变量
l1, l2, l3 = list
#也可用用 _ 来替代不使用的变量
l11, _ , _ = list
#也可以在变量名前面加上一个*,表示剩下的元素赋值给一个列表
li1, *li_last = list
#可以放在中间,但只能出现一次,也可以用 _
li_first_one, *limid, _, li_last_one = list
#解构的原理是python提供的高阶函数实现的
```

### 循环

+ 列表推导式
```python
l = [1, 2, 3, 4]

# 简单的建立一个新的列表,元素是原来列表每个元素的平方
s = [n ** 2 for n in l]

# 可以增加过滤
s = [n ** 2 for n in l if n % 2 == 0]

# 也可以嵌套循环
s = [n ** 2 + n1 for n in l if n % 2 == 0 for n1 in l]
```
+ `enumerate`函数,可以在遍历列表的时候同时能获取下标和元素
```python
l = [1, 2, 3]
for i, x in enumerate(l):
  print(i, x) #将打印 0,1 1,2 2,3
```
+ `zip`函数,可以同时遍历2个或以上的可迭代对象,同时遍历相同位置的元素,如果到达任意一个迭代对象的末尾则结束
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
+ `itertools.product`函数,可以组合多个可迭代对象,嵌套循环遍历,方法返回一个可迭代对象,可以去遍历获取结果
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
+ `itertools.pairwise`函数,可以将一个可迭代对象的元素,相邻的2个元素进行组合,返回一个可迭代对象,
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
+ `itertools.cycle`函数,将可迭代对象无限次的循环,返回一个可迭代对象
```python
from itertools import cycle

#遍历1~3之后,继续重新从1开始遍历,无休止
for n in cycle([1, 2, 3]):
    print(n)
```
+ `itertools.permutations`函数,第一个参数是一个可迭代对象,第2个参数指定组合的数量,结果是按照指定组合数量进行组合的所有可能数据结果(包括重复的,(m,n),(n,,m)都会返回),返回一个可迭代对象
```python
from itertools import permutations

#将输出2个元素组合的所有可能(包含重复)
for p in permutations([1, 2, 3, 4], 2):
    print(p)
```
+ `itertools.combinations`函数,第一个参数是一个可迭代对象,第2个参数指定组合的数量,结果是按照指定组合数量进行组合的所有可能数据结果(不包括重复的,(m,n),(n,,m)只会保留一个),返回一个可迭代对象
```python
from itertools import combinations

#将输出2个元素组合的所有可能(不包含重复)
for p in combinations([1,2,3,4], 2):
    print(p)

```

# operate(运算符)

## arithmetic(算术运算符)



## assignment(赋值运算符) declaration(变量声明)

### 声明(赋值)变量

```python
# python声明变量,声明一个变量(是一个语句)
s = 123
print(s)

# python声明多个变量
s1, s2 = 1, 2
# python赋值(交换2个变量的值)
s2, s1 = s1, s2

# 海象运算符
a := 1 #该复制是一个表达式,可以用在其它表达式中
# 传统写法
lines = []
while True:
    line = input()
    if not line:
        break
    lines.append(line)

# 使用海象运算符
lines = []
while (line := input()):
    lines.append(line)
```

### 索引

`[]` 索引从0开始 但也支持负数索引-1代表最后一个,-2代表倒数第二个,以此类推
超出索引范围的值都会报`IndexError`

### 切片

针对字符串、列表、元组等迭代对象的操作,切片不会修改原对象,而是会返回一个新对象`[start:stop:step]`
+ `start`:起始索引(包含),默认为0
+ `stop`:结束索引(不包含),默认为可迭代对象长度
+ `step`:步长,默认为1
```python
nums = [1,2,3,4]

print(nums[1:3]) #[2, 3]
print(nums[:3]) #[1, 2, 3]
print(nums[1:]) #[2, 3, 4]
print(nums[::2]) #[1, 3]
# 负数切片则从尾部向前切片
print(nums[-3:-1]) #[2, 3]
# 反转
print(nums[::-1]) #[4, 3, 2, 1]
print(nums[:]) #复制整个序列
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



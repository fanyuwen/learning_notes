# operate(运算符)

## arithmetic(算术运算符)



## assignment(赋值运算符) declaration(变量声明)

### 声明(赋值)变量

```pycon
# python声明变量,声明一个变量
s = 123
print(s)

# python声明多个变量
s1, s2 = 1, 2
# python赋值(交换2个变量的值)
s2, s1 = s1, s2
```

### 解构赋值
```pycon
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

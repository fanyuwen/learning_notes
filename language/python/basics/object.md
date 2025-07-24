### 类定义

+ 指定属性名
```python
class A:
    #通过赋值__slots__属性(列表、元组)指定属性名
    __slots__ = ('name', 'age')
    def __init__(self, name, age):
        self.name = name
        self.age = age
a = A()
#运行时这行将会报错,`AttributeError`
a.ssss = 12
```


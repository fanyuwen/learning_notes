### 类定义
+ dataclass @3.7
`dataclass`作为标准库中的一个模块,从`Python 3.7`开始引入.随着`Python`版本的不断更新,`dataclass`逐渐发展和完善,为开发者提供了一种更便捷的方式来创建和管理数据类,`dataclass`的主要作用是简化定义数据类的过程.
```python
class CoinTrans:
    def __init__(
        self,
        id: str,
        symbol: str,
        price: float,
        is_success: bool,
        addrs: list,
    ) -> None:
        self.id = id
        self.symbol = symbol
        self.price = price
        self.addrs = addrs
        self.is_success = is_success
#传统定义类的方式是通过 __init__ 函数初始化对象的各种属性
coin_trans = CoinTrans("id01", "BTC/USDT", 71000, True, ["0x1111", "0x2222"])
#这里我们只打印出了对象的地址，而不是我们期望的每个对象属性的值
print(coin_trans) # <__main__.CoinTrans object at 0x0000022A891FADD0>
#在传统类中，如果我们希望打印出可读的结果，需要自己实现 __str__ 函数(或者__repr__函数)
# 在上面的 CoinTrans 类中添加以下方法
def __str__(self) -> str:
    return f"Transaction Information：{self.id}, {self.symbol}, {self.price}, {self.addrs}, {self.is_success}"
#再次运行,结果: Trade information: id01, BTC/USDT, 71000, ['0x1111', '0x2222'], True
```
```python
#使用 dataclass 装饰器定义相同类
from dataclasses import dataclass, field, astuple, asdict

@dataclass
class CoinTrans:
    id: str
    symbol: str
    price: float
    is_success: bool
    addrs: list
#构造对象
coin_trans = CoinTrans("id01", "BTC/USDT", 71000, True, ["0x1111", "0x2222"])
print(coin_trans) # 输出 CoinTrans(id='id01', symbol='BTC/USDT', price=71000, is_success=True, addrs=['0x1111', '0x2222'])

#`dataclass`可以设置一些参数, `frozen`表示是否冻结对象(不可变,一旦创建不能修改),

def gen_list():
    return ["0x1111", "0x2222"]

#设置默认值
@dataclass
class CoinTrans1:
    id: str = "id01"
    symbol: str = "BTC/USDT"
    price: float = 71000.8
    is_success: bool = True # 可以直接初始值
    not_success: bool = field(default=True) # 也可以使用工厂方法
    addrs: list[str] = ["0x1111", "0x2222"] # 对于可变对象属性,不能直接使用可变对象去赋值, 需要额外定义一个工厂函数,然后进行赋值
    addrs_new: list[str] = field(default_factory=gen_list) # 这边采用属性工厂方法进行初始化

# field()方法里可以指定各种参数, `default`表示默认值, `repr`表示打印对象时是否打印该属性

# 与元组和字典的转换
coinTrans = CoinTrans1()
print(astuple(coinTrans)) # 转换为元组
print(asdict(coinTrans)) # 转换为字典

```

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


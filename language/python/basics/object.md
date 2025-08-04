### 类定义
+ dataclass @3.7
> `dataclass`作为标准库中的一个模块,从`Python 3.7`开始引入.随着`Python`版本的不断更新,`dataclass`逐渐发展和完善,为开发者提供了一种更便捷的方式来创建和管理数据类,`dataclass`的主要作用是简化定义数据类的过程.
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
### 类型系统

#### 泛型
+ 泛型函数
```python
# 导入TypeVar类型
from typing import TypeVar, List
# 创建一个泛型类型
T = TypeVar('T')  # 声明类型变量
# 定义一个接受元素类型为T的List,返回T类型,支持所有类型
def first_element(items: List[T]) -> T:
    return items[0]

# 使用
numbers: List[int] = [1, 2, 3]
print(first_element(numbers))  # 正确，返回int

strings: List[str] = ["a", "b", "c"]
print(first_element(strings))  # 正确，返回str
```
+ 泛型类
```python
# 导入泛型类型和泛型变量
from typing import Generic, TypeVar
# 创建泛型类型
T = TypeVar('T')
# 继承泛型为T的Generic类型,T在实际可以为任何类型
class Box(Generic[T]):
    def __init__(self, content: T):
        self.content = content
    
    def get_content(self) -> T:
        return self.content
# python3.9及以上简洁了语法
class Box1[X]:
    def __init__(self, content: X):
        self.content = content
    
    def get_content(self) -> X:
        return self.content

# 使用
int_box: Box[int] = Box(123)
str_box: Box[str] = Box("hello")
```
+ 泛型约束
```python
from typing import TypeVar
# 限定类型
# 限制T只能是int或str
T = TypeVar('T', int, str)

def double(value: T) -> T:
    return value * 2

print(double(3))  # 6
print(double("x"))  # "xx"
# double([1])  # 类型检查器会报错

# 限定类型上界
from numbers import Number
N = TypeVar('N', bound=Number)  # 必须是Number的子类

def add(a: N, b: N) -> N:
    return a + b

print(add(3, 5))  # 8
print(add(3.5, 2.1))  # 5.6
# add("a", "b")  # 类型检查器会报错

# 逆变和协变
# 协变 (covariant)
T_co = TypeVar('T_co', covariant=True)
# 逆变 (contravariant)
T_contra = TypeVar('T_contra', contravariant=True)
```

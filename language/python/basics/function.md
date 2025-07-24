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

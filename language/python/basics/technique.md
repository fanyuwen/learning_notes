+ 如何快速合并2个字典 @3.5+
```python
# 相同的`key`按照右边覆盖左边,先后顺序
x = {'a':1, 'b':2}
y = {'b':3, 'c':4}
z = {**x, **y}
print(z) # {'c':4, 'a':1, 'b':3} 或 {'a': 1, 'b': 3, 'c': 4}
```
+ 用不同的方式同时判断多个值
```python
x, y, z = 0, 1, 0
if x == 1 or y == 1 or z == 1:
    print('passed')

if 1 in (x,y,z):
    print('passed')

# 下面的例子仅仅测试真值
if x or y or z:
    print('passed')
if any((x, y, z)):
    print('passed')
```

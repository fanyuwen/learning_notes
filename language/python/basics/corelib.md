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
#显示函数的调用次数和执行时间
cProfile.run('my_function()')
```

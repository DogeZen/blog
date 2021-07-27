---
title : python的一些简化写法
date: 2021-07-15 08:00:00
tags :
- python
---

### 1.if 

```python
x=1 if y>0 else -1
```

### 2.生成式

list

```python
[x*2 for x in range(100)]
```

dict

```python
dic1 = {'1':'one','2':'two','3':'three'} 
dic2 = {v:k for k,v in dic1.items()}
```

### 3.赋值

```python
# 多个元素同时赋值
a,b = 1,2
# 序列挨个赋值
list = [1,2,3,4]
a,b,c,d =list 
```

### 4.map reduce

map将传入的函数依次作用到序列的每个元素。

reduce把结果继续和序列的下一个元素做累积计算。

```python
def f(x):
     return x * x
r = map(f, [1, 2, 3, 4, 5, 6, 7, 8, 9])
```

### 5.匿名函数

配合map reduce非常好用。

```python
map(lambda x: x * x, [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

```python
from functools import reduce

DIGITS = {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9': 9}

def char2num(s):
    return DIGITS[s]

def str2int(s):
    return reduce(lambda x, y: x * 10 + y, map(char2num, s))
```

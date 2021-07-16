---
title : python的一些简化写法
tags :
- python
---

1.if 

```python
x=1 if y>0 else -1
```

2.列表生成式

```python
[x*2 for x in range(100)]
```

3.赋值

```python
# 多个元素同时赋值
a,b = 1,2
# 序列挨个赋值
list = [1,2,3,4]
a,b,c,d =list 
```

4.map

```python
def f(x):
     return x * x
r = map(f, [1, 2, 3, 4, 5, 6, 7, 8, 9])
```

5.匿名函数

```python
map(lambda x: x * x, [1, 2, 3, 4, 5, 6, 7, 8, 9]
```


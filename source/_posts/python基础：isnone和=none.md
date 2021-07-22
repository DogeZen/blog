---
title : python基础:is None和==None的区别
date: 2021-06-15 07:00:00
tags :
- python
---



is表示的是对象标识符，用来**检查对象的标识符是否一致**，即两个对象在内存中的地址是否一致。在使用 `a is b` 的时候，相当于`id(a)==id(b)`。


==表示**两个对象是否相等**，相当于调用`__eq__()`方法，即’a==b’等价于`a.__eq__(b)`。

这两个方法很像，**但是不能完全随意替换用**

因为None在Python里是个单例对象，一个变量如果是None，它一定和None指向同一个内存地址。

is None是判断两个对象在内存中的地址是否一致，== None背后调用的是**eq**，而**eq**可以被重载

比如这里如果用==None，就会报错

```
ValueError: The truth value of an array with more than one element is ambiguous. Use a.any() or a.all()
```

因为这里numpy的==重载了
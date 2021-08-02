---
title: python特性整理
date: 2021-08-02 17:48:12
tags:
- python
---
### 1.装饰器：希望添加函数功能但是不想直接重写函数。
基础示例

在原函数前后写你需要添加的功能

```python
#接收需要装饰的函数，返回装饰完毕后的函数
def decorator(fuction):
    def fucution_after_decorate():
        print("1")
        
        fuction()
        
        print("1")
    return fucution_after_decorate
 
 
@decorator
def func():
    print("func")
 
func()
```

```
1
func
1
```



进阶示例:

计时函数
问题: 在flask中需要计算每个接口处理请求的耗时，并且写入log文件。如果每个接口都写一遍，项目代码会很冗长。

```python
def timer (fuction):
	def fucution_after_decorate():
        # 获取时间
		start_time = time.time()
		time_list,result,img_url = fuction()
		down_time,infer_time,post_time = time_list
		# 只保存错误url
		if result['code'] =='200':
			img_url =""
        # log
		app.logger.info(fuction.__name__
		+ str(time.strftime("%Y-%m-%d %H:%M:%S", time.localtime()))
		+" time:"+str(round(time.time()-start_time,4))
        +" "+result['code']+" "
		+" pictime:"+str(round(infer_time-down_time,4))
		+img_url)
		return result
	return fucution_after_decorate

# 请求函数
@app.route("/handleHkData", methods=['POST'])
def handleHkData_app():
    # 这里内部新建一个函数
    # 因为flask的route直接加装饰器,会因为传入装饰器名称重名报错。
	@timer
	def handleHkData():
		img_url = request.json.get("img_url").strip()  # url
		time_list,result = handleHkData(img_url)
		return time_list,result,img_url
	return handleHkData()
```

log

```shell
handlehkData2021-08-02 17:19:19 time:1.7569 200  pictime:1.5439
handlehkData2021-08-02 17:19:25 time:0.399 500002  pictime:0https://open.ys7.com
handlehkData2021-08-02 17:19:31 time:2.854 200  pictime:2.8145
workClothes_hf2021-08-02 17:19:39 time:1.3209 200  pictime:1.2829
workClothes_jf2021-08-02 17:19:45 time:0.9909 200  pictime:0.9575
```



### 2.call方法
把对象可以像函数一样被调用。
http://doge.ac.cn/?p=1331

### 3.直接赋值 浅拷贝 深拷贝
三种赋值方式

|                            |               直接赋值               |                       浅拷贝                       |      深拷贝      |
| :------------------------: | :----------------------------------: | :------------------------------------------------: | :--------------: |
|          使用方式          |                 a=b                  |                     a=b.copy()                     |  a=b.deepcopy()  |
|      原理，以list为例      | list内部和list本身都只是原对象的引用 | 只隔离了list本身，内部元素还是原来list中元素的引用 | 由内到外完全隔离 |
|         原对象变化         |           拷贝对象一起变化           |                  拷贝对象一起变化                  |  拷贝对象不变化  |
|    拷贝对象list整体变化    |              原对象变化              |                    原对象不变化                    |    原对象不变    |
| 拷贝对象list内某个元素变化 |           原对象内元素变化           |                  原对象内元素变化                  | 原对象内元素不变 |

1.直接赋值：list内部和list本身都只是原对象的引用

2.浅拷贝:只隔离了list本身，内部元素还是原来list中元素的引用

3.深拷贝:list和内部元素都完全隔离

### 4.迭代器 生成器
训练时加载数据集必备，节约内存，而不是一次性加载所有数据。

1.迭代器

对可迭代对象用**iter()**产生迭代器,用**next()**读取。

如

```python
x = [1,2,3]
y = iter(x)
z = iter(x)
print(next(y),next(y),next(y))
print(next(z),next(z),next(z))
```

结果为

```
1 2 3
1 2 3
```

可以看到，两个迭代器之间是相互独立的

由此可见，迭代器每次被创建的时候都会把内容都写入内存，从而可以被多次迭代

2.生成器

生成器也是一种特殊的迭代器

生成器是一种只能迭代一次的迭代器，生成器不会一次将所有的元素存入内存中，而是一边迭代一边运算

```python
def pictures_generator():
    for i in range(0,10000000000000000):
        yield pow(i,10)
generator=pictures_generator()
for i in range(0,10):
    print(next(generator))
```

这里不会一次生成包含1000000000000个元素的list用于迭代。

当你调用一个包含**yield**的函数的时候，函数体代码并不会执行，这个函数仅仅是返回一个生成器而已。

而这个生成器在你每次对他使用next函数时，函数体才会从执行到yield处，然后返回yield的值，然后暂停等待你的下次调用。

从而节约了大量的内存。

### 5.定制类
改写例如str(),len()对类的效果。
https://www.liaoxuefeng.com/wiki/1016959663602400/1017590712115904
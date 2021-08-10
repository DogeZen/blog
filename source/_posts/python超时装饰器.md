---
title: python超时装饰器
date: 2021-08-10 11:10:43
tags:
- python
---

#### 安装:

```pip3 install timeout-decorator``` 

#### 使用

默认情况下，timeout_decorator运用signal机制。
不适用于非主线程运行，例如web应用的worker线程。

这种情况下需要使用多进程，timeout decorator设置use_signals=False。

#### 例:设置url下载图片8秒超时

```python
import timeout_decorator

@timeout_decorator.timeout(8, use_signals=False)
def url_to_image(url):
    resp = urllib.request.urlopen(url)
    image = np.asarray(bytearray(resp.read()), dtype="uint8")
    image = cv2.imdecode(image, cv2.IMREAD_COLOR)
    return image
```



原文：https://www.jianshu.com/p/a7fc98c7af4d

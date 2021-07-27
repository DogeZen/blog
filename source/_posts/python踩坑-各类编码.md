---
title: python踩坑-各类编码问题
date: 2021-07-27 16:25:22
tags:
- python
- 踩坑
---

### 1.python json存储时乱码

```python
json_data = json.dumps(data_dict, indent=0,ensure_ascii=False)
with open(path_to_save + '/Result/' + path_to_save + '.json', 'w',encoding="utf-8") as f:
    f.write(json_data)
```

json.dumps默认用ascii码存储，,ensure_ascii=False后才能正常转。

另外就是文件打开时需要编码为utf-8

### 2.python url 解析特殊字符报错

url带空格，+, = , ?等特殊字符导致urllib.request.urlopen(url)解析失败。

这里不是python包的问题，是本身传过来的url就不应该带这类字符。

开发中出现这个问题，需要先去跟同事沟通，确定问题来源。

[具体解释的博客](https://blog.csdn.net/dyyshb/article/details/82346699)

#### 实在不行也可以python解决

```python
url=url.replace(" ", "%20").replace("+", "%20").replace("/", " %2F") \
    .replace("#", "%23").replace("&", "%26").replace("=", "%3D")
```


### 3.opencv puttext 中文乱码

#### 解决方法

先转成PIL的图片格式，再用PIL绘制字体，然后再转回opencv图片格式

#### 定义方法

```python
from PIL import Image, ImageDraw, ImageFont
def cv2ImgAddText(img, text, left, top, textColor=(0, 255, 0), textSize=20):
    if (isinstance(img, np.ndarray)):  # 判断是否OpenCV图片类型
        img = Image.fromarray(cv2.cvtColor(img, cv2.COLOR_BGR2RGB))
    # 创建一个可以在给定图像上绘图的对象
    draw = ImageDraw.Draw(img)
    # 字体的格式
    fontStyle = ImageFont.truetype(
        "simsun.ttc", textSize, encoding="utf-8")
    # 绘制文本
    draw.text((left, top), text, textColor, font=fontStyle)
    # 转换回OpenCV格式
    return cv2.cvtColor(np.asarray(img), cv2.COLOR_RGB2BGR)
```

其中simsun.ttc需要自己下载到当前目录下，这个是新宋体的字体。

#### 调用

```python
im0 = cv2ImgAddText(im0, f"有效数量:{num}\n均值    :{average_length:.2f}cm\n标准差  :{std_length:.2f}cm", 0, 10, (255, 255, 255), 150)
```


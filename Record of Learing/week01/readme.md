# 🐸PythonWebDM-Week02笔记

---

## 📖本周主要内容

### 🐛1.requests-html模块

requests-html模块是类似requests模块的请求模块，在请求的基础上可以通过html标签(xpath)进行选择爬取想要的内容，可以带ua访问。

此模块可以让我们更方便的解析html页面，使得爬虫更加简单易懂。

使用方法详见[官方文档](https://requests-html.kennethreitz.org/)。

<b>[中文文档](https://cncert.github.io/requests-html-doc-cn/#/)</b>

### 🤯来玩玩模块

👍试试看模块的强大

#### 通过css来定位

``` 

from requests_html import HTMLSession

session = HTMLSession()

r = session.get("https://news.cnblogs.com/n/recommend")

# 通过CSS找到新闻标签
news = r.html.find('h2.news_entry > a')

for new in news:
    print(new.text)  # 获得新闻标题
    print(new.absolute_links)  # 获得新闻链接

 ```

输出结果为：

```
《动物森友会》刷爆朋友圈的背后 是逃离时代的我们
{'https://news.cnblogs.com/n/658292/'}
一群日本小学生，在《我的世界》里搞了场“云”毕业
{'https://news.cnblogs.com/n/658276/'}
这款超硬核的游戏，蕴藏着微软实现复兴的巨大野心
{'https://news.cnblogs.com/n/658219/'}
Google 开源 Pigweed，涉足嵌入式开发
{'https://news.cnblogs.com/n/658198/'}
帮罗永浩算笔账：创业这些年，到底挣了多少钱？
{'https://news.cnblogs.com/n/658176/'}

......
......

```

#### 通过Xpath定位


```
from requests_html import HTMLSession

session = HTMLSession()

r = session.get("https://www.liepin.com/zhaopin/?key=web+mining")

# 通过xpath找到工作标签
news = r.html.xpath('//div[@class="job-info"]/h3/a')
for new in news:
    print(new.text)  # 获得工作标题
    print(new.absolute_links)  # 获得工作链接

```

输出结果为： 

```
web mining Engineer
{'https://www.liepin.com/job/1917951351.shtml'}
( Senior） Research Manager
{'https://www.liepin.com/job/1923198421.shtml'}
(Associate）research Director
{'https://www.liepin.com/job/1920754473.shtml'}
高级数据分析员(信息系统)Sr. Data Analyst(IS)
{'https://www.liepin.com/job/1927198767.shtml'}
SAP ApplicationDevelopment Project Lead
{'https://www.liepin.com/job/1926638333.shtml'}
Machine Learning Deployment Engineer
......
......
```

模块功能很多，第二周中的笔记有更多的叙述。更多的使用方式还要看[官方中文文档](https://cncert.github.io/requests-html-doc-cn/#/)。

### 😍抓图！

原理就是用请求，确定到路径，然后就来了。直接上代码了~

```
from requests_html import HTMLSession

session = HTMLSession()

r = session.get("https://cn.bing.com/images/trending")

# 通过xpath找到工作标签
items = r.html.xpath('//img/@src')

for url in items:
#     print(url)  # 获得图片src url
    display(Markdown('![]({url})'.format(url=url)))  # 展示图片    
```

上面的代码爬了图出来，一般来说编辑器是不会给我们预览的，但是我们jupyter有黑魔法！⬇️


### 🧙‍♂️jupyter 黑魔法

爬东西会了，爬下来还要打开文件件来找，太麻烦了吧？

在jupyter 中就直接就能看爬的图！

看看jupyter的黑魔法！

```
# html图
from IPython.core.display import display, HTML
display(HTML('<img src="https://httpstatusdogs.com/img/418.jpg" alt="">'))

# markdown图
from IPython.core.display import display, Markdown
display(Markdown('![](https://httpstatusdogs.com/img/418.jpg)'))

## 注意看display后的格式

```

### 🔍下载图

一般来说，我们爬虫，用黑魔法看看预览就好了，确定是想要的内容后，我们直接下载所有，怎么搞？

```
!mkdir img   #先搞个文件夹


import requests
import shutil

for imgfilename in list_img_src:
    path = urllib.parse.urljoin( url_base, imgfilename)
    
    resp = requests.get(path, stream=True)
    if r.status_code == 200:
        with open(imgfilename, mode="wb") as f:    # mode = write binary
            resp.raw.decode_content = True
            shutil.copyfileobj(resp.raw, f) 
    del resp
            
```

### 👨‍🚀爬豆瓣

豆瓣有反爬机制，为了不让他觉得我们是爬虫，所以我们得伪装一下。带上我们的身份去访问。

通常我们会在请求头（headers）中带上UA之类的信息（有些要登录的还要带上cookie）去访问，这样他就不知道我们是爬虫了，

```
import requests
from urllib import parse
_headers_ = {
        "Accept": "text/plain, */*; q=0.01", 
        "Connection": "keep-alive",
        "Host" : "movie.douban.com", 
        "User-Agent" : "Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3250.0 Iron Safari/537.36",
}
s = requests.Session()
u = URL_src['豆瓣电影排行榜']
r = s.get(u, headers=_headers_)


```

搞定。

---


#### 🔧附加篇

课上有讲去爬官网的带“文学与传媒学院”的所有新闻，尝试了一下，问题倒不大。

在处理翻页时，观察url和headers发现，翻页是通过url的后缀改变来实现的，所以用了一个.format()函数去处理url。

```
from requests_html import HTMLSession
import pandas as pd
session = HTMLSession()
items=[]
times=[]

for i in range(1,8):
    url="http://www.nfu.edu.cn/index.php/home/article/search/keyword/%E6%96%87%E5%AD%A6%E4%B8%8E%E4%BC%A0%E5%AA%92%E5%AD%A6%E9%99%A2/p/{}.html".format(i)

    r = session.get(url)

    item = r.html.xpath('//li/div/a/@title')
    time = r.html.xpath('//li/font/text()')
#     print(item) 

#获取所有标题以及时间
    for a in item:
        items.append(a)
    for a in time:
        times.append(a)
# print(items)
# print(times)
    df = pd.DataFrame(
        {
            "标题":items,
            "时间":times,
        })
df

```

---

[📦代码运行源码](https://github.com/Autumnhui/Learn_PythonWebDM/blob/master/Record%20of%20Learing/week01/week01.ipynb)















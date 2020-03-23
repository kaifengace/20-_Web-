Python上有一个非常著名的HTTP库——[requests](http://cn.python-requests.org/zh_CN/latest/)，相比大家都听说过，用过的人都说好！现在requests库的作者又发布了一个新库，叫做[requests-html](https://html.python-requests.org/)，看名字也能猜出来，这是一个解析HTML的库，而且用起来和requests一样爽，下面就来介绍一下它。

## 安装

安装requests-html非常简单，一行命令即可做到。需要注意一点就是，requests-html只支持Python 3.6及更新的版本，所以使用老版本的Python的同学需要更新一下Python版本了。看了下源代码，因为requests-html广泛使用了一个Python 3.6中的新特性——类型注解。

```
pip install requests-html
```

## 基本使用

### 获取网页

requests-html和其他解析HTML库最大的不同点在于HTML解析库一般都是专用的，所以我们需要用另一个HTTP库先把网页下载下来，然后传给那些HTML解析库。而requests-html自带了这个功能，所以在爬取网页等方面非常方便。

下面的代码获取了糗事百科上面的文字段子页面，返回的对象r是`requests.Reponse`类型，更确切的说是继承自前者的`requests_html.HTMLResponse`类型。这里其实和requests库的使用方法差不多，获取到的响应对象其实其实也没啥用，这里的关键就在于`r.html`这个属性，它会返回`requests_html.HTML`这个类型，它是整个`requests_html`库中最核心的一个类，负责对HTML进行解析。我们学习requests_html这个库，其实也就是学习这个HTML类的使用方法。

```
from requests_html import HTMLSession

session = HTMLSession()
r = session.get('https://www.qiushibaike.com/text/')
// 查看页面内容
print(r.html.html)
```

### 获取链接

`links`和`absolute_links`两个属性分别返回HTML对象所包含的所有链接和绝对链接（均不包含锚点）。

```
# 获取链接
print(r.html.links)
print(r.html.absolute_links)
```

结果为下（因为结果太长，所以我随便取了一点，看个意思就行）：

```
{'/article/104353012', '/article/120616112', '/users/32331196/'}
{'https://www.qiushibaike.com/imgrank/', 'https://www.qiushibaike.com/article/120669516', 'https://www.qiushibaike.com/article/120682041'}
```

### 获取元素

request-html支持CSS选择器和XPATH两种语法来选取HTML元素。首先先来看看CSS选择器语法，它需要使用HTML的find函数，该函数有5个参数，作用如下：
\- selector，要用的CSS选择器；  
\- clean，布尔值，如果为真会忽略HTML中style和script标签造成的影响（原文是sanitize，大概这么理解）;  
\- containing，如果设置该属性，会返回包含该属性文本的标签；  
\- first，布尔值，如果为真会返回第一个元素，否则会返回满足条件的元素列表；  
\- _encoding，编码格式。

下面是几个简单例子：

```
# 首页菜单文本
print(r.html.find('div#menu', first=True).text)
# 首页菜单元素
print(r.html.find('div#menu a'))
# 段子内容
print(list(map(lambda x: x.text, r.html.find('div.content span'))))
```

结果如下，因为段子太多，所以随便选了两个：

```
热门 24小时 热图 文字 穿越 糗图 新鲜
[<Element 'a' href='/' rel=('nofollow',)>, <Element 'a' href='/hot/'>, <Element 'a' href='/imgrank/'>, <Element 'a' id='highlight' href='/text/'>, <Element 'a' href='/history/'>, <Element 'a' href='/pic/'>, <Element 'a' href='/textnew/'>]
['有一次，几位大城市的朋友来家里玩，我招待他们吃风干羊肉做臊子的饸饹面，这是我们老家最具特色的美食！饭快熟的时候，老婆让我在园子里摘点“芫荽 ”，朋友问我，“芫荽”是什么东东？我给他们翻译解释说：我们本地土话叫“芫荽”，你们城里人讲普通话叫香菜，他们还大笑了一场。\n前天下雨没事儿干，翻看新华字典，突然发现“芫荽”才是香菜的学名，Tm香菜才是土话！而且我们地方方言就这两个字发音还特别标准！', '昨天晚上跟老婆吵架，他抓起我的手机就摔了。我立马摔了他的，结果我的还能用，他的坏了。高潮是人家立刻出门买了个新的！我艹，是不是中计了？？', '小姨要去高铁站，我看着大大小小的箱子说：坐公交车要转车，转来转去要一个多小时，太不方便了，不如我开车送你吧。\n小姨迟疑了一下，同意了。\n我准时把小姨送到了高铁站，正好赶上检票。\n小姨高兴地说：自己开车就是方便，不过幸好你妈聪明，让我们提前两个多小时就出发了！'
```

然后是XPATH语法，这需要另一个函数xpath的支持，它有4个参数如下：
\- selector，要用的XPATH选择器；  
\- clean，布尔值，如果为真会忽略HTML中style和script标签造成的影响（原文是sanitize，大概这么理解）;  
\- first，布尔值，如果为真会返回第一个元素，否则会返回满足条件的元素列表；  
\- _encoding，编码格式。  

还是上面的例子，不过这次使用XPATH语法：

```
print(r.html.xpath("//div[@id='menu']", first=True).text)
print(r.html.xpath("//div[@id='menu']/a"))
print(r.html.xpath("//div[@class='content']/span/text()"))
```

输出和上面那个几乎一样，之所以说是“几乎”，因为第三个输出会多出几个换行符，不知道什么原因。需要注意的一点是如果XPATH中包含`text()`或`@href`这样的子属性，那么结果相应的会变成简单的字符串类型，而不是HTML元素。

```
['\n\n\n我一份文件忘家里了，又懒得回家取，就给小姨子发短信息:   帮我把文件送来，晚上我谢谢你。等半天也没送来文件，我只好打个车回家自己拿，到家一进屋，我就发现气氛不对劲，老婆铁青着脸，两手掐着腰，小姨子站旁边对我怒目而视。']
```

### 元素内容

糗事百科首页LOGO的HTML代码如下所示：

```
<div class="logo" id="hd_logo">
<a href="/"><h1>糗事百科</h1></a>
</div>
```

我们来选取这个元素：

```
e = r.html.find("div#hd_logo", first=True)
```

要获取元素的文本内容，用text属性：

```
print(e.text)
# 糗事百科
```

要获取元素的attribute，用attr属性：

```
print(e.attrs)
# {'class': ('logo',), 'id': 'hd_logo'}
```

要获取元素的HTML代码，用html属性：

```python
print(e.html)
# <div class="logo" id="hd_logo">
# <a href="/"><h1>糗事百科</h1></a>
# </div>
```

要搜索元素的文本内容，用search函数，比如说我们现在想知道是糗事什么科：

```
print(e.search("糗事{}科")[0])
# 百
```

最后还有前面提到的两个链接属性：

```
print(e.absolute_links)
print(e.links)
# {'https://www.qiushibaike.com/'}
# {'/'}
```

## 进阶用法

这一部分我懒得找例子了，所以用官网上的例子。

### JavaScript支持

有些网站是使用JavaScript渲染的，这样的网站爬取到的结果只有一堆JS代码，这样的网站requests-html也可以处理，关键一步就是在HTML结果上调用一下render函数，它会在用户目录（默认是`~/.pyppeteer/`）中下载一个chromium，然后用它来执行JS代码。下载过程只在第一次执行，以后就可以直接使用chromium来执行了。唯一缺点就是chromium下载实在太太太太太太慢了，没有科学上网的同学可能无法使用该功能了。

```
>>> r = session.get('http://python-requests.org/')

>>> r.html.render()
[W:pyppeteer.chromium_downloader] start chromium download.
Download may take a few minutes.
[W:pyppeteer.chromium_downloader] chromium download done.
[W:pyppeteer.chromium_downloader] chromium extracted to: C:\Users\xxxx\.pyppeteer\local-chromium\571375
>>> r.html.search('Python 2 will retire in only {months} months!')['months']
'<time>25</time>'
```

render函数还有一些参数，顺便介绍一下（这些参数有的还有默认值，直接看源代码方法参数列表即可）：
\- retries: 加载页面失败的次数  
\- script: 页面上需要执行的JS脚本（可选）  
\- wait: 加载页面钱的等待时间（秒），防止超时（可选）  
\- scrolldown: 页面向下滚动的次数  
\- sleep: 在页面初次渲染之后的等待时间  
\- reload: 如果为假，那么页面不会从浏览器中加载，而是从内存中加载  
\- keep_page: 如果为真，允许你用`r.html.page`访问页面  

比如说简书的用户页面上用户的文章列表就是一个异步加载的例子，初始只显示最近几篇文章，如果想爬取所有文章，就需要使用scrolldown配合sleep参数模拟下滑页面，促使JS代码加载所有文章。

### 智能分页

有些网站会分页显示内容，例如reddit。

```
>>> r = session.get('https://reddit.com')
>>> for html in r.html:
...     print(html)
<HTML url='https://www.reddit.com/'>
<HTML url='https://www.reddit.com/?count=25&after=t3_81puu5'>
<HTML url='https://www.reddit.com/?count=50&after=t3_81nevg'>
<HTML url='https://www.reddit.com/?count=75&after=t3_81lqtp'>
<HTML url='https://www.reddit.com/?count=100&after=t3_81k1c8'>
<HTML url='https://www.reddit.com/?count=125&after=t3_81p438'>
<HTML url='https://www.reddit.com/?count=150&after=t3_81nrcd'>
…
```

这样的话，请求下一个网页就很容易了。

```
>>> r = session.get('https://reddit.com')
>>> r.html.next()
'https://www.reddit.com/?count=25&after=t3_81pm82'
```

### 直接使用HTML

前面介绍的都是通过网络请求HTML内容，其实requests-html当然可以直接使用，只需要直接构造HTML对象即可：

```
>>> from requests_html import HTML
>>> doc = """<a href='https://httpbin.org'>"""

>>> html = HTML(html=doc)
>>> html.links
{'https://httpbin.org'}
```

直接渲染JS代码也可以：

```
# 和上面一段代码接起来
>>> script = """
        () => {
            return {
                width: document.documentElement.clientWidth,
                height: document.documentElement.clientHeight,
                deviceScaleFactor: window.devicePixelRatio,
            }
        }
    """
>>> val = html.render(script=script, reload=False)

>>> print(val)
{'width': 800, 'height': 600, 'deviceScaleFactor': 1}

>>> print(html.html)
<html><head></head><body><a href="https://httpbin.org"></a></body></html>
```

### 自定义请求

前面都是简单的用GET方法获取请求，如果需要登录等比较复杂的过程，就不能用get方法了。`HTMLSession`类包含了丰富的方法，可以帮助我们完成需求。下面介绍一下这些方法。

#### 自定义用户代理

有些网站会使用UA来识别客户端类型，有时候需要伪造UA来实现某些操作。如果查看文档的话会发现`HTMLSession`上的很多请求方法都有一个额外的参数`**kwargs`，这个参数用来向底层的请求传递额外参数。我们先向网站发送一个请求，看看返回的网站信息。

```python
from pprint import pprint
r = session.get('http://httpbin.org/get')
pprint(json.loads(r.html.html))
```

返回的结果如下：

```
{'args': {},
 'headers': {'Accept': '*/*',
             'Accept-Encoding': 'gzip, deflate',
             'Connection': 'close',
             'Host': 'httpbin.org',
             'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_6) '
                           'AppleWebKit/603.3.8 (KHTML, like Gecko) '
                           'Version/10.1.2 Safari/603.3.8'},
 'origin': '110.18.237.233',
 'url': 'http://httpbin.org/get'}
```

可以看到UA是requests-html自带的UA，下面换一个UA：

```
ua = 'Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:62.0) Gecko/20100101 Firefox/62.0'
r = session.get('http://httpbin.org/get', headers={'user-agent': ua})
pprint(json.loads(r.html.html))
```

可以看到UA确实发生了变化：

```
{'args': {},
 'headers': {'Accept': '*/*',
             'Accept-Encoding': 'gzip, deflate',
             'Connection': 'close',
             'Host': 'httpbin.org',
             'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:62.0) '
                           'Gecko/20100101 Firefox/62.0'},
 'origin': '110.18.237.233',
 'url': 'http://httpbin.org/get'}
```

当然这里仅仅是换了一个UA，如果你有需要可以在header中修改其他参数。

#### 模拟表单登录

`HTMLSession`带了一整套的HTTP方法，包括get、post、delete等，对应HTTP中各个方法。比如下面我们就来模拟一下表单登录：

```
# 表单登录
r = session.post('http://httpbin.org/post', data={'username': 'yitian', 'passwd': 123456})
pprint(json.loads(r.html.html))
```

结果如下，可以看到forms中确实收到了提交的表单值：

```
{'args': {},
 'data': '',
 'files': {},
 'form': {'passwd': '123456', 'username': 'yitian'},
 'headers': {'Accept': '*/*',
             'Accept-Encoding': 'gzip, deflate',
             'Connection': 'close',
             'Content-Length': '29',
             'Content-Type': 'application/x-www-form-urlencoded',
             'Host': 'httpbin.org',
             'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_6) '
                           'AppleWebKit/603.3.8 (KHTML, like Gecko) '
                           'Version/10.1.2 Safari/603.3.8'},
 'json': None,
 'origin': '110.18.237.233',
 'url': 'http://httpbin.org/post'}
```

如果有上传文件的需要，做法也是类似的。如果了解过requests库的同学可能对这里的做法比较熟悉，没有错，这其实就是requests的用法。requests-html通过暴露`**kwargs`的方法，让我们可以对请求进行定制，将额外参数直接传递给底层的requests方法。所以如果有什么疑问的话，直接去看[requests文档](http://cn.python-requests.org/zh_CN/latest/)就好了。

## 爬虫例子

文章写完了感觉有点空洞，所以补充了几个小例子。不得不说requests-html用起来还是挺爽的，一些小爬虫例子用scrapy感觉有点大材小用，用requests和BeautifulSoup又感觉有点啰嗦，requests-html的出现正好弥补了这个空白。大家学习一下这个库，好处还是很多的。

### 爬取简书用户文章

简书用户页面的文章列表就是一个典型的异步加载例子，用requests-html的话可以轻松搞定，如下所示，仅仅5行代码。

```python
r = session.get('https://www.jianshu.com/u/7753478e1554')
r.html.render(scrolldown=50, sleep=.2)
titles = r.html.find('a.title')
for i, title in enumerate(titles):
    print(f'{i+1} [{title.text}](https://www.jianshu.com{title.attrs["href"]})')
```

当然这个例子还有所不足，就是通用性稍差，因为文章列表没有分页机制，需要一直往下拉页面，考虑到不同的用户文章数不同，需要先获取用户总文章数，然后在计算一下应该下滑页面多少次，这样才能取得较好的效果。这里仅仅简单获取一些我自己的文章，就不往复杂写了。

### 爬取天涯论坛

以前经常在天涯论坛上追一些帖子，现在正好写一个爬虫，把连载的好帖子一次性爬下来弄成一个文件。

```
# 爬取天涯论坛帖子
url = 'http://bbs.tianya.cn/post-culture-488321-1.shtml'
r = session.get(url)
# 楼主名字
author = r.html.find('div.atl-info span a', first=True).text
# 总页数
div = r.html.find('div.atl-pages', first=True)
links = div.find('a')
total_page = 1 if links == [] else int(links[-2].text)
# 标题
title = r.html.find('span.s_title span', first=True).text

with io.open(f'{title}.txt', 'x', encoding='utf-8') as f:
    for i in range(1, total_page + 1):
        s = url.rfind('-')
        r = session.get(url[:s + 1] + str(i) + '.shtml')
        # 从剩下的里面找楼主的帖子
        items = r.html.find(f'div.atl-item[_host={author}]')
        for item in items:
            content: str = item.find('div.bbs-content', first=True).text
            # 去掉回复
            if not content.startswith('@'):
                f.write(content + "\n")
```

爬完之后，看了一下，700多k的文件，效果不错。

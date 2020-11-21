![51job](home.png)

# 项目名称：“数据”方向就业招聘分析

## 项目背景

随着科技的飞速发展，数据呈现爆发式的增长，任何人都摆脱不了与数据打交道，社会对于“数据”方面的人才需求也在不断增大。因此了解当下企业究竟需要招聘什么样的人才？需要什么样的技能？不管是对于在校生，还是对于求职者来说，都显得很有必要。

本项目基于这个问题，针对  [**前程无忧51job招聘网站**](https://www.51job.com/)，爬取了**广州市**的大数据、数据分析、数据挖掘、机器学习、人工智能等相关岗位的招聘信息。分析了“数据”相关的就业需求信息，如岗位需求、行业需求、岗位薪资、行业薪资等。

## *PRD1*数据加值宣言

本项目产出自按“数据”为关键词，挖掘的关于 *[前程无忧51job](https://www.51job.com/)中各行业* 的工作数据（<b>原数据共5849条,清洗后2465条。详见[Excel表数据链接](https://gitee.com/autumnhui/Learn_PythonWebDM/blob/master/Prj_51job/%E6%B8%85%E6%B4%97%E5%90%8Ejob_info.xlsx)</b>），以解决（不一定准确，但具有参考价值）“数据”相关的就业需求及特性的就业分析问题。

* 关键词：数据
* 页数：所有（1-100）
* 类别数据：学历、经验、薪水、发布时间、岗位名、工作地点、公司名称、公司类型、公司规模、链结、所属行业、工作描述。
* 爬取岗位：大数据、数据分析、机器学习、人工智能等相关岗位
* 说明：基于51job招聘网站，我们搜索全国对于“数据”岗位的需求，取100页进行分析。我们爬取的字段，既有一级页面的相关信息，还有二级页面的部分信息
* 爬取思路：先针对某一页数据的一级页面做一个解析，然后再进行二级页面做一个解析，最后再进行翻页操作
* 使用工具：Python+requests+lxml+pandas+time
* 网站解析方式：[Xpath](https://www.runoob.com/xpath/xpath-tutorial.html)
* 具体执行请见：[主文件ipynb跳转](https://gitee.com/autumnhui/Learn_PythonWebDM/blob/master/Prj_51job/get_job.ipynb)

## *PRD2*数据加值

产品核心价值：通过分析各行业的数据，总结“数据”相关职业的基本要求，进而刻画“数据”相关职业的要求形象。

数据分析师的价值在于从数据中挖掘有效信息，精准反馈和有效决策业务指标和企业发展方向。

<b>依本产品总结：</b>

- 在数据相关的各行业中，**互联网行业**的需求是最大的，其次为**计算机软件行业**，**快速消费品(食品、饮料、化妆品)**虽居第三位，但是行业需求距第二位的差距甚大。（详见图表“广州市数据相关行业需求”）
- 在数据相关的个岗位中，**运营岗、数据分析岗**是占据相当大的优势;，反而时下热门常谈的“大数据”相关的岗位却差之甚远，岗位数量仅为**运营岗、数据分析岗**的3成左右。（详见图表“广州市数据相关岗位数量”）
- 在前面的基础上探究薪资分布，发现**数据中心总经理**的平均薪资是最高的，但行业普遍薪资的高水平在30~40k左右，岗位有数据中心营运总监、数据总监等高层职位。普通开发者（码农）的薪资在30k算是顶级水平了。（详见图表“各岗位薪资平均水平”）
- 数据相关的各行业薪资平均水平以**房地产**行业为首，平均薪资高达22k，后面的**租赁服务**行业及其后行业，平均薪资都在10k以上，要想工资高，还得往这些行业（房地产、租赁服务、石油、中介、网络游戏等）方面靠。（详见图表“各行业薪资平均水平“）

## Query参数

* keyword：数据
* url:搜索关键词“数据”生成的url
* job_name:岗位名称
* company_name：公司名称
* address：工作地点
* salary：工资
* release_time：发布时间
* deep_url：二级网站url
* random_all：经验+学历
* job_describe：岗位描述信息
* company_type：公司类型
* company_size：公司规模
* industry：公司所属行业

## 思路方法及具体执行

### 思路方法

1. 分析搜寻网页之URL
2. 数据爬取(含二级详细页内容爬取)
3. 数据存储
4. 数据清洗
5. 构造新表

### 执行

1. 使用相关的库
```
import requests
import pandas as pd
from pprint import pprint
from lxml import etree
import time
import warnings
warnings.filterwarnings("ignore")
```

2. 分析URL

```
# 第一页
https://search.51job.com/list/000000,000000,0000,00,9,99,%25E6%2595%25B0%25E6%258D%25AE,2,1.html?
# 第二页
https://search.51job.com/list/000000,000000,0000,00,9,99,%25E6%2595%25B0%25E6%258D%25AE,2,2.html?
# 第三页
https://search.51job.com/list/000000,000000,0000,00,9,99,%25E6%2595%25B0%25E6%258D%25AE,2,3.html?

# 发现变化的只有后面的数字，用for循环即可爬取所有的页面

```

3. .....爬取以及清洗数据的过程详见[ipynb文档](https://gitee.com/autumnhui/Learn_PythonWebDM/blob/master/Prj_51job/get_job.ipynb)


## 心得总结及感谢

### 心得总结

- 相较于[猎聘网站](https://www.liepin.com/)的爬取，[51Job前程无忧](https://www.51job.com/)的爬取工作似乎相对简单（url处理方面），但也是有不少坑
- 数据处理方面欠缺，对文字档数据的整理难度相对较大
- 尝试爬取二级页面的信息，以便获取更多的内容
- 好多工作需求都是要求六级，还没过，怎么办

### 感谢

> 感谢[CSDN](https://www.csdn.net/)提供解决问题的代码，感谢[Github开源](github.com/autumnhui)，感谢[51Job前程无忧](https://www.51job.com/)的数据（尽量慢慢爬了），感谢一学期来各位老师的辛勤教导。

---

* [主文件ipynb跳转](https://gitee.com/autumnhui/Learn_PythonWebDM/blob/master/Prj_51job/get_job.ipynb)

* [Excel表数据链接](https://gitee.com/autumnhui/Learn_PythonWebDM/blob/master/Prj_51job/%E6%B8%85%E6%B4%97%E5%90%8Ejob_info.xlsx)

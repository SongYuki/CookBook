# Scrapy

标签： Python

---

scrapy主要包括了以下组件：
引擎Scrapy:用来处理整个系统的数据流处理，触发事务（框架核心）
调度器Scheduler:用来接受引擎发过来的请求，压入队列中，并在引擎再次请求的时候返回，可以想象成一个URL（抓取网页的网址或者说是链接）的优先队列，由它来决定下一个要抓取的网址是什么，同时去除重复的网址

下载器（Downloader）:用于下载网页内容，并将网页内容返回给蜘蛛（Scrapy下载器是建立在twisted这个高效的异步模型上的）

爬虫（Spiders）:爬虫是主要干活的，用于从特定的网页中提取自己需要的信息，即所谓的实体（Item）用户也可以从中提取出链接，让Scrapy继续抓取下一个页面

项目管道（Pipeline）:负责处理爬虫从网页中提取的实体，主要的功能是持久化实体，验证实体的有效性，清除不必要的信息。当页面被爬虫解析后，将被发送到项目管道，并经过几个特定的次序处理数据

下载中间件（Downloader Middlewares）:位于Scrapy引擎和下载器之间的框架，主要是处理Scrapy引擎和下载器之间的请求及响应

爬虫中间件（Spider Middlewares）:介于Scrapy引擎和爬虫之间的框架，主要工作是处理蜘蛛的响应输入和请求输出

调度中间件（Scheduler Middlewares）:介于Scrapy引擎和调度之间的中间件，从Scrapy引擎发送到调度的请求和响应



    sudo pip install virtualenv
    //install virtualenv tool
    virtualenv ENV 
    //create a virtualenv catlog
    source ./ENV/bin/active
    //activate virtualenv
    pip install Scrapy 
    pip list
    //verify if the installation success

    $scrapy startproject tutorial
    

1.定义item
    通过创建scrapy.Item类，并且定义类型为scrapy.Field的类属性来声明一个Item 我们通过将需要的item模型化，来控制从dmoz.org获得的站点数据，比如我们要获得站点的名字，url和网站描述，我们定义着三种属性的域
    编辑项目目录下的items.py
    
    from scrapy.item import Item,Field
    class DmozItem(Item):
    #define the fields for your item here like:
    name = Field()
    description = Field()
    url = Field()
    
    
2.编写Spider
    Spider是用户编写的类，用于从一个域（域组）中抓取信息，定义了用于下载的url的初步列表，如何跟踪链接，以及如何来解析这些网页的内容用于提取items
    要建立一个Spider继承scrapy.Spider基类，并确定三个主要的，强制的属性：
 - name:爬虫的识别名，必须是唯一的，在不同的爬虫中你必须定义不同的名字
 - start_urls:包含了Spider在启动时进行爬取得url列表，因此，第一个被获取到的页面将是其中之一。后续的url则从初始的url获取到的数据中提取。我们可以利用正则表达式定义和过滤需要进行跟进的链接
 - parse():是spider的一个方法。被调用时，每个初始url完成下载后生成的response对象将会作为唯一的参数传递给该函数。该方法负责解析返回的数据（response data）提取数据（生成item）以及生成需要进一步处理的url的request对象
 这个方法负责解析返回的数据，匹配抓取的数据（解析为item）并跟踪更多的url
在/tutorial/tutorial/spiders目录下创建dmoz_spider.py

    import scrapy
    Class DmozSpider(scrapy,Spider)：
    name = "dmoz"
    allowed_domains = ["dmoz.org"]
    start_urls = [
    "http://www.dmoz.org/Computers/",
    "http://www.dmoz.org/Computers/Programming/Language"]
    
    def parse(self,response);
    filename = response.url.spilt("/")[-2]
    with open(filename,'wb')as f;
    f.write(response.body)

3.爬取

    scrapy crawl dmoz
    
4.提取items
4.1介绍selector
从网页中提取数据有很多方法，scrapy使用了一种基于xpath或者css表达式的机制：Scrapy Selectors

    XPath tutorial:
    /html/head/title:
    /html/head/title/text():
    //td
    //div[@class="mine"]:

Selector类：
xpath():返回selectors列表，每一个selector表示一个xpath参数表达式选择的节点
css():返回selectors列表，每一个selector表示css参数表达式选择的节点
extract():返回一个unicode字符串，该字符串为XPath选择器返回的数据
re():返回unicode字符串列表，字符串作为参数由正则表达式提取出来

4.2取出数据

    RPG role-playing game
    RTS real-time Strategy
    MOBA Multiplayer Online Battle Arena 
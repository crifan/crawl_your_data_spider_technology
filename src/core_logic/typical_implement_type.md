# 爬虫的典型实现方式

实现爬虫的方式有很多，根据复杂度，可以分成典型的3种方式：

* `裸写`爬虫代码
  * 解释：在自己了解`HTTP`、爬虫等相关背景知识的前提下，用相对比较原始的方式，用内置库实现爬虫全部功能
  * 优点：更贴近和了解底层技术
  * 缺点：要求熟悉底层技术，相对用已有的库，写起来比较复杂
* 用第三方`库`写爬虫代码
  * 解释：用第三方的、更强大、更好用，网络库下载内容，内容提取库提取数据
  * 优点：省心，高效
  * 缺点：
    * 要额外引入库，且要了解如何使用
    * 对于新手，往往是直接用了第三方库后，不了解内部机制
* 用`爬虫框架`
  * 用成熟的也更复杂和强大的爬虫框架，让框架帮你做重复工作，自己只需学核心的爬虫逻辑即可爬取到数据
  * 优点：适合更复杂的爬虫任务，充分利用框架的任务调度，url去重等等高级功能
  * 缺点：
    * 很多适合杀鸡用牛刀，比较重，不够轻量级
    * 出了问题，需要熟悉内部机制才容易解决问题

下面详细解释，对于爬虫的核心步骤中，不同实现方式的优缺点和所涉及内容：

* 下载
  * `裸写`爬虫代码
    * 举例
      * `Python`的`urllib`
      * `C#`的`HttpWebRequest` + `HttpWebResponse`
        * [crifanLib.cs之Http](https://www.crifan.com/files/doc/docbook/crifanlib_csharp/release/html/crifanlib_csharp.html#http)
  * 用第三方`库`写爬虫代码
    * 举例
      * `Python`
        * `requests`
* 提取
  * 提取数据的方式：
    * 从json中提取想要的内容
      * 用json库，把json字符串转换为json对象（dict，字典）即可
        * 无需（html）解析相关的库
      * 常见的库
        * `Python`
          * `json`
        * `C#`
          * `Newtonsoft.Json`
          * `JavaScriptSerializer`
    * 从html，js等内容中提取想要的内容
      * `裸写`爬虫代码
        * `正则`
          * 【整理Book】应用广泛的超强搜索：正则表达式
          * `Python`
            * `re`模块
              * [Python中的正则表达式：re模块详解](http://book.crifan.com/books/python_regular_expression_re_intro/website)
        * `XPath`
          * [XPath知识总结](http://book.crifan.com/books/xpath_summary/website)
      * 用第三方`库`写爬虫代码
        * `Python`
          * `lxml`
            * [【记录】Python中尝试用lxml去解析html – 在路上](https://www.crifan.com/python_try_lxml_parse_html/)
          * `BeautifulSoup`
            * 【整理Book】网页内容提取利器：BeautifulSoup
          * `PyQuery`
            * 【整理Book】Python心得：HTML解析 PyQuery
          * [python-goose](https://github.com/grangier/python-goose)
          * 等
        * `C#`
          * `HTML解析`
            * `HtmlAgilityPack`
            * `sgml`
        * `Java`
          * `Jsoup`
* 保存
  * `裸写`爬虫代码
    * 自己写代码保存到对应文件或数据库中
  * 用`库`写爬虫代码
    * 用库去将数据保存到文件或数据库中
  * 用`爬虫框架`
    * 框架内置接口
      * PySpider
        * 用内置接口，自动保存数据到对应数据库中

## 不同实现方式和爬虫不同步骤的对应关系

下面以`Python`语言为例，来解释不用爬虫的实现方式和不同步骤之间的对应关系：

|    | 下载（网页） | 提取（内容） | 保存（数据） |
| -- | ---------- | ---------- | ---------- |
| 自己裸写Python代码 |  `urllib` | `re` | `txt`/`csv` |
| 用各种Python库组合 |  `requests` | `BeautifulSoup`/`lxml` | `csv` / `pymysql` |
| 用框架`PySpider`| `requests` (`PySpider`的`self.crawl`) | (`PySpider`内置的)`PyQuery` | (`PySpider`内置)各种`数据库`(接口） |

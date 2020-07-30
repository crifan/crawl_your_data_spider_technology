# 如何写爬虫

由于不同语言对于爬虫的生态不同，导致选择不同语言去实现同样的爬虫目标，难度也差异很大。

类比来解释：

* 目的：爬虫
  * 就像 要建一个自己喜欢的房子
* 不同语言难度
  * Python
    * 已有：
      * 建房子：给你建好了房子，且还搞好了硬装，有了基本的家具等
      * 爬虫
        * 生态极其丰富和强大
          * 单独的库
            * 下载
              * 网络库
                * requests
            * 提取
              * 正则re
              * xpath
              * BeautifulSoup
              * PyQuery
              * 等
          * 爬虫框架
            * Scrapy
            * PySpider
            * 等
    * 需要你：
      * 建房子：简单弄弄软装，就可以得到你喜欢的房子
      * 爬虫
        * 只需要分析网页内容加载逻辑，搞清楚规则
        * 再去用Python代码实现这些规则和逻辑，即可
  * 其他语言
    * 已有
      * 建房子
        * 不仅没硬装，往往也没房子
          * 甚至连钢筋水泥和沙子等原材料都没有
      * 爬虫
        * 不同的语言，对于网络相关库、解析相关库等等，都有一定程度的支持
          * 但往往不够好，具体情况差异很大
        * 对于其他编程语言，用来开发爬虫的生态和便利性，个人总体感觉是：
          * `Go` > `C#` > `Java` > `PHP`
    * 需要你：
      * 建房子
        * 需要你从头做起去建房子
          * 自己开车采购钢筋水泥沙子等原材料
          * 然后找人开始建房子
        * 把房子建好后
          * 才能开始硬装
          * 最后软装
            * 做出你喜欢的房子
      * 爬虫
        * 很多时候都没有很好的轮子可用，导致爬虫方面开发效率很低
          * 往往要了解http网络库的机制，甚至自己裸写代码，才能实现http层面的网页源码的下载
          * 然后再去解析源码，期间甚至还要关注html字符编码的问题，才能提取出你要的内容

> #### info:: 旧教程
> 
> 之前写过一些旧教程，需要的也可以参考：
> 
> * [详解抓取网站，模拟登陆，抓取动态网页的原理和实现（Python，C#等）](https://www.crifan.com/files/doc/docbook/web_scrape_emulate_login/release/html/web_scrape_emulate_login.html)
> * [如何用Python，C#等语言去实现抓取静态网页+抓取动态网页+模拟登陆网站 – 在路上](https://www.crifan.com/how_to_use_some_language_python_csharp_to_implement_crawl_website_extract_dynamic_webpage_content_emulate_login_website/)
> 

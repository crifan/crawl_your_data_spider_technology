# 爬虫的叫法

爬虫有很多种常见的叫法，整理如下：

* `爬虫`
  * 常见英文说法：
    * `crawler`=爬取数据的工具
      * `crawl`英文原意：爬，爬行
    * `spider`=`蜘蛛`=像蜘蛛捕获昆虫一样你去捕获你要的数据
      * `spider`英文原意：`蜘蛛`
        * 为何把爬取数据的工具叫做**蜘蛛**，见后续的类比解释：[为何叫爬虫](http://book.crifan.com/books/crawl_your_data_spider_technology/website/spider_intro/why_called_spider/)
    * `scraper`=刮取到你想要的数据的工具
      * `scrape`英文原意：`刮取`
    * `grab`=抓取你要的数据的工具
      * `grab`英文原意：`攫取，夺取`
* `爬取数据`
  * 常见英文说法：
    * `crawl data`=`crawling data`
    * `scraping data`
    * `grabbing data`
* `爬取网站`=`爬取网页`
  * 常见英文说法：
    * `crawl website`
* `模拟登录`
  * 常见英文说法：
    * `emulate login`
      * `login emulation`
  * 为何（把爬虫）叫做模拟登录？
    * 简答：
      * 爬虫在需要用户登录的场景中应用时的叫法
    * 详解：
      * 对于一些情况：想要爬取很多网站上的数据，需要用户（使用账号和密码等方式）去登录后才能获取到
      * 所以要先去`模拟（用户）登录`，然后才能`爬取数据`
      * 而模拟登录的过程，有时候或者经常，比后续的爬取数据更难，更复杂
      * 所以此时的爬取全称是 `先要模拟用户登录后再去爬取数据`
      * 也就常简称为 `模拟登陆`
        * 用`模拟登陆`指代 `爬虫`


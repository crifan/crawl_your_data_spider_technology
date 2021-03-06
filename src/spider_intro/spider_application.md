# 爬虫应用

## 爬虫应用领域

爬虫的应用领域，从广义上来说，**人类用网络能做啥，爬虫就能干啥**。

对于爬虫的具体应用领域，借用和爬虫密切相关的`IP代理`技术的应用来说明：

* 定时营销策略
    * 游戏试玩
    * 游戏挂机
    * QQ营销
    * 宝贝收藏
    * 下载补量
      * 网购下单
        * 使用软件自动抢票、抢购手机，提高效率
    * 秒杀抢购
* 数据采集应用
    * 爬虫抓取
    * 页面采集
      * 编写爬虫采集网络公开数据，分析得出全面的市场分析报告
    * 投票点赞
      * 投票发帖
    * 网站注册
    * 效果回访
    * 网站搭建
* 优化推广方案
    * 竞价优化
    * 电商优化
    * 邮件群发
    * 打码投票
    * 论坛发帖
    * 问答推广

## 爬虫应用举例

### 自动登录=签到脚本

对于需要用户登录的网站或系统，如果用爬虫能够成功模拟登录的话，则往往就可以实现，其他一些人所需要的功能：

* 用来自动登录系统 -》 叫做 自动登录
  * 用途有很多
    * 比如
      * 每天用来签到 -》 爬虫签到 = 爬虫签到脚本
        * 比如
          * 有人弄过 百度贴吧的签到脚本
          * [自制BILIBILI弹幕爬取，签到，抢楼等爬虫](https://github.com/qq519043202/BILI)
          * 有人用来 [用爬虫在各大机场自动签到获取流量](https://lemea.co/index.php/archives/17/)
          * [每天理财网站登陆签到获取积分](https://www.jianshu.com/p/bfa23227f089)
          * [浦发信用卡自动签到](https://blog.dianqk.org/2019/02/20/entry-voice-union/)
        * 总之还是那句话
          * 可以用爬虫来干什么
            * 取决于你的`想象`：你想用来干什么
            * 和你自己的`技术水平`：你自己能不能搞定，能不能实现
      * 模拟用户发布内容
        * 就变成了
          * 自动发帖（脚本）
          * 自动回复（脚本）

### 模拟浏览器操作

既然爬虫可以爬取网页，那么理论上就支持，用来模拟用户去点击网页，去实现模拟用户的操作，用爬虫，也叫做自动化脚本，去模拟浏览器的各种操作。

而对于模拟浏览器操作方面，和爬虫关系很密切，不过又属于不同的领域。

`模拟浏览器`=`Web Browser Automation`，这个领域，又有很多不同的框架，工具，和技术。

比如：

* [Selenium](https://www.seleniumhq.org)
  * [Selenium知识总结](http://book.crifan.com/books/selenium_summary/website)
* `无头浏览器`=`Headless Browser`：
  * 含义：没有`头`的，没有`界面`的浏览器（内核）
    * -》 因为写代码控制和操作浏览器时，往往不需要（像普通用户用浏览器看网页时那样）看界面
      * -》 专门用于模拟浏览器行为，用于模拟浏览器，提供接口供你操作浏览器
  * 常见工具
    * [Phantomjs](http://phantomjs.org)


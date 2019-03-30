# 爬虫的核心步骤

接下来介绍爬虫的原理、过程和步骤，以及相关涉及到的知识。

从原理上来说，写爬虫去爬数据的过程，最核心的就这3步：

* `下载`=`download`
* `提取`=`extract`
* `保存`=`save`

下面详细解释每一步的各种细节：

* `下载`=`下载网页`
  * 做了什么：请求网址或api接口，去下载返回
  * 得到什么：html网页或json字符串
  * 涉及到
    * （尤其是新手需要学习）Http基本知识
      * `Request`
        * `Method`
          * `GET`
          * `POST`
          * 等
        * `Header`
          * `User-Agent`
          * `Content-Type`
          * `Accept`
          * `Authorization`
          * 等
        * `Cookie`
        * `Body`
          * `data`
            * `json`
      * `Response`
        * `Status Code`
        * `Header`
        * `Cookie`
        * `Body`
          * `data`
            * `json`
              * [主流数据格式：JSON](http://book.crifan.com/books/common_data_format_json/website)
      * 教程：[HTTP知识总结](http://book.crifan.com/books/http_summary/website)
    * 如果被爬方（`网站`，`app`等）
      * 需要用户登录后才能看到数据
        * 用技术绕过限制
          * `模拟登陆`
            * 先要抓包分析出登录逻辑
            * 再用代码模拟用户登录
      * 做了一些`反爬`措施
        * `验证码`
          * 用技术绕过限制
            * `验证码识别`
              * （用第三方）`打码平台`
        * `IP限制` + `抓取频率`限制
          * 用技术绕过限制
            * `IP代理池`
            * 设置抓取的`间隔时间`
        * `身份限制`
          * Http的Headers
            * `UA`=`User-Agent`
    * 被爬网站所含页面层级很多
      * `抓取策略`
        * `深度优先遍历策略`
        * `宽度优先遍历策略`
        * `反向链接数策略`
        * `Partial PageRank策略`
        * `OPIC策略策略`
        * `大站优先策略`
* `提取数据`
  * 做了什么：从（返回的）网页（的`html`，`js`等）或`json`中提取
  * 得到什么：自己需要的内容
  * 涉及到
    * `字符编码`的问题
      * 如果搞不清编码，就容易出现各种乱码问题
      * 需要学习相关`编码`知识
        * 【整理Book】字符编码详解与应用
          * 【整理Book】Python心得：字符串和字符编码
        * `html`的`meta charset`
        * `编码检测`
          * `Python`
            * `chardet`
    * 如果被爬方做了反爬
      * `数据加密`
        * 用技术绕过
          * 找到`解密`的逻辑和方法
            * 【整理Book】安卓应用的安全和破解
              * 【已解决】尝试破解小花生app安卓apk希望看到api返回的json中的J的解密算法得到明文
* `保存数据`
  * 做了什么：把数据保存到对应的地方
  * 得到什么：包含了我们要的特定格式的数据的文件或数据库
  * 保存成不同格式：
    * 文件
      * `txt`
      * `csv` / `excel`
        * [Python心得：操作CSV和Excel](http://book.crifan.com/books/python_experience_csv_excel/website)
    * 数据库
      * `mysql`
        * [主流关系数据库：MySQL](http://book.crifan.com/books/popular_rmdb_mysql/website/)
      * `mongodb`
        * [主流文档型数据库：MongoDB](http://book.crifan.com/books/popular_document_db_mongodb/website)
      * `sqlite`
      * 等等

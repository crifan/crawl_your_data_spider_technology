# 不同方案和爬虫步骤的对应关系

不用爬虫框架和常见爬虫框架和爬虫的不同步骤和功能的对应关系

|    | 下载（网页） | 提取（内容） | 保存（数据） |
| -- | ---------- | ---------- | ---------- |
| 自己裸写Python代码 |  `urllib` | `re` | `txt`/`csv` |
| 用各种Python库组合 |  `requests` | `BeautifulSoup`/`lxml` | `csv` / `pymysql` |
| 用框架`PySpider`| `requests` (`PySpider`的`self.crawl`) | (`PySpider`内置的)`PyQuery` | (`PySpider`内置)各种`数据库`(接口） |

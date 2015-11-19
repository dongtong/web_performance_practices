## Website性能优化实践

### 1. 介绍

网站性能优化，前提是你不要用类似Mac Pro这样的怪兽级电脑，加上100M带宽的客观环境去衡量网站的性能。应该考虑大众的网络带宽以及CPU状况。
在衡量网站性能的时候，最好是在生产环境下，不要在本地访问localhost去监控，大多数情况下没有什么太大意义。

[性能黄金规则](http://www.stevesouders.com/blog/2012/2/10/the-performance-golden-rule/)

    80% ～ 90%终端响应时间都花费在前端，从这里开始优化 -- Steve Souders
    
### 2.性能优化Checklist

#### 2.1 图片优化




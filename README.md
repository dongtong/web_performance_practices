## Website性能优化实践

### 1. 介绍

网站性能优化，前提是你不要用类似Mac Pro这样的怪兽级电脑，加上100M带宽的客观环境去衡量网站的性能。应该考虑大众的网络带宽以及CPU状况。
在衡量网站性能的时候，最好是在生产环境下，不要在本地访问localhost去监控，大多数情况下没有什么太大意义。

- [性能黄金规则](http://www.stevesouders.com/blog/2012/2/10/the-performance-golden-rule/)

    80% ～ 90%终端响应时间都花费在前端，从这里开始优化 -- Steve Souders
    

- [Website页面加载性能测试](http://webpagetest.org)

- [比较页面加载](http://whichloadsfaster.com)

    
### 2.性能优化Checklist

#### 2.1 [预加载](https://css-tricks.com/prefetching-preloading-prebrowsing/)

预加载是一种经常被忽略的页面优化方式，它可以预测将要加载的资源。这些预加载的资源可能在当前页面使用，也可能在将来可能访问的页面使用。开发页面时可以做一些大胆性的预测，在不不影响当前页面的性能前提下，预加载这部分资源。*大部分预加载技术移动端不支持，PC支持有限*。当前预加载资源主要有以下几种:

* dns-prefetch

   提前告知浏览器将要加载资源(页面)的DNS,减少DNS查询时间。如果页面加载跨域资源、CDN资源、页面跳转时，比较有用。chrome会从浏览缓存记录中解析DNS，但是如果没有，此方法便派上用场。所以不管浏览器会不会提前解析DNS,使用此方法。
  
        <link ref="dns-prefetch" href="//resource.com" />
        
* subresource

   当前页面使用到的资源提高加载优先级。
   
        <link ref="subresource" href="main.css" />
        
* prefetch

    提前加载并缓存页面将来需要使用的资源，只要是浏览器可以缓存的(包括图片，脚本，字体...)都可以。此种方式浏览器会发出请求缓存，但是也只是在浏览器空闲的时候。一般使用此方式提前加载字体，prefetch可以跨域加载资源。注意，这个有可能被浏览器忽略，可以通过控制台看一下是否预加载了。
    
        <link ref="prefetch" href="//example.com/images/later.png" crossorigin/>
        <link ref="prefetch" href="//example.com/js/after.js" />
        <link ref="prefetch" href="//example.com/css/main.css" />
        
* preconnect

    比dns-prefetch多做了一些事，就是建立TCP和TLS 链接。后面的请求在前面建立链接的前提下，加快了下载时间。
    
        <link ref="preconnect" href="http://a.com" />
        <link ref="stylesheet" href="http://a.com/fonts/xxxx" />
        
* prerender

    指定加载页面的所有资源。a.html中的所有资源被加载。用户如果有很大可能进入a页面时，可以使用。
    
        <link ref="prerender" href="a.html" />

* preload: 草案

    比prefetch更加可靠，它会始终预加载资源
    
        <link ref="preload" href="//example.com/images/a.png" />


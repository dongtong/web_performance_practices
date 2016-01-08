## Website性能优化实践

### 1. 介绍

网站性能优化，前提是你不要用类似Mac Pro这样的怪兽级电脑，加上100M带宽的客观环境去衡量网站的性能。应该考虑大众的网络带宽以及CPU状况。
在衡量网站性能的时候，最好是在生产环境下，不要在本地访问localhost去监控，大多数情况下没有什么太大意义。

- [性能黄金规则](http://www.stevesouders.com/blog/2012/2/10/the-performance-golden-rule/)

    80% ～ 90%终端响应时间都花费在前端，从这里开始优化 -- Steve Souders
    

- [Website页面加载性能测试](http://webpagetest.org)

- [比较页面加载](http://whichloadsfaster.com)

#### 1.1 性能最佳实践

- 减少Cookie大小、减少域名解析(DNS)、空闲时预加载、CSS选择器优化、web font等等

- Chrome Dev Tools, Google PageSpeed, YSlow等等作为衡量标准

- 自动化检测: http://www.webpagetest.org, https://www.sitespeed.io

#### 1.2 性能监控体系

給以下辨准都设定一个可度量的分数，在特定情况下调准标准:

- 禁止发出字体(woff, ttf)请求

- 减少接口请求数量

- 图片大小检查

- 减少CSS文件请求数量

- 图片懒加载

- 准备404页面

- 首屏请求数量检查

- 首屏请求大小检查

- 图片域名收敛检查

- 图片尺寸检查

- 禁止使用gif图片

一个页面发布之前，强制自动跑分，满足一定的分数才能发布上线。满分 100，低于 93 分的页面不允许发布。每不满足一项扣减相应的分数，比如一个图片链接没有域名收敛扣 5 分，并且单项会重复累加积分，基本有 2 项不合格就无法发布。

真机测评，每次跑分都会随机连接到一个真实的手机上跑分［针对无线］。

所有的无线页面在同一平台发布，保证有统一的入口。这点很重要，这样就可以在这个入口统一处理，保证所有页面都通过测试验证。另外一个好处是如果标准有调整也可以及时传递个开发人员，解决宣导不及时的问题［针对无线］。


监控:

- UI 监控（JS 错误检测、静态资源可访问检测、空链检测等）
- 前端单元测试（前端框架、核心逻辑、Node.js 测试等）
    
### 2.性能优化Checklist

#### 2.1 [预加载](https://css-tricks.com/prefetching-preloading-prebrowsing/)

各种 W3C 标准的预处理，但不是所有浏览器都支持。预加载是一种经常被忽略的页面优化方式，它可以预测将要加载的资源。这些预加载的资源可能在当前页面使用，也可能在将来可能访问的页面使用。开发页面时可以做一些大胆性的预测，在不不影响当前页面的性能前提下，预加载这部分资源。*大部分预加载技术移动端不支持，PC支持有限*。当前预加载资源主要有以下几种:

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

#### 2.2 [Web Font优化](http://velocityconf.com/devops-web-performance-ny-2015/public/schedule/detail/46234)

web fonts 加载完成后会导致已加载的 DOM 回流，解决方式有 2 个：

- 减少文件大小按需加载；
- 使用 woff 2 比 woff 小 30%
- 尽早的触发 fonts 下载

但仍然无法根本解决延迟下载导致的回流问题，只是尽可能的将时间缩短。尤其是使用 iconfont 时，性能较低的机型会先展示一个方框，加载完后才显示正确的图形。

#### 2.3 [Flexbox布局](http://velocityconf.com/devops-web-performance-ny-2015/public/schedule/detail/43921)

flexbox 的性能，pageload 时间比 float 快大约 40%，resize，scroll 时的性能消耗都有较大提升。

####流畅滚动的N条军规:移动(腾讯)

- body上加上 -webkit-overflow-scrolling: touch

- iOS尽量使用局部滚动

- iOS引入ScrollFix 避免出界

- Android下尽量使用全局滚动

	- 尽量不使用overflow: auto
	
	- 使用min-height: 100%代替height: 100%
	
- iOS下带有滚动条且position: absolute的节点不要设置背景色
- 
#### 2.4 移动Web首屏优化

- 网络优化(优先考虑)

首屏渲染其实是主要内容的渲染，可以认为两部分，一是页面的骨架，二是根据项目特定场景，哪些内容是主要内容，必须提前展示的。PC的带宽已经非常快，首屏优化基本针对手机非wifi的数据网络。

手机数据网络没有链路查询，而是和运营商无线网络的信令交换，然后检查你的网络类型，套餐等等，然后才是到运营商的核心网络。然后才通过WAP网关进入互联网络，dns查询，访问特定网站的WAP页面。

一般在iOS机型上，网络延迟不是很明显，但是一些android的低端机（不是黑...:)），如红米，大神等等，在打开webview时，就感觉有延迟，这里可以找一些优化方案。

网络请求过程如下:

	DNS解析（100~200ms可以缓存）-> 建立TCP链接(三次握手100~200ms) -> HTTP Request(半个Round-trip time) -> HTTP Response(Round-trip time不确定优化空间)
	
	注: Round-trip time是回路时间，通俗来说就是一个请求发出到接受服务端回复的时间。PC上感觉不到，但是在移动端是可以明显感觉得到。
	
以上网络是否可以控制在1s以内？答案是可以的(俗称秒开)。4G网络比2G网络快，其实Round-trip time提升很大。可以使用Chrome模拟器模拟不同网络，查看RTT。有时一个空请求都需要耗费几百毫秒时间，这个时间就是RTT。

- 渲染优化

webkit引擎在渲染页面时，渲染时间DOM + CSSOM =>渲染时间。优化DOM操作，优化JavaScript, 优化CSS都是比较有限的。在网络优化基本完成时，可以考虑这部分优化。


---
title: Node.js前后端分离在广告页面中的思考
date: '2014-04-20'
description: node.js 效率
categories: "Node.js"
---

撇下Node.js社区的红火，异步I/O的相比阻塞I/O的好处，也不谈回调嵌套是否反人类，Node.js是否在“羞辱”后端程序员，我想借着这篇文字理一下Node.js在我们广告页面中能否提高合作效率和迭代速度。  

### 过去和现状

我们团队维护直通车、外投外、无线等多条产品线的二跳页面，且正在接入一跳广告位。  
  
2012年我刚来团队的时候，说的简单点，主要做的事情无非就是从广告引擎、其他数据源获取数据，展示到页面上。因为公司技术环境的原因，最开始选择了webx+velocity模板，由前端工程师写静态的html页面，java工程师拿过来，把其中的动态部分用vm模板语言重写一遍，再从后端数据源把数据拿过来，几乎不会对数据进行更多的逻辑加工。加上页面几乎没有用户输入部分，基本上交互都集中在纯js/css动态效果，ajax异步数据请求和展现（没有数据输入）。  
  
可以看到这样的合作模式里，前端/后端工程师的分工是非常明确和解耦的，当然前端同学也能在这样的模式下积累可重用的js组件提交效率。但，随着粗放式增长的瓶颈，加上内部流程的制约，这样的迭代方式遇到了问题？首先，经常性的页面局部调整一般只能在下周的发布日才能完成；一次无法尝试过多版本，因为开发html改成动态模版和测试都需要资源；重复的页面模块开发；后端工程师一直在干重复枯燥的劳动，没有精力去思考如何做到流程的自动化、数据可反馈、统一解决方案的问题；当然还有多终端时代的挑战。  
  
我们做的第一次尝试，“组件化”平台，我认为实际上是做到页面搭建过程的模型化，也就是说用一种统一的模型来描述页面的逻辑，进而用线上可动态改变的配置文件来描述这个模型。显然，在后端工程师的角度，我们不再是写写数据接口，写写页面模版了，我们面对统一的页面模型，进行配置的统一生成和解析。这里可以简单说下这个模型，基于广告页面的实际业务场景，我们把页面切分成几个独立的块，每块用“动态模版+数据源配置+样式js”的方式描述，块之间的js,css依赖通过css class命名规范进行解耦，块之间的数据依赖（这样的依赖极少）通过配置上的数据传递来解决。  
  
“组件化”平台的配置化的统一模型尝试是突破性的，后端开发终于从将html改写成动态模版这样的无聊的工作中解脱了，我们也似乎看到一点平台化的统一解决方案的影子。但，因为这个配置化的模型还是过于复杂了，生成模型配置的管理系统，提供出来的配置界面(`http://moc.taobao.org/`)过于复杂，也注定了它只能给开发自己玩玩。更大的问题是，**前端工程师需要写后端动态模板**，调试是个大问题呀！前端们用node.js+velocity.js搭了一个调试平台，然后再用约定的接口数据格式，自己定义一些mock数据调试。这样的方式勉强可以跑通，但前端开发完页面的动的模块后，需要后端工程师在配置系统配置上数据接口和模块之间的依赖关系，然后再将模块代码上传到后端渲染系统，然后才能看到真正的动态页面长什么样。这样的合作方式，我们做出来的页面已经覆盖到淘客、直通车、外投外、淘星云等多个页面，流量也上千万了。如此多的依赖，和前端对最终运行环境的无法感知，带来不少坎坷和诡异的问题。  

### “组件化”方案中前后端到底哪里耦合

首先再反思下，我们现有的前后端解耦手段：  

1. 数据接口输出预先约定好
2. Node.js + velocity.js 搭建前端开发调试环境
3. 前端控制动态模版的编写和css/js的控制
4. 后端控制数据接口入参的控制，数据接口的准备，页面模块间依赖的配置，
最终配置的解析和渲染输出

看起来，一点问题都没有，大家分工很清晰，由于前端工程师编写的是后端的动态模版（现在是vm），难免不熟悉容易出错。如果熟悉与否还可以通过时间来解决，那另一个致命的问题看起来就很难解决了：模版从开发完到最终渲染成页面，中间隔了太多层，且这部分对前端完全黑盒，一旦页面上出现什么问题，必然是后端前端测试都扎在这个长链里排查。排查结果，有时候是vm语法写错，有时候时配置出错，有时候是js有问题。  

### Node.js真的能够解耦前后端么

如果将模版的渲染交给前端工程师控制呢？  
看一下现在前端圈的声音：  
  
李牧[《818前后分离的架构以及Node在其中的作用》](http://limu.iteye.com/blog/2042700)这篇试图解决的问题就是：“前后端如何解耦？”提到了Backone为为代表的OPOA(One Page One Application)下后端彻底的数据API化，到这里解耦还只在浏览器器和服务器的解耦;然后又提到了Node.js将这种解耦扩展到了服务器端，因为Node.js来了，服务器端端的UI层（原来由vm,jsp等后端模版语言把控）也能交给前端工程师控制了。  
  
朴灵[《Node.js的核心与红利》](http://www.programmer.com.cn/13844/)更多的实在强调Node.js生态圈的完善和生机勃勃，异步I/O的好处，鼓励JavaScript更多的涉足后端，注意，并没有说Node.js是大势所趋，因为毕竟异步I/O并不是Node.js独有，v8引擎性能和稳定性虽然在脚本语言中已经很赞，暂时也不足以替代JVM。  
  
Nicholas[《Node.js and the new web front-end》](http://www.nczonline.net/blog/2013/10/07/node-js-and-the-new-web-front-end/)也在说Node.js能把后端UI layer也放到前端工程师这边来做，后端工程师可以去关心数据存储、数据接口等逻辑。但，其实细想一下，如果是ajax + jjson数据api的方式是不是效果也一样，所谓将UI layer放到Node.js里来做能解耦前后端的说法作用就不那么大了。  
  
所以，我觉得Node.js能解耦前后端的说法其实并不是一个普适的说法。加上大多数现在的服务器架构下，异步化，cache技术，长链接技术，接口化等方案的选择，Node.js也并不是必须的，可能在现有语言和平台做一些技术改造（例如从现在的多线程阻塞I/O变成tomcat异步支持+servlet3+NIO），在人员储备和所花时间精力上都要好的多。  

### Node.js能解决“组件化”方案耦合问题么

首先，前端工程师写模板不用再用vm这样的后端模版语言了，模版渲染也放在Node.js中。考虑到多终端:
  
1. PC下首屏依然得服务器渲染，后面走ajax请求，cache/[BigPipe](http://engineering.xueqiu.com/blog/2013/02/27/implementing-bigpipe-in-nodejs/)走起来
2. 无线App直接面对服务器端数据API接口（就不一定是http了，自定义协议都是可能的）
3. 无线页面，相比PC对网络流量和网速敏感

无线APP看起来广告这边暂时不会做，但将来做不做就说不定了。若三个场景都能用同一套技术架构看起来确实不错。但注意，这也并不是非Node.js不可。还是回到“组件化”里模版交给Node.js渲染，但事情不只是将数据API抽离出去这么简单！因为在请求数据之前，还有页面配置、分流配置的解析，进一步有配置的更新。这部分涉及到metaq tair diamond HSF TFS等交互，当然这些Node.js的支持的也不错（[阿里 node 手册](http://www.atatech.org/article/detail/13675/0)）。问题的关键到了！如果配置的解析和更新不是放到Node.js中实现，实际上前端同学写出的js模板（注意这里已经不是vm模板了）还是跑不起来呀，因为这个模板还是按照“组件化”统一配置化模型定义的数据结构来编写的，一个页面的各模版还需要通过配置串联起来，渲染模版面对的数据结构依然不是纯净的宝贝/店铺list数据，还包含了其他页面控制数据。  
  
再进一步想一下，咱们前端开发工程师现在写完页面是不是还要通过配置系统上传发布呢？是不是排查问题的链还是那么长呢？似乎问题并没有得到很明显的改善。  
  

### 回到问题的本质，我们最终和最初的目的是什么

我们的核心目的是什么？快速的页面迭代，快捷统一构建页面入口，个性化分流支持，数据回流。  
前端工程师擅长页面UI控制，不擅长数据接口输入参数控制，也不应该关心页面数据依赖，不关心分流配置，不关心数据回流。如果去简化“组件化”统一模型配置，去掉页面模块数据依赖（这个完全可以做到，只要把有依赖的木块看做一个模板来实现就行了），并将前后端渲染的选择权交给前端工程师控制，Node.js可调用页面配置解析接口获取这样的返回:  

        {
          "page_layout": 1,
          "page_module1": [
            "http://odin.re.taobao.com/data1?keyword={url.key}&nickname={cookie.tracknick}&pid=44_4444",
            "http://odin.re.taobao.com/data2?keyword={url.key}&nickname={cookie.tracknick}&pid=44_4444",
          ],
          "page_module2": [
            "http://odin.re.taobao.com/data2?keyword={url.key}&nickname={cookie.tracknick}&pid=44_4444",
          ]
        }


一个页面静态的部分包括html框架，css控制，js控制，都在page_layout里，动态数据获取通过返回的数据接口获取。注意`keyword={url.key}`代表Node.js需要从请求参数解析出key参数替换进去。cookie也类似。不过这段逻辑是统一的也不会很复杂。  
  
上面返回格式中，都是http协议的数据api，也不仅限于http。哪几个api直接Node.js请求数据渲染，哪几个由页面ajax异步请求数据，完全交给前端工程师控制了，也就是通过page_layout id能找到那段决定如何使用这些page_module的数据接口的代码就可以了。  
  
页面中其他需要动态数据的部分，例如翻页参数的拼接，因为Node.js能直接面对请求，所以都能顺利拿到，再不需要js在后台吐出的数据中苦苦找了。页面渲染要用啥工具，直接Node.js提供就行了。另，我建议不要用现在metaq消息更新page_layout等配置，前端工程师写完page_layouy页面组件模板等自己行管理推送到Node.js服务器，最简单就是复用tms方式全量同步。将模板完全交给前端工程师维护，也有利于模板复用和模板库的形成。至于在页面数据数据接口配置，分流配置等后端掌控部分，也可以尝试Node.js来写配置管理系统，以做到拖拉生成页面时的实时渲染。  
  
“组件化”模型简化后，看起来皆大欢喜。前端工程师编写页面调试、和最终线上渲染的环境统一了，且无需关心接口的入参逻辑（因为从哪里获取参数那段代码是统一的）。后端的数据json数据API接口封装，屏蔽了不同广告引擎和数据源的复杂性，提供给前端使用的只是宝贝list/店铺list这样的统一模型。再插一句无关的，现在Http接口已经支持多个数据接口的合并请求。  
粗略思考，望各位指正。  

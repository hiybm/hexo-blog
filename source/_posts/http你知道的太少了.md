---
title: HTTP,你知道的太少了
date: 2017-08-20 10:04:45
tags: 
    - http协议
    - TCP/IP
    - 网络通信
categories: Javascript
---


<!-- ##### 开始 -->
>HTTP是一个`基于TCP/IP通信协议`来传递数据的无状态协议，且其是一个属于应用层的面向对象的协议。说到应用层，引一下《计算机网络》里边的内容，在家没有翻到这本书才想起来给忘带了过来，真是后悔大学里没有把这些基础掌握扎实啊。（掌握一些基础知识有百益而无一害，而且计算机方面知识都是互通的，不分到具体某个领域，就像是《计算机基础》这本书，虽说没有重要的知识要领掌握，但这是一本决定你层次高低的标杆，就是因为其内容很多涉及到计算机底层原理的知识）TCP/IP网络通信模型总共分为四层如下所示：

| TCP/IP网络通信模型      | 
| ------------- |
| 应用层-Application layer(HTTP，FTP，DNS...) |
| 传输层-Transport layer (TCP，UDP，RTP...)  |
| 网络层-Internet layer (IP，ICMP，AKP...) |
| 网络接入层-Link layer(IEEE 802.1A，FDDI，Ethernet...) |

而HTTP就是基于其中应用层的一套协议，<u>该层包括所有和应用程序协同工作，利用基础网络交换应用程序专用的数据的协议（来自维基百科）</u>，例如表格种列举的一样，感兴趣的同学可以自己研究下，本篇主要讨论一下http协议。言归正传，HTTP协议工作在客户端-服务端之间，浏览器作为HTTP客户端通过URL向WEB服务器发送请求，服务器接收到来自客户端（浏览器）的请求后会返回响应信息，就是简单的“请求-应答”模式但是为什么说http协议是无状态的？找了些资料大概是这样解释的：
        
- 协议对于事务处理没有记忆能力
- 对同一个url请求没有上下文关系
- 每次的请求都是独立的，它的执行情况和结果与前面的请求和之后的请求是无直接关系的，它不会受前面的请求应答情况直接影响，也不会直接影响后面的请求应答情况
- 服务器中没有保存客户端的状态，客户端必须每次带上自己的状态去请求服务器
    
以上几点中的关键词已经加粗，做重点理解，这时可以抛出一个最恰当的问题：既然HTTP是无状态的，那keep-alive的值意义何在？其实keep-alive就是为了让客户端和服务端保持一种持久的连接联系，这样也是为了减少请求/响应的开销，提高性能，但这并不意味着请求头中有了这个参数，HTTP就不是无状态的协议了，其本身仍是无状态的协议，而且无状态指的是在应用层这个层次上服务器没有保存客户端的状态信息，ok说到这里也是时候搞一搞`cookie`和`session`了，两者都是客户端和服务端通信的介质，简而言之，cookie是客户端的存储空间，由浏览器来维持，其采用的是在客户端保持状态的方案，而session机制采用的是在服务器端保持状态的方案。有些时候由于才服务器端保持状态的方案在客户端也需要保存一个标识，所以session机制可能需要借助于cookie机制来达到保存标识的目的。（近期做的一个项目让我意识到了自己的知识窄区例如`session/http`相关知识，以前只是知道并没有深入了解，个人觉得在某些专业领域还是要抛弃不求甚解的思想，有时候钻个牛角尖也会有额外的收获）重点来了，虽然cookie和session都可以保存状态，还有keep-alive持久连接，但是，但是，但是它们并没有改变HTTP本身无状态这一点，因为这些办法只是在帮助客户端和服务端建立一套更好的协作通信关系，以提高通信和开发效率。
    
HTTP主要通过URI来传输数据和建立来建立连接，这里的URI（Uniform Resource Identifier）指的是统一资源标识符，不同于URL（Uniform Resource Locator），其指的是统一资源定位符，两者的关系可以理解为URL是一个特殊类型且具体的URI，一个URL里包含了用于查找某个资源的足够信息。就拿小融租里的H5页面举个例子： 
    
`http://www.google.com:7001/signContract?apptoken=jksldjflkdjslkfjds&contractId=110&orderId=911#user`

从这个URL我们可以看出里边的参数包含一下几项：
        
1. 协议部分：‘http：’这代表网页使用的是HTTP协议，在‘http’后面的‘//’为分割符，此外还有HTTPS、FTP协议，BTW，一个域名下不建议同时掺杂http和https的链接，这样会导致出现`混合内容`，对网站安全造成威胁，例如之前神策埋点的链接就造成类似问题。具体关于混合内容涉及到we端安全方面的知识，感兴趣的同学可以去了解下，我也是无意中一个朋友问到这个问题才去了解的，多了解下有害无益嘛。
2. 域名部分：该地址的域名为“www.google.com”，一个URL中也可以使用IP地址作为域名使用，比如使用这个IP也可以打开百度首页（渣渣）: `202.108.22.5`，至于为什么，感兴趣的同学可以去了解下DNS（Domain Name System）。
3. 端口：“7001”域名后面冒号（：）紧跟着的是端口，可以省略，如果省略的话会采用浏览器的默认端口即80。
4. 目录路径：从端口号开始的第一个“/”开始到最后一个“/”，这之间是目录部分，例如“signContract”。
5. 参数部分：从“？”开始到“#”为止之间的部分是参数，多个参数用“&”连接，此例中的参数是
“`apptoken=jksldjflkdjslkfjds&contractId=110&orderId=911`”
6. 锚：从“#”开始到最后是锚部分，小融租项目里没有使用到锚点部分，这里示例只做讲解。参数可省略。
    
>至于http状态码、请求及响应相关的东西就不做详解了，这里个人觉得有必要再梳理一下http的工作原理。首先客户端向服务器发送一个请求报文，请求报文中包含请求的方法、url、协议版本、请求头部、请求数据，服务器以一个状态行作为响应，响应的内容包括协议的版本、成功或者错误代码、服务器信息、响应头部和响应数据。以下是http请求响应的步骤：
    
- 客户端连接到web服务器：通常是从浏览器地址栏进入，建立一个TCP套接字连接，例如：“`https://www.google.com/`”
- 发送HTTP请求：通过TCP套接字，客户端向web服务器发送一个文本的请求报文，一个请求报文由请求行、请求头部、空行和请求数据4部分组成。
- 服务器接受请求并返回http响应：web服务器解析请求，定位请求资源。返回给客户端读取。
- 释放连接：若`connection`为`close`，则服务器主动关闭TCP连接，客户端被动关闭连接，释放TCP连接；若`connection`为`keep－alive`，则该连接会保持一段时间。
- 客户端浏览器解析HTML内容：客户端浏览器首先解析状态行，查看表明请求是否成功的状态代码。然后解析响应头，响应头告知一下为若干字节的HTML文档和文档的字符集。客户端浏览器读取响应数据HTML，并在浏览器渲染。
    
以上所述可以理解成一个完整的请求及响应过程，其中还涉及到`DNS解析域名`、`TCP三次握手`等细节，因为本篇文章主要介绍HTTP就不做详细讲解（其实我也不太了解哈，有待深究）。关于HTTP相关知识点是我最近接触到的，所以总结一下加深自己的印象，文中若有歧义或是不正确的地方还望不吝指正，不过要想把HTTP整个吃透还相差甚远，毕竟上周在公交车上看到个人拿了一本《图解HTTP》，想想真的是学海无涯，就如那句hello-world所说，打开了新世界的大门，而且这个新world还有很多东西需要探索，这时就要告诉自己：Keep going and never stop ！
        
> - 在这里非常感谢维基百科以及以下链接作者的解惑，向大佬看齐【敬礼！】
> - http://www.jianshu.com/p/80e25cb1d81a
> - http://www.cnblogs.com/bellkosmos/p/5237146.html
    
    


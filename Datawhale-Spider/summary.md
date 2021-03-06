<!-- TOC -->

- [常用命令](#常用命令)
- [一些快捷操作](#一些快捷操作)
- [Trick](#trick)
- [前要](#前要)
    - [库](#库)
- [HTML/CSS/JS等基础知识](#htmlcssjs等基础知识)
    - [互联网、HTTP](#互联网http)
        - [互联网](#互联网)
        - [HTTP](#http)
    - [网页基础](#网页基础)
        - [网页组成](#网页组成)
        - [网页结构](#网页结构)
        - [HTML DOM](#html-dom)
    - [CSS](#css)
    - [使用开发者工具检查网页](#使用开发者工具检查网页)
    - [JavaScript与AJAX技术](#javascript与ajax技术)
        - [JavaScript语言](#javascript语言)
            - [JavaScript语言的特点](#javascript语言的特点)
            - [如何使用JavaScript](#如何使用javascript)
            - [JavaScript的基本语法](#javascript的基本语法)
            - [JavaScript在HTML中引用](#javascript在html中引用)
        - [AJAX](#ajax)
- [requests](#requests)
    - [requests.get()](#requestsget)
    - [python自带的urllib](#python自带的urllib)
    - [requests.post()](#requestspost)
    - [request.get进阶：爬取豆瓣电影](#requestget进阶爬取豆瓣电影)
    - [使用API](#使用api)
- [网页页面解析](#网页页面解析)
    - [bs4库](#bs4库)
    - [XPath](#xpath)
    - [re正则表达式](#re正则表达式)
- [session & cookie](#session--cookie)
    - [静态网页与动态网页](#静态网页与动态网页)
        - [静态网页](#静态网页)
        - [动态网页](#动态网页)
    - [http 1.0](#http-10)
    - [session和cookies](#session和cookies)
        - [session](#session)
        - [cookies](#cookies)
    - [一个重要概念——服务端session过期](#一个重要概念服务端session过期)
- [ip 代理](#ip-代理)
    - [应对ip被封的几种套路](#应对ip被封的几种套路)
    - [如何获取代理IP地址](#如何获取代理ip地址)
    - [使用代理](#使用代理)
    - [确认代理ip地址的有效性](#确认代理ip地址的有效性)
    - [改进：确认网站title](#改进确认网站title)
    - [关于http和https代理](#关于http和https代理)
- [selenium](#selenium)
    - [操作基本步骤](#操作基本步骤)
    - [查找元素](#查找元素)
    - [页面交互](#页面交互)
    - [等待页面加载](#等待页面加载)

<!-- /TOC -->

# 常用命令
* r.status_code ：状态码，200则requests.get()成功
* r.encoding = 'utf-8' ：更改requests.get()后的编码

# 一些快捷操作
* 通过审查元素，使用快捷键Ctrl+shift+c快速定位网页内容在elements中的位置
* 在chrome开发者模式中，Network，点击XHR就只会显示提交的内容(POST)，主要是获取Request Headers中的User-Agent和From Data
* python的内置函数find来定位指定子串
* chrome找cookie：F12, Network, 勾选preserve log, Doc过滤, Headers。一般是html结尾
* chrome可以直接copy XPath：F12, Elements, 右键copy, 选择copy XPath

# Trick
* 乱码：先r.encode('ISO-8859-1'), 后r.decode('utf-8')。或者直接r.encoding = r.apparent_encoding
* 'User-agent': 'Baiduspider'
* 在线正则测试
* 在chrome上粘贴到的XPath，去掉索引，可以爬出一个列表。
* 在chrome上粘贴到的XPath后加'/text()'就会出来。

# 前要
* 目的：定向网络数据爬取与网页解析
* 理念：The Website is the API...

## 库
* requests：自动爬取HTML页面，自动网络请求提交
* robots.txt：网络爬虫排除标准
* beautiful soup： 解析HTML页面
* re：正则表达式，提取页面关键信息
* scrapy*: 专业网络爬虫框架

# HTML/CSS/JS等基础知识
## 互联网、HTTP
---
### 互联网
---
互联网也叫因特网(Internet)，是指网络与网络所串联成的庞大网络，这些网络以一组标准的网络协议族相连，连接全世界几十亿个设备，形成逻辑上的单一巨大国际网络。它由从地方到全球范围内几百万个私人的、学术界的、企业的和政府的网络所构成。通过电子、无线和光纤等一系列广泛的技术来实现。这种将计算机网络互相连接在一起的方法可称作“网络互联”，在此基础上发展出来的覆盖全世界的全球性互联网络称为“互联网”，即相互连接在一起的网络。

**提示：**
互联网并不等于万维网(WWW),万维网只是一个超文本相互链接而成的全球性系统，而且是互联网所能提供的服务之一。互联网包含广泛的信息资源和服务，例如相互关联的超文本文件，还有万维网的应用，支持电子邮件的基础设施、点对点网络、文件共享，以及IP电话服务。

---
### HTTP
---

HTTP是一个客户端（用户）和服务器端（网站）之间进行请求和应答的标准。通过使用网页浏览器、网络爬虫或者其他工具，客户端可以向服务器上的指定端口（默认端口为80）发起一个HTTP请求。这个客户端成为客户代理（user agent）。应答服务器上存储着一些资源码，比如HTML文件和图像。这个应答服务器成为源服务器（origin server）。在用户代理和源服务器中间可能存在多个“中间层”，比如代理服务器、网关或者隧道（tunnel）。尽管TCP/IP是互联网最流行的协议，但HTTP中并没有规定必须使用它或它支持的层。

事实上。HTTP可以在互联网协议或其他网络上实现。HTTP假定其下层协议能够提供可靠的传输，因此，任何能够提供这种保证的协议都可以使用。使用TCP/IP协议族时RCP作为传输层。通常由HTTP客户端发起一个请求，创建一个到服务器指定端口（默认是80端口）的TCP链接。HTTP服务器则在该端口监听客户端的请求。一旦收到请求，服务器会向客户端返回一个状态（比如“THTTP/1.1 200 OK”），以及请求的文件、错误信息等响应内容。

---
HTTP的请求方法有很多种，主要包括以下几个：

- GET：向指定的资源发出“显示”请求。GET方法应该只用于读取数据，而不应当被用于“副作用”的操作中（例如在Web Application中）。其中一个原因是GET可能会被网络蜘蛛等随意访问。

- HEAD：与GET方法一样，都是向服务器发出直顶资源的请求，只不过服务器将不会出传回资源的内容部分。它的好处在于，使用这个方法可以在不必传输内容的情况下，将获取到其中“关于该资源的信息”（元信息或元数据）。

- POST：向指定资源提交数据，请求服务器进行处理（例如提交表单或者上传文件）。数据被包含在请求文本中。这个请求可能会创建新的资源或修改现有资源，或二者皆有。

- PUT：向指定资源位置上传输最新内容。

- DELETE：请求服务器删除Request-URL所标识的资源，或二者皆有。

- TRACE：回显服务器收到的请求，主要用于测试或诊断。

- OPTIONS：这个方法可使服务器传回该资源所支持的所有HTTP请求方法。用“\*”来代表资源名称向Web服务器发送OPTIONS请求，可以测试服务器共能是否正常。

- CONNECT：HTTP/1.1 协议中预留给能够将连接改为管道方式的代理服务器。通常用于SSL加密服务器的连接（经由非加密的HTTP代理服务器）。方法名称是区分大小写的。当某个请求所针对的资源不支持对应的请求方法的时候，服务器应当返回状态码405（Method Not Allowed），当服务器不认识或者不支持对应的请求方法的时候，应当返回状态码501（Not Implemented）。

---
## 网页基础
---
### 网页组成
---
数据来源是网页。网页是由 HTML 、 CSS 、JavaScript 组成的。

HTML 是用来搭建整个网页的骨架，而 CSS 是为了让整个页面更好看，包括颜色，每个模块的大小、位置等都是由 CSS 来控制的， JavaScript 是用来让整个网页“动起来”，这个动起来有两层意思，一层是网页的数据动态交互，还有一层是真正的动，比如我们都见过一些网页上的动画，一般都是由 JavaScript 配合 CSS 来完成的。

打开 Chrome 浏览器，F12 开发者工具。在选项 Elements 中可以看到网页的源代码，展示的就是 HTML 代码。

不同类型的文字通过不同类型的标签来表示，如图片用 &#60;img&#62; 标签表示，视频用 &#60;video&#62; 标签表示，段落用 &#60;p&#62; 标签表示，它们之间的布局又常通过布局标签 &#60;div&#62; 嵌套组合而成，各种标签通过不同的排列和嵌套才形成了网页的框架。

在Style 标签页中，显示的就是当前选中的 HTML 代码标签的 CSS 层叠样式，“层叠”是指当在HTML中引用了数个样式文件，并且样式发生冲突时，浏览器能依据层叠顺序处理。“样式”指网页中文字大小、颜色、元素间距、排列等格式。

而 JavaScript，它在 HTML 代码中通常使用 &#60;script&#62; 进行包裹，可以直接书写在 HTML 页面中，也可以以文件的形式引入。

---
### 网页结构
---
首先，整个文档是以 DOCTYPE 来开头的，这里定义了文档类型是 html ，整个文档最外层的标签是 &#60;html&#62; ，并且结尾还以 &#60;/html&#62; 来表示闭和。

浏览器解析 HTML 的时候，并不强制需要每个标签都一定要有闭和标签，但是为了语义明确，最好每个标签都跟上对应的闭和标签。大家可以尝试删除其中的闭和标签进行尝试，并不会影响浏览器的解析。

整个 HTML 文档一般分为 head 和 body 两个部分，在 head 头中，我们一般会指定当前的编码格式为 UTF-8 ，并且使用 title 来定义网页的标题，这个会显示在浏览器的标签上面。

body 中的内容一般为整个 html 文档的正文，html的标签由&#60;h1&#62;到&#60;h6&#62;六个标签构成，字体由大到小递减，换行标签为&#60;br&#62;，链接使用&#60;a&#62;来创建，herf属性包含链接的URL地址，比如&#60;a href="http://www.baidu.com" &#62;一个指向百度的链接&#60;/a&#62;

<a href="http://www.baidu.com" >一个指向百度的链接</a>

大多数原色的属性以“名称-值”的形式成对出现，由“=”连接并写在开始标签元素名之后。值一般由单引号或双引号包围，有些值的内容包含特定字符，在html中可以去掉引号。不加引号的属性值被认为是不安全的。要注意的是，许多元素存在一些共同的属性：

- id 属性为元素提供在全文档内的唯一标识。它用于识别元素，以便样式表可以改变其外观属性，脚本可以改变、显示或删除其内容或格式化。对于添加到页面的url，它为元素提供了一个全局唯一识别，通常为页面的子章节。

- class 属性提供了一种将类似元素分类的方式，常被用于语义化或格式化。例如，一个html文档可以指定class="标记"来表明所有具有这一类值得元素都属于文档的主文本。格式化后，这样的元素可能会聚集在一起，并作为页面脚注而不会出现在html代码中。类值也可以多值声明。如class="标记 重要"将元素同时放入“标记”与“重要”两类中。

- style 属性可以将表现性质赋予一个特定原色。比起使用id或class属性从样式表中选择元素，“style”被认为是一个更好的做法。

- tile 属性用于给元素一个附加的说明。大多数浏览器中这一属性显示为工具提示。

---
### HTML DOM
---
在 HTML 中，所有标签定义的内容都是节点，它们构成了一个 HTML DOM 树。

根据 W3C 的 HTML DOM 标准，HTML 文档中的所有内容都是节点：

* 整个文档是一个文档节点

* 每个 HTML 元素是元素节点

* HTML 元素内的文本是文本节点

* 每个 HTML 属性是属性节点

* 注释是注释节点

HTML DOM 将 HTML 文档视作树结构。这种结构被称为节点树：

![ct_htmltree](https://img-blog.csdnimg.cn/20200419153111123.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0MzE1OTg3,size_16,color_FFFFFF,t_70)

通过 HTML DOM，树中的所有节点均可通过 JavaScript 进行访问。所有 HTML 元素（节点）均可被修改，也可以创建或删除节点。

节点树中的节点彼此拥有层级关系。

父（parent）、子（child）和同胞（sibling）等术语用于描述这些关系。父节点拥有子节点。同级的子节点被称为同胞（兄弟或姐妹）。

* 在节点树中，顶端节点被称为根（root）

* 每个节点都有父节点、除了根（它没有父节点）

* 一个节点可拥有任意数量的子

* 同胞是拥有相同父节点的节点

下面的图片展示了节点树的一部分，以及节点之间的关系：

![dom_navigate.gif](https://img-blog.csdnimg.cn/2020041915314543.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0MzE1OTg3,size_16,color_FFFFFF,t_70)

---
## CSS
---
在CSS中，使用CSS选择器来定位节点。例如，div 节点的 id 为 container ，那么就可以表示为 #container ，其中 # 开头代表选择 id ，其后紧跟 id 的名称。另外，如果我们想选择 class 为 wrapper 的节点，便可以使用 .wrapper ，这里以点 . 开头代表选择 class ，其后紧跟 class 的名称。

另外， CSS 选择器还支持嵌套选择，各个选择器之间加上空格分隔开便可以代表嵌套关系，如 #container .wrapper p 则代表先选择 id 为 container 的节点，然后选中其内部的 class 为 wrapper 的节点，然后再进一步选中其内部的 p 节点。另外，如果不加空格，则代表并列关系，如 div#container .wrapper p.text 代表先选择 id 为 container 的 div 节点，然后选中其内部的 class 为 wrapper 的节点，再进一步选中其内部的 class 为 text 的 p 节点。这就是 CSS 选择器，其筛选功能还是非常强大的。

 <a href="https://www.runoob.com/html/html-tutorial.html" >菜鸟教程</a>HTML、CSS

---
## 使用开发者工具检查网页
---
以Chrome为例，选择“菜单”中的“更多工具”$\rightarrow$“开发者工具”，也可以直接在网页内容中右击并选择“检查”选项，还可以按f12键。

Chrome的开发者模式为用户提供了下面几组工具。

- Elements：允许用户从浏览器的角度来观察网页，用户可以借此看到Chrome渲染页面所需要的HTML、CSS和DOM（Document Object Model）对象。

- Network：可以看到网页向服务气请求了哪些资源、资源的大小以及加载资源的相关信息。此外，还可以查看HTTP的请求头、返回内容等。

- Source：即源代码面板，主要用来调试JavaScript。

- Console：即控制台面板，可以显示各种警告与错误信息。在开发期间，可以使用控制台面板记录诊断信息，或者使用它作为shell在页面上与JavaScript交互。

- Performance：使用这个模块可以记录和查看网站生命周期内发生的各种事情来提高页面运行时的性能。

- Memory：这个面板可以提供比Performance更多的信息，比如跟踪内存泄漏。

- Application：检查加载的所有资源。

- Security：即安全面板，可以用来处理证书问题等。

- 另外，通过切换设备模式可以观察网页在不同设备上的显示效果，快捷键为：Ctrl + Shift + M（或者在 Mac上使用 Cmd + Shift + M)。

右击后还有很多操作，值得一提的是快捷菜单中的“Copy XPath”选项。由于XPath是解析网页的利器，因此Chrome中的这个功能对于爬虫程序编写而言就显得十分实用和方便了。

使用“Network”工具可以清楚地查看网页加载网络资源地过程和相关信息。请求的每个资源在“Network”表格中显示为一行，对于某个特定的网络请求，可以进一步查看请求头、响应头及已经返回的内容等信息。对于需要填写并发送表单的网页而言（比如执行用户登录操作，以百度贴吧为例），在“Network”面板勾选“Preserve log”复选框，然后进行登录，就可以记录HTTP POST信息，查看发送的表单信息详情。之后在贴吧首页开启开发者工具后再登录时，就可以看到下图所示的信息，其中“Form Data”就包含向服务器发送的表单信息详情。

---
## JavaScript与AJAX技术
---
    
<br>
&#8195;&#8195;如果利用Requests库和BeautifulSoup来采集一些大型电商网站的页面，可能会发现一个令人疑感的现象，那就是对于同一个URL、同一个页面，抓取到的内容却与浏览器中看到的内容有所不同。比如有的时候去寻找某一个&lt;div&gt;元素，却发现Python程序报出异常，查看requests.get()方法的响应数据也没有看到想要的元素信息。这其实代表着网页数据抓取的一个关键问题——开发者通过程序获取到的HTTP响应内容都是原始的HTML数据，但浏览器中的页面其实是在HTML的基础上，经过JavaScript进一步加工和处理后生成的效果。比如淘宝的商品评论就是通过JavaScript获取JSON数据，然后“嵌入”到原始HTML中并呈现给用户。这种在页面中使用JavaScript的网页对于20世纪90年代的web界面而言几乎是天方夜测，但在今天，以AJAX（Asynchronous JavaScript and XML，异步JavaScript与XML）技术为代表的结合JavaScript、CSS、HTML等语言的网页开发技术已经成为绝对的主流。</br>

<br>
&#8195;&#8195;为了避免为每一份要呈现的网页内容都准备一个HTML，网站开发者们开始考虑对网页的呈现方式进行变革。在JavaScript问世之初，Google公司的Gmail邮箱网站是第一个大规模使用JavaScript加载网页数据的产品，在此之前，用户为了获取下一页的网页信息，需要访问新的地址并重新加载整个页面。但新的Gmail则做出了更加优雅的方案，用户只需要单击“下一页”按钮，网页就（实际上是浏览器）会根据用户交互来对下一页数据进行加载，而这个过程并不需要对整个页面（HTML）的刷新。换句话说，JavaScript使得网页可以灵活地加载其中一部分数据。后来，随着这种设计的流行，“AJAX”这个词语也成为一个“术语”，Gmail作为第一个大规模使用这种模式的商业化网站，也成功引领了被称之为“Web2.0”的潮流。
</br>

---
### JavaScript语言
---
 &#8195;&#8195;JavaScript语言一般被定义为一种“面向对象、动态类型的解释性语言”，最初由Netscape公司为Navigator浏览器开发，目的是作为新一代浏览器的脚本语言支持。换句话说，不同于PHP或者ASP. NET，JavaScript不是为“网站服务器”提供的语言，而是为“用户浏览器”提供的语言。从客户端——服务器端的角度来说，JavaScript无疑是一种客户端语言，但是由于JavaScript受到业界和用户的强烈欢迎，加之开发者社区的活跃，目前的JavaScript已经开始朝向更为综合的方问发展。随着V8引擎（可以提高JavaScript的解释执行效率）和Node.js等新潮流的出现，JavaScript甚至已经开始涉足“服务器端”。在TIOBE排名（一个针对各类程序设计语言受欢迎度的比较）上，JavaScript稳居前10，井与PHP、Pytbon、C#等分庭抗礼。
有一种说法是，对于今天任何一个正式的网站页面而言，HTML决定了网页的基本内容，CSS（Cascading Style Sheets，层叠样式表）描述了网页的样式布局，JavaScript 则控制了用户与网页的交互。

---
#### JavaScript语言的特点
---
* 动态语言
     
     动态语言是指程序在运行时可以改变其结构：新的函数可以被引进，已有的函数可以被删除等在结构上的变化。JavaScript便是一个动态语言。除此之外如Ruby、Python等也都属于动态语言，而C、C++等语言则不属于动态语言。比如在JavaScript中可以在对象定义之后动态的为其添加属性和方法


* 脚本语言
     
     脚本语言是为了缩短传统的编写-编译-链接-运行（edit-compile-link-run）过程而创建的计算机编程语言，只在被调用时进行解释或编译，然后执行。它的命名起源于一个脚本“screenplay”，每次运行都会使对话框逐字重复。早期的脚本语言经常被称为批量处理语言或工作控制语言。


* 弱类型
    
    弱/强类型指的是语言类型系统的类型检查的严格程度，弱类型的语言在声明变量的时候不必进行变量类型的确定，语言的运行时会隐式做数据类型转换，对于弱类型语言来说，不同类型的变量可以进行直接运算，而强类型的则不可以。
---
#### 如何使用JavaScript
---

1. 通过&lt;script&gt;&lt;/script&gt;中直接编写
2. 通过&lt;script src=’目标文档的URL’&gt;&lt;script&gt;连接外部Js文件
3. href属性值: 作为某个元素的时间属性值或者是超链接的href属性值

---
#### JavaScript的基本语法
---
* JavaScript的执行顺序：按照HTML文件中出现的顺序依次执行
* 大小写敏感：JavaScript严格区分大小写
* 忽略空白符和换行符
* 语句分隔符：使用";"结束语句，可以把多个语句写在一行，最后一个语句的分号可以省略，但尽量不要省略。可以使用{}括成一个语句组，形成一个block
* 通过\对代码进行折行操作：document.write(‘hello\world’);
* //单行注释&#8195;&#8195;多行注释/注释内容/
* JavaScript中的保留字：abstract，else，instanceof，super，boolean，enum，int，switch，break，export，interface，synchronized，byte，extends，let，this，case，false，long，throw，catch，final，native，throws，char，finally，new，transient，class，float，null，true，const，for，package，try，continue，function，private，typeof，debugger，goto，protected，var，defaut，if，public，void，delete，inplements，return，volatile，do，import，short，while，doble，in，static，width
* 通过document.write()向文档书写内容
* 通过console.log()向控制台写入内容
* 语法错误：通过控制台进行调试
* 逻辑错误：通过alert() 进行调试

__【提示】__ JavaScript的名字使得很多人会将其与Java语言联系起来，认为它是Java的某种派生语言，但实际上JavaScript在设计原则上更多受到了Scheme（一种函数式编程语言）和C语言的影响，除了变量类型和命名规范等细节，JavaScript与Java关系并不大。Netscape公司最初为之命名“LiveScript”，但当时正与Sun公司合作，加上Java语言所获得的巨大成功，为了“蹭热点”，遂将其名字改为“JavaScript”。JavaScript推出后受到了业界的一致肯定，对JavaScript的支持也成为在21世纪出现的现代浏览器的基本要求。浏览器端的脚本语言还包括用于Flash动画的ActionScript等。

---
#### JavaScript在HTML中引用
---

&#8195;&#8195;为了在网页中使用JavaScript，开发者一般会把JavaScript脚本程序写在HTML的&#60;script&#62;标签中。在HTML语法里，&#60;script&#62;标签用于定义客户端脚本，如果需要引用外部脚本文件，可以在src属性中设置其地址。

&#8195;&#8195;JavaScript在语法结构上比较类似于C++等面向对象的语言，循环语句、条件语句等也都与Python中的写法有较大的差异，但其弱类型特点会更符合Python开发者的使用习惯。

&#8195;&#8195;使用Chrome开发者模式下的“Console”工具（“Console”一般翻译为“控制台”），输入并执行JS函数，就可以看到Console对应的输出，如下图。

![4.2.png](https://i.loli.net/2020/04/02/tDFy7c8h5auKbjU.png)

&#8195;&#8195;除了对JavaScript 语法的了解，为了更好地分析和抓取网页，还需要对目前广为流行的JavaScript 第三方库有简单的认识。包括jQuery、Prototype、React 等在内的这些JavaScript库一般会提供丰富的函数和设计完善的使用方法。如果要使用jQuery，可以访问http://jquery.com/download/ ，并将jQuery源码下载到本地，最后在HTML中引用。

[jQuery库的教程](https://www.runoob.com/jquery/jquery-tutorial.html)

&#8195;&#8195;也可使用另一种不必在本地保存.js文件的方法，即使用CDN（见下方代码）。谷歌、百度、新浪等大型互联网公司的网站上都会提供常见JavaScript库的CDN。如果网页使用了CDN，当用户向网站服务器请求文件时，CDN会从离用户最近的服务器上返回响应，这在一定程度上可以提高加载速度。

__【提示】__ 曾经编写过网页的人可能对CDN一词并不陌生。CDN即Content Delivery Network（内容分发网络），一般会用于存放供人们共享使用的代码。Google的API服务器即提供了存放jQuery等JavaScript库的CDN。这是比较狭义的CDN含义，实际上CDN的用途不止“支持JavaScript脚本”一项。

---
### AJAX
---

&#8195;&#8195;AJAX技术与其说是一种“技术”，不如说是一种“方案”。如上文所述，在网页中使用JavaScript 加载页面中数据的过程，都可以看作AJAX技术。AJAX技术改变了过去用户浏览网站时一个请求对应一个页面的模式，允许浏览器通过异步请求来获取数据，从而使得一个页面能够呈现并容纳更多的内容，同时也就意味着更多的功能。只要用户使用的是主流的浏览器，同时允许浏览器执行JavaScript，用户就能够享受网页中的AJAX内容。

&#8195;&#8195;AJAX技术在逐渐流行的同时，也面临着一些批评和意见。由于JavaScript本身是作为客户端脚本语言在浏览器的基础上执行的，因此，浏览器兼容性成为不可忽视的问题。另外，由于JavaScript在某种程度上实现了业务逻辑的分离（此前的业务逻辑统一由服务器端实现），因此在代码维护上也存在一些效率问题。但总体而言，AJAX技术已经成为现代网站技术中的中流砥柱，受到了广泛的欢迎。AJAX目前的使用场景十分广泛，很多时候普通用户甚至察觉不到网页正在使用AJAX技术。
以知乎的首页信息流为例，与用户的主要交互方式就是用户通过下拉页面（具体操作可通过鼠标滚轮、拖动滚动条等实现）查看更多动态，而在一部分动态（对于知乎而言包括被关注用户的点赞和回答等）展示完毕后，就会显示一段加载动画并呈现后续的动态内容。此处的页面动画其实只是“障眼法”，在这个过程中，JavasScript脚本已向服务器请求发送相关数据，并最终加载到页面之中。这时页面显然没有进行全部刷新，而是只“新”刷新了一部分，通过这种异步加载的方式完成了对新内容的获取和呈现，这个过程就是典型的AJAX应用。

&#8195;&#8195;比较尴尬的是，爬虫一般不能执行包括“加载新内容”或者“跳到下一页”等功能在内的各类写在网页中的JavaScript代码。如本节开头所述，爬虫会获取网站的原始HTML页面，由于它没有像浏览器一样的执行JavaScript脚本的能力，因此也就不会为网页运行JavaScript。最终，爬虫爬取到的结果就会和浏览器里显示的结果有所差异，很多时候便不能直接获得想要的关键信息。为解决这个尴尬处境，基于Python编写的爬虫程序可以做出两种改进，一种是通过分析AJAX内容（需要开发者手动观察和实验），观察其请求目标、请求内容和请求的参数等信息，最终编写程序来模拟这样的JavaScript 请求，从而获取信息（这个过程也可以叫作“逆向工程”）。另外一种方式则比较取巧，那就是直接模拟出浏览器环境，使得程序得以通过浏览器模拟工具“移花接木”，最终通过浏览器渲染后的页面来获得信息。这两种方式的选择与JavaScript在网页中的具体使用方法有关。

---
---
# requests
## requests.get()
## python自带的urllib
## requests.post()
## request.get进阶：爬取豆瓣电影
## 使用API

---
---
# 网页页面解析
## bs4库
## XPath
## re正则表达式

---
---
# session & cookie

## 静态网页与动态网页
---
### 静态网页

- 静态网页就是那种 html 页面，后缀为 `.html` 的这种文件，直接部署到或者是放到某个 web 容器上，就可以在浏览器通过链接直接访问到了，常用的 web 容器有 Nginx 、 Apache 、 Tomcat 、Weblogic 、 Jboss 、 Resin 等等，很多很多。举个例子：[desmonday's bolg](https://desmonday.github.io/)，就是静态网页的代表，这种网页的内容是通过纯粹的 HTML 代码来书写，包括一些资源文件：图片、视频等内容的引入都是使用 HTML 标签来完成的。它的好处当然是加载速度快，编写简单，访问的时候对 web 容器基本上不会产生什么压力。但是缺点也很明显，可维护性比较差，不能根据参数动态的显示内容等等。

### 动态网页

- 大家常用的淘宝、京东、拼夕夕等网站都是由动态网页组成的。
- 动态网页可以解析 URL 中的参数，或者是关联数据库中的数据，显示不同的网页内容。现在各位同学访问的网站大多数都是动态网站，它们不再简简单单是由 HTML 堆砌而成，可能是由 JSP 、 PHP 等语言编写的，当然，现在很多由前端框架编写而成的网页这里也归属为动态网页。
- 动态网页，可能使用频率最高的一个功能是登录，像各种电商类网站，肯定是登录了以后才能下单买东西。那么，问题来了，后面的服务端是如何知道当前这个人已经登录了呢？

## http 1.0

- HTTP 1.0的特点是无状态无链接的
- ![](https://cdn.geekdigging.com/python-spider/jd-http-1.png)

- 无状态就是指 HTTP 协议对于请求的发送处理是没有记忆功能的，也就是说每次 HTTP 请求到达服务端，服务端都不知道当前的客户端（浏览器）到底是一个什么状态。客户端向服务端发送请求后，服务端处理这个请求，然后将内容响应回客户端，完成一次交互，这个过程是完全相互独立的，服务端不会记录前后的状态变化，也就是缺少状态记录。这就产生了上面的问题，服务端如何知道当前在浏览器面前操作的这个人是谁？其实，在用户做登录操作的时候，服务端会下发一个类似于 token 凭证的东西返回至客户端（浏览器），有了这个凭证，才能保持登录状态。那么这个凭证是什么？

## session和cookies

- Session 是会话的意思，会话是产生在服务端的，用来保存当前用户的会话信息，而 Cookies 是保存在客户端（浏览器），有了 Cookie 以后，客户端（浏览器）再次访问服务端的时候，会将这个 Cookie 带上，这时，服务端可以通过 Cookie 来识别本次请求到底是谁在访问。

  可以简单理解为 Cookies 中保存了登录凭证，只要持有这个凭证，就可以在服务端保持一个登录状态。

  在爬虫中，有时候遇到需要登录才能访问的网页，只需要在登录后获取了 Cookies ，在下次访问的时候将登录后获取到的 Cookies 放在请求头中，这时，服务端就会认为我们的爬虫是一个正常登录用户。

### session

- 那么，Cookies 是如何保持会话状态的呢？

  在客户端（浏览器）第一次请求服务端的时候，服务端会返回一个请求头中带有 `Set-Cookie` 字段的响应给客户端（浏览器），用来标记是哪一个用户，客户端（浏览器）会把这个 Cookies 给保存起来。

  我们来使用工具 PostMan 来访问下某东的登录页，看下返回的响应头：

  ![](https://cdn.geekdigging.com/python-spider/jd_login-http-response.png)

- 当我们输入好用户名和密码时，客户端会将这个 Cookies 放在请求头一起发送给服务端，这时，服务端就知道是谁在进行登录操作，并且可以判断这个人输入的用户名和密码对不对，如果输入正确，则在服务端的 Session 记录一下这个人已经登录成功了，下次再请求的时候这个人就是登录状态了。

  如果客户端传给服务端的 Cookies 是无效的，或者这个 Cookies 根本不是由这个服务端下发的，或者这个 Cookies 已经过期了，那么接下里的请求将不再能访问需要登录后才能访问的页面。

  所以， Session 和 Cookies 之间是需要相互配合的，一个在服务端，一个在客户端。

### cookies

- 我们还是打开某东的网站，看下这些 Cookies到底有哪些内容：

  

  [![小白学 Python 爬虫（10）：Session 和 Cookies](https://cdn.geekdigging.com/python-spider/jd-cookies.png)
  
  ](https://cdn.geekdigging.com/python-spider/jd-cookies.png)

- 具体操作方式还是在 Chrome 中按 F12 打开开发者工具，选择 Application 标签，点开 Cookies 这一栏。

  - Name：这个是 Cookie 的名字。一旦创建，该名称便不可更改。
  - Value：这个是 Cookie 的值。
  - Domain：这个是可以访问该 Cookie 的域名。例如，如果设置为 .jd.com ，则所有以 jd.com ，结尾的域名都可以访问该Cookie。
  - Max Age：Cookie 失效的时间，单位为秒，也常和 Expires 一起使用。 Max Age 如果为正数，则在 Max Age 秒之后失效，如果为负数，则关闭浏览器时 Cookie 即失效，浏览器也不会保存该 Cookie 。
  - Path：Cookie 的使用路径。如果设置为 /path/ ，则只有路径为 /path/ 的页面可以访问该 Cookie 。如果设置为 / ，则本域名下的所有页面都可以访问该 Cookie 。
  - Size：Cookie 的大小。
  - HTTPOnly：如果此项打勾，那么通过 JS 脚本将无法读取到 Cookie 信息，这样能有效的防止 XSS 攻击，窃取 Cookie 内容，可以增加 Cookie 的安全性。
  - Secure：如果此项打勾，那么这个 Cookie 只能用 HTTPS 协议发送给服务器，用 HTTP 协议是不发送的。

  那么有的网站为什么这次关闭了，下次打开的时候还是登录状态呢？

  这就要说到 Cookie 的持久化了，其实也不能说是持久化，就是 Cookie 失效的时间设置的长一点，比如直接设置到 2099 年失效，这样，在浏览器关闭后，这个 Cookie 是会保存在我们的硬盘中的，下次打开浏览器，会再从我们的硬盘中将这个 Cookie 读取出来，用来维持用户的会话状态。

  第二个问题产生了，服务端的会话也会无限的维持下去么，当然不会，这就要在 Cookie 和 Session 上做文章了， Cookie 中可以使用加密的方式将用户名记录下来，在下次将 Cookies 读取出来由请求发送到服务端后，服务端悄悄的自己创建一个用户已经登录的会话，这样我们在客户端看起来就好像这个登录会话是一直保持的。

## 一个重要概念——服务端session过期

- 当我们关闭浏览器的时候会自动销毁服务端的会话，这个是错误的，因为在关闭浏览器的时候，浏览器并不会额外的通知服务端说，我要关闭了，你把和我的会话销毁掉吧。

  因为服务端的会话是保存在内存中的，虽然一个会话不会很大，但是架不住会话多啊，硬件毕竟是会有限制的，不能无限扩充下去的，所以在服务端设置会话的过期时间就非常有必要。

  当然，有没有方式能让浏览器在关闭的时候同步的关闭服务端的会话，当然是可以的，我们可以通过脚本语言 JS 来监听浏览器关闭的动作，当浏览器触发关闭动作的时候，由 JS 像服务端发起一个请求来通知服务端销毁会话。

  由于不同的浏览器对 JS 事件的实现机制不一致，不一定保证 JS 能监听到浏览器关闭的动作，所以现在常用的方式还是在服务端自己设置会话的过期时间

---
# ip 代理

## 应对ip被封的几种套路

## 如何获取代理IP地址

## 使用代理

## 确认代理ip地址的有效性

## 改进：确认网站title

## 关于http和https代理

---
# selenium

## 操作基本步骤

## 查找元素

## 页面交互

## 等待页面加载

---
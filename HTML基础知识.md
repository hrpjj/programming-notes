# HTML 与 Web 开发基础知识总结

本文面向已经接触过一些 HTML、但尚未学习 CSS 和 JavaScript 的初学者。我们会先建立浏览器与 Web 的整体认识，再按页面内容和使用场景复习 HTML，最后把知识组合成一个可以通过本地服务器访问的多页面学习笔记网站。

@[TOC](HTML 与 Web 开发基础知识复习目录)

**阅读路线**

- **第一次学习 Web：**按 1～18 章顺序阅读，先了解网页如何被访问，再逐步认识 HTML 结构、常见元素和完整案例。
- **只复习 HTML：**可从第 7 章开始，按语法、文档、文本、列表、链接、媒体、表格、表单和语义结构复习；遇到不熟悉的地址或浏览器概念时，再回看前 6 章。
- **准备继续 CSS/JavaScript：**先读第 1～8 章建立 Web 与文档结构基础，再关注第 15～18 章的语义、可访问性、调试和后续学习路线。

> **代码完整度说明：**完整示例会标明文件名，并说明是否可以直接保存运行；局部片段会注明应放置的位置或所依赖的上下文。文中只解释 CSS 和 JavaScript 在 Web 中的职责，不提前讲解它们的规则或逻辑。

## 1. Web 开发全景

本章回答 Web、网页、浏览器和服务器分别是什么，以及一次页面访问中客户端与服务器如何协作。我们会用请求与响应的视角串起 HTML、CSS、JavaScript、后端和数据的职责。

### 1.1 从 Internet 到一个网页

**Internet（互联网）**是让许多计算机网络互相连通的基础设施；**Web（万维网）**是运行在其上的一种服务，主要靠 URL 定位资源、通过 HTTP 交换资源、由浏览器展示页面。电子邮件也能使用 Internet，但不等于 Web。

一个**网页**是浏览器呈现的一份页面内容；多个互相关联的网页和资源通常组成**网站**。带登录、搜索、购物车等业务功能的网站常称 **Web 应用**。这不是严格的技术等级划分：一份静态 HTML 文件也能有可点击的链接；“动态”主要指内容可按用户或请求生成，不等于有动画。

| 角色 | 做什么 | 学习笔记站中的例子 |
| --- | --- | --- |
| 浏览器（客户端） | 发出请求、解析资源、显示页面 | 打开笔记首页 |
| Web 服务器 | 接收请求、返回资源或交给后端处理 | 返回 `index.html` |
| 搜索引擎 | 抓取并建立索引，帮助查找公开网页 | 搜索笔记相关内容 |

搜索引擎是找网页的服务，不是打开网页所必需的浏览器；浏览器可以直接访问已知 URL。B/S（Browser/Server）架构就是由浏览器这个客户端和服务器配合：

```text
用户输入地址 → 浏览器发出请求 → 服务器返回响应 → 浏览器解析并显示网页
```

**可观察结果：**访问同一地址时，浏览器地址栏显示 URL，开发者工具的 Network 面板可看到请求和响应。地址、请求、响应分别将在第 3～5 章展开。参见 [MDN：Web 如何工作](https://developer.mozilla.org/en-US/docs/Learn_web_development/Getting_started/Web_standards/How_the_web_works)。

### 1.2 静态、动态与分工

**静态网站**通常把已有 HTML、图片等文件直接作为响应：不同访问者请求同一文件，通常得到相同文件。**动态 Web 应用**可由服务器按账户、查询条件或数据库记录生成不同内容，也可让浏览器后续请求 API 更新数据。静态页面仍可有导航、原生表单和折叠内容；动态页面也可能看起来很朴素。

| 部分 | 主要职责 | 笔记站的情形 |
| --- | --- | --- |
| 前端 | 浏览器中看到和操作的页面 | 笔记目录、标题、链接 |
| 后端 | 处理业务请求、权限和数据 | 将来按关键词查询笔记 |
| 数据库 | 保存和查询结构化业务数据 | 将来保存用户的笔记记录 |

在前端内部，**HTML**描述内容和结构，**CSS**控制外观与布局，**JavaScript**负责程序化行为。现在只用 HTML 完成可读、可导航的页面；框架也不能代替这些基础。后端可以返回 HTML，也可以返回供前端使用的数据；数据库通常由后端访问，不能把浏览器当成可信的数据库客户端。

## 2. 第一个本地网站与开发环境

本章介绍编辑器、浏览器、开发者工具和本地 Web 服务器各自解决的问题，并建立一个最小可访问的网站目录。完成后可以保存首页、在浏览器中查看，并理解直接打开文件与通过本地服务器访问的区别。

### 2.1 建目录并保存首页

**用途：**编辑器负责写文件，浏览器负责显示，开发者工具负责观察页面结构和请求，本地 Web 服务器负责以 HTTP 提供文件。先在任意便于查找的位置建立 `study-notes` 目录：

```text
study-notes/
└── index.html
```

`index.html` 是许多 Web 服务器对目录首页采用的约定文件名；它不是浏览器唯一能读取的 HTML 文件名。保存时确认扩展名是 `.html`，不是 `.html.txt`；文件名宜用简短英文、小写字母和连字符，避免空格及大小写混淆。

**完整示例，文件名：`study-notes/index.html`，可直接保存并打开：**

```html
<!doctype html>
<html lang="zh-CN">
<head>
  <meta charset="utf-8">
  <title>学习笔记</title>
</head>
<body>
  <h1>学习笔记</h1>
  <p>欢迎来到我的 Web 学习笔记。</p>
</body>
</html>
```

**可观察结果：**浏览器标签页显示“学习笔记”，页面上出现大标题和一段文字。`title` 是标签页标题，`h1` 是页面正文标题；`lang` 标记文档语言，`meta charset` 指定字符编码。完整文档骨架会在第 8 章细讲。

### 2.2 从本地文件走到 localhost

双击文件时，地址栏常以 `file:///.../study-notes/index.html` 开头。这表示浏览器直接读取本机文件，适合快速检查简单 HTML，但它没有经过 HTTP 服务器；部分依赖来源或网络请求的功能在 `file://` 下表现也可能不同。

**在终端切换到 `study-notes` 目录后**，运行下面的命令（需要本机已安装 Python）：

```text
python -m http.server 8000 --bind 127.0.0.1
```

随后在浏览器打开 `http://127.0.0.1:8000/`；`http://localhost:8000/` 在本机通常也可访问。`127.0.0.1` 是指向本机的回环地址，`8000` 是此处选用的端口；`--bind 127.0.0.1` 只让服务监听本机。访问目录 `/` 时，这个静态服务器会寻找 `index.html`。

**可观察结果：**页面内容与双击文件相同，但地址栏从 `file://` 变成 `http://`。访问 `http://127.0.0.1:8000/` 时，Network 中这次 GET 的 Request URL 是 `http://127.0.0.1:8000/`，静态服务器返回的是 `index.html` 的内容；只有明确访问 `/index.html`，请求 URL 才包含这个文件名。改动 `<p>` 文字、保存后刷新，就能看到更新；右键“查看网页源代码”能看到收到的 HTML。停止服务器可在运行它的终端按 `Ctrl+C`。

> **注意：**`python -m http.server` 仅用于本机学习静态资源与 GET 请求，不实现登录、数据库或业务 POST 处理。即使能从表单发出 POST，它也不是处理业务数据的后端。

参见 [Python `http.server` 文档](https://docs.python.org/3/library/http.server.html) 与 [MDN：Web 如何工作](https://developer.mozilla.org/en-US/docs/Learn_web_development/Getting_started/Web_standards/How_the_web_works)。

## 3. URL、域名与网页地址

本章拆解 URL 中的协议、主机、端口、路径、查询参数和片段，并说明域名、DNS 与 IP 地址之间的基本关系。我们还会区分几种常见路径写法，帮助后续正确引用页面和资源。

### 3.1 拆开一条地址

**URL**（统一资源定位符）告诉浏览器去哪里、以何种方式访问资源。用一条示意地址逐段看：

```text
https://www.example.com:443/notes/list.html?tag=html&page=2#forms
```

| 部分 | 值 | 含义 |
| --- | --- | --- |
| 协议 | `https` | 使用 HTTPS |
| 主机名 | `www.example.com` | 要访问的服务器名称 |
| 端口 | `443` | 主机上接收连接的服务入口 |
| 路径 | `/notes/list.html` | 站点中的资源位置 |
| 查询字符串 | `tag=html&page=2` | 两个参数，可供服务器或页面读取 |
| 片段 | `forms` | 页面内的定位标记 |

浏览器把主机名（例如 `www.example.com`）交给 **DNS** 查询对应的 **IP 地址**，再按协议和端口连接目标服务。DNS 通常解析的是主机名，并不把整条 URL 连同路径、查询参数一起“翻译成 IP”；一个 IP 地址也可能承载多个网站。HTTP 默认端口通常是 80，HTTPS 默认端口通常是 443，因此示例里的 `:443` 可以省略，但端口概念依然存在。

**可观察结果：**将示例 URL 的 `#forms` 改成别的片段，浏览器可能改变页内位置，却不会因此把片段作为 HTTP 请求目标发送给服务器；片段由浏览器在本地处理。查询参数则是 URL 请求目标的一部分。实际写入 URL 的空格、中文及特殊字符需要按 URL 规则编码，浏览器通常会帮忙处理；例如空格可表示为 `%20`，不要把任意原始文字直接拼进去。参见 [MDN：什么是 URL](https://developer.mozilla.org/en-US/docs/Learn_web_development/Howto/Web_mechanics/What_is_a_URL)。

### 3.2 地址相同与不同：同源及路径

浏览器中的“**同源**”比较的是**协议、主机和端口**三项。`https://www.example.com/notes/` 与 `https://www.example.com:443/about` 同源；把协议改为 `http`、主机改为 `api.example.com`，或端口改为 `8443`，都会成为不同源。路径和查询参数不决定同源。

假设当前文档地址为 `https://www.example.com/notes/list.html`，下面是放在该文档链接 `href` 中的**局部示意值**：

| 写法 | 例子 | 解析后的目标 |
| --- | --- | --- |
| 绝对 URL | `https://www.example.com/about.html` | 指定协议和主机的页面 |
| 根相对 URL | `/about.html` | `https://www.example.com/about.html` |
| 文档相对 URL | `detail.html` | `https://www.example.com/notes/detail.html` |

根相对路径中的 `/` 从当前网站的 origin 根目录开始，不是电脑硬盘根目录。文档相对路径以当前文档所在目录为基准；`./detail.html` 与上例的 `detail.html` 一样，`../about.html` 会退回上一级。URL 用正斜杠 `/`，不能用 Windows 文件路径的反斜杠替代；上线后的服务器还可能区分文件名大小写。

从输入地址到取得页面，可以先记为：解析 URL → 查询主机名对应的 IP 地址 → 与目标服务建立连接 → 发出 HTTP 请求 → 接收响应。真实网络还涉及更多步骤，此处先抓住与写 HTML 有关的部分。

## 4. HTTP 与 HTTPS

本章说明浏览器和服务器如何通过 HTTP 请求与响应交换资源，认识常见方法、状态码、头部和媒体类型。随后简要解释 HTTPS 与 TLS 的作用，以及浏览器缓存带来的可观察现象。

### 4.1 看懂一问一答

**HTTP** 是客户端发出请求、服务器返回响应的协议，单次请求本身通常不保留上一请求的业务状态。请求和响应都有起始信息、头部和可选的消息体；并非每条消息都有正文。下面是对应第 3 章 URL 的**HTTP/1 风格教学文本**，用于认识结构；HTTP/2 和 HTTP/3 的线上表示方式不同，也不保证实际报文与此完全相同。

```text
GET /notes/list.html?tag=html&page=2 HTTP/1.1
Host: www.example.com
Accept: text/html

```

```text
HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
Cache-Control: max-age=60

<!doctype html>
<html lang="zh-CN">...</html>
```

空行分隔头部和消息体。这里的 GET 请求没有消息体；响应正文是 HTML。`#forms` 不在请求行里，因为片段不会发送给服务器。`Host` 指明目标主机，`Accept` 表示客户端希望接收的类型；响应的 `Content-Type` 告诉浏览器实际返回内容的媒体类型（MIME），如 `text/html`、`image/png`、`application/json`。同一个站点的 HTML、图片等资源通常各有自己的请求。

其他常见头部可先认用途：`Location` 常在重定向响应中给出新地址；`Cache-Control` 指示缓存策略；`Cookie` 是浏览器随合适请求发出的 Cookie，`Set-Cookie` 是服务器设置 Cookie；`Authorization` 可携带认证凭据；跨源请求可能带 `Origin`，服务器可用 `Access-Control-Allow-Origin` 表明允许哪个来源读取响应。头部是否出现，取决于具体场景，并非每个请求都带齐。参见 [MDN：HTTP 消息](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Messages)。

### 4.2 方法、状态码与缓存

**GET** 通常用于获取资源，例如访问笔记首页或按查询条件查找内容；**POST** 通常用于提交数据并让服务器处理，例如创建一条记录。POST 不自带加密能力，也不天然比 GET 安全；敏感信息的传输保护依赖 HTTPS，业务操作还须由服务器设计和验证。此处的本地静态服务器仅用于观察 GET，不用于验证 POST 业务。

| 方法 | 常见含义 | 记忆要点 |
| --- | --- | --- |
| GET / HEAD | 获取资源 / 只获取响应头 | HEAD 的响应不含消息体 |
| POST / PUT / PATCH | 提交处理 / 替换 / 局部修改 | 是否支持由服务器决定 |
| DELETE | 请求删除目标资源 | 方法名不保证服务器允许 |

**状态码**是服务器对请求结果的概括。1xx 表示处理中信息，2xx 表示成功，3xx 表示进一步动作或重定向，4xx 表示请求一侧的问题，5xx 表示服务器一侧的问题。

| 代码 | 常见含义 | 观察提示 |
| --- | --- | --- |
| 200 / 201 / 204 | 成功 / 已创建 / 成功但无响应正文 | 204 页面不应期待正文 |
| 301 / 302 / 304 | 永久重定向 / 临时重定向 / 缓存副本仍可用 | 304 响应不含消息体；客户端复用已有表示 |
| 400 / 401 / 403 | 请求有误 / 未认证或认证失败 / 服务器理解请求但拒绝访问 | 401 与 403 不可混同 |
| 404 / 405 | 资源未找到 / 方法不允许 | 先检查路径或方法 |
| 500 / 502 / 503 | 服务器内部错误 / 网关收到无效响应 / 服务暂不可用 | 看服务器或上游状态 |

例如在本地服务器中访问不存在的 `missing.html`，Network 通常显示 404。浏览器缓存可能保存资源以减少重复传输；刷新时可能复用缓存或向服务器发条件请求，收到 304 响应后，客户端复用已有表示。浏览器的“强制刷新”常用于排查旧资源，但具体缓存表现会因浏览器和响应头而异。可在 Network 查看 **Status、Size、Response Headers**，不要仅凭页面外观判断是否重新下载。参见 [RFC 9110：HTTP 语义](https://www.rfc-editor.org/rfc/rfc9110.html) 与 [MDN：HTTP 概览](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Overview)。

### 4.3 HTTPS 保护哪一段

**HTTPS** 是通过 **TLS** 保护传输的 HTTP。它帮助防止途中被窃听和篡改，并让浏览器依据证书验证所连接的主机身份。**可观察结果：**访问 HTTPS 网站时地址栏显示 `https://`，浏览器通常提供连接与证书信息；若证书不可信会给出警告。HTTPS 不保证网站内容没有欺诈、服务端没有漏洞，也不替代权限控制或输入验证。参见 [MDN：HTTPS](https://developer.mozilla.org/en-US/docs/Glossary/HTTPS)。

## 5. 浏览器如何把代码变成页面

本章跟踪 HTML 从网络响应到浏览器文档结构和页面显示的大致过程，并区分原始源代码与开发者工具中的实时页面结构。我们也会认识 Network 等面板如何帮助观察资源请求和定位问题。

### 5.1 HTML 文件、DOM 与页面

浏览器拿到 HTML 后解析内容，在内存中建立 **DOM**（文档对象模型）树。下面的**局部片段**可放在第 2 章 `study-notes/index.html` 的 `<body>` 中，替换原有的标题和段落：

```html
<h1>学习笔记</h1>
<p>先读 <a href="notes/html-core.html">HTML 笔记</a>。</p>
```

浏览器中可把关键节点理解为下列**简化文本树**（省略自动生成的其他节点）：

```text
body
├─ h1
│  └─ "学习笔记"
└─ p
   ├─ "先读 "
   ├─ a（href="notes/html-core.html"）
   │  └─ "HTML 笔记"
   └─ "。"
```

**可观察结果：**页面显示标题与一段带链接的文字；点击链接时，若目标文件尚未创建，本地服务器会返回 404。DOM 是浏览器当前的文档树，不是硬盘上的原始 HTML 文件。浏览器会按 HTML 解析规则修正某些不合法标记，后续脚本也可能改变 DOM，因此 **“查看网页源代码”（view-source）** 所见的原始响应与 **Elements** 面板中的当前 DOM 可能不同。能显示出来不代表 HTML 一定写对。参见 [MDN：浏览器如何加载网站](https://developer.mozilla.org/en-US/docs/Learn_web_development/Getting_started/Web_standards/How_browsers_load_websites)。

### 5.2 从 DOM 到屏幕，以及怎样观察

当 HTML 引用图片、外部样式表或脚本时，浏览器还可能发出额外资源请求。CSS 会被解析为用于计算样式的结构（常称 CSSOM）；浏览器结合文档与样式信息决定哪些内容参与显示，再进行布局、绘制。即使没有自己写 CSS，浏览器默认样式仍会让 `h1` 与普通段落看起来不同。本章只建立流程概念，不需要写 CSS 或 JavaScript。

打开页面后按 `F12`（或从浏览器菜单打开开发者工具），可以做一次最小观察：

| 面板 | 先看什么 | 能回答什么 |
| --- | --- | --- |
| Elements | `html`、`body`、`h1` 的层级 | 浏览器当前怎样组织 DOM？ |
| Network | 页面请求的 URL、Method、Status、Headers、Type、时间 | 哪个资源请求失败或较慢？ |
| Console | 错误与提示 | 浏览器报告了什么问题？ |
| Application | Cookie 与站点存储 | 浏览器保存了哪些站点数据？ |

**可观察结果：**刷新本地首页 `/` 时，Network 出现 Request URL 为 `http://127.0.0.1:8000/` 的 GET 请求；静态服务器返回 `index.html` 的内容。点击该请求可看状态码、请求头、响应头、`Content-Type` 和加载时间；明确访问 `/index.html` 时才会在请求 URL 中看到文件名。若要观察从加载开始的请求，先打开 Network 再刷新。第 6 章将解释 Application 中的状态数据。

## 6. Web 应用、数据、状态、安全与上线

本章把静态资源、动态请求、API、浏览器状态和服务器数据流放在同一张知识地图中，并建立同源策略、CORS 与常见安全风险的边界认识。最后简要梳理从代码、域名和托管到网站上线的关系。

### 6.1 从静态文件到业务数据

本地 `study-notes` 首页是静态资源：浏览器 GET `/`，服务器返回现成的 `index.html`。若将来增加“按关键词查找我的笔记”，一种常见流程是：

```text
浏览器 → 前端页面 → API 请求 → 后端处理 → 数据库查询
浏览器 ← 前端更新显示 ← API 响应 ← 后端整理结果 ← 数据库返回记录
```

**概念示例（不是本地静态服务器可运行的接口）：**未来的笔记应用可能请求 `GET /api/notes?tag=html`。后端查询后以 `Content-Type: application/json` 返回一份数据，响应体例如：

```text
{"notes":[{"id":12,"title":"HTML 入门","tag":"html"}],"total":1}
```

**可观察结果：**在有后端的应用里，Network 会分别列出 HTML 页面请求与 `/api/notes?tag=html` 数据请求；点开后者的 Response，可以看到这段 JSON，而不是一个已经排好版的网页。**API** 是程序之间约定的访问接口，不是数据库；JSON 是表达数据的格式，不是 API。服务器也可以直接把查询结果写成 HTML 响应。以后学习 JavaScript 时会接触 Ajax/Fetch 等异步请求方式，现在无需编写其代码。参见 [MDN：Web 如何工作](https://developer.mozilla.org/en-US/docs/Learn_web_development/Getting_started/Web_standards/How_the_web_works) 与 [MDN：JSON](https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/Scripting/JSON)。

### 6.2 HTTP 无状态与保存状态

HTTP 请求本身通常不记得上一次请求是谁；登录应用需要额外机制把相关请求关联起来。下表按常见用法区分几个容易混淆的词：

| 名称 | 主要保存位置 | 常见用途与边界 |
| --- | --- | --- |
| Cookie | 浏览器；按规则随匹配请求发送 | 保存一个会话标识等小数据；可设置 `Secure`、`HttpOnly`、`SameSite` 等属性 |
| 服务端 Session | 服务器 | 保存与会话标识关联的登录状态；具体实现由后端决定 |
| session cookie | 浏览器中的一种 Cookie | 通常没有持久到期时间，按浏览器会话管理；不是服务端 Session |
| `localStorage` | 浏览器、按来源保存 | 一般跨关闭和重开浏览器保留；不会像 Cookie 一样自动随请求发送 |
| `sessionStorage` | 浏览器、按来源和标签页会话保存 | 常随标签页会话结束而清除；不是服务端 Session，也不是 session cookie |

**概念示例（需要真正的登录后端，非本地静态服务器功能）：**用户登录成功后，服务器可通过响应头 `Set-Cookie: sid=abc123; HttpOnly; Secure; SameSite=Lax` 请浏览器保存一个示意会话标识。随后浏览器访问同站点的个人笔记页，符合 Cookie 规则时请求头会带 `Cookie: sid=abc123`；服务器据此查找自己保存的 Session，再决定是否返回该用户的笔记。示意值 `abc123` 不是可直接使用的安全会话标识。

**可观察结果：**真实应用中可在 Network 的响应头看到 `Set-Cookie`，在之后的请求头看到 `Cookie`，在 Application 中看到 Cookie；成功认证后个人笔记页会显示对应用户的数据。浏览器只能直接看到标识，不能从 Network 直接看到服务器内存或数据库里的 Session。第 2 章的纯静态首页没有登录数据也完全正常。不要把敏感凭据随意放入浏览器可读存储；具体身份验证方案属于后端学习范围。参见 [MDN：Cookie](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Cookies) 与 [MDN：Web Storage API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API)。

### 6.3 同源与跨源数据

第 3 章的同源由协议、主机、端口组成。**同源策略**限制不同来源的浏览器脚本互相读取数据；**CORS** 允许服务器通过响应头声明，哪些外部来源的浏览器页面可读取它的响应。

**概念示例（需要应用前端和 API，不在本文实现）：**页面位于 `https://notes.example.com/`，想读取 `https://api.example.com/notes` 的响应；两者主机不同，因此跨源。若 API 对该请求返回允许页面来源的响应头 `Access-Control-Allow-Origin: https://notes.example.com`，并且其他适用的 CORS 条件也满足，浏览器脚本才可读取响应；若缺少授权，Network 仍可能显示请求与响应，但浏览器脚本不能读取该响应数据，Console 通常会提示 CORS 错误。

**可观察结果：**在真实应用里可对比 Network 的响应头与 Console 的报错，判断是跨源读取被阻止，还是服务器本身返回了错误。CORS 不是身份认证，也不是对普通服务器到服务器请求的通用限制；允许跨源读取仍需后端检查身份和权限。参见 [MDN：CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CORS)。

### 6.4 安全边界

还要认识两类风险：**XSS** 是不可信内容被当成页面脚本执行，可能窃取或篡改用户可见信息；**CSRF** 是诱使已登录用户的浏览器向目标站点发出非本意请求。防护需要按应用设计处理，例如安全输出、会话 Cookie 属性和服务端校验；这些不是靠 HTML 页面单独完成。密码、API 密钥等秘密不能写进前端文件，因为用户可以下载或查看它们。浏览器的表单验证可被绕过，业务数据与权限必须由服务端再次验证。嵌入第三方 `iframe` 也要考虑内容来源、隐私与权限边界。参见 [MDN：Web 安全](https://developer.mozilla.org/en-US/docs/Web/Security)。

**概念示例：**若网站把陌生人写的笔记评论当成 HTML 代码插入页面，恶意内容可能引发 XSS；若另一网站诱使已登录用户触发“删除笔记”请求，可能形成 CSRF。**可观察结果：**前者可能让页面出现非预期内容或操作，后者可能让用户发现笔记被非本意地修改；这些现象都需要结合真实应用的服务器日志和浏览器请求排查，不能仅凭页面看起来正常就认定安全。

### 6.5 从代码到上线

将网站上线时，可以按“文件 → 托管 → 地址 → 安全连接”理解：先把可公开访问的文件部署到静态网站托管或 Web 服务器；配置域名的 DNS 记录指向服务；为 HTTPS 配置有效证书。**Git** 用于记录代码版本，GitHub 一类平台用于托管代码仓库；代码仓库、静态网站托管、域名、已部署可访问的网站是不同事物。开发环境用于日常修改，测试环境用于上线前验证，生产环境面向真实访问者。部署后还要检查真实链接、证书与资源路径。

**概念示例：**如果学习笔记站部署在 `https://notes.example.com/`，检查 `https://notes.example.com/notes/html-core.html` 是否能打开，再在 Network 查看它引用的图片是否返回 200；如果页面可打开但图片是 404，优先核对资源 URL、文件名大小写及上传目录。**可观察结果：**地址栏应显示预期域名和 HTTPS，页面与资源请求分别有状态码；代码已推送到仓库并不等于上述公开地址已经可访问。

可访问性让不同能力的用户理解和操作页面，SEO 帮助搜索引擎理解内容，响应式设计让不同屏幕获得合适呈现。性能先从可测量的项目开始：图片体积、请求数量、缓存和 Network 瀑布图。这个知识地图的下一步仍是把 HTML 结构写好，再学习 CSS、JavaScript 与部署实践。

## 7. HTML 语法基础

本章从元素、标签、内容和属性出发，解释嵌套关系、空元素、布尔属性、注释与字符实体。学完后应能读懂基础 HTML 片段，并理解浏览器容错显示不等于标记写法正确。

### 7.1 从标签读到元素

**用途：**HTML 用元素说明一段内容是什么。下面是**局部片段**，放进第 2 章 `study-notes/index.html` 的 `<body>` 内，替换原有段落即可观察：

```html
<p class="intro">先读 <strong>HTML 基础</strong>，再做笔记。</p>
```

**页面结果：**出现一段“先读 HTML 基础，再做笔记。”；`HTML 基础` 通常被浏览器加粗。`<p>` 是开始标签，`</p>` 是结束标签，中间的文字和 `strong` 元素是内容；这一整组构成一个 `p` 元素。`class="intro"` 是写在开始标签里的属性，`class` 是属性名，`intro` 是属性值。`p` 是 `strong` 的父元素，若同一个 `p` 中放两个 `strong`，它们互为兄弟元素。结束标签与开始标签要正确配对，通常用小写名称、双引号包住属性值，便于阅读和避免空格等字符引起歧义。

空元素没有内容和结束标签，例如 `<br>`、`<img>`、`<meta>`。在 HTML 中给空元素写成 `<br />`，末尾斜杠也不产生 XML 式“自闭合”机制；把**普通元素**写成 `<div />` 或 `<script />` 更不能代替结束标签。布尔属性则由**是否出现**决定真假。例如以下**局部片段**放在 `<body>` 内：

```html
<button disabled>暂不可用</button>
<button>可以操作</button>
```

**页面结果：**第一个按钮处于禁用状态，第二个可按。把第一个改成 `disabled="false"` 仍然禁用；要启用就去掉 `disabled`。这里仅观察浏览器原生状态，不需要脚本。可参见 [WHATWG：HTML 语法](https://html.spec.whatwg.org/dev/syntax.html) 与 [MDN：基础 HTML 语法](https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/Structuring_content/Basic_HTML_syntax)。

### 7.2 注释、特殊字符与空白

**用途：**注释给阅读源码的人看；字符引用让特殊字符作为文字出现。以下**局部片段**放在 `study-notes/index.html` 的 `<body>` 内：

```html
<!-- 这一行是给维护者看的，页面不显示 -->
<p>学习进度：HTML &amp; Web，下一步写出 &lt;p&gt; 元素。</p>
<p>第一步   阅读
第二步   实践</p>
<p>第一步<br>第二步</p>
```

**页面结果：**注释不显示；第一段显示“HTML & Web”和文字“`<p>`”；第二段源码中的多个普通空格会折叠，源码换行也不会自动变成页面换行，第三段才在“第一步”后换行。普通空格序列通常折叠为一个空格；但中文字符之间的源码换行在具体排版中可能不显示为空隙，不能保证第二段“阅读”和“第二步”之间一定看得见空格。源码中的 `&`、`<` 若要作为普通文字，应分别写成 `&amp;`、`&lt;`；`>` 可写成 `&gt;`，引号需要出现在同种引号包围的属性值内时可写 `&quot;`。`<br>` 表示内容本身确实需要换行，例如诗句或地址；段落间距应留给以后学习的 CSS，不靠连续 `<br>` 或 `&nbsp;` 堆砌。

### 7.3 合法嵌套看内容模型

**问题：**“块级元素不能放进内联元素”是旧式简化口诀，不能准确判断现代 HTML。每种元素都有自己的**内容模型**：`p` 接受短语内容，`div` 属于流内容，不能放在 `p` 中；而现代 `a` 可以包住合适的流内容，例如整段笔记摘要，但它里面不能再放链接、按钮等交互内容。先看父元素允许什么子内容，再判断具体约束，而不是只看默认是否换行。参见 [WHATWG：内容模型](https://html.spec.whatwg.org/dev/dom.html#content-models)。

下面是**故意写错、不要复制进正文的反例**；可临时放在 `<body>` 中，再对照“查看网页源代码”和 Elements 面板：

```text
<p>开头<div>笔记卡片</div>结尾</p>
```

**页面结果：**浏览器仍可能显示全部文字，但解析到 `<div>` 前会自动结束 `p`；Elements 中不会出现你以为的“`p` 包着 `div`”结构，末尾的 `</p>` 还可能形成空段落。正确写法是并列放置：

```html
<p>开头</p>
<div>笔记卡片</div>
<p>结尾</p>
```

这段**正确片段**可放在 `<body>` 内，页面上会依次显示两段文字与中间内容。还有几种常见错误同样需要避免：

| 错误写法（示意） | 原因与改法 |
| --- | --- |
| `<a href="a.html"><a href="b.html">B</a></a>` | 链接不能嵌套链接；改成两个并列链接。 |
| `<a href="a.html"><button>打开</button></a>` 或 `<button><a href="a.html">打开</a></button>` | 链接、按钮内不放交互后代；导航用链接，操作用按钮。 |
| `<ul>一项<li>另一项</li></ul>` | 列表项内容应放入 `li`；改为两个 `li`。 |
| `<form><form>...</form></form>` | 表单不能嵌套；拆成两个独立表单。 |

浏览器对错误标记有容错规则，可能自动结束、移动或忽略标签；因此 HTML 源码、解析出的 DOM、屏幕显示三者不一定一一对应。能显示不等于结构正确，写完可用第 17 章介绍的验证工具检查。

## 8. 完整文档骨架与 head

本章拆解一个完整 HTML 文档中的 doctype、根元素、head 与 body，说明语言、字符编码、视口和标题等基础信息放在哪里。我们还会区分页面元信息与页面主体内容。

### 8.1 一份可以直接打开的完整文档

**用途：**每个独立 HTML 页面都需要清楚的文档骨架。将下面内容完整保存为 `study-notes/index.html`，可以直接双击打开，也可按第 2 章通过本地服务器访问；示例的 favicon 使用内嵌数据 URL，因此无需额外图片文件。

```html
<!doctype html>
<html lang="zh-CN">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>首页｜学习笔记</title>
  <meta name="description" content="HTML 与 Web 开发学习笔记首页">
  <link rel="icon" href="data:image/svg+xml,%3Csvg%20xmlns='http://www.w3.org/2000/svg'%20viewBox='0%200%2032%2032'%3E%3Ctext%20x='3'%20y='25'%20font-size='26'%3EH%3C/text%3E%3C/svg%3E">
</head>
<body>
  <h1>学习笔记</h1>
  <p>从 HTML 开始，记录 Web 学习过程。</p>
</body>
</html>
```

**页面结果：**标签页标题为“首页｜学习笔记”，正文显示“学习笔记”和一段说明文字；浏览器可能在标签页显示字母 H 图标。`head` 中的信息主要供浏览器、搜索引擎等使用，不是页面正文；`body` 中的标题和段落才是读者在页面内看到的内容。图标的具体显示会受浏览器缓存和界面影响。

| 行或元素 | 职责 | 读者能看到什么 |
| --- | --- | --- |
| `<!doctype html>` | 告诉浏览器以标准模式解析；不是旧式“HTML 版本号” | 页面本身不显示 |
| `<html lang="zh-CN">` | 根元素并标明主要语言，帮助语音朗读等工具 | 不作为文字显示 |
| `<head>`、`<body>` | 分放元信息与页面内容 | 正文来自 `body` |
| `<meta charset="utf-8">` | 告诉浏览器用 UTF-8 解读文本；宜靠近 `head` 开头 | 正常显示中文 |
| viewport 元信息 | 让移动设备按设备宽度布局并采用初始缩放 | 手机视口符合设备宽度；不替代响应式设计 |
| `<title>`、description | 前者给文档标题，后者概述页面内容 | 标题见于标签页；描述通常不在正文显示 |
| `<link rel="icon">` | 指定页面图标；此处数据 URL 内嵌简短 SVG | 图标可能出现在标签页 |

`title` 是文档标题，常用于浏览器标签页，也可能被搜索服务参考；`h1` 是正文的主要标题，应让读者理解当前页面。两者可以相近，但职责不同。`head` 不是 `header`：后者是第 15 章要讲的页面或区块的介绍性内容，写在 `body` 中。

### 8.2 外部资源与代码的位置

`<link>` 常放在 `head` 引入网站图标或外部样式表；`<style>` 通常放在 `head`，用于放置文档内的 CSS 规则；`<script>` 用来引用或编写 JavaScript，可放在 `head`，也可放在 `body` 中（常见于结束标签 `</body>` 前），具体位置还需结合加载方式选择。这里先认职责，不写 CSS 规则或 JavaScript 逻辑。普通页面内容应留在 `body`，不能把可见段落塞进 `head`。日后把笔记站扩成多页时，每个 `.html` 文件都需要自己的骨架、语言、编码和标题。可参见 [WHATWG：文档元数据](https://html.spec.whatwg.org/multipage/semantics.html#the-head-element)。

## 9. 文本内容与语义

本章围绕标题、段落、强调、引用、时间和代码等内容场景，介绍常见文本元素表达的含义。重点是让标记反映内容结构，而不是只关注浏览器默认呈现效果。

### 9.1 标题、段落与真正的换行

**用途：**用 `h1`～`h6` 表示标题层级，用 `p` 表示一个段落。以下**局部片段**可放在 `study-notes/notes/html-core.html` 的 `<body>` 内；如果该文件还未建立，也可暂放第 8 章 `index.html` 的 `<body>` 内：

```html
<h1>HTML 学习笔记</h1>
<h2 id="syntax">语法基础</h2>
<p>元素由标记和内容组成。写笔记时，先把内容分成有意义的段落。</p>
<h3>常见疑问</h3>
<p>问：一行文字就是一个段落吗？答：不一定，段落按意思划分。</p>
<hr>
<p>今日任务：读一节、写一个示例。</p>
```

**页面结果：**出现一个大标题、两个依次降低层级的小标题、段落和一道内容分隔线。`h1`～`h6` 是从高到低的六级标题，不应为了字体大小跳级或把普通文字当标题；`hr` 表示主题转换，不是装饰性画线。真正属于同一段的诗句、地址等可在该段内用 `<br>` 表示换行，普通段落则分别用 `p`。屏幕阅读器也会利用标题层级理解文档结构。

### 9.2 语义相近，含义不同

**用途：**有些元素默认外观相似，但表达的含义不同。以下**局部片段**放在 `<body>` 内：

```html
<p><strong>注意：</strong>保存文件后再刷新。<b>HTML</b> 是本段的关键词。</p>
<p>请<em>先</em>读示例；<i>localhost</i> 是文中的外文术语。</p>
```

**页面结果：**`strong`、`b` 通常都较粗，`em`、`i` 通常都倾斜，但默认外观不能代替语义。`strong` 表示重要性，`em` 表示语气上的强调；`b` 只把词语从周围内容中凸显出来而不增加重要性，`i` 可标识术语、外文短语等不同语气的文字。它们都不是“已废弃标签”；选哪个要看句子的意思，而非只想加粗或斜体。

| 元素 | 合适的内容含义 | 在笔记中的用法 |
| --- | --- | --- |
| `mark` | 当前上下文中特别相关的文字 | 标记搜索词 |
| `small` | 附注、版权等旁注信息 | 页末版权说明 |
| `del` / `ins` | 被删除 / 新增的修订内容 | 记录笔记修改 |
| `sub` / `sup` | 下标 / 上标 | 水的化学式 H₂O、平方 x² |

例如以下**局部片段**放在 `<body>` 内，就能看到标注、修订及上下标：

```html
<p>搜索结果：<mark>HTML</mark> 入门笔记。</p>
<p>建议每天读 <del>三章</del><ins>一章</ins>。</p>
<p>水的化学式是 H<sub>2</sub>O；平方写作 x<sup>2</sup>。</p>
<small>笔记整理于 2026 年。</small>
```

### 9.3 引用、时间与联系信息

**用途：**引用别人说的话、标注作品名称或机器可读的日期时，用对应元素。下列**局部片段**放在 `study-notes/notes/html-core.html` 的 `<body>` 内：

```html
<blockquote>
  <p>阅读一份教程时，可以边看示例边在浏览器中验证。</p>
</blockquote>
<p>我把这句学习建议写在 <cite>HTML 学习摘记</cite> 中；朋友说：<q>先做一个能打开的页面。</q></p>
<p><abbr title="HyperText Markup Language">HTML</abbr> 是超文本标记语言。</p>
<p>复习日期：<time datetime="2026-09-26">2026 年 9 月 26 日</time>。</p>
<address>本页笔记维护者：<a href="mailto:notes@example.com">笔记邮箱</a></address>
```

**页面结果：**块引用通常另起一段；行内引用可能由浏览器自动加引号；缩写与日期显示为普通可读文字，邮箱成为可点击链接。`blockquote` 用于较长的独立引用，`q` 用于短的行内引用；`cite` 标记作品标题，不是用来标注作者姓名。引用有真实在线来源时，可在 `blockquote` 上加 `cite="来源URL"` 记录它；浏览器通常不会自动显示该属性，正文仍应给读者可见的来源链接。上面的中文句子只是教学自拟示意。`abbr` 的 `title` 可给出全称，但重要解释也应在正文提供，不依赖悬停提示。`time` 的 `datetime` 提供机器可读日期；`address` 表示本篇内容或所在页面的联系信息，不是所有邮寄地址的通用容器。

### 9.4 程序片段与原样排版

**用途：**技术笔记中的代码、按键、程序输出和变量各有标记。下面的**局部片段**放在 `<body>` 内：

```html
<p>按 <kbd>Ctrl</kbd> + <kbd>S</kbd> 保存；源码中使用 <code>&lt;h1&gt;</code>。</p>
<pre><code>&lt;h1&gt;学习笔记&lt;/h1&gt;
&lt;p&gt;先保存，再刷新。&lt;/p&gt;</code></pre>
<p>终端若显示 <samp>Serving HTTP on 127.0.0.1</samp>，说明本地服务已启动。</p>
<p>把文件名记作变量 <var>filename</var>。</p>
```

**页面结果：**行内的 `<h1>` 以文字而非标题出现；`pre` 中的换行与空格得到保留。`code` 表示代码，`pre` 保留预格式化文本，`kbd` 表示用户输入，`samp` 表示程序输出，`var` 表示变量。示例中的终端输出是说明性片段，具体运行信息以本机终端为准。可参见 [WHATWG：文本级语义](https://html.spec.whatwg.org/multipage/text-level-semantics.html)。

## 10. 列表、容器与内容分组

本章比较无序列表、有序列表和描述列表的适用场景，并说明列表如何嵌套组织。随后认识 div 与 span 这类通用容器的边界，练习先表达内容含义再选择元素。

### 10.1 顺序是否重要，决定列表类型

**用途：**并列项目用 `ul`，必须按步骤走的项目用 `ol`；每一项由 `li` 包住。以下**局部片段**可放在 `study-notes/notes/html-core.html` 的 `<body>` 内：

```html
<h2>本周要学什么</h2>
<ul>
  <li>HTML 文档结构</li>
  <li>常见内容元素
    <ul>
      <li>文本</li>
      <li>链接</li>
    </ul>
  </li>
</ul>
<h2>打开笔记的步骤</h2>
<ol>
  <li>保存 HTML 文件。</li>
  <li>启动本地服务器。</li>
  <li>在浏览器输入页面 URL。</li>
</ol>
```

**页面结果：**第一个列表默认显示项目符号，第二项下有缩进的子列表；步骤列表默认显示 1、2、3。嵌套列表放在所属的 `li` 内，而不是放在两个 `li` 之间。`ol` 的 `start="3"` 可从第 3 项开始，`reversed` 可倒序计数，`type="A"` 可用字母编号；这些属性表达编号方式，不能用来代替列表本身的顺序含义。`ul`、`ol` 的直接列表项应是 `li`，不要把项目文字裸放在列表容器内。

### 10.2 术语与解释用描述列表

**用途：**术语与解释、姓名与定义等成组关系适合用 `dl`，其中 `dt` 是名称，`dd` 是说明。下面的**局部片段**放在 `<body>` 内：

```html
<dl>
  <dt>HTML</dt>
  <dd>描述网页内容和结构的标记语言。</dd>
  <dt>URL</dt>
  <dd>用于定位网络资源的地址。</dd>
</dl>
```

**页面结果：**浏览器通常把说明缩进，形成“名称—解释”的两组内容。一个名称也可对应多个解释，或多个名称对应一组解释；它不是单纯为了做缩进而选的标签。参见 [WHATWG：列表元素](https://html.spec.whatwg.org/multipage/grouping-content.html#the-ul-element)。

### 10.3 通用容器没有自带主题含义

**用途：**确实需要把内容分组、又没有更贴切的语义元素时，可用 `div`；句子中的一小段可用 `span`。以下**局部片段**放在 `study-notes/index.html` 的 `<body>` 内：

```html
<div>
  <h2>学习提醒</h2>
  <p>今天先读 <span lang="en">HTML</span> 笔记。</p>
</div>
```

**页面结果：**出现标题和段落；`span` 本身通常不会额外改变显示，但这里的 `lang="en"` 标明英文片段语言，可帮助辅助技术正确发音。`div` 和 `span` 都不自动说明“这是导航”“这是文章”之类的内容含义；第 15 章会介绍更合适的语义容器。选标签先看内容和关系，再考虑以后用 CSS 调整外观。

## 11. 超链接与路径

本章介绍页面跳转、页内锚点、邮件和电话链接，并用目录示例解释相对路径、根相对路径与绝对 URL。我们也会说明链接目标和路径错误如何影响访问结果。

### 11.1 先把目录与当前页面固定下来

**用途：**相对路径要从当前 HTML 文件的位置算起。第 18 章的完整案例采用下面的目录；本节先依此推算，尚未创建的文件请在完成第 18 章后再点击验证：

```text
study-notes/
├── index.html
├── contact.html
├── notes/
│   └── html-core.html
└── images/
    └── html-notes.svg
```

假设在 `study-notes` 目录运行第 2 章的服务器，并打开 `http://127.0.0.1:8000/notes/html-core.html`，下表中的值是放在**该页 `href` 或 `src` 属性里的局部 URL**：

| 写法 | 从笔记页解析到 | 说明 |
| --- | --- | --- |
| `./html-core.html` | `/notes/html-core.html` | `./` 是当前目录；也可省略它。 |
| `../index.html` | `/index.html` | `../` 回到上一级目录。 |
| `../images/html-notes.svg` | `/images/html-notes.svg` | 先回上一级，再进 `images`。 |
| `/contact.html` | `/contact.html` | 从当前 origin 根目录开始。 |
| `https://developer.mozilla.org/` | 指定的外部网站 | 绝对 URL 自带协议和主机。 |

**可观察结果：**完成目录中的文件后，从笔记页点击 `../index.html` 会回首页；写成 `./index.html` 会找 `/notes/index.html`，因目录中没有它，本地服务器返回 404。`/contact.html` 只在当前站点恰好部署于 origin 根目录时指向这个文件；如果整站部署在 `https://example.com/study-notes/` 下，它会跳到 `https://example.com/contact.html`，应改用 `../contact.html` 这类相对路径。这里的 `/` 不是 Windows 磁盘根目录；URL 使用正斜杠 `/`，不能把 `D:\study-notes\notes\html-core.html` 或反斜杠写进网页链接。上线后的服务器还可能区分 `Notes` 与 `notes` 的大小写。可参见 [MDN：创建超链接](https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/Structuring_content/Creating_links)。

### 11.2 页面导航、片段与可理解的文字

**用途：**链接负责把用户带到另一个位置。下面的**局部片段**放在 `study-notes/notes/html-core.html` 的 `<body>` 内；其中 `syntax` 目标由同页标题的 `id` 提供，若未复制第 9 章的标题，请一并放入：

```html
<h2 id="syntax">语法基础</h2>
<p><a href="#syntax">跳到本页的语法基础</a></p>
<p><a href="../index.html">返回学习笔记首页</a></p>
<p><a href="../contact.html">打开联系页面</a></p>
```

**页面结果：**点击第一条，浏览器跳到对应标题，并在地址栏末尾出现 `#syntax`；片段只供浏览器定位，不随 HTTP 请求发送。后两条分别打开根目录下的首页和联系页。`id` 在同一页面内应唯一。链接文字应说明目标，例如“查看 HTML 语法笔记”，避免多处只写“点击这里”，这样即使单独浏览链接列表也能判断去向。若已在同一页面放过第 9 章 `id="syntax"` 的标题，就只复制三段链接，避免重复 `id`。

### 11.3 特殊目标、新窗口与下载

下面的**局部片段**放在 `study-notes/notes/html-core.html` 的 `<body>` 内；邮箱与号码是示意值，下载目标在第 18 章建立 `images/html-notes.svg` 后才存在：

```html
<p><a href="mailto:notes@example.com">给笔记维护者发邮件</a></p>
<p><a href="tel:+8613800000000">拨打示例电话</a></p>
<p><a href="../images/html-notes.svg" download="html-notes.svg">下载 HTML 笔记图</a></p>
<p><a href="https://developer.mozilla.org/en-US/docs/Web/HTML" target="_blank" rel="noopener noreferrer">在新标签页阅读 MDN HTML 文档</a></p>
```

**页面结果：**邮件和电话链接会交给设备上已配置的相应应用；无对应应用时可能没有预期动作。下载链接请求同源 SVG，浏览器通常按给定文件名保存，具体行为会受浏览器设置影响。`target="_blank"` 要求在新浏览上下文打开；`rel="noopener"` 防止新页面通过 `window.opener` 操作原页面，`noreferrer` 还要求不发送 `Referer` 来源信息（并包含 `noopener` 效果），两者含义并不相同。`download` 对跨源 URL 的处理有限制，不要把它当成强制下载任何外站资源的方法。

链接 `<a href>` 用于**导航到目标**；按钮 `<button>` 用于**在当前页面执行操作**，例如提交表单。写反会让键盘操作、辅助技术提示和浏览器行为都不符合预期。若链接打不开，在开发者工具先打开 Network 再点击：找到失败的请求，看 **Request URL** 是否把 `../` 算错，再看 **Status** 是否为 404，并对照实际文件名、大小写与服务器启动目录。

## 12. 图片、响应式图片与嵌入内容

本章讲解图片替代文本、尺寸和延迟加载等常见属性，再概览音视频、响应式图片与 iframe 的使用边界。重点是让非文本内容有合适的说明，并能理解外部嵌入内容的影响。

### 12.1 图片既要能看，也要能被理解

**用途：**在第 11 章的 `study-notes` 目录中，第 18 章会创建 `images/html-notes.svg`。完成该文件后，把下面的**局部片段**放进 `study-notes/notes/html-core.html` 的 `<body>`；现在复制也可以，但图片文件未创建前会显示加载失败：

```html
<figure>
  <img src="../images/html-notes.svg"
       alt="study-notes 目录示意图：index.html、contact.html、notes/html-core.html 和 images/html-notes.svg 四个文件"
       width="480" height="260" decoding="async">
  <figcaption>图 1：学习笔记站的四文件目录。</figcaption>
</figure>
```

**页面结果：**图片加载成功时，图像下方出现图注，图中列出学习笔记站的四个文件路径；路径错或图片未下载成功时，替代文本仍能交代这张目录图的信息。`src` 指向资源，`alt` 是图片无法显示及辅助技术阅读时的文字替代，`figcaption` 是所有读者可见的图注，两者不能互相代替。`width`、`height` 应写与实际图片比例相符的尺寸，有助于浏览器提前预留位置；第 18 章的 SVG 是 480×260。`decoding="async"` 是解码提示，不保证某个固定显示时刻。`figure` 把图和说明组成一体，适合被正文引用的内容图。

**装饰图**若不提供独立信息，应写 `alt=""`，让读屏软件跳过它；不要省略 `alt`，也不要把“图片”两字机械写入每个替代文本。`title` 属性弹出的提示也不能代替 `alt`。首屏关键图片通常直接加载；较靠下且非关键的图片可以在 `<img>` 上加 `loading="lazy"`，浏览器可能延后请求，以减少初始加载量。以上属性并不压缩原始文件，图片体积仍需留意。参见 [MDN：HTML 图片](https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/Structuring_content/HTML_images)。

### 12.2 同一内容的不同尺寸，或不同裁切

**用途：**`srcset` 提供候选图片，`sizes` 告诉浏览器图片大约占多宽，由浏览器综合视口、屏幕像素密度等选择资源。下面是放在 `study-notes/notes/html-core.html` 的 `<body>` 中的**局部片段**。请先自行准备同一张照片的 `images/desk-480.jpg`（实际宽 480 像素）和 `images/desk-960.jpg`（实际宽 960 像素）；这两张练习照片不属于第 18 章的完整案例：

```html
<img src="../images/desk-960.jpg"
     srcset="../images/desk-480.jpg 480w, ../images/desk-960.jpg 960w"
     sizes="480px"
     alt="摊开的 HTML 学习笔记与键盘"
     width="480" height="270">
```

**页面结果：**两张文件都存在时，浏览器显示同一内容的照片；不同设备可能请求不同文件，可在 Network 面板核对实际请求，不能只凭窗口宽度断定所选文件。`480w`、`960w` 必须对应文件的真实像素宽度；本例假定图片显示宽度为 480 CSS 像素，因此 `sizes="480px"` 与 `width="480"` 一致，`height="270"` 也保持照片的 16∶9 比例。`sizes` 描述预计显示宽度，不是要求浏览器把图片强制缩放到该宽度；若将来用布局规则让窄屏显示为视口宽度，可再改用类似 `sizes="(max-width: 600px) 100vw, 480px"` 的条件提示，并确保实际布局与提示一致。`src` 提供默认资源。

**用途：**如果窄屏需要展示照片的特写而宽屏展示全景，使用 `picture` 的 `source` 按条件换图。下面同样是放在该笔记页 `<body>` 的**局部片段**，还需自行准备 `images/desk-close.jpg` 和 `images/desk-wide.jpg`：

```html
<picture>
  <source media="(max-width: 600px)" srcset="../images/desk-close.jpg">
  <img src="../images/desk-wide.jpg" alt="桌面上摊开的 HTML 学习笔记" width="960" height="540">
</picture>
```

**页面结果：**符合 `media` 条件时浏览器可选特写，否则回退到 `img` 的全景；两张图应表达相同的主要信息，替代文本才准确。`source` 在这里提供候选资源，真正显示并提供 `alt` 的仍是 `img`。若特写和全景的长宽比不同，需要结合实际素材设置合适尺寸，避免尺寸提示与所选图片不符。以上只演示 HTML 的选图能力，不要求读者现在学习 CSS 媒体查询。参见 [MDN：响应式图片](https://developer.mozilla.org/en-US/docs/Web/HTML/Guides/Responsive_images)。

### 12.3 音视频与外部页面

**用途：**`audio`、`video` 可以让浏览器提供原生播放控件；`source` 为不同编码格式提供备选。下列**局部片段**放在 `study-notes/notes/html-core.html` 的 `<body>` 中，但须自行添加 `media/intro.mp3`、`media/intro.ogg`、`media/demo.mp4`、`media/demo.webm` 和 `media/demo-zh.vtt`，否则不能播放；这些文件也不属于第 18 章的完整案例：

```html
<audio controls preload="metadata">
  <source src="../media/intro.mp3" type="audio/mpeg">
  <source src="../media/intro.ogg" type="audio/ogg">
  浏览器不支持此音频；请阅读页面上的文字介绍。
</audio>

<video controls width="640" height="360" preload="metadata" poster="../images/desk-wide.jpg">
  <source src="../media/demo.mp4" type="video/mp4">
  <source src="../media/demo.webm" type="video/webm">
  <track kind="captions" src="../media/demo-zh.vtt" srclang="zh" label="中文字幕" default>
  浏览器不支持此视频；请阅读页面上的文字说明。
</video>
```

**页面结果：**资源存在且格式受支持时出现播放控件；`poster` 是视频播放前的封面图，缺失时不影响视频标签的结构；字幕文件有效时可在播放器中选择字幕。`controls` 让用户自行播放，`preload="metadata"` 只提示浏览器优先读取元数据，实际加载仍由浏览器决定；不要为有声媒体默认自动播放。标签中的回退文本主要给不支持该元素的浏览器，**不能代替**内容的字幕或文字稿。`track` 的 VTT 字幕也必须与视频内容和时间对应，音频则宜另给文字稿。

**用途：**`iframe` 在当前页嵌入另一份网页文档。下面的**局部片段**放入同一笔记页的 `<body>`，不用准备额外文件：

```html
<iframe title="章节提示示例" loading="lazy" sandbox
        srcdoc="<p>先学结构，再练路径。</p>"></iframe>
```

**页面结果：**页面里出现独立的小文档，显示一句提示。`title` 说明这个框架的内容，方便辅助技术识别；`loading="lazy"` 可延后加载离屏框架；空的 `sandbox` 对嵌入文档施加较严格限制。嵌入第三方站点时还要考虑对方是否允许被嵌入、隐私和安全影响；确有需要时再按最小权限逐项开放 sandbox 能力，不要因为页面显示不出来就一次性放开全部权限。这个内嵌示例只演示框架结构，不代表第三方页面一定允许嵌入。

SVG 是可缩放的矢量图，像第 18 章的 `html-notes.svg` 一样可以直接作为 `img` 的图片资源。`canvas` 是一块可供脚本绘图的画布；只写 `<canvas>` 不会自动生成图表或交互，本篇不编写 JavaScript 绘图。图片、音视频、框架都需要考虑文字替代和实际资源是否可访问。

## 13. 表格

本章介绍如何用表格表达真正的二维数据，以及表题、行列标题和数据单元格之间的关系。示例会涉及表头分组和单元格合并，并强调表格不是页面布局工具。

### 13.1 一张能读懂行列关系的数据表

**用途：**第 18 章笔记页会有更小的表格；这里单独练习有分组的“每周笔记篇数”。下面是**局部片段**，放在 `study-notes/notes/html-core.html` 的 `<body>` 内，无需 CSS 或其他文件：

```html
<table>
  <caption>两周 HTML 笔记计划（单位：篇）</caption>
  <colgroup>
    <col>
    <col span="2">
  </colgroup>
  <thead>
    <tr>
      <th scope="col">周次</th>
      <th scope="col">主题</th>
      <th scope="col">篇数</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th scope="rowgroup" rowspan="2">第一周</th>
      <th scope="row">文档结构</th>
      <td>2</td>
    </tr>
    <tr>
      <th scope="row">链接与图片</th>
      <td>1</td>
    </tr>
  </tbody>
  <tbody>
    <tr>
      <th scope="rowgroup" rowspan="2">第二周</th>
      <th scope="row">表格</th>
      <td>1</td>
    </tr>
    <tr>
      <th scope="row">表单</th>
      <td>1</td>
    </tr>
  </tbody>
  <tfoot>
    <tr>
      <th scope="row" colspan="2">合计</th>
      <td>5</td>
    </tr>
  </tfoot>
</table>
```

**页面结果：**浏览器排出三列、四条主题记录，周次各跨两行，底部合计为 5；浏览器默认外观可能没有明显边框。`caption` 是整张表的标题，`thead`、`tbody`、`tfoot` 区分表头、数据和汇总，`tr` 表示一行，`td` 放普通数据。`th` 表示标题单元格：`scope="col"` 指明列表头，`scope="row"` 指明行标题，跨两行的周次用 `scope="rowgroup"` 和 `rowspan="2"` 说明关联；`colspan="2"` 让“合计”覆盖前两列。`colgroup`/`col` 标明列结构，这里不设置外观，因此通常不会有额外可见效果。简单表用 `scope` 往往就足以让表头和数据的关系更清楚；复杂表可继续学习更细的关联方法。参见 [MDN：HTML 表格基础](https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/Structuring_content/HTML_table_basics)。

表格适合“行与列交叉才能解释清楚”的数据，例如课程安排、价格清单；页面的导航、正文和侧栏是文档结构，应使用第 15 章的语义元素，**不要用表格给页面排版**。

## 14. 表单与浏览器原生验证

本章说明表单控件如何组成可提交的数据，认识 label、name、常见 input 类型和浏览器提供的基础约束验证。我们会观察 GET 查询参数，并明确静态服务器和浏览器验证各自的能力边界。

### 14.1 表单把控件的值送到哪里

**用途：**`form` 为一组控件定义提交目标和方法。`action` 是目标 URL，`method` 常见为 `get` 和 `post`；`autocomplete` 告诉浏览器是否可按自身策略提供自动填充。每个要提交的控件通常需要 `name`，浏览器把它和当前值组成名值对。`label for="..."` 与控件的唯一 `id` 对应，点标签文字也可聚焦或切换控件；`placeholder` 只是输入提示，不能代替始终可见的标签。

下面的完整示例保存为 `study-notes/contact-demo.html`。它是一份**独立的练习页面**，不会替代第 18 章的 `contact.html`。在 `study-notes` 目录运行第 2 章的 `python -m http.server 8000 --bind 127.0.0.1`，打开 `http://127.0.0.1:8000/contact-demo.html`，无需后端就能观察 GET 查询参数：

```html
<!doctype html>
<html lang="zh-CN">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>笔记筛选练习</title>
</head>
<body>
  <h1>筛选学习笔记</h1>
  <form action="contact-demo.html" method="get" autocomplete="on">
    <p>
      <label for="keyword">关键词：</label>
      <input id="keyword" name="keyword" type="text" required minlength="2" maxlength="20">
    </p>
    <fieldset>
      <legend>笔记类型（单选）</legend>
      <label><input type="radio" name="category" value="html" checked>HTML</label>
      <label><input type="radio" name="category" value="web">Web</label>
    </fieldset>
    <fieldset>
      <legend>包含内容（可多选）</legend>
      <label><input type="checkbox" name="part" value="example">代码示例</label>
      <label><input type="checkbox" name="part" value="diagram">示意图</label>
    </fieldset>
    <p>
      <label for="order">排序：</label>
      <select id="order" name="order">
        <optgroup label="常用排序">
          <option value="new" selected>最近更新</option>
          <option value="old">最早更新</option>
        </optgroup>
      </select>
    </p>
    <p>
      <label for="topic">主题建议：</label>
      <input id="topic" name="topic" list="topics">
      <datalist id="topics">
        <option value="链接">
        <option value="表单">
      </datalist>
    </p>
    <button type="submit">查看请求参数</button>
  </form>
</body>
</html>
```

**页面结果：**不填关键词或只填一个字符时，浏览器通常阻止提交并提示修正。输入“网页”、勾选“代码示例”后提交，地址栏出现类似 `contact-demo.html?keyword=%E7%BD%91%E9%A1%B5&category=html&part=example&order=new&topic=` 的查询串；中文会被 URL 编码，参数顺序和空值的表现可依浏览器与输入情况观察。服务器仍返回同一份静态 HTML，页面列表不会自动筛选，输入框也不会自动回填。要按参数筛选数据，需要后端程序或后续学习的脚本逻辑；这里练习的是浏览器怎样构造 GET 请求。GET 参数在 URL 中可见、可被收藏和记录，不适合提交密码等敏感信息。

`fieldset` 与 `legend` 给一组选项命名；相同 `name` 的单选按钮只会选中一个，多个同名复选框可以分别提交同名参数。`checked` 设定单选或复选的初始选中状态，`selected` 设定 `option` 的初始选中状态。`select` 是给定选项中的选择，`datalist` 只是为可编辑输入框提供建议，用户仍可输入列表之外的文字。提交的是 `value`，不是屏幕上看到的选项文字。`button` 在表单内即使不写 `type`，默认也会提交；不负责提交的按钮应明确写 `type="button"`。

### 14.2 常用控件与成功提交的条件

| 元素或类型 | 适用输入 | 关键提醒 |
| --- | --- | --- |
| `text`、`password`、`email` | 普通文本、口令、邮箱 | `password` 只遮住屏幕显示，不会自动加密传输；传输安全依赖 HTTPS。 |
| `number`、`date` | 真正的数值、日期 | 手机号、学号、邮编可能有前导零或非算术意义，不用 `number`。 |
| `radio`、`checkbox` | 单选、多选 | 未选中的控件通常不提交。 |
| `file`、`hidden` | 文件、页面附带值 | `hidden` 不是保密措施，用户仍可查看或修改。 |
| `submit`、`button` | 提交或其他操作 | `<input type="submit" value="提交">` 也是提交按钮。 |
| `textarea` | 多行文本 | 初始内容写在开始和结束标签之间。 |

**用途：**再练习控件状态。下面的**局部片段**放在 `contact-demo.html` 的 `<form>` 内、提交按钮前；这只是观察片段，复制后重新打开页面再提交：

```html
<p><label for="memo">备注：</label><textarea id="memo" name="memo" rows="3" cols="30">待整理</textarea></p>
<p><label for="author">作者：</label><input id="author" name="author" value="小明" readonly></p>
<p><label for="draft">草稿编号：</label><input id="draft" name="draft" value="7" disabled></p>
<input type="hidden" name="source" value="notes">
```

**页面结果：**查询串一般会有 `memo`、`author` 和 `source`，没有 `draft`。只读控件通常仍会提交；禁用控件不可操作且不提交。缺少 `name` 的输入控件，以及没有选中的单选或复选控件，一般也不会成为成功提交的名值对。`disabled="false"` 仍是禁用，因为布尔属性只要出现就为真；要恢复控件，应删掉整个 `disabled` 属性。隐藏字段的值同样由浏览器发送，不可信任它代表真实身份或权限。

### 14.3 原生验证和文件上传的边界

**用途：**下面的**局部片段**可以放在另一份实验页面的 `<form>` 内；它展示 HTML 提供的约束，提交目标需由实际后端提供，此处不对本地静态服务器提交：

```html
<p><label for="email">邮箱：</label><input id="email" name="email" type="email" required multiple></p>
<p><label for="count">篇数：</label><input id="count" name="count" type="number" min="1" max="10" step="1"></p>
<p><label for="phone">手机号（本练习可选；填写时限 11 位数字）：</label><input id="phone" name="phone" type="tel" inputmode="numeric" pattern="[0-9]{11}"></p>
<p><label for="photo">封面：</label><input id="photo" name="photo" type="file" accept="image/png,image/jpeg"></p>
```

**页面结果：**`required` 要求填写；`type="email"` 检查邮箱格式，`multiple` 允许输入多个以逗号分隔的邮箱；`min`、`max`、`step` 约束数值范围与步长，`minlength`、`maxlength` 约束文本长度，`pattern` 可要求匹配指定格式。手机号的 11 位数字规则只是本练习的可选输入约束，不是所有地区电话号码的通用验证规则。浏览器的提示文字和出现时机可能不同。`type="tel"` 适合电话号码；`inputmode="numeric"` 只是虚拟键盘提示，`accept` 只是文件选择提示，都不能阻止用户绕过检查或上传其他内容。前面的 GET 示例展示基本操作，本片段是属性速查，不提供文件上传的可用后台。

提交时，`method="get"` 把参数放入 URL 查询串，适合查询；`method="post"` 把表单数据放进请求体，适合向后端提交或修改数据，但 **POST 并不天然加密或比 GET 安全**，传输保护要靠 HTTPS。默认表单编码 `application/x-www-form-urlencoded` 常用于普通字段；实际上传文件通常使用 `method="post" enctype="multipart/form-data"`，并需要能解析上传内容的服务端。原生浏览器验证可以被绕过，服务端仍必须检查格式、范围、权限和文件内容。第 2 章的 `python -m http.server` 只提供静态文件服务；若真的向它发 POST，通常会得到“不支持的方法”响应，它不会保存留言或文件。本篇只实际演示 GET，不把静态服务器当业务后端。参见 [MDN：HTML 表单](https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/Structuring_content/HTML_forms)与[MDN：发送表单数据](https://developer.mozilla.org/en-US/docs/Learn_web_development/Extensions/Forms/Sending_and_retrieving_form_data)。

## 15. 语义化页面结构

本章介绍 header、nav、main、article、section、aside 和 footer 如何表达页面区域的含义。通过结构对比，理解语义元素如何让页面组织更清楚，也更便于辅助技术和后续维护。

### 15.1 同样的文字，不同的结构

**用途：**假设要在 `study-notes/index.html` 展示站名、导航、一篇笔记摘要、补充阅读和版权信息。下面两段是**结构对比用的局部片段**，二选一放在该文件的 `<body>` 内；若沿用第 2 章已经写好的 `index.html`，请先替换原有 `<body>` 内容，避免重复站名和导航。

只有通用容器的写法：

```html
<div>
  <div><h1>学习笔记</h1></div>
  <div><a href="notes/html-core.html">HTML 笔记</a></div>
  <div>
    <div>
      <h2>HTML 入门</h2>
      <p>先学文档结构，再练链接与图片。</p>
    </div>
    <div><h2>延伸阅读</h2><p>接下来可学习 CSS。</p></div>
  </div>
  <div><p>© 学习笔记</p></div>
</div>
```

表达区域含义的写法：

```html
<header><h1>学习笔记</h1></header>
<nav aria-label="主导航"><a href="notes/html-core.html">HTML 笔记</a></nav>
<main>
  <article>
    <h2>HTML 入门</h2>
    <p>先学文档结构，再练链接与图片。</p>
  </article>
  <aside>
    <h2>延伸阅读</h2>
    <p>接下来可学习 CSS。</p>
  </aside>
</main>
<footer><p>© 学习笔记</p></footer>
```

**页面结果：**两种写法都会显示相同的文字和链接，默认间距可能略有差异；它们都没有实现布局样式。第二种让浏览器和辅助技术能区分页眉、导航、主要内容、独立文章、附属内容和页脚，也让阅读源码的人更快找到区域。`header` 可以是页面或某个章节的引言区域，不是放元数据的 `<head>`；`nav` 放主要导航，不必包住每一个普通链接；`aside` 放与主内容相关但可独立略读的补充信息；`footer` 放页面或章节的收尾信息。`aria-label` 在这里为导航区域命名，原生语义仍由 `nav` 提供。参见 [MDN：组织文档](https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/Structuring_content/Structuring_documents)。

### 15.2 main、article、section 怎样选

**用途：**一个页面中应有一个主要可见的 `<main>`，放这一页独有的核心内容，不把每个卡片都写成 `main`，也不要把整站重复的导航放进去。能够单独拿出去阅读、分享或复用的一篇笔记适合 `article`。同一文档中有主题明确、通常能用标题命名的分区适合 `section`；若只是为了给以后排版套一层、没有独立主题，才用 `div`。标题层级按内容关系安排，不因字号或默认外观跳级。

例如，下面的**局部片段**可以放进 `study-notes/notes/html-core.html` 的 `<body>`，作为这页的主要内容骨架；若页面已有 `main`，请替换旧的主要内容，不要复制出第二个可见 `main`：

```html
<main>
  <article>
    <h1>HTML 基础笔记</h1>
    <section>
      <h2>文档结构</h2>
      <p>先确认 doctype、html、head 和 body 的职责。</p>
    </section>
    <section>
      <h2>页面链接</h2>
      <p>相对路径从当前文件的位置计算。</p>
    </section>
  </article>
</main>
```

**页面结果：**读者按“整篇笔记 → 文档结构／页面链接”顺序阅读；浏览器默认把各区域分开显示，辅助技术也能利用标题和语义区域导航。两个 `section` 是同一篇文章的主题分区；整篇文章是当前页面的主内容。写源码时也要保持合理顺序，不要指望日后视觉排版来修正混乱的阅读顺序。`section` 不是每段文字都要套的标签，`div` 也不是错误标签，关键是选择与内容含义相符的元素。

## 16. 全局属性与实用元素

本章整理 id、class、lang、hidden、tabindex 和 data-* 等常用全局属性，并认识 details、progress、meter 等实用元素。我们会区分可见内容、附加数据与需要脚本才能实现的交互。

### 16.1 给元素补充身份、语言和状态

**用途：**全局属性能用于各种 HTML 元素，但是否产生有用效果仍取决于元素和使用场景。先把常用属性按目的记住：

| 属性 | 用途 | 容易误解的地方 |
| --- | --- | --- |
| `id` | 标识页面中一个元素，可作为锚点或标签关联目标 | 同一文档内必须唯一；不同页面可使用相同的 `id` |
| `class` | 给元素分组，便于后续样式或脚本选取 | 可重复，一个元素可有多个以空格分隔的类名；类名本身不产生外观 |
| `title` | 提供补充说明 | 鼠标可能显示提示，但不能代替可见说明、`label` 或 `alt` |
| `lang`、`dir` | 声明语言和文字方向 | `lang` 不会翻译文字；`dir` 可取 `ltr`、`rtl`、`auto` |
| `hidden` | 将当前不相关的内容隐藏 | 普通 `hidden` 内容也不供屏幕阅读器正常阅读，不用于存秘密 |
| `tabindex` | 调整元素的可聚焦性 | `0` 加入自然 Tab 顺序；`-1` 不加入；避免正数重排顺序 |
| `data-*` | 保存当前页面自定义的附加数据 | 不会自动显示或提交，也不能存密码、密钥 |
| `contenteditable` | 允许用户编辑元素内容 | 修改页面不等于保存文件，也不自动成为表单字段 |
| `spellcheck` | 提示浏览器是否检查拼写 | 结果依赖浏览器、语言与用户设置，不是输入验证 |
| `translate` | 指示翻译工具是否应翻译内容 | `no` 常用于代码名或品牌词，不代表所有工具一定遵守 |
| `inert` | 暂停整个区域的用户交互 | 通常仍可见，但子元素不能正常获得焦点或点击，并从可访问性树中移除 |

下面是**局部片段，放在完整页面的 `<main>` 内**：

```html
<p><a href="#revision">跳到复习提示</a></p>
<p id="revision" class="note important" data-topic="html">
  复习提示：<span lang="en" translate="no">HTML</span> 负责内容结构。
</p>
<p title="这是补充说明">保存后记得刷新浏览器；这句话始终可见。</p>
<p lang="ar" dir="rtl">مرحبا</p>
<p hidden>这段草稿暂不展示。</p>
<p contenteditable="true" spellcheck="true" tabindex="0">
  可在这里修改复习提示，刷新后恢复原文。
</p>
<div inert>
  <p>以下区域暂不可操作。</p>
  <button type="button">暂不可用的操作</button>
</div>
```

**结果：**点击第一条链接会跳到复习提示；阿拉伯语段落按右到左方向呈现；草稿不显示；可编辑段落可以输入文字；最后的按钮虽可见，却不能正常点击或 Tab 聚焦。打开 Elements 可查看 `class` 和 `data-topic`，它们没有自动变成页面文字。刷新会丢失编辑内容，因为这里没有保存逻辑。

`contenteditable`、`spellcheck` 等是有指定取值的属性，不能把所有属性都按布尔属性理解。普通隐藏写成 `hidden` 即可，写 `hidden="false"` 也不会显示；恢复显示时应删除该属性。`hidden="until-found"` 是另一种支持页面查找揭示内容的状态，本篇不展开。`inert` 与 `hidden`、表单的 `disabled` 用途不同，不能互相替代。相关行为参见 [WHATWG：用户交互、隐藏与焦点](https://html.spec.whatwg.org/multipage/interaction.html)。

### 16.2 不用脚本也能展开的 details

**用途：**把补充解释放在可展开区域，让读者自行决定是否阅读。下面是**放在 `<main>` 内的局部片段**：

```html
<details>
  <summary>为什么修改文件后页面没有变化？</summary>
  <p>先保存正在编辑的文件，再刷新与该文件对应的地址。</p>
</details>
```

**结果：**初始通常只显示问题；点击摘要，或者让摘要获得键盘焦点后按 Enter 或空格，可展开和收起答案。`summary` 作为 `details` 的第一个子元素，提供容易理解的摘要；给 `details` 添加 `open` 可让它初始展开。不要为了折叠效果再把链接或按钮塞入摘要中，让摘要本身负责切换即可。

### 16.3 进度、测量和结果各用什么元素

**用途：**任务完成比例、固定范围内的测量值、计算结果表达的是不同信息。下面三个**局部片段可一同放在 `<main>` 内**：

```html
<p>
  <label for="reading-progress">阅读进度：已完成 3 / 10 章</label>
  <progress id="reading-progress" value="3" max="10">30%</progress>
</p>
<p>
  <label for="storage">资料空间：已使用 6 / 10 GB</label>
  <meter id="storage" min="0" max="10" value="6">6 / 10 GB</meter>
</p>
<p>
  <label for="total">本次练习得分：</label>
  <output id="total">8 / 10</output>
</p>
```

**结果：**浏览器通常将前两项呈现为不同的条形指示器，得分显示为文字。旁边的可见标签把数据含义写清楚，不依赖条形的颜色。`progress` 表示任务进度，不写 `value` 时表示进度未知；`meter` 表示已知范围中的量，不用于任务进度。`output` 表示计算或用户操作的结果，这里只是预先写好的示意值，不会自动计算，也不会把内容当作表单字段提交。三项都不会自己更新；动态更新留到 JavaScript 阶段学习。

### 16.4 注音与长词换行

**用途：**给汉字标注读音，或者允许浏览器在长词的指定位置换行。下面是**放在 `<main>` 内的局部片段**：

```html
<p>今天复习：<ruby>语义<rt>yǔ yì</rt></ruby>化标签。</p>
<p>长标识：study<wbr>notes<wbr>archive<wbr>reference<wbr>collection</p>
```

**结果：**`rt` 中的拼音通常出现在汉字上方；缩窄窗口后，长标识可以在 `wbr` 处折行。`wbr` 只提供可选断行点，空间足够时不会换行；它和总是产生换行的 `br` 不同。

### 16.5 dialog 与 template 的静态边界

**用途：**认识对话框与内容模板，但不提前编写交互逻辑。下面是**放在 `<body>` 内的局部片段**：

```html
<dialog open aria-labelledby="preview-title">
  <h2 id="preview-title">笔记预览</h2>
  <p>这是直接打开的非模态对话框示意。</p>
</dialog>
<template id="note-template">
  <article>
    <h2>待填充的笔记标题</h2>
    <p>待填充的内容。</p>
  </article>
</template>
```

**结果：**对话框可见，模板中的文章不会显示。`open` 只让这个对话框处于打开状态，不自动让背景不可交互，也不保证 Escape 关闭；这里没有提供关闭按钮或模态交互。实际对话框还需要设计打开、关闭、焦点进入和返回等行为，本篇不实现。`template` 保存可供以后使用的片段，需要后续的脚本等机制将内容插入页面才会显示。参见 [MDN：dialog 元素](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/dialog)。

## 17. 可访问性、SEO、验证与调试

本章从语言声明、标题结构、替代文本、表单标签和键盘操作出发，建立基础可访问性检查习惯。随后介绍 HTML 验证、浏览器工具与常见错误排查，并说明基础 SEO 能做什么、不能保证什么。

### 17.1 先用正确的 HTML，再补充 ARIA

**用途：**让使用键盘、屏幕阅读器和不同显示设置的读者都能理解页面。可访问性要融入每个元素的选择：跳转使用带 `href` 的链接，操作使用按钮，输入框使用可见标签；不能只在最后添加几个 `aria-*` 属性。

下面是**页面结构片段，用于替换 `<body>` 内相应区域，不要与已有的同名 `id` 重复**：

```html
<a href="#main-content">跳到主要内容</a>
<nav aria-label="主导航">
  <a href="index.html" aria-current="page">首页（当前页）</a>
  <a href="notes/html-core.html">HTML 基础笔记</a>
</nav>
<main id="main-content" tabindex="-1">
  <h1>学习笔记首页</h1>
  <p>从这里开始复习。</p>
</main>
```

**结果：**页面顶部有可见的跳转链接；键盘用户可先按 Tab 选中它，再按 Enter 跳过导航进入主要内容。`tabindex="-1"` 让目标能够接受焦点，但不增加日常 Tab 停靠点。当前页同时用可见文字与 `aria-current="page"` 标识，后者帮助辅助技术理解状态，自己不会改变外观。这里保留可见跳转链接，尚不涉及用 CSS 隐藏与显示它。

ARIA 用于补充可访问的名称、角色和状态，不能自动提供键盘行为。例如给 `div` 写 `role="button"` 不会使它自动支持按钮的焦点、Enter 和空格操作；直接用 `<button>` 更合适。也不必给 `<nav>` 重复添加 `role="navigation"`。只有原生语义不足且能正确维护状态与行为时，才考虑增加 ARIA。参见 [WAI：使用 ARIA 前须知](https://www.w3.org/WAI/ARIA/apg/practices/read-me-first/)。

### 17.2 把可访问性变成可检查的操作

按下面的顺序检查一个页面，比只判断“看起来正常”更可靠：

| 检查内容 | 实际操作 | 通过时应看到什么 |
| --- | --- | --- |
| 文档语言与区域 | 查看 `lang`、`main` 和导航 | 语言声明匹配内容，主要内容和导航分工清楚 |
| 标题结构 | 顺序阅读 `h1`～`h3` 等标题 | 标题表达内容层次，不为改变字号跳级 |
| 链接与图片 | 单独阅读链接文字和 `alt` | 能知道链接去哪、图片传达什么；装饰图用空 `alt` |
| 表格与表单 | 检查 `caption`、`th`、`scope` 和 `label` | 表头可关联数据，输入有可见名称与填写要求 |
| 键盘与焦点 | 连续按 Tab、Shift+Tab、Enter 和空格 | 焦点可见、顺序合理；链接、表单、折叠区域都能操作 |
| 缩放与说明 | 放大页面，脱离颜色阅读提示 | 未禁用页面缩放；必填、错误、当前项还有文字说明 |
| 音视频 | 检查字幕、文字稿和播放控件 | 不依靠听力或自动播放才能获得关键信息 |

**结果解释：**Tab 通常访问链接和表单控件，普通段落不会逐段停靠，这是正常行为。不要给所有文字加 `tabindex="0"`；确实需要焦点的自定义区域才使用它。避免 `tabindex="1"` 等正数，优先把源码顺序写正确。不同浏览器和系统的完整键盘导航设置可能影响 Tab 行为，测试时也要确认系统设置。后续写 CSS 时保留清晰的焦点提示，不要只为美观去掉焦点轮廓。

这份清单用于基础自查，不代表已经完成所有无障碍测试。可以进一步对照 [MDN：HTML 与可访问性](https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/Accessibility/HTML)。

### 17.3 基础 SEO 从准确描述页面开始

**用途：**让读者和搜索引擎容易理解每页主题。下面是**笔记页 `<head>` 内的元信息片段，替换已有的同类项即可**：

```html
<title>HTML 基础笔记 | 学习笔记</title>
<meta name="description" content="复习 HTML 文档结构、相对路径、语义标签与常见学习问题。">
```

**结果：**浏览器标签页显示标题；描述通常不会显示在正文中。搜索引擎可能将描述用于摘要，也可能自行选择正文内容，不能保证展示形式或排名。每页应有准确、不同的 `title`，正文有清晰主标题，链接文字说明目标内容，图片提供合适的替代文本。不要堆砌关键词或把重要说明全部画进图片。第 18 章的三页会给出不同标题与描述；仅在本机运行的站点不会因为加了这些元信息就自动被公网搜索引擎收录。

### 17.4 验证源码，再观察浏览器如何理解它

**用途：**浏览器会修复某些错误，所以“页面显示出来”不等于 HTML 合法。保存文件后使用 [Nu HTML Checker](https://validator.w3.org/nu/) 的文本输入或文件上传功能检查完整页面；`127.0.0.1` 地址只指访问者自己的电脑，在线服务不能通过该地址读取你的本地文件。不要把私密资料交给在线验证器。

**操作顺序：**先修复第一条结构性错误，再重新验证，避免一个未闭合标签引出许多后续提示。重点检查重复 `id`、错误嵌套、遗漏闭合、不允许的属性和值。验证器检查标准符合性，不会替你证明文案合适、路径存在、键盘体验良好或业务逻辑正确。

| 工具 | 要观察的位置 | 能回答的问题 |
| --- | --- | --- |
| 查看源代码 | 原始 HTML 文本 | 浏览器拿到的内容是不是刚保存的版本 |
| Elements | DOM、属性、可访问性信息 | 浏览器是否调整了嵌套，标签和控件名称是否正确 |
| Network | 请求 URL、状态码、响应类型和正文 | 页面或图片请求了哪里，是否返回了预期资源 |

**可观察练习：**回看第 7 章错误嵌套的 `p` 与 `div`，比较源代码和 Elements；浏览器可能提前关闭段落，DOM 不再与原文缩进对应。回看第 11 章相对路径，在 Network 找出错误资源的完整 URL，先纠正文件名和目录，不要靠反复刷新碰运气。Console 中没有报错也不能证明 HTML 正确，结构问题要配合验证器判断。

### 17.5 看到旧教程时，先辨认哪些写法不该照搬

| 旧写法或误用 | 问题 | 现代方向 |
| --- | --- | --- |
| `font`、`center` | 已废弃的表现型元素 | 用语义元素组织内容，外观以后交给 CSS |
| `marquee` | 已废弃的滚动文字元素 | 普通文本先保证可读；确需动效时再考虑现代实现与减少动态效果设置 |
| `frameset`、`frame` | 已废弃的页面分框方式 | 用正常页面结构、导航和布局；必要嵌入再单独评估 `iframe` |
| `align`、`bgcolor` 等旧表现属性 | 在旧教程里常用于排版或配色 | 查当前元素允许的属性，表现需求交给 CSS |
| 多个 `br`、`&nbsp;`、布局表格 | 用内容标签硬凑间距或布局 | 正确段落与语义分区，间距和布局留到 CSS |
| “所有加粗都用 strong” | 把视觉效果误当内容的重要程度 | 按第 9 章的语义选择标签，再决定外观 |

**判断边界：**`b`、`i`、`u`、`s` 和 `iframe` 并非整类废弃。它们仍有现代语义或合法场景，但不应拿来无条件替代 `strong`、`em`、`del` 或页面布局。遇到旧属性先查元素的当前文档，不能把正常的图片 `width`、`height` 也一概判为废弃。参见 [WHATWG：废弃特性](https://html.spec.whatwg.org/dev/obsolete.html)。

## 18. 完整多页面案例与后续路线

本章将前面的知识组合成首页、笔记页和联系页，并检查页面链接、图片资源与 GET 表单在本地服务器中的表现。最后整理常见故障的排查顺序和从 HTML 继续学习 Web 开发的建议路线。

### 18.1 保存四个文件，组成一个网站

**用途：**把零散示例组合成可实际访问的“学习笔记”站。下面给出四个资源的全部内容，不需要 CSS、JavaScript、第三方图片或后端。若已有前面章节的练习文件，先另存备份，再用本章完整代码替换相应文件；不要把多份完整文档拼在同一个文件里。

在同一个 `study-notes` 文件夹下建立：

```text
study-notes/
├── index.html
├── contact.html
├── notes/
│   └── html-core.html
└── images/
    └── html-notes.svg
```

**结果：**两页位于网站根目录，笔记页在 `notes` 子目录，图片在 `images` 子目录。按文件名保存为 UTF-8，确认没有隐藏的 `.txt` 后缀。代码块前的 `File` 注释是文章中的文件标记，不必复制进文件。

### 18.2 首页：index.html

**用途：**展示站点入口和学习顺序。下面是可直接保存为 `study-notes/index.html` 的完整文件，双击可预览；后面的请求观察统一通过本地服务器进行。

<!-- File: index.html -->
```html
<!doctype html>
<html lang="zh-CN">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <meta name="description" content="学习笔记首页：从文档结构、链接和图片开始复习 HTML。">
  <title>首页 | 学习笔记</title>
</head>
<body>
  <a href="#main-content">跳到主要内容</a>
  <header>
    <p>学习笔记 · 把知识写成网页</p>
    <nav aria-label="主导航">
      <ul>
        <li><a href="index.html" aria-current="page">首页（当前页）</a></li>
        <li><a href="notes/html-core.html">HTML 基础笔记</a></li>
        <li><a href="contact.html">联系与练习</a></li>
      </ul>
    </nav>
  </header>
  <main id="main-content" tabindex="-1">
    <h1>我的学习笔记</h1>
    <p>这个小站用 HTML 记录知识，并练习页面、图片与表单之间的联系。</p>
    <section>
      <h2>从一篇笔记开始</h2>
      <article>
        <h3><a href="notes/html-core.html">HTML 基础：结构与路径</a></h3>
        <p>复习文档骨架、语义标签和相对路径，再检查自己是否能解释页面结构。</p>
      </article>
    </section>
    <section>
      <h2>今天的练习顺序</h2>
      <ol>
        <li>打开笔记页，阅读正文和表格。</li>
        <li>检查笔记页图片请求是否成功。</li>
        <li>打开联系页，用测试数据观察 GET 查询参数。</li>
      </ol>
    </section>
    <aside>
      <h2>学习提醒</h2>
      <p>先把内容和路径写正确，再学习 CSS 如何改变外观。</p>
    </aside>
  </main>
  <footer>
    <p>学习笔记 · 本地 HTML 练习站</p>
  </footer>
</body>
</html>
```

**结果：**首页显示导航、笔记摘要和有序练习清单。主导航中的首页带“当前页”说明；按 Tab 可依次访问跳转链接和导航。页面较为朴素是浏览器默认样式的结果，语义结构已经齐全。

### 18.3 笔记页：notes/html-core.html

**用途：**把文本、列表、图片、表格和折叠问答组织成独立文章。下面是可保存为 `study-notes/notes/html-core.html` 的完整文件：

<!-- File: notes/html-core.html -->
```html
<!doctype html>
<html lang="zh-CN">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <meta name="description" content="HTML 基础笔记：文档结构、语义标签、相对路径与目录示意。">
  <title>HTML 基础笔记 | 学习笔记</title>
</head>
<body>
  <a href="#main-content">跳到主要内容</a>
  <header>
    <p>学习笔记 · 把知识写成网页</p>
    <nav aria-label="主导航">
      <ul>
        <li><a href="../index.html">首页</a></li>
        <li><a href="html-core.html" aria-current="page">HTML 基础笔记（当前页）</a></li>
        <li><a href="../contact.html">联系与练习</a></li>
      </ul>
    </nav>
  </header>
  <main id="main-content" tabindex="-1">
    <article>
      <h1>HTML 基础笔记：结构与路径</h1>
      <p>HTML 用元素表达内容含义。<strong>结构正确比标签数量多更重要。</strong></p>
      <section>
        <h2>先检查文档骨架</h2>
        <ul>
          <li><code>head</code> 保存标题、编码等元信息。</li>
          <li><code>body</code> 包含读者能够访问的页面内容。</li>
          <li><code>main</code> 标识当前页面的主要内容。</li>
        </ul>
      </section>
      <section>
        <h2>从目录理解相对路径</h2>
        <p>本页在 <code>notes</code> 目录，返回上一级后才能找到首页、联系页和图片目录。</p>
        <figure>
          <img src="../images/html-notes.svg" width="480" height="260"
               alt="study-notes 目录包含 index.html、contact.html、notes/html-core.html 和 images/html-notes.svg。">
          <figcaption>图 1：四个文件组成同一个学习笔记站。</figcaption>
        </figure>
        <table>
          <caption>从当前笔记页出发的路径</caption>
          <thead>
            <tr><th scope="col">目标</th><th scope="col">相对路径</th><th scope="col">作用</th></tr>
          </thead>
          <tbody>
            <tr><th scope="row">首页</th><td><code>../index.html</code></td><td>回到站点入口</td></tr>
            <tr><th scope="row">联系页</th><td><code>../contact.html</code></td><td>练习 GET 表单</td></tr>
            <tr><th scope="row">目录图片</th><td><code>../images/html-notes.svg</code></td><td>显示本页插图</td></tr>
          </tbody>
        </table>
      </section>
      <section>
        <h2>常见问题</h2>
        <details>
          <summary>为什么不能直接写 images/html-notes.svg？</summary>
          <p>浏览器会从当前 notes 目录寻找 images 子目录。本站的 images 在上一级，所以要先写 ../。</p>
        </details>
      </section>
    </article>
  </main>
  <footer>
    <p><a href="../index.html">返回学习笔记首页</a></p>
  </footer>
</body>
</html>
```

**结果：**三页导航互通，文章中有一张目录图片、一个三列表格和可展开的路径问答。图片文件在 18.5 节提供，保存它后再检查图片显示。这里的 `alt` 描述图片真正包含的四个文件，而不是“图片”或文件名的简单重复。

### 18.4 联系与练习页：contact.html

**用途：**练习表单标签、分组、原生验证与 GET 提交。下面是可保存为 `study-notes/contact.html` 的完整文件。仅使用虚构测试数据，不填真实联系方式；查询参数会进入地址栏和服务器日志。

<!-- File: contact.html -->
```html
<!doctype html>
<html lang="zh-CN">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <meta name="description" content="使用测试数据练习 HTML 表单标签、浏览器验证与 GET 查询参数。">
  <title>联系与练习 | 学习笔记</title>
</head>
<body>
  <a href="#main-content">跳到主要内容</a>
  <header>
    <p>学习笔记 · 把知识写成网页</p>
    <nav aria-label="主导航">
      <ul>
        <li><a href="index.html">首页</a></li>
        <li><a href="notes/html-core.html">HTML 基础笔记</a></li>
        <li><a href="contact.html" aria-current="page">联系与练习（当前页）</a></li>
      </ul>
    </nav>
  </header>
  <main id="main-content" tabindex="-1">
    <h1>联系与练习</h1>
    <p id="form-help">这是 GET 提交练习。仅使用测试数据；提交后重新加载本页，不保存留言、不发送邮件。</p>
    <form action="contact.html" method="get" aria-describedby="form-help">
      <fieldset>
        <legend>练习信息</legend>
        <p>
          <label for="nickname">测试昵称（必填，2～20 个字符）</label>
          <input id="nickname" name="nickname" type="text" required minlength="2" maxlength="20">
        </p>
        <p>
          <label for="email">测试邮箱（必填，例如 learner@example.com）</label>
          <input id="email" name="email" type="email" required>
        </p>
        <p>
          <label for="topic">练习主题（必选）</label>
          <select id="topic" name="topic" required>
            <option value="">请选择主题</option>
            <option value="html">HTML 结构</option>
            <option value="paths">链接与路径</option>
            <option value="forms">表单</option>
          </select>
        </p>
        <p>
          <label for="chapters">本次复习章数（必填，1～18 的整数）</label>
          <input id="chapters" name="chapters" type="number" min="1" max="18" step="1" required>
        </p>
        <p>
          <label for="message">练习留言（必填，10～200 个字符）</label>
          <textarea id="message" name="message" rows="5" cols="30" required minlength="10" maxlength="200"></textarea>
        </p>
        <p>
          <input id="test-only" name="test_only" type="checkbox" value="yes" required>
          <label for="test-only">我确认以上均为测试数据（必选）</label>
        </p>
      </fieldset>
      <p>
        <button type="submit" name="action" value="preview">提交练习并查看地址栏</button>
      </p>
    </form>
  </main>
  <footer>
    <p>此站只有静态文件，没有接收留言的业务后端。</p>
  </footer>
</body>
</html>
```

**结果：**留空提交时，浏览器会阻止常规提交并提示需要完成的控件；昵称太短、邮箱格式不符、未选择主题、章数超出范围或留言太短，也会触发相应约束。输入长度练习请手动键入，具体提示文字因浏览器而异。全部填写正确并点击提交后，地址栏出现 `nickname`、`email`、`topic`、`chapters`、`message`、`test_only` 和被点击提交按钮的 `action` 参数。

每个数据控件都有 `name` 和关联的可见 `label`；提交按钮自身的可见文字就是它的标签。按钮的名值只有在它作为提交按钮参与提交时才出现，因此本练习明确要求点击该按钮。服务器收到 GET 后返回同一份静态页面，不会回填表单、持久保存留言或发送邮件。浏览器是否保留部分输入还可能受到恢复表单状态的行为影响，不应把它理解为后端保存成功。这里的约束只帮助输入，真正的业务系统仍需服务端验证。

### 18.5 图片资源：images/html-notes.svg

**用途：**提供可随文章一起保存的目录示意图，不依赖外部图床。将下面代码保存为 `study-notes/images/html-notes.svg`，它是独立 SVG 文件，不要加 HTML 文档骨架。

<!-- File: images/html-notes.svg -->
```html
<svg xmlns="http://www.w3.org/2000/svg" width="480" height="260" viewBox="0 0 480 260" role="img" aria-labelledby="diagram-title diagram-desc">
  <title id="diagram-title">学习笔记站目录</title>
  <desc id="diagram-desc">study-notes 包含 index.html、contact.html、notes/html-core.html 和 images/html-notes.svg。</desc>
  <text x="20" y="35">study-notes/</text>
  <text x="40" y="80">index.html</text>
  <text x="40" y="125">contact.html</text>
  <text x="40" y="170">notes/html-core.html</text>
  <text x="40" y="215">images/html-notes.svg</text>
</svg>
```

**结果：**图片显示站点名称及缩进排列的四条文件路径，与本章目录一致。SVG 是文本格式的矢量图片；这里仅使用文字和位置属性，没有脚本、外部资源或 CSS 规则。通过 `<img>` 使用它时，笔记页上的 `alt` 是主要替代文本；SVG 内部的 `title`、`desc` 也描述资源本身，不能据此省略 HTML 中的 `alt`。

### 18.6 启动服务器，观察页面与资源请求

**操作：**在终端进入实际创建的 `study-notes` 文件夹，再运行以下命令；不能站在它的上一级目录却期待同样的 URL 路径：

```bash
python -m http.server 8000 --bind 127.0.0.1
```

**结果：**终端开始等待请求。浏览器访问 `http://127.0.0.1:8000/`，通常会读到目录中的 `index.html`。Windows 若已安装 Python 启动器但没有 `python` 命令，可用 `py -m http.server 8000 --bind 127.0.0.1`；若端口被占用，可改为 `8001` 并同步修改浏览器地址。结束时在终端按 Ctrl+C。只绑定回环地址意味着这个练习服务器供本机访问。

打开 DevTools 的 Network，保留面板打开并刷新页面。首次无缓存读取通常返回 200；若要明确观察 200，可在开发者工具打开期间勾选 Disable cache 再刷新。普通刷新也可能出现 304 或使用缓存，不要把它误认为加载失败。按以下顺序验证：

| 操作 | Network 中观察 | 预期含义 |
| --- | --- | --- |
| 打开首页、笔记页、联系页 | 对应请求的 Headers：Request URL、Request Method、Status Code | 三页 GET 成功，通常是 200 |
| 打开笔记页 | `html-notes.svg` 请求及响应 `Content-Type` | 图片单独请求，通常为 `image/svg+xml`，状态 200 |
| 点击跳到主要内容 | 地址末尾出现 `#main-content`，通常无新文档请求 | 片段用于页面内部定位，不随 HTTP 请求发送 |
| 有效填写并点击表单提交按钮 | `contact.html?...` 的请求 URL，以及 Payload 或查询参数区域 | GET 参数出现在查询串，请求仍可返回静态联系页 |
| 手动访问 `/missing.html` | 新请求的状态码和响应正文 | 不存在资源返回 404，故意的测试不需要破坏正常导航 |

例如昵称输入“同学”，邮箱使用 `learner@example.com`，主题选 HTML 结构，章数填 `3`，留言输入“这是一条用于练习表单的测试留言。”并勾选确认。浏览器会对中文及特殊字符编码，查询参数面板通常可显示解码后的值。不要把查询串中的 `%` 编码当作乱码。若首次留空点击提交没有产生新的请求，这正是浏览器验证阻止提交的可观察结果。

先打开 Network 再操作；需要保留导航前的请求时勾选 Preserve log。请求列表可能还有浏览器尝试获取的 `favicon.ico`，它的 404 不代表三页导航或目录图片坏了。此处只实测 GET，静态服务器没有业务 POST 处理能力。

### 18.7 四类常见问题，按证据排查

| 问题 | 先查什么 | 然后怎么定位 |
| --- | --- | --- |
| 页面打不开或不是新版 | 服务器是否运行、地址和端口是否一致、启动目录是否正确 | 查看请求 URL 与状态；确认文件已保存且不是 `.html.txt`，再核对响应内容和缓存 |
| 图片不显示 | Network 中图片的实际 URL 是否含多余的 `notes/images` | 对照目录确认 `../`、文件名大小写和 `/`；直接打开图片 URL 检查 200、类型与内容 |
| 表单没有请求或缺参数 | 是否被原生验证阻止，控件是否有 `name` | 检查 `disabled`、复选是否选中、点击了哪个提交按钮；在查询参数里找名值对，不在请求体里找 GET 字段 |
| 中文乱码 | 文件实际保存编码是否为 UTF-8 | 检查早期的 `meta charset`、服务器响应编码信息；区分页面文字乱码与正常 URL 百分号编码 |

**完成标准：**能从首页进入其他两页，笔记图片可见，表格含义清晰，折叠问答可用键盘打开，表单无效时阻止提交、有效时显示查询参数，且能解释那条故意制造的 404。仅“截图看起来差不多”不足以证明路径和提交正确。

### 18.8 从 HTML 继续学习的顺序

这个案例已经把内容结构与请求联系起来。接下来每一步都可以继续改造同一个小站，避免为了练一项基础知识立即换一套框架：

| 学习阶段 | 主要解决的问题 | 在本站上的下一步练习 |
| --- | --- | --- |
| 1. 语义化 HTML | 内容是什么、结构是否合理 | 独立写出三页，解释标签和路径选择 |
| 2. CSS 基础与盒模型 | 字体、间距、边框与尺寸 | 给文章建立一致的阅读样式 |
| 3. Flex / Grid 布局 | 区域如何排列 | 组织导航和内容区 |
| 4. 响应式设计 | 不同屏幕如何阅读和操作 | 检查小屏表格、图片、表单与缩放 |
| 5. JavaScript 基础 | 如何表达程序逻辑 | 先练变量、条件、循环和函数 |
| 6. DOM 与事件 | 如何响应用户操作和更新页面 | 做本地阅读进度或可控的交互组件 |
| 7. HTTP / API | 如何向真正的服务请求数据 | 学习请求、响应、错误与服务端验证 |
| 8. Git 与部署 | 如何记录版本和让别人访问 | 保存版本，把静态站发布到合适的托管环境 |
| 9. 前端框架 | 复杂界面如何组织与复用 | 能解释基础流程后再按实际需求选择 |

**学习结果：**每增加一种技术，都能说出它解决了哪类问题。Git 的基本版本记录也可较早开始；表中第 8 步强调将版本管理与上线流程连起来。当前阶段不必为了运行这四个静态文件而安装框架、构建工具或数据库。

### 18.9 一页式速查索引

| 想实现什么 | 关键标签或概念 | 对应章节 |
| --- | --- | --- |
| 理解浏览器与服务器分工 | Web、前后端、HTML / CSS / JS | 第 1、6 章 |
| 在本机访问网页 | 文件目录、本地服务器、`index.html` | 第 2、18 章 |
| 拆解地址、理解请求 | URL、DNS、HTTP、HTTPS、状态码 | 第 3～4 章 |
| 查页面与资源加载 | DOM、Elements、Network、缓存 | 第 5、17～18 章 |
| 避免错误嵌套和乱码 | 内容模型、实体、doctype、charset | 第 7～8 章 |
| 标记文章文字 | 标题、`p`、强调、引用、代码 | 第 9 章 |
| 组织条目与分组 | `ul`、`ol`、`dl`、`div`、`span` | 第 10 章 |
| 链接其他页或页内位置 | `a`、`href`、`id`、`../` | 第 11 章 |
| 放图片或媒体 | `img`、`alt`、`picture`、媒体元素 | 第 12 章 |
| 呈现二维数据 | `table`、`caption`、`th`、`scope` | 第 13 章 |
| 收集并验证输入 | `form`、`label`、`name`、原生约束 | 第 14 章 |
| 划分页面语义区域 | `header`、`nav`、`main`、`article` | 第 15 章 |
| 增加附加信息与原生交互 | 全局属性、`details`、`progress`、`meter` | 第 16 章 |
| 检查可访问性与页面质量 | ARIA 边界、键盘、SEO、验证器 | 第 17 章 |
| 完成多页网站并排错 | 相对路径、资源请求、GET 查询参数 | 第 18 章 |

### 18.10 官方参考与使用方式

遇到疑问，先带着具体问题查对应入口；入门解释与规范的用途不同，不必从头通读整份标准：

- [WHATWG HTML Living Standard](https://html.spec.whatwg.org/multipage/)：查元素、属性、内容模型和浏览器处理规则，是 HTML 的规范依据。
- [MDN HTML](https://developer.mozilla.org/en-US/docs/Web/HTML)：查元素与属性的解释、示例及兼容性资料。
- [MDN Learn Web Development](https://developer.mozilla.org/en-US/docs/Learn_web_development)：按学习路径理解 Web、HTML、可访问性及后续 CSS / JavaScript。
- [MDN HTTP](https://developer.mozilla.org/en-US/docs/Web/HTTP)：结合 Network 面板查方法、状态码、头部和缓存。
- [IETF RFC 9110：HTTP Semantics](https://www.rfc-editor.org/rfc/rfc9110.html)：需要精确确认 HTTP 语义时查阅；它不是要求初学者背诵的入门清单。

学习时以“能写出、能运行、能观察、能解释”为一轮：遇到具体页面问题，再回到本篇索引和官方文档定位，而不是仅背标签名称。

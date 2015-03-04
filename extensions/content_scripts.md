# 内容脚本(content_scripts)

内容脚本是在网页的上下文中运行的 JavaScript 文件，它们可以通过标准的[文档对象模型（DOM）](http://www.w3.org/TR/DOM-Level-2-HTML/)来获取浏览器访问的网页详情，或者作出更改。

如下是内容脚本可以实现的一些功能的例子：

* 在网页中找出未链接的URL，并将它们转换为超链接
* 增加字体大小，使文本更具有可读性
* 发现并处理 DOM 中的[微格式](http://microformats.org/)数据

然而，内容脚本也有一些限制，它们**不能** ：

* 调用 chrome.* API（除了 chrome.extension 中的一部分）
* 使用所属扩展程序页面中定义的变量或函数
* 使用网页或其他内容脚本中定义的变量或函数

这些限制并不如看上去那么糟糕，内容脚本可以间接地通过与所属扩展程序交换[消息](messaging.md)的方式，来使用 chrome.* API、访问扩展程序数据并请求扩展程序完成操作。内容脚本也可以像所属扩展程序一样向拥有主机权限的站点发出[跨站 XMLHttpRequest](xhr.md)，另外也可以使用共享的 DOM [与网页通信](#host-page-communication)。有关内容脚本能做什么与不能做什么的更多内部细节，请参见[执行环境](#execution-environment)。

## 清单文件
如果你的内容脚本代码每一次都需要插入到网页中，在扩展程序的清单文件中使用 content_scripts 字段注册它，如以下例子所示：

```
{
  "name": "我的扩展程序",
  ...
  "content_scripts": [
    {
      "matches": ["http://www.google.com/*"],
      "css": ["mystyles.css"],
      "js": ["jquery.js", "myscript.js"]
    }
  ],
  ...
}
```
如果只是有时需要插入代码，应该使用 [permissions](declare_permissions.md) 字段，如[以编程方式插入](#pi)部分所述。

使用 content_scripts 字段，扩展程序可以向一个页面中插入多个内容脚本，每个内容脚本可以有多个 JavaScript 和 CSS 文件，content_scripts 数组中的每一个项目可以包含如下属性：

| 名称           | 类型           | 描述  |
| :------------- |:-------------| :-----|
| matches | array of strings | 必选。 指定内容脚本要插入到哪些页面中去。有关这些字符串语法的更多细节请参见匹配表达式，有关如何排除 URL 的信息请参见[匹配表达式和范围](#match-patterns-globs) |
| exclude_matches | array of strings |   可选。 排除不需要插入内容脚本的页面。有关这些字符串语法的更多细节请参见匹配表达式，有关如何排除 URL 的信息请参见[匹配表达式和范围](#match-patterns-globs) |
| css | array of strings | 可选。 要插入匹配页面的 CSS 文件列表，它们将在页面的所有 DOM 构造或显示之前，按照数组中指定的顺序插入。 |
| js | array of strings | 可选。 要插入匹配页面的 JavaScript 文件列表，它们将按照数组中指定的顺序插入。 |
| run_at | strings | 可选。 控制 js 中的 JavaScript 文件何时插入，可以为 "document_start"、"document_end" 或 "document_idle"，默认为 "document_idle"。  如果是 "document_start"，这些文件将在 css 中指定的文件之后，但是在所有其他 DOM 构造或脚本运行之前插入。<br/><br/> 如果是 "document_end"，文件将在 DOM 完成之后立即插入，但是在加载子资源（如图像与框架）之前插入。 <br/><br/>如果是 "document_idle"，浏览器将在 "document_end" 和刚发生 window.onload 事件这两个时刻之间选择合适的时候插入，具体的插入时间取决于文档的复杂程度以及加载文档所花的时间，并且浏览器会尽可能地为加快页面加载速度而优化。 <br/><br/>注意：如果使用 "document_idle"，内容脚本不一定会收到 window.onload 事件，因为它们可能在这一事件已经发生后再执行。在大多数情况下，在 "document_idle" 时运行的内容脚本没有必要监听 onload 事件，因为它们保证在 DOM 完成后运行。如果您的脚本确实需要在 window.onload 之后运行，您可以检查 document.readyState 属性确定 onload 事件是否已经发生。|
| all_frames | boolean | 可选。 控制内容脚本运行在匹配页面的所有框架中还是仅在顶层框架中。 |
| include_globs | array of string | 可选。 在应用 matches 之后仅包含同时匹配这一范围的 URL。该属性是为了模拟 Greasemonkey 中的 @include 关键字，有关更多详情请参见下面的[匹配表达式和范围](#match-patterns-globs) |
| include_globs | array of string | 可选。 在应用 matches 之后排除匹配这一范围的 URL。该属性是为了模拟 Greasemonkey 中的 @exclude 关键字，有关更多详情请参见下面的[匹配表达式和范围](#match-patterns-globs) |



### 扩展程序的用户界面
许多扩展程序（但不包括 Chrome 应用）以浏览器按钮或页面按钮的形式向 Google Chrome 浏览器增加用户界面，每个扩展程序最多能有一个浏览器按钮或页面按钮。当扩展程序与大部分网页相关时选择使用浏览器按钮，当扩展程序的图标显示还是消失取决于具体网页时选择使用页面按钮。

扩展程序（以及 Chrome 应用）也可以以其他形式呈现用户界面，例如在 Chrome 浏览器的右键菜单中添加内容，提供选项页面，或者利用内容脚本更改页面的显示方式。有关完整的扩展程序功能以及每一种功能的实现细节，请看 [开发者指南]() 。

## 文件
每一个扩展程序包含以下文件：

* 一个清单文件
* 一个或多个 HTML 文件（除非扩展程序是一个主题背景）
* 可选：一个或多个 JavaScript 文件
* 可选：扩展程序需要的任何其他文件，例如图片

### 引用文件
可以在扩展程序中放置任何文件，使用的话可以通过相对 URL 引用文件，如：
` <img src="images/myimage.png"> `

使用Chrome的调试器可发现，扩展程序中的每一个文件也可以通过绝对 URL 访问，如：
`chrome-extension://<扩展程序标识符>/<文件路径>`

在这 URL 中，<扩展程序标识符> 是扩展程序系统为每一个扩展程序生成的唯一标识符，进入 chrome://extensions 就能看到所有扩展程序的标识符。<文件路径> 是扩展程序的主目录下的文件位置，与相对 URL 相同。

### 清单文件
manifest.json 提供有关扩展程序的各种信息，例如最重要的文件和扩展程序可能具有的能力。以下是一个典型的清单文件，用于一个浏览器按钮，它将会访问来自 google.com 的信息：

```
{
  "name": "我的扩展程序",
  "version": "2.1",
  "description": "从 Google 获取信息。",
  "icons": { "128": "icon_128.png" },
  "background": {
    "persistent": false,
    "scripts": ["bg.js"]
  },
  "permissions": ["http://*.google.com/", "https://*.google.com/"],
  "browser_action": {
    "default_title": "",
    "default_icon": "icon_19.png",
    "default_popup": "popup.html"
  }
}
```

关于manifest文件的更多细节： [manifest.json](manifest.md)


## 架构
许多扩展程序有一个后台网页，它是一个包含扩展程序主要逻辑的不可见页面。扩展程序也可以包含其他页面，展现扩展程序的用户界面。如果扩展程序需要与用户加载的网页交互（相对于包含在扩展程序中的页面），扩展程序必须使用内容脚本(content-script)。

### 后台网页
下图所示的浏览器至少安装了两个扩展程序：一个浏览器按钮（黄色图标）和一个页面按钮（蓝色图标）。浏览器按钮和页面按钮都有后台页面。下图显示了浏览器按钮的后台页面，由 background.html 定义，并且包含在这两个窗口中控制浏览器按钮的 JavaScript 代码。

![]()

后台网页分两种：持久运行的后台网页与事件页面。顾名思义，持续运行的后台网页保持打开状态，事件页面根据需要打开与关闭。除非绝对需要你的后台网页一直运行，最好首选事件页面。

有关更多细节，参考 [事件页面]()与 [后台网页]()。

### 用户界面网页
扩展程序可以包含普通的 HTML 网页，用来显示扩展程序的用户界面。例如，浏览器按钮可以包含弹出菜单，通过 HTML 文件实现。任何一个扩展程序都可以有选项页面，让用户自定义扩展程序的工作方式。另外一种特殊页面是替代页面。最后，可以使用 tabs.create 或 window.open() 来显示扩展程序中的任何其他 HTML 文件。

扩展程序中的 HTML 网页可以互相访问其他页面的全部 DOM，并且可以互相调用函数。

下图显示了浏览器按钮弹出菜单的架构。弹出菜单是由一个 HTML 文件（popup.html）定义的网页，该扩展程序也正好有一个后台网页（background.html）。弹出窗口不用重复后台网页中的代码，因为弹出窗口可以调用后台网页上的函数。


有关更多细节，参考 [浏览器按钮]()、[选项]()、[替代页面]() 和 [页面间通信]() 这些部分。



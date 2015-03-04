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

### 匹配表达式和范围

如果页面的 URL 匹配任何 matches 指定的表达式以及任何 include_globs 指定的表达式，并且不匹配 exclude_matches 和 exclude_globs 指定的表达式，则会在页面中插入内容脚本。因为 matches 属性是必需的，exclude_matches、include_globs 和 exclude_globs 只能用来限制受到影响的页面。
例如，假设 matches 为 ["http://\*.nytimes.com/\*"]：

* 如果 exclude_matches 为 ["\*://\*/\*business*"]，那么内容脚本会插入“http://www.nytimes.com/health”，但不会插入“http://www.nytimes.com/business”。
* 如果 include_globs 为 ["\*nytimes.com/???s/\*"]，那么内容脚本会插入“http://www.nytimes.com/arts/index.html”和“http://www.nytimes.com/jobs/index.html”，但不会插入“http://www.nytimes.com/sports/index.html”。
* 如果 exclude_globs 为 ["\*science\*"]，那么内容脚本会插入“http://www.nytimes.com”，但不会插入“http://science.nytimes.com”或“http://www.nytimes.com/science”。

范围（glob）属性与匹配表达式相比遵循不同并且更灵活的语法。所有含有“通配符”星号和问号的 URL 都是可接受的范围字符串，星号（*）匹配任意长度的字符串（包括空字符串），问号（?）匹配任意单个字符。

例如，范围 "http://???.example.com/foo/*" 匹配以下任一 URL：

* "http://www.example.com/foo/bar"
* "http://the.example.com/foo/"

但是不匹配如下 URL：

* "http://my.example.com/foo/bar"
* "http://example.com/foo/"
* "http://www.example.com/foo"

## 以编程方式插入

如果您的 JavaScript 或 CSS 代码不需要插入匹配的每一个页面时，例如，如果您希望当用户单击浏览器按钮的图标时才运行脚本，以编程方式插入代码就十分有用。

要向页面中插入代码，您的扩展程序必须拥有该页面的[主机权限](xhr.md#requesting-permission)，并且还需要用到 chrome.tabs 模块。您可以使用清单文件中的 [permissions](declare_permissions.md) 字段获得这些权限。

一旦您拥有了相应的权限，您可以通过调用 [tabs.executeScript](tabs.md#method-executeScript) 向页面插入 JavaScript 代码。要插入 CSS 代码，请使用 [tabs.insertCSS](tabs.md#method-insertCSS)。

下列代码（来自 [make_page_red](http://src.chromium.org/viewvc/chrome/trunk/src/chrome/common/extensions/docs/examples/api/browserAction/make_page_red/) 例子）当用户单击浏览器按钮时向当前标签页插入并执行 JavaScript 代码。

```
chrome.browserAction.onClicked.addListener(function(tab) {
  chrome.tabs.executeScript({
    code: 'document.body.style.backgroundColor="red"'
  });
});
```
```
"permissions": [
  "activeTab"
],
```

当浏览器正在显示一个 HTTP 页面，并且用户单击该扩展程序的浏览器按钮时，扩展程序将页面的 bgcolor 属性设为'red'。结果，除非页面通过 CSS 设置了背景颜色，页面将变成红色。

通常，您不会直接插入代码（如前面的例子所示），而是将代码放在一个文件中。您可以像这样插入文件的内容：

```
chrome.tabs.executeScript(null, {file: "content_script.js"});
```

## 执行环境

内容脚本在一个称为隔离环境的特殊环境中执行。它们可以访问所在页面的 DOM，但是不能访问当前页面创建的任何 JavaScript 变量或函数。对于每个内容脚本来说，就像没有其他 JavaScript 在当前页面上执行。反过来也是如此：在当前页面运行的 JavaScript 不能调用或访问任何内容脚本定义的函数或变量。

例如，考虑如下简单页面：

```
<html>
  <button id="mybutton">单击我</button>
  <script>
    var greeting = "您好，";
    var button = document.getElementById("mybutton");
    button.person_name = "Bob";
    button.addEventListener("click", function() {
      alert(greeting + button.person_name + "。");
    }, false);
  </script>
</html>
```
假设如下内容脚本插入到了 hello.html 中：

```
var greeting = "您好啊，";
var button = document.getElementById("mybutton");
button.person_name = "Roberto";
button.addEventListener("click", function() {
  alert(greeting + button.person_name + "。");
}, false);
```

现在，如果按钮按下，您将会看到两条问候。

隔离环境允许每一个内容脚本更改自己的 JavaScript 环境，而不用担心是否会与页面或其他内容脚本发生冲突。例如，一个内容脚本可以包含 JQuery v1 而页面可以包含 JQuery v2，并且它们互不影响。

隔离环境的另一个重要好处是将页面上的 JavaScript 和扩展程序中的 JavaScript 完全区分开来，这样我们就可以为内容脚本提供额外功能，而这些额外功能不应该从网页中访问，我们也不用担心访问它们的网页。

网页与扩展程序之间共享的 JavaScript 对象值得注意，例如 `window.onload` 事件。每一个隔离环境拥有该对象自己的副本，对该对象赋值只影响这一独立的副本。例如，网页和扩展程序都可以给 `window.onload` 赋值，但是都不能读取另外一方的事件处理器。事件处理器将按照它们赋值的顺序调用。

## 与嵌入的页面通信
尽管内容脚本的执行环境和所在页面是互相隔离的，但是它们都可以访问页面的 DOM。如果页面想要和内容脚本通信（或者通过内容脚本与扩展程序通信），必须通过共享的 DOM 进行。

可以使用 window.postMessage（或者 window.webkitPostMessage 用于可传输（Transferable）的对象）：

```
var port = chrome.runtime.connect();

window.addEventListener("message", function(event) {
  // 我们只接受来自我们自己的消息
  if (event.source != window)
    return;

  if (event.data.type && (event.data.type == "FROM_PAGE")) {
    console.log("内容脚本接收到：" + event.data.text);
    port.postMessage(event.data.text);
  }
}, false);
```

```
document.getElementById("theButton").addEventListener("click",
    function() {
  window.postMessage({ type: "FROM_PAGE", text: "来自网页的问候！" }, "*");
}, false);
```
在上面的例子中，example.html（不是扩展程序的一部分）向自己发送消息，由内容脚本截获并检查，然后发送至扩展程序进程。通过这种方式，页面建立了与扩展程序之间的通信，通过类似的方式反过来也是可能的。


## 安全性考虑
当您编写内容脚本时，您应该注意两个安全问题。首先，注意不要向您插入内容脚本的网站引入安全隐患。例如，如果您的内容脚本从另一个网站接收内容（例如通过发出 [XMLHttpRequest](xhr.md)），一定要注意把内容插入当前页面前过滤可能的[跨站脚本攻击](https://en.wikipedia.org/wiki/Cross-site_scripting)。例如，首选 innerText 而不是 innerHTML 插入内容。当您在 HTTPS 页面上获取来自 HTTP 的内容时要特别小心，因为如果用户在不安全的网络环境中，HTTP 内容可能会因为网络中的[中间人攻击](http://en.wikipedia.org/wiki/Man-in-the-middle_attack)而遭到破坏。

第二，尽管在隔离环境中运行您的内容脚本提供了某些保护，但是如果您不加区分地使用来自网页的内容，恶意网页仍然可能攻击您的内容脚本。例如，以下形式是危险的：

```
var data = document.getElementById("json-data")
// 警告！可能会执行恶意脚本！
var parsed = eval("(" + data + ")")
```
```
var elmt_id = ...
// 警告！elmt_id可能为"); …恶意脚本… //"！
window.setTimeout("animate(" + elmt_id + ")", 200);
```
因此，改用更安全的不运行脚本的 API：

```
var data = document.getElementById("json-data")
// JSON.parse 不会运行攻击者的脚本。
var parsed = JSON.parse(data);
```
```
var elmt_id = ...
// 闭包形式的 setTimeout 不会运行脚本。
window.setTimeout(function() {
  animate(elmt_id);
}, 200);
```
## 引用扩展程序的文件
使用 [extension.getURL](extension.md#method-getURL) 获得扩展程序的文件 URL，您可以像任何其他 URL 一样使用获得的结果，如以下代码所示。

```
//用来显示 <扩展程序目录>/images/myimage.png 的代码：
var imgURL = chrome.extension.getURL("images/myimage.png");
document.getElementById("someImage").src = imgURL;
```

## 视频
以下视频（英文）讨论了内容脚本的重要概念
[第一个视频描述了内容脚本和隔离环境。](https://www.youtube.com/watch?v=laLudeUmXHM)

接下来的视频描述了消息传递，突出演示了[内容脚本如何向所属扩展发送请求](https://www.youtube.com/watch?v=B4M_a7xejYI)。
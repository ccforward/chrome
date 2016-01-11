# 后台网页 background_pages

扩展程序通常需要有一个长时间运行的脚本来管理一些任务或状态，而后台网页就是为这一目的而设立。

如架构概述所述，后台网页是一个 HTML 页面，在扩展程序的进程中运行。它在您的扩展程序整个生命周期中都存在，并且一次只有一个活动的实例。（例外：如果您的扩展程序使用[“分离式”隐身模式](developer.chrome.com/extensions/manifest/incognito.html)，则会为隐身窗口创建另一个实例。）

在一个具有后台页面的典型扩展程序中，用户界面（例如浏览器按钮或页面按钮以及选项页面）通过哑视图（dumb view）实现，当视图需要某些状态时，则从后台页面请求状态；当后台页面得知状态更改时，后台页面通知相应的视图更新。



## 清单文件
请在扩展程序的清单文件(manifest)中注册您的后台页面。通常情况下，后台页面不需要任何 HTML 标记，这种情况下后台页面可以单独使用 JavaScript文件实现，如下列代码所示：

manifest.json

```js
{
  "name": "我的扩展程序",
  ...
  "background": {
    "scripts": ["background.js"]
  },
  ...
}

```
后台页面将由扩展程序系统生成，包含 scripts 属性中列出的每一个文件。

如果您需要在您的后台页面中指定 HTML，您可以改用 page 属性：

```js
{
  "name": "我的扩展程序",
  ...
  "background": {
    "page": "background.html"
  },
  ...
}
```
如果您需要浏览器早些启动，例如显示通知，您可能还需要指定 "background" 权限。

## 详情

您可以通过直接的脚本调用在不同页面间通信，类似于在框架间通信。extension.getViews 方法返回属于您的扩展程序的所有活动页面的 window 对象，而 extension.getBackgroundPage 方法返回后台页面。

## 例子
以下代码片段演示了后台页面是如何与扩展程序中的其他页面通信的，同时也演示了您应该如何使用后台页面处理诸如用户单击之类的事件。

这一例子中的扩展程序有一个后台页面和多个创建自 image.html 的页面（使用 tabs.create）。

background.js

```js
// 单击浏览器按钮时作出反应。
chrome.browserAction.onClicked.addListener(function(tab) {
  var viewTabUrl = chrome.extension.getURL('image.html');
  var imageUrl = /* 某个图片的 URL */;

  // 查找扩展程序中的所有页面，找到我们可以使用的一个。
  var views = chrome.extension.getViews();
  for (var i = 0; i < views.length; i++) {
    var view = views[i];

    // 如果这一视图有正确的 URL 并且还未使用……
    if (view.location.href == viewTabUrl && !view.imageAlreadySet) {

      // ……调用其中一个函数并设置属性。
      view.setImageUrl(imageUrl);
      view.imageAlreadySet = true;
      break; // 完成
    }
  }
});
```
image.html

```html
<html>
  <script>
    function setImageUrl(url) {
      document.getElementById('target').src = url;
    }
  </script>

  <body>
    <p>
    图片在此：
    </p>

    <img id="target" src="white.png" width="640" height="480">

  </body>
</html>
```

## 方法



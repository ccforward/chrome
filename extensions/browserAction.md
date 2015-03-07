# chrome.browserAction

* 描述： 使用浏览器按钮可以在 Google Chrome 浏览器主窗口中地址栏右侧的工具栏中添加图标。除了图标，浏览器按钮还可以有工具提示、徽章和弹出内容。 
* 从 Chrome 5 开始稳定支持
* 清单文件(manifest)： "browser_action": {...} 

在下图中，地址栏右侧多种颜色的正方形是一个浏览器按钮的图标，图标下有一弹出内容。

![](http://developer.chrome.com/static/images/browser-action.png)

如果您想创建一个不是永远可见的图标，请使用[页面按钮](extensions/pageAction.md)，而不是浏览器按钮。

## 清单文件

如下所示在扩展程序的[清单文件](extensions/manifest.md)中注册您的浏览器按钮

manifest.json

```
{
  "name": "我的扩展程序",
  ...
  "browser_action": {
    "default_icon": {                    // 可选
      "19": "images/icon19.png",           // 可选
      "38": "images/icon38.png"            // 可选
    },
    "default_title": "Google Mail",      // 可选，在工具提示中显示
    "default_popup": "popup.html"        // 可选
  },
  ...
}
```

如果您只提供 19px 或 38px 图标大小中的一个，扩展程序系统将会缩放您提供的图标，以适应用户显示器的像素密度，这有可能会丢失细节或使它看上去很模糊。注册默认图标的旧语法仍然支持

manifest.json

```
{
  "name": "我的扩展程序",
  ...
  "browser_action": {
    ...
    "default_icon": "images/icon19.png"  // 可选
    // 等价于 "default_icon": { "19": "images/icon19.png" }
  },
  ...
}
```




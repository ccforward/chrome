# 丰富的通知

> 警告：网络通知 API 中的 webKitNotifications.createHTMLNotification() 已弃用，新的 网络通知 API 只允许文本通知。Chrome 浏览器的通知 API 很快就会在稳定版中支持，网络通知将更新为新的丰富通知格式。


使用丰富的通知告诉用户发生了一些重要的事。通知出现在浏览器窗口的外面，如下面的截图所示，通知的显示方式与位置的具体细节取决于具体平台。

![](https://developer.chrome.com/static/images/notification-windows.png)

![](https://developer.chrome.com/static/images/notification-mac.png)

![](https://developer.chrome.com/static/images/notification-linux.png)

您只要用一点点 JavaScript 代码以及可选的一个与您的扩展程序一起打包的 HTML 页面就能创建通知窗口。

##例子

首先在您的清单文件中声明 notifications 权限：

manifest.json

```
{
  "name": "我的扩展程序",
  "manifest_version": 2,
  ...
  "permissions": [
    "notifications"
  ],
  ...
  // 注意：由于 bug 134315，您必须将您希望与 createNotification()
  // 一起使用的所有图片声明为可以在网页中访问的资源。
  "web_accessible_resources": [
    "48.png"
  ],
}
```
然后，使用 webkitNotifications 对象创建通知：

```
// 注意：没有必要调用 webkitNotifications.checkPermission()。
// 声明了 notifications 权限的扩展程序总是允许创建通知。

// 创建一个简单的文本通知：
var notification = webkitNotifications.createNotification(
  '48.png',  // 图标 URL，可以是相对路径
  '您好！',  // 通知标题
  '内容（Lorem ipsum...）'  // 通知正文文本
);

// 或者创建 HTML 通知：
var notification = webkitNotifications.createHTMLNotification(
  'notification.html'  // HTML 的 URL，可以是相对路径
);

// 然后显示通知。
notification.show();
```

## API 参考
参见[桌面通知规范草案](http://dev.chromium.org/developers/design-documents/desktop-notifications/api-specification)（英文）。

## 与其他视图通信

您可以使用 extension.getBackgroundPage 和 extension.getViews 在您的扩展程序中实现通知与其他视图间的通信。例如：

notification.js

```
chrome.extension.getBackgroundPage().doThing();
```

background.js

```
chrome.extension.getViews({type:"notification"}).forEach(function(win) {
  win.doOtherThing();
});
```

## 更多例子

您可以在 [examples/api/notifications](http://src.chromium.org/viewvc/chrome/trunk/src/chrome/common/extensions/docs/examples/api/notifications/) 目录中找到一个使用通知的简单例子。有关其他例子以及查看源代码的帮助，请参见[示例](http://developer.chrome.com/extensions/samples.html)。

另外，您也可以参见 html5rocks.com（英文）上的[通知教程](http://www.html5rocks.com/tutorials/notifications/quick/)。请忽略与权限有关的代码，如果您声明了 "notifications" 权限它们就没有必要了。
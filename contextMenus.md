# chrome.contextMenus

* 描述： 使用 chrome.contextMenus API 向 Google Chrome 浏览器的右键菜单添加项目。您可以选择您在右键菜单中添加的项目应用于哪些类型的对象，例如图片、超链接和页面。 
* 从 Chrome 6 开始稳定支持。 
* 权限："contextMenus" 

## 用法

右键菜单项可以在任何文档（或文档中的框架）中出现，即使它们使用 file:// 或 chrome:// 协议的 URL。要控制您的菜单项在哪些文档中出现，在您调用 create() 或 update() 方法时请指定 documentUrlPatterns 属性。

您可以根据自己的需要创建任意数目的右键菜单项，但是如果您的扩展程序一次显示超过一个菜单项，Google Chrome 浏览器会自动将它们折叠至一个父菜单中。


## 清单文件

您必须在扩展程序的清单文件中声明 "contextMenus" 权限才能使用有关 API，您还应该指定一个 16×16 像素的图标，显示在您的菜单项旁边。例如：

manifest.json

```
{
  "name": "我的扩展程序",
  ...
  "permissions": [
    "contextMenus"
  ],
  "icons": {
    "16": "icon-bitty.png",
    "48": "icon-small.png",
    "128": "icon-large.png"
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

## 例子

您可以在 [extensions/samples](https://developer.chrome.com/extensions/samples#search:contextMenus) 目录中找到简单例子。


## 方法

每个方法的具体参数属性直接看[API](https://developer.chrome.com/extensions/contextMenus)

### create
`rome.browserAction.setTitle(object details)`
创建一个新的右键菜单项。注意，如果创建过程中发生错误，您可能要等到回调函数调用时才能得知。

### update
`chrome.contextMenus.update(integer or string id, object updateProperties, function callback)`


更新以前创建的菜单项。

### remove

`chrome.contextMenus.remove(integer or string menuItemId, function callback)`

移除右键菜单项。

### removeAll

`chrome.contextMenus.removeAll(function callback)`

移除该扩展程序添加的所有右键菜单项。

## 事件


### onClicked

`chrome.contextMenus.onClicked.addListener(function callback)`

当右键菜单项单击时产生。


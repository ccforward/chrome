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

## 用户界面的几个部分
浏览器按钮可以包含图标、工具提示、徽章和弹出内容。

### 图标

浏览器按钮图标的宽度和高度最大可以为 19 dip（设备无关像素），更大的图标会被缩小。为了最佳结果，请使用 19 dip 大小的正方形图标。

您可以通过两种方式设置图标：使用静态图像或者使用 HTML5 canvas 元素。对于简单的应用来说，使用静态图像更方便，但是您可以使用 canvas 元素创建动态的用户界面，例如平滑的动画。

静态图像可以是 WebKit 能够显示的任何格式，包括 BMP、GIF、ICO、JPEG 或 PNG。对于未打包的扩展程序来说，图片必须是 PNG 格式。

要设置图标，请使用清单文件中 browser_action 部分的 default_icon 属性，或者调用 setIcon 方法。

要在屏幕像素密度（比值 size_in_pixel / size_in_dip）不为 1 的时候正确显示图标，图标可以定义为一组不同大小的图片。实际显示的图片将从这一组图标中选取，以便最佳匹配 19 dip 图标的像素大小。目前一组图标可以包含像素大小为 19 和 38 的图片。

### 工具提示

要设置工具提示，使用清单文件中 browser_action 部分的 default_title 属性，或者调用 setTitle 方法。对于 default_title 属性，您可以指定区域相关的字符串，有关更多细节请参考[国际化支持](extensions/i18n.md)。

### 徽章

浏览器按钮可以选择在图标上显示徽章，即一小段文字。徽章使得更新浏览器按钮更容易，来显示有关扩展程序状态的一点点信息。

因为徽章所拥有的空间有限，它不应超过四个字符。

分别调用 setBadgeText 和 setBadgeBackgroundColor 设置徽章的内容和颜色。

### 弹出内容

如果浏览器按钮有弹出内容，当用户单击图标时会出现弹出内容。弹出内容可以包含任何您需要的 HTML 内容，并且会自动调整大小以适应内容。

要向您的浏览器按钮添加弹出内容，请创建一个 HTML 文件存放弹出内容，并在清单文件中 browser_action 部分的 default_popup 属性指定该 HTML 文件或者调用 setPopup 方法。

## 提示
为了达到最好的视觉效果，请遵循下面的指导：

* 对于适用于大多数页面的功能请使用浏览器按钮。
* 对于仅适用于少数页面的内容请不要使用浏览器按钮，而应该使用页面按钮。
* 请使用较大的、颜色丰富的图标，充分利用 19×19 像素的空间。浏览器按钮的图标应该看上去比页面按钮的图标更大一些、更明显一些。
* 请不要试着模仿 Google Chrome 浏览器的单色 Chrome 菜单图标。这样与主题背景不协调，并且无论如何，扩展程序应该更加突出一点。
* 请使用 Alpha 透明，使您的图标边框更加平滑。因为很多人使用主题背景，您的图标应该在各种背景颜色下看着舒服。
* 请不要一直使用动画图标，那样只会显得很烦人。

## 例子

您可以在 [examples/api/browserAction](http://src.chromium.org/viewvc/chrome/trunk/src/chrome/common/extensions/docs/examples/api/browserAction/) 目录中找到使用浏览器按钮的简单例子。有关其他示例和查看源代码的帮助，请参见[示例](https://developer.chrome.com/extensions/samples)。

# chrome.browserAction 参考

[详细最新的API地址](https://developer.chrome.com/extensions/browserAction)

## 类型
### ColorArray
array of integer
### ImageDataType
图片的像素数据，必须为 ImageData 对象（例如来自 canvas 元素）。

## 方法

### setTitle
`rome.browserAction.setTitle(object details)`

设置浏览器按钮的标题，显示在工具提示中。

参数

* details ( object )

属性
	* title ( string )
	当鼠标移到浏览器按钮上时应显示的字符串。
	* tabId ( optional integer )

将更改限制在当某一特定标签页选中时应用，当该标签页关闭时，更改的内容自动恢复。

### getTitle
`chrome.browserAction.getTitle(object details, function callback)`

获取浏览器按钮的标题。

### setIcon

`chrome.browserAction.setIcon(object details, function callback)`

设置浏览器按钮的图标。图标既可以指定为图片文件的路径，也可以指定来自 canvas 元素的像素数据，或者这两者中任意一个的词典。path 或 imageData 属性中有且只有一个必须指定。

### setPopup

`chrome.browserAction.setPopup(object details)`

设置当用户单击浏览器按钮时显示为弹出内容的 HTML 文档。

### getPopup

`chrome.browserAction.getPopup(object details, function callback)`

获取设置为浏览器按钮弹出内容的 HTML 文档。

### setBadgeText

`chrome.browserAction.setBadgeText(object details)`

设置浏览器按钮上的徽章，显示在图标上。

### getBadgeText

`chrome.browserAction.getBadgeText(object details, function callback)`

获取浏览器按钮上的徽章，如果没有指定标签页，则返回用于所有标签页的徽章。

### setBadgeBackgroundColor

`chrome.browserAction.setBadgeBackgroundColor(object details)`
设置徽章的背景颜色。

### getBadgeBackgroundColor

`chrome.browserAction.getBadgeBackgroundColor(object details, function callback)`

获取浏览器按钮上的徽章，如果没有指定标签页，则返回用于所有标签页的徽章。

### enable

`chrome.browserAction.enable(integer tabId)`

为某一标签页启用浏览器按钮。默认情况下，浏览器按钮是启用的。

### disable

`chrome.browserAction.disable(integer tabId)`

为某一标签页禁用浏览器按钮。

## 事件

### onClicked

浏览器按钮的图标单击时产生，如果浏览器按钮有弹出内容则不会触发该事件。

#### addListener

`chrome.browserAction.onClicked.addListener(function callback)`

参数

* callback ( function )  
  callback 参数应该指定一个如下形式的函数：

  `function(tabs.Tab tab) {...};`

* tab ( tabs.Tab )


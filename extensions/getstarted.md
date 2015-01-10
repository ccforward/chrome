# 入门：创建chrome的扩展程序

扩展程序允许你为 Chrome 浏览器增加功能，但不需要深入研究本机代码。可以使用在网页开发中已经很熟悉的前端技术来开发新的扩展程序。

下面将实现一个称为浏览器按钮的用户界面元素，将一个可单击的按钮放在浏览器地址栏的右边，单击按钮弹出弹出窗口，显示很多猫。

不翻译废话了，直接开编辑器一步一步的写吧：

## 要声明的内容
我们要创建一个清单文件 manifest.json 。这是一个 JSON 格式的目录，包含了扩展程序的名称、描述、版本号等属性。从更高层次来看，我们使用它来向 Chrome 浏览器生命扩展程序将会做什么以及所需要的权限。

为了显示猫，应该告诉 Chrome 要创建一个按钮，还希望自由访问某个特定来源的猫。

创建manifest.json:

```
{
  "manifest_version": 2,

  "name": "One-click Kittens",
  "description": "This extension demonstrates a browser action with kittens.",
  "version": "1.0",

  "permissions": [
    "https://secure.flickr.com/"
  ],
  "browser_action": {
    "default_icon": "icon.png",
    "default_popup": "popup.html"
  }
}
```
### manifest.json 文件的含义

第一行生命我们使用的manifest版本为 2 （1已经过时了，废弃）

接下来的部分定义扩展程序的名称、描述、版本。这些会现用户显示已安装的扩展程序，同时在Chrome的商店中向潜在的新用户推广

最后一部分是权限请求，用于访问https://secure.flickr.com/上的数据，同时声明了该扩展实现了一个浏览器按钮，并为它指定一个默认图标和弹出窗口

### 资源
manifest文件指向了两个资源文件：icon.png 和 popup.html。 这两个资源必须在扩展程序包中存在，所以必须要创建。

icon.png 会在地址栏右侧出现，等待用户交互。  
popup.html 文件还需要额外的js，直接从google代码库[下载]()

现在工作目录中应该有四个文件： icon.png、manifest.json、popup.html、popup.js 。下一步就是加载。

### 加载扩展程序
从Chrome商店上下载的扩展程序打包为 .crx 文件，便于发布但不便于开发，Chrome提供了可以加载工作目录用于测试的方式：

1. 在Chrome中访问 chrome://extensions
2. 选中右上角开发者模式
3. 单击加载正在开发的扩展程序，弹出对话框
4. 选择自己的工作目录

如果加载无效，顶部将会显示错误消息。

### 修改代码
现在已经准备好了第一个扩展程序，可以尝试做些修改，修改popup.js：将 var QUERY = 'kittens'; 改为 var QUERY = 'puppies';

保存后在单击扩展程序的浏览器按钮，是没有更新到新的效果的。需要让Chrome知道发生了更改，回到 chrome://extensions 中单击重新加载或者刷新页面，即完成更新。

### 接下来
* 概述会对Chrome扩展做进一步的介绍
* 调试教程非常适合帮助代码Debug
* Chrome 扩展程序能够访问强大的 API，远远超过了普通网页可用的部分：浏览器按钮就是冰山一角。chrome.* API 文档将让你了解每一个 API。
* 开发者指南包含了很多额外的链接，指向你感兴趣的文档。




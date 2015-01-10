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



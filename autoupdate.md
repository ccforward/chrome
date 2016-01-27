# 自动更新 autoupdate

我们希望扩展程序能够像Google Chrome浏览器一样自动更新，以便加入漏洞及安全补丁，增加新特性，增强性能，改善用户界面。

如果您使用 [Chrome 开发者信息中心](https://chrome.google.com/webstore/developer/dashboard)发布您的扩展程序，您可以忽略这一页面。您可以利用信息中心向用户以及 Chrome 网上应用店发布您的扩展程序的更新版本。

如果您想在网上应用店以外的地方托管您的扩展程序，请继续读下去。您还应该阅读[托管](http://developer.chrome.com/extensions/hosting.html)以及[打包](http://developer.chrome.com/extensions/packaging.html)。

## 概述

* 扩展程序的清单文件可以包含 "update_url" 属性，指向检查更新的位置。
* 检查更新返回的内容为一个更新清单 XML 文档，列出扩展程序的最新版本。

每隔几小时，浏览器都会检查已安装的扩展程序是否有更新 URL。浏览器会对每一个扩展程序的更新 URL 发出请求，查询更新清单 XML 文件。如果更新清单提到了比已安装的扩展程序更新的版本，浏览器下载并安装新版本。与手动更新相同，新的 .crx 文件必须与已安装的扩展程序使用同一私有密钥签名。

## 更新 URL
如果您要托管自己的扩展程序，您需要在您的 manifest.json 文件中添加 "update_url" 属性，如下所示：

manifest.json

```js
{
  "name": "我的扩展程序",
  ...
  "update_url": "http://myhost.com/mytestextension/updates.xml",
  ...
}
```
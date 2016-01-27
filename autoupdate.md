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


## 更新清单

服务器返回的更新清单应该为一个如下形式的 XML 文档（高亮部分表示您需要修改的部分）：

updates.xml

```xml
<?xml version='1.0' encoding='UTF-8'?>
<gupdate xmlns='http://www.google.com/update2/response' protocol='2.0'>
  <app appid='aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa'>
    <updatecheck codebase='http://myhost.com/mytestextension/mte_v2.crx' version='2.0' />
  </app>
</gupdate>
```

这一 XML 格式借用了 Omaha（Google 用于更新的基础架构）的格式，有关细节请参见 https://code.google.com/p/omaha/（英文）。扩展程序系统使用更新清单中 <app> 和 <updatecheck> 元素的以下属性：

* appid  
  扩展程序或 Chrome 应用标识符，基于公共密钥的散列值生成，如打包部分所述。您可以进入扩展程序页面（chrome://extensions）找到您的扩展程序标识符。

* codebase  
指向扩展程序 .crx 文件的 URL。

* version  
  用于客户端，确定是否应该下载 codebase指定的 .crx 文件。这一属性应该与 .crx 文件中 manifest.json 文件中的 "version" 属性一致。

更新清单 XML 文件可以包含多个 <app> 元素，描述不同扩展程序的信息。

## 测试

默认更新检查频率为几个小时，但是您可以使用扩展程序页面中的i立即更新扩展程序按钮强制更新。

另一个选择是使用 --extensions-update-frequency 命令行参数，以秒为单位，设置更频繁的更新间隔。例如，要每隔 45 秒就检查更新，像这样运行 Google Chrome 浏览器：

```
chrome.exe --extensions-update-frequency=45
```

注意，这会影响到所有已安装的扩展程序，所以您应该考虑到这会带来的带宽以及服务器负载。您可能需要临时卸载除了您正在测试的扩展程序以外的所有其他扩展程序，并且在正常的浏览器使用过程中不应该以这一选项运行浏览器。

## 高级用法：请求参数

基本的自动更新机制使得服务器端的工作十分简单，只要在任何普通的 Web 服务器（例如 Apache）上加入一个静态 XML 文件，并在发布您的扩展程序的新版本时更新此 XML 文件即可。

更高级的开发者可能希望利用我们请求更新清单时附加的参数，指示扩展程序的标识符和版本。这样，就可以对所有扩展程序都使用相同的更新 URL，指向运行动态服务器端代码而不是静态 XML 文件的 URL。

请求参数的格式为：

```
  ?x=<extension_data>
```

其中 <extension_data> 为如下格式的内容经过 URL 编码后所得的字符串：

```
  id=<id>&v=<version>
```

例如，假设您有两个扩展程序，都指向相同的更新 URL（http://test.com/extension_updates.php）：

	* 扩展程序 1
	  标识符："aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa"
	  版本："1.1"
	* 扩展程序 2
	  标识符："bbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb"
	  版本："0.4"

则向每一个单独的扩展程序发出的请求分别为：

	* http://test.com/extension_updates.php?x=id%3Daaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa%26v%3D1.1
	* http://test.com/extension_updates.php?x=id%3Dbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb%26v%3D0.4


对于每一个唯一的更新 URL，多个扩展程序可以在一个请求中列出。对于上述例子，如果用户安装了这两个扩展程序，则两个请求合并为一个请求：

``` 
http://test.com/extension_updates.php?x=id%3Daaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa%26v%3D1.1&x=id%3Dbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb%26v%3D0.4
```

如果已安装的扩展程序数目太多，使用相同的更新 URL 使得 GET 请求 URL 太长（大约 2000 个字符以上），更新检查程序会发送必要的附加 GET 请求。

**注意： 将来可能会发出单个 POST 请求，包含请求参数，而不是发出多个 GET 请求。**



# chrome.storage

* 描述：	使用 chrome.storage API 存储、获取用户数据，追踪用户数据的更改。 
* 可用版本：	从 Chrome 20 开始稳定支持。 
* 权限：	"storage" 
* 了解更多：	[Chrome 应用办公时间：Chrome 存储 API ](https://developers.google.com/live/shows/7320022/) 
	[Chrome 应用办公时间：存储 API 深入探索](https://developers.google.com/live/shows/7320022-1/)


## 概述

这一 API 为扩展程序的存储需要而特别优化，它提供了与 [localStorage API](https://developer.mozilla.org/en/DOM/Storage#localStorage) 相同的能力，但是具有如下几个重要的区别：

	* 用户数据可以通过 Chrome 浏览器的同步功能自动同步（使用 `storage.sync`）。
	* 您的扩展程序的内容脚本可以直接访问用户数据，而不需要后台页面。
	* 即使使用[分离式隐身行为](http://developer.chrome.com/extensions/manifest/incognito.html)，用户的扩展程序设置也会保留。
	* 它是异步的，并且能够进行大量的读写操作，因此比阻塞和串行化的 `localStorage API` 更快。
	* 用户数据可以存储为对象（`localStorage API` 以字符串方式存储数据）。
	* 可以读取管理员为扩展程序配置的企业策略（使用 `storage.managed` 和 [架构](http://developer.chrome.com/extensions/manifest/storage.html) ）。

## 清单文件

您必须在扩展程序的清单文件中声明 "storage" 权限才能使用存储 API。例如：

manifest.json

```js
{
  "name": "我的扩展程序",
  ...
  "permissions": [
    "storage"
  ],
  ...
}
```
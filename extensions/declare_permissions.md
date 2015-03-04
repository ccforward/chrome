# 声明权限

要使用大多数 chrome.* API，你的扩展程序或应用程序必须在清单文件的 "permissions" 字段中声明它的意图。每一个权限既可以是已知字符串列表中的某一个（例如 "geolocation"），也可以是授予访问一个或多个主机权限的匹配表达式。权限可以帮助你在扩展程序或应用程序受到攻击时尽可能减小损失，某些权限也会在安装前向用户显示，这些将在权限警告中详细描述。

如果某个 API 需要你在清单文件中声明某个权限，它的文档会告诉你如何去做，例如存储 API 网页会向你演示如何声明 "storage" 权限。

如下是一个清单文件中权限部分的例子

```
"permissions": [
  "tabs",
  "bookmarks",
  "http://www.blogger.com/",
  "http://*.google.com/",
  "unlimitedStorage"
],
```

下表列出了当前可用的权限。

* "[scheme]:[host]/*"	指定主机权限。如果扩展程序或应用需要与页面上运行的代码交互则必须指定该权限。许多扩展程序的能力，例如跨站 XMLHttpRequest、以编程方式插入内容脚本以及扩展程序的 Cookie API 都需要主机权限。有关语法上的详情，请参见匹配表达式。允许指定路行，但始终视为 /*。
* "activeTab"	根据 activeTab 规范请求授予扩展程序权限。
* "alarms"	使你的扩展程序能够访问 chrome.alarms API。
* "background"	
* 让 Chrome 很早就启动很晚才退出，以便应用和扩展程序可以有更长的生命周期。

如果已安装的任何托管应用、打包应用或扩展程序拥有 "background" 权限，Chrome 浏览器在用户登录计算机时就会（不可见地）运行，那时用户还没有亲自运行 Chrome 浏览器。"background" 权限也会使 Chrome 浏览器继续运行（即使在最后一个窗口已经关闭），直到用户主动退出 Chrome 浏览器。

注意：已禁用的应用和扩展程序以未安装对待。
通常你使用 "background" 权限时，同时也会使用后台网页、事件页面或（对于托管应用）后台窗口。

* "bookmarks"	使你的扩展程序能够访问 chrome.bookmarks API。
* "browsingData"	使你的扩展程序能够访问 chrome.browsingData API。
* "clipboardRead"	如果扩展程序或应用使用 document.execCommand('paste') 则必须指定。
* "clipboardWrite"	表示扩展程序或应用可以使用 document.execCommand('copy') 或 document.execCommand('cut')。托管应用必须指定该权限，建议扩展程序和打包应用也指定该权限。
* "contentSettings"	使你的扩展程序能够访问 chrome.contentSettings API。
* "contextMenus"	使你的扩展程序能够访问 chrome.contextMenus API。
* "cookies"	使你的扩展程序能够访问 chrome.cookies API。
* "debugger"	使你的扩展程序能够访问 chrome.debugger API。
* "declarativeContent"    使你的扩展程序能够访问 chrome.declarativeContent API。
* "declarativeWebRequest"	使你的扩展程序能够访问 chrome.declarativeWebRequest API。
* "desktopCapture"	使你的扩展程序能够访问 chrome.desktopCapture API。
* "dns"	使你的扩展程序能够访问 chrome.dns API。
* "downloads"	使你的扩展程序能够访问 chrome.downloads API。
* "experimental"	如果扩展程序或应用使用 chrome.experimental.* API 则必须指定。
* "fileBrowserHandler"	使你的扩展程序能够访问 chrome.fileBrowserHandler API。
* "fontSettings"	使你的扩展程序能够访问 chrome.fontSettings API。
* "gcm"	使你的扩展程序能够访问 chrome.gcm API。
* "geolocation"	允许扩展程序或应用使用提议的 HTML5 地理定位 API 而不需要提示用户权限。
* "history"	使你的扩展程序能够访问 chrome.history API。
* "identity"	使你的扩展程序能够访问 chrome.identity API。
* "idle"	使你的扩展程序能够访问 chrome.idle API。
* "idltest"	使你的扩展程序能够访问 chrome.idltest API。
* "infobars"	使你的扩展程序能够访问 chrome.infobars API。
* "location"	使你的扩展程序能够访问 chrome.location API。
* "management"	使你的扩展程序能够访问 chrome.management API。
* "notifications"	允许扩展程序使用提议的 HTML5 通知 API 而不用调用权限方法（例如 checkPermission()）。有关更多信息，请参见桌面通知。
* "pageCapture"	使你的扩展程序能够访问 chrome.pageCapture API。
* "power"	使你的扩展程序能够访问 chrome.power API。
* "privacy"	使你的扩展程序能够访问 chrome.privacy API。
* "processes"	使你的扩展程序能够访问 chrome.processes API。
* "proxy"	使你的扩展程序能够访问 chrome.proxy API。
* "pushMessaging"	使你的扩展程序能够访问 chrome.pushMessaging API。
* "sessions"	使你的扩展程序能够访问 chrome.sessions API。
* "signedInDevices"	使你的扩展程序能够访问 chrome.signedInDevices API。
* "storage"	使你的扩展程序能够访问 chrome.storage API。
* "system.cpu"	使你的扩展程序能够访问 chrome.system.cpu API。
* "system.display"	使你的扩展程序能够访问 chrome.system.display API。
* "system.memory"	使你的扩展程序能够访问 chrome.system.memory API。
* "system.storage"	使你的扩展程序能够访问 chrome.system.storage API。
* "tabCapture"	使你的扩展程序能够访问 chrome.tabCapture API。
* "tabs"	使你的扩展程序能够访问 $ref:[tabs.Tab Tab] 对象的特权字段，包括 $ref:[tabs chrome.tabs] 和 $ref:[windows chrome.windows] 在内的多种 API 都使用 Tab 对象。在很多情况下，你的扩展程序不需要声明 "tabs" 权限就能使用这些 API。
* "topSites"	使你的扩展程序能够访问 chrome.topSites API。
* "tts"	使你的扩展程序能够访问 chrome.tts API。
* "ttsEngine"	使你的扩展程序能够访问 chrome.ttsEngine API。
* "unlimitedStorage"	提供无限的存储空间，保存 HTML5 客户端数据，例如数据库以及本地存储文件。如果没有这一权限，扩展程序或应用的本地存储将限制在 5 MB 以内。
注意：该权限仅应用于网络 SQL 数据库以及应用程序缓存（参见问题 58985）。另外，当前还不能和包含通配符的子域名一起使用，例如 http://*.example.com。
* "webNavigation"	使你的扩展程序能够访问 chrome.webNavigation API。
* "webRequest"	使你的扩展程序能够访问 chrome.webRequest API。
* "webRequestBlocking"	如果扩展程序以阻塞方式使用 chrome.webRequest API 则必须指定。
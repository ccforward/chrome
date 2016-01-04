# 跨站 XMLHttpRequest

普通网页可以通过 [XMLHttpRequest](http://www.w3.org/TR/XMLHttpRequest/) 对象向远程服务器发送和接收数据，但是它们受到[同源策略](http://en.wikipedia.org/wiki/Same_origin_policy)的限制。扩展程序不受这一限制，只要首先请求跨站权限，扩展程序就可以与来源范围外的远程服务器通信。

## 扩展程序的来源
每一个运行中的扩展程序在它自己的安全来源中存在，如果不请求额外的权限，扩展程序只能使用 XMLHttpRequest 获取自己的资源。例如，如果扩展程序包含一个名为 config.json 的 JSON 配置文件，位于 config_resources 文件夹中，扩展程序可以像这样获取文件内容：

```
var xhr = new XMLHttpRequest();
xhr.onreadystatechange = handleStateChange; // 在其他地方实现。
xhr.open("GET", chrome.extension.getURL('/config_resources/config.json'), true);
xhr.send();
```
如果扩展程序尝试使用除了它自己的安全来源，例如 http://www.google.com，除非扩展程序已经请求了相应的跨站权限，浏览器会禁止这一请求。

## 请求跨站权限
通过向[清单文件](manifest.md)的 [declare_permissions.md](permissions) 部分添加主机或主机匹配表达式，扩展程序可以请求访问位于自己的来源外的远程服务器。

```
{
  "name": "我的扩展程序",
  ...
  "permissions": [
    "http://www.google.com/"
  ],
  ...
}
```

跨站权限值可以是完整的主机名，例如：

* "http://www.google.com/"
* "http://www.gmail.com/"

或者也可以是匹配表达式，例如：

* "http://*.google.com/"
* "http://*/"

"http://*/" 这一匹配表达式允许所有可及域名的访问。注意，这里的匹配表达式和[内容脚本匹配表达式](match_patterns.md)类似，但是主机后的任何路径信息都将被忽略。

同时注意访问权限是通过主机和协议同时授予的，如果扩展程序需要指定某个主机安全和非安全的 HTTP 访问，必须分开声明权限：

```
"permissions": [
  "http://www.google.com/",
  "https://www.google.com/"
]
```

## 安全性考虑
当使用通过 XMLHttpRequest 获取的资源时，您的后台网页应该小心，以免跨站脚本攻击。特别地，避免使用如下一些危险的 API：

```
var xhr = new XMLHttpRequest();
xhr.open("GET", "http://api.example.com/data.json", true);
xhr.onreadystatechange = function() {
  if (xhr.readyState == 4) {
    // 警告！可能会执行恶意脚本！
    var resp = eval("(" + xhr.responseText + ")");
    ...
  }
}
xhr.send();
```
```
var xhr = new XMLHttpRequest();
xhr.open("GET", "http://api.example.com/data.json", true);
xhr.onreadystatechange = function() {
  if (xhr.readyState == 4) {
    // 警告！可能会插入恶意脚本
    document.getElementById("resp").innerHTML = xhr.responseText;
    ...
  }
}
xhr.send();
```
因此，您应该首选更安全的不运行脚本的 API：

```
var xhr = new XMLHttpRequest();
xhr.open("GET", "http://api.example.com/data.json", true);
xhr.onreadystatechange = function() {
  if (xhr.readyState == 4) {
    // JSON.parse 不执行攻击者的脚本。
    var resp = JSON.parse(xhr.responseText);
  }
}
xhr.send();
```

```
var xhr = new XMLHttpRequest();
xhr.open("GET", "http://api.example.com/data.json", true);
xhr.onreadystatechange = function() {
  if (xhr.readyState == 4) {
    // innerText 不会让攻击者插入 HTML 元素。
    document.getElementById("resp").innerText = xhr.responseText;
  }
}
xhr.send();
```

另外，通过 HTTP 接收资源时尤其要小心。如果你的扩展程序在不安全的网络环境中使用，网络攻击者（即中间人攻击）可以修改响应，甚至可能攻击您的扩展程序。请尽可能地在可能的情况下首选 HTTPS。

### 与内容安全策略的关系
如果您在你的清单文件中添加 content_security_policy 属性修改了应用或扩展程序默认的[内容安全策略](contentSecurityPolicy.md)，就需要确保您需要连接的所有主机都受到允许。尽管默认策略不限制连接到哪些主机，当你显式添加 connect-src 或 default-src 指示符时要特别注意。
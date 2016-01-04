# 选项

为了让用户自定义您的扩展程序的行为，您可能会提供一个选项页面。如果这样做的话，选项页面的链接将会在扩展程序管理页面（chrome://extensions）中显示，单击“选项”链接打开一个新标签页，指向您的选项页面。

## 第一步：在清单文件中声明您的选项页面

manifest.json

```
{
  "name": "我的扩展程序",
  ...
  "options_page": "options.html",
  ...
}
```

## 第二步：编写您的选项页面

如下是一个选项页面的例子：

options.html

``` html
<html>
<head><title>我的测试扩展程序选项</title></head>

<body>

我最喜欢的颜色：
<select id="color">
 <option value="red">红</option>
 <option value="green">绿</option>
 <option value="blue">蓝</option>
 <option value="yellow">黄</option>
</select>

<br>
<div id="status"></div>
<button id="save">保存</button>
</body>

<script src="options.js"></script>

</html>
```

options.js

```
// 将选项保存在 localStorage 中。
function save_options() {
  var select = document.getElementById("color");
  var color = select.children[select.selectedIndex].value;
  localStorage["favorite_color"] = color;

  // 更新状态，告诉用户选项已保存。
  var status = document.getElementById("status");
  status.innerHTML = "Options Saved.";
  setTimeout(function() {
    status.innerHTML = "";
  }, 750);
}

// 从保存在 localStorage 中的值恢复选定的内容。
function restore_options() {
  var favorite = localStorage["favorite_color"];
  if (!favorite) {
    return;
  }
  var select = document.getElementById("color");
  for (var i = 0; i < select.children.length; i++) {
    var child = select.children[i];
    if (child.value == favorite) {
      child.selected = "true";
      break;
    }
  }
}
document.addEventListener('DOMContentLoaded', restore_options);
document.querySelector('#save').addEventListener('click', save_options);
```


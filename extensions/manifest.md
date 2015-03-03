#清单文件格式

每一个扩展程序、可安装的网络应用以及主题背景都有一个 [JSON](http://www.json.org/) 格式的清单文件，名为 manifest.json，提供重要信息。

## 字段概述

如下代码展示了支持的清单文件字段，以及讨论每一个字段的链接。只有 name 和 version 字段是必需的。

```
{
  // 必选
  "manifest_version": 2,
  "name": "我的扩展程序",
  "version": "版本字符串",

  // 推荐
  "default_locale": "en",
  "description": "纯文本描述",
  "icons": {...},

  // 选择某一个（或者无）
  "browser_action": {...},
  "page_action": {...},

  // 可选
  "author": ...,
  "background": {
    // 推荐
    "persistent": false
  },
  "background_page": ...,
  "chrome_settings_overrides": ...,
  "chrome_url_overrides": {...},
  "commands": {
    "global": ...
  },
  "content_pack": ...,
  "content_scripts": [{...}],
  "content_security_policy": "策略字符串",
  "converted_from_user_script": ...,
  "current_locale": ...,
  "devtools_page": ...,
  "externally_connectable": {
    "matches": ["*://*.example.com/*"]
  },
  "file_browser_handlers": [...],
  "homepage_url": "http://path/to/homepage",
  "import": ...,
  "incognito": "spanning 或 split",
  "input_components": ...,
  "key": "公钥",
  "minimum_chrome_version": "版本字符串",
  "nacl_modules": [...],
  "oauth2": ...,
  "offline_enabled": true,
  "omnibox": {
    "keyword": "aString"
  },
  "optional_permissions": ...,
  "options_page": "aFile.html",
  "page_actions": ...,
  "permissions": [...],
  "platforms": ...,
  "plugins": [...],
  "requirements": {...},
  "sandbox": [...],
  "script_badge": ...,
  "short_name": "短名称",
  "signature": ...,
  "spellcheck": ...,
  "storage": {
    "managed_schema": "schema.json"
  },
  "system_indicator": ...,
  "tts_engine": ...,
  "update_url": "http://path/to/updateInfo.xml",
  "web_accessible_resources": [...]
}
```
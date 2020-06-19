---
title: 在Flutter中使用WebView
categories:
  - 前端
tags:
  - Flutter
date: 2020-04-15 16:38:11
---

#### 导包

在 **`pubspec.yml`** 中的 **`dependencies`** 下加入 *[webview_flutter](https://pub.flutter-io.cn/packages/webview_flutter)* 的 package

```yaml
dependencies:
  webview_flutter: 0.3.19+9
```

然后点击标签栏出现的 ***Packages get***，或者在终端输入 `flutter package get`

#### 新建 Widget

新建一个 WebViewWidget，这个 Widget 接收两个参数，分别是浏览器页面标题和浏览页面的 url

```dart
import 'package:flutter/material.dart';
import 'package:webview_flutter/webview_flutter.dart';

class Browser extends StatelessWidget {
  const Browser({Key key, this.url, this.title}) : super(key: key);

  final String url;
  final String title;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(title),
      ),
      body: WebView(
        initialUrl: url,
        javascriptMode: JavascriptMode.unrestricted,
      ),
    );
  }
}
```

#### 使用 WebView

使用的时候只需要跳转到该页面，并传入标题和网址即可

```dart
onPressed: () {
  Navigator.of(context)
      .push(new MaterialPageRoute(builder: (_) {
    return new Browser(
      url: "https://flutter-io.cn/",
      title: "Flutter 中文社区",
    );
  }));
}
```

#### iOS plist 设置

```xml
<key>io.flutter.embedded_views_preview</key>
<string>YES</string>
```

#### 设置 iOS 支持 http

在  iOS 模块的 `Runner` 中的 ***info.plist*** 文件中添加如下字段：

```xml
<key>NSAppTransportSecurity</key>
<dict>
<key>NSAllowsArbitraryLoads</key>
<true/>
</dict>
```

然后执行 `flutter clean` 后重新运行即可访问 HTTP 网页
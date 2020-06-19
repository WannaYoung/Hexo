---
title: 使用Mac自带脚本一键部署Hexo
categories:
  - 技巧
tags:
  - Hexo
date: 2020-03-27 11:19:55
---

网上 Hexo 自动部署流程大都是抓取 Git 提交，自动部署博客，常见的实现方式是: Travis CI 和 Git Hooks。 不过我不喜欢这种频繁提交 Git 来部署的方式，但是又不想每次打开终端使用命令行部署，于是想到使用Mac系统自带的AppleScript实现。

#### 在Hexo根目录新建 publish.sh 文件

```shell
rm -rf ~/Library/Caches/google/Chrome/Default/  #清除chrome缓存
cd /Users/yang/Desktop/Hexo  #打开Hexo根目录
hexo clean && hexo d -g  #部署Hexo
```

#### 新建 AppleScript，实现在 iTerm 运行 publish.sh 文件

```vbscript
tell application "iTerm"
	activate
	tell current window
		tell current session
			write text "/Users/yang/Desktop/Hexo/publish.sh"
		end tell
	end tell
end tell
```

> 我用的终端是 iTerm，使用自带终端同理。

#### AppleScript 导出为“应用程序”

![AppleScript](https://tva1.sinaimg.cn/large/00831rSTgy1gdbxvlon6mj30uw0fp49j.jpg)

> 可以通过替换应用包里面的 /Contents/Resources/applet.icns 文件来修改应用图标

最终实现的效果就是使用 Markdown 编辑器写好文章后，使用Alfred输入 AppleScript 导出的应用程序名称，Hexo便自动部署至设置好的VPS。Markdown 编辑器推荐使用 Typora，特别好用。
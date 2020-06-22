---
title: Mac显示隐藏文件的两种方式
categories:
  - 技巧
tags:
  - Mac
abbrlink: 26cf
date: 2020-05-12 10:32:11
---

## Mac上的隐藏文件？

在Mac中，隐藏文件不会显示在正常的文件和文件夹列表中。它们以 “.” 命名。在他们的名字之前隐藏文件可以是任何类型，例如，它可以是 .bash_profile，.htaccess 文件或 .svn 目录。如果希望，还可以隐藏/ bin，/ usr和/ etc等文件夹。

修改这些文件和文件夹可能很危险，导致MacBook无法正常运行。有时，您所做的更改是不可逆转的，导致您的数据永久丢失。

## 如何在Mac上显示隐藏文件

> 以下两种方法在 macOS 10.15.4 系统下亲测可用，推荐使用第二种方法

- ### 使用命令显示隐藏文件和文件夹

  显示隐藏文件

  ```bash
  defaults write com.apple.finder AppleShowAllFiles Yes && killall Finder
  ```

  不显示隐藏文件

  ```bash
  defaults write com.apple.finder AppleShowAllFiles No && killall Finder
  
  ```

- ### 使用 “自动操作” 创建服务显示隐藏文件

  打开 “自动操作”（应用程序文件夹中）并新建 “快速操作”。在资源库中选择 “实用工具” ——“运行 Shell 脚本”，并将它拖到右边的工作区中。将以下代码复制到文本框中

  ```bash
  STATUS=`defaults read com.apple.finder AppleShowAllFiles`
  if [ $STATUS == YES ];
  then
      defaults write com.apple.finder AppleShowAllFiles NO
  else
      defaults write com.apple.finder AppleShowAllFiles YES
  fi
  killall Finder
  ```

  具体设置如图，然后将工作流程保存为 “隐藏显示文件”

  ![截屏2020-05-12 10.46.27](https://i.loli.net/2020/05/12/iFDz73tGQNxIAyj.png)

  现 在，如果你打开Finder——服务，你会看到刚才制作的 “隐藏显示文件” 选项。

  打开系统偏好设置——键盘，点击快捷键选项卡。在左边选择服务，然后勾上“隐藏显示文件”，在它的右边双击鼠标，然后按下你想要设定成为的快捷键。我使用的是Command+Shift+.（点）。

  ![截屏2020-05-12 10.53.58](https://i.loli.net/2020/05/12/g78UbdPH6x1kwte.png)


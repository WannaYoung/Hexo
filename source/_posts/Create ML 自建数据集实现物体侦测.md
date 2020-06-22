---
title: Create ML 自建数据集实现物体侦测
categories:
  - 机器学习
tags:
  - Create ML
abbrlink: 58729
date: 2020-06-16 17:12:35
---

### Create ML 介绍

Create ML 可以与 Swift 和 macOS Playground 等您所熟悉的工具搭配使用，在 Mac 上创建和训练自定的机器学习模型。您可以训练模型来完成一些任务，例如识别图像、提取文本含义或查找数字值之间的关系。

### Create ML 模型种类

![模型分类](https://i.loli.net/2020/06/17/LwsEPZMcrJbVSvI.png)

### 自建数据集实现物体侦测

1. ##### 创建图像侦测 Create ML 项目

   新建 Create ML 项目

   ![创建模型](https://i.loli.net/2020/06/17/t1FvVm8H6S59pKs.png)

   项目面板介绍

   ![项目面板](https://i.loli.net/2020/06/17/QwRdbG3WEgMJkXC.png)

2. ##### 自建用于训练的数据集

   数据集整理如下，包含需要训练的图片和标记文件

   ![截屏2020-06-17 18.38.09](https://i.loli.net/2020/06/17/HT4jxsbMySFzr9h.png)

   annotations.json 格式如下，image → 图片名称，label → 标记类别，coordinates → 标记位置

   ```json
   [
     {"image":"domino_01.jpg","annotations":[
        {"label":"fall","coordinates":{"x":94,"y":409,"width":146,"height":225}},
        {"label":"stand","coordinates":{"x":259,"y":268,"width":159,"height":269}}]
     },
     {"image":"domino_02.jpg","annotations":[
        {"label":"fall","coordinates":{"x":196,"y":642,"width":200,"height":278}},
        {"label":"stand","coordinates":{"x":153,"y":373,"width":189,"height":263}}]
     }
   ]
   ```

   > 可以使用 IBM Cloud 标记工具来标记数据集，具体方法可参考  [Create ML 标记神器](https://blog.csdn.net/duxinshuxiaobian/article/details/95915249)

3. ##### 开始模型训练

   数据集整理完成后，直接把数据集文件夹拖入项目的训练数据即可。项目会自动识别数据集的总类别数，数据集包含图片数量，填入训练步数后点击“运行”按钮开始训练。注意：必须完成所有训练步数才能生成模型，中途取消不会生成模型。

   在项目面板可以查看 loss 趋势，训练完成后可看到各个类别的识别率。

   ![截屏2020-06-17 18.49.49](https://i.loli.net/2020/06/17/pIAiKnek27YS8u5.png)

4. ##### 使用测试数据验证模型

   <iframe height="400" width="600" src="//player.bilibili.com/player.html?aid=371069850&bvid=BV1kZ4y1H7ct&cid=203127865&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>

5. ##### 导出模型并集成进 iOS 应用

   

   


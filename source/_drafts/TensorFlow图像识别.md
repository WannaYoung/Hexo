---
title: TensorFlow图像识别
categories:
  - 机器学习
tags:
  - TensorFlow
abbrlink: 52050
date: 2020-05-18 10:30:15
---

准备图片数据

1、使用tf.keras中的ImageDataGenerator工具构建图片数据生成器

2、使用tf.data.Dataset搭配tf.image中的一些图片处理方法构建数据管道

第一种方法更为简单，第二种方法是TensorFlow的原生方法，更加灵活，可以获得更好的性能。

定义模型

1、使用Sequential按层顺序构建模型

2、使用函数式API构建任意结构模型

3、继承Model基类构建自定义模型

训练模型

1、内置fit

2、内置train_on_batch

3、自定义训练循环

### Anaconda上传环境

```shell
conda env export -n my-environment -f my-environment.yml
```

```shell
anaconda upload my-environment.yml
```


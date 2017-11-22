---
title: 使用 hexo 搭建个人博客
date: 2016-08-25 23:07:07
categories:
  - 工具
tags:
  - hexo
  - personal blog
---

作为开发人员，每天都在接触许多新的东西，如果不记下来下次还得 Google，有时候觉得许多时间都浪费在了解决相同的问题上了。

而这也是我搭建博客的初衷：把一些比较技术的东西记录下来，给自己也给他人引导。

<!-- more -->

## 原则
我希望搭建博客不需要花费我太多时间，我不太精通前端，最好能够简单的配置就有简洁美观的界面，让我可以专心写博客；另外博客引擎应该是轻量级的，并且可以部署在 github pages 上。

总结如下：

* 轻量级
* 易配置
* 能部署到 github pages 上

## 选择 hexo
选择 hexo 的原因不外乎上述的三点原则。hexo 是基于 Node.js 的一个静态博客框架，由此也就有了轻量级、易部署到 github pages 的特点。而在配置方面，hexo 也是十分简单，它包含许多主题和插件，比如我比较喜欢的[NexT](https://github.com/iissnan/hexo-theme-next)主题，也就是现在这个博客的主题；另外，在 hexo 中还支持 MathJax，输入数学公式十分方便。

## hexo的基本使用
安装 hexo 就不多说了，可以到[官网](https://hexo.io/)查看安装指南以及基本的配置。

常用命令:

* 新建页面
```
hexo new page page-name
```

* 新建博客
```
hexo new post title
```

* 清除缓存
```
hexo clean
```

* 部署前生成页面
```
hexo generate
```

* 部署
```
hexo deploy
```

* 本地启动服务
```
hexo server
```

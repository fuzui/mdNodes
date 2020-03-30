---
title: 配置hexo博客基础框架
date: 2019-03-17
tags: Hexo
categories: Hexo
---

<div align='center' ><font size='70'>配置hexo博客基础框架</font></div>

1.安装node.js

2.安装hexo

```shell
npm install hexo-cli -g
npm install hexo-deployer-git --save
```

3.hexo实例

新建blog文件夹

初始化：

```shell
hexo init
```

出现以下文件夹及文件:

* `node_modules`：依赖包（一些黑操作，官网有相应的包，也可以自己编写）
* `scaffolds`：文章模板（post默认、page、draft）
* `source`：存放文章（`/_post/xx.md`）
* `themes`：主题（默认landscape，有点丑，hexo官网预览，下载自己心仪的主题）
* `_config.yml`：整个博客的配置（themes中也有一个同名文件，为了区分前者称为站点配置文件 ，后者称为主题配置文件）
* `package.json`：应用程序的信息

部署到github：

打开站点配置文件 ：`_config.yml`，找到deploy，进行如下配置，最后保存。

```yaml
deploy:
  type: git
  repo: 你的github项目地址(git形式)
  branch: master
```

执行：

```shell
hexo clean
hexo generate
hexo server
```

本地预览：`localhost:4000`

会出现hexo默认界面，可通过`hexo server -p [端口号] `来切换端口。

上传github：

```shell
hexo clean
hexo generate
hexo deploy
```

`hexo generate`可简写`hexo g`，`hexo server`可简写`hexo s`，`hexo deploy`可简写`hexo d`。

4.发布文章

```shell
hexo new [layout] <title>
```

5.通过`fuzui.github.io`预览查看
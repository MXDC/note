---
title: Hexo搭建个人博客
tags:
  - hexo
  - Next-Theme
date: 2020/4/28
---

Hexo搭建个人博客
===

## 初始化项目


首先根据[Hexo官方文档](https://hexo.io)安装`hexo`。`hexo`可以全局安装，也可以局部安装。安装好`hexo`后进行以下操作初始化项目。

```shell
/** hexo会在指定的文件夹中创建项目 */
$ hexo init <youfolder>
$ cd <youfolder>
$ npm install
```

查看文件目录结构如下

```yml
.
├── _config.yml
├── package.json
├── scaffolds
├── source
|   ├── _drafts
|   └── _posts
└── themes

/**
  _config.yml 是站点配置文件。
  pacakge.json 是项目和依赖包信息。
  scaffolds 是模板文件夹，新建文章使用的模板。
  source/_posts 是存放Markdown文章的目录。
  themes 是主题文件夹。
 */
```

更新依赖包

```shell
/** 更新全局依赖 */

$ npm update -g

/** 更新项目依赖,在包含package.json
的文件夹目录下面执行 */

$ npm update

```

项目运行

```npm
/** 本地启动服务器 */
$  hexo server
/** 参数
 *  -p,--port,默认4000
 *  -s,--static,只使用静态文件
 *  -l,--log,启用日志记录
 */
```

## 更换主题

下载主题

``` shell
/** 进入你的项目目录 */

$ cd  <youfolder>

/** 在github上找到你要的主题 */

$ git clone https://github.com/theme-next/hexo-theme-next  themes/next

/** 更新主题 */

$ cd themes/next
$ git pull

```

主题配置

下载完主题后修改`_config.yml`文件中的`theme：`为你的主题

## 自定义效果

1. 右上角GitHub Logo

可以通过修改next主题配置文件添加github banner。

  ```yml
  /** themes\next\_config.yml  */
  github_baner:
    enable: true
  
  ```

网站[github-ribbons](https://github.blog/2008-12-19-github-ribbons/)和[github-corners](http://tholman.com/github-corners/)有github corner样式。
可以将样式复制到next主题对应的位置。位置可以通过查找`_config.yml`文件的配置文件关键词进行定位
实现效果如下所示:

![github banner](https://api.onedrive.com/v1.0/shares/s!AnfzhZ6EzsFXgUINwX8KysBXdEER/root/content)

2. 添加RSS

`hexo-generator-feed`是`hexo`的一个RSS插件。执行以下命令安装插件。

```npm
$ npm install hexo-generator-feed --save

```

---
title: 博客使用指南
date: 2020/4/10 11:34:32
categories:
- 博客
tags:
- 博客
---
## 从仓库克隆项目

仓库git地址： git@github.com:mind-node/mind-node.github.io.git
执行命令
``` shell
$ git clone git@github.com:mind-node/mind-node.github.io.git
```
- 切换到 project 分支
- 从 project 分支签出自己的分支，以自己的名字来命名分支
- `npm install`安装项目依赖
- 不要在 master 分支上修改

## 项目目录介绍

```diff
┌─scaffolds               			//文章结构的配置文件							  
├─source           					//文章文件夹
│	├─_post							//存放文章的文件夹
├─themes      						//主题文件夹
├─_config.yml         				//项目的配置文件，网站的标题等信息
|	├─.travis.yml					//ci持续集成配置文件
└─package.json 						//项目依赖
```

## 文章写作流程

你可以执行下列命令来创建一篇新文章或者新的页面。

```
$ hexo new [layout] <title>
```

您可以在命令中指定文章的布局（layout），默认为 `post`，可以通过修改 `_config.yml` 中的 `default_layout` 参数来指定默认布局。

也可以手动 source/_post 文件夹下创建 `.md` 文件

##### 文章格式

```diff
---

title: 博客使用指南

date: 2020/4/10 11:34:32

categories:

- 博客

tags:

- 博客

---

文章内容...
```

- title：文章标题
- date：文章日期
- categories：分类
- tags：标签

## 文章发布

把自己的分支合并到project分支，推送project分支到远端

在project分支执行命令

```shell
$ hexo clean
```

```shell
$ hexo deploy
```

hexo clean清理之前的编译文件

hexo deploy编译project分支，并把编译后的代码推送到master分支，部署。

## hexo命令

## init

```
$ hexo init [folder]
```

新建一个网站。如果没有设置 `folder` ，Hexo 默认在目前的文件夹建立网站。

## server

```
$ hexo server
```

启动服务器。默认情况下，访问网址为： `http://localhost:4000/`。

| 选项             | 描述                           |
| :--------------- | :----------------------------- |
| `-p`, `--port`   | 重设端口                       |
| `-s`, `--static` | 只使用静态文件                 |
| `-l`, `--log`    | 启动日记记录，使用覆盖记录格式 |

## clean

```
$ hexo clean
```

清除缓存文件 (`db.json`) 和已生成的静态文件 (`public`)。

在某些情况（尤其是更换主题后），如果发现您对站点的更改无论如何也不生效，您可能需要运行该命令。

```
$ hexo deploy
```

部署网站。

| 参数               | 描述                     |
| :----------------- | :----------------------- |
| `-g`, `--generate` | 部署之前预先生成静态文件 |

该命令可以简写为：

```
$ hexo d
```

## 拓展阅读

官方文档地址： https://hexo.io/zh-cn/docs/ 






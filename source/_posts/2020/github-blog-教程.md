---
title: github blog　教程
catalog: true
date: 2020-03-28 23:31:36
subtitle:
header-img:
tags:
- GitHub blog
---


# github blog　教程

## 下载blog仓库hexo源码
```
git clone https://github.com/forevercqd/hexo-theme-huweihuang.git
```

## 部署和发布文章
```
hexo g
hexo s
```
建议：在使用 hexo g 部署之后，可以先使用 hexo s 运行本地站点，然后在浏览器输入地址 http://lacolhost:4000/ 查看运行结果，检查无误后再使用 hexo d 发布


## 新建文章
```
hexo new 文件名
```
上述方法创建文件时，除了会生成对应的　文件名.md 外，还会生成对应的文件夹，添加图片之类的操作时则可放置在该文件夹内。

## 遇到坑点
### 坑点1: 新建文章及添加图片
图片除了可以放在统一的images文件夹中，还可以放在文章自己的目录中。
文章的目录可以通过配置_config.yml:
```
post_asset_folder: true
```
根目录执行命令
![创建新文章](new_arical.png)
会在"source_posts" 创建"github blog教程" 文件夹及"github blog教程.md"文件;

此时将"github blog教程.md" 中用到的图片放入至"source_posts\github blog教程"文件夹中，
同时引用时按以下格式引用图片，如下为引用"new_arical.png"

### 坑点2: ERROR Local hexo not found in xxx
如图:
![Local hexo not found](error_hexo.png)
解决办法:
```
npm install hexo-deployer-git --save
npm audit fix
```
![](error_hexo_solution.png)

### 坑点3: FATAL Port 4000 has been used. Try other port instead.
如图:
![](kill_port.png)

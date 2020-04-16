---
title: 基于Hexo框架，轻松搞定个人BLOG
tags: 
    - hexo
    - 教程
categories:
    - [hexo]
date: 2020-04-14 16:19:34
cover: https://i.loli.net/2020/04/10/jH36xkB7YAbJNPp.png
---



## 一、环境准备
Node.js (Node.js 版本需不低于 8.10，建议使用 Node.js 10.0 及以上版本)
Git
> 具体的安装细节请参考官方网站

## 二、初始化工程
window下进入cmd窗口

2.1 安装hexo
> npm install -g hexo-cli

2.2 初始化工程
>hexo init <folder>
cd <folder>
npm install

项目工程结构说明，可以参考官方说明：https://hexo.io/zh-cn/docs/setup

2.3 引入主题（hexo-theme-butterfly）
>git clone -b master https://github.com/jerryc127/hexo-theme-butterfly.git themes/Butterfly

主题引入成功后，可以用idea打开项目，项目工程themes会出现一个Butterfly的主题文件夹。

![gitclonethem.png](https://i.loli.net/2020/04/10/U1Y7CPeRsFjNrnA.png)

3.2 修改站點配置文件_config.yml，把主題改為 Butterfly

![modifyMainConf.png](https://i.loli.net/2020/04/10/4vAKeJyqxQj9rBm.png)

3.3 如果你沒有 pug 以及 stylus 的渲染器，请下载安装： npm install hexo-renderer-pug hexo-renderer-stylus --save

4.hexo-theme-butterfly主题配置
详细配置参照官方配置文件：https://jerryc.me/posts/4aa8abbe/

## 三、启动服务
idea打开Terminal
3.1 生成静态文件
> hexo generate or hexo g 

![heoxg.png](https://i.loli.net/2020/04/10/7xlUQrujBevtPXJ.png)

3.2 启动服务
> hexo server

![runserver.png](https://i.loli.net/2020/04/10/3lxJsOGd2gKQtvY.png)

启动服务器。默认情况下，访问网址为： http://localhost:4000/

![home.png](https://i.loli.net/2020/04/10/8VYgetkP7GBCZ1M.png)

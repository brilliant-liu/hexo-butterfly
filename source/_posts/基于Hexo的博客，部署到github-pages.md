---
title: 基于Hexo的博客，部署到github pages
tags: 
    - hexo
    - 教程
    - github
categories:
    - [hexo,教程,github]
date: 2020-04-14 16:20:22
cover: https://i.loli.net/2020/04/11/pt6MOZsTBdJwRfK.png
---


## GitHub Actions自动化部署博客到GitHub Pages

## 一、前言
网上虽然有很多通过 Github Actions 自动部署 Hexo 的教程，但都有各种各样的问题。而我就遇到一种样式无法加载的问题，不管怎么样都无法解决。因此也写个博文记录解决过程。下文会点出具体错误原因以及规避办法

![csserror.png](https://i.loli.net/2020/04/11/CEGh7gXNqypiPkn.png)

## 二、部署过程
2.1 创建本地的博客项目，请参照鄙人创建博客教程：
> https://brilliant-liu.github.io/2020/04/14/%E5%9F%BA%E4%BA%8EHexo%E6%A1%86%E6%9E%B6%EF%BC%8C%E8%BD%BB%E6%9D%BE%E6%90%9E%E5%AE%9A%E4%B8%AA%E4%BA%BABLOG/#%E4%BA%8C%E3%80%81%E5%88%9D%E5%A7%8B%E5%8C%96%E5%B7%A5%E7%A8%8B

2.2 创建github仓库
注意事项： 
> 1.仓库 Repository的名称必须为username.github.io，具体原因请参考官方： https://pages.github.com/ ， 否者可能导致部署时构建通过，会出现资源地址不匹配的问题，就是我前言中提到的样式无效。
> 2.默认分支只能为master，具体原因参照官方说明：https://help.github.com/en/github/working-with-github-pages/creating-a-github-pages-site
>
![image.png](https://i.loli.net/2020/04/11/n92ySmih6PQp5eW.png)

2.3 本地项目绑定git仓库地址
打开本地的博客项目根目录,并打开Git Bush Here，然后把资源推送到远程，具体命令如下(命令中的username请根据自己的名称替换)：
> git init
> git add .
> git commit -m "first commit"
> git remote add origin https://github.com/username/username.git
> git push -u origin master

2.4 Action配置

为了关键账户数据保密，账户等信息采用了环境变量的形式，如上面配置中的：GIT_NAME （github用户名），GIT_EMAIL（邮箱），GH_REF（项目地址如：github.com/brilliantsliu/brilliantsliu.github.io.git） ,GH_TOKEN的值为私人访问密钥，首先看下如何获取私人访问密钥：

![image.png](https://i.loli.net/2020/04/11/3F5z4OXjJcTPxUY.png)

![image.png](https://i.loli.net/2020/04/11/W1yPiBGRN9rcezo.png)


环境变量的配置如下图：

![image.png](https://i.loli.net/2020/04/11/nyVNLgUZvJWurdz.png)

![image.png](https://i.loli.net/2020/04/11/5gIdwKsODm4rlti.png)

环境变量配置好后在github项目内点击Action.

![image.png](https://i.loli.net/2020/04/11/9jQ8dvCS3xIEesa.png)

![image.png](https://i.loli.net/2020/04/11/YPb3l18ndSzVJcO.png)

填写下面的配置文件：

```yaml
name: 自动配置 hexobutterfly

on:
  push:
    branches:
      - master

jobs:
  build:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [10.x]

    steps:
      - name: 开始运行
        uses: actions/checkout@v1

      - name: 设置 Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v1
        with:
          node-version: ${{ matrix.node-version }}

      - name: 安裝 Hexo CI
        run: |
          export TZ='Asia/Shanghai'
          npm install hexo-cli -g

      - name: 緩存
        uses: actions/cache@v1
        id: cache-dependencies
        with:
          path: node_modules
          key: ${{runner.OS}}-${{hashFiles('**/package-lock.json')}}

      - name: 安裝插件
        if: steps.cache-dependencies.outputs.cache-hit != 'true'
        run: |
          npm install

      - name: 部署博客
        run: |
          hexo clean && hexo g && hexo douban
          cd ./public
          git init
          git config user.name "${{secrets.GIT_NAME}}"
          git config user.email "${{secrets.GIT_EMAIL}}"
          git add .
          git commit -m "release pages"
          git push --force --quiet "https://${{secrets.GH_TOKEN}}@${{secrets.GH_REF}}" master:master
 

```
> ps: git push --force --quiet "https://${{secrets.GH_TOKEN}}@${{secrets.GH_REF}}" master:master 我是把项目master分支构建的文件推到master分支下,这里有个大坑，就是如果我把构建的文件推送到非master分支，例如：gh-pages
> 应用构建正常，但是会收到一封邮件内容: </br>
> ![notSupportTheme.png](https://i.loli.net/2020/04/14/Ij91XMQthxspLk3.png)
> 但是访问主页会出现404，无法访问 </br> ，
> ![404.png](https://i.loli.net/2020/04/14/5RbHudms4ZLl39p.png)
>
> 在官方文件中也有相应的描述，指明源文件必须存储在master分支，所以建议将源码仓库和部署仓库分开处理。可参考本博客构建方式。

配置文件配置好，系统会自动检测到master分支是否有改动，有改动系统会自动构建并发布。

## 三、查看运行状态
点击action如图：

![image.png](https://i.loli.net/2020/04/11/TbcP869BoesZwi3.png)

![image.png](https://i.loli.net/2020/04/11/Pt4WpeQ7bcDu6Go.png)

> PS： 到此就结束了，祝大家顺利，用于一个自己的博客。

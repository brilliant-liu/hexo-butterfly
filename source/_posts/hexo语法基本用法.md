---
title: hexo语法基本用法
date: 2020-04-15 20:59:36
tags:
  - hexo
  - 教程
categories:
  - 教程
---

## Hexo 语法基本用法

+ 创建一篇新文章或者新的页面
> hexo new \[layout] \<title>
>
layout 类型   
> 如果你不想你的文章被处理，你可以将 Front-Matter 中的layout: 设为 false   

|布局|路径|
|:----:|:----:|
|post|source/_posts|
|page|source|
|draft|source/_drafts|

+ 创建草稿：
> hexo new draft "markdown基本用法"  

+ 发布草稿：
> hexo publish drafts markdown基本用法  

+ 创建非草稿文章：
> hexo new "基于Hexo框架，轻松搞定个人BLOG"  

+ 生成静态文件
> hexo g  or hexo generate

+ 启动服务：
> hexo server  or hexo s 







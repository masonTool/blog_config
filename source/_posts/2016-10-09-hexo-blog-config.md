---
title: blog的hexo配置移植方法
date: 2016-10-09 11:59:08
categories:
- 技术
- 原创
tags: 
- hexo
- github
---

介绍如何将个人博客的hexo后台移植到另外的设备上, 使可以多平台上可以同步操作博客后台.

### 0. 进入到你的hexo目录, 执行git init 生成git仓库, 将git仓库上传到github.

    //初始化仓库
    cd hexo
    git init
    //提交init
    git add .gitignore
    git add .npmignore
    git add _config.yml
    git add source/
    git add themes/
    git commit -a -m init
    //上传github服务器, 首先你要在github上添加了新的远程仓库
    git remote add github git@gitlab.meizu.com:xxxx/xxxx.git
    push -u github master

将配置文件上传至github, 看我的配置文件为[GITHUB](https://github.com/masonTool/blog_config)

### 1. 进入移植设备. 将本工程下拉, 会生成目录blog_config

    mkdir temp
    git clone git@github.com:masonTool/blog_config.git

### 2. 再拷贝一份blog_config(命名为blog_config1), 进入目录

    cp -r blog_config/ blog_config1
    cd blog_config1

### 3. 依次执行指令

    npm install hexo --save
    hexo init
    npm install hexo-deployer-git --save

### 4. 执行完上一步, 会生成文件夹node_modules, 文件夹中应该有如下的文件

    hexo
    hexo-deployer-git
    dhexo-generator-archive
    hexo-generator-category
    hexo-generator-index
    hexo-generator-tag
    hexo-renderer-ejs
    hexo-renderer-marked
    hexo-renderer-stylus
    hexo-server

### 5. 移动node_modules到原blog_config文件中. 再删除blog_config1

完成了移植. 此时你可以在多设备上写文章. 通过git来管理配置了.

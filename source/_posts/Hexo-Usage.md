---
title: 使用Hexo在Github上搭建Blog
date: 2019-10-16 14:54:26
tags:
- Hexo
- Github
categories:
- 教程
---

介绍如何使用Hexo在Github上搭建Blog
以及一些常用的操作

本文较为简单，仅供参考

【本文仅为学习目的，如有侵权，请联系我删除】
<!-- more -->

# 环境安装

Windows下载安装包进行安装

名称|下载地址
---|---
Node.js|https://nodejs.org/en
Git|https://git-scm.com

```
# 使用Git Bash运行以下命令安装Hexo
npm install -g hexo-cli
```

# Github操作

## 不同分支保存网站、源文件【仅一次】

> 此过程仅在第一次搭建博客需要，操作过一次之后就不需要再操作了

### 创建仓库

在Github上创建仓库，名称为自己账号的名称
```
<用户名>.github.io
如
SimoLin.github.io
```

### 创建两个分支

创建两个分支master与hexo,并设置hexo为默认主分支
执行以下命令，拷贝仓库到本地
```
git clone git@github.com:<用户名>/<用户名>.github.io.git
如
git clone git@github.com:SimoLin/SimoLin.github.io.git
```
拷贝完成之后，打开<用户名>.github.io文件夹

### 初始化Hexo环境

复制文件夹下的./git目录到任意位置进行备份
在文件夹下通过Git bash执行以下命令，初始化hexo(此时当前分支应显示为hexo)
```
hexo init
npm install hexo-deployer-git --save
```
使用前文中备份的./git目录，替换hexo init生成的./git文件夹

### 修改配置文件

修改_config.yml中的deploy参数，分支修改为master
```
deploy:
    type: git
    repository: git@github.com:<用户名>/<用户名>.github.io.git
    branch: master
```
修改.gitignore文件为如下内容(用以标记需要上传和不需要上传的文件/目录)
```
.DS_Store
Thumbs.db
db.json
*.log
node_modules/
public/
.deploy*/
themes/next/*
!themes/next/_config.yml
```

## 上传源文件

```
# 确认上传所有文件
git add .

# 编写更新的描述
git commit -m "<更新描述>"

## 如果不知道写什么描述可以随便写
## 比如 git commit -m "update"

# 提交更新到hexo分支
git push origin hexo
```

## 上传网站文件

```
# 清除hexo缓存
hexo clean

# 使用hexo生成网站文件并进行deploy
hexo generate -d

# 也可以使用简写
hexo g -d
```
至此，源文件和网站文件已成功备份和部署到github上

# Hexo操作

```
# 新建文章
## 将会在<用户名>.github.io\source\_posts目录下生成<文章标题>.md文件及对应目录
hexo new [布局] <文章标题>
```

# 文章更新历史

时间|更新内容
:-:|---
2019-10-16 14:57:17|新建文章
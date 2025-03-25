# 利用Hugo生成静态网页教程

[toc]
- [利用Hugo生成静态网页教程](#利用hugo生成静态网页教程)
  - [1、环境配置](#1环境配置)
  - [2、利用`brew`自动配置安装`Hugo`（其他方式不推荐）](#2利用brew自动配置安装hugo其他方式不推荐)
  - [3、生成站点](#3生成站点)
    - [3.1、前置条件](#31前置条件)
    - [3.2、创建一些页面资源](#32创建一些页面资源)
    - [3.3、安装皮肤](#33安装皮肤)
  - [4、运行`Hugo`](#4运行hugo)
    - [4.1、本地运行](#41本地运行)
    - [4.2、远程发布到GitHub](#42远程发布到github)
*资料来源*

[Hugo中文文档](https://www.gohugo.org/)

## 1、环境配置

使用终端：`Oh-My-Zsh`

## 2、利用`brew`自动配置安装`Hugo`（其他方式不推荐）

```bash
brew install hugo
```

## 3、生成站点

### 3.1、前置条件

```
1、桌面建立一个空文件夹（Test_Hugo），用于存放静态网页文件相关资源以及依赖
```

```
2、拖动Test_Hugo到终端，执行：hugo new site 【拖动‘Test_Hugo’文件夹到终端】，此时文件夹Test_Hugo里面生成内容如下图所示👇🏻
```

![image-20230403235709736](/Users/jobs/Library/Application Support/typora-user-images/image-20230403235709736.png)

### 3.2、创建一些页面资源

*创建文章页面*

```bash
hugo new about.md
```

*创建第一篇文章，放到 `post` 目录，方便之后生成聚合页面*

```bash
hugo new post/first.md
```

### 3.3、安装皮肤

*cd命令进入`themes`文件夹，执行以下命令*

```javascript
git clone https://github.com/spf13/hyde.git
```

## 4、运行`Hugo`

### 4.1、本地运行

*跳出`themes`文件目录，并且回到`Test_Hugo`目录，执行命令：*

```bash
hugo server --theme=hyde --buildDrafts
```

![image-20230404000715846](/Users/jobs/Library/Application Support/typora-user-images/image-20230404000715846.png)

*此时，页面服务（监听默认端口1313）已经开启，可以在浏览器里面进行访问*

```javascript
http://localhost:1313
```

**注意：只要关闭Mac终端或者Ctrl+C，都会结束掉页面服务，导致 http://localhost:1313 无法打开**

### 4.2、远程发布到GitHub

*1、首先在GitHub上创建一个Repository，命名为：`295060456.github.io` （295060456替换为你的github用户名）*

*2、回到Test_Hugo目录，运行以下命令👇🏻*

```
hugo --theme=hyde --baseUrl="http://coderzh.github.io/"
```

*此时"public"目录下生成一些文件，如下图所示👇🏻*

![image-20230404015126775](/Users/jobs/Library/Application Support/typora-user-images/image-20230404015126775.png)

*3、按照Github官方的解释，这个时候要确保：*

**在该项目文件夹下面→Setting→（左侧边栏）Code and automation→GitHub Pages**

![image-20230404015444441](/Users/jobs/Library/Application Support/typora-user-images/image-20230404015444441.png)

**可能需要等待几分钟，这个时候访问浏览器：https://295060456.github.io/ 🍺成功🍺**
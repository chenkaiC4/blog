---
title: Hexo博客搭建记录(一)
date: 2021-04-29 23:37:16
categories: 打磨博客
tags:
  - Hexo
  - 阿里云
  - 备案
  - 博客
---


## 1. 购买并备案域名

### 1.1 购买域名
> 建议在阿里云购买域名并备案，无它，国内最成熟的云服务。

### 1.2 域名备案
**遵**(没)**纪**(有)**守**(办)**法**(法)，**备**(需)**案**(要)**快**(80)**乐**(口)。

几个提示：
1. 域名备案，需要制定一个云服务器（IP地址）。
2. 备案通常是针对一级域名，二级域名一般不需要再备案（阿里云确实如此）。
3. 北京地区备案时间不到10天，一切都在网上操作，相比以前还需要拿着蓝色幕布拍照，现在简洁省心很多了。
4. 备案后，需要在30天内，上线网站，并在网站上（通常在页脚）添加`<a>`标签，`src`设定为`https://beian.miit.gov.cn/`。内容备案通过后下发的备案号。可参考本博客底部页脚。

### 1.3 解析域名
为域名添加A类解析，即将域名解析到一个IPv4的地址。例如`blog.xxx.com` --> `xxx.xx.xx.x`，这样操作便会创建一个二级域名。如果要针对一级域名设置，可以改为：`*.xxx.com` --> `xxx.xx.xx.x`


## 2. Caddy 代理

`Caddy`已经发布第二个版本了，相比`nginx`，它的配置更简单，自动支持`HTTPS`，非常适用于博客、官网等轻应用。
安装参考官网，配置文件参考如下：

```yml
# 全局配置，只设定一个邮箱，用于收取 cert 相关信息
{
	email chenkaic4@gmail.com
}

# 一个server，域名类型
blog.viseem.com {
	file_server         # 转到文件服务
	root * /root/blog   # 根目录，即博客的地址
}

```

## 3 博客工具选取

### 3.1 Hexo
简单，成熟，社区活跃。

### 3.2 自动发布
安装 `hexo-deployer-rsync`。其原理是借助 `SCP` 进行文件传输，所以确保自己能够 `SSH` 到服务器。
```bash
npm i hexo-deployer-rsync
```

配置格式如下：
```yml
deploy:
  type: rsync
  host: viseem.com     # 你的域名
  user: root           # 服务器的用户名
  root: /root/blog/    # 服务器上的静态文件存放目录
  port: 22             # 默认
  delete: true         # 默认，每次删除旧版本
  verbose: true        # 默认，打印所有日志
  ignore_errors: false # 默认，不忽略错误
```

### 3.3 写作模式自动刷新
`Hexo`本身不能自动刷新，需要安装扩展，这里用的是`browsersync`。

```bash
npm install hexo-browsersync
```

运行 `hexo s`，既可以自动同步。
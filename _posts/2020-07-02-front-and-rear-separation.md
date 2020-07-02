---
layout: post
title: 搭建前后端分离页面的一些注意事项
categories: [javascript, vue, php, laravel, apache, linux, vagrant]
description: 之前一直说要搭建一个前后端分离的应用，苦于一直没有时间，现在终于抽出时间搭建了一个并记录下来以后备用
keywords: 前后端分离应用
topmost: false
---

### 搭建环境

- 前端: vue/vue-cli
- 后端：laravel 7.0
- 环境：apache/centos/vagrant

### 项目地址

- [前端](https://github.com/TcTOrz/water-environment-front)

```bash

git clone
composer install
mv .env.example .env

```

- [后端](https://github.com/TcTOrz/water-environment-end)

```md

git clone
npm install

```

### 后端
主要是注意同源策略的影响，索性laravel已做好了配置。
laravel自带fruitcake/laravel-cors插件
在config/cors.php中配置即可

### 前端

```js

// 设置publicPath
publicPath: process.env.NODE_ENV === 'production'
    ? './'
    : '/',

```

在测试环境下，如果前后端在同一台服务器上，则需要在vue.conf.js中配置devServer/devServer.proxy

```js

devServer: {
    host: '10.10.10.61',
    port: '8000',
    open: true,
    proxy: {
        // 没有运行在同一台主机上。
        '/captcha': {
            target: 'http://127.0.0.1:80',
            changeOrigin: true,
        },
    },
},

```

在发布版本中，如果是history模式，需要注意在前端框架中加入.htaccess文件

### 结束

其实配置并不难，但是踩坑较多，遇到问题报错一头雾水，加上端口转发/配置虚拟域名等在里面弯弯绕绕，发布版中各种找不到路由，404错误，真的挺头大的。想想就那样，挨过去就好，千万不能放弃！

剩下会继续完善框架内容。
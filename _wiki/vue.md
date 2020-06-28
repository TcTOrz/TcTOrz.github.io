---
layout: wiki
title: Vue
categories: [Vue, Vagrant]
description: vue项目遇到的一些问题,及时记录下来
keywords: Vue, Vagrant
---

**目录**

* TOC
{:toc}

### 报错[WDS] Disconnected!并且运行`npm run serve`后，Network地址浏览器打开不了
项目环境: `Vagrant+Centos7+Xampp+Vue-cli`搭建的`Vue`项目
解决办法: 
- vue.config.js添加代码

```js

module.exports = {
    devServer: {
        host: '10.10.10.61',
    }
}

```

- Vagrantfile中添加

```

 config.vm.network :private_network, ip: "10.10.10.61"

```

参考
- <https://www.webpackjs.com/guides/development-vagrant/>
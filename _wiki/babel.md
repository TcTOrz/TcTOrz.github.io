---
layout: wiki
title: babel-一个JavaScript编译器
categories: [Javascript, Babel]
description: 学习babel,及时记录下来
keywords: Javascript, Babel
---

<!-- **目录**

* TOC
{:toc} -->

### 安装&配置
将es6编译成可在浏览器中工作的js代码

```bash

# 安装
npm install --save-dev @babel/core @babel/cli @babel/preset-env
npm install --save @babel/polyfill

# 创建babel.config.js, 并配置

```

### Basic usage with CLI

#### babel核心

babel核心功能位于`@babel/core`模块。

```bash

# install
npm install --save-dev @babel/core

```

#### babel命令行

```bash

# install
npm install --save-dev @babel/core @babel/cli

```

#### Plugins & Presets
插件是实现代码转换的一个个小模块，而预设则可以提前整体配置代码转换。
例如：

```bash

npm i @babel/plugin-transform-arrow-functions -D

```

### Configure Babel
- 使用monorepo搭建项目
- 编译node_modules
则使用`babel.config.json`

- 配置项目中的某一部分
则使用`.babelrc.json`

推荐使用`babel.config.json`

```json

// babel.config.json
{
    "presets": [],
    "plugins": []
}

```

```js

// babel.config.js
module.exports = funtion(api) {
    api.cache(true)
    const presets = []
    const plugins = []
    return {
        presets,
        plugins
    }
}

```

```json

// babelrc.json
{
    "presets": [],
    "plugins": []
}

```

```json

// package.json
{
    "babel": {
        "presets": [],
        "plugins": []
    }
}

```

```js

const presets = [];
const plugins = [];
module.exports = {presets, plugin};

```

```js

const presets = [];
const plugins = [];

if( process.env['ENV'] === "prod" ) {
    plugins.push(/* ... */);
}

module.exports = {presets, plugins};

```

### presets
- @babel/preset-env
- @babel/preset-flow
- @babel/preset-react
- @babel/preset-typescript


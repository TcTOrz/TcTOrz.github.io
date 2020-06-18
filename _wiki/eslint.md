---
layout: wiki
title: eslint-一个插件化的javascript代码检测工具
categories: [Javascript, Eslint]
description: 学习Eslint,及时记录下来
keywords: Javascript, Eslint
---

**目录**

* TOC
{:toc}

### 安装&配置

```bash

# 安装
npm i eslint -D

# 配置
./node_modules/.bin/eslint --init

```

错误级别，可以使下面的值之一：
- `"off"` or `0` - 关闭规则
- `"warn"` or `1` - 将规则视为一个警告（不会影响退出码）
- `"error"` or `2` - 将规则视为一个错误 (退出码为1)

你的 .eslintrc 配置文件可以包含下面的一行：

```md

"extends": "eslint:recommended"

```   

由于这行，所有在 规则页面 被标记为 “✔” 的规则将会默认开启。另外，你可以在 `npmjs.com` 搜索 `“eslint-config”` 使用别人创建好的配置。只有在你的配置文件中扩展了一个可分享的配置或者明确开启一个规则，`ESLint` 才会去校验你的代码。

### Configuring ESLint
ESlint 被设计为完全可配置的，这意味着你可以关闭每一个规则而只运行基本语法验证，或混合和匹配 ESLint 默认绑定的规则和你的自定义规则，以让 ESLint 更适合你的项目。有两种主要的方式来配置 `ESLint`：
- Configuration Comments - 使用 `JavaScript` 注释把配置信息直接嵌入到一个代码源文件中。
- Configuration Files - 使用 `JavaScript`、`JSON` 或者 `YAML` 文件为整个目录（处理你的主目录）和它的子目录指定配置信息。可以配置一个独立的 `.eslintrc.*` 文件，或者直接在 `package.json` 文件里的 `eslintConfig` 字段指定配置，`ESLint` 会查找和自动读取它们，再者，你可以在命令行运行时指定一个任意的配置文件。

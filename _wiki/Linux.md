---
layout: wiki
title: Linux
categories: [Linux, Centos]
description: 
keywords: Centos, Linux
---

### the NTP socket is in use, exiting报错

如果手动更新时间出错，运行

```bash

service ntpd stop

```

### 更新centos时间

```bash

ntpdate ntp.fudan.edu.cn

# or

ntpdate ntp.api.bz

```
---
layout: wiki
title: Mysql
categories: [Mysql]
description: 学习mysql,及时记录
keywords: Mysql
---

### Vagrant链接数据库问题

- 手动增加可远程访问数据库用户<不推荐>

```sql

mysql -uroot -p 
use mysql;
update user set host='%' where user='root'

```

- 直接授权<推荐>

```sql

mysql -uroot -p 
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'youpassword' WITH GRANT OPTION;

-- 刷新权限
FLUSH PRIVILEGES;

```
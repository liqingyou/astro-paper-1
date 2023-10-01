---
author: liqingyou
pubDatetime: 2023-10-01T09:11:54.547Z
title: linux 查询大文件夹 大海量文件删除
postSlug: ""
featured: false
tags:
  - linux
description: "linux,查询大文件夹,海量文件,快速删除命令"
---

#### 查询当前排名前5
```
du -h . | sort -rh | head -n 5
```

#### 使用同步原理快速删除
```
rsync --delete-before -a -H -v --progress --stats /tmp/test/ log/
```
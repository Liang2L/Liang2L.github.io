---
title: "Linux常用命令"
date: 2019-07-15 15:47:11
categories:
- "常用命令"
- "Redis"
---

## redis key的数量（ 集群）
redis-cli -c -p 16379 -h 172.31.133.98 -a yhsj_idc@act  keys "*" |wc -l

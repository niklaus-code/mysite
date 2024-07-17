---
title: Nginx日志过滤
date: 2024-07-17 09:52:12
---

```bash
access_log /var/log/nginx/access-$logdate.log main if=$iploggable; #如果匹配变量iploggable 则记录log

map $time_iso8601 $logdate {
        '~^(?<ymd>\d{4}-\d{2}-\d{2})' $ymd;
        default    'date-not-found';
        }

map $uri $iploggable{  #定义变量iploggable
        default 0; #默认为0
        ~*/dboxobsfs/v2/objs/create* 1; #匹配正则 iploggable 为1
        ~*/dboxobsfs/v2/objs/write* 1; #匹配正则 iploggable 为1
        ~*/dboxobsfs/v2/objs/delete* 1; #匹配正则 iploggable 为1
        ~*/dboxobsfs/v2/dirs/create* 1; #匹配正则 iploggable 为1
        ~*/dboxobsfs/v2/dirs/delete* 1; #匹配正则 iploggable 为1
    }
```

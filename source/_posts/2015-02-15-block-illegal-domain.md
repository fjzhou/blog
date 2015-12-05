---
layout: default
title: 屏蔽非法域名访问
---

## Nginx屏蔽非法域名访问
一个具有中国特色攻击方式，随便申请一个域名，不备案，然后解析到要攻击的服务器IP地址上。然后你的域名被封的时候，有一定概率会连带这对方IP也被封掉。这里使用Nginx的配置文件来屏蔽没有配置过的域名访问。

<!--more-->

```
server {
    listen 80 default_server;
    server_name _;
    location / {
        proxy_pass http://block.htm;
    }
}

```

非法域名重定向到一个页面，上面用大字体写`此域名已关停`即可。


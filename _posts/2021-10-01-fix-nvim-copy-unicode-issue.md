---
layout: post
title: "解决 Neovim 复制中文乱码"
date: 2021-10-01T07:27:47+08:00
draft: false
---

之前一直使用指令 `"+y` 将文本复制到系统剪切板，在迁移到 Neovim 之后发现复制的中文文本变成了乱码。在经历了1个小时的搜索之后终于发现与 locale 有关。

当前没有配置的状态
![system locale](/vim/vim-locale.png)

设置 `LANG=zh_CN.UTF-8` 的状态
![system locale zh_CN](/vim/vim-locale-zh_CN.png)

Neovim 会从环境变量里读取 locale 信息。

重启 Neovim 之后复制黏贴就会一切正常。

参考:
* https://neovim.io/doc/user/mlang.html
* https://www.zhihu.com/question/377299283


---
layout: post
title: "Build Golang MacOS Universal Binary"
date: 2021-08-24T10:21:19+08:00
draft: false
---

When developing a micro tool [xlsx2csv](http://github.com/vincent178/xlsx2csv) for converting xlsx with multiple sheets to csv in batch, my personal laptop is running on M1 Chip, but the person using this tool use Intel chip.

With some investigation, I found [lipo](https://ss64.com/osx/lipo.html) could do the trick.
```sh
lipo -create arm64-xlsx2csv amd64-xlsx2csv -o xlsx2csv
```

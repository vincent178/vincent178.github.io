---
layout: post
title: "Fix GoLand Debug Not Work"
date: 2021-09-09T10:01:27+08:00
draft: false
---

I found GoLand debugger is not working yesterday after I upgrade my MacOS version to 11.5.2 (20G95) on my M1 based Macbook Air. 
<!--more-->

## Behavior
When start GoLand debugging, it will hang forever without stopping at any breakpoint.
![Goland debug hang](/go/dlv-error-goland.png)

## Investigation

After some investigation I figured that GoLand is using delve for debugging which is not suprised, and directly using dlv in terminal is not working either.
<br/>
![dlv error](/go/dlv-error-simple.png)

Adding debug information when launch `dlv`.
<br/>
![dlv error debug](/go/dlv-error-debug-info.png)

I don't understand every word of this error, but the `invalid argument` caught my attention. <br/>

I could not find useful information on Google, then I tried searching on Twitter, someone reported a similar issue https://twitter.com/Mach_XNU/status/1415986625612484613. 
<br/>
![platform error](/go/dlv-error-twitter.png)

It seems related to platform based on his investigation, so I tried append `arch -arm64` before dlv command, and magically it's working.

## Solve the Problem
Next step is to solve GoLand debug issue. 

1. first create a bash script, and save it. I use `$HOME/bin/dlv`
```bash
arch -arm64 /Users/vincenthuang/go/bin/dlv "$@"
```

2. make script executable.
```bash
chmod +x $HOME/bin/dlv
```

3. update GoLand configuration
Open GoLand, go to Help -> Edit Custom VM Options, append a line
```
-Ddlv.path=/Users/vincenthuang/bin/dlv
```
After restart GoLand, it should work as expected.


---
layout: post
title: Fix Arch Network Bluetooth Error
date: 2022-02-21T13:20:18+08:00
---


<!--more-->

Right now I'm using a Surface Pro 3 with Manjaro Linux.
```bash
~ uname -a
Linux vincent-surfacepro3 5.15.21-1-MANJARO #1 SMP PREEMPT Sun Feb 6 12:21:42 UTC 2022 x86_64 GNU/Linux
```

After a normal `sudo pacman -Syu`, after reboot, first I notice the Bluetooth mouse is not working, and then Wifi failed to connect either.

As a Linux newbie, I have to start debugging myself. After searching and browsing with Arch forum and Google, I tried below meaning steps.

## Downgrade Kernel
Manjaro has an utility to manage kernel: `mhwd-kernel`.

Show all the installed kernels:
```bash
~ mhwd-kernel -li
```

Install a kernel:
```bash
~ mhwd-kernel -i linux515
```

To boot with the new installed kernel, reboot into **Grub** page. If there's no such step when booting into system, pressing `ESC` during boot process works with such `hidden` grub page case. Then Select "Advanced ..." and choose the new installed Linux kernel. 

After booting with new Kernel, the wifi and bluetooth still does not work.

## Investigate Log
Run `sudo journalctl -b` will print all the system and application logs, `-b` will narrow the logs only after the last boot.

There're some red lines catch my attention:
```bash
➜  ~ sudo journalctl -b -1 | grep mrvl
kernel: mwifiex_pcie 0000:01:00.0: Direct firmware load for mrvl/pcie8897_uapsta.bin failed with error -2
kernel: mwifiex_pcie 0000:01:00.0: Failed to get firmware mrvl/pcie8897_uapsta.bin
```

It seems firmware driver is missing, I download from https://github.com/wkennington/linux-firmware/tree/master/mrvl and put it `/lib/firmware/mrvl` folder.
After reboot, everything works fine now.

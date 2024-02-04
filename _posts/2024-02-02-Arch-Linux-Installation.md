---
layout: post
title: Arch Linux Installation
category: linux
mathjax: true
tags: arch, linux, gnome
---

Recently I switched from Windows back to linux for various C++ and CUDA development purposes. For a distro, I chose Arch, which I ended up installing on both my desktop and laptop. Here is my manual installation procedure, and current setup with Gnome 45:

![Arch Linux with Gnome 45](/assets/images/2024-02-02/gnome45.png)

In this post I'll summarize what I learned through the Arch Wiki's [Installation Guide](https://wiki.archlinux.org/title/Installation_guide) and other sources, going from a barebones setup all the way to desktop environment. I will also detail my own personal selection of essential packages. Here we go!

---

## Booting into Arch and preliminary configuration

1. Download Arch from [archlinux.org/download/](https://archlinux.org/download/) and create a bootable USB drive, either from the commanda line or using an etching tool such as [Balena Etcher](https://etcher.balena.io/). Boot into BIOS by holding the appropriate key (e.g. F2 or del), and boot Arch from the USB drive.
2. Verify that the boot mode is UEFI:
```
# cat /sys/firmware/efi/fw_platform_size
```
The command should return `64`.
3. Connect to the internet with `iwd` using `iwctl`:
```
[iwd]# device list
[iwd]# device <device> set-property Powered on
[iwd]# adapter <adapter> set-property Powered on
[iwd]# station <device> scan
[iwd]# station <device> get-networks
[iwd]# station <device> connect <SSID>
```
On the USB install medium, the DNS manager and DHCP client are preconfigured, but note that you will need to enable automatic 
```
/etc/iwd/main.conf
---
[General]
EnableNetworkConfiguration=true
```
---
title: About the Windows Subsystem for Linux (WSL)
date: 2022-01-11 12:36:54 +0000
categories: [Technique, OS]
tags: [linux, windows]
---

This post is to show how to install and set up a proper Windows subsystem for Linux (WSL). Initially written for Windows 10 Home 21H2 on 07 Jan 2022, updated for Windows 11 on 19 Jul. 2026.

Resources:  
<https://docs.microsoft.com/en-us/windows/wsl/install-manual>  
<https://docs.microsoft.com/en-us/windows/wsl/install>  
<https://github.com/DDoSolitary/LxRunOffline>  

## Enable WSL

WSL2 is a new edition of WSL with better compatibility with Windows. Some known bugs with WSL1 can be fixed by upgrading to WSL2:

- the inconsistent fonts of vim editor and the shell  
- the initial mode set as 'Replace' in vim editor  

Prerequisites of WSL2 are detailed in the [official webpage](https://docs.microsoft.com/en-us/windows/wsl/install).

### Manual installation (deprecated)

This corresponds to the [manual installation](https://docs.microsoft.com/en-us/windows/wsl/install-manual) of WSL2 for old Windows 10 systems.
Detailed steps are available via the link above.

### Installation via Windows PowerShell

1. Open the PowerShell as the administrator. Note: Search 'PowerShell' or Win + R, type 'PowerShell', enter.  
2. Type the command below to check available distributions:  

``` console
> wsl --list --online
```

A Ubuntu WSL1 virtual machine seems to be installed by default in Windows 10 (by late 2022), but is not shown by `wsl -l` command.
Using the following command will download the Ubuntu application from Microsoft store and activate the pre-installed virtual machine --- in which case it might be a WSL1.

``` console
> wsl --install --Ubuntu
```

If other distributions are preferred, modify the `--Ubuntu` option into other distribution names listed on the screen.
It will install a new WSL distribution and the corresponding Microsoft application.

## Upgrade to WSL2

WSL1 is the previous version of WSL, which seems not to be a virtual machine platform of full functionality.
It is suggested to check the WSL version and to upgrade it to WSL2.

In PowerShell: 

``` console
> wsl -l -v
  NAME      STATE           VERSION
* Ubuntu    Running         1
```

Then use the commands below and the distribution name (`Ubuntu`) to upgrade WSL: 

``` console
> wsl --shutdown
> wsl --set-version Ubuntu 2
```

Alternatively, use the following command to set the default WSL version:

``` console
> wsl --set-default-version 2
```

## Migrate WSL to another disk

By default, the Linux subsystem is installed in the system disk, with limited memory for WSL.
It is possible to migrate the WSL to another disk by a third party tool, [LxRunOffline](https://github.com/DDoSolitary/LxRunOffline).
It can also be used to back up and recover an old WSL. 

P.S., Nevertheless, the routine of allocating limited memory to system disk to mitigate the loss of system crash is somewhat out-of-date nowadays.
Increasing the storage space of the system disk is preferred. 

In PowerShell, `cd` to the directory of `LxRunOffline.exe`, and modify `Debian` and `D:\Debian` accordingly:

``` console
> .\LxRunOffline.exe m -n Debian -d D:\Debian
```

Then a virtual disk is visible in the folder specified. 

## Upgrade and maintenance of Ubuntu WSL

Upgrade Linux OS release:

``` bash
$ sudo do-release-upgrade
```

The default upgrade behavior can be defined in '/etc/update-manager/release-upgrades'.
By default `Prompt=lts` upgrades to the long-term supported version immediately after the current one.
To upgrade to the latest version, try `Prompt=normal`.

Check the current release:

``` bash
$ lsb_release -a
```

Basic `apt` commands are collected below:

``` bash
$ sudo apt install <Package>
$ sudo apt remove <Package>
$ sudo apt update # Download and update local package info
$ sudo apt full-upgrade # Upgrade installed packages
$ apt list --installed # List all installed packages
$ sudo apt autoremove # Automatically remove redundant packages
$ apt search <keyword> # Search the basic information of a package
```

Add mirrors to APT repository, which offer larger collections and faster speed:

``` bash
$ sudo echo 'deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ <Version Name, resolute in my case> main universe' >> /etc/apt/sources.list # THU mirror
$ apt modernize-sources
$ sudo apt update
```

Note that using a '.list' file is deprecated.
`apt modernize-sources` converts the list file into the 'sources.list.d/' directory.

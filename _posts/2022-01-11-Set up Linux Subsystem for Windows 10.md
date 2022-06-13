---
title: Set up the Linux Subsystem on Windows 10
date: 2022-01-11 12:36:54 +0000
categories: [Technique, OS]
tags: [linux, windows]
---

This post is to show how to install and set up a proper Windows subsystem for Linux (WSL). Tested on Windows 10 Home 21H2, Jan. 07 - 21.

Resources:  
<https://docs.microsoft.com/en-us/windows/wsl/install-manual>  
<https://docs.microsoft.com/en-us/windows/wsl/install>  
<https://github.com/DDoSolitary/LxRunOffline>  

# Enable the WSL 2
WSL 2 is a new edition of WSL with better compatibility with Windows. Some known bugs with WSL 1 can be fixed by upgrading to WSL 2:

- the inconsistent fonts of vim editor and the shell  
- the initial mode set as 'Replace' in vim editor  

Prerequisites of WSL 2

- x64 systems, should be Version **1903** or higher  
- ARM64 systems should be Version **2004** or higher  

## Manual installation
1. open "Settings", find and click on **Apps & features**  
2. From the right panel, find and click the **Programs and Feature**  
3. From the left panel, find and click the **Turn Windows features on or off**. Note, 'Run as administrator' needed.  
4. Select the check box for **Windows Subsystem for Linux** from the menu and click OK.  
5. Restart your computer to install the updates.  

## Automatic installation
1. Open the cmd or PowerShell as the administrator. Note: Search 'cmd'/'PowerShell' or Win + R, type 'cmd'/'PowerShell', enter.  
2. Type the command:  

``` console
~$ wsl --install
```

By default, a Ubuntu distribution will be installed. If another distribution is preferred, e.g. openSUSE: 

``` console
~$ wsl --install --openSUSE-42
```

Use the command `wsl --list --online` to check available distributions. 

# Install a Linux distribution
This step has been integrated into the previous step if you used the **Automatic installation** option. If not, install a distribution you like from [Microsoft Store](https://www.microsoft.com/en-gb/store/apps/windows). Then launch the application and initialize the WSL as instructed. 

# Upgrade from WSL 1 to WSL 2
If a WSL 1 application was installed previously, or by mistake, it is possible to upgrade it. First, you should check your WSL version by cmd or PowerShell: 

``` console
~$ wsl -l -v
  NAME      STATE           VERSION
* Debian    Running         2
```

Then use the command below and the distribution name (`Debian`) to upgrade WSL: 

``` console
~$ wsl --set-version Debian 2
```

# Migrate WSL to another disk
By default, the Linux subsystem is installed in the system disk, which might easily get filled with application data, leaving only limited memory for WSL. It is possible to easily migrate the subsystem to another disk by a third party tool, [LxRunOffline](https://github.com/DDoSolitary/LxRunOffline). Backing up or recovering your WSL can also be utilized with it. 

P.S., Nevertheless, the routine of allocating limited memory to system disk to mitigate the loss of system crash is somewhat out-of-date at the era of solid disks. Increasing the storage space of the system disk is preferred. 

After downloading the executable application, open PowerShell and `cd` to its directory. Type the command below (substitute `Debian` and `D:\Debian` with your own distribution name and path): 

``` console
~$ .\\LxRunOffline.exe m -n Debian -d D:\\Debian
```

Then a virtual disk is visible in the folder specified. 

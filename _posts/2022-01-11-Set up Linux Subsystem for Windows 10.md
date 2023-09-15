---
title: Set up the Linux Subsystem on Windows 10
date: 2022-01-11 12:36:54 +0000
categories: [Technique, OS]
tags: [linux, windows]
---

This post is to show how to install and set up a proper Windows subsystem for Linux (WSL). Tested on Windows 10 Home 21H2, Jan. 07, 2022. Updated on Apr. 25, 2023.

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

## Update Windows settings
1. open "Settings", find and click on **Apps & features**  
2. From the right panel, find and click the **Programs and Feature**  
3. From the left panel, find and click the **Turn Windows features on or off**. Note, 'Run as administrator' needed.  
4. Select the check boxes for **Virtual Machine Platform** and **Windows Subsystem for Linux** from the menu and click OK.  
5. Restart your computer to install the updates.  

## Install via Windows PowerShell
1. Open the PowerShell as the administrator. Note: Search 'PowerShell' or Win + R, type 'PowerShell', enter.  
2. Type the command below to check available distributions:  

``` console
> wsl --list --online
```

A Ubuntu WSL1 virtual machine seems to be installed by default in recent Windows 10 updates (in late 2022), but is not shown by `wsl -l` command. Using the following command will download the Ubuntu application from Microsoft store and activate the pre-installed virtual machine only - in which case the next section is skipped.

``` console
> wsl --install --Ubuntu
```

If other distributions are preferred, modify the `--Ubuntu` option into other distribution names listed on the screen.

# Install a Linux distribution
This step has been integrated into the previous step if you used the **Automatic installation** option. If not, install a distribution you like from [Microsoft Store](https://www.microsoft.com/en-gb/store/apps/windows). Then launch the application and initialize the WSL as instructed. 

# Upgrade from WSL 1 to WSL 2
WSL1 is the previous version of WSL, which seems not to be a virtual machine platform of full functionality. It is suggested to check your WSL version and to upgrade it to WSL2. In PowerShell: 

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

# Migrate WSL to another disk
By default, the Linux subsystem is installed in the system disk, which might easily get filled with application data, leaving only limited memory for WSL. It is possible to easily migrate the subsystem to another disk by a third party tool, [LxRunOffline](https://github.com/DDoSolitary/LxRunOffline). Backing up or recovering your WSL can also be utilized with it. 

P.S., Nevertheless, the routine of allocating limited memory to system disk to mitigate the loss of system crash is somewhat out-of-date at the era of solid disks. Increasing the storage space of the system disk is preferred. 

After downloading the executable application, open PowerShell and `cd` to its directory. Type the command below (substitute `Debian` and `D:\Debian` with your own distribution name and path): 

``` console
> .\LxRunOffline.exe m -n Debian -d D:\Debian
```

Then a virtual disk is visible in the folder specified. 

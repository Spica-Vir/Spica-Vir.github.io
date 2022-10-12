---
title: How to use Imperial College Research Data Store (RDS)
date: 2022-10-05 20:57:04 +0100
categories: [Technique, others]
tags: [linux, windows, Imperial RCS, Imperial RDS]
---

In this post, necessary tutorials are collected and the step-by-step guides are provided for setting up connections to [Imperial College Research Data Store (RDS) services](https://www.imperial.ac.uk/admin-services/ict/self-service/research-support/rcs/rds/) on Linux (OpenSUSE) and Windows (Windows10 Pro) machines. Connection on OpenSUSE is tested on NHPC101\@ic.ac.uk on 29 Sept. 2022, and the connection on Win10 Pro is tested on my personal laptop on 5 Oct. 2022. Special thanks to Dr. G. M. for addressing my issues with Windows. 


# What is RDS?

A comprehensive introduction is given [here](https://www.imperial.ac.uk/admin-services/ict/self-service/research-support/rcs/rds/). There is also a [Wiki page](https://wiki.imperial.ac.uk/display/HPC/Research+Data+Store) accessible for college users. Several comments are added for explanation. 

- RDS is a cloud disk of which the space can be requested by the principle investigator (PI) of a group only, based on his/her budgets. Students' access (either editable or read-only) to the certain folder of RDS is provided after being permitted by its admin.  
- RDS has been integrated into Imperial CX1. The `rds.imperial.ac.uk/RDS/user/${USER}/home/` folder is the `${HOME}` directory of cluster; the `rds.imperial.ac.uk/RDS/user/${USER}/ephemeral/` is the `${EPHEMERAL}` directory (`${USER}` is your college username, same for following texts.). See [Connect to the Imperial Cluster](https://spica-vir.github.io/posts/Connect-to-the-Imperial-Cluster/) and [Structure and usage of Clusters](https://spica-vir.github.io/posts/Structure-and-usage-of-clusters/#secure-your-storage-work-directory-and-home-directory) for more information. Even if you do not have access to any project folder, your own data can still be stored on the 1TB disk space that comes with your HPC resource allocation.  
- The `rds.imperial.ac.uk/RDS/user/${USER}/projects` folder stores data of specific projects (in the sub-folder of that project) that may be shared around the group. The partition of archives is explained [here](https://www.imperial.ac.uk/admin-services/ict/self-service/research-support/rcs/rds/archiving-data-in-the-rds/). Usually *live* is sufficient for ongoing projects.  
- To examine and manage your RDS project allocations, visit [this website](https://selfservice.rcs.imperial.ac.uk/rds/manage/projects/).  
- RDS can also be accessed by this [webpage](https://www.globus.org/). After logging in, click on the 'File Manager' tab from the side bar. In the 'Collection' box on the top, search for 'Imperial College London Research Data Store' to visit folders under your account.  

# Map RDS as a netdisk

Instead of visiting your data every time via a web browser, RDS folders can be mapped on your machine as a netdisk, making visiting it as convenient as visiting local disks - as long as you have proper internet connections. 

## On lab servers

1. Open the Dolphin file manager. From the left column, select 'Remote-Network'.  
2. From the panel that appears, click on 'Shared Folders (SMB)'.  
3. Click on 'smb:' on the top of the panel and open the address line. In the address line, enter the path to your RDS folder: `smb://rds.imperial.ac.uk/RDS/user/${USER}/home` (This commands leads to your home directory on CX1).  
4. In the popup window, enter your username with the domain, i.e., `${USER}@ic.ac.uk` and your college account password.

The account management software that comes with OpenSUSE (KDE Wallet) will popup the window with auto-filled credentials every time you access the shared folder. Use KWallet Manager to modify your information.

## On Windows machines

1. Connect to Imperial VPN. For instructions of setting it up, refer to [this step-by-step guide](https://www.imperial.ac.uk/admin-services/ict/self-service/connect-communicate/remote-access/virtual-private-network-vpn/). Note that according to tests on Win10 Pro, connection by Open VPN is not needed.  
2. Open the file explorer. Right click on 'Network' and select 'Map Network Drive'.  
3. Select any unused letter as the flag. In 'Folder', enter: `\\rds.imperial.ac.uk\RDS\user\${USER}` for everything under your name. If you get multiple projects, it is also possible to add paths of subfolders to load them as independent drives. For example, to load the home directory of CX1, you can use: `\\rds.imperial.ac.uk\RDS\user\${USER}\home`.  
4. Click on 'Finish' button. If the Internet connection works fine, after several seconds, a Windows Security popup will appear, asking for the college password. If you simply enter your password, although correctly, the 'invalid password' error will appear. The reason is that RDS requires a login domain, which is not provided with the current username.  
5. Select 'More choices/User a different account'. In 'Username', enter: `${USER}@ic.ac.uk`. Then enter the password in the next box.  



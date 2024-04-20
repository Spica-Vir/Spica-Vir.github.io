---
title: Connect to the Imperial Cluster
date: 2022-01-11 20:07:53 +0000
categories: [Technique, Parallel Computing]
tags: [cluster, Imperial RCS, regular inspection, linux, windows]
---

This page is to show how to set up the connection to Imperial cluster for the research computing services (RCS) and its usage. Contents of this page are regularly inspected. 

**N.B. MAKE SURE YOU HAVE BEEN AUTHORIZED TO DO SO.** Please note this page is not an official guidance. The author disclaims all responsibility for any trouble induced by the improper use of the information provided. For further details and technical supports, please visit the [Imperial RCS website](https://www.imperial.ac.uk/admin-services/ict/self-service/research-support/rcs/), the [User Guide](https://icl-rcs-user-guide.readthedocs.io/en/latest/) and [high performance computing (HPC) service Wiki](https://icl-rcs-user-guide.readthedocs.io/en/latest/). The live status of the cluster is available from [RCS status page](https://status.rcs.imperial.ac.uk/d/u0zkcYQ7z/rcs-status?orgId=2&refresh=1m). Brief introductions to parallel computing, queuing systems and the structures are provided in [this post](https://spica-vir.github.io/posts/Structure-and-usage-of-clusters/). 

# Connection via VPN
VPN is the easiest access to the Imperial cluster when users are off-campus. The cluster is directly accessible for on-campus visitors connected to the 'Imperial-WPA' WiFi. 

## Set up Imperial VPN
The step-by-step, OS-specific guide: <https://www.imperial.ac.uk/admin-services/ict/self-service/connect-communicate/remote-access/virtual-private-network-vpn/>

## Set up ssh on WSL
The most convenient way to visit computing resources. For guidance of setting up WSL, refer this article: [**Set up Linux Subsystem on Windows 10**](/posts/Set-up-Linux-Subsystem-for-Windows-10/index.html). For alternatives / other OSs, refer: <https://www.imperial.ac.uk/admin-services/ict/self-service/research-support/rcs/support/getting-started/using-ssh/>

The connection command: 
``` console
$ ssh -XY username@login.hpc.ic.ac.uk
```

Notes  
1. If `ssh: command not found` error is reported, install the ssh package by `sudo apt-get install ssh` (Ubuntu / Debian) or activate openssh service in OpenSUSE.  

``` console
$ # Enable ssh services
$ service sshd start
$ # If firewall blocks the visit
$ sudo firewall-cmd --permanent --add-service=ssh
$ sudo firewall-cmd --reload

```

2. `-XY` option can be omitted for most of cases, if you do not need GUI to run that program. 

# An alternative connection option
Sometimes the VPN service might be unstable or even not available. It is possible to channel through the gateway of the cluster: 

``` console
$ ssh username@sshgw.ic.ac.uk
```

Then you will log into an intermediate server. Use the command in the last section to visit the Imperial cluster. 

# Upload & download files

Use the `scp` command to upload / download files. Its format is similar to `ssh` and `cp` command: 

* `scp /local/path/file_name username@login.hpc.ic.ac.uk:~/path/file_name` Upload the local file
* `scp username@login.hpc.ic.ac.uk:~/path/file_name /local/path/file_name` Download the file

Note: if using the sshgw channel, `scp` will download the file to the intermediate server at first. You need another `scp` to download it to a local address. Same to upload. 

# Usage
## Batch system
The [qsub](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/qsub.html) job scheduler is implemented on Imperial cluster. 

* `availability` Check the availability resources;  
* `qsub filename.qsub` Submit the job 'filename';  
* `qstat` Check the state of submitted jobs;  
* `qdel jobID` Kill the process with the ID number 'jobID'.  

## Software
The `module` commands can be used to examine/load publicly accessible modules.

- `module avail` List all the available modules;  
- `module load mod_name` Load a specific module, 'mod_name';  
- `module rm mod_name` Remove a specific module, 'mod_name';  
- `module list` List all the loaded modules in the current environment;  
- `module help mod_name` Check the instructions of the module 'mod_name'. 

Note: There is a CRYSTAL14 module in the list. For users in NMH's group, the latest CRYSTAL edition is available, so do not use that module. 

## Status
The current status of imperial cluster can be inspected by [RCS status page](https://status.rcs.imperial.ac.uk/d/u0zkcYQ7z/rcs-status?orgId=2&refresh=1m).

## Compilation and Parallelization
It is suggested to use the compilers and MPI available on CX1 if the you want to compile any code, as they are tuned according to the architecture to get the optimal performance. The [Easy Build](https://icl-rcs-user-guide.readthedocs.io/en/latest/hpc/applications/easybuild/) tool packages is available on CX1 by running `module load tools/dev`. The efficiency issue has been encountered before when I tried to use a self compiled OpenMPI.


# CX1 vs. CX2

## New Job partition guide

The new job partition guide is released in April 2022, which makes the old CX1/CX2 job partitions degenerate. For the new job sizing guide, refer to [Wiki Page](https://icl-rcs-user-guide.readthedocs.io/en/latest/hpc/queues/job-sizing-guidance/). The command `availability` is still useful to check the availability of resources. 

Note that the new job sizing scheme increases its flexibility, which is especially friendly for smaller, array-like jobs, at the price of increased inter-node communication cost (My own understanding, although received some agreements, not from my supervisor). To improve the efficiency of large jobs, increasing the number of processors per node rather than increasing the number of nodes is recommended. See [Structure and usage of clusters](https://spica-vir.github.io/posts/Structure-and-usage-of-clusters/) for more information.


## Old job partitions - Only as a historical record

CX1 and CX2 are old names of Imperial clusters. Now they are merged, so you have access to both resources if logging in with the previous commands. Their job type and the core number per node are slightly different, which can be checked by `availability`. CX1 job partitions are listed under the name of 'High-Throughput jobs', and CX2 jobs are 'High-End Parallel jobs'.

``` console
~$ availability
High-Throughput jobs: 
Nodes available for throughput  :   0 (24hr)   0 (72hr) 
Nodes available for general     :   0 (24hr)   0 (72hr) 
Nodes available for singlenode  :   5 (24hr)   
Nodes available for multinode   :   0 (24hr)   0 (48hr) 
Nodes available for large memory:   2 (24hr)   2 (48hr)  
Nodes available for GPU         :   0 (24hr)   0 (48hr) 
Nodes available for debug       :   4 (30min)  

Nodes available for 48c/128gb express  :   3 (72hr)  
Nodes available for 32c/64gb express   :   0 (72hr)  

Nodes available for pqnmh       :   0   (shared) 
GPUs available: 

  RTX6000 :   0/112 (24hr)       0/  0 (48hr) 

High-End Parallel jobs: 
 Nodes available for short      ( select=1-18   ncpus=24 mem=120gb walltime=2:0:0  ) : 14  
                   large      ( select=18-72  ncpus=24 mem=120gb walltime=48:0:0 ) : 140, of which 0 are 28-core 
                   capability ( select=72-265 ncpus=28 mem=120gb walltime=24:0:0 ) : 22 
                   express    ( select=1-265  ncpus=24 mem=120gb walltime=72:0:0 ) : 0 
```

P.S. 

Bug identified on Jan. 04 - 21  
: Probably due to the settings of the router, logging into the cluster via 'Imperial WPA' at MSRH, White City campus leads to a new system never seen before, CX3. `availability` and `qdel` are not available, with error messages: 'availability: command not found' and 'qdel: Unknown Job Id 4977447.pbs-cx3'. (Job is on CX2)

Solution  
: Logging in via VPN and another WiFi, or use the command to specify CX2: 

``` console
$ ssh username@login.cx2.hpc.ic.ac.uk
```

Fixed
: Jan. 19 -21. For both campus and VPN connections, the old cx1 and cx2 addresses are merged to cx3: `cx3.hpc.ic.ac.uk`. Job types unchanged. 

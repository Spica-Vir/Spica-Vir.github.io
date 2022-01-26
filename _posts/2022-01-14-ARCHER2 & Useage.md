---
title: ARCHER2 & Useage
date: 2022-01-14 22:00:08 +0000
categories: [Technique, Parallel Computing]
tags: [cluster, ARCHER2, CRYSTAL, regular inspection, linux]
math: true
---

This page is to briefly summarize the connection and usage of the UK national supercomputing service, ARCHER2. 

**N.B. MAKE SURE YOU HAVE BEEN AUTHORIZED TO DO SO.** Please note this page is not an official guidance. The author disclaims all responsibility for any trouble induced by the improper use of the information provided. For further details and technical supports, please visit user documentations of [ARCHER2](https://docs.archer2.ac.uk/) and [EPCC SAFE](https://epcced.github.io/safe-docs/safe-for-users/#package-group). 

# Set up the connection
An SSH key pair is needed every time the user logs in. Use the command below to generate a key pair: 

``` console
> ssh-keygen -t rsa -C your@email.com
```

Then follow the instructions to generate the key pair. After generating that key pair, log into your SAFE account and add the generated key pair, saved in `~/.ssh/id_rsa.pub` by default, to the corresponding Login account. After the system approves the request, connect to ARCHER2 via: 

``` console
> ssh login_account@login.archer2.ac.uk
```

Enter the passwords for ARCHER2 account and for SSH public key in sequence. 

For the detailed, step-by-step guide, please refer: <https://docs.archer2.ac.uk/user-guide/connecting/#ssh-key-pairs> 

# General usages
## The job scheduler
The [Slurm](https://slurm.schedmd.com/overview.html) job scheduler is used in ARCHER2. The job submission file should be named as `jobname.slurm`. Here lists some commonly used commands: 

* `sbatch jobname.slurm` Submit the job 'jobname'  
* `squeue -u $USER` List the information of jobs submitted (job ID, name, status, node number)  
* `sinfo` Check the availability of resources  
* `scancel jobid` Kill the job with the ID number 'jobid'.   

## Quality of service (QoS)
Various QoS are provided to meet different computation needs. Here lists the capacities of commonly used QoS. For full details, please find <https://docs.archer2.ac.uk/user-guide/scheduler/#quality-of-service-qos>.  

| QoS Name  | CPU per node | Max nodes per job | Max walltime |
|:----------|:------------:|:-----------------:|-------------:|
| Standard  | 128          | 1024              | 24:00        |

Note:  
1. Slurm schedules the submitted jobs in a walltime-increasing fashion, which means jobs with larger walltime will be left in the queue for longer, so if the condition permits, increasing the number of nodes to reduce the time consumption is preferred. 

# CRYSTAL on ARCHER2
A good example for the job submission file is provided in [this webpage](https://docs.archer2.ac.uk/other-software/crystal/#running-parallel-crystal-jobs). It is pasted here for reference, with the modification suggested in the same page. 

Note: 
1. To launch different calculations, substitute the `MPPcrystal` keyword in the script below with corresponding keywords:  
    * `MPPcrystal` crystal (.d12) calculation in the massive parallel edition  
    * `Pcrystal` crystal (.d12) calculation in a simple parallel edition  
    * `Pproperties` parallel properties (.d3) calculation  
2. Should be careful with the proper number of cores when `MPPcrystal` is activated. It follows the equation below: 

$$ n_{procs} \geq n_{tasks} = n_{r} + n_{c} \times WEIGHT $$  

$n_{r}$ and $n_{c}$ are the number of k points with real or complex Fock matrices. $WEIGHT$ is the overloading of the diagonalization of the complex matrices. Its typical value is 1.5 ~ 2.5. **(?)**


``` bash
#!/bin/bash
#SBATCH --nodes=2
#SBATCH --ntasks-per-node=128
#SBATCH --cpus-per-task=1
#SBATCH --time=0:20:00

# Replace [budget code] below with your full project code
#SBATCH --account=[budget code]
#SBATCH --partition=standard
#SBATCH --qos=standard
#SBATCH --export=none

module load epcc-job-env
module load other-software
module load crystal

# Change this to the name of your input file
cp tio2.d12 INPUT

export FI_MR_CACHE_MAX_COUNT=0 

srun --hint=nomultithread --distribution=block:block MPPcrystal
```

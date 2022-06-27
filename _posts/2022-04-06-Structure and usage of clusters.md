---
title: Structure and usage of clusters
date: 2022-04-06 16:59:03 +0100
categories: [Technique, Parallel Computing]
tags: [cluster, Imperial RCS, ARCHER2, regular inspection, linux]
mermaid: true
---

This page is adapted from my Crystal Growth project notebook with added contents, which was written between Feb. and Mar. 2022 and shared within the group for induction and training proposes. Both this page and the notebook are under regular inspection, but their contents are not synced. 

# Divide a job: Nodes, Processors and Threads

**Node**: A bunch of CPUs and probably with GPUs / coprocessors for acceleration. Memory and input files are shared by processors in the same node, so a node can be considered as an independent computer. The communication between nodes are achieved by ultra-fast network, which is the bottleneck of modern clusters. 

**Processor**: The unit to deal with a 'process', also known as 'central processing unit', or CPU. Processors in the same node communicate via shared memory. 

**Thread**: Subdivision of a process. Multiple threads in the same process share the resources allocated to the CPU. 

The figure below illustrates the hierarchy of node, processor, and thread: 

![Hierarchy of computing units](/220406-1.png){: w="700" h="428"}

## Multiple processes vs multiple threads

From the figure above, it is not difficult to distinguish the differences between a 'process' and a 'thread': process is the smallest unit for resource allocation; thread is part of a process. The idea of 'thread' is introduced to address the huge difference in the speed of CPU and RAM. CPU is always several orders of magnitude faster than RAM, so typically the bottleneck of a process is loading the required environment from RAM, rather than computations in CPU. By using multiple threads in the same process, various branches of the same program can be executed simultaneously. Therefore, the shared environmental requirements doesn't need to be read from RAM for multiple times, and the loading time for threads is much smaller than for processes. 

However, multithreading is not always advantageous. A technical prerequisite is that the program should be developed for multithread proposes. Python, for example, is a pseudo-multithread language, while Java is a real one. Sometimes multithreading can lead to catastrophic results. Since threads share the same resource allocation (CPU, RAM, I/O, etc.), when a thread fails, the whole process fails as well. Comparatively, in multiple processes, other processes will be protected if a process fails. 

In practice, users can either run each process in serial (i.e., number of threads = 1), or in parallel (i.e., number of threads > 1) on clusters. However, **the former is recommended**, because of more secured resource managements. The latter is not advantageous. Besides the problem mentioned above, it might lead to problems such as memory leak when running programs either: poorly developed for multithreading /  compiled with improper flags / launched in an improper environmental setup - common problems of scientific computing packages & their users. :-) 

## More nodes vs more CPUs

When memory allocation is allowed, from my experience, using more CPUs/processes per node is usually a better idea, considering that all nodes have independent memory space and the inter-node communications are achieved by wired networks. It almost always takes longer to coordinate nodes than to coordinate processors within the same node.

# The internal coordinator: What is MPI

Message passing interface, or MPI, is a standard for communicating and transferring data between nodes and therefore distributed memories. It is utilized via MPI libraries. The most popular implementations include: 

- [MPICH](https://www.mpich.org/) - an open-source library;  
- [Intel MPI](https://www.intel.com/content/www/us/en/developer/tools/oneapi/mpi-library.html#gs.xld8oa) - a popular implementation of MPICH especially developed for Intel CPUs;  
- [OpenMPI](https://www.open-mpi.org/) - an open-source library;  
- [OpenMP](https://www.openmp.org/) - Not MPI; parallelization based on shared memory, so only implemented in a single node; can be used for multithreading;  

In practice, a hybrid parallelization combining MPI and OpenMP to run multithread jobs on cluster is allowed, though not recommended. 

So far, MPI only supports C/C++ and FORTRAN, which explains why all parallel computing software is based on these languages. To launch an executable in parallel, one should specify: `mpiexec` or `mpirun`. 

Technical details about how MPI is implemented is too advanced for typical computational chemists / solid state physicists, which is a specific, completely different research area, so they are not covered in this page. 

# Distribute files

As mentioned in previous sections, memories are distributed to nodes. Considering the inefficiency of inter-node communication, it is unpractical to frequently transfer files across nodes. In practice, required input files are duplicated, or synced, to all the nodes before any calculation. During the calculation, data is transferred from computing nodes to the I/O node. 

Although such sync is automatic for almost any modern cluster, it is safer to specify that when developing job submission scripts: 

``` console
~$ ssh ${NODEADDRESS} "${COMMAND}"
```

# Secure your storage: Work directory and home directory

All the modern clusters have separate disk spaces for differently proposes, namely, work directory and home directory. This originates again from the famous speed difference between CPU and RAM/ROM. 2 distinctly kinds of disks are used respectively to improve the overall efficiency and secure important data:

- For work directory, large, high-frequency disks are used. Data stored in work directory is usually not backed up, and in some cases, will be automatically cleaned after a fixed time length.  
- For home directory, mechanical disks with slower read/write frequency but better robustness are used. Usually files in home space are backed up.

For large clusters like ARCHER2, the work directory and the home directory are completely separated, i.e., directory is only viable by login nodes; work directory is viable by both job and login nodes. Job submission in home directory is prohibited. 

For more flexible, medium-sized clusters like Imperial CX1, submitting jobs in home directory and visiting home directory by job nodes are allowed, yet storing temporary files during calculation in home directory is still not recommended because of the potential influence on other files and the reduced overall efficiency. Work and home directories can be called with the following environmental variables:

`${EPHEMERAL}` - Work directory. Data will be cleaned over 30 days.  
`${HOME}` - Home directory.  
 
# The external coordinator: What is a batch system

Always bear in mind that the computational resources are limited, so you need to acquire reasonable resources for your job. Besides, the cluster also needs to coordinate jobs submitted by various users and make the best of available resources. When job is running, maybe you also want to check its status. All of this are fulfilled by batch systems.

# Work flow: How to run a job in parallel


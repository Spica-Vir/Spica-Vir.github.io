---
title: Structure and usage of clusters
date: 2022-04-06 16:59:03 +0100
categories: [Technique, Parallel Computing]
tags: [cluster, Imperial RCS, ARCHER2, regular inspection, linux]
mermaid: true
---

This page is adapted from my Crystal Growth project notebook, which was written between Feb. and Mar. 2022 and shared within the group for introduction and training proposes. Both this page and the notebook are under regular inspection. 

# Divide a job: Nodes, Processors and Threads

**Node**: A bunch of CPUs and probably with GPUs / coprocessors for acceleration. Memory and input files are shared by processors in the same node, so a node can be considered as an independent computer. The communication between nodes are achieved by ultra-fast network, which is the bottleneck of modern clusters. 

**Processor**: The unit to deal with a 'process', also known as 'central processing unit', or CPU. Processors in the same node communicate via shared memory. 

**Thread**: Subdivision of a process. Multiple threads in the same process share the resources allocated to the CPU. 

The figure below illustrates the hierarchy of node, processor, and thread: 

![Hierarchy of computing units](/220406-1.png){: w="700" h="428"}

## Multiple processes vs multiple threads

From the figure above, it is not difficult to distinguish the differences between a 'process' and a 'thread': process is the smallest unit for resource allocation; thread is part of a process. The idea of 'thread' is introduced to address the huge difference in the speed of CPU and RAM. CPU is always several orders of magnitude faster than RAM, so typically the bottleneck of a process is loading the required environment from RAM, rather than computations in CPU. By using multiple threads in the same process, various branches of the same program can be executed simultaneously. Therefore, the shared environmental requirements doesn't need to be read from RAM for multiple times, and the loading time for threads is much smaller than for processes. 

However, multithreading is not always advantageous. A technical prerequisite is that the program should be developed for multithread proposes. Python, for example, is a pseudo-multithread language, while Java is a real one. Sometimes multithreading can lead to catastrophic results. Since threads share the same resource allocation (CPU, RAM, I/O, etc.), when a thread fails, the whole process fails as well. Comparatively, in multiple processes, other processes will be protected if a process fails. 

In practice, users can either run each process in serial (i.e., number of threads = 1), or in parallel (i.e., number of threads > 1) on clusters. However, **the former is recommended**, because of more secured resource managements. The latter is not advantageous. Besides the problem mentioned above, it might lead to problems such as memory leak when running programs poorly developed for multithreading - a common problem of scientific computing packages :-). 

# The internal coordinator: What is MPI

# Distribute files: Copy vs split

# Secure your storage: Work space and home space

# The external coordinator: What is job scheduler

# Work flow: How to run a job in parallel


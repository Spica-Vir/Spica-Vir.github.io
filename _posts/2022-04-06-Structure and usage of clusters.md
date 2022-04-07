---
title: Structure and usage of clusters
date: 2022-04-06 16:59:03 +0100
categories: [Technique, Parallel Computing]
tags: [cluster, Imperial RCS, ARCHER2, regular inspection, linux]
mermaid: true
---

This page is adapted from my Crystal Growth project notebook, which was written between Feb. and Mar. 2022 and shared within the group for introduction and training proposes. Both this page and the notebook are under regular inspection. 

# Divide a job: Nodes, Processors and Threads

**Node**: A bunch of CPUs. Memory and input files are shared by processors in the same node. The communication between nodes are achieved by ultra-fast network, but it is still the bottleneck of modern clusters. 

**Processors**: The unit to calculate a "process", also known as "central processing unit", or CPU. Processors in the same node communicate via shared memory. 

****

# The internal coordinator: What is MPI

# Distribute files: Copy vs split

# Secure your storage: Work space and home space

# The external coordinator: What is job scheduler

# Work flow: How to run a job in parallel


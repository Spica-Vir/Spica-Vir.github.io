---
title: Create a new environment in Anaconda3
date: 2022-01-26 19:52:11 +0000
categories: [Technique, programming]
tags: [python, Anaconda3, windows, linux, Imperial RCS]
---
This post is to summarize the procedures to create an [Anaconda](https://www.anaconda.com/) python environment in Windows and Linux systems. Tested on Jan. 08-2022 with Windows 10 21H2, and on Jan. 29-2022 with Imperial cluster cx3. 

# Windows system
[Anaconda 3](https://www.anaconda.com/products/individual)has been integrated into a application with GUI. After installing [Anaconda 3](https://www.anaconda.com/products/individual), launch the 'Anaconda Prompt(Anaconda3)' application, which is the command line of the software, and setup everything (see below). It is also accessible to available environments / packages through Anaconda Navigator(Anaconda3) with a GUI. 

# Load Anaconda3 on Imperial cluster
The python environment loaded on the cluster is only used for testing proposes. Any demanding jobs are required to run on Anaconda environments. To load Anaconda3, use the command below: 

``` console
> module load anaconda3/personal
```

Note: The access to the module 'anaconda3' is restricted. The personal edition is recommended. 

For the first-time users, the system will take half an hour or even more to install the packages Anadonda depends on. Follow the instructions when 'anaconda3/personal' module is successfully loaded. After finishing installations, restart the terminal to execute initialization. Taking the commonly used bash as an example, anaconda will automatically modify the '.bashrc' file in the home directory if following commands are executed: 

``` console
> conda init bash
```

*Thanks to A. A. for testing this procedure (Feb. 3-22).*

Then use the command below to activate `conda` commands. The `module` command and the `source` command should be executed every time the user logs in. 

``` console
> source ~/.bashrc
```

# Create & setup a python environment
## Command list
Create a new environment  
: The 'base' environment is the default environment of Anaconda python, corresponding to the interpreter in the install root. The following command is used to create a python 3.6 environment named as 'py36ase'.


: The interpreter of the created environment is saved in the subfolder of install root: 'env/py36ase'

``` console
> conda create -n py36ase python=3.6
```


**List installed packages/environments**

``` console
> conda list
> conda env list
> conda info -e
```

**Check available updates**

``` console
> conda update conda
```

**Enter/exist a specific environment**

``` console
> conda activate env_name
> conda deactivate
```

**Install/uninstall packages for a specific environment**

``` console
> conda install -n env_name package_name
> conda remove -n env_name package_name
```

**Completely remove an environment**

``` console
> conda remove -n env_name --all
```

## Package specific issues
Atomic simulation environment (ASE) not available  
: ASE cannot be automatically located with the `conda install` command, though it is listed in [Anaconda3 packages repository](https://anaconda.org/rmg/ase). Use the command (provided in the link) to install it: 

``` console
> conda install -c rmg ase
```
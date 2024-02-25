---
title: Set Up a Remote Jupyter Server
date: 2022-08-10 18:11:07 +0100
categories: [Technique, programming]
tags: [Anaconda3, python, linux, windows]
---

In this post the method to launch the Jupyter service remotely is introduced, i.e., set up a remote Jupyter server. Note that is different from setting up a remote desktop connection, where the image on the monitor is transferred in real-time and a higher data stream is required. In the current case, the remote server is set as a 'Jupyter Notebook server', so the notebook app is launched locally in the browser, but the user can still have access to the environments and files on the remote server. Updated tests on Windows 10, NHPC101 and ARCHER2, Mar. 09, 23. Revised on Feb. 25, 24.

# Project Jupyter
[Project Jupyter](https://en.wikipedia.org/wiki/Project_Jupyter) is an open-source, cross-platform programming environment which provides hierarchical services for multiple programming languages (with the optimal support to python and R, of course). To deploy Jupyter service framework on a server, Jupyter Core and Jupyter Server are needed. 

**Jupyter Core** and [**Jupyter Server**](https://jupyter-server.readthedocs.io/en/latest/operators/public-server.html) cannot do any specific job except providing interactive programming environment and establishing communications to specific applications. When the server is launched, its configuration file is applied to other applications, unless a specific configuration file is defined elsewhere for that specific application.

**Jupyter Notebook** provides an interactive programming environment by calling the corresponding kernel and a basic file explorer. Markdown and Latex equations are provided as well, making it is useful for writing a script or developing a workflow. **Jupyter Lab** provides a more comprehensive integrated developing environment (IDE) for a single user, including Notebook, console, terminal, file explorer and text editor. Obvious advantages of Jupyter Lab include light-weight, highly extendable and customizable. The thing that I am not pretty happy with is that it, so far, has poor support of ``ssh`` and ``http``, and prohibits downloading folders. **Jupyter Hub** manages IDE for multiple users. It can both deploy users' local environment set-ups and provide the default environment for all users. It is also the recommended way to call [Jupyter Services on Imperial RCS](https://icl-rcs-user-guide.readthedocs.io/en/latest/hpc/applications/guides/jupyter/).

Jupyter Hub is set in this post for personal use.

# Configure Jupyter Server
The recommended way to install Jupyter services is using ``conda`` package and environment manager, such as [Anaconda](https://www.anaconda.com/) or [Mamba](https://mamba.readthedocs.io/en/latest/). The essentials of Jupyter Hub and Server is suggested to be installed in the 'base' (default) environment, while the interpreter and compiler of programming languages are installed in individual environments. This avoids potential conflicts in dependencies and the (in)famous ['Solving Environment' issue](https://stackoverflow.com/questions/63734508/stuck-at-solving-environment-on-anaconda) of conda. The following command installs Jupyter Core, Server, Lab, Notebook from the channel 'conda-forge' (as suggested):

``` console
(base)$ conda install jupyter -c conda-forge
```

## Generate a configuration file
A configuration file for Server is needed, which is not automatically generated when graphical user interface is not available, such as when setting up the server on clusters. To generate the configuration file, use the following command:

``` console
(base)$ jupyter server --generate-config
```

Then by default, the file is generated as `${HOME}/.jupyter/jupyter_server_config.py`. Refer to the messages in command line.

## Generate password

A password is (by default) needed for servers. There are 2 ways to generate the password:

1. Automatic generation: By default, the hashed password can be found in `${HOME}/.jupyter/jupyter_server_config.json`.

``` console
(base)$ jupyter server password
```

2. Manually generate a hashed password. Launch the conda environment that requires a Jupyter Server. In command line, type `python3` to enter the interactive python environment, then:

``` python
>>> from notebook.auth import passwd
>>> passwd()
>>> exit()
```

Then copy the generated token, the one with single quotes. 

## Edit configuration file

Open the configuration file ``${HOME}/.jupyter/jupyter_server_config.py``. Find and uncomment the following options:

``` python
c.ServerApp.allow_password_change = True # Allow the password to be changed at login for the Jupyter server
c.ServerApp.allow_remote_access = True # Allow remote access to the server
c.ServerApp.ip = '*' # Viable for all ip addresses
c.ServerApp.password = u'argon2:$argon2id$v=......' # The hashed password
c.ServerApp.password_required = False # If you do not want to enter password everytime you login
c.ServerApp.port = 1008 # Port number, can be any number, as long as it is unoccupied.
c.ServerApp.root_dir = '/home/username' # Root directory of Jupyter Server, the directory from which the Jupyter service starts
```

If you are sure that you will not use graphic interface of the server, you can set `--no-browser` as the default option in the configuration file to prevent the server to launch the browser locally:

``` python
c.ServerApp.open_browser = False
```

In practice, it is found that leaving the hashed password in `jupyter_server_config.json` will lead to invalid token error for the first-time login. So it is strongly recommended to copy the hashed password into the configuration file however the user generated it. And the user should be noted that for the first-time login, the password is required even though `c.ServerApp.password_required = False`.

For the port number, if you are not sure whether it is occupied by other processes, after the file is configured, in command line, type `jupyter server` and check messages on the screen for the available port number. It is suggested to update the `c.NotebookApp.port` value to avoid future tests. 


# Setup environments
As mentioned before, the actual programming environment is suggested to be isolated from Jupyter Service and other programming environments. After installing the packages from ``conda`` in a new environment, the kernel should be added to Jupyter in 'base' environment. Here lists some programming environments that I used.

Useful commends

* `conda create -n <name>` Creates a new conda environment.  
* `conda env list` Lists all the conda environments.  
* `conda remove -n <name> --all` Completely remove an environment.  
* `jupyter kernelspec install --user <path>` Configure Jupyter kernel using the files in the path.  
* `jupyter kernelspec list` List the available kernels.  
* `jupyter kernelspec uninstall <name>` Remove a kernel.  

## Python
Jupyter has the optimal support to python as it is where Jupyter was from. Use the following command to get a python3.9 environment and add kernel to Jupyter:

``` console
(base)$ conda create -n python39 python=3.9 # Get an env called 'python39'
(base)$ conda activate python39
(python39)$ conda install ipykernel
(python39)$ ipython kernel install --user --name=python39 # Add the kernel 'python39' for the current user
(python39)$ conda deactivate
```

Then launch Jupyter Hub (see the next section), the kernel 'python39' is available on the start-up panel.

## C++
A popular implementation is [Xeus-Cling](https://xeus-cling.readthedocs.io/en/latest/index.html). 'Xeus' is the protocol of Jupyter to implement programming languages and 'Cling' is C++ interpreter which enables interactive developing and quick compilation (probably not optimal) of C++ codes. It takes some time to install and compile Cling.

``` console
(base)$ conda create -n cpp # Get an env called 'cpp'
(base)$ conda activate cpp
(cpp)$ conda install xeus-cling -c conda-forge
(cpp)$ conda deactivate
```

After installation, go back to 'base' environment and configure kernel by the kernel setups that come with Xeus-Cling:

``` console
(base)$ jupyter kernelspec install --user path/to/anaconda/envs/cpp/share/jupyter/kernels/xcpp11
(base)$ jupyter kernelspec install --user path/to/anaconda/envs/cpp/share/jupyter/kernels/xcpp14
(base)$ jupyter kernelspec install --user path/to/anaconda/envs/cpp/share/jupyter/kernels/xcpp17
```

Then launch Jupyter Hub (see the next section), kernels 'xcpp11', 'xcpp14' and 'xcpp17' are available on the start-up panel.

## FORTRAN
Things with Fortran is always tricky as it is not as popular. The optimal solution that I found is [LFrotran](https://docs.lfortran.org/en/) which also follows Xeus protocol. An alternative is the minimalist's [jupyter-fortran-kernel](https://github.com/ZedThree/jupyter-fortran-kernel).

``` console
(base)$ conda create -n fortran # Get an env called 'fortran'
(base)$ conda activate fortran
(fortran)$ conda install lfortran -c conda-forge
(fortran)$ conda deactivate
```

Similarly, config fortran kernel in 'base' environment:

``` console
(base)$ jupyter kernelspec install --user path/to/anaconda/envs/fortran/share/jupyter/kernels/fortran
```

Then launch Jupyter Hub (see the next section), kernel 'fortran' is available on the start-up panel.


# Connect to the server
Now the server has already been set up. Use the following command to connect to the server:

``` console
~$ ssh -L localhost:1009:localhost:1008 username@serverip
```

`-L` option specifies the remote (1008) and local (1009) port numbers. The port numbers can be any ones that are not occupied by remote/local processes. The remote one is suggested to be the default port number of Jupyter services. Alternatively, the user can specify the Jupyter Service port when launching it (see below), but that number should be consistent with the remote port specified during logging in.

Then the connection is set up as usual. Using the following command to launch, for example, Jupyter Notebook:

``` console
(base)$ nohup jupyter notebook --no-browser --port=1008 > /dev/null 2>&1 &
```

`nohup` hangs up the process in the background to avoid occupying the command line and the normal + error output is redirected to nowhere (`/dev/null`). `--no-browser` is to prevent the application to open the browser of the server, not needed if `c.NotebookApp.open_browser = False`. `--port` specifies the remote port, if it is not the default one. Then open the browser in the local machine. Enter the address with local port: `http://localhost:1009`. Enter the password (not the token) to use notebook. To shut down Jupyter Notebook, kill the process (`kill -9` + PID) by PID. To get process ID, use `ps -ef | grep 'jupyter'`.

Alternatively, when using Jupyter Hub, the `nohup` command is unnecessary as the Hub provides access to terminal. However, some key-bindings are covered by Hub, such as ``ctrl+c`` is copy if the text is highlighted, while in Linux it is escaping the current command.

``` console
(base)$ jupyter lab --no-browser --port=1008
```

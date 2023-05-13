---
title: Set Up a Remote Jupyter Server
date: 2022-08-10 18:11:07 +0100
categories: [Technique, programming]
tags: [Anaconda3, python, linux, windows]
---

In this post the method to launch the Jupyter service remotely is introduced, i.e., set up a remote Jupyter server. Note that is different from setting up a remote desktop connection, where the image on the monitor is transferred in real-time and a higher data stream is required. In the current case, the remote server is set as a 'Jupyter Notebook server', so the notebook app is launched locally in the browser, but the user can still have access to the environments and files on the remote server. Updated tests on Windows 10, NHPC101 and ARCHER2, Mar. 09, 23. 

A Jupyter Server itself cannot do any specific job. But once the server is launched, its configuration file is applied to Jypter Notebook and Jupyter Lab services, unless a specific configuration file is generated for Jupyter Notebook (Not sure whether Lab also has a config file).

Reference: [Jupyter Server Documentation](https://jupyter-server.readthedocs.io/en/latest/operators/public-server.html)

# Configure Jupyter Server
## Generate a configuration file
A configuration file is needed, which is not automatically generated when graphical user interface is not available, such as when setting up the server on clusters. To generate the configuration file, use the following command:

``` console
~$ jupyter server --generate-config
```

Then by default, the file is generated as `${HOME}/.jupyter/jupyter_server_config.py`. Refer to the messages in command line.


## Generate password

A password is (by default) needed for servers. There are 2 ways to generate the password:

1. Automatic generation: By default, the hashed password can be found in `${HOME}/.jupyter/jupyter_server_config.json`.

``` console
~$ jupyter server password
```

2. Manually generate a hashed password. Launch the conda environment that requires a Jupyter Server. In command line, type `python3` to enter the interactive python environment, then:

``` python
>>> from notebook.auth import passwd
>>> passwd()
>>> exit()
```

Then copy the generated token, the one with single quotes. 

## Edit configuration file

Open the configuration file `jupyter_server_config.py`. Find and uncomment the following options:

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

# Remotely connect to the server
Now the server has already been set up. Use the following command to connect to the server:

``` console
~$ ssh -L localhost:1009:localhost:1008 username@serverip
```

`-L` option specifies the remote (1008) and local (1009) port numbers. The port numbers can be any ones that are not occupied by remote/local processes. The remote one is suggested to be the default port number of Jupyter services. Alternatively, the user can specify the Jupyter service port when launching it (see below), but that number should be consistent with the remote port specified during logging in.

Then the connection is set up as usual. Using the following command to launch, for example, Jupyter Notebook:

``` console
~$ nohup jupyter notebook --no-browser --port=1008 > /dev/null 2>&1 &
```

`nohup` hangs up the process in the background to avoid occupying the command line and the normal + error output is redirected to nowhere (`/dev/null`). `--no-browser` is to prevent the application to open the browser of the server, not needed if `c.NotebookApp.open_browser = False`. `--port` specifies the remote port, if it is not the default one. 

Then open the browser in the local machine. Enter the address with local port: `http://localhost:1009`. Enter the password (not the token) to use notebook. To shut down Jupyter Notebook, kill the process (`kill -9` + PID) by PID. To get process ID, use `ps -ef | grep 'jupyter'`.
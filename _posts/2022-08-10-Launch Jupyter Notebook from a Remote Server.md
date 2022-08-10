---
title: Launch Jupyter Notebook from a Remote Server
date: 2022-08-10 18:11:07 +0100
categories: [Technique, programming]
tags: [Anaconda3, python, linux, windows]
---

In this post the method to launch the Jupyter Notebook remotely is introduced. Note that is different from setting up a remote desktop connection, where the image on the monitor is transferred in real-time and a higher data stream is required. In the current case, the remote server is set as a 'Jupyter Notebook server', so the notebook app is launched locally in the browser, but the user can still have access to the environments and files on the remote server. Tested on Windows 10 and NHPC101, Aug. 10, 22. 

# Configure Jupyter Notebook
## Generate a password
A password is needed if the notebook application is to be set for servers. Log in the server and launch [Jupyter Notebook](https://jupyter.org/). Use the following commands to set password:

``` python
from notebook.auth import passwd

passwd()
```

Then copy the generated token, the one with single quotes. 

## Configuration file

If [Anaconda3](https://www.anaconda.com/) and Jupyter Notebook are installed with default options, open the configuration file in the following directory:

``` console
~$ vi ~/.jupyter/jupyter_notebook_config.py
```

Find and uncomment the following options:

``` python
c.NotebookApp.ip = '*' # Viable for all ip addresses
c.NotebookApp.password = u'token' # Paste the token generated
c.NotebookApp.port = 1008 # Port number, can be any number, as long as it is unoccupied.
```

If you are sure that you will not use graphic interface of the server, you can set `--no-browser` as the default option in the configuration file to prevent the server to launch the browser locally:

``` python
c.NotebookApp.open_browser = False
```

Then exit and use `jupyter notebook` to launch the application. If error messages are printed out, it is highly probable that the port specified has been occupied. 

# Remotely connect to the server
Now the server has already been set up. Assuming the notebook has been shut down and needs to be launched remotely. Using the following command to connect to the server:

``` console
~$ ssh -L localhost:1009:localhost:1008 username@serverip
```

`-L` option specifies the remote (1008) and local (1009) port numbers. 

Then the connection is set up as usual. Using the following command to launch Jupyter Notebook:

``` console
~$ nohup jupyter notebook --no-browser --port=1008 > /dev/null 2>&1 &
```

`nohup` hangs up the process in the background to enable the following processing (if necessary) in the console. `--no-browser` is to prevent the application to open the browser of the server, not needed if `c.NotebookApp.open_browser = False`. `--port` specifies the remote port. 

Then open the browser in the local machine. Enter the address with local port: `http://localhost:1009`. Enter the password (not the token) to use notebook. To shut down Jupyter Notebook, kill the process. 
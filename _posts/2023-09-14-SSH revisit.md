---
title: Revisiting SSH - Key Pair and Configuration file
date: 2023-09-14 19:33:49 +0100
categories: [Technique, SSH]
tags: [linux]
---

Recently I reset my WSL virtual machine, which forces me to regenerate ssh key pair for [ARCHER2](https://docs.archer2.ac.uk/). Some useful commands and configurations are documented here for reference. Tested on 12th September, 2023.

# Generate and copy SSH key

SSH key is a requirement for ARCHER2, but not for other clusters. It is not until recently that I found that can save a lot of time. To generate a hashed ssh public/private key pair, use:

``` console
$ ssh-keygen
```

By default the public part is saved in `~/.ssh/id_rsa.pub` and the private part is in `~/.ssh/id_rsa`. When connecting server via SSH, the private key stored locally will be compared with the public part stored on the host. If that is a match, the connection is automatically set up. Therefore, the public key should be copied to the server:

``` console
$ ssh-copy-id user@server
```

The public key is saved in `~/.ssh/authorized_keys` by default. Alternatively, if the command `ssh-copy-id` is not available, use the following line to send a command to server:

``` console
$ ssh user@server 'mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys' < ~/.ssh/id_rsa.pub
```

With public key the user can access server without password, as long as the corresponding private key is stored in `~/.ssh`. However, according to my tests, password is still required when accessing clusters including Imperial CX1 and ARCHER2. Clusters might have a separate host to deal with logins, which improves security. But the server in the lab can be accessed with public key only.

# Configuration file

Important logging in information can be saved in the `~/.ssh/config` file, so the user does not need to type the lengthy ssh commands every time. Definitions of all the keywords can be referred to by the `man ssh_config` command. Keywords that I typically use are listed below.

``` text
Host alias1
	HostName     address or ip
	User         login account
	LocalForward [bind_address]:port1 host:port2
	ForwardX11   yes

Host alias2
	HostName     address or ip
	User         login account
```

`LocalForward` specifies a local port and a remote port on the host through which commands, data etc. are transferred via a secured channel, so by calling 'port1' locally the 'port2' on the server is called. That is equivalent to `ssh -L` command. `ForwardX11` is equivalent to `ssh -X`, which establishes X11 forwarding. Graphical software on the host can access the local X11 services and be displayed on the local monitor.

After setting the config file, use the following commands for connection and file transfer:

``` console
$ ssh alias1
$ scp local/file alias1:host/destination
```

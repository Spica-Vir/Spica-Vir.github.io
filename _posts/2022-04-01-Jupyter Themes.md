---
title: Install and configure themes for Jupyter Notebook
date: 2022-04-01 00:21:09 +0100
categories: [Technique, programming]
tags: [Anaconda3, python]
---

[Jupyter Notebook](https://jupyter.org/) is a web-based, interactive python developing environment which has been integrated into [Anaconda 3](https://www.anaconda.com/). However, it is sometimes annoying especially for long-time users that it has no dark mode. Although it is possible by setting up the forced dark mode in chrome, the cursor is kept black, making it difficult to be found. 

[Jupyter Themes](https://github.com/dunovank/jupyter-themes) is a GitHub project to develop and set themes for Jupyter Notebook. Tested on Mar. 30-2022. 

# Install
To install Jupyter Themes, activate the corresponding environment, and use the command: 

``` console
> pip install jupyterthemes
```

Note: `conda install` command does not work. 

# Usage
For detailed usage, please refer to the [Readme page of Jupyter Themes project](https://github.com/dunovank/jupyter-themes/blob/master/README.md). Here is listed my settings for future reference: 

``` console
> !jt -t monokai -f dejavu -fs 11 -nfs 10 -T -N -kl -lineh 120
```

Use Jupyter Notebook to run setups, then relaunch it to see changes. 

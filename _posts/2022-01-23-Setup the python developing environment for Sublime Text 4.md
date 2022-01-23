---
title: Setup the python developing environment for Sublime Text 4
date: 2022-01-23 21:31:10 +0000
categories: [Technique, programming]
tags: [software, Sublime Text 4, python, Anaconda3]
---
In this page my setups of python3 developing environment with [Sublime Text 4](https://www.sublimetext.com/) text editor are briefly introduced. The following functions are achieved. Setup with Windows 10 21H2, Jan. 9-2022.  
* Python interpreters managed by [Anaconda 3](https://www.anaconda.com/);  
* Interactive PDB debugger;  
* Command line;  
* Automatically modify the script to fit the Pep8 standards;  
* Syntax check and code completion. 

# Create a new REPL environment
Step 1: Install package control
: Press `Ctrl + Shift + P`. Type 'install package control' and enter. Notification will pop out after the installation is successful. 

Step 2: Install SublimeREPL
: Press `Ctrl + Shift + P`. Type 'Package control: Install packages' and enter. Type 'SublimeREPL' to install this package. 

Step 3: Create a new environment
: On the top banner, click 'Preferences/Browse Packages', open the folder 'SublimeREPL/config'. Copy the subfolder 'Python' and rename it as any name, say, 'py36ase'. Restart the software, from the top banner click 'Tools/SublimeREPL/'. Find the environment with the newly defined name to confirm it has been installed correctly. 

# Link Anaconda 3 to the new environment




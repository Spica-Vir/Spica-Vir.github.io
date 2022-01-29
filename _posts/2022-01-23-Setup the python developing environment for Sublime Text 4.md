---
title: Setup the python developing environment for Sublime Text 4
date: 2022-01-23 21:31:10 +0000
categories: [Technique, programming]
tags: [Sublime Text 4, python, Anaconda3, windows]
---
In this page my setups of python3 developing environment with [Sublime Text 4](https://www.sublimetext.com/) text editor are briefly introduced. The following functions are achieved. Setup with Windows 10 21H2, Jan. 9-2022.  
* Python interpreters managed by [Anaconda 3](https://www.anaconda.com/);  
* Interactive PDB debugger;  
* Command line;  
* Automatically modify the script to fit the Pep8 style;  
* Syntax check and code completion. 

# Create a new REPL environment
Step 1: Install package control  
: Press `Ctrl + Shift + P`. Type 'install package control' and enter. Notification will pop out after the installation is successful. 

Step 2: Install SublimeREPL  
: Press `Ctrl + Shift + P`. Type 'Package control: Install packages' and enter. Type 'SublimeREPL' to install this package. 

Step 3: Create a new environment
: On the top banner, click 'Preferences/Browse Packages', open the folder 'SublimeREPL/config'. Copy the subfolder 'Python' and rename it as any name, say, 'py36ase'. Restart the software, from the top banner click 'Tools/SublimeREPL/'. Find the environment with the newly defined name to confirm it has been installed correctly. 

# Link Anaconda 3 to the new environment
Step 1： Create the REPL commands  
: In the folder 'SublimeREPL/config/py36ase', open the file 'Main.sublime-menu' and substitute the word 'Python' with the word 'py36ase', except the following lines: 

``` json
"syntax": "Packages/Python/Python.tmLanguage"

"extend_env": {"PYTHONIOENCODING": "utf-8"}
```

: In lines with `"cmd"`, substitute the environmental variable `"python"` with the path of python executable file. Here the 'py36ase' environment is used: 

``` json
"cmd": ["D:/Anaconda3/envs/py3.6ase/python.exe", "-i", "-u"],
```

Then the available REPL commands for the 'py36ase' environment are defined, which are visible and executable by clicking 'Tools/SublimeREPL/py36ase/'. 

Step 2: Edit the captions of commands  
: There is no known issues with an unmodified or even removed 'Default.sublime-commands' file. However, just in case, captions and arguments of commands can be defined there. 

: Change the path in `"file"` lines to the same folder: 

``` json
"file": "config/py3.6ase/Main.sublime-menu"
```

: Change the `"caption"` line into any name, and choose the corresponding `"id"` line in the 'Main.sublime-menu' file. Save and close both files. 

``` json
"caption": "SublimeREPL: py3.6ase - IPython"

"id": "repl_py3.6ase_ipython"
```

# Setting keybindings for REPL
There is no default keybindings for SublimeREPL, but user defined keybindings can be added via 'Preferences/Key Bingdings'. The parameters required for defining key bindings are the same as defining their captions, except an extra keyword `"keys"`. Copy and past the contents in 'Default.sublime-commands' and insert a `"keys"` line on the top of each command.

``` json
{
    "keys": ["f5"],// Run the current script with Python 3.10
    "caption": "SublimeREPL: py3.6ase - RUN current file",
    "command": "run_existing_window_command",
    "args": {
        "id": "repl_py3.6ase_run",
        "file": "config/py3.6ase/Main.sublime-menu"}
},
{
    "keys": ["ctrl+f5"],// Run PDB of the current python script
    "caption": "SublimeREPL: py3.6ase - PDB current file",
    "command": "run_existing_window_command",
    "args": {
        "id": "repl_py3.6ase_pdb",
        "file": "config/py3.6ase/Main.sublime-menu"}
},
{
    "keys": ["ctrl+shift+f5"],// Open the shell
    "caption": "SublimeREPL: py3.6ase",
    "command": "run_existing_window_command", "args":
    {
        "id": "repl_py3.6ase",
        "file": "config/py3.6ase/Main.sublime-menu"
    }
},
```

The code above indicates the settings of key bindings: 

* `F5` - Run the opened script in the py36ase environment  
* `Ctrl+F5` - Run PDB debugger for the opened script in the py36ase environment  
* `Ctrl+Shift+F5` - Open the py36ase shell for interactive commands. 

# Syntax check and code completion
Both features are integrated into the **Anaconda** package. After installing it via Package Control, several important properties should be specified by opening 'Preferences/Package Settings/Anaconda/Settings - User' and setting it as follows:  

``` json
{
	"python_interpreter":"D:/Anaconda3/envs/py3.6ase/python.exe",
	"suppress_word_completions":true,
	"suppress_explicit_completions":true,
	"swallow_startup_errors":true,
	"anaconda_linting":true
}
```

The proper interpreter should be specified in the first line. To avoid the error report when launched, set `"swallow_startup_errors"` to be `true`. To close the limit of 79 characters per column, set `"anaconda_linting"` to be `false`. The Anaconda package is automatically launched when opening a python script. 

# Pep8 coding standard
To automatically check and fit python scripts into the Pep8 style, the **AutoPep8** package is suggested. It does not require extra settings after finishing the installation. The default key bindings ('Preferences/Package Settings/AutoPep8/Key Bindings - Default') are: 

* `Ctrl+8` - Examine the script and show the previews of modifications;
* `Ctrl+Shift+8` - Examine the script and directly modify it. 

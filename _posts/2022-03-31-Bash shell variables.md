---
title: Linux bash script - variables, command outputs, and determine statements
date: 2022-03-31 16:21:30 +0100
categories: [Technique, programming]
tags: [linux, job scheduler]
---

No update for so long - I have been busy with some technical work in the group. The masters students I mentor are using [LAMMPS](https://www.lammps.org/) for classical molecular dynamics simulations, with which neither my supervisor nor myself have much experience. It is a delicate job to compile parallel LAMMPS package and design job submission scripts on Imperial cluster, or [Imperial RCS](https://www.imperial.ac.uk/admin-services/ict/self-service/research-support/rcs/). Luckily it is fruitful. By doing so my knowledge of both Linux bash programming, which I haven't actually engaged in for more than one year, and the structure of the cluster has increased a lot. 

I will probably update my thinking in the following days, as long as time permits. I still have 2 unfinished posts, *[the stone curving of Southern Dynasties](https://spica-vir.github.io/posts/%E5%8D%97%E4%BA%AC%E4%B8%B9%E9%98%B3%E5%8D%97%E6%9C%9D%E7%9F%B3%E5%88%BB%E5%88%97%E8%A1%A8%E4%B8%8E%E5%9D%90%E6%A0%87/)* and *[parameter lists for Grimme's DFT-D3 method](https://spica-vir.github.io/posts/Parameter-list-for-Grimme's-DFT-D3-and-gCP-method/)*, which has been delayed for so long. Hopefully I'll address them in this weekend. 

# Variables
Variables should be named as the combination of **numbers, letters, and underscore**. 

## Special variables
Here is the list of special variables and their meanings, which should not be occupied when programming. 

| VARIABLES  | MEANINGS                                                                                |
|:----------:|:----------------------------------------------------------------------------------------|
| $0         | Name + path of the current script                                                       |
| $n         | n is an integer > 0. External variables, entered after the script name before executing |
| $#         | The number of external variables                                                        |
| $*         | All the external variables, "$1", "$2", ...                                             |
| "$*"       | All the external variables as a single variable, "$1 $2 ..."                            |
| \$@, "$@"  | Same to $*, all the external variables, "$1", "$2", ...                                 |
| $?         | Exit status of the last command. Successful, =0; Failed, >0                             |
| $$         | PID of the current process                                                              |

## Environmental variables
The specific definition of environmental variables depend on the specific environment. Here the important environmental variables of [Imperial cluster](https://www.imperial.ac.uk/admin-services/ict/self-service/research-support/rcs/) / [PBS job scheduler](https://www.openpbs.org/) and [ARCHER2](https://www.archer2.ac.uk/) / [slurm job scheduler](https://slurm.schedmd.com/overview.html) are listed for reference. 

**Imperial cluster / PBS**  
| VARIABLES       | MEANINGS                                                                  |
|:---------------:|:--------------------------------------------------------------------------|
| \${HOME}        | Home directory, /rds/general/user/${USER}/home/                           |
| ${USER}         | User name                                                                 |
| \${TMP}         | Temporary (work) directory, /rds/general/ephemeral/user/${USER}/ephemeral |
| ${PBS_JOBID}    | ID of the current PBS job                                                 |
| ${PBS_NODEFILE} | The list of nodes currently running the PBS job                           |

**ARCHER2 / slurm**  
| VARIABLES       | MEANINGS                                                                  |
|:---------------:|:--------------------------------------------------------------------------|
| \${HOME}        | Home directory, /rds/general/user/${USER}/home/                           |
| ${USER}         | User name                                                                 |

Note  
: There is no available environmental variable to obtain the ID of an slurm job. To do that, use the command below:  
``` bash
JOBID=`sacct -u ${USER} -n -X --format jobid --name jobname.slurm --state r`
JOBID=`echo ${JOBID} | rev | cut -d' ' -f1 | rev`
```

## Variable operations
List variables are defined as `(elem1 elem2 ...)`. Common operations for a list variable `${LIST}`: 

| OPERATIONS                 | MEANINGS                                   |
|:--------------------------:|:-------------------------------------------|
| ${LIST}\[n\]               | n >= 0, the nth element of ${LIST}         |
| ${LIST}                    | The first element of ${LIST}, ${LIST}\[0\] |
| ${LIST}\[@\], ${LIST}\[*\] | All the elements of ${LIST}                |
| ${#LIST}\[@\]              | The length ${LIST}                         |

String variables can be truncated from a specified character or a specified index. Taking `link='spica-vir.github.io'` as an example: 

| OPERATIONS   | MEANINGS                                                                          |
|:------------:|:----------------------------------------------------------------------------------|
| ${link%.\*}  | Cut the right most '.' and the content on its right, ${link%.\*}=spica-vir.github |
| ${link%%.\*} | Cut the left most '.' and the content on its right, ${link%%.\*}=spica-vir        |
| ${link#\*.}  | Cut the left most '.' and the content on its left, ${link#\*.}=github.io          |
| ${link##\*.} | Cut the right most '.' and the content on its left, ${link##\*.}=io               |
| ${link: 3:6} | Return to the string with indices 3~6, including 2 ends, ${link: 3:6}=ca-v        |
| ${link: 6}   | Return to the string starting from the index 6, ${link: 6}=vir.github.io          |
| ${link: -6}  | Return to the string of the last 6 characters, ${link: -6}=hub.io                 |

# Command outputs
The output of commands can be redirected using the symbols listed below: 

| SYMBOLS                           | MEANINGS                                                                                            |
|:---------------------------------:|:----------------------------------------------------------------------------------------------------|
| 0                                 | Standard input                                                                                      |
| 1                                 | Standard output                                                                                     |
| 2                                 | Standard error                                                                                      |
| command > file                    | Redirect the standard output of 'command' to a newly created 'file'                                 |
| command >> file                   | Redirect the standard output of 'command' to the end of 'file'. If 'file' does not exist, create it |
| command >& file,  command &> file | Redirect the standard output and standard error of 'command' to a newly created 'file'              |
| 2>&1                              | Copy the behavior of standard output and use it for standard error                                  |

Analysis: 

``` bash
# Command 1
command > file.out 2>&1
# Command 2
command 2>&1 > file.out
```

* Command 1: Standard output redirected to 'file.out'; Copied to standard error; Standard error redirected to 'file.out'. ---> Output and error printed in 'file.out'.  
* Command 2: Standard output by default printed on screen; Copied to standard error; Standard output redirected to 'file.out'. ---> Output printed in 'file.out', error printed on screen.  

## Determine statements
Determine statements are important for both 'if' sentences and 'while' loops. The differences between statements acting on variables / files, or numbers / strings should be noticed. 

**For files and directories**  
| OPERATORS | MEANINGS                          | 
|:---------:|:----------------------------------|
| -e        | File / directory exists, True     |
| -s        | File / directory not empty, True  |
| -d        | Is a directory, True              |
| -f        | Is a regular file, True           |
| -h        | Is a soft link, True              |
| -r        | Readable, True                    |
| -w        | Writable, True                    |
| -x        | Executable, True                  |

**For variables**  
| OPERATORS   | MEANINGS                 | 
|:-----------:|:-------------------------|
| ${variable} | ${variable} exists, True |
| -z          | Variable is empty, True  |

**For strings**
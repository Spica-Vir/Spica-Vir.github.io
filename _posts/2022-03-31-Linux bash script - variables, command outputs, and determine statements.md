---
title: Linux bash script - variables, command outputs, and determine statements
date: 2022-03-31 16:21:30 +0100
categories: [Technique, programming]
tags: [linux, job scheduler]
---

<!-- No update for so long - I have been busy with some technical work in the group. The masters students I mentor are using [LAMMPS](https://www.lammps.org/) for classical molecular dynamics simulations, with which neither my supervisor nor myself have much experience. It is a delicate job to compile parallel LAMMPS package and design job submission scripts on Imperial cluster, or [Imperial RCS](https://www.imperial.ac.uk/admin-services/ict/self-service/research-support/rcs/). Luckily it is fruitful. By doing so my knowledge of both Linux bash programming, which I haven't actually engaged in for more than one year, and the structure of the cluster has increased a lot. 

I will probably update my thinking in the following days, as long as time permits. I still have 2 unfinished posts, *[the stone curving of Southern Dynasties](https://spica-vir.github.io/posts/%E5%8D%97%E4%BA%AC%E4%B8%B9%E9%98%B3%E5%8D%97%E6%9C%9D%E7%9F%B3%E5%88%BB%E5%88%97%E8%A1%A8%E4%B8%8E%E5%9D%90%E6%A0%87/)* and *[parameter lists for Grimme's DFT-D3 method](https://spica-vir.github.io/posts/Parameter-list-for-Grimme's-DFT-D3-and-gCP-method/)*, which has been delayed for so long. Hopefully I'll address them in this weekend.  -->

## Variables
Variables should be named as the combination of **numbers, letters, and underscore**. 

### Special variables
Here is the list of special variables and their meanings, which should not be occupied when programming. 

| VARIABLES    | MEANINGS                                                                                |
|:------------:|:----------------------------------------------------------------------------------------|
| `$0`         | Name + path of the current script                                                       |
| `$n`         | n is an integer > 0. External variables, entered after the script name before executing |
| `$#`         | The number of external variables                                                        |
| `$*`         | All the external variables, `"$1"`, `"$2"`, ...                                         |
| `"$*"`       | All the external variables as a single variable, `"$1 $2 ..."`                          |
| `$@`, `"$@"` | Same as $*                                                                              |
| `$?`         | Exit status of the last command. Successful, =0; Failed, >0                             |
| `$$`         | PID of the current process                                                              |

### Environmental variables
The specific definition of environmental variables depend on the specific environment.
Here are some common environmental variables. 

| VARIABLES          | MEANINGS                                           |
|:--------------  --:|:---------------------------------------------------|
| `$HOME`            | Home directory                                     |
| `$USER`            | User name                                          |
| `$PWD`             | Current directory                                  |
| `$PATH`            | Path to executables                                |
| `$LD_LIBRARY_PATH` | Path to libraries to be linked with `-D -l` flag   |
| `$INCLUDE`         | Path to header files to be included with `-I` flag |

### Variable operations
List variables are defined as `(elem1 elem2 ...)`. Common operations for a list variable `$LIST`: 

| OPERATIONS                 | MEANINGS                                   |
|:--------------------------:|:-------------------------------------------|
| `${LIST[n]}`               | n >= 0, the nth element of `$LIST`         |
| `$LIST`                    | The first element of `$LIST`, `${LIST[0]}` |
| `${LIST[@]}`, `${LIST[*]}` | All the elements of `$LIST`                |
| `${#LIST[@]}`              | The length of `$LIST`                      |

String variables can be truncated from a specified character or a specified index. Taking `link='spica-vir.github.io'` as an example: 

| OPERATIONS     | MEANINGS                                                                |
|:--------------:|:------------------------------------------------------------------------|
| `${link%.*}`   | Cut the right most '.' and the content on its right, `spica-vir.github` |
| `${link%%.*}`  | Cut the left most '.' and the content on its right, `spica-vir`         |
| `${link#*.}`   | Cut the left most '.' and the content on its left, `github.io`          |
| `${link##*.}`  | Cut the right most '.' and the content on its left, `io`                |
| `${link: 3:6}` | Return to the string with indices 3~6, including 2 ends, `ca-v`         |
| `${link: 6}`   | Return to the string starting from the index 6, `vir.github.io`         |
| `${link: -6}`  | Return to the string of the last 6 characters, `hub.io`                 |

## Command outputs
The output of commands can be redirected using the symbols listed below: 

| SYMBOLS                              | MEANINGS                                                                                            |
|:------------------------------------:|:----------------------------------------------------------------------------------------------------|
| 0                                    | Standard input                                                                                      |
| 1                                    | Standard output                                                                                     |
| 2                                    | Standard error                                                                                      |
| `command > file`                     | Redirect the standard output of 'command' to a newly created 'file'                                 |
| `command >> file`                    | Redirect the standard output of 'command' to the end of 'file'. If 'file' does not exist, create it |
| `command >& file`, `command &> file` | Redirect the standard output and standard error of 'command' to a newly created 'file'              |
| `2>&1`                               | Copy the behavior of standard output and use it for standard error                                  |
| `: > file`                           | Create an empty file                                                                                |

Analysis: 

``` bash
# Command 1
$ command > file.out 2>&1
# Command 2
$ command 2>&1 > file.out
```

* Command 1: Standard output redirected to 'file.out'; Copied to standard error; Standard error redirected to 'file.out'. ---> Output and error printed in 'file.out'.  
* Command 2: Standard output by default printed on screen; Copied to standard error; Standard output redirected to 'file.out'. ---> Output printed in 'file.out', error printed on screen.  

## Determine statements
Determine statements are important for both 'if' sentences and 'while' loops. The differences between statements acting on variables / files, or numbers / strings should be noticed. 

### For files and directories

| OPERATORS | MEANINGS                          | 
|:---------:|:----------------------------------|
| -e        | File / directory exists, True     |
| -s        | File / directory not empty, True  |
| -d        | Is a directory, True              |
| -f        | Is a regular file, True           |
| -h, -L    | Is a soft link, True              |
| -r        | Readable, True                    |
| -w        | Writable, True                    |
| -x        | Executable, True                  |

### For variables

| OPERATORS   | MEANINGS                    |
|:-----------:|:----------------------------|
| `$variable` | Variable exists, True       |
| -n          | Variable is not empty, True |
| -z          | Variable is empty, True     |

### For strings

For string comparison, in case of spaces, adding "" to protect variables is recommended. Besides, `==` is preferred to `-eq` due to the same reason. 

| OPERATOR                       | MEANING                                              | 
|:------------------------------:|:-----------------------------------------------------|
| `"$string_1" == *"$string_2"*` | If `$string_2` is contained within `$string_1`, True |

### For numbers

To compare integer values in `if` statement, use:

| COMMAND  | MEANING                  | 
|:--------:|:-------------------------|
| ==, -eq  | Equal to                 |
| -gt      | Greater than             |
| -lt      | Less than                |
| -ge      | Greater than or equal to |
| -le      | Less than or equal to    |

To calculate integers, use:

| COMMAND          | MEANING                                                                            | 
|:----------------:|:-----------------------------------------------------------------------------------|
| `c=$(( a + b ))` | Save the sum of `$a` and `$b` to `$c`. Note there is no dollar sign in parenthesis |

To compare float numbers, `bc` command should be used.
`bc` command returns an integer variable, and it cannot be used as determine statements, otherwise the statement can only tell whether the specific variable is empty.

| COMMAND                      | MEANING            | 
|:----------------------- ----:|:-------------------|
| `$(echo "$a==$b" | bc) == 1` | Float equal        |
| `$(echo "$a>$b" | bc) == 1`  | Float greater than |

Use `bc` to run float calculations:

| COMMAND                     | MEANING                                | 
|:---------------------------:|:---------------------------------------|
| `echo "scale=2;$a+$b" | bc` | Float sum, rounded to 2 decimal places |

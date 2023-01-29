# runpyscr

Utility shell-script for small python script project. Setup the python script template with sub-directory structure for module install locally. Same script works for python module install with pip locally. It also work as wrapper to call python script with the setting to use python modules installed locally.

## Requirement

- Bash
- Python
- Optional: GNU realpath (in GNU coreutils)
- sed
- awk

## Usage

This script has three modes of operation.

1. Setup/Initialize mode (`init-mode`)

When the script `runpyscr` is invoked with `--manage` as the first option and `init` as the sub-command, this script works as `init-mode`. The following is most simple example to prepare the directory structure under `/somewhere/pyworktool` and to make the python script template with the name, `pywork`, under the prepared directories. If `-r` option is given in `init-mode`, the template of `README.md` is also made. Title of `README.md` can be specified with `-t` option. If `-g` option is given in `init-mode`, the template for the `Git` related files such as `.gitignore` is also made.

```
% ./runpyscr --manage -p /somewhere/pyworktool -3 init pywork
% (cd /somewhere/ ; find pyworktool -ls )
 drwxr-xr-x   ...... pyworktool
 drwxr-xr-x   ...... pyworktool/bin
 -rwxr-xr-x   ...... pyworktool/bin/runpyscr
 lrwxr-xr-x   ...... pyworktool/bin/mng_pyenv -> runpyscr
 lrwxr-xr-x   ...... pyworktool/bin/mng_pyenv2 -> runpyscr
 lrwxr-xr-x   ...... pyworktool/bin/mng_pyenv3 -> runpyscr
 lrwxr-xr-x   ...... pyworktool/bin/pywork -> runpyscr
 lrwxr-xr-x   ...... pyworktool/bin/pywork3 -> runpyscr
 drwxr-xr-x   ...... pyworktool/lib
 drwxr-xr-x   ...... pyworktool/lib/python
 drwxr-xr-x   ...... pyworktool/lib/python/site-packages
 -rw-r--r--   ...... pyworktool/lib/python/pywork3.py
 lrwxr-xr-x   ...... pyworktool/lib/python/pywork.py -> pywork3.py
```

2. Management mode (`manage-mode`)

`mng_pyenv` and `mng_pyenv[23]` made by `runpyscr` with `init-mode` is symbolic link to `bin/runpyscr`. Invoking `mng_pyenv` is equivalent to invoke `runpyscr --manage`. (Invoking `mng_pyenv2` and `mng_pyenv3` are equivalent to invoke `runpyscr --manage -2` and `runpyscr --manage -3`, respectively.) In this mode, other sub-commands are also available. If it is invoked with sub-command `install` and python module name(s) in following arguments, the specified python modules are installed under the local directory structure.

```
% /somewhere/pyworktool/bin/mng_pyenv3 install pytz tzlocal
Collecting install
  Downloading install-1.3.5-py3-none-any.whl (3.2 kB)
Collecting pytz
  Using cached pytz-2022.7.1-py2.py3-none-any.whl (499 kB)
Collecting tzlocal
  Using cached tzlocal-4.2-py3-none-any.whl (19 kB)
.....
Installing collected packages: install, pytz, tzdata, backports.zoneinfo, pytz-deprecation-shim, tzlocal
Successfully installed backports.zoneinfo-0.2.1 install-1.3.5 pytz-2022.7.1 pytz-deprecation-shim-0.1.0.post0 tzdata-2022.7 tzlocal-4.2

% ls  /somewhere/pyworktool/lib/python/site-packages/3.8/
backports                           install                  pytz                     pytz_deprecation_shim                        tzdata                   tzlocal
backports.zoneinfo-0.2.1.dist-info  install-1.3.5.dist-info  pytz-2022.7.1.dist-info  pytz_deprecation_shim-0.1.0.post0.dist-info  tzdata-2022.7.dist-info  tzlocal-4.2.dist-info
```

3. Run mode (`run-mode`)

If the symbolic link to this script(`runpyscr`) with the name other than `mng_pyenv{,2,3}` is invoked, the python script, `lib/python/{symbolic_link_name}.py`, is invoked with setting environmental variable `PYTHONPATH` to use python modules installed locally. If `runpyscr` is invoked with first argument other than `--manage`, the python script, `lib/python/{first_arguments}.py` or `{first_arguments}` is invoked with setting `PYTHONPATH`.

```
% ~/tmp/pyworktool/bin/pywork3
Hello, World!
Python : 3.8.9 (/Applications/Xcode.app/Contents/Developer/usr/bin/python3)
....

% /somewhere/pyworktool/bin/runpyscr pywork3 Japan
Hello, Japan!
Python : 3.8.9 (/Applications/Xcode.app/Contents/Developer/usr/bin/python3)
....

% /somewhere/pyworktool/bin/runpyscr  /somewhere/pyworktool/lib/python/pywork3.py  Japan
Hello, Japan!
Python : 3.8.9 (/Applications/Xcode.app/Contents/Developer/usr/bin/python3)
....
```

## Other options / examples

1. Installing python modules in `init-mode`.

If the python module name is given with `-m` option in `init-mode`, the python module is installed. (Multiple `-m` options can be accepted.) Multiple names for python template are also accepted.

```
% ./runpyscr --manage -p /somewhere/pyworktool -g -r -t 'Worktool by Python' -3 -m pytz -m tzlocal init prepwork mainwork
Collecting pytz
  Using cached pytz-2022.7.1-py2.py3-none-any.whl (499 kB)
Collecting tzlocal
  Using cached tzlocal-4.2-py3-none-any.whl (19 kB)
......
Installing collected packages: pytz, tzdata, backports.zoneinfo, pytz-deprecation-shim, tzlocal
Successfully installed backports.zoneinfo-0.2.1 pytz-2022.7.1 pytz-deprecation-shim-0.1.0.post0 tzdata-2022.7 tzlocal-4.2

% ( cd /somewhere ; find pyworktool -ls ) 
...... pyworktool
...... pyworktool/README.md
...... pyworktool/.gitignore
...... pyworktool/bin
...... pyworktool/bin/runpyscr
...... pyworktool/bin/mng_pyenv -> runpyscr
...... pyworktool/bin/mng_pyenv2 -> runpyscr
...... pyworktool/bin/mng_pyenv3 -> runpyscr
...... pyworktool/bin/prepwork -> runpyscr
...... pyworktool/bin/prepwork3 -> runpyscr
...... pyworktool/bin/mainwork -> runpyscr
...... pyworktool/bin/mainwork3 -> runpyscr
...... pyworktool/lib
...... pyworktool/lib/python
...... pyworktool/lib/python/mainwork.py -> mainwork3.py
...... pyworktool/lib/python/mainwork3.py
...... pyworktool/lib/python/prepwork.py -> prepwork3.py
...... pyworktool/lib/python/prepwork3.py
...... pyworktool/lib/python/site-packages
...... pyworktool/lib/python/site-packages/.gitkeep
...... pyworktool/lib/python/site-packages/3.8
...... pyworktool/lib/python/site-packages/3.8/tzlocal-4.2.dist-info
......
% cat /somewhere/pyworktool/README.md 
#
# Worktool by Python
#

Skeleton for small portable tools by python script

- Contents:

  1.  README.md:                         This file
  2.  bin/mng_pyenv:                     Symblic link to 'runpyscr' for installing Python modules by pip locally.
  2a. bin/mng_pyenv2:                    Same as above using pip2 as default
  2b. bin/mng_pyenv3:                    Same as above using pip3 as default

  3.  bin/runpyscr:                      Wrapper bash script to invoke Python script. (Entity)

  4.  lib/python/site-packages:          Directory where python modules are stored

  5.  lib/python/prepwork.py:            Example Python script that use modules
  5a. lib/python/prepwork3.py:           Same as above using python3 as default

  6.  bin/prepwork:                      Symbolic link to 'runpyscr' to invoke prepwork.py.
  6a. bin/prepwork3:                     Same as above using python3 as default

  7.  lib/python/mainwork.py:            Example Python script that use modules
  7a. lib/python/mainwork3.py:           Same as above using python3 as default

  8.  bin/mainwork:                      Symbolic link to 'runpyscr' to invoke mainwork.py.
  8a. bin/mainwork3:                     Same as above using python3 as default

  9.  .gitignore:                        Git-related file
  10. lib/python/site-packages/.gitkeep: Git-related file to keep modules directory in repository.
....
```

2. Adding python script template in `manage-mode`.

`add` sub-command in `manage-mode` can be used to add the new python script template, the symbolic link to `bin/runpyscr` with corresponding name, and to update `README.md` when `-r` option is given.

```
% /somewhere/pyworktool/bin/mng_pyenv -r add postwork
% ( cd /somewhere ; find pyworktool -ls ) ...... 
...... pyworktool/bin/postwork3 -> runpyscr
...... pyworktool/bin/postwork -> runpyscr
...... 
...... pyworktool/lib/python/postwork3.py
...... pyworktool/lib/python/postwork.py -> postwork3.py
...... 
% cat /somewhere/pyworktool/README.md
......
  11.  lib/python/postwork.py:            Example Python script that use modules
  11a. lib/python/postwork3.py:           Same as above using python3 as default

  12.  bin/postwork:                      Symbolic link to 'runpyscr' to invoke postwork.py.
  12a. bin/postwork3:                     Same as above using python3 as default
......
```

## Application

`runpyscr` is making python templates is embedded in itself at the lines from  `########## __py_template_start__ ##########` to ``########## __py_template_end__ ##########` with replacing the keyword, `## __py_shebang_pattern__ ##`, by the relevant python location using `sed`.  (`python` location is looked for by the following order; 1. command line option: `-P`, 2. environmental variable: `PYTHON`, 3, python in `PATH`).  Therefore, you can change the python script template by editing this part of `runpyscr`. 

Yet another usage is to take a python script created in one environment and use it in another environment, or to distribute the custom python script file for others to use. As mentioned above, this script changes its behavior depending on whether the file entity is invoked directly or invoked by the symbolic link, but the entity file name itself (`runpyscr`) is not used to determine the mode of operation, so it can be changed as you like.

The following is the example when the python script `tool.py`, which is often used in hostA, is brought to another environment hostB. (It is useful when you do not have the privileges to install python module in hostB system by `pip`.) It is possible to change the template of `README.md` and `.gitignore` in similar way. Note that the keyword substitution for these template is performed by `sed`.

```
UserA@HostA % cp -ai 'runpyscr' '/somewhere/tool-dist'
UserA@HostA % emacs /somewhere/tool-dist # Edit template part with the contents of tool.py
UserA@HostA % scp /somewhere/tool-dist UserB@HostB:/tmp/tool-dist

UserB@HostB % /tmp/tool-dist --manage -p /destination/util -g -r -t 'Tool by UserA@HostA' -3 -m pytz -m tzlocal -m other-python-module init tool
UserB@HostB % /destination/util/bin/tool # Executable
```

## Command-line help

Command line help can be shown with `-h` option in `manage-mode`.

```
% ./runpyscr --manage -h
[Usage]        % mng_pyenv{,2,3}   [options] sub-command [arguments]
               % runpyscr --manage [options] sub-command [arguments]
[sub-commands] 
               % runpyscr --manage [options] init [scriptnames] ... setup directory tree,
                                                                        and prepare templates if arguments are given.
               % runpyscr --manage [options] add script_names   ... prepare python script templates.
               % runpyscr --manage install  module_names        ... Install python module with pip locally.
               % runpyscr --manage download module_names        ... Download python module with pip locally.
               % runpyscr --manage clean                        ... Delete local python module for corresponding python version.
               % runpyscr --manage distclean/cleanall/allclean  ... Delete local python module for all python version.
               % runpyscr --manage info                         ... Show information.
[Options]
               -h     : Show this message
               -P arg : specify python command / path
               -v     : Show verbose messages
               -q     : Supress verbose messages (default)
               -n     : Dry-run mode.
               -2     : Prioritize Python2 than Python3
               -3     : Prioritize Python3 than Python2
[Options for sub-command: setup/init ]
               -p     : prefix of the directory tree. (Default: Grandparent directory if the name of parent directory of 
                        runpyscr is bin, otherwise current working directory.)
               -M     : moving this script body into instead of copying
               -g     : setup files for git
               -G     : do not setup files for git (default)
               -r     : setup/update README.md
               -R     : do not setup/update README.md (default)
               -k     : keep backup file of README.md when it is updated.
               -K     : do not keep backup file of README.md when it is updated. (default)
               -t arg : Project title
[Options for sub-command: install/download ]
               -i arg : specify pip command
```
  
## Author
    Nanigashi Uji (53845049+nanigashi-uji@users.noreply.github.com)


##### README

[taskwarrior-um](https://github.com/d630/taskwarrior-um) is a tiny bash shell wrapper for using the command line todo manager [Taskwarrior](http://taskwarrior.org/) as a simple bookmark manager by defining [User Defined Attributes (UDAs)](http://taskwarrior.org/docs/udas.html).

##### BUGS & REQUESTS

Please feel free to open an issue or put in a pull request on https://github.com/D630/taskwarrior-um

##### GIT

To download the very latest source code:

```
git clone https://github.com/D630/taskwarrior-um
```

In order to use the latest tagged version, do also something like this:

```
cd ./taskwarrior-um
git checkout $(git describe --abbrev=0 --tags)
```

##### INSTALL

Put `./bin/taskum` on your PATH and copy `./doc/examples/taskumrc` to TASKUM_CONFIG.

##### USAGE

###### INVOCATION

```
taskum [ -h | -v | -n | -o | -O ] [ -m ] [ TW_NATIVE ... ]
```

###### ENVIRONMENT VARIABLES

```
TASKUM_CONFIG
        Default: ${XDG_CONFIG_HOME:-${HOME}/.config}/taskum/taskrc
TASKUM_DATA
        Default: ${XDG_DATA_HOME:-${HOME}/.local/share}/taskum
```

###### OPTIONS

```
-h,  --help
-v,  --version
-m,  --use-menu                 Use a menu when adding project and tags.
                                Configure it in Taskum::AddMenu. Defaults
                                are dmenu and slmenu. Don't execute
                                taskum via source when using this option
-n,  --verbose-nothing          Means: 'task rc.verbose:nothing'
-o,  --verbose-on               ""     'task rc.verbose:on'
-O,  --verbose-off              ""     'task rc.verbose:off'
```

###### EXAMPLES

See [INFO](../master/doc/INFO.md)

##### NOTICE

taskwarrior-um has been written in [GNU bash](http://www.gnu.org/software/bash/) on [Debian GNU/Linux 9 (stretch/sid)](https://www.debian.org) using these programs/packages:

- GNU bash 4.3.42(1)-release
- GNU coreutils 8.23: mkdir, sort, tr
- dmenu-4.6
- grep (GNU grep) 2.22
- procps-ng 3.3.11: ps
- sed (GNU sed) 4.2.2
- slmenu 0.1
- task 2.5.0

##### LICENCE

[Copyrights](../master/doc/COPYRIGHT)

[GNU GPLv3](../master/doc/LICENCE)

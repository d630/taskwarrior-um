## taskwarrior-um v0.1.1.0 [GNU GPLv3]

[Taskwarrior](http://taskwarrior.org/) is a list processing program written in C++ (MIT License) with a
very nice command line interface to act like a
todo list manager with a rich set of subcommands. As the name implies,
Taskwarrior is mainly written to work with todo lists; but since Taskwarrior
has introduced [User Defined Attributes (UDAs)](http://taskwarrior.org/docs/udas.html) in version 2.1.0 (2012-07-23), it
is quite easy to change the focus point by defining new metadata items. To use
Taskwarrior as simple command line bookmark manager we just have to create a
proper configuration file with UDAs and custom [reports](http://taskwarrior.org/docs/tutorials/report.html); additionally, we need to
take care that an [URI](http://en.wikipedia.org/wiki/URI) is recorded once only.

So, `taskwarrior-um` does nothing else. As a very simple bash shell script it
wraps up Taskwarrior and sees to it that only new URIs will be recorded.
Therefore, it prepares the `add` subcommand of Taskwarrior.

#### Metadata Items (Fields)

In Taskwarrior every task needs to have a description. In doing so, it is
irrelevant, if a description conforms with an another description of different
task. To identify a task, there are the `UUID` field as permanent key and the
[id](http://taskwarrior.org/docs/ids.html) field as temporary assignment (line counted relative to the entries); not
until a task is completed or deleted, the id will have been discarded.

The first difference in using Taskwarrior as urimark manager is that there is - of
course - no tasking and nothing, what should be completed. In `taskwarrior-um` all
records have the status `pending` or `deleted`, and we do not need all the
subcommands and built-in-attributes (for example [urgency](http://taskwarrior.org/docs/urgency.html)), which imply the
differentiation between `pending`, `completed` etc. The second difference is
that we ignore the description attribute; a record should be free to have a
description. Unfortunately, we can not rename the label of this attribute,
because it is built-in. For this reason, every record gets a `description` with
the value `uri` as place holder. It could also be used as a kind of
categorization. By default, we do not need it.

The built-in attributes and metadata, that we consider, are `project` and `depends`.
Moreover we want to use `tags`, which are arbitrary words in Taskwarrior. The
following UDAs are defined and used:

```bash
$ taskum udas

Name      Type   Label     Allowed Values                                     Default Usage Count
--------- ------ --------- -------------------------------------------------- ------- -----------
authority string Authority                                                            7553
name      string Name                                                         null    7553
note      string Note                                                         null    7553
part      string Part                                                                 7553
scheme    string Scheme    http,https,ftp,ftps,dav,davs,gopher,webdav,webdavs         7553
uri       string URI                                                                  7553

6 UDAs defined
```

A data set is structured like this:

```bash
$ taskum 1,2 info

Name                  Value
--------------------- --------------------------------------
ID                    1
Description           uri
Status                Pending
Project               software.internet.bookmarking
This task is blocking 2 uri
Tags                  commandline urimarks homepage todo gtd
UUID                  886c7566-ce4d-4825-b23b-8ab9441e49c6
Entered               17_2014-04-21_05:06 (7 mins)
Urgency               0
Authority             taskwarrior.org
Name                  taskwarrior
Note                  A command line todo manager
Part                  /
Scheme                http
URI                   http://taskwarrior.org

Name                 Value
-------------------- ------------------------------------
ID                   2
Description          uri
Status               Pending
Project              software.internet.bookmarking
This task blocked by 1 uri
Tags                 taskwarrior documentation cms
UUID                 419c5906-bd38-4d9c-87bb-8c5b100cd992
Entered              17_2014-04-21_05:13 (27 secs)
Urgency              0
Authority            taskwarrior.org
Name                 taskwarrior
Note                 some tutorials and introductions
Part                 /docs
Scheme               http
URI                  http://taskwarrior.org/docs
```

Like I said, we should ignore `description` and `urgency`. On the `depends`
attribute we give a different interpretation as `reference` attribute. In fact,
we could use a tag as a reference, but having an own attribute is better in
case of some postprocessing.

#### Reports

`taskwarrior-um` comes along with 36 built-in and custom reports. The buit-in
reports, which are modified, are `list`, `newest` and `oldest`; the custom
reports are:

```
rmeta            Custom: Lists ID, Pro, Tags and Ref
rname            Custom: Lists ID, Name and URI
rnote            Custom: Lists ID and Note
rpro             Custom: Lists ID, Project and URI
rref             Custom: Lists ID, Ref and URI
rsplit           Custom: Lists ID, Scheme, Authority and Part
rtags            Custom: Lists ID, Tags and URI
ruri             Custom: Lists ID and URI
```

You may list all supported reports with Taskwarriors's subcommand `reports`:

`$ taskum reports`

Feel free to follow up and build your own reports.

### Index

1. [Requierement](#requierement)
2. [Usage](#usage)
3. [Examples](#examples)
4. [Configurations](#configurations)
5. [Enviroment](#enviroment)
6. [Notes](#notes)
7. [Bugs & Requests](#bugs--requests)

### Requierement

-   GNU bash >= 4.0
-   task >= 2.1.0
-   GNU sed
-   mkdir

### Usage

    taskum [-h|-v|-n] [-a|TASKWARRIOR_SUBCOMMAND]

    OPTIONS
    -------
        -h,  -help
        -v,  -version
        -n,  -verbose-nothing   means: 'task rc.verbose:nothing'

    SUBCOMMANDS
    -----------
        -a,  -add                [ <AFIELD> ... ]
        TASKWARRIOR_SUBCOMMAND   [ <NIDFILTER> ]
                                 See 'man task'

    ARGUMENTS
    ---------
        <AFIELD>
                                 'uri:string'
                                 'name:string'
                                 'note:string'
                                 'pro:one.project.with.hierarchy'
                                 'dep:id,id,id'
                                 '+tag'
        <NIDFILTER>              '--id'

### Examples

To add a record, we have to specify minimum an URI. Without any arguments the
built-in command `read` of `bash` prompts you to build the command arguments.
Take care to seperate the tags with `;`. If you do not specify `project`, `name`
or `note`, they will get the place holder `null`. A fully formulated `add` command
could look like this:

```bash
$ taskum add \
             uri:"http://taskwarrior.org/docs" \
             name:taskwarrior \
             note:"some tutorials and introductions" \
             depends:1 \
             pro:software.internet.bookmarking \
             +taskwarrior \
             +documentation \
             +cms
```

The default subcommand is the report `ruri` without a filter:

```bash
$ taskum

ID URI
-- ---------------------------
 1 http://taskwarrior.org
 2 http://taskwarrior.org/docs
```

With option '-n':

```bash
$ taskum -n
 1 http://taskwarrior.org
 2 http://taskwarrior.org/docs
```

It is possible to use a negativ id-filter expression (only as first argument).
To filter the 5th last record:

`$ taskum --5`

You can also write the ids like a normal id sequence:

```bash
$ taskum --5,10
$ taskum --1-10
$ taskum --1-10,20
```

All other commands are native Taskwarrior commands or custom reports. See its Manpage for more
details.
```bash
$ taskum +documentation rmeta

ID Pro                           Tags                          Ref
-- ----------------------------- ----------------------------- ---
 2 software.internet.bookmarking taskwarrior documentation cms 1
```

```bash
$ taskum 'part ~ doc' rsplit

ID Scheme Auth            Part
-- ------ --------------- -----
 2 http   taskwarrior.org /docs
```

```bash
$ taskum 1,2 delete
Permanently delete task 1 'uri'? (yes/no/all/quit) yes

Permanently delete task 2 'uri'? (yes/no/all/quit) yes
```

```bash
$ taskum undo

--- previous state  Undo will restore this state
+++ current state   Change made 2014-04-21_06:56
-end:
+end:               1398056191
-modified:
+modified:          1398056193
-status:            pending
+status:            deleted

The undo command is not reversible.  Are you sure you want to revert to the previous state? (yes/no) yes
Modified task reverted.
```

```bash
$ taskum undo

--- previous state  Undo will restore this state
+++ current state   Change made 2014-04-21_06:56
-end:
+end:               1398056183
-modified:
+modified:          1398056191
-status:            pending
+status:            deleted

The undo command is not reversible.  Are you sure you want to revert to the previous state? (yes/no) yes
Modified task reverted.
```

```bash
$ taskum -n newest
 2 1398049985 1h  http://taskwarrior.org/docs
 1 1398049585 1h  http://taskwarrior.org
```

### Configurations

Along with this script comes an example configuration file named `taskumrc.example`.
Additionally, there is a fresh new file with all defaults of the configuration
settings used in Taskwarrior v2.3.0.

The defaults are:

```bash
$ taskum _show | egrep -e "^default\."
default.command=ruri
default.due=
default.priority=
default.project=null
```

The UDAs are:

```bash
$ taskum _show | egrep -e "^uda"
uda.authority.label=Authority
uda.authority.type=string
uda.authority.values=
uda.name.default=null
uda.name.label=Name
uda.name.type=string
uda.name.values=
uda.note.default=null
uda.note.label=Note
uda.note.type=string
uda.note.values=
uda.part.label=Part
uda.part.type=string
uda.part.values=
uda.scheme.label=Scheme
uda.scheme.type=string
uda.scheme.values=http,https,ftp,ftps,dav,davs,gopher,webdav,webdavs
uda.uri.label=URI
uda.uri.type=string
uda.uri.values=
```

The modified and custom reports are:

```bash
$ taskum _show | egrep -e "^report.(r[^e].*|new.*|old.*|list)"
report.list.columns=id,entry.epoch,modified.epoch,name,note,uri,scheme,authority,part,project.full,tags.list,depends.list
report.list.description=Modified: Lists all pending tasks
report.list.filter=status:pending
report.list.labels=ID,ED,MD,Name,Note,URI,Scheme,Auth,Part,Pro,Tags,Ref
report.list.sort=id+
report.newest.columns=id,entry.epoch,entry.age,uri
report.newest.description=Modified: Shows the newest URIs
report.newest.filter=status:pending limit:30
report.newest.labels=ID,ED,Age,Uri
report.newest.sort=id-
report.oldest.columns=id,entry.epoch,entry.age,uri
report.oldest.description=Modified: Shows the oldest URIs
report.oldest.filter=status:pending limit:30
report.oldest.labels=ID,ED,Age,Uri
report.oldest.sort=id+
report.rmeta.columns=id,project.full,tags.list,depends.list
report.rmeta.description=Custom: Lists ID, Pro, Tags and Ref
report.rmeta.filter=status:pending
report.rmeta.labels=ID,Pro,Tags,Ref
report.rmeta.sort=id+
report.rname.columns=id,name,uri
report.rname.description=Custom: Lists ID, Name and URI
report.rname.filter=status:pending
report.rname.labels=ID,Name,URI
report.rname.sort=id+
report.rnote.columns=id,note
report.rnote.description=Custom: Lists ID and Note
report.rnote.filter=status:pending
report.rnote.labels=ID,Note
report.rnote.sort=id+
report.rpro.columns=id,project.full,uri
report.rpro.description=Custom: Lists ID, Project and URI
report.rpro.filter=status:pending
report.rpro.labels=ID,Pro,URI
report.rpro.sort=id+
report.rref.columns=id,depends.list,uri
report.rref.description=Custom: Lists ID, Ref and URI
report.rref.filter=status:pending
report.rref.labels=ID,Ref,URI
report.rref.sort=id+
report.rsplit.columns=id,scheme,authority,part
report.rsplit.description=Custom: Lists ID, Scheme, Authority and Part
report.rsplit.filter=status:pending
report.rsplit.labels=ID,Scheme,Auth,Part
report.rsplit.sort=id+
report.rtags.columns=id,tags.list,uri
report.rtags.description=Custom: Lists ID, Tags and URI
report.rtags.filter=status:pending
report.rtags.labels=ID,Tags,URI
report.rtags.sort=id+
report.ruri.columns=id,uri
report.ruri.description=Custom: Lists ID and URI
report.ruri.filter=status:pending
report.ruri.labels=ID,URI
report.ruri.sort=id+
```

Every urgency setting has no value:

```bash
$ taskum _show | egrep -e "^urgency"
urgency.active.coefficient=
urgency.age.coefficient=
urgency.age.max=
urgency.annotations.coefficient=
urgency.blocked.coefficient=
urgency.blocking.coefficient=
urgency.due.coefficient=
urgency.next.coefficient=
urgency.priority.coefficient=
urgency.project.coefficient=
urgency.scheduled.coefficient=
urgency.tags.coefficient=
urgency.waiting.coefficient=
```

All other custom settings you may find out with:

`$ taskum rc.color=yes show`

### Enviroment

To use Taskwarrior with non-default data dir and conf file, set `TASKDATA` and
`TASKRC`. To build them automatically, we use our own variables:
- To locate the data dir the varibale `TASKUM_DATA` is used. If this is not set,
  `${XDG_DATA_HOME}/taskum` respec. `${HOME}/.local/share/taskum` will be used
  instead.
- To locate the conf dir the variable `TASKUM_CONFIG` is used. If this is not
  set, `${XDG_CONFIG_HOME}/taskum` respec. `${HOME}/.config/taskum` wil be used
  instead. The conf file is named `taskumrc`.

### Notes

-   You may write all subcommands and options of `taskwarrior-um` without masking `-`. So, instead of
    `-help` you may use `help`.
-   Currently, `uri` field needs to have a scheme one of this: http, https, ftp,
    ftps, dav, davs, gopher, webdav, webdavs.
-   Taskwarrior stores its data in json format. Exporting and converting your data is
    quite easy.

### Bugs & Requests

Report it on 'https://github.com/D630/taskwarrior-um'.

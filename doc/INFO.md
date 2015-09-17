#### INFO

In order to add a record, we have to specify minimum an URI. Without any arguments, `read` prompts you to build the command arguments. Take care to seperate tags with `;`. If you do not specify `project`, `name` or `note`, they will get the default value `null`. A fully formulated `add` command could look like this:

```
$ taskum add \
    uri:"http://taskwarrior.org/docs" \
    name:taskwarrior \
    note:"some tutorials and introductions" \
    depends:1 \
    project:software.internet.bookmarking \
    +taskwarrior \
    +documentation \
    +cms
```

The default subcommand is the report `ruri` without a filter:

```
$ taskum -n
 1 http://taskwarrior.org
 2 http://taskwarrior.org/docs
```

#### Metadata Items (Fields)

In Taskwarrior, every task needs to have a description. In doing so, it is irrelevant, if a description conforms with an another description of a different task. To identify a task, there are the `UUID` field as permanent key and the [id](http://taskwarrior.org/docs/ids.html) field as temporary assignment (line counted relative to the entries).

The first difference in using Taskwarrior as bookmark manager is that there is -- of course -- no tasking and nothing, what should be completed. In taskwarrior-um all records have the status `pending` or `deleted`, and we do not need all the subcommands and built-in-attributes (for example [urgency](http://taskwarrior.org/docs/urgency.html)), which imply the differentiation between `pending`, `completed` etc.

The second ascpect is that we ignore the description attribute; a bookmark should be free to have a description. Unfortunately, we can not rename the label of this attribute, because it is built-in. For this reason, every record gets a `description` with the value `uri` as place holder.

The built-in attributes and metadata, that we consider, are `project` and `depends`. Furthermore, we will use `tags`, which are arbitrary words in Taskwarrior. The following UDAs are defined and used:

```
Name     Type   Label    Allowed Values Default Usage Count
-------- ------ -------- -------------- ------- -----------
name     string Name                    null    0
note     string Note                    null    0
uri      string URI                             0
```

A data set is structured like this:

```
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
Name                  taskwarrior
Note                  A command line todo manager
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
Name                 taskwarrior
Note                 some tutorials and introductions
URI                  http://taskwarrior.org/docs
```

Like I said, we should ignore `description` and `urgency`. On the `depends` attribute we give a different interpretation as `reference` attribute.

#### Reports

taskwarrior-um comes along with 35 built-in and custom reports. The modified buit-in reports are `ls`, `newest` and `oldest`. The custom reports are:

```
rmeta            Custom: Shows ID, Pro, Tags and Ref
rname            Custom: Shows ID, Name and URI
rnote            Custom: Shows ID and Note
rpro             Custom: Shows ID, Project and URI
rref             Custom: Shows ID, Ref and URI
rtags            Custom: Shows ID, Tags and URI
```

You may list all supported reports with Taskwarriors's subcommand `reports`:

`$ taskum reports`

Feel free to follow up and build your own reports.

#### Configurations

Along with this script comes an examplary configuration file named `taskumrc`.

The defaults are:

```
$ taskum _show | egrep -e "^default\."

default.command=ruri
default.due=
default.project=null
```

The UDAs are:

```
$ taskum _show | egrep -e "^uda"

uda.name.default=null
uda.name.label=Name
uda.name.type=string
uda.name.values=
uda.note.default=null
uda.note.label=Note
uda.note.type=string
uda.note.values=
uda.priority.label=Priority
uda.priority.type=string
uda.priority.values=H,M,L,
uda.uri.label=URI
uda.uri.type=string
uda.uri.values=

```

The modified and custom reports are:

```
$ taskum _show | egrep -e "^report.(r[^e].*|new.*|old.*|list)"

report.ls.columns=id,entry.epoch,modified.epoch,name,uri,project.full,tags.list,depends.
report.ls.description=Modified: Shows all pending tasks
report.ls.filter=status:pending
report.ls.labels=ID,ED,MD,Name,URI,Pro,Tags,Ref,Note
report.ls.sort=id+
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
report.rmeta.description=Custom: Shows ID, Pro, Tags and Ref
report.rmeta.filter=status:pending
report.rmeta.labels=ID,Pro,Tags,Ref
report.rmeta.sort=id+
report.rname.columns=id,name,uri
report.rname.description=Custom: Shows ID, Name and URI
report.rname.filter=status:pending
report.rname.labels=ID,Name,URI
report.rname.sort=id+
report.rnote.columns=id,note
report.rnote.description=Custom: Shows ID and Note
report.rnote.filter=status:pending
report.rnote.labels=ID,Note
report.rnote.sort=id+
report.rpro.columns=id,project.full,uri
report.rpro.description=Custom: Shows ID, Project and URI
report.rpro.filter=status:pending
report.rpro.labels=ID,Pro,URI
report.rpro.sort=id+
report.rref.columns=id,depends.list,uri
report.rref.description=Custom: Shows ID, Ref and URI
report.rref.filter=status:pending
report.rref.labels=ID,Ref,URI
report.rref.sort=id+
report.rtags.columns=id,tags.list,uri
report.rtags.description=Custom: Shows ID, Tags and URI
report.rtags.filter=status:pending
report.rtags.labels=ID,Tags,URI
report.rtags.sort=id+
report.ruri.columns=id,uri
report.ruri.description=Custom: Shows ID and URI
report.ruri.filter=status:pending
report.ruri.labels=ID,URI
report.ruri.sort=id+
```

Every urgency setting has no value:

```
$ taskum _show | egrep -e "^urgency"

urgency.active.coefficient=
urgency.age.coefficient=
urgency.age.max=
urgency.annotations.coefficient=
urgency.blocked.coefficient=
urgency.blocking.coefficient=
urgency.due.coefficient=
urgency.inherit.coefficient=
urgency.next.coefficient=
urgency.project.coefficient=
urgency.scheduled.coefficient=
urgency.tags.coefficient=
urgency.uda.priority.H.coefficient=
urgency.uda.priority.L.coefficient=
urgency.uda.priority.M.coefficient=
urgency.waiting.coefficient=
```

All other custom settings you may find out with:

`$ taskum rc.color=yes show`

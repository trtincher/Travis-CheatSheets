# Delete All Node Modules

## Mac

```
//cd into whatever directory you want to search
$ cd documents

//this finds all the node_modules, run first to check
$ find . -name "node_modules" -type d -prune | xargs du -chs


//this is irreversible so be careful!!!
$ find . -name "node_modules" -type d -prune -exec rm -rf '{}' +

```

## Windows

```
$ cd documents
$ FOR /d /r . %d in (node_modules) DO @IF EXIST "%d" echo %d"
$ FOR /d /r . %d in (node_modules) DO @IF EXIST "%d" rm -rf "%d"
```

# SATellite control

This script is used by me daily on my mobile/tablet devices to synchronize project data with my server; in addition, it provides easy way to run build commands after synchronization is done.

## How it works (in general)

Each project is version controlled by git on both satellite and master. A `remotepath` script is used to do translation between local/satellite project path and master project path. For each data synchronization request, the `sat` command then proceeds to do these things:

1. if the project doesn't exist on master, provides option to clone the same repo, same branch on master
2. check git revision number on satellite and master
3. if they don't match, fast forward one of the repos to match the other, if they are on the same linear history (during this process, do `stash save` and `pop` appropriately so you don't lose uncommitted changes)
4. once the git revisions match, use rsync to sync uncommitted changes one way or the other. The rsync command is made to exclude `.git` directory and also anything ignored by git itself in `.gitignore`.

## `sat [ pull | push ]`

`pull` performs synchronization of uncommitted changes from master to satellite; `push` does the reverse.

## `sat exec`

Convenient way to `exec` a command on server in the project directory. Without any argument this falls into a `ssh` auto-`cd`ed at the project level.

## Examples

```
# push any local change and run cabal commands 
$ sat push && sat exec cabal build
# push any local change and run lein commands
$ sat push && sat exec lein midje
```

## Caveats

* The shebang directive points to termux bash; as I'm using these scripts within Termux (feel free to change that to anything else)
* The long script in `git_sync_commit` is in zsh syntax, as my server's login shell is zsh; if you use bash you should change those interactive prompt lines to compatible bash formats (i.e., `read -p`)

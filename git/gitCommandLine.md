


# git command line usage

## WARNING:

when dealing with github repos, remember to:

* keep private your emai address\
  this will allow you to use github provided *fake email address*,\
   ( something like `<random_number>-<username>@users.noreply.github.com` )

* block any commit that brings a different author's email address from the one expected\
    ( look at email settings in github account's profile )

## base commands:
    
### config param listing ( local and global )

```sh

    git config -l
    
    git config --global -l

```


### declaring a global default gitignore

An actual `.gitignore` file, that resides locally in a repo, _should list_\
files that are a part of a project and that we do not want to be "versioned" ( e.g. artifacts )\
Everything else (e.g. IDE related junky files ) should be listed in a global gitignore file ( e.g. .project, .settings, .idea, etc...) :

```shell script

    git config --global core.excludesfile ~/path/to/global/gitignore

```

here is a useful example:

```text

/.idea/
/*/build-*-Desktop-Debug/
CMakeLists.txt.user
/**/.settings/
/**/.project
/**/.classpath
/**/target/

```

Note:

* to do granular file inclusions, you have to EXPLICITLY include all parent directories.
* !/path/**     matches all ( directoies, files, and children also )
* !/path/*      matches files only
* !/path/*/     matches directories only



### remove config param ( local and global )

```sh

    git config unset user.name
    git config unset user.email

    git config --global --unset user.name
    git config --global --unset user.email

```


### set config params ( local and global )

```sh

    git config user.name "username"
    git config user.email "my_github_assigned_fake_email@github.com"

    git config --global user.name "username"
    git config --global user.email "my_github_assigned_fake_email@github.com"

```


### branch creation and checkout

```sh

    git branch pippo
    
    git checkout pippo
    
```

### commit

_please, check **user.name** e **user.email**_

```sh

    git status

    git add .

    git commit -m "my message"

```

### push

please, **squash** all commits in a **local** branch before merging\
( for remotes, github will propose "squash and merge" on the _PR_

```sh

    # checkout the branch to squash
    git checkout test


    #-------------------------
    # squashing current branch
    #-------------------------
    
    # where 3 is the commit counts
    git rebase -i HEAD~3
    
    # then, in the editor, you choose wich commit to keep ( peak ) and which to squash
    
    pick 3d25738 feature 01 completed
    squash 74a3f52 feature 01 second step
    squash 561c7e9 feature 01 first step
    
    # save and quit your editor
    
    #-------------------------
    
    # then push
    
    git push origin

```

### fetching

```sh

     git fetch origin

```


### log printing

concise printing:

```sh

    git log --oneline --graph --pretty --all --oneline

```
    
    
shows author:
    
```sh

    git log --oneline --graph --pretty --all

    git log --oneline --graph --pretty

```


### undoing a commit from local branch

moves back the **current** branch to the given commit, **without** loosing files\
( they will be found in index, ready to be committed again )

```sh

    git reset --soft d9fede4

```

### stashing

```sh

    # lists stash
    git stash list
    
    # stashes current files
    git stash --include-untracked
    
    # do whatever you want with HEAD
    
    # lists stash
    git stash list
    
    # applies the stash
    git stash apply "stash@{n}"
 
```


### after a github "squash and merge"

you tipically end up with somethig like this:

```sh

    *   fbe3d6b (origin/master, origin/HEAD) feature_01 completed!       <-- this is the result of a "squash and merge"
    | * 3d25738 (origin/feature_01, feature_01) feature 01.g
    | * 74a3f52 feature 01.f
    | * 561c7e9 feature 01.e
    | * 7eba50a feature 01.d
    | * 26d8aab feature 01.c
    | * 018d5e6 feature 01.b
    | * ddf5cfc feature 01.a
    |/
    * d9fede4 (HEAD -> master) Initial commit


```

so you need to rebase master branch over origin/master

```sh

    # checkout if not yet
    git checkout master
    
    # rebase current branch upon origin/master
    git rebase origin/master

```

then you can proceed with **removing** the "old closed" **local and remote** branch.


### tagging

#### WARING: try to tag only locally !! ( risk is to taint other devs repos )

```sh

    # listing
    git tag

    # creating a tag
    git tag tagName
    
    #to a specific commit
    git tag tagName 9fceb02
    
    # rename
    git tag newName oldName
    git tag -d oldName
    

    # remove local tag
    git tag -d tagName
    
    # remove a remote tag( push an void reference to the remote tag )
    git push origin :refs/tags/tagname
    
    

```

## advanced:

### some explanation

`origin/HEAD` is a pointer to the "default" remote branch\
( will be checked out automatically when cloning a repo )

### forcing a push

```sh

    git push --force origin

```

### if a branch was deleted from github-web and I still need to delete local remote-tracking branch:
    
    
deletes **ALL** "broken" remote-tracking branches towards a given remote repo

```sh

    # fake removal ( just pretending and printing output )
    git remote prune --dry-run origin
    
    
    # true removal
    git remote prune origin

```
    
deletes a specific given "broken" remote-tracking branch, BUT NOT the remote one

```sh

    git branch -rd origin/test

```

### delete a local branch
   
```sh

    git branch -D test

```



### having an ugly merge

```sh

    *   fbe3d6b (origin/master, origin/HEAD) testing markdown
    |\
    | * 3d25738 (test, origin/test) testing markdown
    | * 74a3f52 testing markdown
    | * 561c7e9 testing markdown
    | * 7eba50a testing markdown
    | * 26d8aab testing markdown
    | * 018d5e6 testing markdown
    | * ddf5cfc testing markdown
    |/
    * d9fede4 (HEAD -> master) Initial commit

```

I would like to shrink al that.\
still need to figure out how from the command line.


## error cases:

### using HTTPS repo, git doesn't ask for login credentials

check di variables:

* SSH_ASKPASS // env variable
* GIT_ASKPASS // env variable
* core.askPass // git config param

if needed, override them.\
for exaple, from command line:

```sh

    # overriding console env var:
    SSH_ASKPASS="" && git push origin test

```







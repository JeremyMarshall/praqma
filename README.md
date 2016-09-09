# praqma
A PRAGMATIC WORKFLOW

This is a test repo to try out http://www.praqma.com/stories/a-pragmatic-workflow/

Which uses git, ghi and waffle and Jenkins to integrate changes and move status

What you will need 

- assuming a mac and homebrew

`brew install rbenv ruby-build`

- Add rbenv to bash so that it loads every time you open a terminal
```bash
echo 'if which rbenv > /dev/null; then eval "$(rbenv init -)"; fi' >> ~/.bash_profile
source ~/.bash_profile

# Install Ruby
rbenv install 2.3.1
rbenv global 2.3.1
ruby -v
```

https://gorails.com/setup/osx/10.11-el-capitan

- Fix ruby gems for our neurotic firewall
```bash
gem sources -r https://rubygems.org/
gem sources -a http://rubygems.org/
```
http://stackoverflow.com/questions/10246023/bundle-install-fails-with-ssl-certificate-verification-error

- config
- 
```bash
ghi config --auth YOUR_GITHUB_USER
```

- Add labels (only on new repos)
```bash 
ghi label "Action - awaiting feed-back" -c 6EB82C
ghi label "Action - needs grooming"     -c 009800
ghi label "Prio 1 - must have"          -c E83D0F
ghi label "Prio 2 - should have"        -c EB6420
ghi label "Prio 3 - could have"         -c E8850F
ghi label "Prio 4 - won't have"         -c E8A80F
ghi label "Size 0 - Briefing"           -c C7DEF8
ghi label "Size 1 - small"              -c 20B4E5
ghi label "Size 2 - medium"             -c 208FE5
ghi label "Size 3 - large"              -c 0052CC
ghi label "Size 4 - too big"            -c 100B6B
ghi label "Status - duplicate"          -c 111111
ghi label "Status - workable"           -c EDEDED
ghi label "Status - in progress"        -c EDEDED
ghi label "Status - up Next"            -c EEEEEE
```

- Add aliases to git

`git config --global --edit`

and paste

```
[alias]
  addremove = add -A .
  deliver = "!BRANCH=`git symbolic-ref --short HEAD`;REMOTEBRANCH=ready/$BRANCH;   git push origin $BRANCH:$REMOTEBRANCH && git branch -m delivered/$BRANCH"
  co = checkout
  wrapup = "!f() { MSG='close #'`git symbolic-ref --short HEAD | sed 's/-/ /g'`; echo $MSG > ~/WRAPUP_EDITMSG; git addremove; git commit -F ~/WRAPUP_EDITMSG; rm ~/WRAPUP_EDITMSG; }; f"
  purge-all-delivered = "!f() { git co `git default-branch`; git branch | grep 'delivered/' |   sed 's/delivered\\///g' | xargs -I %br sh -c 'git branch -D delivered/%br; git   push origin :%br' 2>/dev/null; }; f "
  default-branch = "!f(){ if git show-ref refs/heads/master >/dev/null 2>&1; then   echo master; elif git show-ref refs/heads/gh-pages >/dev/null 2>&1; then echo   gh-pages; fi; }; f"
  issue-branch = "!f() { MATCH=#$1:; ghi show $1 2>/dev/null | grep $MATCH | sed   s/$MATCH/$1/g | sed 's/ /-/g' | sed s/[:\\']//g; }; f"
  issue-wip = "!f() { ghi label $1 'Status - in progress'; }; f"
  work-on	= "!f() { BRANCH=`git issue-branch $1`; git fetch origin; git co $BRANCH   2> /dev/null || git co -b $BRANCH origin/`git default-branch`; git issue-wip $1; ghi assign $1;  }; f"
```

##WHAT DO THE ALIASES DO?

Alias | Comment 
---|---
`git issue-wip <issue-number>` | Set the label used to mark an issue as work in progress. In waffle.io it will be moved to the corresponding column.
`git issue-branch <issue-number>` | Take the title of the issue and based on this, it will construct a string that will essentially make up a good branch name for working on this issue.
`git default-branch` | Determine if our integration branch is master or gh-pages, simply assuming that if master exists, then that takes precedence.
`git work-on <issue-number>` | Fetch from origin and branch off from the <origin/default-branch>. This has the intended side effect, that you track the remote branch default branch - so at any point in your work process a simple `git pull --rebase` will keep you in sync.
`git addremove` |  Takes every change in the workspace and adds or removes it to the index. If you’re familiar with Mercurial Hg, you know the usefulness of this already.
`git wrapup` |  is used to automatically addremove everything and then commit it with a close message - and the title of the issue.
`git deliver` |  Push your current branch to a ready/<current-branch> on the remote and then rename your local branch to delivered/<current-branch>. You might not want to delete your branch just yet, until you’ve seen that it’s successfully integrated.
`git purge-all-delivered` | will remove all local branches named delivered/** and at the same time remove any corresponding branches you may have pushed to origin - without the ready/** prefix.

## Workflow

- list mine
`ghi list --mine`


- Work on an issue 
`git work-on <issue>` will create a branch called `<issue>-<title>` and also move it in https://waffle.io

`git push origin <issue>` Seems to be needed to update waffle. wtf? 


- Wrap up
`git wrapup` will add and remove and commit


- Open an issue
`ghi open` which opens an editor. The first line is the title the rest is the description

## Labels
Do stuff with labels on issues

```
$ ghi help label
usage: ghi label <labelname> [-c <color>] [-r <newname>]
   or: ghi label -D <labelname>
   or: ghi label <issueno> [-a] [-d] [-f]
   or: ghi label -l [<issueno>]

    -l, --list [<issueno>]           list label names
    -D                               delete label

Label modification options
    -c, --color <color>              color name or 6-character hex code
    -r, --rename <labelname>         new label name

Issue modification options
    -a, --add                        add labels to issue
    -d, --delete                     remove labels from issue
    -f, --force                      replace existing labels
```
https://github.com/stephencelis/ghi/issues/105

You need to use the whole label to add it to an issue or it will create a new label and add that to your issue!

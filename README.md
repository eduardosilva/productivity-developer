# Productivity Developer

These are  my thoughts on how to be a better developer improving performance in your daily and sometimes trivial activities.

## Table of Contents

- [Git](#git)
- [Bash](#bash)
- [Regex](#regex)
- [VSCode](#vscode)
- [VIM](#vim)

## Git

### Configure prompt with git information

Put git information in your prompt (i.e.: branch and status) is a great way to get these without execute commands. There are many examples about how to do that, so that is a link with one of them [Git Prompt](https://github.com/magicmonty/bash-git-prompt) 

### Auto-correct

Enable auto-correct and suggested commands will run after 2 seconds.

before:

```bash
git statsh pop

git: 'statsh' is not a git command . See 'git --help'.

the most similar command is
    stash
```

```bash
git config --global help.autocorrect 20
```

after:

```bash
git statsh pop

WARNING: You called a Git command named 'statsh', witch does not exist.
Continuing in 2.0 seconds, assuming that you meant 'stash'.
```

### Changing push strategies

Using `current` option to push branches with same name.

before:

```bash
git push
fatal: The current branch mko has no upstream branch.
To push the current branch and set the remote as upstream, use

    git push --set-upstream origin mko
```

configure strategy:

```bash
git config --global push.default current
```

after:

```bash
git push
Total 0 (delta 0), reused 0 (delta 0), pack-reused 0
To [origin branch]
 * [new branch]      mko -> mko
```

### Enable Git's autosquash feature by default

```bash
git config --global rebase.autosquash true
```



### .gitattributes Line Endings

Git can actually be configured to automatically handle line endings using a setting called autocrlf. This automatically changes the line endings in files depending on the operating system. However, you shouldn't rely on people having correctly configured Git installations. If someone with an incorrect configuration checked in a file, it would not be easily visible in a pull request and you'd end up with a repository with inconsistent line endings.

The solution to this is to add a .gitattributes file at the root of your repository and set the line endings to be automatically normalised like so:

```text
# Set default behavior to automatically normalize line endings.
* text=auto
```

### RERERE

[git-rerere](https://git-scm.com/docs/git-rerere) is a command that allows you to save resolved conflict to replicate the same solution in the future.

```bash
git config --global rerere.enabled 1
```

#### Forgot recorded wrong rerere resolution

If you have enabled rerere feature and record a wrong merge conflict, you can erase/forgot this merge resolution with following command:

```bash
git rerere forget <pathtofile>
```

### Show all aliases

```bash
git config --get-regexp alias
```

### Folder root

To get the root folder based in the .git folder configuration

```bash
git rev-parse --show-toplevel
```

To move to the root folder

```bash
cd $(git rev-parse --show-toplevel)
```

That's why I have a function in the `.bashrc` called `root`

```bash
function root {
    cd $(git rev-parse --show-toplevel)
}
```

### Go to the last branch

you can jump back to your previous Git branch with `git checkout -`

### Rebase

I usually commit my changes frequently but I don't like to push or merge a lot of commits. To fix this git rebase is perfect.

Example:

```bash
git rebase -i <base>

# Commands:
# p, pick = use commit
# r, reword = use commit, but edit the commit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
# f, fixup = like "squash", but discard this commit's log message
# x, exec = run command (the rest of the line) using shell
# d, drop = remove commit
```

### Push local git branch to a remote with a different name

I've worked in a company where they use jira tickets as branch names. I don't like this pattern because it is difficult to know about what is that branch without access the jira. So there's a way to use a local, intuitive branch name and keep some strange remote branch name at same time :)

```bash
git push -u origin localBranchName:remoteBranchName
```

### Pull all subfolder in a single command

Sometimes I need to work with a lot of repositories and that is a shortcut to maintain them up to date.

```bash
for d in ./*/ ; do (cd "$d" && echo "$d" && [ -e .git ] &&  g pull); done
```

* Start a loop based in your current folder;
* Run `cd` command to directory;
* Print current directory;
* Check if .git path exists inside the current directory;
* Run `git pull` in the current directory; 

### Git Hooks

There are many several useful git hooks to increase productivity. [This](https://gist.github.com/eduardosilva/d84a6ccfb521300ae0cba7ebd2b95f39) is a list with some useful hooks.

## Bash

### Navigation

* `CTRL+A`: beginning of the line
* `CTRL+E`: ending of the line
* `ALT+F`: next word
* `ALT+B`: previous word


### Shortcut to the latest command

You can use `CTRL+R` and type to search last command 

First:
```bash
git status
```

After using `CTRL+R` type `status`


### Go to the last directory

Use `cd -` to go back into the directory you were in before your last cd command. 


### Using pushd and popd to navigate between directories

Using pushd to go to .config directory

```bash
pushd .config/
```
Using popd to go back to the previous directory


```bash
popd
```

### Extract column from a tabular output


```bash
git status -s | awk '{print $2}'
```

or you can create a function as recommended in the reference (it was what I did):

```bash
function col {
    awk -v col=$1 '{print $col}'
}
```

```bash
git status -s | col 2
```

[Reference](https://blog.developer.atlassian.com/ten-tips-for-wonderful-bash-productivity/)

### Search, remove, change directories and another things using wildcard

Bash has tree basic wildcards:

* Asterisk `*` - any character for zero or more times;
* Question mark `?` - fixed number of character;
* Square brackets `[]` - match with the characters of a defined range or a group of character - match with the characters of a defined range or a group of characters;


Searching filenames that starts with `t`.

```bash
ls t*
```

Removing filenames that start with `t` with three characters with a .txt extension.

```bash
rm t???.txt
```

Searching filenames that contains `m` or `u` character

```bash
ls [m-u]*.*
```

### Download http body response from request made by content file

Let's create a `movies.txt` file with imdb title ids:

```
tt0068646
tt0111161
```

Now we can get the html body content like below:

```bash
while read line; do curl "https://www.imdb.com/title/${line// /%}/" -o "${line}.txt"; done < movies.txt;
```

### Get current branch name

```bash
git branch | grep -Po "(^\*\s)\K(.*)"
```

I have a function my `.bashrc` to get the current branch name

```bash
function branchName {
    git branch | grep -Po "(^\*\s)\K(.*)"
}
```

This way, I can run a command like below without to worry about read or search for the current branch name:

```bash
git push -u origin $(branchName)
```

### Checkout to a specific branch with name contains a word

```bash
git branch | grep dev | xargs -I % git checkout %
```

### Find & copy command

```bash
find . -name '*-config.yaml' -exec cp {} ../otherFolder/ \;
```

## Regex

### Capture group

This is a simple and rustic example what you can do with regex capture group.

Change this:

```html
<a href="https://myerrordomain.com/">Link 1</a>
<a href="https://myerrordomain.com/">Link 2</a>
<a href="https://myerrordomain.com/">Link 3</a>
```

For:

```html
<a href="https://mydomain.com/p/1">Link 1</a>
<a href="https://mydomain.com/p/2">Link 2</a>
<a href="https://mydomain.com/p/3">Link 3</a>
``` 

In the VSCode Find feature enable Regular Expression (.*).

Find:

```bash
(<a href=")(https://myerrordomain.com/")(>Link )([0-9])
```

Replace for:

```bash
$1https://mydomain.com/p/$4"$3$4
``` 

> Yes, you can easily improve this regex, this only a example to getting started.

### Changing case with regex


Change this:

```
item 1: electronics
item 2: farm
item 3: music
```

For:

```
item 1: Electronics
item 2: Farm
item 3: Music
```

In the VSCode Find feature enable Regular Expression (.*).

Find:

```bash
(item [0-9]: )(\w)(\w*)
```

Replace for:

```bash
$1\u$2$3
``` 

* \u: uppercase;
* \l: lowercase;

## VSCode

Useful plugins:

* [VIM Plugin](https://marketplace.visualstudio.com/items?itemName=vscodevim.vim)
* [Code Spell Checker](https://marketplace.visualstudio.com/items?itemName=streetsidesoftware.code-spell-checker)
* [Docker](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker)
* [Thunder Client](https://marketplace.visualstudio.com/items?itemName=rangav.vscode-thunder-client)

## VIM

### Enable check spell

```
set spell
```



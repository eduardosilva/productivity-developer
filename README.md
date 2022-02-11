# Productivity Developer

These are  my thoughts on how to be a better developer improving performance in your daily and sometimes trivial activities.

## Table of Contents

- [Git](#git)
- [Bash](#bash)
- [Regex](#regex)
- [VSCode](#vscode)
- [VIM](#vim)

## Git

### RERERE

[git-rerere](https://git-scm.com/docs/git-rerere) is a command that allows you to save resolved conflict to replicate the same solution in the future.

```bash
git config --global rerere.enabled 1
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

#### Squash commits




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



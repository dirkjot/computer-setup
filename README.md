# computer-setup


## OS-X

Set up for a clean OS-X laptop.  I did not make a script as my
requirements vary.

- [shift it](https://github.com/fikovnik/ShiftIt),
[direct download](https://github.com/fikovnik/ShiftIt/releases/download/version-1.6.3/ShiftIt-1.6.3.zip): Tool to move
windows side by side etc, similar to
WindowKey Left etc on Windows.
- [FlyCut](https://github.com/TermiT/Flycut/), [direct download](https://github.com/TermiT/Flycut/releases/download/1.8.2/Flycut.app.1.8.2.zip) Cut and paste memory tool.   Enable "Stick Bezel"  and "Launch on start up".  

- [Iterm2](https://iterm2.com/),  [direct download](https://iterm2.com/downloads/stable/iTerm2-3_0_10.zip) is a terminal replacement 

- [Aquamacs](http://aquamacs.org/), [direct download](http://aquamacs.org/download-release.shtml)

- [Bash it](https://github.com/Bash-it/bash-it) install by running this script: 
  ```
  git clone --depth=1 https://github.com/Bash-it/bash-it.git ~/.bash_it
  # Then make it automatic with 
  ~/.bash_it/install.sh
  # This may pull in the XCode developer tools, you want those.  
  # Start a new terminal and execute 
  bash_it enable completion git
  ```
    

- install IntelliJ
- install brew `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`
- brew tap caskroom/cask
- brew cask install meld
- brew cask install java  # (if not already installed)
- brew install markdown
- brew install ruby
- brew install node   # or ` brew install homebrew/versions/node4-lts`
- brew install joe

- brew install mongodb
- sudo mkdir -p /data/db; env ME=$(id -un) sudo chown -R  ${ME}: /data
- brew services start mongodb  [ does not work right today ]


for [git duet:](https://github.com/git-duet/git-duet)
- brew tap git-duet/tap
- brew install git-duet


## simple .gitconfig:
I don't always use Nano as my default editor, but if you are working with clients who don't use VIM it certainly helps. 

```
[user]
        name = dirkjot
        email = dirkjot@dirkjot
[alias]
	cb = rev-parse --abbrev-ref HEAD
	ci = commit
	co = checkout
	dci = duet-commit
	lola = log --graph --decorate --pretty=oneline --abbrev-commit
	lola9 = log --graph --decorate --pretty=oneline --abbrev-commit -n9
	pl = pull --ff-only
        st = status
        unpushed = log --branches --not --remotes --simplify-by-decoration --decorate --oneline
	ready = rebase -i @{u}
	lg = log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr)%C(bold blue)<%an>%Creset' --abbrev-commit
	story = "!f() { n=$1; story=$(sed -e \"s/#//\" <<< $n); branch=$(git branch -a | grep -o \"$story[-_a-zA-Z]*\" | head -n 1); git checkout $branch; }; f"
	
[core]
	editor = /usr/bin/nano
	excludesfile = /Users/pivotal/.gitignore_global
	autocrlf = input
[diff]
    tool = meld
[difftool]
    prompt = false
[difftool "meld"]
    cmd = meld "$LOCAL" "$REMOTE"
[merge]
    tool = meld
[mergetool "meld"]
    cmd = meld "$LOCAL" "$MERGED" "$REMOTE" --output "$MERGED"

```



## IntelliJ

Install the following plugins
- lines sorter [also on github](https://github.com/syllant/idea-plugin-linessorter)
- angular
- karma
- nodeJS

Set up git-duet with intellij by adding this script to your repo, then pointing intellij to the script as your git exectuable (search in settings for `path to git`). We usually call this `intellij_git_duet_wrapper.sh`:

```
#!/usr/bin/env bash

if [[ "$5" == "commit" ]] ; then
  set -- "${@:1:4}" "duet-commit" "${@:6}"
fi

exec git "$@"

```

This script has now been updated for IntelliJ 18.x, which sends two `-c OPTION` commands with every git commit.  


## Hooks

### precommit

This precommit hook will run jshint and scan your sourcefiles for 'verboten' words.  In the example: `fdescribe(`, `fit(` as first token on line).  This assumes your tests live in `test/` and there is a `test/node_modules` directory that you do not want to check.  

```
#!/usr/bin/env bash

# INSTALL THIS WITH:
# bash$  ln -s $PWD/scripts/precommit_hook.sh .git/hooks/pre-commit

grunt jshint # or whatever runs your linter
hintresult=$?

find test/ -type f -name "*spec.js"  -not -path "test/node_modules/*" -exec egrep '^ *(fit|fdescribe) *\(' {}  \+
findresult=$?

if [[ $findresult -ne 0 ]]; then
  if [[ hintresult ]]; then
       exit 0
  fi
fi
exit 1

```

### postreceive
This hook notifies jenkins that a new commit has been made.  You can put the polling frequency to once an hour or so once you have this running. 

```
#!/usr/bin/env bash
set -e

# INSTALL THIS WITH:
# bash$  ln -s $PWD/scripts/postreceive_hook.sh .git/hooks/post-receive


curl http://JENKINSURL:8080/git/notifyCommit?url=https://github.com/GITHUBUSER/GITHUBREPO.git
exit 0

```

## Setup an ssh config

Greatly improve your ssh experience.  This is an example, partially stolen from [nerderati.com](http://nerderati.com/2011/03/17/simplify-your-life-with-an-ssh-config-file/)

```
Host tunnel
    HostName database.example.com
    IdentityFile ~/.ssh/coolio.example.key
    LocalForward 9906 127.0.0.1:3306
    User coolio
Host github.com
    IdentityFile ~/.ssh/github.key
```



# computer-setup


## OS-X

Set up for a clean OS-X laptop.  I did not make a script as my
requirements vary.

- [shift it](https://github.com/fikovnik/ShiftIt),
[direct download](https://github-cloud.s3.amazonaws.com/releases/959084/72c0dcfa-ed3a-11e4-9d8e-cdafe2ee6e57.zip?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAISTNZFOVBIJMK3TQ%2F20161017%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20161017T032321Z&X-Amz-Expires=300&X-Amz-Signature=0e424ba996d4c7741aae86884b8347d77d521de04e91d432a40679c63695ee3f&X-Amz-SignedHeaders=host&actor_id=0&response-content-disposition=attachment%3B%20filename%3DShiftIt-1.6.3.zip&response-content-type=application%2Foctet-stream): Tool to move
windows side by side etc, similar to
WindowKey Left etc on Windows.
- [FlyCut](https://github.com/TermiT/Flycut/), [direct download](https://github-cloud.s3.amazonaws.com/releases/1502462/41c4d70e-88ad-11e6-8f6c-c51f67052c84.zip?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAISTNZFOVBIJMK3TQ%2F20161017%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20161017T032705Z&X-Amz-Expires=300&X-Amz-Signature=b167e10e4a3bfd6f873f73e805da7dc2ff5b34ad2ae7effd5ba44a067edc1ca7&X-Amz-SignedHeaders=host&actor_id=0&response-content-disposition=attachment%3B%20filename%3DFlycut.app.1.8.1.zip&response-content-type=application%2Foctet-stream) Cut and paste memory tool.   Enable "Stick Bezel"  and "Launch on start up".  

- [Iterm2](https://iterm2.com/),  [direct download](https://iterm2.com/downloads/stable/iTerm2-3_0_10.zip) is a terminal replacement 

- [Aquamacs](http://aquamacs.org/), [direct download](https://github-cloud.s3.amazonaws.com/releases/163782/df98aa78-7ec1-11e6-94e2-f2baf22a8138.dmg?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAISTNZFOVBIJMK3TQ%2F20161017%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20161017T033114Z&X-Amz-Expires=300&X-Amz-Signature=2852372ae195be018fcafa83540533b2ccf160788a97b283e5e356ea6a2dcb6a&X-Amz-SignedHeaders=host&actor_id=0&response-content-disposition=attachment%3B%20filename%3DAquamacs-Emacs-3.3.dmg&response-content-type=application%2Foctet-stream)

- [Bash it](https://github.com/Bash-it/bash-it) install by running this script: `git clone --depth=1 https://github.com/Bash-it/bash-it.git ~/.bash_it`

- install IntelliJ
- install brew 
- brew install markdown
- brew install ruby
- brew install node   or ` brew install homebrew/versions/node4-lts`
- brew install joe

- brew install mongodb
- sudo mkdir -p /data/db; env ME=$(id -un) sudo chown -R  ${ME}: /data
- brew services start mongodb  [ does not work right today ]


for [git duet:](https://github.com/git-duet/git-duet)
- brew tap git-duet/tap
- brew install git-duet


## simple .gitconfig:
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
[difftool "sourcetree"]
	cmd = opendiff \"$LOCAL\" \"$REMOTE\"
	path =
[mergetool "sourcetree"]
	cmd = /Users/pivotal/Applications/SourceTree.app/Contents/Resources/opendiff-w.sh \"$LOCAL\" \"$REMOTE\" -ancestor \"$BASE\" -merge \"$MERGED\"
	trustExitCode = true
[mergetool "bc3"]
	cmd = \"/Users/pivotal/Applications/Beyond Compare.app/Contents/MacOS/bcomp\"  \"$LOCAL\" \"$REMOTE\" -ancestor \"$BASE\" -merge \"$MERGED\"
	trustExitCode = true

[merge]
    tool=bc3
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

if [[ "$3" == "commit" ]] ; then
 shift 3
  exec git duet-commit "$@"
fi

exec git "$@"
```


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





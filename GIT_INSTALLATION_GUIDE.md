# Git Installation Guide

## Client CLI / GUI

### Win10

Download the Git Client from their website.

Install it with ALL the default choices. Also make sure that the conf choices for the Bash + GUI client are checked.

### ubuntu

Update the package list and install with the default choices:
```bash
sudo apt update
sudo apt install -y git
```

## Git Tools

Also install the file comparison tools, it will be necessary later during the configuration:

### Win10

Download the KDiff3 installer from their site `http://kdiff3.sourceforge.net/` & install it with ALL default choices.

### Ubuntu

Install KDiff3 from the command line (with its dependencies requested)
```bash
sudo apt-get install kdiff3
```


> * Other comparison tools such as Meld are available, but here only KDiff3 is considered.
> In case you prefer to install another, consider adapting the configuration described below.

## configure
 
To configure it globally (for all projects in the workstation), you must open a command line terminal.

> On win10 the following configuration steps are done on the Git Bash command line (not the same as CMD) previously installed.
 
### Config global
 
Add user & mail configuration:
```bash
git config --global user.name "<PRENOM_NOM>"
git config --global user.email "<MON_USER>@mycompany.com"
```

Add the core configuration:
```bash
# commit message editor
git config --global core.editor vim
 
# convert CRLF to LF on commit
git config --global core.autocrlf input
 
# default compression level : -1 zlib. 0 no compression, and 1..9 are various speed/size tradeoffs, 9 the slowest
git config --global core.compression 9
 
# avoid ignoring filemode (chmod)
git config --global core.fileMode false
```

Add aliases:
```bash
# alias - basic
git config --global alias.cl clone
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.cp cherry-pick
git config --global alias.visual '!gitk --all'
git config --global alias.dif 'diff --word-diff'
git config --global alias.difc 'diff --cached'
git config --global alias.difch 'diff --cached HEAD^'
git config --global alias.difr '!f() { git diff "$1"^.."$1"; }; f'

# alias - list logs : last 1, date shorten/relative, ..
git config --global alias.ls '!git --no-pager log -10 --decorate --pretty=format:"%C(yellow)%h%C(magenta)%d %C(reset)%s%C(blue) [%cn]%Creset"; echo'
git config --global alias.ll '!git --no-pager log -10 --decorate --pretty=format:"%C(yellow)%h%C(magenta)%d %C(reset)%s%C(blue) [%cn]%Creset" --numstat; echo'
git config --global alias.lds 'log --pretty=format:"%C(yellow)%h\\ %ad%C(magenta)%d\\ %C(reset)%s%C(blue)\\ [%cn]" --decorate --date=short'
git config --global alias.ld 'log --pretty=format:"%C(yellow)%h\\ %ad%C(magenta)%d\\ %C(reset)%s%C(blue)\\ [%cn]" --decorate --date=relative'
git config --global alias.le 'log --oneline --decorate'
git config --global alias.lg 'log --graph --abbrev-commit --decorate --format=format:"%C(bold blue)%h%C(reset) - %C(bold green)(%ar)%C(reset) %C(white)%s%C(reset) %C(dim white)- %an%C(reset)%C(auto)%d%C(reset)"'
git config --global alias.fl 'log -u'
git config --global alias.gr 'log --graph --decorate --oneline'
git config --global alias.dl '!git ll -1'
git config --global alias.dlr '!f() { git diff "$1"^.."$1"; }; f'

# alias - list files
git config --global alias.lf  '!git ls-files | grep -i'

# alias - stash
git config --global alias.sl 'stash list'
git config --global alias.sa 'stash apply'
git config --global alias.ss 'stash save'

# alias - reset
git config --global alias.r reset
git config --global alias.r1 'reset HEAD^'
git config --global alias.r2 'reset HEAD^^'
git config --global alias.rh 'reset --hard'
git config --global alias.rh1 'reset HEAD^ --hard'
git config --global alias.rh2 'reset HEAD^^ --hard'

# alias - list alias
git config --global alias.la '!git config -l | grep alias | cut -c 7-'
```

Add color config: 
```bash
git config --global color.ui always
git config --global color.branch.current 'yellow reverse'
git config --global color.branch.local yellow
git config --global color.branch.remote green
git config --global color.diff.meta 'yellow bold'
git config --global color.diff.frag 'magenta bold'
git config --global color.diff.old 'red bold'
git config --global color.diff.new 'green bold'
git config --global color.status.added yellow
git config --global color.status.changed green
git config --global color.status.untracked red
```

Add the Diff & Merge tools config: (Remember to take the corresponding lines according to your crawling and installation system)
```bash
# diff
git config --global diff.guitool kdiff3
git config --global difftool.kdiff3.prompt false
git config --global difftool.kdiff3.trustExitCode false
# diff LNX
git config --global difftool.kdiff3.cmd '/usr/bin/kdiff3 "$LOCAL" "$REMOTE"'
# diff WIN
git config --global difftool.kdiff3.cmd '"C:\Program Files\KDiff3\kdiff3.exe" "$LOCAL" "$REMOTE"'
 
# merge
git config --global merge.ff no
git config --global merge.commit no
git config --global merge.log true
git config --global merge.tool kdiff3
git config --global mergetool.kdiff3.trustExitCode false
# merge tool LNX
git config --global mergetool.kdiff3.path "/usr/bin/diff3"
git config --global mergetool.kdiff3.cmd '/usr/bin/diff3 "$LOCAL" "$MERGED" "$REMOTE" --output "$MERGED"'
# merge WIN
git config --global mergetool.kdiff3.path "C:\Program Files\KDiff3\kdiff3.exe"
git config --global mergetool.kdiff3.cmd '"C:\Program Files\KDiff3\kdiff3.exe" "$LOCAL" "$MERGED" "$REMOTE" --output "$MERGED"'
```

Add Pull & Push config:
```bash
# Pull  rebase true :  rebase branches on top of the fetched branch, instead of merging the default branch from the default remote when "git pull" is run
git config --global pull.rebase true
 
# Push default simple : refuse to push if the upstream branch’s name is different from the local
git config --global push.default simple
 
# rebase autostash :  automatically create a temporary stash entry before the operation begins, and apply it after the operation ends
git config --global rebase.autoStash true
```

Add global gitignore config: 
 * add the file to the configuration:
   ```bash
   git config --global core.excludesfile ~/.gitignore_global
   ```
   and create & edit the file:
   ```bash
   touch ".gitignore_global"
   # LNX
   vim .gitignore_global
   # WIN
   notepad .gitignore_global
   ```
   and add the next block
   ```
   .gitignore
    
   # OS junk files
   *.log
   *.swp
   *.tmp
   *.bak
  
   # Temporary Files
   ~$*
   .DS_Store
   [Tt]humbs.db
  
   # IntelliJ IDE
   /.idea
   out/*
   *.iml
   *.ipr
  
   # Eclipse  IDE
   .settings
   .project
   .classpath
  
   # Java compiled files
   *.class
   *.jar
   *.war
 
   # Maven
   target
  
   # Python compiled files
   *.pyc
   *.pyo
   __pycache__
   ```

### Add SSH key (always use)

Another important element to install is the SSH key. This is required when you want to use an SSH git repository, so it will be required to establish secure communication with the GitLab server.

To create and install:

With the help of Git Bash or terminal, create an SSH key on your computer (if you need more details about the procedure see: gitlab_doc or else_git-ssh_doc):
```bash
# generate an RSA key
ssh-keygen -o -t rsa -b 2048 -C <MON_USER>@mycompany.com -N ""
 
# copy the ssh key to the clipboard
cat ~/.ssh/id_rsa.pub | clip
```

 * choose all default options (ie enter, enter, enter all questions without writing anything)
 * DO NOT ENTER PASSPHRASE if it asks you
 * With the key in the clipboard, access the configuration of your profile in the server GitLab mycompany (User Settings> SSH Key)
 * Add this SSH key in the configuration of your profile
 * Tester l'accès depuis votre poste :
   * la connexion ssh 
   ```bash
   ssh -T git@gitlab.mycompany.com
   # réponse attendu : "Welcome to GitLab, @<MON_USER>!"
   ```
   * or by cloning a project in SSH, for example:
   ```bash
   git clone git@gitlab.mycompany.com:rd/sandbox/wicket-tuto-project.git
   ```

## Hooks & Templates

Git allow you to add templates for your projects (see a more detailed list here).

To be able to use them it is necessary to define the directory where the templates will be:
```bash
# sur Windows : Run As Administrator
mkdir -p ~/.git-templates/hooks
git config --global init.templateDir '~/.git-templates'
```

Add a message template for the default commit:

  * create and configure the default message
  ```bash
  touch ~/.git-templates/gitmessage.txt
  echo -e "&ref#XYZ : [msg]\n" > ~/.git-templates/gitmessage.txt
  git config --global commit.template ~/.git-templates/gitmessage.txt
  ```
  
As part of a client-side Git-Enforced-Policy, to be able to block commits without messages for example, you have to add a hook script (Hook) with the specific name "commit-msg". To do this you must:

 create the hook file with execute rights: 
  ```bash
  touch ~/.git-templates/hooks/commit-msg
  chmod a+x ~/.git-templates/hooks/commit-msg
  ```
  edit the file with VIM (OR NOTEPAD IF WINDOWS), adding the following script:
  ```
  vim  ~/.git-templates/hooks/commit-msg
  ```
  ```bash
  #!/usr/bin/env bash
 
  #-------- check is DEV branch (TO REACTIVATE WHEN READY) ---------------
  # set this to your active development branch
  #  develop_branch="develop"
  #current_branch="$(git rev-parse --abbrev-ref HEAD)"
 
  # only check commit messages on main development branch
  #[ "$current_branch" != "$develop_branch" ] && exit 0
 
  #-------- check commit message ---------------
  # regex to validate in commit msg
  commit_user_regex='(^(refs|fixes|closes) #[0-9]{4,5})|^&'
  commit_mvn_regex='^\[maven-release-plugin\]'
 
  error_msg="Aborting commit. Your commit message is missing or doesn't start by 'refs|fixes|closes #redmineIssueId' or '&'"
 
  if ! grep -iqE -e "$commit_user_regex" -e "$commit_mvn_regex" "$1"; then
    echo "$error_msg" >&2
    exit 1
  fi
  ```

If you already have a Git project cloned or initialized on your workspace, for this project to take into account the previously defined commit message template as well as the verification of the respect of the syntax of the commit messages, you must launch the following command:  
```bash
$ git init
#Reinitialized existing Git repository in C:/Users/<MON_USER>/DEV/<NOM_PRJET>/.git/
```
Finally, if you try to commit on your local branch, Git will display the following message:
```bash
$ git ci -m ""
#Aborting commit. Your commit message is missing either a Redmine Issue ('&REF#1111') or 'Merge'
```

# Git Installation Guide


## Client CLI / GUI

### Win10

Télécharger le client Git depuis leur site web.

L'installer avec TOUS les choix par défaut. S'assurer également que les choix de conf pour le client Bash + GUI sont cochés.

### ubuntu
Mettre à jour la liste de paquets et installer avec les choix par défaut :
```bash
sudo apt update
sudo apt install -y git
```

## Git Tools

Installer également l'outils de comparaison de fichiers*, il sera nécessaire plus tard pendant la configuration :

### Win10

Télécharger l'installateur KDiff3  depuis leur site : http://kdiff3.sourceforge.net/
l'installer avec TOUS les choix par défaut

### Ubuntu

Installer KDiff3 depuis la linge de commandes (avec ses dépendances demandés)
```bash
sudo apt-get install kdiff3
```


> * D'autres outils de comparaison tels que Meld sont disponibles, mais ici seulement KDiff3 est considéré.
> Dans le cas où vous préférez d'installer un autre, pensez à adapter la configuration décrite plus bas.

 ## Configurer 
 
 Pour le configurer de manière globale  (pour tous les projets dans le poste de travail), il faut ouvrir un terminal de ligne de commandes.

 >Sur win10 les étapes de configuration suivants sont faites sur la ligne de commandes Git Bash (pas la même que CMD) installé précédemment.
 
 ### Config global
 
 Ajouter la configuration d'utilisateur & mail :

git config --global user.name "<PRENOM_NOM>"
git config --global user.email "<MON_USER>@mycompany.com"
Ajouter la configuration core : 

# commit message editor
git config --global core.editor vim
 
# convert CRLF to LF on commit
git config --global core.autocrlf input
 
# default compression level : -1 zlib. 0 no compression, and 1..9 are various speed/size tradeoffs, 9 the slowest
git config --global core.compression 9
 
# avoid ignoring filemode (chmod)
git config --global core.fileMode false
Ajouter des alias: 

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
Ajouter la config des couleurs : 

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
Ajouter la config des outils de Diff & Merge :  (Pensez à prendre les lignes qui  correspond selon votre système d’exploration et installation)

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
Ajouter la config de Pull & Push :

# Pull  rebase true :  rebase branches on top of the fetched branch, instead of merging the default branch from the default remote when "git pull" is run
git config --global pull.rebase true
 
# Push default simple : refuse to push if the upstream branch’s name is different from the local
git config --global push.default simple
 
# rebase autostash :  automatically create a temporary stash entry before the operation begins, and apply it after the operation ends
git config --global rebase.autoStash true
Ajouter la config gitignore globale : 
rajouter le fichier à la configuration : 

git config --global core.excludesfile ~/.gitignore_global
et créer & éditer le fichier : 

touch ".gitignore_global"
# LNX
vim .gitignore_global
# WIN
notepad .gitignore_global
et rajouter le bloque suivant

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

### Rajouter les identifiants SSH (à toujours utiliser)

Un autre élément important à installer c'est la clé SSH. Ceci est requis lors qu'on souhaite utiliser un dépôt git SSH, donc elle sera demandé pour établir une communication sécurisé avec le serveur GitLab.

Pour la créer et installer :

Avec l'aide de Git Bash ou terminal, créer une clé SSH sur votre poste (

si besoin de plus de détail sur la procédure voir : gitlab_doc  ou encore  autre_git-ssh_doc ) :
# générer une clé RSA
ssh-keygen -o -t rsa -b 2048 -C <MON_USER>@mycompany.com -N ""
 
# copier la clé ssh dans le presse-papiers
cat ~/.ssh/id_rsa.pub | clip
choisir tous les  options par défaut (c.a.d. enter, enter, enter à tous les questions sans rien écrit)

NE PAS SAISIR DE PASSPHRASE si'il vous demande
Avec la clé dans le presse-papiers, accéder a la configuration de votre profile dans le serveur GitLab mycompany  (User Settings > SSH Key) 
Rajouter cette clé SSH dans la configuration de votre profile

 Tester l'accès depuis votre poste :
la connexion ssh 

ssh -T git@gitlab.mycompany.com
# réponse attendu : "Welcome to GitLab, @<MON_USER>!"
en clonant en SSH une projet, par exemple :  

git clone git@gitlab.mycompany.com:rd/sandbox/wicket-tuto-project.git


## Hooks & Templates

Git permettre de rajouter des modèles pour vos projets (voir une liste plus détaillée ici).

Pour pouvoir les utiliser il faut définir le répertoire où se trouveront les modèles : 

# sur Windows : Run As Administrator
mkdir -p ~/.git-templates/hooks
git config --global init.templateDir '~/.git-templates'


Ajouter un modèle du message pour le commit par défaut :

créer et configurer le message par défaut

touch ~/.git-templates/gitmessage.txt
echo -e "&ref#XYZ : [msg]\n" > ~/.git-templates/gitmessage.txt
git config --global commit.template ~/.git-templates/gitmessage.txt
Dans le cadre d'un Git-Enforced-Policy côté client, pour pouvoir bloquer les commits sans message par exemple, il faut rajouter un script d'accroche (Hook) avec le nom spécifique "commit-msg". Pour ce faire il faut :

créer le fichier d'accroches avec droits d’exécution : 

touch ~/.git-templates/hooks/commit-msg
chmod a+x ~/.git-templates/hooks/commit-msg
éditer le fichier avec VIM (OU NOTEPAD SI WINDOWS), en rajoutant  le script suivant : 

vim  ~/.git-templates/hooks/commit-msg
#!/usr/bin/env bash
 
#-------- check is DEV branch (TO REACTIVATE WHEN READY) ---------------
# set this to your active development branch
#develop_branch="develop"
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


Si vous avez déjà un projet Git cloné ou initialisé sur votre workspace, pour que ce projet prenne en compte le template de message de commit précédemment défini ainsi que la vérification du respect de la syntaxe des messages de commit, vous devez lancer la commande suivante :   

$ git init
Reinitialized existing Git repository in C:/Users/<MON_USER>/DEV/<NOM_PRJET>/.git/
Finalement, si vous essayez de faire un commit sur votre branche locale, Git affichera le message suivant :

$ git ci -m ""
Aborting commit. Your commit message is missing either a Redmine Issue ('&REF#1111') or 'Merge'

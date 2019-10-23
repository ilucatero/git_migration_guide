# Git Migration Guide

* Basé sur le tutoriel de migration d'attlasian ici https://www.atlassian.com/git/tutorials/migrating-prepare


### Préparer la migration


Via Git Bash, télécharger l'outil de migration svn  to git : https://bitbucket.org/atlassian/svn-migration-scripts/downloads/svn-migration-scripts.jar
```bash
mkdir -p ~/DEV/svn_git_migration
cd ~/DEV/svn_git_migration
curl -s -H "Accept: application/zip" https://bitbucket.org/atlassian/svn-migration-scripts/downloads/svn-migration-scripts.jar  -o svn-migration-scripts.jar
```

Vérifier que tous les outils git & svn sont pressentes : 
```bash
java -jar svn-migration-scripts.jar verify
```

Importer les utilisateurs SVN en format git
```bash
java -jar svn-migration-scripts.jar authors http://svn.mycompany.com/EFM > authors_REPO1.txt
et puis éditer le fichier avec une éditeur de texte et remplacer tous les entrés avec '@xxx.com' par '@mycompany.com'.
```


### Recouper le projet SVN


Récupération du code du SVN pour stockage sous la forme d'un dépôt GIT local avec liaison correct des noms de développeurs & Accès au dépôt GIT local créé

Si le le layout de svn n'est pas standard (il n'a pas de sous répertoires tels que /trunk, /tags, /branches), utiliser une commande similaire à 
```bash
#git svn clone --authors-file=authors.txt --no-metadata <SVN_REPO>/<PROJECT_NAME> <OUTPUT_GIT_REPO_NAME> -T <DIR_TRUNK> -t <DIR_TAGS> -b <DIR_BRANCHES>
git svn clone --authors-file=authors_REPO1.txt --no-metadata http://svn.mycompany.com/REPOS/REPO1/test-mock/ -T .
#git svn clone --authors-file=authors.txt --no-metadata -s <SVN_REPO>/<PROJECT_NAME> <OUTPUT_GIT_REPO_NAME>
git svn clone --authors-file=authors_REPO1.txt --no-metadata -s stt-mock http://svn.mycompany.com/REPO1/test-mock/
 

cd test-mock
```

### Convertir Branches & Tags SVN en format Git


Création des vrais branches & tags GIT :

Atttention

/!\ Ce conversion est définitive sur le projet en local, le "backuper"  pour éviter de refaire l’opération svn-à-git qui est suivant très lente !
```bash
cp -r test-mock test-mock.bck
# Follow this exact order
git for-each-ref refs/remotes/origin/tags | cut -d / -f 5- | grep -v @ | while read tagname; do git tag "$tagname" "origin/tags/$tagname"; git branch -r -d "origin/tags/$tagname"; done
git for-each-ref refs/remotes/origin | cut -d / -f 4- | grep -v @ | while read branchname; do git branch "$branchname" "refs/remotes/origin/$branchname"; git branch -r -d "origin/$branchname"; done
```

Suppression de la branche trunk qui fait doublon avec master
```bash
git branch -d trunk
```

Suppression des branches & tags  résiduels
```bash
git for-each-ref refs/remotes/origin | cut -d / -f 4- | grep '@' | while read branchname; do git branch -r -d "origin/$branchname"; done
Créer et lier le projet au dépôt distant
```

Sur GitLab, créer le projet en amont  dans le groupe souhaité. Dans l'exemple suivant on le crée dans le groupe "test-mock".


Apres l'avoir crée une liste de commandes sera affiché, récupère une ligne comme la suivant et l’exécuter sur ton Git Bash pour le lier au dépôt sur Gitlab  remote : dans ce cas les répertories (Groups) /rd/repo1/test-mock/ doivent exister.
```bash
git remote add origin git@gitlab.mycompany.com/REPOS/REPO1/test-mock.git
```

Vérification que les branches et tags ont été créés correctement
```bash
git branch -a
git tag -l
```

Vérification que le HEAD pointe vers une seul branche
```bash
git log -5
# Doit afficher quelque chose comme : 
#         * 5ffcb7f - (19 hours ago) & move to trunk - ... (HEAD -> master)
```

Stockage du code migré sur le dépôt GIT distant
```bash
git push -u origin --all
git push -u origin --tags
Étapes post-migration du code
```

Créer le fichier .gitignore (version minimale ci-dessous) à la racine du projet avec la commande suivant
```bash
echo -e ".project\n.classpath\n.checkstyle\n.settings\n.idea\ntarget\n" >> .gitignore
```
et le rajouter / versioner
```bash
git add .gitignore -f
git ci -m "& add .gitignore"
git push
```

### Créer les clés Jenkins sur git

Pour ce faire, il est nécessaire de suivre la procédure détaille sur Configurer un job sur jenkins avec des sources sur gitlab

Ajouter une clé sur le projet gitlab dans les settings du projet aller vers l'item repository, par exemple ici : https://gitlab.mycompany.com/REPOS/REPO1/test-mock/settings/repository
étendre l’élément Deploy keys et activer les clés et puis valider que le projet est  correctement construit depuis jenkins


Mettre à jour le champ SCM du pom.xml du projet : 
```xml
<scm>
    <connection>scm:git:https://gitlab.mycompany.com/REPOS/REPO1/test-mock.git</connection>
</scm>
```
et pour le valider
```bash
mvn scm:checkout
```

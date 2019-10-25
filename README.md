Before running any migration from SVN to GIT, follow the [git installation guide](GIT_INSTALLATION_GUIDE.md).

# Git Migration Guide

Based on attlasian's guide : https://www.atlassian.com/git/tutorials/migrating-prepare


### Prepare the migration

* Using Git Bash <img align="center" width="50" height="50" src="https://msysgit.github.io/img/gwindows_logo.png">, download the below util for svn to git migration : https://bitbucket.org/atlassian/svn-migration-scripts/downloads/svn-migration-scripts.jar
   ```bash
   mkdir -p ~/DEV/svn_git_migration
   cd ~/DEV/svn_git_migration
   curl -s -H "Accept: application/zip" https://bitbucket.org/atlassian/svn-migration-scripts/downloads/svn-migration-scripts.jar -o        svn-migration-scripts.jar
   ```

* Verify that all git and svn libs are present: 
   ```bash
   java -jar svn-migration-scripts.jar verify
   ```

* Import all SVN users in git  format:
   ```bash
   java -jar svn-migration-scripts.jar authors http://svn.mycompany.com/REPOS/REPO1 > authors_REPO1.txt
   ```
   and then replace in file all entries from '@xxx.com' to '@mycompany.com'
   ```bash
   sed -i 's/@xxx.com/@mycompany.com/g' authors_REPO1.txt
   ```

### Get the SVN projects


* Retrieving the SVN code for storage as a local GIT repository with proper linking of developer names & local GIT repository access created.

   If the svn layout is not standard (it does not have subdirectories such as /trunk, /tags, /branches), use a command similar to 
```bash
#git svn clone --authors-file=authors.txt --no-metadata <SVN_REPO>/<PROJECT_NAME> <OUTPUT_GIT_REPO_NAME> -T <DIR_TRUNK> -t <DIR_TAGS> -b <DIR_BRANCHES>
git svn clone --authors-file=authors_REPO1.txt --no-metadata http://svn.mycompany.com/REPOS/REPO1/test-mock/ -T .
#git svn clone --authors-file=authors.txt --no-metadata -s <SVN_REPO>/<PROJECT_NAME> <OUTPUT_GIT_REPO_NAME>
git svn clone --authors-file=authors_REPO1.txt --no-metadata -s stt-mock http://svn.mycompany.com/REPO1/test-mock/
 
cd test-mock
```

### Convert Branches & Tags SVN to Git format

* Creating real branches & GIT tags:

   > **/!\ Warning: This conversion is definitive on the local project, backup it avoid redoing the svn-to-git operation which is very slow !**
   ```bash
   cp -r test-mock test-mock.bck
   # Follow this exact order
   git for-each-ref refs/remotes/origin/tags | cut -d / -f 5- | grep -v @ | while read tagname; do git tag "$tagname"    "origin/tags/$tagname"; git branch -r -d "origin/tags/$tagname"; done
   git for-each-ref refs/remotes/origin | cut -d / -f 4- | grep -v @ | while read branchname; do git branch "$branchname" "refs/remotes/origin/$branchname"; git branch -r -d "origin/$branchname"; done
   ```

* Deleting the /trunk branch that duplicates with master
   ```bash
   git branch -d trunk
   ```

* Remove residual branches & tags
   ```bash
   git for-each-ref refs/remotes/origin | cut -d / -f 4- | grep '@' | while read branchname; do git branch -r -d "origin/$branchname"; done
   ```

### Create and bind the project to the remote repository

* On GitLab, create first the project in the desired group. In the following example it is created in the "test-mock" group.

   After creating it, a list of commands will be displayed. Get a line like the following one and run it on your Git Bash to bind it to the repository on Gitlab remote: in this case the directories (Groups) /repo1/test-mock/ must exist.
   ```bash
   git remote add origin git@gitlab.mycompany.com/REPOS/REPO1/test-mock.git
   ```

* Verifying that branches and tags have been created correctly
   ```bash
   git branch -a
   git tag -l
   ```

* Verification that the HEAD points to a single branch
   ```bash
   git log -5
      # Must display something like:
      #         * 5ffcb7f - (19 hours ago) & move to trunk - ... (HEAD -> master)
   ```

* Storing migrated code on remote GIT repository
   ```bash
   git push -u origin --all
   git push -u origin --tags
   ```

### Post-migration steps of the code

* Create the .gitignore file (minimum version below) at the root of the project with the following command
   ```bash
   echo -e ".project\n.classpath\n.checkstyle\n.settings\n.idea\ntarget\n" >> .gitignore
   ```
   and add it
   ```bash
   git add .gitignore -f
   git ci -m "& add .gitignore"
   git push
   ```

* Create Keys Jenkins on git

   To do this, it is necessary to follow the detailed procedure on Configuring a Job on jenkins with sources on gitlab

   Add a key on the project gitlab in the project settings go to the item repository, for example here:
   > https://gitlab.mycompany.com/REPOS/REPO1/test-mock/settings/repository
   extend the Deploy Keys element and activate the keys and then validate that the project is correctly built since Jenkins


* Update the SCM field of the project pom.xml:
   ```xml
   <scm>
       <connection>scm:git:https://gitlab.mycompany.com/REPOS/REPO1/test-mock.git</connection>
   </scm>
   ```
   and to validate it
   ```bash
   mvn scm:checkout
   ```


### Synchronize SVN repositories with git

If you already got the svn repository, but in middle of the migration someone commit something, do next to update it: 
   ```batch
   # with one command
   git svn rebase
   
   # or withoud applying changes
   git svn fetch ; 
   # then applying them
   git svn rebase -l # or git svn rebase --local
   ```

<font color=#FF0000>2022-11-18</font>

# Git operation learning

#### environment

Description:	Debian GNU/Linux 11 (bullseye)
Release:	11
Codename:	bullseye

## Git basic command

1. Set user signature

   ```shell
   git config --global user.name YaoWang
   git config --global user.email yaowang02@foxmail.com
   ```

   The result can be check in `~/.gitconfig`.

2. Create an empty Git repository or  reinitialize an existing one

   ```shell
   git init
   ```

   After entering this command,  .git directory will be created in the current directory.

   ```
   root@server1[01:36:44]:~/vscode/git-learning$ ls -al
   total 12
   drwxr-xr-x 3 root root 4096 Nov 18 01:36 .
   drwxr-xr-x 7 root root 4096 Nov 18 01:35 ..
   drwxr-xr-x 7 root root 4096 Nov 18 01:36 .git
   ```

3. Show the working tree status

   ``` shell
   git status
   ```

   If the current directory is just created, no file exists except `.git`

   ``` shell
   root@server1[01:38:22]:~/vscode/git-learning$ git status
   On branch master
   
   No commits yet
   
   nothing to commit (create/copy files and use "git add" to track)
   ```

   If create a new file in the working directory, you will see:

   ```
   root@server1[02:18:20]:~/vscode/git-learning$ vim first_file.txt
   root@server1[02:19:11]:~/vscode/git-learning$ git status
   On branch master
   
   No commits yet
   
   Untracked files:
     (use "git add <file>..." to include in what will be committed)
   	first_file.txt
   
   nothing added to commit but untracked files present (use "git add" to track)
   ```

4. Add file contents to the index which is in `.git`

   ```shell
   git add first_file.txt
   ```

   After that the status changed:

   ```shell
   root@server1[02:25:14]:~/vscode/git-learning$ git status
   On branch master
   
   No commits yet
   
   Changes to be committed:
     (use "git rm --cached <file>..." to unstage)
   	new file:   first_file.txt
   ```

   which means file contents has been tracked.

5. Untrack a file contents from the index

   ``` shell
   git rm --cached first_file.txt
   ```

   Now the file become untracked again

6. Record changes to the repository

   ``` shell
   git commit -m "first commit" first_file.txt
   ```

   use `-m` to add a description on this commit.

   Now you will see:

   ``` shell
   root@server1[02:37:31]:~/vscode/git-learning$ git commit -m "first commit" first_file.txt 
   [master (root-commit) 33a7768] first commit		# 33a7768 is Version No
    1 file changed, 8 insertions(+)
    create mode 100644 first_file.txt
   root@server1[02:40:15]:~/vscode/git-learning$ git status
   On branch master
   nothing to commit, working tree clean 
   ```

7. View the log

   ``` shell
   root@server1[02:44:39]:~/vscode/git-learning$ git reflog
   33a7768 (HEAD -> master) HEAD@{0}: commit (initial): first commit
   root@server1[02:44:57]:~/vscode/git-learning$ git log
   commit 33a7768d9f7ff6429f34abba1631a25f4ca02563 (HEAD -> master)
   Author: YaoWang <yaowang02@foxmail.com>
   Date:   Fri Nov 18 02:37:49 2022 -0500
   
       first commit
   ```

8. Modify file contents and commit

   ```shell
   root@server1[02:45:00]:~/vscode/git-learning$ vim first_file.txt 
   root@server1[02:48:51]:~/vscode/git-learning$ git status
   On branch master
   Changes not staged for commit:
     (use "git add <file>..." to update what will be committed)
     (use "git restore <file>..." to discard changes in working directory)
   	modified:   first_file.txt
   
   no changes added to commit (use "git add" and/or "git commit -a")
   root@server1[02:49:06]:~/vscode/git-learning$ git add first_file.txt 
   root@server1[02:49:11]:~/vscode/git-learning$ git status
   On branch master
   Changes to be committed:
     (use "git restore --staged <file>..." to unstage)
   	modified:   first_file.txt
   
   root@server1[02:49:14]:~/vscode/git-learning$ git commit -m "second commit" first_file.txt 
   [master ed928a3] second commit
    1 file changed, 1 insertion(+), 1 deletion(-)
   root@server1[02:49:33]:~/vscode/git-learning$ git reflog
   ed928a3 (HEAD -> master) HEAD@{0}: commit: second commit
   33a7768 HEAD@{1}: commit (initial): first commit
   ```

9. Return to a any existing version

   ``` shell
   root@server1[02:58:05]:~/vscode/git-learning$ git reset --hard 33a7768
   HEAD is now at 33a7768 first commit
   root@server1[02:59:54]:~/vscode/git-learning$ git reflog
   33a7768 (HEAD -> master) HEAD@{0}: reset: moving to 33a7768
   ed928a3 HEAD@{1}: commit: second commit
   33a7768 (HEAD -> master) HEAD@{2}: commit (initial): first commit
   ```

   

## Branch operation

1. view branches

   ``` shell
   root@server1[03:00:00]:~/vscode/git-learning$ git branch -v
   * master 33a7768 first commit
   ```

2. create a new branch

   ```shell
   root@server1[03:34:31]:~/vscode/git-learning$ git branch hot-fix
   root@server1[03:35:55]:~/vscode/git-learning$ git branch -v
     hot-fix 33a7768 first commit
   * master  33a7768 first commit
   ```

3. change current branch

   ``` shell
   root@server1[03:35:57]:~/vscode/git-learning$ git checkout hot-fix
   Switched to branch 'hot-fix'
   root@server1[03:37:58]:~/vscode/git-learning$ git branch -v
   * hot-fix 33a7768 first commit
     master  33a7768 first commit
   ```

   Hint: If create a new branch like what I do, the hot-fix branch and the master branch are share the latest commit data. Once you do a commit  in a branch, the data will be given to another branch and the current branch own the commit contents. Log will record all these.

4. merge branches

   ``` shell
   root@server1[05:51:36]:~/vscode/git-learning$ git checkout master 
   Switched to branch 'master'
   root@server1[05:55:17]:~/vscode/git-learning$ git merge hot-fix 
   Updating 83ae65f..4eca2b3
   Fast-forward
    first_file.txt | 1 +
    1 file changed, 1 insertion(+)
   ```

   If both branch are modified, a conflict will occur. Now I show this case. 

   ```shell
   root@server1[06:02:48]:~/vscode/git-learning$ git merge hot-fix 
   Auto-merging first_file.txt
   CONFLICT (content): Merge conflict in first_file.txt
   Automatic merge failed; fix conflicts and then commit the result.
   root@server1[06:04:17]:~/vscode/git-learning$ cat first_file.txt 
   <<<<<<< HEAD
   111   master modified
   111
   =======
   111
   111   hot-fix modified
   >>>>>>> hot-fix
   111
   222
   222
   222
   333
   ```

   you can see that the lines between `<<<<<<< HEAD` and `=======`is the file contents in current branch and lines between  `=======` and `>>>>>>> hot-fix` is the file contents another branch. You should modify the file by yourself. Below is how to do after modifing the conflict file.

   ```shell
   root@server1[06:04:30]:~/vscode/git-learning$ git add -A
   root@server1[06:12:40]:~/vscode/git-learning$ git commit -m "merge file commit" first_file.txt 
   fatal: cannot do a partial commit during a merge.	# Cannot specify file in merge status
   root@server1[06:13:01]:~/vscode/git-learning$ git commit -m "merge file commit"
   [master 0223036] merge file commit
   ```

   ## Github - remote repository operation

   1. create a remote ropository and add a alias

      ```shell
      root@server1[06:38:10]:~/vscode/git-learning$ git remote add git-learning https://github.com/YaoWang02/git-learning.git
      root@server1[06:38:28]:~/vscode/git-learning$ git remote -v
      git-learning	https://github.com/YaoWang02/git-learning.git (fetch)
      git-learning	https://github.com/YaoWang02/git-learning.git (push)
      ```

   2. push local repository to remote one

      ```shell
      root@server1[06:52:52]:~/vscode/git-learning$ git push git-learning master
      Username for 'https://github.com': YaoWang02
      Password for 'https://YaoWang02@github.com': 
      Enumerating objects: 21, done.
      Counting objects: 100% (21/21), done.
      Delta compression using up to 2 threads
      Compressing objects: 100% (10/10), done.
      Writing objects: 100% (21/21), 1.66 KiB | 565.00 KiB/s, done.
      Total 21 (delta 1), reused 0 (delta 0), pack-reused 0
      remote: Resolving deltas: 100% (1/1), done.
      To https://github.com/YaoWang02/git-learning.git
       * [new branch]      master -> master
      ```

      You should use token in password field, refer to https://blog.csdn.net/qq_41646249/article/details/119777084

   3. pull remote repository to local

      ``` shell
      root@server1[07:34:13]:~/vscode/git-learning$ git pull git-learning master
      remote: Enumerating objects: 5, done.
      remote: Counting objects: 100% (5/5), done.
      remote: Compressing objects: 100% (2/2), done.
      remote: Total 3 (delta 1), reused 0 (delta 0), pack-reused 0
      Unpacking objects: 100% (3/3), 664 bytes | 55.00 KiB/s, done.
      From https://github.com/YaoWang02/git-learning
       * branch            master     -> FETCH_HEAD
         0223036..2cacb1c  master     -> git-learning/master
      Updating 0223036..2cacb1c
      Fast-forward
       first_file.txt | 1 +
       1 file changed, 1 insertion(+)
      
      ```

   4.  git-clone - Clone a repository into a new directory

      ```shell
      git clone [repository address]
      ```

      1-pull codes, 2-initial local repository, 3-create alias

   5. use ssh to access github

      First, create ssh key
   
      ```
      ssh-keygen -t rsa -C "yaowang02@foxmail.com"
      ```
   
      Second, add the `id_rsa.pub` to github.
   
      Third, test
   
      ```shell
      root@server1[08:49:14]:~/vscode/git-learning$ git pull git@github.com:YaoWang02/git-learning.git master
      The authenticity of host 'github.com (192.30.255.112)' can't be established.
      ECDSA key fingerprint is SHA256:p2QAMXNIC1TJYWeIOttrVc98/R1BUFWu3/LiyKgUfQM.
      Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
      Warning: Permanently added 'github.com,192.30.255.112' (ECDSA) to the list of known hosts.
      From github.com:YaoWang02/git-learning
       * branch            master     -> FETCH_HEAD
      Already up to date.
      ```
   
      

#### reference:

https://www.bilibili.com/video/BV1vy4y1s7k6/
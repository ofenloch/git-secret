# Using git-secret to safely store confidential data in your git repository

## Common remarks

[git-secret](https://git-secret.io/) uses [gpg](https://gnupg.org/) to encrypt confidential data in [git](https://git-scm.com/) repositories.

## Preparations

Before you can use git-secret you must create a RSA key pair. Check out README-gpg.md hot to do that.

Once you have a RSA key pair you can initialize a new and empty or an existing git repository with `git secret init`

```bash
ofenloch@teben:~/github-projects/git-secret$ git secret init
'/home/ofenloch/github-projects/git-secret/.gitsecret/' created.
cleaning up...
ofenloch@teben:~/github-projects/git-secret$ 
```

The .gitsecret/ folder will be created. **All the contents of the .gitsecret/ folder should be checked in, except the *random_seed file*.** 
In other words, of all the files in .gitsecret/, only the random_seed file should be mentioned in your .gitignore file. 
By default, git secret init will add the file .gitsecret/keys/random_seed to your .gitignore file.

```bash
ofenloch@teben:~/github-projects/git-secret$ git status
On branch main
Your branch is up to date with 'origin/main'.

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        .gitignore
        .gitsecret/

nothing added to commit but untracked files present (use "git add" to track)
ofenloch@teben:~/github-projects/git-secret$ git add .
ofenloch@teben:~/github-projects/git-secret$ git commit -a -m"changes after 'git secret init'"                             
[main 1905e94] changes after 'git secret init'
 3 files changed, 1 insertion(+)
 create mode 100644 .gitignore
 create mode 100644 .gitsecret/keys/mapping.cfg
 create mode 100644 .gitsecret/paths/mapping.cfg
ofenloch@teben:~/github-projects/git-secret$
```

Now we add the first user (most probably this will be us) to the git-secret repo keyring by running `git secret tell your@gpg.email`.

```bash
ofenloch@teben:~/github-projects/git-secret$ git secret tell 57812959+ofenloch@users.noreply.github.com
gpg: keybox '/home/ofenloch/github-projects/git-secret/.gitsecret/keys/pubring.kbx' created
gpg: /home/ofenloch/github-projects/git-secret/.gitsecret/keys/trustdb.gpg: trustdb created
done. 57812959+ofenloch@users.noreply.github.com added as someone who know(s) the secret.
cleaning up...
ofenloch@teben:~/github-projects/git-secret$ 
ofenloch@teben:~/github-projects/git-secret$ git add .
ofenloch@teben:~/github-projects/git-secret$ git commit -a -m"changes after 'git secret tell 57812959+ofenloch@users.noreply.github.com'"
[main a5a8a04] changes after 'git secret tell 57812959+ofenloch@users.noreply.github.com'
 3 files changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 .gitsecret/keys/pubring.kbx
 create mode 100644 .gitsecret/keys/pubring.kbx~
 create mode 100644 .gitsecret/keys/trustdb.gpg
ofenloch@teben:~/github-projects/git-secret$ 
```

## Adding confidential files to your repository

Now we are ready to add secret data with `git secret add`

```bash
ofenloch@teben:~/github-projects/git-secret$ echo "Nobody uses the top secret pasword \"IS)h6%C(d71yYC$#:z8\vZ0\;H#u_O:~\"." > top-secret-password.tct
ofenloch@teben:~/github-projects/git-secret$ git secret add top-secret-password.tct
these files are not ignored: top-secret-password.tct ; abort.
ofenloch@teben:~/github-projects/git-secret$ echo "top-secret-password.tct" >> .gitignore
ofenloch@teben:~/github-projects/git-secret$ git secret add top-secret-password.tct
1 item(s) added.
ofenloch@teben:~/github-projects/git-secret$ git status
On branch main
Your branch is up to date with 'origin/main'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   .gitignore
        modified:   .gitsecret/paths/mapping.cfg

no changes added to commit (use "git add" and/or "git commit -a")
ofenloch@teben:~/github-projects/git-secret$ git commit -a -m"changes after 'git secret add top-secret-password.tct'"
[main 3debe4c] changes after 'git secret add top-secret-password.tct'
 2 files changed, 2 insertions(+)
ofenloch@teben:~/github-projects/git-secret$
```

Make sure these files are ignored by mentions in .gitignore, otherwise git-secret won’t allow you to add them, as 
these files could be stored unencrypted. In the default configuration, git-secret add will automatically add the 
unencrypted versions of the files to .gitignore for you.

My version of git-secret does not automatically add the git-add-added files to .gitignore, so I have to this by 
manually. This is the meaning of `echo "top-secret-password.tct" >> .gitignore`.

When we are done adding secret files, we hide them with `git secret hide`:

```bash
ofenloch@teben:~/github-projects/git-secret$ git secret hide
done. all 1 files are hidden.
ofenloch@teben:~/github-projects/git-secret$
```
The files will be encrypted with the public keys described by the 
`git secret tell` command. We see that there is a new file named 
*top-secret-password.tct.secret* which is the encrypted version of 
the (un-encrypted) original file *top-secret-password.tct*

After using git `secret hide` to encrypt 
your data, it is safe to commit your changes. 

We use `git add` to add the encrypted file to the repository and 
commit our changes:

```bash
ofenloch@teben:~/github-projects/git-secret$ git add top-secret-password.tct.secret
ofenloch@teben:~/github-projects/git-secret$ git commit -a -m"changes after 'git secret hide'"
[main a8b0353] changes after 'git secret hide'
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 top-secret-password.tct.secret
ofenloch@teben:~/github-projects/git-secret$
```

**NOTE:** It’s recommended to add the `git secret hide` command to your pre-commit hook, so you won’t miss any changes.

Now we push our repo to the server:

```bash
ofenloch@teben:~/github-projects/git-secret$ git push --all
Enumerating objects: 14, done.
Counting objects: 100% (14/14), done.
Delta compression using up to 8 threads
Compressing objects: 100% (7/7), done.
Writing objects: 100% (9/9), 1.49 KiB | 1.49 MiB/s, done.
Total 9 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), done.
To https://github.com/ofenloch/git-secret.git
   a5a8a04..a8b0353  main -> main
ofenloch@teben:~/github-projects/git-secret$
```





## Complete log

```bash
ofenloch@teben:~/github-projects/git-secret$ git secret init
'/home/ofenloch/github-projects/git-secret/.gitsecret/' created.
cleaning up...
 git tag after-git-secret-init 
ofenloch@teben:~/github-projects/git-secret$ 
ofenloch@teben:~/github-projects/git-secret$ 
ofenloch@teben:~/github-projects/git-secret$ 
ofenloch@teben:~/github-projects/git-secret$ 
ofenloch@teben:~/github-projects/git-secret$ 
ofenloch@teben:~/github-projects/git-secret$ git secret tell 57812959+ofenloch@users.noreply.github.com
gpg: keybox '/home/ofenloch/github-projects/git-secret/.gitsecret/keys/pubring.kbx' created
gpg: /home/ofenloch/github-projects/git-secret/.gitsecret/keys/trustdb.gpg: trustdb created
done. 57812959+ofenloch@users.noreply.github.com added as someone who know(s) the secret.
cleaning up...
ofenloch@teben:~/github-projects/git-secret$ git status
On branch main
Your branch is ahead of 'origin/main' by 1 commit.
  (use "git push" to publish your local commits)

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        .gitsecret/keys/pubring.kbx
        .gitsecret/keys/pubring.kbx~
        .gitsecret/keys/trustdb.gpg

nothing added to commit but untracked files present (use "git add" to track)
ofenloch@teben:~/github-projects/git-secret$ git add .
ofenloch@teben:~/github-projects/git-secret$ git commit -a -m"changes after 'git secret tell 57812959+ofenloch@users.noreply.github.com'"
[main a5a8a04] changes after 'git secret tell 57812959+ofenloch@users.noreply.github.com'
 3 files changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 .gitsecret/keys/pubring.kbx
 create mode 100644 .gitsecret/keys/pubring.kbx~
 create mode 100644 .gitsecret/keys/trustdb.gpg
ofenloch@teben:~/github-projects/git-secret$ git tag after-git-secret-tell
ofenloch@teben:~/github-projects/git-secret$  git push --all
Everything up-to-date
ofenloch@teben:~/github-projects/git-secret$  git push --tags
Total 0 (delta 0), reused 0 (delta 0)
To https://github.com/ofenloch/git-secret.git
 * [new tag]         after-git-secret-init -> after-git-secret-init
 * [new tag]         after-git-secret-tell -> after-git-secret-tell
ofenloch@teben:~/github-projects/git-secret$ 
ofenloch@teben:~/github-projects/git-secret$ 
ofenloch@teben:~/github-projects/git-secret$ 
ofenloch@teben:~/github-projects/git-secret$ echo "Nobody uses the top secret pasword \"IS)h6%C(d71yYC$#:z8\vZ0\;H#u_O:~\"." > top-secret-password.tct
ofenloch@teben:~/github-projects/git-secret$ git secret add top-secret-password.tct
these files are not ignored: top-secret-password.tct ; abort.
ofenloch@teben:~/github-projects/git-secret$ git secret add top-secret-password.tct
1 item(s) added.
ofenloch@teben:~/github-projects/git-secret$ git status
On branch main
Your branch is up to date with 'origin/main'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   .gitignore
        modified:   .gitsecret/paths/mapping.cfg

no changes added to commit (use "git add" and/or "git commit -a")
ofenloch@teben:~/github-projects/git-secret$ git commit -a -m"changes after 'git secret add top-secret-password.tct'"
[main 3debe4c] changes after 'git secret add top-secret-password.tct'
 2 files changed, 2 insertions(+)
ofenloch@teben:~/github-projects/git-secret$ git tag after-git-secret-add
ofenloch@teben:~/github-projects/git-secret$ 
ofenloch@teben:~/github-projects/git-secret$ 
ofenloch@teben:~/github-projects/git-secret$ git secret hide
done. all 1 files are hidden.
ofenloch@teben:~/github-projects/git-secret$ git status
On branch main
Your branch is ahead of 'origin/main' by 1 commit.
  (use "git push" to publish your local commits)

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        top-secret-password.tct.secret

nothing added to commit but untracked files present (use "git add" to track)
ofenloch@teben:~/github-projects/git-secret$ git add .
ofenloch@teben:~/github-projects/git-secret$ git commit -a -m"changes after 'git secret hide'"
[main a8b0353] changes after 'git secret hide'
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 top-secret-password.tct.secret
ofenloch@teben:~/github-projects/git-secret$ git tag after-git-secret-hide
ofenloch@teben:~/github-projects/git-secret$ git status
On branch main
Your branch is ahead of 'origin/main' by 2 commits.
  (use "git push" to publish your local commits)

nothing to commit, working tree clean
ofenloch@teben:~/github-projects/git-secret$ git push --all
Enumerating objects: 14, done.
Counting objects: 100% (14/14), done.
Delta compression using up to 8 threads
Compressing objects: 100% (7/7), done.
Writing objects: 100% (9/9), 1.49 KiB | 1.49 MiB/s, done.
Total 9 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), done.
To https://github.com/ofenloch/git-secret.git
   a5a8a04..a8b0353  main -> main
ofenloch@teben:~/github-projects/git-secret$ git push --tags
Total 0 (delta 0), reused 0 (delta 0)
To https://github.com/ofenloch/git-secret.git
 * [new tag]         after-git-secret-add -> after-git-secret-add
 * [new tag]         after-git-secret-hide -> after-git-secret-hide
ofenloch@teben:~/github-projects/git-secret$ 
```

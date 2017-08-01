### Git and GitHub - Initial config and setup

A basic outline for getting started with Git and GitHub.

#### Contents
  * basic install
  * basic setup
  * basic usage
  * useful extras

#### Basic install
Install Git from

  * https://git-scm.com

Then check version number,

```
git --version // e.g. current version 2.13.1 as of 07/17
```

Useful Git tutorial,

  * https://www.atlassian.com/git/tutorials

and more detailed book,

  * https://git-scm.com/book/en/v2

#### Basic setup
Initialise local directory as Git repository, and check for hidden `.git` repository

```
git init
ls -al
```

Check current status of repository,

```
git status
```

Create a local `.gitignore` file, and specify files &c. to ignore per push.

```
touch .gitignore
```

and then add directories and files to ignore per commit and push with Git. e.g.

```
node_modules/
*.log
```

#### Basic usage
Then, add any files that need to be included in the next commit, e.g.

```
git add server.js // add a single specified file from current directory
git add . // add everything for current directory
```

We can then update Git, and set a commit point for the repository,

```
git commit -m "initial project commit"
```

where the flag `-m` is for a custom commit message (usually written in the present tense...)

For GitHub synch and usage, we can setup some SSH keys locally and on GitHub,

  * GitHub guide to SSH keys = https://help.github.com/categories/ssh/

Start by checking for existing local keys,

```
ls -al ~/.ssh
```

A standard key pair is `id_rsa` and `id_rsa.pub` where the first key is private to the user's machine, and the second public key may be shared with remote services such as GitHub and Heroku.

If necessary, generate a new key pair,

```
ssh-keygen -t rsa -b 4096 -C 'your_email_address'
```

Then, start the local ssh agent and check it is running OK,

```
eval "$(ssh-agent -s)"
```

which returns the `pid` value for the running ssh agent.

Now, we can add the required ssh key,

```
ssh-add ~/.ssh/id_rsa
```

This key can then be used with remote services such as GitHub. Navigate to `SSH and GPG Keys` page in GitHub user account, and paste the contents of the `id_rsa.pub` file. OS X users can copy from terminal as follows,

```
pbcopy < ~/.ssh/id_rsa.pub
```

Then, test key authentication with GitHub,

```
ssh -T git@github.com
```

In the root of the local Git repository, we can now sync with a remote GitHub repository. e.g. we can add a remote repo,

```
git remote add origin https://github.com/your_account_name/repo_name.git
```

and then finally push some local data to a remote repository on GitHub,

```
git push -u origin master
```

#### Useful extras
We can also customise the git username and email address per repository, even if the account is different for the commit. e.g.

```
git config user.name "your_user_name"
git config user.email "your_email_address"
```

To then push with a different authenticated account, we simply set that account as an authenticated collaborator on GitHub for the chosen repository.

Each subsequent commit and push will then be signed by this specific repository username and email address. This is made easier if we've previously setup keys for the GitHub access.

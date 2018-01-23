### Node.js - Guide
#### section 8 - Project Management - Version Control with Git

A brief outline of integrating Git based version control into project management.

#### Contents
* intro
* setup Git version control
* Git usage and project integration
* setup GitHub and ssh keys
* push data to GitHub repository

#### intro
Version control with Git is a crucial part of development, in particular within collaborative environments.

We can also push incremental updates, managed by Git, to remote repositories and version control services such as GitHub and Bitbucket.

#### setup Git version control
Install Git on your local machine, which includes CLI tools for creating, maintaining, and updating Git for project version control. e.g. download installer from

  * https://git-scm.com

After install, check version number for Git from the CLI,

```
git --version // e.g. current version 2.13.1 as of 07/17
```

We can now initialise a project directory as a Git repository, e.g. in the CWD for the chosen project

```
git init
```

Then check the directory for the hidden `.git` repository directory,

```
ls -a
```

#### Git usage and project integration
Check the current status of a project repository using the following Git command,

```
git status
```

This command will print some output and repository information to the console, including tracked files, files ready to commit, and so on. We can use this output to check the current working status of a project's repository before updates, commits, and push requests to remote hosts and services.

For example, the output of untracked files and directory at the root of a project will help us determine what we want to track. The rest we can tell Git to ignore, which means they won't be tracked, commits, and pushed to a service &c.

If we want to add a single file or directory to be tracked, e.g.

```
git add server.js
```

and so on. However, we can create a file called `.gitignore` at the root of a repository to tell Git the files &c. to ignore,

```
/node_modules
request.log
```

Then, we can tell Git the files we want to commit,

```
git commit -m "initial project commit"
```

This will commit the current state of the files we added to the commit with Git.

#### setup GitHub and ssh keys
GitHub's own guide for generating and working with SSH key and their online service,

  * https://help.github.com/categories/ssh/

However, we can start the basic process by checking for any existing ssh keys on local system

```
ls -al ~/.ssh
```


this will output a list of available ssh keys. Standard key pairs include,

  * id_rsa and id_rsa.pub

The `id_rsa` key is the private key for the local machine, and it should not be shared. The second key, `id_rsa.pub` is the public half of the key pair, and can be shared with services such as GitHub.

If necessary, we can generate a new private key pair for ssh using the following command,

```
ssh-keygen -t rsa -b 4096 -C 'your_email_address'
```

Then, we can start the ssh agent, and check it is running OK,

```
eval "$(ssh-agent -s)"
```

This will output the `pid` for the current agent, e.g. `Agent pid 19439`. We can then add a required ssh key to the ssh agent using the following command,

```
ssh-add ~/.ssh/id_rsa
```

and update our GitHub account to enable ssh key usage. After login to a GitHub account, we can navigate to the settings page, and select the `SSH and GPG Keys` option in the left sidebar menu.

Then, we need to specify a name for the new ssh key, and paste this key into the text field for `Key`. On OS X, a simply way to do this is by using the following command,

```
pbcopy < ~/.ssh/id_rsa.pub
```

Then, we can test that the keys can authenticate with the remote service for GitHub,

```
ssh -T git@github.com
```

Follow any prompt instructions, accept `yes` to continue, and GitHub will return the following confirmation,

```
Hi your_username! You've successfully authenticated, but GitHub does not provide shell access.
```

This means the authentication setup with ssh key worked correctly for GitHub.

#### push data to GitHub repository
We can start by creating a new repository from our account homepage on GitHub. We can set a name for the new repository, a brief description, specify the license, and so on.

Once we've created this new repository, we can push our local repository to this new remote GitHub repository.

Then, we can add a remote repository to our local Git repository setup,

```
git remote add origin git@github.com:your_account_name/repo_name.git
```

This means we can link the local Git repository to a remote repository hosted on GitHub. We can then push and synchronise our local repository content with the online GitHub version, e.g.

```
git push -u origin master
```

This will push the local repository to the linked remote repository on GitHub.

We may also use a simpler command,

```
git push
```

to push data from the local repository to the current working repository, such as `master`, on GitHub.

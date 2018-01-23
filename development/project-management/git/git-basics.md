### Git - Intro and Basics

A basic outline and intro for getting started with Git.

#### Contents
  * Intro
  * Version control - what is version control?
  * Version control - setting up Git
  * Git GUIs
  * Command-line - navigating directory and files
  * Git basics - parts 1 - 7

#### Intro

As we start to develop increasingly detailed, complex, and collaborative software project, applications, and websites, we need a way to ensure that we keep track of changes, updates, contributions, and suggested modifications.

Naturally, we could try to achieve this boring and tedious task using different directories, notes, and messages from one team member to another, but at some point it will all come crashing down upon us.

This is why we need to use a *version control* tool such as **Git**.

In fact, as we continue to develop our web applications and sites, we'll often engage in what is commonly known as *exploratory coding*. This is a practice of researching, learning, and checking what does and does not work correctly to achieve a given requirement in the app. All developers and designers have tried this type of methodology, and it is something that will often also lead to changes and updates in our code and design.

#### Version control - what is version control?

In a basic manner, we have all attempted, at one time or another, to create and control versions of work, software etc by simply copying and replicating folders or documents. As we modify the copied document, for example, we are able to compare our updates with the previous copy and, if necessary, revert back to an earlier state should errors or loss arise. However, this is a pretty basic form of version control, and not something recommended for any development beyond a few short lines.

In essence, for software development version control allows us to maintain defined, labelled points in our code, thereby enabling us to easily refer back to them or revert to an earlier state if needed. For development, version control is also an important tool for collaborative work with other developers.

There have been a number of different, and popular, version control tools over the last few years. Subversion was particularly popular for many years, replacing strict CVS for many people. Now, however, Git seems to be the almost de-facto choice for web development, and it continues to grow in popularity since Linus Torvalds handed it over in July 2005. With the advent of GitHub and BitBucket, Git's rise to the top continues apace, at least for now.

For example, it has been estimated by [Forrester](http://blogs.forrester.com/application_development/2010/01/forrester-databyte-developer-scm-tool-adoption-and-use.html) that by 2010 Subversion held approximately 33.4% of the market for version control. By contrast, Git is believed to have only held approximately 2.7%, and Mercurial a paltry 0.7%. By 2013, Git usage was almost as high as Subversion, and it continues to grow in popularity.

One of the reasons for the uptick in Git usage is a general preference for distributed version control compared to centralised. This growth was also helped by [Atlassian's decision to switch](http://blog.bitbucket.org/2012/01/19/how-atlassian-migrated-from-svn-to-git/) from Subversion to Git in 2012.

So, we will now look at version control from the perspective of Git in general, and GitHub for online hosting.

##### image of version control usage from 2010 to 2013

<center>
![Version control usage - pt1](../media/images/version-control1.jpg)
<br />
Source - [RedMonk](http://redmonk.com/sogrady/2013/12/19/dvcs-and-git-2013/#ixzz2qyfVpSR9)
</center>

##### image of version control change in usage (2010 to 2013)

<center>
![Version control usage - pt2](../media/images/version-control2.jpg)
<br />
Source - [RedMonk](http://redmonk.com/sogrady/2013/12/19/dvcs-and-git-2013/#ixzz2qyfVpSR9)
</center>

#### Version control - setting up Git

Thankfully, Git has simple installers for OS X and Windows. It is also available for most package managers in Linux. For example, choose your platform's installer from the following site,

* [git](http://git-scm.com/downloads)

and simply follow the instructions to install.

Full instructions for various Linux distributions can also be found on this site,

* [git - Linux downloads](http://git-scm.com/download/linux)

Again, simply follow the brief install commands for your distribution's package manager. eg: for Debian/Ubuntu based APT distributions,

```
apt-get install git
```

#### Git GUIs

There are many GUIs available for working with Git,

* [Git GUIs](https://git-scm.com/download/gui/linux)

including specific desktop clients for GitHub

* [GitHub desktop clients](https://desktop.github.com/)

However, it is still beneficial, and often a lot quicker, to simply use the command-line to manage and maintain our code.

Let's start with a few simple, basic commands for a unix-style command-line. These initial commands allow us to navigate our files and directories, necessary for any project structure, create new directories, list directories and files, and so on. They will provide a useful segue for working with Git basics.

##### image of OS X terminal

The underlying file and directory structure remains the same, as you might imagine, regardless of whether you use a graphical browser or the command-line.

So, on OS X or Linux we need to use the terminal application. OS X's version is shown in the image.

For Unix based OSs, the principle of the underlying structure is the same, which means we can use the following commands to navigate.

#### Command-line - navigating directory and files

We can check the current directory

```
pwd
```

This command is short for *print working directory*. We can also check the contents of the current directory,

```
ls
```

This command effectively *lists* the content of the working directory. Another common command allows us to change directory,

```
cd
```

Once we've navigated to our project's working directory, for example, we might need to create a new directory for media files or plugins etc. We can use the following command,

```
mkdir
```

to make our new directory.

##### image - command-line examples

<center>
![OS X terminal 2](../media/images/osx-terminal2.png)
<br />
</center>

#### Git basics - part 1

Before we can do anything useful with Git, we need to configure our user/developer details.

###### username and email
We can set details for *username* and *user email*, and by adding a *global* flag we can also set these details for all work within our local instance of Git.

```
git config --global user.name "424dev"
```

This sets our preferred username for our local copy of Git. It means that when we issue a commit for some work, or make changes etc, they will be tagged with our username. This is particularly important, as you might imagine, when we are working in a group, collaborative project or if we push changes to a larger community.

We also need to set our preferred email address,

```
git config --global user.email "424dev@gmail.com"
```

We can also override these settings for a specific repository in Git by omitting the `--global` flag.

```
git config user.name "424dev-single"
```

and so on. To check we have set the correct username for the current repository we can use the following command,

```
git config user.name
```

So, if we've used the `--global` flag, *Git* is now setup with the desired username and password. We will not need to do this again unless an individual case arises.

#### Git basics - part 2
To start tracking our projects in *Git*, let's create a new working directory at the root of our home directory,

```
cd ~/
```

ensures that we are in our *home* directory. Then check the working directory to be sure,

```
pwd
```

and then make a new directory for our client-side development.

```
mkdir client-dev
```

##### image of creating a client-dev directory

<center>
![OS X terminal 3](../media/images/osx-terminal3.png)
<br />
</center>

#### Git basics - part 3
With a working development directory, we can now add version control using *Git*, and initialise a repository for our work.

After initialising our new repository,
```
git init
```
we can check to make sure the hidden *.git* directory has been created.
```
ls -a
```
We can show the hidden directory *.git*
```
ls .git
```
which shows us the contents of the newly created hidden directory.

##### image of initialising a new repository

<center>
![OS X terminal 4](../media/images/osx-terminal4.png)
<br />
</center>

#### Git basics - part 4

##### start using our repository
Using our preferred text editor, we will now create an initial `index.html` file in the `project1` working directory. This is the directory where we just initialised our first *Git* repository.

After saving this new file, we can check Git to ensure that it is correctly tracking our new respoitory,

```
git status
```

This will output the current status of the branch we are working on, in this case the default `master`, and the fact that we have some `untracked files`. These files will, at this point, include our new `index.html` file.

So, we can now `add` these untracked files to our current repository using the following command for a specific file

```
git add index.html
```

or

```
git add *
```

for all untracked files.

##### image - git status and add

<center>
![OS X terminal 5](../media/images/osx-terminal5.png)
<br />
</center>

#### Git basics - part 6

This commit has allowed us to save a defined point within our work, and one we can revert to if necessary. If we now check `git status` we'll see that there is nothing available to commit, and that our working directory is clean. In effect, *Git* has now set the files within our last commit as ready for tracking.

Each time we make changes to our repository files, we repeat this process to `add`, `commit`, and thereby freeze defined points within our project's development and code.

We can check these recent commits, and thereby view a brief record of our project work, using the command,

```
git log
```

This will output an identifier for each commit, the defined author, and the date as well. It will also output the commit message added by the user. Each of these commits is, effectively, a set of instructions for modifying the defined file. It acts in a similar manner to standard collation or *DIFF*. Add and delete content from one instance of a file to another, and you get the different revised versions of a document.

Therefore, Git does not store individual copies of a file, simply instructions for the revisions and version history.

#### Git basics - part 7

So far, we've seen how *Git* is able to record simple linear commits. This presumes that a file has one parent, and child commits detail this linear revision of the content.

However, a *Git* commit is also able to store multiple parents and children. For example, a commit history might include revisions to a single file, addition or deletion of new files, merging of files to different branches, further additions, and so on.

Therefore, for a given project, it is often useful to maintain a brief record of commits and their overall pattern relative to the repository itself. This can act, in many respects, in a similar manner to a simple flowchart. It can show the branches and commit history in a visual manner to help maintain our project.

##### a few useful *Git* commands...

| Git command | Expected Outcome |
|-------------|------------------|
| *git config user.name "..."* | sets username for current repo |
| *git config --global user.name "..."* | sets username for all repos (unless overridden per repo) |
| *git config user.email "..."* | sets user's email address for current repo |
| *git config --global user.email "..."* | sets user's email address for all repos (unless overridden per repo)
| *git init* | initialise a Git repository in the current working directory |
| *git status* | output current status of repo in current working directory |
| *git add "..."* | define specific file in current repo for next commit |
| *git add *\* | define all files in current repo for next commit |
| *git commit -m "..."* | commit defined files (set using `git add`) with message |
| *git log* | output commit history for current repo |

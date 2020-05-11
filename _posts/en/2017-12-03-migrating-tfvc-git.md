---
title: "Migrating our projects from TFVC to Git"
header:
  image: /assets/posts/en/migrating-tfvc-git/header.jpg
tags: [ English, Git, TFVC ]
categories: [ Git, DevOps ]
lang: en
ref: 7
permalink: /en/migrating-tfvc-git/
---

With the decline of code version control systems such as [**Team Foundation Version Control (TFVC)**](https://www.visualstudio.com/docs/tfvc/overview) and the rise of others such as [**Git**](https://git-scm.com/), we are facing a common and recurring problem in *TFVC* repositories:

How do we change from an existing *TFVC* repository to a *Git* repository, without affecting all the work already done, to all the code tracking, simply, and quickly, and most importantly, without affecting the team?

Faced with this problem, we find two solutions:

- Manually create a *Git* repository and pass each of these changes and information that we would like to keep.

- Use tools created by the community to, in a few steps, copy all the content from one repository to another and also get the advantages that *Git* bring to our project.

The obvious answer is the second option, and the reason for this document, using the [**git-tfs**](http://git-tfs.com/) tool to achieve it. Let's start.

## Prerequisites

You need to have installed [*.NET 4*](https://msdn.microsoft.com/en-us/library/w0x726c2(v=vs.110).aspx) or highet, and the 2010, 2012, 2013 or 2015 versions of [*Team Explorer*](https://msdn.microsoft.com/es-es/library/hh500420.aspx) (or [*Visual Studio*](https://www.visualstudio.com/es-es/visual-studio-homepage-vs.aspx)).

To use the *git-tfs* tool I recommend the use of [*Chocolatey*](https://chocolatey.org/).

**Chocolatey NuGet** is a **Machine Package Manager**, that is, a package manager for our computer, capable of installing tools in a simple and well-managed way using *Windows*. To install *Chocolatey*, open a command console **as administrator** and enter the following command:

```shell
C:\> @powershell -NoProfile -ExecutionPolicy Bypass -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
```

Restart the command console.

## Obtaining **git-tfs**

Open a command prompt and run:

```shell
choco install gittfs
```

Once installed, you have to make sure that the **git-tfs** folder is included in the `PATH` environment variable. You can include the folder temporarily using:

```shell
set PATH=%PATH%;%cd%\GitTfs\bin\Debug
```

## Using *git-tfs*

### Help

As usual, there is a help section in the tool itself, just use the following commands:

```shell
# All available commands
git tfs help

# Show a summary of use of one of the commands
git tfs help <command>
```

### Listing our projects and branches

Since more than one project can exist on our *TFS* server, we can use the `list-remote-branches` to list all available projects and their branches.

```shell
# If we use Visual Studio Team Services (Visual Studio Online)
git tfs list-remote-braches http://[ACCOUNT].visualstudio.com/tfs/DefaultCollection

# If we use Visual Studio On-Premise
git tfs list-remote-braches http://tfs:8080/tfs/DefaultCollection
```

> From this point on, we assume that *VSTS*  is being used so we will always use the address `http://[ACCOUNT].visualstudio.com/tfs/`, to use *VS On-Premise* just change the addresses seen below by `http://tfs:8080/tfs/`.

What will give an output by command console like the following:

```shell
$/project/trunk [*]
|
+- $/project/branch1
|
+- $/project/branch2
|
+- $/project/branch3
|  |
|  +- $/project/branche3-1
|
+- $/project/git_central_repo


$/other_project/trunk [*]
|
+- $/other_project/b1
|
+- $/other_project/b2

Cloning root branches (marked by [*]) is recommended!

PS:if your branch is not listed here, perhaps you should convert the containing folder to a branch in TFS
```

We may not find the project, or branch in the list, this is because our branch is surely a folder and not a branch itself.

### Convert a folder to branch (optional)

In the case of not having the branch we need, it is very easy to [convert a folder to a branch](https://www.visualstudio.com/es-es/docs/tfvc/branch-folders-files).

From *Visual Studio*, we access *Source Control Explorer* from our *TFVC* repository, in which we will see the different folders that make up the project. We just have to select the folder that we want to convert into a branch, right click on the mouse and select: `Branching and Merging > Convert to Branch`.

![Convert to Branch](/assets/posts/en/2017-11-25-migrando-tfvc-git/convert-to-branch.png)

### Clone only the root

It is possible that we only need to clone the root for different reasons: that we are not interested in the rest of the branches, that we want to migrate our project to a free *Git* repository of dead branches... To do this, and continuing with the list of projects and branches mentioned above, we can use the following command to generate a *Git* repository from the *TFS* repository:

```shell
git tfs clone http://[ACCOUNT].VisualStudio.com/tfs/DefaultCollection $/project/trunk . --branches=none
```

This method is not appropriate if we have performed merges in our *TFS* repository. And we should use the following method.

### Clone all history

If on the other hand, we want to clone the entire *TFS* repository and generate a new *Git* repository with all available branches and changes, we just have to use the following command:

```shell
git tfs clone http://[ACCOUNT].VisualStudio.com/tfs/DefaultCollection $/project/trunk . --branches=all
```

Both options are slow but safe, so take the opportunity to have a coffee.

Once our *Git* repository has been created, we need to be located in the folder created to continue with the operations.

### Cleaning the commits (optional)

We can clean our new *Git* repository of any impurity created in the conversion and dragged from the *TFS* repository.

To clean all metadata created by *git-tfs* in commit messages:

```shell
git filter-branch -f --msg-filter "sed 's/^git-tfs-id:.*$//g'" -- --all
```

Once resolved, we verify that everything is correct in our *Git* repository and delete the `.git/refs/original` folder, which will erase all the old or dead branches.

If we want to have some ids of changes in a more "human" way and not the one that generated *TFS* in its day, we will use the following command:

```shell
git filter-branch -f --msg-filter "sed 's/^git-tfs-id:.*;C\([0-9]*\)$/Changeset:\1/g'" -- --all
```

### Uploading the changes to a remote repository

Add a remote repository to your local repository using the central *Git* repository of your choice:

```shell
git remote add origin https://github.com/user/project.git
```

Then, upload all the changes of all the branches created in the process:

```shell
git push --all origin
```

We're done.

## Conclusion

With these simple steps we will have converted our *TFS* repository to a *Git* repository keeping all the history of changes, all the branches, and all the information of our old repository.

> NOTE: The *WorkItems* / Tasks associated with each *checkin* / *commit* **can not be converted from** *TFS* to *Git*, for this, you would have to manually modify each commit and add the information.

> NOTE: At this time, *Visual Studio Team Services* works separately with the *Git* and *TFS* repositories, and **requires at least one of them once created**. That is, if at the time of converting our project from *TFS* to *Git*, we work on the same project and both repositories are uploaded to the same project, we would have two repositories in the same project, one from *Git* and another from *TFS*, by only existing one of each type, it would be impossible to delete the *TFS* repository and work only with the *Git* repository, having to somehow clean up or mark that the *TFS* repository has become obsolete.

## More info about git-tfs

- [Official repository](https://github.com/git-tfs/git-tfs).
- [Official web](http://git-tfs.com/).
- [Official documentation](https://github.com/git-tfs/git-tfs/tree/master/doc/).

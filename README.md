# Merge Conflicts

When merging two branches there are sometimes changes that Git cannot automatically resolve. Git prefers then to flag the conflict as something it cannot resolve instead of intervening and potentially causing even larger errors. Errors that require human intervention usually result from changes to the same file, for example two people modify the same line of a file. Git would then require the person merging the files to decide which one it should keep.

It is important to note that during a merge process if you choose the wrong line or mess up the merge you can revert the merge. Meaning you must only commit the merge once you are happy that everything has been merged properly. At any time you can use the `git reset --hard HEAD` command to reset your HEAD to the last commit before the merge (if you didn't pick up on what this is then read [this](http://www.gitguys.com/topics/head-where-are-we-where-were-we/)).

## Resolving a conflict

When there is a merge conflict Git will tell you that there is unmerged paths and it will give you a list of the files involved. This can be found using `git status`.

``` bash
Unmerged paths:
  (use "git add <file>..." to mark resolution)

	both modified:   src/main.c
```

Inside each conflict file Git places markers that indicate the area of conflict. Let's take the simple example where two changes affected the same line of code in a file. This means that Git needs you to decide which change to keep. You will manually need to edit the code to integrate both solutions into your project. Choosing how to fix your code will be up to your discretion.

Now in this example I have branched my original code, then on the new branch **and** on my current branch created commits that modify the same line of code. On the current branch I added "Result:" to a `printf` statement while on the branch I added "Output:".

``` bash
Original code (shared commit) ------ + "Result:"
                              |----- + "Output:"
```

This has caused a merge conflict as the commit which they both share now has two different diffs when compared with the HEAD of both branches.

Looking into the file `src/main.c`, as shown by `git status`, we would see the following around the line of interest.

``` 

        printf("Result: %s", tmp);                                               
                                                                       
        printf("Output: %s", tmp);                                               

```


Showing us a few things. It shows us our branch `master`, the beginning of the commit hash `3c2b284`, the commit message `Added README`, the changes made as well as the files added, in this case the `README.md`. To find out what the mode is read [here](https://stackoverflow.com/questions/737673/how-to-read-the-mode-field-of-git-ls-trees-output/8347325#8347325).

We have now successfully created a commit in our repository. Running `git log` we can see that the commit now appears in the repository's logs. In the log you can also see the entire commit hash which is used to identify that specific commit within your repo.

In a repo you will create many commits as you implement features and commit them, the commits will not be automatically visible to others who also have the repository on their machines or are looking through the web interface. Git does not automatically sync changes as it is designed to be usable offline, only syncing when told to. To then send your commits to the remote repository, stored on a Git server, you must `git push`. To understand what we are doing exactly when we are pushing we need to know a couple of things.

To push our code to the Git server we us the command `git push origin master`.

Breaking this command down we have:

 * `git push`, this is the command the tells Git to send all of the commits saved locally on your machine to the remote server.
 * `origin`, is the alias given to the remote server where our repository is stored. If you look into the `.git/config` file in your repositories root directory you will see something similar to the following

  ```
  ...
  [remote "origin"]
    url = git@git.alxhoff.com:alxhoff/espl-test.git
    fetch = +refs/heads/*:refs/remotes/origin/* 
  ...
  ```

  This is telling us that when we use `origin` we will be sending our commits to the server specified by that alias. It is possible to send to multiple servers at once, for instance a backup server, or to send to both a private and public server.

* `master`, tells Git which branch's commits we are wanting to send. We will cover branches soon.

Running `git push origin master` will show us the following

```
Enumerating objects: 3, done.
Counting objects: 100% (3/3), done.
Writing objects: 100% (3/3), 253 bytes | 253.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To git.alxhoff.com:alxhoff/espl-test.git
 * [new branch]      master -> master
```

which should be quick self-explanatory.

Now that we have covered how to add, commit and push we will stop using the ESPL repository and focus purely on this tutorial repository that you forked. The ESPL repository is there for you to use over the semester, read the exercise and project descriptions to see how your use of Git will be assessed. Using Git is required as part of the grading in this course, USE IT!

# Programming Challenge

Inside this repository there is a small programming challenge where you will learn about some more advanced features of Git, the basics of building a C project and also do some very basic programming exercises. The first step will be to start our C project. To do this you will need to get and merge the CMake project to your master branch, to do this we need to learn about branches and merging. First step it branching.

Git uses branches to allow for parallel development of code without interfering with the code on other peoples machines or without breaking your stable code base whilst you hack around some changes. How exactly branches are used is a personal preference or the preference of your company, but there are some good ideas to employ when using your Git branches.

We have already come across the master branch, as the name implies this is the root branch of the repository and it usually the most sacred of all branches. Good practice is to not develop directly on the master branch as the master branch should always have a stable version of your code that builds and runs, while it might not have all the latest features, it should be able to be demo'd at any moment in time (except maybe exactly during a merge). You should as such never push to master, only merge to master. Be prepared for impromptu requests to see your code running, it should thus be able to be run from your master branch always even if it lacks all the latest features.

As master is this stable branch that must never break, you might of guessed that all development should be done on other branches. This is correct. When working in large groups or on a project where you are swapping between implementing multiple features at the same time, each person or feature should be a branch. I would recommend towards having a branch for a feature as multiple people might work on a feature over the duration of a project. To understand the next concept we need to have a basic understanding of what merging is, while we will look at merging in practice shortly, a theoretical understanding is needed.

Merging, as the name implies, is the task of merging the code (the changes) from one branch into another. If you have a branch to implement a certain feature, you would then merge this feature into your master branch once the feature is completed. The merge process can become quite complex and involved but for now you should understand what merging aims to achieve. As you can imagine, merging large complex changes can become quite involved, while Git does a great job at handling most changes automatically it does not always manage on its own. As such, merging your changes into master can sometimes lead master to becoming unstable whilst the merge is handled. As such you can imagine that employing a merge branch to handle the merging of complicated code changes into one single delta can act as a good intermediary between your feature branches and the master branch.

Therefore it can be good practice to have a branch structure similar to the following

```
master ----- merging ----- feature A
                     |---- feature B
```

Thus any complicated merge conflicts will be contained to the merging branch and can be resolved there before being merge (via a less complex merge as the complex problems have now been resolved) into the master branch. We will cover merge errors and the likes later as your perform your own merges.

Let us now go to the merging branch. To check the branches that exist in the repository one can use the `git branch` command. A new repository might not show all of the branches as Git does not download all information when not required, to try and minimize the data required locally. `man git fetch` will detail how Git fetch can be used.

When no remote is specified the default `origin` is used. Run `git fetch` to thus retrieve all the branches and tags on the origin remote. Now run `git branch --all` to list all of the branches on the origin. We want a branch called `merging` where we will perform our merges before merging to master. To do this we need to first create a branch and then swap to this branch. To create a new branch simply use

``` bash
git branch merging
```
now if we list our branches using `git branch` you should now see that there is a `merging` branch. We now need to change to this branch so our modifications that we perform are done there. It should be noted that the new branch is a copy of this current branch, although if we were to continue modifying master then the merging branch would fall behind and would need to be brought back up to speed with master. But for now just checkout `merging` using the checkout command.

``` bash
git checkout merging
```
It is also good to note that this action of branching and checking out can be done in a single command by using the `-b` option with the `checkout` command.

``` bash
git checkout -b merging
```

# Merge Basics

Now that you have checked out your merging branch we are going to perform some merges. As this tutorial will also look into building C projects, using CMake specifically, we will using merging and other Git tools to pull a basic CMake project together.

Firstly we will want to make our Git server (origin remote) aware of this new branch we have created, as it does not get made aware of this change unless we tell it. Similarly to before we will use the `git push` command but this time our branch has changed.

As such please push the current branch using the previous command of
=======
This tells use that on our current branch (our current HEAD) the line containing "Result", where as on the branch we wish to merge into our current branch (bar) the line contains "Output". Git does not know which one we wish to use and as such we must decide. Let's say that we wish the have the line contain output and not result, then we must manually delete the markers from Git as well as the line. Using our new patch knowledge we can see the what needs to be done below.


``` bash
--- src/main.c	2019-03-20 11:47:22.947753390 +0100
+++ src/main.c	2019-03-20 11:47:34.777753931 +0100
@@ -8,11 +8,7 @@
     char *tmp = NULL;
     tmp = num_to_words(123);
     if (tmp)

-        printf("Result: %s", tmp);

         printf("Output: %s", tmp);

     else
         return 1;
    return 0;
```


Now back to the problem. You should be able to find a branch called `make`, check it out using your newly learnt checkout command. On this branch is the skeleton for our CMake project. Now to merge the `CMakeLists.txt` file, which is the core CMake file for any CMake build, into our merging branch. We need to use the `git merge` command. Details of this can be found in the manual, you should be able to run the correct `man` command yourself now to do this.

Merging is always done on the branch into which you wish to merge. If you wish to merge your `merging` branch into `master` you would first need to `git checkout master` and then merge `merging` into `master`. As we are wanting to merge the `make` branch into our current branch we don't need to change branches.

The `git merge` command handles the merging of files automatically, although it requires human intervention occasionally. We will get to this later. For now we simply want to merge the `CMakeLists.txt` into our current branch. As our branch does not have a `CMakeLists.txt` file the merge should not have any errors when performing this merge.

We can thus execute

``` bash
git merge make
```
This should give us an output along the lines of

``` bash
CMakeLists.txt | 12 ++++++++++++
1 file changed, 12 insertions(+)
create mode 100644 CMakeLists.txt
```
Telling us that a new file was created with 12 new insertions, 1 for each line in the file. Now if we run `git log` we will see the commits made on the make branch when this `CMakeLists.txt` file was added to the repo.

Now that we have got the commits from the make branch merged into our branch we should push these changes to the remote, running `git push` again will now show that the files have been pushed. If we rerun `git log` you will notice that the commit where the `CMakeLists.txt` file was commited has now changed from

``` bash
(HEAD -> merging, origin/make, make)
```
to
``` bash
(HEAD -> merging, origin/merging, origin/make, make)
```
meaning that this commit can now found be found in origin/merging and not just merging, origin/make and make. This annotation (`origin/`) signifies the remote branch (ie. the branch on the server). The branch merging is your local branch while the branch origin/merging is that on the remote.

# CMake

Now that we have merged our CMakeLists to our current branch we need to go about making the project build such that it is stable and is in a condition that we would be happy to have on `master`. Good practice when building code projects is to have a folder where all temporary and/or build files are kept such that your project folder doesn't become cluttered with temporary build files. Cleaning the build is also easier as all build files are clumped together.

A common standard practice is to use a `build` folder. As such create a build folder in your Git repo's root, such that the build folder and `CMakeLists.txt` are in the same folder.

Now running the command `man cmake` we can see that to execute a CMake script one simply has to call the command cmake and one can specify either the build directory or the path to the cmake script file. By specifying the build dir we can run the command

``` bash
cmake -B build
```
To execute the CMake script whilst specifying the `build` directory as our build directory. Running `git status` you can now see that the build directory is now untracked and has had changes done to it. Running `git status build` shows us that the build directory now includes a `CMakeCache.txt` and a directory `CMakeFiles`. These are the temporary files generated by CMake. Instead of using the '-B' option one can also first navigate into the build director and then execute `cmake ..` where `..` specifies the folder in which the CMake script can be found while the current directory (build) is used as the build directory. We will see that the `cmake` command did not run correctly right now, lets ignore this for now.

Now before we go ahead and actually get the CMake project building lets play it safe and add **all** of the current files in the Git to the staging area, commit and push them so that we have a safe point to return to. Do this yourself, using a meaningful commit message.

## .gitignore

When committing the files you will see a lot of new files being created in Git. These are all temporary build files and should not actually be added to Git. If you already was questioning what I was doing by adding all of these then pat yourself on the back, you were correct in thinking so. This is a common problem that people new to Git have in that they include all sorts of useless metadata files, build files and binaries to the Git repository so that they clutter the Git repository and make navigating around the branches difficult as you create little changes without meaning so that changing branches becomes more difficult. This will be something you will come across in the future. But for now we will now fix this error by using a file called the `.gitignore`.

The `.gitignore` is a hidden file that lives in the Git root and contains a list of files that should intentionally be left untracked. Meaning that changes to those files are not of concern to Git. A more detailed description of how to use this file can be found [here](https://git-scm.com/docs/gitignore).

For now we just want to tell Git that the build folder's contents should be left untracked. To do this we need to create the `.gitignore` file and put the build folder in it.

This can be done by runing
``` bash
echo "build/" > .gitignore
```
From the Git repo's root directory

## Removing Staging Cache

Now running Git status we can see that the `.gitignore` file is untracked but the files we wish to have untracked (the build folder) are still being tracked. This is because the files are in the staging cache and need to thus be removed before the gitignore will be applied to them. A common fix that is used is to simply remove all files from the staging cache and then add them back.

To do so run

``` bash
git rm -r --cached .
```
This will recursively remove tracked files from the staging cache. Running `git status` again will now show us that all of the files in the repo have been deleted, meaning deleted from the staging area. In the untracked files section you will now only see the README, CMakeLists and .gitignore as these files have not been ignored via the gitignore. Now we can add these files back and commit them using something such as "Actualizing gitignore" as the commit message. After pushing the new commit, if we look at the repo through the web interface, looking specifically at the files on the merging branch, you will see that the build files are not included. It is important to add all files that you do not want included in the repository to be added to the gitignore so that there is no way for them to become accidentally included in a commit, this makes you look like a Git noob if you are committing build files.

Once you have resolved the merge conflict you can then add the resolved file and finalize the merge with a normal commit. The commit message should summarize the changes during the merge.

Now that you has seen the basic ideas of how merging works, lets see if you can handle some more complex merge problem yourself. You will find a branch called "unknown_features" which has diverged from this current branch at the previous commit. Your job now is to merge this branch into this current branch and resolve the conflicts presented. The project is a self-contained CMake project inside the `merge_exercise` folder and you will need to apply you C knowledge and CMake knowledge to merge the files correctly to get the project building properly. Once you have the project merged and building, merge the project into `merging` and finally into `master`, if both projects are stable and working as expected. Finally create another tag with the annotation "Exercise 1.2 Submission".

If all of that is done then you have completed this tutorial. Please be wary that the use of Git is a requirement in this course and will be part of the project's assessment. Inform yourself on proper use of Git commit messages and make sure that you and your team partner establish a Git workflow that you will use throughout the course. A fun tool to use to make sure your workflow has been used properly is `git log --graph --all` which will give you a graphical representation of your repo's logs.

# Future Reading

There are a number of other features in Git that are useful to know. If you are motivated then I would recommend reading up on these features so that during semester you are able to overcome some problems you will no doubt encounter.


To do so jump to the branch `compiling`.

In your web browser, if you select the branch `compiling` you can read the README directly in the browser. 

 * `git stash`
 * `git pull`
 * `git show`
 * `git revert`
 * `git clean`

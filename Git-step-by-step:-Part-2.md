[< Return to Part 1](https://github.com/GSoft-SharePoint/Dynamite/wiki/Git-step-by-step:-Part-1)

We'll now go over the basic day-to-day source control operations you need to be familiar with.

###Your first commit

As we touched upon briefly in Part 1, committing with Git is different conceptually than in centralized version control systems like TFS and SVN. Git is a distributed version control system, and a full clone of the "central" repository from github.com (or hosted elsewhere) is kept locally on your machine.

Thus, when you commit some changes, you do so locally on your own computer - which allows you to work offline. There's also a brand new concept that comes into play before committing: the staging area.

Let's illustrate with our example. We are currently on the newly-created *develop* branch and we make a change to the README file. Note that the file status tab in SourceTree is automatically refreshed to indicate that your working copy is not clean anymore:

![srctree-stage](http://i.imgur.com/SQUhuz4.png)

* A. The indicator that your working copy is not clean anymore. Click on any file in the *File status* tab to make the visual diff of this file's changes appear on the right.
* B. These buttons allow you to stage your changes: basically, you can choose to stage only changes that you wish to commit right away. This gives you the flexibility to break up your current changes into multiple granular commits (by staging each piece one at a time and committing them separately) or to keep some of your changes on hold until later.

Once your changes are staged, you can *Commit*:

![srctree-commit-1](http://i.imgur.com/6Z0zW88.png)

![srctree-commit-2](http://i.imgur.com/v3m5qig.png)










###Branching and merging 101


###Sharing your changes with your colleagues: fetching, pulling and pushing




###[Move on to Part 3 >](https://github.com/GSoft-SharePoint/Dynamite/wiki/Git-step-by-step:-Part-3)
[< Return to wiki home](https://github.com/GSoft-SharePoint/Dynamite/wiki)
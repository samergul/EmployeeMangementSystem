[< Return to Part 2](https://github.com/GSoft-SharePoint/Dynamite/wiki/Git-step-by-step:-Part-2)
##Basic remote operations

Git is great when you want to work offline and still be able to commit some changes and work on multiple things in parallel. However, in the end you want to share your changes with your colleagues through the "central" repository hosted on Github.

###Publishing your changes: Pushing

Let's pick up where we left off with our example. Right now, we've made some local changes but we haven't published anything to our Github-hosted *origin* repo.

We have many commits locally and a new *develop* branch:

![push-1](http://i.imgur.com/Hxp4xkV.png)

But there's just a single commit and a single *master* branch at the *origin*:

![push-2](http://i.imgur.com/3KLvPOz.png)

Before doing a *Push* it's important to *Fetch* (or *Pull*, see below) so that we get the latest updates from the server and merge them in with our local changes. In a centralized version-control system like TFS (or SVN), the analog of *Pull* is "Get Latest..." (or "Update"). 

For the moment, let's assume no one has been working in parallel with us so far. So, let's publish our new *develop* branch by hitting *Push* (i.e. the equivalent of "Check in pending"):

![push-button](http://i.imgur.com/1vLDcPp.png)

The following dialog appears, giving you the option of selecting which local branches to push:

![push-dialog](http://i.imgur.com/YLACipX.png)

By checking the *develop* checkbox, SourceTree infers that you want to create a new remote branch (also called *develop*) and that you want your local *develop* branch to track its remote:

![push-dialog-2](http://i.imgur.com/6LZQTrW.png)

We can leave the experimental branch as local-only. When you confirm the dialog, your new branch is created on Github, the commits are pushed and the result is the following:

![push-result](http://i.imgur.com/j6FkAOC.png)

###Get the latest server updates: Fetching and Pulling

In TFS (or SVN), when you "Get Latest..." (or "Update") you risk generating conflicts with your current working copy.

In Git, when you *Fetch* from all your remotes, it is a much safer operation. All that will happen is that your local copy of the remotes' branches will be updated. It is then up to you to merge any new commits from the remote branches into your local branches.

Let's continue with our example. Assume that, prior to our initial *Push*, we had cloned the original repo on another PC (referred to henceforth as **PC2**). In PC2's local repo the *Log / History* view looks like this (assuming gitflow was initialized):

![pc2-clone](http://i.imgur.com/bHeskUn.png)

In the section above we did a *Push* on the *develop* branch from our first development environment **PC1** (e.g. your office computer). Now, we want to get those changes down on our **PC2** repository (e.g. your home laptop). Let's *Fetch* from all remotes:

![fetch-button](http://i.imgur.com/5HjWASO.png)

The result is that your local copy of the remote branches is updated, as shown here:

![fetch-result](http://i.imgur.com/TOdJ18T.png)

Now, what we need to do  we have two equivalent options:

* *Merge* *origin/develop* into *develop* OR
* *Pull*
    * If you *Pull* while checked out on your *develop* the following will happen:
        1. Git will *Fetch* to bring your remotes/origin/develop branch up to date (which we just did manually)
        2. Git will *Merge* any new commit from *remotes/origin/develop* into *develop*
    * In essence, **Pull = Fetch + Merge**

To be on the safe side, try to always use *Fetch* before using *Pull*, so that you get a chance to visualize how difficult the remote-to-local merges will be.

If you decide to *Pull*, SourceTree will ask you which remote branch to merge into *develop*. Choose *origin/develop*:

![pull-dialog](http://i.imgur.com/vDyqYK1.png)

The end result of both operations will be a fast-forward (i.e. trivial merge) giving you this:

![pull-result](http://i.imgur.com/isNyZlc.png)

Your local *develop* branch is now up-to-date with the version on Github.

###Working in parallel

Let's imaging that, for some reason, you decide to work in parallel from PC1 and PC2 (so that we can give a further example on how to share your progress with your colleagues).

From PC1 we do a couple of commits locally:

![parallel-PC1-1](http://i.imgur.com/aYzqERZ.png)

And from PC2 we do a few commits as well:

![parallel-PC2-1](http://i.imgur.com/gNeaDwc.png)

Note how PC2 doesn't tell us "2 commits ahead" because we haven't ever done a *Push* from PC2, which means that on PC2 our local branch *develop* hasn't yet been linked as tracking branch for *origin/develop*.

Now, let's push our changes from PC2 to Github, the end result being:

![parallel-PC2-2](http://i.imgur.com/2BAAltg.png)

So, the Github repo now has PC2's changes but not PC1's changes yet:

![parallel-github-PC2only](http://i.imgur.com/MTLO3FT.png)

Let's switch over back to PC1. After we *Fetch* from PC1 we now see:

![parallel-PC1-2](http://i.imgur.com/bI8X9y5.png)

Note how *origin/develop* is ahead of our local *develop* by 2 commits: this explains the "2 behind" note SourceTree puts on our local *develop* branch. We need to *Pull* these two changes into out working copy.

Also take heed of the "2 ahead" note: this means we have 2 local commits that we haven't pushed to the server yet.

Before pushing, we always need to merge the server's latest changes into our local branches. Make sure you're checked out on your local *develop* branch and then use *Pull* (or *Merge*) to merge the two PC2 commits from *origin/develop* into your local PC1 *develop*:

![parallel-PC1-3](http://i.imgur.com/3T4I0tI.png)

If not merge conflicts occur between the changes from PC2 and our local PC1 commits, a merge commit will be automatically created, making the local *develop* branch "3 ahead" of its remote:

![parallel-PC1-4](http://i.imgur.com/OiNec92.png)

If merge conflicts had occurred, you would have had to 1) fix those conflicts manually or through a DiffMerge-like tool then 2) commit your merge conflict resolution changes.

You can now push your changes from PC1. In the *Push* dialog, uncheck the branches you do not wish to push:

![parallel-PC1-5](http://i.imgur.com/903x7a4.png)

The *origin* on Github will now hold changes from both PC1 and PC2:

![parallel-PC1-6](http://i.imgur.com/7CSaRPl.png)






###[Move on to Part 4 >](https://github.com/GSoft-SharePoint/Dynamite/wiki/Git-step-by-step:-Part-4)
[< Return to wiki home](https://github.com/GSoft-SharePoint/Dynamite/wiki)
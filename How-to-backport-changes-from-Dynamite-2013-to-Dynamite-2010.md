The main Dynamite project targets SharePoint 2013 and thus uses the .NET 4.5 runtime. This project provides you with a VS2012 solution.

The Dynamite-2010 project is a fork of the main Dynamite project which targets SP2010 and .NET 3.5. This legacy version offers you both VS2010 and VS2012 solutions (to allow developers stuck on an older VS to contribute in spite of that)

Bleeding-edge development usually occurs in the main Dynamite project. If we want to share some of these new changes with the 2010 version, we need to backport these changes in a granular fashion (in order to avoid merging into the 2010 version code which is .NET 4.5-specific - things like async/await, etc.).

###Step 1: apply your changes on the main Dynamite project's develop branch

From a SharePoint 2013 environment, create and commit some changes on the main (2013) Dynamite project. Push these changes to github.com. Let's assume that you want to apply these changes on the 2010 version as well.

###Step 2: add the main Dynamite project as upstream remote on your Dynamite-2010 local repo

Now that the Dynamite 2013 version includes your changes, turn off your SP2013 VM and spin up your SharePoint 2010 environment.

Fetch and merge (i.e. pull) from Github to bring your Dynamite-2010 working copy up to date.

Normally, if you cloned your local repo from Github, there is already one remote: *origin*, which is connected to the Dynamite-2013 Github repo. 

Let's add a second remote: a new remote called *upstream* which points to the original main Dynamite (2013) project. 

* By convention, *upstream* this is the name of a forked repo's original location: for example if I ever forked jQuery on my personal github account, my fork would be at github.com/taoneill/jquery (i.e. my *origin*) and its *upstream* repo would be github.com/jquery/jquery.

![backport-new-remote](http://i.imgur.com/n07MHY3.png)

Click on *Add* in the dialog that appears. Then enter the new remote details:

![backport-new-remote-2](http://i.imgur.com/YCX4Lkn.png)

Confirm with OK. The *upstream* remote should appear, but without any branches under it, at first.

Do a *Fetch* and the remote repository's branches should appear:

![backport-new-remote-3](http://i.imgur.com/t4JHy8x.png)

### Step 3: merge (or cherry pick) changes from *remotes/upstream/develop* into your local *develop* branch

We're now ready to bring code from *upstream* into our local repo. We have two choices:

* Merge: apply all commits from *upstream/develop* onto your own *develop*. Do this when you want to update Dynamite-2010 with everything new from Dynamite(-2013). Usually, this is NOT the case (since we want to avoid merging in .NET 3.5-incompatible changes
    * To do this, simply make sure you are checked out on your local *develop* branch, choose *Merge*, select the topmost commit on the upstream/develop branch, confirm and resolve any conflicts that occur as a consequence.
* Cherry-pick: apply only specific commit from *upstream/develop* onto your local *develop* branch. This is most likely the case.
    * To cherry-pick *upstream* changes into your local *develop*, from the *Log / History* view, click on the commit you want to select (or CTRL-select the couple of commit you are interested in, if more than one). Then, right-click and the *Cherry Pick* option should appear:

![cherry-pick](http://i.imgur.com/Ec5FBTg.png)

You'll need to confirm the application of the cherry-picked commit(s) on top of your current (*develop*) branch:

![cherry-pick-confirm](http://i.imgur.com/0jF39La.png)

Note how the *Log / History* view now shows how the cherry-picked commit was applied on *develop*:

![cherry-pick-result](http://i.imgur.com/W73Gkx4.png)

### Step 4: Push your backported changes to *origin*

Your local *develop* branch now includes changes that used to be exclusive to the main Dynamite 2013 project - congrats. All you need to do now is push to your *origin* (i.e. Dynamite-2010) on Github.
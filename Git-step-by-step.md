This guide aims to give you step-by-step instructions on how to use SourceTree to manage your source control. For demo purposes, we'll begin by creating a new empty project on github.com

### Create a new project on github
From [GSoft-SharePoint's organization page](https://github.com/organizations/GSoft-SharePoint), click on *New repository*. 

![github-new-repo](http://i.imgur.com/3wWB8Kx.png)

Make sure to check the option to create a README file. Also, Github gives you the handy option of adding a pre-configured .gitignore file: choose the CSharp settings. The typical open source license we use is MIT License.

![github-new-repo-form](http://i.imgur.com/R74Xgl7.png)

You'll be redirected to your new project's home page.

![github-repo-home](http://i.imgur.com/xjPp2El.png)

*A. Click on the commit count to visualize the currently selected branch's commmit history.
*B. Use this dropdown to switch between the branches that are available. For now we only have master.
*C. This is the repository's clone URL that we'll use shortly to clone the repository locally.

### Clone your project locally with SourceTree

Now we can start SourceTree from our work machine (from now on referred as **PC1**) and choose *Clone / New*. Paste the repo's clone URL and map it to the local folder of your choice:

![srctree-clone](http://i.imgur.com/HtFWEq3.png)

Once you've cloned the repo, click on *master* under *Branches* and you'll be presented with a 1-commit version history:

![srctree-home](http://i.imgur.com/2F2i0Os.png)

*A. Under *Branches* you find your local branches. Any commits made on these branches will be kept locally
    *i.e. when you commit in Git, you only affect your local branches, allowing you to keep your code under version control even if you are working offline without internet access
*B. Under *Remotes* you'll find your remote-tracking branches. Here, we only have the *origin* remote: this is your copy of the remote repository hosted at github.com. You never commit directly on a remote-tracking branch; they are used solely as a staging area when fetching new changes from the server or when pushing your changes back to origin.
    *In other words, when you clone a repository, two copies of the server's branches are made locally: one for your working branches and another to track and push changes to/from the github-hosted repository.

### Setup gitflow on your local repository
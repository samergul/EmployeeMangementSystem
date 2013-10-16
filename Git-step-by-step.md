This guide aims to give you step-by-step instructions on how to use SourceTree to manage your source control. For demo purposes, we'll begin by creating a new empty project on github.com

### Create a new project on github and clone it
From [GSoft-SharePoint's organization page](https://github.com/organizations/GSoft-SharePoint), click on *New repository*. 

![github-new-repo](http://i.imgur.com/3wWB8Kx.png)

Make sure to check the option to create a README file. Also, Github gives you the handy option of adding a pre-configured .gitignore file: choose the CSharp settings. The typical open source license we use is MIT License.

![github-new-repo-form](http://i.imgur.com/R74Xgl7.png)

You'll be redirected to your new project's home page.

![github-repo-home](http://i.imgur.com/xjPp2El.png)

* A. Click on the commit count to visualize the currently selected branch's commmit history.
* B. Use this dropdown to switch between the branches that are available. For now we only have master.
* C. This is the repository's clone URL that we'll use shortly to clone the repository locally.

Now we can start SourceTree and choose *Clone / New*. Paste the repo's clone URL and map it to the local folder of your choice:

![srctree-clone](http://i.imgur.com/HtFWEq3.png)

Once you've cloned the repo, click on *master* under *Branches* and you'll be presented with a 1-commit version history:

![srctree-home](http://i.imgur.com/2F2i0Os.png)



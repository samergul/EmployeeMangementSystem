The recommended git client app to contribute on Team Dynamite's project is Atlassian's SourceTree because of its [Jimmy](http://www.codinghorror.com/blog/2012/07/new-programming-jargon.html)-proof integration of git-flow.

### Create a github.com account
If you don't have one already, create a github.com account. If you work for [GSoft](http://www.gsoft.com), create [an issue](https://github.com/GSoft-SharePoint/Dynamite/issues) to ask one of the Team Dynamite owners to add you to a contributor's team for the project you want to work on.

If you are a third-party developer looking to contribute to Team Dynamite's projects, please fork the project in your own profile, make your improvements then [submit a pull request](https://help.github.com/articles/using-pull-requests) to ask Team Dynamite to merge in your changes.

### Download and install SourceTree
SourceTree can be found at http://www.sourcetreeapp.com/. It is preferred to Github for Windows or Visual Studio's git integration because only SourceTree gives you UI support to follow the git-flow development process.

During installation:
* Enter your full name and email address (your @gsoft.com email, if you work for GSoft)
* Choose the option to install the self-contained Git version to be used by when prompted
* Skip the Mercurial option
* Choose Putty as SSH option
* Enter your github.com username and password

### Git basics
If you're unfamiliar with Git, please take the time to review the basics on a tutorial site such as:
* http://gitimmersion.com/
* https://www.atlassian.com/git
* http://www-cs-students.stanford.edu/~blynn/gitmagic/
* http://stackoverflow.com/questions/315911/git-for-beginners-the-definitive-practical-guide
* A look at git internals: http://ftp.newartisans.com/pub/git.from.bottom.up.pdf

You need to get comfortable with the concepts of:
* Cloning a repo
* Staging and committing changes
* Branching and merging
* Fetching and pulling
* Pushing to your origin repo on github.com
* Managing multiple remotes(origin, upstream, contributors' forks, etc.)

It really helps to get confortable with the git command line while learning Git, instead of relying on the SourceTree interface. You'll need to know your way around the command line if you want to leverage the full power of Git with more advanced features like the stash, rebasing and cherry-picking. Create some test repositories to play around with the various features before trying to contribute.

### Using git-flow
SourceTree gives us a nice UI integration with git-flow, a robust workflow that helps us manage releases in a systematic way. Press the git-flow button the the SourceTree application ribbon to start with initializing git-flow on your local repository.

Take the time to understand the power of git-flow (its feature branches, releases and hotfixes):
* https://www.atlassian.com/git/workflows#!workflow-gitflow
* Make sure you memorize the workflow illustrated here: http://danielkummer.github.io/git-flow-cheatsheet/

SourceTree's git-flow integration is context aware. For example, when you are checked out on the develop branch, SourceTree's git-flow dialog only gives you the option of starting a new release or a new feature. When you are working on a feature branch, the dialog guides you in the right direction by suggesting that you finish your feature (which merges it back into the develop branch), and so on.
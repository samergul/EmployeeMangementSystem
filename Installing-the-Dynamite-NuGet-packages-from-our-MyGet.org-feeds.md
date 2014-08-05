The Dynamite toolkit is available as NuGet packages from our MyGet.org NuGet feeds. 

For the time being we maintain separate package feeds for stable releases and prerelease. Down the road, these feeds may get consolidated into a unique feed.

##SharePoint 2013 feeds

Stable feed: https://www.myget.org/F/dynamite-2010/

Dev (prerelease) feed: https://www.myget.org/F/dynamite-2013-dev/

##SharePoint 2010 feeds

Stable feed: https://www.myget.org/F/dynamite-2010/

Dev (prerelease) feed: https://www.myget.org/F/dynamite-2010-dev/

##How to connect to one of the feeds?

You should only connect to one of the feeds at a time. In Visual Studio:

1. On your solution in Solution Explorer, right click and open *Manage NuGet Packages for Solution...*
2. At the bottom left, open the *Settings*
3. Press the + button at the top right to add a new feed.
4. Copy-paste one of the above feed URLs. Pick a good name for the feed like "Dynamite Dev 2013".
5. Press *Update*, then *Ok* to close the dialog.
6. The new feed should become available in your NuGet "Online" and "Updates" feeds.
7. If you connected to a dev/prerelease feed, be sure to toggle the dropdown at the top right from *Stable only* to *Include prereleases*.

##Available packages

These two packages are available from each of the public feeds (2010 or 2013):

###Project-specfic package: GSoft.Dynamite
The GSoft.Dynamite package adds a reference to the GSoft.Dynamite.DLL and its utilities to that you can use them in your code. Note that this package will also install Autofac and Newtonsoft.Json in your project references.
![GSoft.Dynamite Package](http://i.imgur.com/5qcWXpl.png)

###Solution-wide package: GSoft.Dynamite.SP
The GSoft.Dynamite.SP is a solution-wide NuGet package, meaning that it will not add any DLL references in your projects. Rather, this package will just download some files to your packages folder and then you can use to help your kickstart your SharePoint custom development project.

Namely, the GSoft.Dynamite.SP package includes:
* GSoft.Dynamite.WSP
    * a solution package that provisions the following DLLs to the GAC: GSoft.Dynamite.dll, Autofac.dll, Newtonsoft.Json.dll
    * provides a few features to help you kickstart your project
    * install it using ```Add-SPSolution <path-to-file>\GSoft.Dynamite.wsp``` then ```Install-SPSolution GSoft.Dynamite.wsp -Identity GSoft.Dynamite.wsp -GACDeployment``` (or use DSP's ```Deploy-DSPSolutions``` - see below)
* The Dynamite PowerShell Toolkit (aka DSP)
    * a PowerShell module that can help you automate your project setup
    * install it locally by running the ```Install-DSPModule.ps1``` script
        * Please note that this module will modify your ```C:\Users\your.username\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1``` so that the SharePoint PowerShell Snap-in is attached to all regular PowerShell windows. Tweak your profile script manually to your liking after install if you don't like this behavior.
    * use ```Get-DSPCommand``` to list all available commands

Thus, the NuGet package GSoft.Dynamite.SP is the perfect companion to GSoft.Dynamite to ensure that the Dynamite DLL and its dependencies are properly deployed to the GAC.
![GSoft.Dynamite.SP Package](http://i.imgur.com/dxcbsXW.png)

## How to update a Dynamite NuGet package
Updating NuGet packages can get a bit hairy if you are not careful.

Be sure to follow these guidelines to avoid headaches:

1. Include the download packages under source control whenever practical (this makes the solution work even if NuGet feeds are unavailable).
2. Don't attempt to update packages that have already been updated by one of your coworkers concurrently. The merge conflict could be bloody (and, under TFS, file locks could ruin your NuGet update operation very fast). 
3. Update packages as part of a specific commit which includes as few other changes as possible. This way, the package update commit is completed quickly (less risk of merge conflicts, etc.)
4. Before installing Dynamite or another package as a new reference on a project, stop and go update that package from another project which already depends on that package. If you don't, some if your projects may end up depending on different versions of Dynamite at the same time (both versions of the package would get installed side-by-side).
5. Be sure to use the NuGet package management dialog available by right-clicking on your Visual Studio solution (*Manage NuGet Packages for Solution...*). Also, for updates, don't forget to switch from *Online* to the *Updates* tab:

![package-updates](http://i.imgur.com/p5aTiuD.png)


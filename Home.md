Welcome to the Dynamite wiki!

### Git beginner's guide

* Overview: [Getting started with SourceTree, Git and git-flow](https://github.com/GSoft-SharePoint/Dynamite/wiki/Getting-started-with-SourceTree,-Git-and-git-flow)
* Git step-by-step:
    * Part 1: [Cloning a new repo from github.com with SourceTree, initializing gitflow](https://github.com/GSoft-SharePoint/Dynamite/wiki/Git-step-by-step:-Part-1)
    * Part 2: [Basic local operations - Staging, commiting, branching and merging](https://github.com/GSoft-SharePoint/Dynamite/wiki/Git-step-by-step:-Part-2)
    * Part 3: [Basic remote operations - Fetching, pulling and pushing](https://github.com/GSoft-SharePoint/Dynamite/wiki/Git-step-by-step:-Part-3)
    * Part 4: [Using gitflow for feature branches, releases and hotfixes](https://github.com/GSoft-SharePoint/Dynamite/wiki/Git-step-by-step:-Part-4)
    * Part 5: [The stash is your best friend](https://github.com/GSoft-SharePoint/Dynamite/wiki/Git-step-by-step:-Part-5)
* Advanced guides: 
    * How to back-port changes from the main Dynamite (2013) repo into Dynamite-2010's codebase
    * Keeping two distinct TFS servers' projects in sync by using github.com as an intermediary

### Developer's guide

(WIP - Coming soon...)

This guide applies to SharePoint 2013 (and 2010) farm solution (i.e. full-trust WSPs) architecture
* Getting started with the GSoft.Dynamite toolkit and Microsoft Unity for dependency injection
    * How to set up your first Unity application container (for service location)
    * As a first example, we introduce Dynamite's ILogger interface
    * Using constructor injection to gracefully declare all your classes' collaborators and to avoid referring to your application container throughout your codebase
    * Providing your own services through Unity by configuring your own IRegistrationModule
* Managing dependencies and choosing how to break up your Visual Studio solution into multiple projects
    * A strategy on how to package, deploy and support multiple versions of third-party libraries and your own core-logic DLLs on your SharePoint farm
    * Two approaches on how to split up your SharePoint Solution (WSP) projects
* Properly setting up your resource files for uses in SharePoint XML files and in server-side code
    * Dynamite's IResourceLocator helps you abstract out SharePoint's relatively baroque resource API and fetch resources from both locations (XML and AppGlobalResources)
* Using the EntityBinder to map your business entities to SPListItems

Also, don't miss the Guide to easy, automated and repeatable SharePoint deployments using the Dynamite Powershell Toolkit.
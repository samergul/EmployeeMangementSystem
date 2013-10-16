Welcome to the Dynamite wiki!

### Contributor's guide

* [Getting started with SourceTree, Git and git-flow](https://github.com/GSoft-SharePoint/Dynamite/wiki/Getting-started-with-SourceTree,-Git-and-git-flow)
* Step-by-step tutorial on how to setup your local repo with SourceTree
* Advanced guide: how to back-port changes from the main Dynamite (2013) repo into Dynamite-2010's codebase

### Developer's guide

(WIP - Coming soon...)

This guide applies to SharePoint 2013 (and 2010) farm solution development (i.e. full-trust WSPs)
* Getting started with the GSoft.Dynamite toolkit and Microsoft Unity for dependency injection
    * How to set up your first Unity application container (for service location)
    * As a first example, we introduce Dynamite's ILogger interface
    * Using constructor injection to gracefully declare all your classes' collaborators
    * Providing your own services through Unity by building your own IRegistrationModule
* Managing dependencies and choosing how to break up your Visual Studio solution into multiple projects
    * A strategy on how to package, deploy and support multiple versions of third-party libraries and your own core-logic DLLs on your SharePoint farm
    * Two approaches on how to 
* Properly setting up your resource files for uses in SharePoint XML files and in server-side code
    * Dynamite's IResourceLocator helps you abstract out SharePoint's relatively baroque resource API and fetch resources from both locations (XML and AppGlobalResources)
* 
* Using the EntityBinder to map your business entities to SPListItems

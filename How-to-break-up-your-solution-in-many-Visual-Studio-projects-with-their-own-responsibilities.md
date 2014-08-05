When you start a brand new Visual Studio solution for SharePoint development, it can be hard to know how to organize your projects (WSP projects and class libraries) for maximum code reuse and easy deployments.

##Anti-patterns

One instinct is to split up your solution in many WSP (SharePoint-type) projects, with one project per type of component. Another idea is to spin off most C# in a core class library. For example:

* > Company.Project.sln
    * > Company.Project.Branding.wsp
    * > Company.Project.ContentTypes.wsp
    * > Company.Project.Core.dll
    * > Company.Project.PageLayouts.wsp
    * > Company.Project.WebParts.wsp
    
This type of separation of concerns is convenient from a developer's point of view, because each project has different "types" of components, so it's easy to get around the solution. The approach works for smallish projects but begins to show some cracks as the project gets larger.

The main issue here is this: suppose that, in your project's context, you are tasked with building a full intranet portal home site collection. Your backlog is full of requirements like:

* A nice branding
* Static page content and navigation controls
* A news archive and a nice news feed on the front page
* Search that works with a customized look for search results
* etc.

So your project is going to be large. If you take the one-project-per-component-type approach above, you'll end up with something like this:

![project-per-component-type](http://i.imgur.com/LsBvblB.png)
In effect, this gives you a little bit of each functionality in each Visual Studio project. Two major consequences are:

1. It will hard to reuse any of these functionalities (for example, the Navigation functionality) without depending on the entire original project. If you decide that you want to extract one of these features out of the original solution to package it independently, you will have a bad time (because everything will probably coupled together).
2. *Perhaps most importantly* (for those of you living in the enterprise who live under strict release guidelines): Whenever you want to patch a small part of a feature, you will often be forced to redeploy all the WSP packages in your solution. This could mean a big slowdown in the process of quality assurance, authorization and deployment procedures - since much more of the application than needed comes under scrutiny and will need to be re-tested.

If we want to avoid this "monolithic" model, we need to bring in a more modular approach.

## All aboard the modular train

The modular approach that we recommend to maximize code reuse is one where a group of three projects is used for every "functional module". For example:

* > Company.Project.sln
    * > Company.Farm.Dependencies.wsp
    * > Company.Project.Branding.wsp
    * > Company.Project.**Nav**.wsp
    * > Company.Project.**Nav**.Contracts.dll
    * > Company.Project.**Nav**.Core.dll
    * > Company.Project.**News**.wsp
    * > Company.Project.**News**.Contracts.dll
    * > Company.Project.**News**.Core.dll
    * > Company.Project.Search.wsp
    * > Company.Project.ServiceLocator.dll

Note how the News and Nav functional modules come with their own Contracts and Core class libraries. Also note how they is no trace of a solution-wide "Company.Project.Core.dll" anymore. 

### Company.Project.Module.Contracts.dll

The "Contracts" assembly of a module holds the following:

* The interfaces for all Services provided by the module
* The business entities used to expose the module's data in C# objects (and often serialized to JSON when used client-side)
    * in effect, these entities are data serialization contracts for your module
* Any static constants specific to the module


### Company.Project.Module.Core.dll

The "Core" assembly of a module holds the following:

* the implementations of the Service interfaces declared in the Contracts DLL
* the module's Autofac type registration module for dependency injection configuration
    * this module will be automatically scanned in the GAC by your application's Service Locator (see below) to make your module's types visible and reusable across the Autofac application container's app domain.
    * see [this other article about how to build your own registration module](https://github.com/GSoft-SharePoint/Dynamite/wiki/How-to-provide-your-own-reusable-services-through-an-Autofac-registration-module)


### Company.Project.Module.wsp

The SharePoint solution package reserved for the module must take care of provisioning both assemblies ```Company.Project.Module.Contracts.dll``` and ```Company.Project.Module.Core.dll``` in the Global Assembly Cache (GAC).

As soon as 
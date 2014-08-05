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

Note how Branding and Search are smaller modules, and don't need to expose any reusable functionality through a Contracts DLL of their own.

The roles of the Dependencies and ServiceLocator projects are explained below.

### Company.Project.Module.Contracts.dll

The "Contracts" assembly of a module holds the following:

* The interfaces for all Services provided by the module
* The business entities used to expose the module's data in C# objects (and often serialized to JSON when used client-side)
    * in effect, these entities are data serialization contracts for your module
* Any static constants specific to the module (site column names, content type ids, etc.)


### Company.Project.Module.Core.dll

The "Core" assembly of a module holds the following:

* the implementations of the Service interfaces declared in the Contracts DLL
* the module's Autofac type registration module for dependency injection configuration
    * this module will be automatically scanned in the GAC by your application's Service Locator (see below) to make your module's types visible and reusable across the Autofac application container's app domain.
    * see [this other article about how to build your own registration module](https://github.com/GSoft-SharePoint/Dynamite/wiki/How-to-provide-your-own-reusable-services-through-an-Autofac-registration-module)


### Company.Project.Module.wsp

The SharePoint solution package reserved for the module must take care of provisioning both assemblies ```Company.Project.Module.Contracts.dll``` and ```Company.Project.Module.Core.dll``` in the Global Assembly Cache (GAC).

As soon as your project's service locator scans the GAC and finds the Autofac registration module, your module's types will become available across the container's domain.

Otherwise, your module's WSP is structured like a classic SharePoint project, with feature encapsulating your projet's provisioning operations. See [The case for intelligent, code driven, self correcting features](https://github.com/GSoft-SharePoint/Dynamite/wiki/The-case-for-intelligent,-code-driven,-self-correcting-features) for more on how to structure your features' activation behavior.

### Company.Project.ServiceLocator.dll

This class library project holds only a single class: your project's application container - i.e. the class that will configure [Dependency Injection](https://github.com/GSoft-SharePoint/Dynamite/wiki/What-is-Dependency-Injection%3F) across your solution and that will be used for service location.

For instructions on how to setup your Container so that all of your solution's modules are loaded correctly, please see [How to set up your first application-wide Autofac service locator](https://github.com/GSoft-SharePoint/Dynamite/wiki/How-to-set-up-your-first-application-wide-Autofac-service-locator).

### Company.Farm.Dependencies.wsp

The Dependencies WSP is responsible for deploying all 3rd party and common DLL dependencies across the Company's SharePoint farm (hence the .Farm namespace discriminator, to emphasize that you should only maintain one such project)

Besides deploying DLLs to the GAC, this WSP package does nothing else.

Why centralize DLL deployment like this? Because, in a SharePoint on-premise environment, when any WSP can decide to deploy any DLL to the GAC, we need a strategy to prevent the following unfortunate scenario:

1. ProjectX.wsp packages the very common 3rd party DLL Newtonsoft.Json (the Json.NET serialization utility library) because one of its features needs it.
2. Upon deployment to the farm, the Json.NET DLL becomes available in the GAC.
3. ProjectY.wsp packages the same DLL in its WSP
4. ProjectY.wsp is deployed to the farm. Suddenly, ProjectY.wsp become the WSP "responsible" for the JSON.NET DLL.
5. ProjectY.wsp is uninstalled from the farm. This removes Newstonsoft.Json.DLL from the GAC.
6. ProjectX's features are now broken, because the JSON.NET is absent from the GAC.

Thus, across all solutions that deploy to the same corporate farm, you must maintain a single and only Dependencies project, responsible for providing all these 3rd party DLLs to all of your company's SharePoint solutions.

In our example above, ```Company.Farm.Dependencies.wsp``` is responsible for provisioning the ```Company.Project.ServiceLocator.dll``` in the GAC (since the WSP projects will depend on it - see [Do's and Don'ts of Service Locator usage](https://github.com/GSoft-SharePoint/Dynamite/wiki/Do's-and-Don'ts-of-Service-Locator-usage) for more).

Don't forget to sign all the DLLs that you provision in the GAC: execution in a SharePoint context requires signing. Try to use a common signing key across all projects the Visual Studio solution (use *Add existing file > Add as link* in solution explorer to re-use the same key in every project).
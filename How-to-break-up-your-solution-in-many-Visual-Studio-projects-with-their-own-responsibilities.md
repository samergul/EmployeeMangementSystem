When you start a brand new Visual Studio solution for SharePoint development, it can be hard to know how to organize your projects (WSP projects and class libraries) for maximum code reuse and easy deployments.

##Anti-patterns

One instinct is to split up your solution in many WSP (SharePoint-type) projects, with one project per type of component. Another idea is to spin off most C# in a core class library. For example:

* > Company.Project.sln
    * > Company.Project.ContentTypes.wsp
    * > Company.Porject.Branding.wsp
    * > Company.Project.Core.dll
    * > Company.Project.WebParts.wsp
    * > Company.Project.PageLayouts.wsp
    
This type of separation of concerns is convenient from a developer's point of view, because each project has different "types" of components, so it's easy to get around the solution. The approach works for smallish projects but begins to show some cracks as the project gets larger.

The main issue here is this: suppose that, in your project's context, you are tasked with building a full intranet portal home site collection. Your backlog is full of requirements like:

* A nice branding
* Static page content and navigation controls
* A news archive and a nice news feed on the front page
* Search that works with a customized look for search results
* etc.

So your project is going to be large. If you take the one-project-per-component-type approach above, you'll end up with something like this:





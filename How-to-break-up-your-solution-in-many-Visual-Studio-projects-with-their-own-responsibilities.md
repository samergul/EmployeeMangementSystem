When you start a brand new Visual Studio solution for SharePoint development, it can be hard to know how to organize your projects (WSP projects and class libraries) for maximum code reuse and easy deployments.

##Anti-patterns

One instinct is to split up your solution in many WSP (SharePoint-type) projects, with one project per type of component. For example:

* > Company.Project.sln
    * > Company.Project.ContentTypes.wsp
    * > Company.Project.WebParts.wsp
    * > Company.Project.PageLayouts.wsp
    


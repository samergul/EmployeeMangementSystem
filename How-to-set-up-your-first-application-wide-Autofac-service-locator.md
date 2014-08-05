So you've decided to go the modular route and you're ready to set up your ServiceLocator project as outlined [here](https://github.com/GSoft-SharePoint/Dynamite/wiki/How-to-break-up-your-solution-in-many-Visual-Studio-projects-with-their-own-responsibilities#all-aboard-the-modular-train)? Let's do this.

Install the GSoft.Dynamite NuGet package in your empty Company.Project.ServiceLocator.dll project (as a matter of convention, it is important that your assembly file name respects the pattern "*.ServiceLocator.dll").

Add a class to your project, name it ProjectContainer. Start by populating it like this:

```
// The Container that is used by WSPs for dependency injection across 
// all Company.Project.*.WSP projects
public class ProjectContainer
{
    // The key that distin
    private const string AppName = "Company.Project";

    // The locator will scan the GAC and load the following Autofac registration modules:
    // - the core Dynamite utilities registration module
    // - all registration modules from assemblies with a filename that starts with "Company.Project"
    private static ISharePointServiceLocator locatorInstance = new SharePointServiceLocator(AppName);
}
```

This is the core of the service locator/container. The interface ```ISharePointServiceLocator``` comes from the namespace ```GSoft.Dynamite.ServiceLocator``` and provides SharePoint-aware dependency injection capabilities. The implementation ```SharePointServiceLocator``` will scan the GAC using the ```AppName``` as a pattern to match assembly file names (as a convention). 

Of course, Dynamite's own type registration module will be loaded earlier during bootstrapping (note that this allows you to override [Dynamite's own type registrations](https://github.com/GSoft-SharePoint/Dynamite/blob/develop/Source/GSoft.Dynamite/ServiceLocator/AutofacDynamiteRegistrationModule.cs) with your own alternate implementations - effectively replacing Dynamite's default implementations with your own across the Container's domain).




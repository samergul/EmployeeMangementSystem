So you've decided to go the modular route and you're ready to set up your ServiceLocator project as outlined [here](https://github.com/GSoft-SharePoint/Dynamite/wiki/How-to-break-up-your-solution-in-many-Visual-Studio-projects-with-their-own-responsibilities#all-aboard-the-modular-train)? Let's do this.

Install the GSoft.Dynamite NuGet package in your empty Company.Project.ServiceLocator.dll project (as a matter of convention, it is important that your assembly file name respects the pattern "*.ServiceLocator.dll").

## Core of the ServiceLocator: Dynamite makes it easy with GAC assembly scanning magic

Add a static class to your project, name it ProjectContainer. Start by populating it like this:

```
// The Container that is used by WSPs for dependency injection across 
// all Company.Project.*.WSP projects
public static class ProjectContainer
{
    // The key that distinguishes your container from others in the same AppDomain
    private const string AppName = "Company.Project";

    // The locator will scan the GAC and load the following Autofac registration modules:
    // - the core Dynamite utilities registration module
    // - all registration modules from assemblies with a filename that starts with "Company.Project"
    private static ISharePointServiceLocator locatorInstance = new SharePointServiceLocator(AppName);
}
```

This is the core of the service locator/container. The interface ```ISharePointServiceLocator``` comes from the namespace ```GSoft.Dynamite.ServiceLocator``` and provides SharePoint-aware dependency injection capabilities. The implementation ```SharePointServiceLocator``` will scan the GAC using the ```AppName``` as a pattern to match assembly file names (as a convention). 

Of course, Dynamite's own type registration module will be loaded earlier during bootstrapping (note that this allows you to override [Dynamite's own type registrations](https://github.com/GSoft-SharePoint/Dynamite/blob/develop/Source/GSoft.Dynamite/ServiceLocator/AutofacDynamiteRegistrationModule.cs) with your own alternate implementations - effectively replacing Dynamite's default implementations with your own across the Container's domain).

##Exposing the service location on your container
Once you have the core of your service locator, you can expose the service location methods of its ```innerLocator``` through simple delegation. The comments were copied from the ```SharePointServiceLocator``` implementation for quick reference:

```
// The Container that is used by WSPs for dependency injection across 
// all Company.Project.*.WSP projects
public static class ProjectContainer
{
    // The key that distinguishes your container from others in the same AppDomain
    private const string AppName = "Company.Project";

    // The locator will scan the GAC and load the following Autofac registration modules:
    // - the core Dynamite utilities registration module
    // - all registration modules from assemblies with a filename that starts with "Company.Project"
    private static ISharePointServiceLocator locatorInstance = new SharePointServiceLocator(AppName);

    /// <summary>
    /// Exposes the most-nested currently available lifetime scope.
    /// In an HTTP-request context, will return a shared per-request
    /// scope (allowing you to inject InstancePerSite, InstancePerWeb
    /// and InstancePerRequest-registered objects).
    /// Outside an HTTP-request context, will return the root application
    /// container itself (preventing you from injecting InstancePerSite,
    /// InstancePerWeb or InstancePerRequest objects).
    /// Do not dispose this scope, as it will be reused by others.
    /// </summary>
    public static ILifetimeScope Current
    {
        get
        {
            return innerLocator.Current;
        }
    }

    /// <summary>
    /// Creates a new child lifetime scope that is as nested as possible,
    /// depending on the scope of the specified feature.
    /// In a SPSite or SPWeb-scoped feature context, will return a web-specific
    /// lifetime scope (allowing you to inject InstancePerSite and InstancePerWeb
    /// objects).
    /// In a SPFarm or SPWebApplication feature context, will return a child
    /// container of the root application container (preventing you from injecting
    /// InstancePerSite, InstancePerWeb or InstancePerRequest objects).
    /// Please dispose this lifetime scope when done (E.G. call this method from
    /// a using block).
    /// Prefer usage of this method versus resolving manually from the Current property.
    /// </summary>
    /// <param name="feature">The current feature that is requesting a child lifetime scope</param>
    /// <returns>A new child lifetime scope which should be disposed by the caller.</returns>
    public static ILifetimeScope BeginFeatureLifetimeScope(SPFeature feature)
    {
        return innerLocator.BeginFeatureLifetimeScope(feature);
    }

    /// <summary>
    /// Creates a new child lifetime scope under the scope of the specified web
    /// (allowing you to inject InstancePerSite and InstancePerWeb objects).
    /// Please dispose this lifetime scope when done (E.G. call this method from
    /// a using block).
    /// Prefer usage of this method versus resolving manually from the Current property.
    /// </summary>
    /// <param name="feature">The current web from which we are requesting a child lifetime scope</param>
    /// <returns>A new child lifetime scope which should be disposed by the caller.</returns>
    public static ILifetimeScope BeginWebLifetimeScope(SPWeb web)
    {
        return innerLocator.BeginWebLifetimeScope(web);
    }
}
```
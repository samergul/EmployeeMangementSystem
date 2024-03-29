If you want to be able to access your modules' Services through your Service Locator, you'll need to provide an Autofac registration module.

### First: make sure your assembly will be automatically scanned by the ServiceLocator

Your assembly file name should respect the pattern of the AppName that was fed into the SharePointServiceLocator. So, for example, if the AppName is "Company.Project", then only registration modules that belong to DLL that has a filename that follows the pattern "Company.Project*.dll".

Make sure that your Module's WSP project is configured to deploy the Core.dll to the GAC, of course.

### Second: Put your RegistrationModule in your Core DLL

Register your types using a class that derives from the Autofac ```Module``` class.

```
using Autofac;

using IFC.IntactNet.Navigation.Core.Repositories;
using IFC.IntactNet.Navigation.Core.Services;
using IFC.IntactNet.Navigation.Contracts.Repositories;
using IFC.IntactNet.Navigation.Contracts.Services;

namespace Company.Project.Navigation.Core.RegistrationModules
{

    /// <summary>
    /// The navigation registration module
    /// </summary>
    public class NavigationRegistrationModule : Module
    {
        /// <summary>
        /// Loads the specified builder.
        /// </summary>
        /// <param name="builder">The builder.</param>
        protected override void Load(ContainerBuilder builder)
        {
            // Repositories
            builder.RegisterType<AdminQuickLinkRepository>().As<IAdminQuickLinkRepository>();

            // Services
            builder.RegisterType<QuickLinkService>().As<IQuickLinkService>();

            // Breadcrumb
            builder.RegisterType<SectorPageRepository>().As<ISectorPageRepository>();
            builder.RegisterType<BreadcrumbService>().As<IBreadcrumbService>();
        }
    }
}
```

From then on, using ```ProjectContainer.Current.Resolve<IQuickLinkService>()``` from any WSP will work. Also, other modules' Core libraries will now be able to depend on ```Company.Project.Navigation.Contracts.dll``` and add ```IQuickLinkService``` to their Services' constructor's dependencies. In effect, we enable easy cross-module collaboration through the service locator. 

### Third: understand lifetimes and transient-by-default

When you register an interface and its implementation using the default syntax:

```
builder.RegisterType<QuickLinkService>().As<IQuickLinkService>();
```

What you are saying to Autofac is

> Every time someone tries to ```.Resolve``` a ```IQuickLinkService```, please return a new instance of ```QuickLinkService```

i.e. Autofac will call ```new QuickLinkService(...)``` everytime the dependency injection tree requests a ```IQuickLinkService```.

This behavior is called *transient lifetime*. These objects will typically will not last long and be garbage collected rather fast: they don't live long, they are transient.

### Fourth: explore alternative lifetimes

There are alternative options to configure the lifetime of your objects. You may have legitimate reasons to store state in some Services.

One typical alternate lifetime is the Singleton:

```
builder.RegisterType<QuickLinkService>().As<IQuickLinkService>().SingleInstance();
```

As the Autofac syntax makes clear, Autofac will only create one instance of ```QuickLinkService``` and reuse the same instance whenever someone resolves a ```IQuickLinkService```.

Of course, this means you need to start to be careful with concurrency issues: for example, two concurrent HttpRequests can potentially update a singleton's state at the same time (leading to synchronization issues).

Thus, Autofac registration modules give us hooks to control how long our objects will last.

Dynamite provides you with more alternate lifetime scope that you can read about in [Controlling the lifetime of your objects through Dynamite's custom lifetime scopes](https://github.com/GSoft-SharePoint/Dynamite/wiki/Controlling-the-lifetime-of-your-objects-through-Dynamite's-custom-lifetime-scopes).
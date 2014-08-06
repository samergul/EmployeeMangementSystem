If you want to be able to access your modules' Services through your Service Locator, you'll need to provide an Autofac registration module.

### First: make sure your assembly will be automatically scanned by the ServiceLocator

Your assembly file name should respect the pattern of the AppName that was fed into the SharePointServiceLocator. So, for example, if the AppName is "Company.Project", then only registration modules that belong to DLL that hasa filename that follows the pattern "Company.Project*.dll".

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

### Third: understand lifetimes and transient-by-default



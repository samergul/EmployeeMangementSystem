Everyone I know has a gripe with the SharePoint developer tools in Visual Studio. They've been getting much better through the years, yet sometimes the nature of GAC and WSP deployments makes things that should be simple - like hooking up a debugger to your solution - sometimes much more frustrating than they should be.

Here are a collection of recommended patterns to avoid having your life completely ruined by the travails of SharePoint full-trust solution development.

## Use a dedicated deployment site collection as target for your VS deployments

You should *never* trust Visual Studio's "magic" deployment capabilities (like deleting lists that conflict with waht you're about to redeploy). If you start relying on these deployment tricks, when comes the time to ship your project in a real testing and prod environment, you won't be able to reproduce your typical, Visual Studio-powered provisioning sequence.

Thus, all your SharePoint projects in Visual Studio should target a site for deployments that:

1. Is a *different* site collection than the one your will actually test on. This is a dedicated deployment site collection. I put mine under ```/sites/deploy```.
2. Resides on the same web application as the site collection you will actually test on.

So, when you use the Visual Studio *Deploy* functionality, all that happens on out real test site (which may reside under ```/``` or ```/sites/project-test``) is that the assemblies in the GAC are updated and that the IIS application pools has been recycled.

## Automate your test site initialization process

It's up to you to maintain a suite of PowerShell scripts that 

1. (Re)deploy all the WSPs you need to your Farm
2. Initializes service application (like the term store)
3. (Re)create your site collections and subsites
4. Activates the site and web features in the right sequence
5. Furnish the test site with test data so that search can be configured automatically
6. Trigger search crawls to complete a completely automated site configuration sequence.

With such a repeatable installation process, you'll be ready when the time comes to deploy this to a live multi-server SharePoint environment.

Such automation also makes developer onboarding much faster (since the developer's environment setup is automated as much as possible).

## Turn off Active Deployment Configurations and other VS magic

The default settings for many SharePoint components can also lead to misleading deployment errors.

On all you SharePoint-type Visual Studio projects, in the Properties panel, set the ```Active Deployment Configuration``` to ```No Activation```.

On all your Features, in the Properties (open Feature designer, press F4, look in Properties view):

* Set ```Activate On Default``` to ```False```
* Set ```Always Force Install``` to ```True```

## When in doubt, retract everything

There are a few occasions when it's a good idea to Retract All your WSP packages from your SharePoint environment:

* Before packaging/publishing new WSP packages
    * if DLLs are still in the GAC while packaging, unexpected results may occur
* Before adding or removing references to assemblies
    * playing with NuGet packages or with the Add Reference... dialog with DLLs still in the GAC can lead to issues


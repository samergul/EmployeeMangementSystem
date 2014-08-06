## Do: always begin injection from a child lifetime scopes

Lifetime scopes are Autofac's concept of "child containers", or nested injection contexts. From any ```ILifetimeScope``` you can call ```.Resolve<MyInterfaceType>()``` and begin a new child lifetime scope.

Since Autofac is in charge of creating all our objects, we want to make sure that any ```IDisposable``` that could potentially leak memory gets rightfully garbage collected. Resolving an ```IDisposable``` instance repeatedly from the Root container (i.e. the root lifetime scope) would cause a memory leak in no time.

This is why it's good hygiene to also begin dependency resolution from a child scope. For example, in some user control code-behind:

```
protected void Page_Load(object sender, EventArgs e)
{
    using(var scope = ProjectContainer.Current.BeginLifetimeScope())
    {
        var log = scope.Resolve<ILogger>();
        log.Warn("Tss-BOOM!");
    }
}
```

While ```ProjectContainer.Current``` will always give you the most nested lifetime scope available - see [Controlling the lifetime of your objects through Dynamite's custom lifetime scopes](https://github.com/GSoft-SharePoint/Dynamite/wiki/Controlling-the-lifetime-of-your-objects-through-Dynamite's-custom-lifetime-scopes) for a discussion of the hierarchy of custom SharePoint lifetime scopes that Dynamite provides) -, you may still want to be carefull and use ```BeginLifetimeScope()```.

Or during the activation of a feature:

```
public override void FeatureActivated(SPFeatureReceiverProperties properties)
{
    using(var scope = ProjectContainer.BeginFeatureLifetimeScope(properties.Feature))
    {
        var log = scope.Resolve<ILogger>();
        log.Warn("Tss-BOOM!");
    }
}
```

## Don't: add a reference to your ServiceLocator project in your Contracts or Core module projects

The graph of inter-project dependencies should be this:

![module-dependencies](http://i.imgur.com/GbXOGnj.png)

Never use a container from within your Core class library. Dependencies of its Services are supposed to be injected through their constructor. Their types [must be registered in the Core's registration module](How to provide your own reusable services through an Autofac registration module) (and those registration modules will be automatically scanned by the service locator if properly deployed to the GAC - and if their Assembly's name matches ).

The container is meant to be used *only at the entry-points of your application*, to trigger the constructor injection hierarchy as maintained by Autofac.

Thus, as shown above, you should only use a Container within:

* in code-behind of ASP.NET user controls or pages
* in feature event receivers
* at the top of an SPCmdlet's processing
* in an item event receiver
* in a Timer Job's execution

Those qualify as entry points to your application. You should keep logic within them to a minimum. Use your container to resolve an Expert object provided by your module's Contracts and Core so that you can delegate any heavy lifting that needs to be done. If you keep such business logic within your Core as much as possible, it will be much easier to unit test your components.
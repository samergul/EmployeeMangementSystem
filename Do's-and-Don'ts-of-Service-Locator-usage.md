## Do: always begin injection from a child lifetime scopes

Lifetime scopes are Autofac's concept of "child containers", or nested injection contexts. From any ```ILifetimeScope``` you can call ```.Resolve<MyInterfaceType>()``` and begin a new child lifetime scope.

Since Autofac is in charge of creating all our objects, we want to make sure that any ```IDisposable``` that could potentially leak memory gets rightfully garbage collected. Resolving an ```IDisposable``` instance repeatedly from the Root container (i.e. the root lifetime scope) would cause a memory leak in no time.

This is why it's good hygiene to also begin dependency resolution from a child scope. For example:

```
```

## Don't: add a reference to your ServiceLocator project in your Contracts or Core module projects


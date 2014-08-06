Please refer to [How to provide your own reusable services through an Autofac registration module](https://github.com/GSoft-SharePoint/Dynamite/wiki/How-to-provide-your-own-reusable-services-through-an-Autofac-registration-module)

If you use the ```SharePointServiceLocator``` from Dynamite as a container, you gain access to Dynamite's custom, SharePoint-specific lifetimes:

* ```InstancePerSite```
    * if you want a "singleton per SPSite" behavior - useful to hold state at the site collection level
* ```InstancePerWeb```
    * similar, but "singleton per SPWeb" behavior
* ```InstancePerRequest``` 
    * the instance will be reused, but only to the time of a single HttpRequest
    * this is a typical use-case in web applications (reuse a DBConnection object to flush it only once at the end of the web request, reuse the result of a large CAML query to display the same content in many web parts in the same page, etc.)
    * to enable this option, you need to activate the Web Application-scope feature "GSoft.Dynamite.SP_Web Config Modifications" - ```InstancePerRequest``` requires the deployment of a HttpModule to the web.config. 

### How to use Dynamite's custom lifetimes

Simply end you type registrations with the lifetime you want:

```
builder.RegisterType<QuickLinkService>().As<IQuickLinkService>().InstancePerSite();
```

As always, since you're declaring shared objects through ```InstancePerSite()``` and ```InstancePerWeb()``, you need to be aware of concurrency issues.

### The hierarchy of lifetime scopes

It is critical to understand that objects which has a per-site, per-web or per-request lifetime can **never** be resolved from a root application container.

To enable the site, web and request lifetimes, Dynamite's ```SharePointServiceLocator``` maintains a hierarchy of lifetime scopes (a hierarchy of parent and child containers, if you will).

It's throgh those site-specific, web-specific and request-specife lifetime scopes that objects with the custom lifetimes (```InstancePerSite```, ```InstancePerWeb``` and ```InstancePerRequest```) will be resolved and their state shared for a longer-than-transient lifetime.

The parent-child hierarchy of lifetime scopes maintained by Dynamite looks like this:

![lifetime-scope-hierarchy](http://i.imgur.com/H4W7zag.png)



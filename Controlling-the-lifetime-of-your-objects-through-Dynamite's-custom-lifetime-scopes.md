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



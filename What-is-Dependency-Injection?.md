##Basic principles

When building software components one should always strive for:

* Reusability
    * you want to avoid repeating yourself as much as possible (DRY)
    * copypasta (copy/pasted code) leads to un-maintainable messes
* Low coupling
    * the impact of any change should be minimal
    * thereby, a component shouldn't depend on a large number of other components
* High cohesion
    * each object should be assigned highly-focused responsibilities
    * highly cohesive components are easier to manage and understand

See [Larman 2004](http://en.wikipedia.org/wiki/GRASP_(object-oriented_design)) for more on responsibility assignment patterns and practices.

##Story time

Once upon a time, Jimmy coded up a nice little static utliity class to help him get the job done:

```
public static class MyStoryConfig 
{
    public static string BookTitle 
    {
        get { return "My Story's Great Title" }
    }
}
```

Jimmy thought "hey, this is great, I won't be copy-pasting ```"My Story's Great Title"```` all over the place anymore." An use that static utility he did, in some factory method, for example:

```
public Book GetFullBookInformationForMyStory()
{
    var fullBookExcerpt = MyStoryConfig.BookTitle + " - Just read it, it's great!"
    return new Book(MyStoryConfig.BookTitle, fullBookExcerpt);
}  
```

But then someday Jimmy figured out that he had it wrong all along and that his system should support more than just one story (not just his! - how nacissistic of him), so he went about and changed his method signature to this:

```
public Book GetFullBookInformationForStory(StoryConfig currentStoryConfig)
```

But then Jimmy realized that he couldn't do ```new MyStoryConfig``` and simply rename it to ```StoryConfig``` because he'd built the whole thing as a static in the first place. 

So Jimmy was sad for a bit, but eventually he fixed it.

===============================

Once upon a time, Jimmy thought "hey, I've got all this book binding logic polluting my nice ```Story``` class and making things harder to understand, so I'd better  put all that stuff in a separate class". And refactor-out a ```BookBindingExpert``` he did:

```
public class Story
{
    // Encapsulates all binding logic. 
    // E.g. use bbe.CalculateCostPerPage(this.book.NumberOfPages)
    private BookBindingExpert bbe = new BookBindingExpert();
    
    // Here follows my nice Story logic
    // ...
}
```

But then someday, Jimmy realize that each story could potentially be printed at a different printers business, meaning that he shouldn't have just one type of ```BookBindingExpert```, but rather a ```PrinterBusinessXExpert``` and an alternative ```PrinterBusinessXExpert```, both implementors of a new ```IBookBindingExpert```. 

Jimmy saw that the line ```new BookBindingExpert()``` needed to be changed in the ```Story``` class, so he thought: "that's ok, I'll just pass the right ```IBookBindingExpert``` into my ```Story``` constructor." So he changed the constructor to this:

```
public Story(string title, string summary, IBookBindingExpert bookExpert)
```

But then Jimmy discovered that the old ```new Story(title, summary)``` constructor was used in 35 different places in his project and that compilation was broken everywhere.

So Jimmy was sad for a bit, but eventually he fixed it.


##Lessons learned
Jimmy was just following his best intentions, but still he felt frustrated. Jimmy's (contrived) examples lead us to two easy (and slightly oversimplified) conclusions:

* using the ```static``` keyword is often a trap
    * in the case of static classes, you may want to instantiate these de-facto singletons more than once at some point eventually
* using the ```new``` keyword often shouldn't be your responsibility
    * you shouldn't be the one responsible for instantiating your collaborating expert objects


##Services vs. "Newables"
In the grand scheme of things, most OO classes will fall into one of two categories:

* Services
    * Expert objects that encapsulates some logic
    * Services collaborate with other services to provide encapsulation for new behavior
    * Think: Controllers, Business logic experts, Repositories, Utilities, etc.
    * Services should implement an interface to emphasize the "contract" the service will respect and to allow for alternate implementations down the road (for example, the ```IBookBindingExpert``` above and its many implementations).
    * Services should only depend on the interfaces of their collaborating services, to keep them decoupled from other expert implementation. Consequently, a service should never be responsible for instantiating one of its collaborators.
* "Newables"
    * Classes that you can call ```new``` on without worry
    * Objects that encapsulate some data (passed in through the constructor)
    * Think: Business Entities, Data Transfer Objects (DTO), View Models, Configuration data, etc. 
    * Newables shouldn't have field references to any Service-type class, and ideally they should let other Service-type objects take care of implementing their dynamic behavior.

##Dependency Injection

Dependency Injection is a pattern for implementing the "collaboration of services" idea above. One form of dependency injection is called Constructor Injection and it goes like this:

```
public class SomeSmartService : ISomeSmartService
{
    private IOtherServiceX x;
    private IOtherServiceY y;
    private ILogger log;

    public SomeSmartService(IOtherServiceX x, IOtherServiceY y, ILogger log)
    {
        this.xService = x;
        this.yService = y;
        this.log = log;
    }

    public SmartResult DoSmartStuff(SomeMethodInjectedContext ctx, bool someOption)
    {
        this.log.Info("Smart stuff starting");
        var partX = this.xService.GetResult(ctx, someOption);
        var partY = this.yService.GetResult(ctx, someOption);
        return new SmartResult(partX, partY);
    }
}
```

The main rules are these:

* Your Services should have only a single constructor
* Your references to your collaborators/dependencies should be kept private
    * it's not one else's business to know what set of collaborators a particular implementation uses

One big advantage of constructor injection (over the alternative: Property Injection through class property setters) is this: with a single glance at the constructor you can determine:

a) all the dependencies of your class (i.e. all your collaborators) - and infer from them roughly what the responsibilities of your class is

b) whether your class is getting bloated by having too many dependencies injected through its constructor (after more than 5 or 6 collaborators, things can get [smelly](http://en.wikipedia.org/wiki/Code_smell) fast).

Another benefit of constructor injection is that it makes unit testing of your classes very easy (since all the dependencies of the object-under-test can be easily injected through the constructor):

```
// Arrange
var fakeX = new DummyServiceXImplementation();    // implements IOtherServiceX
var fakeY = new DummyServiceYImplementation();    // implements IOtherServiceY
var fakeLog = MyFavorityMockingLibrary.MockInstance<ILogger>();

var objectUnderTest = new SomeSmartService(fakeX, fakeY, fakeLog);

// Act
var result objectUnderTest.DoSmartStuff(null, true);

// Assert
Assert.IsTrue(result.IsSmart);
```

##Inversion of Control Containers

[Inversion of Control](http://martinfowler.com/articles/injection.html#InversionOfControl) in the context of Dependency Injection relates to how the tree-of-collaborating-objects is wired up and configured through a Container object.

The Container holds all the "configuration" information about which class implements which interface. We call these "type registrations", where you link you Service interfaces to the specific implementations you wish to be injected when the interface is requested:

```
containerBuilder.RegisterType<MyVerySmartService>().As<ISmartService>();
containerBuilder.RegisterType<CustomULSTraceLogger>().As<ILogger>();

var container = new Container(containerBuilder);
```

This configuration step is often called the "bootstrapping" stage of dependency injection. Once configured, the container becomes responsible for creating **all** Service-type objects. In effect, it becomes the "factory" for all your registered types. The container exposes this "mega-factory" functionality through a pattern called Service Location.

##Service Location
The process of dependency injection starts when, in your application code, you make a *type resolution request* to the container by giving it the interface you want to call:

```
// because of above configuration, this will return an instance of type MyVerySmartService
var smartServiceInstance = container.Resolve<ISmartService>()

smartServiceInstance.DoSmartStuff(...);
```

When you call ```.Resolve<ISmartService>()``` on the container, it looks up which implementation is registered (```MyVerySmartService```). It then reflects on the constructor of ```MyVerySmartService``` to determine what *other* dependencies it needs to look up and create before it can create the ```MyVerySmartService``` instance. 

For example, the constructor of may look like ```public MyVerySmartInstance(ILogger log)``` and thus require an instance of ```ILogger```. Once the associated implementation ```CustomULSTraceLogger``` has been looked up, the custom logger constructor may depend itself on another interface, and so on.  This goes on recursively down the chain of constructors. 

Thus, no matter how deep the chain of constructor dependencies go, the container will eventually return a ```MyVerySmaryService``` with its dependencies fully initialized all the way down the chain.

 
In short, you "locate" your Services through the ```container.Resolve``` method. This is the Service Location pattern.

To be more precise about the pattern names:
* A "pure" Service Locator class will not allow you to register additional types,
* while the Container pattern allows you to change the type registrations.

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
public static MyStoryConfig 
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
public Story
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



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

See (Larman \[2004\])[http://en.wikipedia.org/wiki/GRASP_(object-oriented_design)] for more on responsibility assignment patterns and practices.

##Story time

Once upon a time Jimmy coded up a nice little static singleton to help him get the job done:

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

But then someday Jimmy figured out that he had it wrong all along and that his system should support more than just one story (not just his!), so he went about and changed his method signature to this:

```
public Book GetFullBookInformationForStory(Story currentStory)
```



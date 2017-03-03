Published: 03-01-17
Title: C Sharp general Conventions
PromoImageUrl: http://www.hindscc.edu/Assets/images/careertech-hinds-computer-programmer-code.jpg
Category: csharp
Intro: A few quick and easy rules pulled from technical documentation I once wrote
---

I love to mentor people looking to learn a bit of programming. Whenever I work with a new person, it doesn't take them long to ask "Why does your code look so different than mine? Why does mine break and yours doesn't? Aren't they the same?"

The biggest difference I've noticed is just general formatting issues - large methods, poor formatting, and unhelpful comments lead to simple bugs that could have been avoided just with a little extra effort. In an effort to help combat that, I've decided to pull out some of the guidelines I wrote for a large Software project and make them easy to consume in one place. Without further ado, here they are!

## Method Sizing

The intent of each method should be describable in one sentence without using the word "and". If a method has multiple steps before it returns, each of those steps should be pulled into a method. 

As a general rule-of-thumb, methods should be no longer than 15 lines long.


> Bad:
```cs
// Returns a list of all results and organizes them alphabetically
GetOrganizedResults()
{
  // Get Results
  // Organize List
  // Return
}
```



> Good:
```cs
// Returns a list of all results
GetResults() 
{
  // Return Results
}
// Organizes a list of results alphabetically
OrganizeAlphabetically()
{
  // Organize List
}
```


## Formatting

**DO:** Try to use one class per file; Consider nested classes carefully.

<br>
**DO:** Declare each variable independently, not in the same statement.
> Good:
```cs
var magicNumber = 42;
var counter = 1;
var minimum = 100;
```

> Bad:
```cs
var magicNumber = 42, counter = 1, minumum = 100;
```

<br>
**DO:** Always use curly braces for conditional statements.
> Good:
```cs
if (theAnswer == 42)
{
	DoSomething();
}
else
{
	DoSomethingElse();
}
```

> Bad:
```cs
if (theAnswer == 42)
	DoSomething();
else
	DoSomethingElse();
```

**DO:** Use C#'s overloaded `==` operators for string comparisons instead of `string.Equals()`
> Good:
```cs
if (message == "Hello")
{
	DoSomething();
}
```
> Bad:
```cs
if (message.Equals("Hello"))
{
	DoSomething();
}
```

**DO:** Keep basic Get / Set Properties on one line
> Good:
>
> ```cs
> public String Name { get; set; }
> ```
>
> Bad:
```cs
public String Name
{
	get;
	set;
}
```

<br>
**DO:** Favor Method Properties over Getter and Setter methods for basic values
> Good:
```cs
public String Name { get; set; }
```

>Good:
```cs
public int Total
{
	get { return 5 + 5; }
}
```

> Bad:
```cs
private _name = string.Empty;
public string GetName()
{
	return _name;
}
public void SetName(string toSet)
{
	_name = toSet;
}
```


## Commenting

**DON'T:** Leave code blocks commented out if they are old / unused. The Git repository maintains a record of the old version of the file.


**DO:** Strive to write self-documenting code that is human friendly and readable on its own.


**DO:** Use comments to explain the logic flow of complicated algorithms or to document external business rules.


**Do:** Use comments to explain the WHY not the WHAT.


**DO:** Use the Summary `///` commenting system for methods accessible in other classes.


**DON'T:** Write unnecessary or redundant comments.
> Bad:
```cs
// This is the maximum number of words
private const var MaxiumumWords = 500;
```
> Good:
```cs
// The external system will only accept blog posts under 500 words
private const var MaxiumumWords = 500;
```

**DO:** Use comments for task list keyword filtering.
```cs
// TODO: Some task that must be done, such as fully implementing a method
// SHOULDDO: Some task that should be done, such as refactoring a method
// HACK: Indicates code that is a workaround and should possibly be reapproached in the future.
```
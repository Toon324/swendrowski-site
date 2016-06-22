Published: 06-19-16
Title: Method Sizing in C Sharp
PromoImageUrl: http://www.hindscc.edu/Assets/images/careertech-hinds-computer-programmer-code.jpg
Category: csharp
Intro: Thoughts on why big methods make me sad
---
The intent of each method should be describable in one sentence without using the word "and". If a method has multiple steps before it returns, each of those steps should be pulled into a method. 

As a general rule-of-thumb, methods should be no longer than 15 lines long.


> Bad:
```cs
using System.Web;

/// <summary>
/// Summary description for Class1
/// </summary>
public class Class1
{
	public Class1()
	{
		//
		// TODO: Add constructor logic here
		//
        var names = new List<string>() { "John", "Tom", "Peter" };
        foreach (string name in names)
        {
            Console.WriteLine(name);
        }
	}
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
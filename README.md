# C# Coding Guidelines & Practices

This repo highlights the list of software engineering guidelines in general. Most of these are industry-wide conventions, thus using them will ensure that your code is easily readable by people who are not you.

Just keep in mind that this post isn't about how you should indent your code, but it's more of a guidlines on how to write clean code that are easy to manage. With that said, if you are leading a team or even a single contributor developer who wanted to become better, having a set of coding guidelines is a great start to make that happen.

Also, feel free to add your own tips through pull requests.

### Try to avoid boxing like in the following:
```
int x = 32;
object o = x;
```
Boxing is the process of wrapping a value type such as a primitive or struct inside an object that lives on the heap. Unboxing is gettting the orginal value back out again.

👍 Map to matching primitive or struct variable instead:

```
int x = 32;
int y = x;
```


### Try to avoid the traditional if-else statements like in the following:

```
int statusCode;
if (condition)
{
   statusCode = 1;
}
else
{
   statusCode = 2;
}
```

👍 Do use ternary conditional operator (?:) instead:

```
int statusCode = condition ? 1 : 2;
```

The preceding code is much cleaner, easier to read and understand. On top of that, it's more concise.

### Try to avoid nested Ternary operators:

```
public string GetJobStatus(Job job)
{
  return job.IsRunning ? "Running" : job.HasErrors ? "Failed" : "Succeeded";
}
```

👍 Use another line to express the nested operation as a separate statement for readability:

```
if (job.IsRunning)
  {
    return "Running";
  }
return job.HasErrors ? "Failed" : "Succeeded";
```

Nesting ternary operators results in the kind of code that may seem clear as day when you write it, but six months later could leave maintainers (or future you) scratching their heads.

### Try to avoid using if statement for null checks like in the following:

```
if (something != null)
{
    if (something.Other != null)
    {
        return something.Other.Whatever;
    }
}
```

👍 Do use null conditional (?.) operator instead:

```
return something?.Other?.Whatever;
```

The preceding code is also much cleaner and concise.

### Try to avoid complex if-else statements for null checks like in the following:

```
if (something != null)
{
    if (something.other != null)
    {
        return something.other.whatever;
    }
    else
    {
        return string.empty;
    }
}
else
{
    return string.empty;
}
```

👍 Do use null coalescing (??) operator instead:

```
return something?.other?.whatever ?? string.empty;
```

### Try to avoid using the following code when returning a default value when an object is null:

```
int? number = null;
var n = number.HasValue ? number : 0;
```

👍 Do use null coalescing (??) operator as well:

```
var n = number ?? 0;
```

👍 or alternatively, you could do:

```
var n = number.GetValueOrDefault();
```

### Try to avoid using the equality operator (==) or HasValue for nullable variable check like in the following:

```
int? number = null;

if (number == null)
{
    //do something
}

if (!number.HasValue)
{
    //do something
}
```

👍 While the preceding code is fine, we can still improve that by using the is keyword like in the following:

```
int? number = null;

if (number is null)
{
    //do something
}
```

### Avoid code without braces ({}) for single conditional if statement, for and foreach loops like in the following:

```
if (condition) action;
```

Without the braces, it is too easy to accidentally add a second line thinking it is included in the if, when it isn’t.

👍 Always use braces instead:

```
if (condition) { action; }

//or better
if (condition)
{
    action;
}
```

###  Try to avoid using multiple if-else statements like in the following:

```
if (condition)
{
   //do something
}
else if (condition)
{
   //do something
}
else if (condition)
{
   //do something
}
else (condition)
{
   //do something else
}
```

👍 Do use switch statements instead:

```
switch (condition)
{
   case 1:
      //do something
      break;
   case 2:
      //do something
      break;
   case 3:
      //do something
      break;
   default:
      //do something else
     break;
}
```
👍 But prefer switch expressions over switch statements where possible like in the following:

```
condition switch
{
    1 => //do something;
    2 => //do something;
    3 => //do something;
    _ => //do something else;
}
```

The preceding code is more concise yet, still easy to read and understand. (Note, only available in C# 8 or later versions) 💡 Exceptions - There are cases that if-else statements would make more sense than using switch statements. One such example could be, if the condition involves different objects and complex conditions, though if the conditional logic here boils down to checking whether an object matches a certain shape in terms of property values, you might want to explore recursive pattern matching.

### Do use the using statement when working with objects that eat resources or implements IDisposable interface:

```
using (MemoryStream stream = new MemoryStream())
{
    // do something
}
```

👍 Or prefer to use the new using declaration introduced in C# 8 like in the following:

```
using var stream = new MemoryStream();
// do something
```

The preceding code reduces the number of curly braces in your method, but it can still be seen easily where a resource is disposed. For more information, see: "pattern-based using" and "using declarations".

⚠ Do not use either of the above if the IDisposable is the return value of your method:

```
Stream GetABrokenFileStream()
{
    // this is wrong! The stream will be disposed when you exit the method
    using var fileStream = File.OpenRead("path to my file");
    return fileStream;
}
```

### Avoid concatenating strings with the + sign/symbol like in the following:

```
string space = "Vianne";
string greetings = "Hello " + name + "!";
```

👍 Use string.Format() method instead:

```
string name = "Vynn";
string greetings = string.Format("Hello {0}!", name);
```

👍 Or prefer using string interpolation ($) instead where possible:

```
string name = "Vjor";
string greeting = $"Hello, {name}!;
```

The preceding code is much more concise and readable compared to other approaches.

### Try to avoid string.Format() when formatting simple objects like in the following:

```
var date = DateTime.Now;
string greetings = string.Format("Today is {0}, the time is {1:HH:mm} now.", date.DayOfWeek, date);
```

👍 Use string interpolation instead:

```
var date = DateTime.Now;
string greetings = $"Today is {date.DayOfWeek}, the time is {date:HH:mm} now.");
```

The preceding code is much easier to understand and concise. However, there are certain cases that using the string.Format() would makes more sense. For example, when dealing with complex formatting and data manipulation. So, use your judgement when to apply them in situations.

### Avoid using specific type for complex objects when defining variables like in the following:

```
List<Repository.DataAccessLayer.Whatever> listOfBlah = _repo.DataAccessLayer.GetWhatever();
```

👍 Use the var keyword instead:

```
var listOfBlah = _repo.DataAccessLayer.GetWhatever();
```

👍 Same goes for other local variables:

```
var students = new List<Students>();
var memoryStream = new MemoryStream();
var dateUntilProgramExpiry = DateTime.Now;
```

### Try to avoid one-liner method implementation with curly braces like in the following:

```
public string Greeter(string name)
{
    return $"Hello {name}!";
}
```

👍 Do use Expression-bodied (=>) implementation instead:

```
public string Greeter(string name) => $"Hello {name}!";
```

The preceding code is more concise while maintaining readability.

### Avoid object initialization like in the following:

```
Person person = new Person();
person.FirstName = "Vianne";
person.LastName = "Durano";
```

👍 Do use object and collection initializers instead:

```
var person = new Person
{
    FirstName = "Vianne",
    LastName = "Durano"
};
```

The preceding code is more natural to read and the intent is clear because the properties are defined within braces.

### Avoid creating a class just to return two simple result sets like in the following:

```
public Person GetName()
{
    var person = new Person
    {
        FirstName = "Vincent",
        LastName = "Durano"
    };

    return person;
}
```

👍 Do use Tuples instead where possible:

```
public (string FirstName, string LastName) GetName()
{
    return ("Vincent", "Durano");
}
```

The preceding code is more convenient for accessing objects and manipulating the data set. Tuples replaces the need to create a new class whose sole purpose is to carry around data.

### Try to create an Extention Methods to perform common tasks such as conversion, validation, formatting, parsing, transformation, you name it. So, instead of doing the following:

```
string dateString = "40/1001/2021";
var isDateValid = DateTime.TryParse(dateString, out var date);
```

The preceding code is perfectly fine and should handle the conversion safely. However, the code is bit lengthy just to do basic conversion. Imagine you have tons of the same code conversion cluttering within the different areas in your project. Your code could turn into a mess or potentially causes you alot of development time overtime.

👍 To prevent that, you should consider creating a helper/utility functions to do common tasks that can be reused across projects. For example, the preceding code can now be converted to following extension:

```
public static class DateExtensions
{
     public static DateTime ToDateTime(this string value)
         => DateTime.TryParse(value, out var result) ? result : default;
}
```

and you will be able to use the extension method like in the following anywhere in your code:

```
var date = "40/1001/2021".ToDateTime();
```

The preceding code makes your code concise, easy to understand and provides convenience.

The preceding code removes alot of noise in your code when injecting dependencies as you don't need to write private readonly declarations which can make your code cleaner.

In situations where you want to expose one of the fields to be public, you can define and set it in the constructor as what you would normally do. Otherwise, the arguments are marked as private fields.

### Avoid using .NET predefined data types such as Int32, String, Boolean, etc.:

```
String firstName;
Int32 orderCount;
Boolean isCompleted;
```

👍 Do use built-in primitive data types instead:

```
string firstName;
int orderCount;
bool isCompleted;
```

The preceding code is consistent with the Microsoft’s .NET Framework and more natural to read.

### Do not use initials as identifier abbreviations like in the following:

```
private readonly PersonManager _pm;
```

The main reason for this is that it can cause confusion and inconsistency when you have class that might represents the same thing like in the following:

```
private readonly ProductManager _pm;
```

👍 Instead, do choose clarity ver brevity like in the following:

```
private readonly PersonManager _personManager;
private readonly ProductManager _productManager;
```

The preceding code provides more clarity as it clearly suggests what the object is about.

### Do organize namespaces with a clearly defined structure. Take a look at the following example:

```
namespace ProjectName.App.Web
namespace ProjectName.Services.Common
namespace ProjectName.Services.Api.Payment
namespace ProjectName.Services.Api.Ordering
namespace ProjectName.Services.Worker.Ordering
```

Generally namespaces should reflect the folder hierarchy within a project. The preceding code suggest good organization of your code within the project, allowing you to navigate between layers easily.

### Do use singular form, noun or noun phrases to name a class:

```
public class Person
{
    //some code
}

public class BusinessLocation
{
    //some code
}

public class DocumentCollection
{
    //some code
}
```

This enables you to easily determine if an object holds a single item value or collection. Imagine, if you have a List<People> vs List<Person>. It's just odd to put plural form names in a List or Collection.

### Do use nouns or adjective phrases for Property names as well. When naming boolean properties or variables, you may add the prefix "can", "is", "has", etc. just like in the following:

```
public bool IsActive { get; set; }
public bool CanDelete { get; set; }

//variables
bool hasActiveSessions = false;
bool doesItemExist = true;
```

Adding those suffixes will provide more value to the caller.

### Do use Pascal Casing for Class, Method, Property and Constant variable names:

```
public class ClassName
{
    const int MaxPageSize = 100;

    public string PropertyName { get; set; }

    public void MethodName()
    {
        //do something
    }
}
```

This is so that our code is consistent with the Microsoft .NET Framework.

### Do use Camel Casing for method arguments and local variables:

```
public void MethodName(CreatePersonRequestDto requestDto)
{
       var firstName = requestDto.FirstName;
}
```

This is so that our code are consistent with the Microsoft .NET Framework.

### Do use meaningful and self-explanatory names for classes, methods and properties:

```
int daysUntilProgramExpiry;

public Person GetPersonProfileById(long personId)
{
       //do something
}
```

This makes your code easier to read and understand without having you to write (or atleast minimizes) comments of what the code does.

### Do suffix asynchronous methods with the Async word:

```
public async Task<List<Person>> GetPersonProfileByIdAsync(long personId)
{
     //do something
}
```

This enable developers to easily identify synchronous vs asynchronous methods by just looking at the method name itself.

### Do prefix interfaces with the capital letter I

```
public interface IPersonManager
{
   //...
}
```

This is to easily distinguish between an interface and classes. In fact, it's a well known standard for defining interfaces.

### Do prefix global variables and class fields with underscores (\_):

```
private readonly ILogger<ClassName> _logger;
private long _rowsAffected;
private IEnumerable<Persons> _people;
```

This is to easily differentiate between local and global identifiers/variables.

### Do declare all member variables and fields at the top of a class, with static fields at the very top:

```
private static string _externalIdType;
private readonly ILogger<PersonManager> _logger;
private int _age;
```

This is just a generally accepted practice that prevents the need to hunt for variable declarations.

### Do consider putting all your private methods at the bottom after public methods:

```
public class SomeClass
{
    public void SomePublicMethodA()
    {

    }

    // rest of your public methods here

    private void SomePrivateMethodA()
    {

    }

    private void SomePrivateMethodB()
    {

    }
}
```

### Try to avoid grouping your code into regions like in the following:

```
#region Private Members
    private void SomePrivateMethodA()
    {

    }

    private void SomePrivateMethodB()
    {

    }

#endregion
```

The preceding code is a code smell which could potentially make your code grow without you realizing it. I admit that I have used this feature many times to collapse the code within a class. However, I realize that hiding code into regions won't give you any value aside from maximizing your visual view when the region is collapsed. If you are working with a team of developers on a project, chances are, other developers will append their code in there until the code get's bigger and bigger over time. As a good practice, it's always recommended to keep your classes small as possible.

If you have tons of private methods within a class, you could split them into a separate class instead.

### Try to use short-hand names only when they’re generally known:

```
private readonly CreateQuestionDefinitionRequestDto _requestDto;
```

It would be too much to name a variable "createQuestionDefinitionRequestDto" when you know that the variable/parameter is a request object. The same thing applies for FTP, UI, IO, etc. It's perfectly fine to use abbreviation for as long as they're generally known, otherwise it would be counter productive not to do so.

### Avoid underscores (\_) in between identifier names:

```
public PersonManager person_Manager;
private long rows_Affected;
private DateTime row_updated_date_time;
```

The reason being is that C# isn't postgres. Seriously, it's to be consistent with the Microsost .NET Framework convention and makes your code more natural to read. It can also avoid "underline stress" or inability to see underline.

### Do not use SCREAMING CAPS for constants or read-only variables:

```
public static const string EXTERNALIDTYPE = "ABC";
public static const string ENVIRONMENT_VARIABLE_NAME = "TEST";
```

They just grab too much attention.

### Do not use Hungarian notation or any other type identification in identifiers (except interfaces):

```
int iCounter;
string strName;
string spCreateUsers;
OrderingService svcOrdering;
```

Visual Studio code editor already provides helpful tooltips to determine object types. In general, you want to avoid type indicators in the identifier.

### Do not use an "Enum" suffix in enum type names and do not use plural form names for enums.

The following is an example for defining an enum:

```
public enum BeerType
{
  Lager,
  Ale,
  Ipa,
  Porter,
  Pilsner
}
```

Again, this is to be consistent with the Microsoft .NET framework and avoids type indicators in the identifier.

### Try to use record types for immutable objects. Record types is a new feature introduced in C# 9 where it simplfies your code. For example, the following code:

```
public class Person
{
    public string FirstName { get; init; }
    public string LastName { get; init; }

    public Person(string firstName, string lastName)
    {
        FirstName = firstName;
        LastName = lastName;
    }
}
```

can be written in the following way using record:

```
public record Person(string FirstName, string LastName);
```

Using record types will automatically generates the boilerplate code for you and keeping your code concise. Records will be really useful for defining DTOs, Commands or any object that carries immutable data around. For more information about this feature, see: [Record Types](https://devblogs.microsoft.com/dotnet/c-9-0-on-the-record/)

### Use Attributes on local functions
before c# 9.0 we used C# compiler directive on local functions

```
static void Main(string[] args)
        {
            static void DoAction()
            {
                // DoAction

                Console.WriteLine("DoAction...");
            }

#if DEBUG
            DoAction();
#endif
        }
```
in c# 9.0 can be written in the following way using Attributes on local functions:

```
static void Main(string[] args)
        {
            [Conditional("DEBUG")]
            static void DoAction()
            {
                // Do Action Here

                Console.WriteLine("Do Action...");
            }

            DoAction();
        }
```

### Arrange your code before commits
```
Visual Studio shortcut : CRTL+K+D
```

### Use local function for conditions
```
if (RequestIsValid(request.Id))
{

}

bool RequestIsValid(int id)
{
    return id > 0;
}
```

### Always remove unnecessary usings and sort them
```
Visual Studio shortcut : CRTL+R+G
```

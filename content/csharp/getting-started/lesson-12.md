# Defining and Calling Methods
by [Steve Smith](http://deviq.com/me/steve-smith)

#### Sample Files
Download a ZIP containing this tutorial's sample files:
- [Initial Version] - Use this as a starting point when following along with the tutorial yourself
- [Completed Version] - Includes the completed versions of all samples

## What are Methods?

So far in this tutorial, you've only been working within one method of your own, the ``Main`` method of a console application. However, you've called many other methods in the course of completing the lessons so far and the associated exercises. A method is a function that is associated with a class. A function is a named series of statements in a program. Functions (and therefore methods) can optionally accept parameters, and they can optionally return a result. In this lession, we'll generally refer to methods and functions interchangeably.

## Declaring Methods

There are two main kinds of methods in C#: *static* methods and *instance* methods. A static method is global to the program, and is called off of the type it is associated with. You learned about these kinds of methods in [lesson 2](lesson-02.md), since the ``Main`` method you've seen so much of is static. Instance methods are attached to an object instance. They can only be called if they are invoked from a non-null instance of an object of the appropriate type. Two objects of the same type will run the same code for a given instance method, but each method will have access to that object's internal state, so the results may be very different.

If you're writing a method that will only operate on the parameters being passed into it, you can typically declare it as a ``static`` method. Otherwise, your methods should be instance methods (which is the default if you don't add the ``static`` keyword).

```c#
static int Add(int operand1, int operand2)
{
    // only works with its own parameters; works well as static method
    return operand1 + operand2;
}
string FullName()
{
    // FirstName and LastName would be Properties of the class
    // this method appears on
    return $"{FirstName} {LastName}";
}
```

## Naming Methods

Of course, [naming things](http://deviq.com/naming-things/) is one of the hardest problems in computer science. It's worth putting a little thought into what you name methods, as they're one of the key building blocks you'll use to build larger software applications. Methods *do* things, so typically their names should include verbs that describe the action they perform. Methods should be small and focused. If you give the method a name that describes what it does, and the name includes "and", the method might be larger than it needs to be. Most of the time, methods should do one thing. However, if you do have methods that do multiple things, it's better to have a long, descriptive name than to give it a shorter, less descriptive method that doesn't cover everything it does. Your methods shouldn't surprise programmers who call them with unexpected behavior.

Frequently, you can create methods within your program by taking existing code (for instance, from your ``Main`` method), and moving it into a method, and replacing the original code with a call to the new method. This is especially important when you find that you're writing the same code in multiple places - you should try to follow the [Don't Repeat Yourself (DRY) principle](http://deviq.com/don-t-repeat-yourself/) in your code whenever you can. However, it can also be useful even for code that only exists in one place, as a way to help keep your code modular and easy to understand. If you have a part of your program that is getting long and complex, to the point where you want to start adding comments that describe what different sections are doing, it's almost always a better choice to move blocks of codes into their own methods, and give the methods good names that make it clear what's being done in them.

As an example, consider this long block of code, which could appear in the program's ``Main`` method:

```c#
// display header
Console.WriteLine("-----------------------------------------");
Console.WriteLine("**** My Super Program *******************");
Console.WriteLine("-----------------------------------------");

// display the menu
Console.WriteLine("What do you want to do?");
Console.WriteLine("1 - View the Menu");
Console.WriteLine("2 - Exit the Program");

// read command
string command = Console.ReadLine();
int menuOption = int.Parse(command);

switch(menuOption)
// additional code omitted
```

The above code could be greatly simplified by using methods:

```c#
DisplayHeader();
DisplayMenu();

switch(ReadCommand())
// additional code omitted
```

The first two methods do not return anything, and would simply contain the ``Console.WriteLine`` statements that previously were in the main method. The ``ReadCommand`` method would return an ``int``. Learn more about return types in the next section.

## Return Types

Methods are either declared as ``void``, meaning they don't return a result, or they must declare a type they will return. You can see two examples of simple methods below:

```c#
string CreateGreeting(string name)
{
    return $"Hi {name}!";
}

void DisplayGreeting()
{
    Console.WriteLine(CreateGreeting("Steve"));
}
```
The first method returns a ``string`` type; the second one simply performs some action, without returning anything.

Methods can only return a single result, which sometimes presents a challenge when designing how a program will work, especially when considering edge cases. For example, some programs might return an integer representing the ID of a record that was created, but return a negative number to represent an error code. This kind of program design, which changes the meaning of a return type based on what its value might be based on some convention, is extremely error-prone. In .NET programming, it's recommend you avoid returning error codes from your methods, and instead raise *exceptions* if necessary when errors occur.

At other times, a method may need to return more than one value in certain circumstances. For example, parsing a string into a type might be an operation for which failure is not an error, but instead should just be represented by a ``false`` return type. However, when the parsing operation is successful, a value of ``true`` should be returned, along with the actual type created by the parsing operation. One approach to this problem is to use a parameter than can return the value (called an *out parameter*). You'll learn more about these in the next section.

Frequently, methods that need to return complex results should be modified in one of two ways. Either the type being returned should be replaced with a class that can encapsulate all of the information the operation needs to provide as a result, or the method itself should be moved into a class that can contain this behavior. Keep in mind when you're deciding how to return results from your methods that sometimes it will make sense to create a new class whose only role is to represent the result of a particular operation (often such classes are named with *Result* as a suffix). In most cases, it's better to use a result type that can accurately and fully describe the result of a function's action than to use out parameters for this same purpose.

## Parameters

Methods can accept parameters, which can be used to modify their behavior. Parameters are declared inside of the parentheses in the method's declaration, as you've seen. Each parameter must specify a type and a name. C# supports optional parameters which will use default values when omitted. You can even create methods that take an arbitrary number of parameters by using a params array, but some of these behaviors are more advanced than you need to know for this lesson.

The name of a method, combined with the types of its parameters, must be unique within the class to which the method belongs. This is known as as the method's *signature*. You will see an error when you build your program if you have two methods with the same name and set of parameter types, even if the return types are different or the parameers have different names. For example, having the following two methods within the same class would generate an error at compile time:

```c#
string GetValue(string fileName)
{}
int GetValue(string versionNumber)
{}
```

It is possible to repeat the same name for a method, but when doing so each *signature* must be unique. These are referred to as *method overloads* and you'll learn more about this technique in the next section.

It's a good idea to limit the number of parameters a method accepts, since a large number of parameters often indicates a method is doing too much and should be broken up into smaller methods. As with the recommendation to use specialized objects for *Result* types, you may sometimes find that it makes sense to create objects to represent parameters as well, or even to create a new type for a method to live on that eliminates the need to pass it many parameters.

Parameters can be made optional by supplying them with default values. The syntax for this is simply to supply the value with the parameter declaration as an assignment:

```c#
string CreateGreeting(string name = "You")
{
    return $"Hi {name}!";
}
```

The above method can now be called with or without a ``string`` argument for the ``name`` parameter. If no argument is passed, the method will use "You" as the value for ``name``. Note that required parameters must be declared before any optional parameters.

## Overloads

C# has always supported the concept of method overloads, which are multiple declarations of the same named method, with different sets of parameters. In many cases, this feature was used to support cases that are now better supported by optional parameters (a more recent C# language feature). In any case, you can create method overloads to provide a richer interface for programmers, allowing them to choose the method signature that is best-suited to their needs. Frequently, method overloads are written such that the real work of the method is performed by the method with the largest number of parameters, and each overload will call this method, supplying the appropriate parameters. This helps to follow the DRY principle - you should avoid creating method overloads that simply copy behavior between themselves.

The following example demonstrates how method overloads might be used for a case where default parameters might also work:

```c#
string CreateGreeting()
{
    return CreateGreeting("You"); // call version with more parameters, passing a default value
}
string CreateGreeting(string name)
{
    return $"Hi {name}!";
}
```

Sometimes, you want to help programmers who might use your method by eliminating the need for them to convert whatever type they have into the type your method expects as a parameter. Thus, you create multiple overloads that will perform the type conversion internally. This makes your method easier to use, and reduces the complexity of the code that calls it. For example, consider these methods that will display how many seconds remain until a deadline, or based on a given ``TimeSpan``:

```c#
int SecondsRemaining(DateTime endTime)
{
    return SecondsRemaining(endTime - DateTime.Now);
}
int SecondsRemaining(string endTime)
{
    return SecondsRemaining(DateTime.Parse(endTime));
}
int SecondsRemaining(TimeSpan duration)
{
    return (int)duration.TotalSeconds;
}
```

Ultimately, all of the methods end up calling the last one, which takes a ``TimeSpan``, but they perform different calculations and/or conversions along the way to make working with the method easier for the calling code. This kind of simplification is a key benefit your programs can get from the method overloading feature in C#.

## Next Steps

In [lesson 10](lesson-10.md) you wrote a program to manage a list of items, responding to different commands the user typed. Use what you've learned about methods to break up that program into several methods, each responsible for a different part of the program. You might create methods to determine the action to take based on the user's input, and to perform each action. Don't worry if your methods are very short (even one line) - the point is for you to get comfortable taking code from one long method and moving it into smaller methods, while still keeping your code working.
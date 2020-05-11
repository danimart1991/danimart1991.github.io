---
title: "Constructor Overloading in C#"
header:
  image: /assets/posts/en/constructor-overloading-in-csharp/header.jpg
toc: false
classes: wide
tags: [ English, C#, Constructor, .NET, Overload ]
categories: [ .NET, C# ]
lang: en
ref: 6
permalink: /en/constructor-overloading-in-csharp/
---

We all know the advantages of method overloading in our code.

> Method overload is the creation of several methods with the same name, but with different signatures and definitions. The number and type of arguments are used to select which method definition to execute.

This offers flexibility when calling a method using a different number of parameters without having to replicate code. For example:

> Disponible en Español [aquí](https://www.nocountryforgeeks.com/sobrecarga-de-constructores-en-c/)

```csharp
public Task<IUICommand> ShowDialog(string title, string content)
{
    var dialog = new Windows.UI.Popups.MessageDialog(content, title);

    return dialog.ShowAsync().AsTask();
}

public Task<IUICommand> ShowDialog(string title)
{
    return ShowDialog(title, string.Empty);
}
```

But what if we want to use these advantages in our constructors?

Just imagine for a moment that we have the following constructors:

```csharp
public MainViewModel(object parameter)
{
    Parameter = parameter;

    LoadData();
}

public MainViewModel()
{
    LoadData();
}
```

You can see at a glance that we are duplicating code.

In this case it may seem like something that doesn't affect the robustness of our code very significantly, but in the case of both overloading methods and overloading constructors, this code can escalate and become a real problem.

A simple solution would be, as you can see in the example we have just given, to create a method that encapsulates all the common functionality. The problem with using this technique is that in the case of assigning values to `readonly` variables, this assignment can only be made through the constructor, so this code would have to be duplicated by obligation.

`C#` proposes a much cleaner and elegant solution:

```csharp
public MainViewModel(object parameter, IList<Checks> checks) : this()
{
    Parameter = parameter;
    Checks = checks;
}

public MainViewModel(IList<Checks> checks) : this(null, checks)
{
}

public MainViewModel(object parameter) : this(parameter, null)
{
}

public MainViewModel()
{
    LoadData();
}
```

With `this()` we can call another constructor that is defined in our class and execute the code that contains it in addition to our own.

Thanks to this technique we will be able to use the advantages of **overloading methods in constructors** and improve our code in a very simple way.

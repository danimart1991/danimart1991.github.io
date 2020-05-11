---
title: "[Micro-Post] Organizing the references in C#"
header:
  image: /assets/posts/en/micro-post-organizing-the-references-in-c/header.jpg
toc: false
classes: wide
tags: [ English, C#, Visual Studio ]
categories: [ .NET, C# ]
lang: en
ref: 5
permalink: /en/micro-post-organizing-the-references-in-c/
---

There are many ways to improve our code, some people advocate for having a code with solid and reliable functionality, others for a clean code, and why not, others for a code that just works.

> Disponible en español [aquí](https://www.nocountryforgeeks.com/micro-post-organizando-las-referencias-en-c/)

In **No Country For Geeks** we believe that the code has to be art, and we are not referring to an abstract picture that each person interprets in a different way; we are talking about a symphony in which each variable, each method, each line, is a compound of functionality, beauty, simplicity and elegance that reach excellence as far as possible.

Among these definitions, organization comes in. And something very simple to improve is the organization of references *using* in our code.

![Bad Organization](/assets/posts/en/2017-08-30-micro-post-organizing-the-references-in-c/bad-organization.png)

The best thing in these cases is that the references are organized in **alphabetical order**, so that at a glance you can easily find the reference in which we are interested.
On the other hand, something optional, but considered a good practice, is that the *System* references should be listed before any other reference, and again in alphabetical order.

With **Visual Studio**, this whole organization can be done in a very simple way.

1. To make the references *System* to appear at the top of the list of references, just go to *Tools -> Options* and in the new window that will display, look for the option *Text Editor -> C# -> Advanced* and activate the check box: *"Place 'System' directives first when sorting usings"*.

    ![System Options](/assets/posts/en/micro-post-organizing-the-references-in-c/system-options.png)

1. Once we are in a class in which we want to organize the references, just use a keyboard shortcut little known but very useful: *Ctrl+R, Ctrl+G*. By doing this, we will see how references not used in our class are cleaned up, references to *System* will be listed first, and all references will be sorted in alphabetical order.

    ![Good organization](/assets/posts/en/micro-post-organizing-the-references-in-c/good-organization.png)

This way we have organized all the references of our project in a very comfortable and fast way.

One thing I like to do when modifying a file is to press *Ctrl+R, Ctrl+G* (organize references), *Ctrl+K, Ctrl+D* (format the document), and finally *Ctrl+S* to save the document. This would make all our code organized and clean in a very quick way.

Note: Depending on the **Visual Studio** settings, the keyboard shortcut for formatting your document may be *Ctrl+E, Ctrl+D*.

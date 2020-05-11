---
title: "Solve unexplained problems in Visual Studio Tools for Apache Cordova"
header:
  image: /assets/posts/en/solve-unexplained-problems-in-visual-studio-tools-for-apache-cordova/header.jpg
toc: false
classes: wide
tags: [ English, Cordova, Visual Studio ]
categories: [ Cordova, Visual Studio ]
lang: en
ref: 2
permalink: /en/solve-unexplained-problems-in-visual-studio-tools-for-apache-cordova/
---

Sometimes when you work with **Visual Studio Tools for Apache Cordova**, you can find some unexplained problems. These problems may arise at compilation time, when you open a project, and often by strange reasons that we can’t identify.

One possible solution to these problems, and that doesn’t carry secondary problems if doesn’t work, is as follows:

1. Make sure you have *Google Chrome* installed and updated.
1. Delete the folder: `c:\Users\<username>\appdata\local\microsoft\visualstudio\14.0\componentmodelcache`
1. Open *Visual Studio* and create a new *Cordova* blank project. This will regenerate the folder you just deleted.
1. Debug the project created using *Ripple*. It may cause some problems on the first try. Close your browser and try to recompile the project, you should now connect without inconvenience.

![Result](/assets/posts/en/solve-unexplained-problems-in-visual-studio-tools-for-apache-cordova/result.png)

With these simple steps, we can solve many problems and clean up the resources that are in cache.

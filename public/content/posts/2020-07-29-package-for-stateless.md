+++ 
draft = false
date = 2020-07-29T18:29:48-04:00
title = "Creating a Unity3D Package for Stateless"
description = "Creating a package from an existing 3rd party github repo for Unity's new package manager."
slug = "unity3d-package-for-stateless" 
tags = ["unity3d", "game-programming", "package-manager"]
categories = []
externalLink = ""
series = []
+++

One pattern that I depend on all the time is the _State Machine_ pattern. It's a great way to decouple inherit state from the game objects in your system. It's a simple pattern that's pretty simple to implement yourself, but there already happens to be a complete and robust library in the C# eco-system: [Stateless](https://github.com/dotnet-state-machine/stateless). Also, unfortunate, you cannot use .NET's package manager in Unity (that'd be a little weird but awesome).

I created a package for use with Unity's recent package manager that lets users easily consume the [Stateless](https://github.com/dotnet-state-machine/stateless) library.

## How to Use the Package

Luckily, [Stateless](https://github.com/dotnet-state-machine/stateless) is dependency free, and if you include all the source files as assets in your project it becomes immediately available.

Rather than copying the files into individual projects over and over again, I made a Unity package for the new package manager. Here's the link https://github.com/jeff-kwak/StatelessUnityPackage. To use the package you enter the git URL [git@github.com:jeff-kwak/StatelessUnityPackage.git](git@github.com:jeff-kwak/StatelessUnityPackage.git), the same URL you would use to clone the repository into the "Add package from git URL..." dialog.

Here are the steps:

1. Open the package manager window. ![Open the Package Manager Window](/images/unity3d-package-for-stateless/open-package-manager.png)
2. Click the `Add` button and choose "Add package from git URL..." ![Add a package from a Git URL](/images/unity3d-package-for-stateless/add-package-from-git-url.png)
3. Enter the git URL and push the `Add` button. ![Enter the Git URL and push add](/images/unity3d-package-for-stateless/enter-url-and-push-add.png)

## How to Make a Package for the Package Manager

I actually built the package by hand, this time. If the number of releases is frequent enough, I'll tie into GitHub actions and create the package CI/CD style. We'll see if that's necessary.

The layout of the package on disc is described here: [Unity - Manual: Package layout](https://docs.unity3d.com/Manual/cus-layout.html).

You have to add a "package.json" that describes the package. This is described here: [Unity - Manual: Package manifest](https://docs.unity3d.com/Manual/upm-manifestPkg.html). Here is the _package.json_ from this project:

```json
{
  "name": "com.github.dotnet-state-machine.stateless",
  "version": "5.1.2",
  "displayName": "Stateless",
  "description": "A simple library for creating state machines in C# code",
  "unity": "2019.4",
  "type": "library",
  "author": {
    "name": "Nicholas Blumhardt",
    "url": "https://github.com/dotnet-state-machine/stateless"
  }
}
```

In a folder named `Runtime` (for this example), you can add the script assets. You also need an `.asmdef` file (e.g., `Unity.Stateless.asmdef`) which simply contains the package name:

```json
{
	"name": "Stateless"
}
```

You'll need to import the project, into a blank project for example, to get unity to process all the scripts and create `.meta` files. Be sure to include those and the `.csproj` file.



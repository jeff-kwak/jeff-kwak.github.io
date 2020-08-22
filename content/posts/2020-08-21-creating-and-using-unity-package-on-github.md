+++ 
draft = false
date = 2020-08-21T21:06:04-04:00
title = "Creating and Using Unity Packages on GitHub"
description = "Create a Unity Package as an embedded package. Publish the project to GitHub. Publish the deployable package using the Git subtree command.Add to new projects using the name of the subtree branch as a suffix to the GitHub clone URL"
slug = "creating-using-unity-packages-on-github" 
tags = ["unity3D", "game programming", "GitHub", "Git"]
categories = []
externalLink = ""
series = []
+++

Unity does a good job of blending in with the .NET ecosystem, but it stands alone as something a little different. I had initially packaged [Stateless](https://github.com/dotnet-state-machine/stateless), but in a couple of projects I started to have issues compiling. The `StateMachine` pattern at it's most basic level is pretty simple so I just made a version specific to Unity (the first release is definitely an MVP implementation): [gr8tgames/statemachine](https://github.com/gr8tgames/statemachine).

Targetting the new Unity Package Manager format, I came across this excellent tutorial from _InfallibleCode_: [A Better Way To Share Your Code! (Unity Tutorial)](https://www.youtube.com/watch?v=OhwP5TYgucM). This gave me the step-by-step instructions for creating the Unity package as an embedded project. This made developing the package really smooth.

The basic procedure is, create a folder for the package directly in the `Packages` folder of the Unity project. Add a `project.json` per https://docs.unity3d.com/Manual/upm-manifestPkg.html and add files according to the expected layout as specified here: https://docs.unity3d.com/Manual/cus-layout.html. If you're adding tests (and you should be adding tests), the difference between *Editor* tests and *Runtime* tests is, the editor tests run without the Unity Engine running. For this package, that's perfect as we don't need the full Unity game engine running to test the `StateMachine`. You would use *Runtime* tests to test interactions with the game engine such as collisions, triggers, et. al.

If you open the package manager window, and choose the add button, you'll notice one of the options is to add a package via a Git URL. GitHub was my first thought. Thanks to https://neogeek.dev/creating-custom-packages-for-unity-2018.3/ I discovered something that's been in Git for a long time, the `subtree` command. Essentially, this would let me _"publish"_ the package for reference while keeping the home repository in useable state for future hacking. 

Develop the package in the `Packages` folder, commit the whole project as you normally would to GitHub, then when it's time to publish run

`git subtree push --prefix Packages\YourPackage origin package`

Adding your package using the Package Manager Window is as simple as selecting the add button, choosing git URL as the option, pasting the clone URL followed by `#package` (which is what you named the "branch" in the subtree command).

For bonus points, you can make a "Release" in GitHub, being careful to tag the `package` branch (versus `main` or some other branch), and then you can use the tag name instead of the branch name to add to Unity (if needed).

Using the embedded package technique and deploying to GitHub with subtree is a great way to package and share code between projects.

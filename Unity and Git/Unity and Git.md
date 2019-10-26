# Unity + Git + Git LFS

TODO: see [mikewesthad/unity-git-and-lfs](https://github.com/mikewesthad/unity-git-and-lfs) for possible setup tutorial to integrate.

## Overview

This is a template for setting up Unity + Git + Git Large File Storage (LFS). 

There are a couple of challenges when it comes to using git (or version control software in general) with Unity:

- Binary assets - commit history, merge conflicts, etc. are near impossible for a human to read. We can solve this by serializing assets, e.g. turning Unity scenes into a text format.
- Large files (large 3D models, big spritesheets, etc.) - GitHub warns you at 50mb and caps you at 100mb. We can solve this with [Git LFS](https://git-lfs.github.com/).

## Guide

TODO

There are two setup options for Git & LFS with your Unity project:

- Unity project at the root of your Git repository. See [here](./Unity%20Project%20as%20Root/).
- Unity project in a subdirectory of your Git repository. See [here](./Unity%20Project%20as%20Subdirectory/).

If you need a more customized solution, start with one of those templates and then dig deeper into .gitignore syntax to fit your needs. This [guide](https://www.atlassian.com/git/tutorials/saving-changes/gitignore) provides a good overview. 

## Resources

- [Version control: Effective use, issues and thoughts, from a gamedev perspective](https://www.what-could-possibly-go-wrong.com/version-control/) - a good overview of the uses and challenges of version control in game dev
- [How to Git with Unity](https://thoughtbot.com/blog/how-to-git-with-unity) - a concise post of Git + Unity
- [Unity's manual of VCS](https://docs.unity3d.com/Manual/VersionControl.html)

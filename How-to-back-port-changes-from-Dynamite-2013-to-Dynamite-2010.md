The main Dynamite project targets SharePoint 2013 and thus uses the .NET 4.5 runtime. This project provides you with a VS2012 solution.

The Dynamite-2010 project is a fork of the main Dynamite project which targets SP2010 and .NET 3.5. This legacy version offers you both VS2010 and VS2012 solutions (to allow developers stuck on an older VS to contribute in spite of that)

Bleeding-edge development usually occurs in the main Dynamite project. If we want to share some of these new changes with the 2010 version, we need to back-port these changes in a granular fashion (in order to avoid merging into the 2010 version code which is .NET 4.5-specific - things like async/await, etc.).


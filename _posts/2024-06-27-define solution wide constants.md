---
title: how to define solution wide constants in c# solution
date: 2024-06-27 12:00:00 +0800
categories: [c#,visusal studio]
tags: [build]
---

## purpose

to solve a problem that we have multiple projects in our solution and we want to define some constants that are used in all projects.

## solution

1. create file in the same directory as your solution file and name it Directory.Build.props

`
Directory.Build.props
`

2. in the Directory.Build.props file, add the following code:

```xml
<Project>
  <PropertyGroup>
      <DefineConstants></DefineConstants>
  </PropertyGroup>
</Project>
``` 

3. add the constants you want to define in the DefineConstants property.

4. save the file and build your solution.
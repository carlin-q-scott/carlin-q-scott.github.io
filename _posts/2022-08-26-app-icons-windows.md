---
tags: [C#, dotnet, .NET, Windows, WiX, MSI]
---
# Using App Icons with Windows

I just learned how to add an icon to my Windows application, and I was surprised how hard it was to put the pieces together from various resources. So here's my one-stop guide to application icons for Windows developers.

## Application Icon (Windows Forms)

Each component with a window can have its icon set in the `InitializeComponent` or constructor methods. But there are several ways to add the icon file to the project.

##### MyComponent.cs InitializeComponent()
```cs
// myicon.ico added to project as embedded resource
this.Icon = new System.Drawing.Icon(typeof(MyEntrypointClass), "myicon.ico");

// myicon.ico added to project resource file
this.Icon = new System.Drawing.Icon(MyEntrypointClass.Properties.Resources.MyIconKey);

// myicon.ico added to component resource file
this.Icon = new System.Drawing.Icon(typeof(MyComponentClass), Resources.MyIconKey);

// myicon.ico added to project as "copy to output directory"
this.Icon = new System.Drawing.Icon("myicon.ico");
```

Any of these methods work fine, but I've listed them in order of my preference. I prefer not using a resource file because if I'm not supporting multiple languages, they add unecesary overhead to compilation and probably negatively impact runtime performance.

Normally, I would prefer leaving the file external to the DLL to reduce time it takes to load the DLL; meaning the last option I listed. However, in this case, the icon will most definitely be needed at startup, so embedding it makes the most sense as it's simpler to manage the deployment with less files.

## Add/Remove Programs Icon (MSI)

You can set the icon used in Add/Remove Programs in your WiX project WSX file:

##### Product.wsx
```xml
<Wix>
  <Product>
    <Icon Id="icon.ico" SourceFile="$(var.SolutionDir)\myicon.ico"/>
    <Property Id="ARPPRODUCTICON" Value="icon.ico" />
```

The installer icon is more complicated to set because it needs to be a banner bitmap, so I didn't bother figuring that out.

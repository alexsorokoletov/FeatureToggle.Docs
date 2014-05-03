---
layout: default
title: Installing and Using FeatureToggle
---

#Documentation for v1.x

```
PM> Install-Package FeatureToggle
```

```
PM> Install-Package FeatureToggle.WPFExtensions
```
### Getting Started

FeatureToggle is designed for ease of use:

1 Create a (strongly typed) toggle
```c#
public class MyAwesomeFeature : SimpleFeatureToggle {}
```
2 Add some config stuff (default is app.config / web.config):
```
<appSettings>
   <add key="FeatureToggle.MyAwesomeFeature" value="false" />
</appSettings>
```
3 Use the toggle in code:
```c#
var myAwesomeFeature = new MyAwesomeFeature ();

if (!myAwesomeFeature.FeatureEnabled)  
{
   // code to disable stuff (e.g. UI buttons, etc)
}
```

Or in XAML:
```
<Button Visibility="{Binding Path=MyAwesomeFeature, Converter={StaticResource toggleVisibilityConverter} }">My Awesome Feature</Button>
```

Types of toggles available:

* AlwaysOffFeatureToggle
* AlwaysOnFeatureToggle
* EnabledOnOrAfterDateFeatureToggle
* EnabledOnOrBeforeDateFeatureToggle
* EnabledBetweenDatesFeatureToggle
* SimpleFeatureToggle
* SqlFeatureToggle




## Using the Provided Toggles.

### SimpleFeatureToggle
This type of Toggle gets it's enabled value from the config file (app.config or web.config).

Create a Toggle for the feature you want to control by inheriting from `SimpleFeatureToggle` - there's no need to override anything:

```c#
private class SaveToPdfFeatureToggle : SimpleFeatureToggle { }
```
Add an entry to your AppSettings section to control the value of the SaveToPdfFeatureToggle
```
<appSettings>
   <add key="FeatureToggle.SaveToPdfFeatureToggle " value="true"/>
</appSettings>
```

Note the convention FeatureToggle.<class name>

Now you can use the Toggle in code:
```c#
var savePdfFeature= new SaveToPdfFeatureToggle();
 
if (savePdfFeature.FeatureEnabled)
   ShowSavePdfButton();
else
   HideSavePdfButton();
```


To disable the Toggle, just set the appSettings value to false.

### AlwaysOffFeatureToggle & AlwaysOnFeatureToggle
These toggle are not configurable by config so cannot be altered at runtime.

Create a Toggle for the feature you want to control by inheriting from AlwaysOffFeatureToggle or AlwaysOnFeatureToggle - there's no need to override anything:
```c#
private class SaveToPdfFeatureToggle : AlwaysOffFeatureToggle { }
```

Now you can use the Toggle in code:
```c#
var savePdfFeature= new SaveToPdfFeatureToggle();
 
if (savePdfFeature.FeatureEnabled)
   ShowSavePdfButton();
else
   HideSavePdfButton();
```

### EnabledOnOrAfterDateFeatureToggle and EnabledOnOrBeforeDateFeatureToggle

Create a Toggle for the feature you want to control by inheriting from EnabledOnOrAfterDateFeatureToggle or EnabledOnOrBeforeDateFeatureToggle- there's no need to override anything:
```c#
private class SaveToPdfFeatureToggle : EnabledOnOrAfterDateFeatureToggle { }
```

Add an entry to your AppSettings section to control the value of the EnabledOnOrAfterDateFeatureToggle
```
<appSettings>
   <add key="FeatureToggle.SaveToPdfFeatureToggle " value="01/01/2000 00:00:00"/>
</appSettings>
```
And then use in the same way as other toggles.


### EnabledBetweenDatesFeatureToggle

Create a Toggle for the feature you want to control by inheriting from EnabledBetweenDatesFeatureToggle - there's no need to override anything:
```c#
private class SaveToPdfFeatureToggle : EnabledBetweenDatesFeatureToggle { }
```

Add an entry to your AppSettings section to control the value of the EnabledBetweenDatesFeatureToggle (start date | end date)
```
<appSettings>
   <add key="FeatureToggle.SaveToPdfFeatureToggle " value="01/01/2000 00:00:00 | 01/01/2001 00:00:00"/>
</appSettings>
```
And then use in the same way as other toggles.

### SqlFeatureToggle
This Toggle gets it's value from a Sql Server database. You can use to turn enable a Feature from a single point when the Feature spans a number of applications, services, layers, etc.

Create a Toggle for the feature you want to control by inheriting from SqlFeatureToggle- there's no need to override anything:
```c#
private class SaveToPdfFeatureToggle : SqlFeatureToggle{ }
```

Add an entries to your AppSettings section for the Sql Server database connection string and a sql staetment that returns either "true" or "false".

```
<add key="FeatureToggle.SaveToPdfFeatureToggle.ConnectionString" value="Data Source=.\SQLEXPRESS;Initial Catalog=FeatureToggleDatabase;Integrated Security=True;Pooling=False"/>
<add key="FeatureToggle.SaveToPdfFeatureToggle.SqlStatement" value="select Value from Toggle where ToggleName = 'SaveToPdfFeatureToggle '"/>
```

### WPF and Windows Phone 7
Binding the Visibility of a UI element to a Toggle
Create a Toggle as described above.

In your XAML declare a converter:
```
<Wpf:FeatureToggleToVisibilityConverter x:Key="toggleVisibilityConverter"/>
```

Create a property in your view-model or code-behind:
```c#
public IFeatureToggle SaveToPdfFeature
{
   get 
   {
      return _saveToPdfFeature;
   }
   set
   {
      _saveToPdfFeature = value;
      // Do INotifyPropertyChanged stuff here if required
   }
}
```


Now bind your UI elements visibility:
```
<Button Visibility="{Binding Path=SaveToPdfFeature, Converter={StaticResource toggleVisibilityConverter} }">Save To PDF</Button>
```

### Windows Phone 7 SimpleFeatureToggle Provider
As WP7 does not have the concept of an app.config, a WindowsPhone7ApplicationResourcesSettingsProvider is used to determine a Toggle's value.

Declare a toggle as defined in the examples above, but before using you must add to the applications resources, e.g.
```c#
Application.Current.Resources.Add("SaveToPdfFeatureToggle", false);
```

This adds the Toggle and sets it's value to false. You could implement your own code here to retrieve the value from a file in Isolated Storage for example.

### Creating Your Own Custom Toggles
Just implement IFeatureToggle and provide an implementation for FeatureEnabled - easy :)

### Creating Your Own Providers
For the SimpleFeatureToggle and SqlFeatureToggle, create a class that implements IBooleanToggleValueProvider and after you instantiate your Toggle set it's BooleanToggleValueProvider property to you custom provider.
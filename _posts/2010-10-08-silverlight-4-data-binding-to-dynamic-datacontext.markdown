---
layout: single
title: "Silverlight 4 Data Binding to Dynamic DataContext"
date: 2010-10-08 16:57:00 +0200
comments: true
published: true
categories: ["post"]
tags: ["C#","Silverlight","Open source"]
alias: ["/2010/10/08/silverlight-4-data-binding-to-dynamic-datacontext/"]
author: Xavier Decoster
redirect_from:
 - /2010/10/08/silverlight-4-data-binding-to-dynamic-datacontext/.html
 - /2010/10/08/silverlight-4-data-binding-to-dynamic-datacontext/.html
---
<p>If you ever want to build a Silverlight control that supports TwoWay <a title="data binding in Silverlight (MSDN)" href="http://msdn.microsoft.com/en-us/library/cc278072(VS.95).aspx" target="_blank">data binding</a> on a dynamic object, you might hit the same issues as I did, so I hope this post is useful for at least some of you.</p>

<p>To put this in context, think about the following request:<em>As an end-user I want to be able to design my own personalized user control so that I can use my company's branding, business vocabulary, modify the available fields on the control, ...</em>E.g. replace control by a Post-It on a Silverlight wall in an application, or replace by any similar situation you can think of.Of course, this control is not read-only, but has to be editable (hence the TwoWay data binding).</p>

<p>The above use case has some pretty neat consequences:</p>

<ul>
<li>the <strong><a title="FrameworkElement.DataContextProperty" href="http://msdn.microsoft.com/en-us/library/system.windows.frameworkelement.datacontext(VS.95).aspx" target="_blank">datacontext</a> Type is unknown</strong> (yes, I know, you can work-around it with proper OO, although it will take you much more time to come up with a design that fits all scenarios, not to mention maintenance time afterwards): I'm following the principle that <em>all code is bad code</em>: the less code I write, the less defects I create, and the more time I have to do other stuff (in other words, I was lazy ^^)</li>
<li>how do you make the <strong><a title="System.Windows.Controls.ControlTemplate" href="http://msdn.microsoft.com/en-us/library/system.windows.controls.controltemplate(VS.95).aspx" target="_blank">controltemplate</a></strong> suitable for such a scenario? preferably, you won't create new controltemplates for every type of datacontext you might ever bind to it, right? (if you do, I will send you over 10K different scenarios your control needs to support)</li>
<li>there are more, but they can be tackled without this dynamic stuff</li>
</ul>

<p>First of all, it is noteworthy that you won't be able to use the built-in .NET 4 <a title="System.Dynamic.ExpandoObject" href="http://msdn.microsoft.com/en-us/library/system.dynamic.expandoobject.aspx" target="_blank">ExpandoObject</a>, although it implements <a title="System.ComponentModel.INotifyPropertyChanged" href="http://msdn.microsoft.com/en-us/library/system.componentmodel.inotifypropertychanged(VS.95).aspx" target="_blank">INotifyPropertyChanged</a>. The reason is that Silverlight is using reflection to perform binding. In other words, Silverlight looks for the <em>actual</em> property.Hence my approach creating my own dynamic type by inheriting from the new <a href="http://msdn.microsoft.com/en-us/library/system.dynamic.dynamicobject(VS.95).aspx" target="_blank">DynamicObject</a> class.</p>

<p>Behold, my newly created <strong>DynamicDataContext</strong> type, which supports adding and removing properties of any type at runtime, and above all, it will be supporting Silverlight TwoWay data binding when you use the Silverlight 4 indexer syntax for your Binding Path (see example usage at the bottom of this post).Note that the code sample below does not implement all virtual members of the base type for brevity.</p>

<pre class="brush: csharp;">using System.Dynamic;
using System.ComponentModel;

public class DynamicDataContext : DynamicObject, INotifyPropertyChanged
{
     private readonly IDictionary&lt;string, object&gt; propertyBag = new Dictionary&lt;string, object&gt;();

     public event PropertyChangedEventHandler PropertyChanged;

     /// &lt;summary&gt;
     /// The indexer is needed to be able to use indexer-syntax in XAML
     /// to data bind to the properties available in the private property bag.
     /// &lt;/summary&gt;
     /// &lt;param name="index"&gt;The name of the property.&lt;/param&gt;
     /// &lt;returns&gt;The value of the property, or null if the property doesn't exist.&lt;/returns&gt;
     public object this [string index]
     {
          get
          {
               object result;
               propertyBag.TryGetValue(index, out result);
               return result;
          }
          set { propertyBag[index] = value; RaisePropertyChanged(index); }
     }

     public override bool TryGetMember(GetMemberBinder binder, out object result)
     {
          return propertyBag.TryGetValue(binder.Name, out result);
     }

     public override bool TrySetMember(SetMemberBinder binder, object value)
     {
          propertyBag[binder.Name] = value;
          RaisePropertyChanged(binder.Name);
          return true;
     }

     private void RaisePropertyChanged(string propertyName)
     {
          if(PropertyChanged != null)
               PropertyChanged(this, new PropertyChangedEventArgs(propertyName));
     }
}
</pre>

<p>To perform TwoWay data binding in XAML, you simply use the indexer syntax, forcing the Silverlight data binding system to look for the indexer (which points to the property bag).The example below binds a control to some datacontext of type DynamicDataContext.Also note that this controltemplate is not the one that will be able to pick up all dynamically added properties at runtime (I need to do a bit more magic to do that).</p>

<pre class="brush: xml;">&lt;SomeTemplatedControl DataContext={Binding DynamicDataContext}&gt;
     &lt;SomeTemplatedControl.ControlTemplate&gt;
          &lt;ControlTemplate TargetType="SomeTemplatedControl"&gt;
               &lt;StackPanel Orientation="Vertical"&gt;
                    &lt;ContentPresenter Content="[Title]"/&gt;
                    &lt;ContentPresenter Content="[Description]"/&gt;
               &lt;/StackPanel&gt;
          &lt;/ControlTemplate&gt;
     &lt;/SomeTemplatedControl.ControlTemplate&gt;
&lt;/SomeTemplatedControl&gt;
</pre>

<p>The data context can be created by dynamically by adding any property you want, as shown in the example below:</p>

<pre class="brush: csharp;">dynamic datacontext = new DynamicDataContext();
     datacontext.Title = "A dynamic title";
     datacontext.Description = "A dynamic description (duh!)";
     DynamicDataContext = datacontext;
</pre>

<p>I have to admit, the new dynamic features in .NET 4 are pretty cool stuff.</p>

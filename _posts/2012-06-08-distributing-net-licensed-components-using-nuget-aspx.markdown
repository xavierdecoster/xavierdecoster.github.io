---
layout: post
title: "Distributing .NET Licensed components using NuGet"
date: 2012-06-08 22:07:00 +0200
comments: true
published: true
categories: ["post"]
tags: []
alias: ["/2012/06/08/distributing-net-licensed-components-using-nuget-aspx/"]
author: Xavier Decoster
redirect_from:
 - /2012/06/08/distributing-net-licensed-components-using-nuget-aspx/.html
 - /2012/06/08/distributing-net-licensed-components-using-nuget-aspx/.html
---
<p>If you are building components that are licensed using the <a href="http://www.codeguru.com/csharp/.net/net_framework/licensing/article.php/c5469/Licensed-Applications-using-the-NET-Framework.htm" target="_blank">.NET Licensing Model</a>, you might have been looking for a way to take benefit from <a href="http://www.nuget.org" target="_blank">NuGet</a> as a distribution mechanism.</p>

<p>If you are unfamiliar with the .NET Licensing Model, I recommend this <a href="http://www.codeguru.com/csharp/.net/net_framework/licensing/article.php/c5469/Licensed-Applications-using-the-NET-Framework.htm" target="_blank"><em>excellent article</em></a> providing you with a good introduction and sample code.</p>

<p>No matter what licensing model you use, an application that consumes a licensed component needs to use a mysterious <strong>licenses.licx</strong> file and set it as an embedded resource. In addition, the license key or <em>.lic</em> file for the licensed component being consumed must be present.</p>

<p>This makes packaging a .NET licensed component slightly different from packaging a simple assembly.</p>

<h1>Licensing a component</h1>

<p>To illustrate this, I have a sample component (a Windows Forms user control) that I licensed using the .NET built-in <a href="http://msdn.microsoft.com/en-us/library/system.componentmodel.licfilelicenseprovider.aspx" target="_blank">LicFileLicenseProvider</a>. Actually, I used a custom one which inherits from it, just because I can :) The only thing I did was changing the license key that I expect from the consuming application. The following code block contains the entire user control (yep, that's all there is to it). The code required to license the control is highlighted.</p>

<pre class="brush: csharp; gutter: true; first-line: 1; tab-size: 2;  toolbar: false; highlight: [7,10,16,17,37,38,39,40,41];">using System.ComponentModel;
using System.Drawing;
using System.Windows.Forms;

namespace LicensedComponent
{
  [LicenseProvider(typeof(MyLicFileLicenseProvider))]
  public class LicensedUserControl : UserControl
  {
    private License _license;
    private Label _lblText;

    public LicensedUserControl()
    {
      InitializeComponent();
      _license = LicenseManager.Validate(typeof(LicensedUserControl), this);
      _lblText.Text = _license.LicenseKey;
    }

    private void InitializeComponent()
    {
      _lblText = new Label();
      SuspendLayout();
      _lblText.Dock = DockStyle.Fill;
      _lblText.TextAlign = ContentAlignment.MiddleCenter;

      Controls.Add(_lblText);
      Size = new Size(400, 250);

      ResumeLayout(false);
    }

    protected override void Dispose(bool disposing)
    {
      if (_license != null)
      {
        _license.Dispose();
        _license = null;
      }
      base.Dispose(disposing);
    }
  }
}
</pre>

<p>You might be wondering why I keep track of the license object: I simply display the license key in the control's label. Note that you can create your own custom license type by inheriting from License (and extend it with properties, e.g. a boolean IsInDemoMode). To customize the licensing mechanism, you simply create your own LicenseProvider. In this case, the <a href="#MyLicFileLicenseProvider" target="_blank">MyLicFileLicenseProvider</a> looks as following:</p>

<pre class="brush: csharp; gutter: true; first-line: 1; tab-size: 2;  toolbar: false;  width: 650px; height: 352px;">using System.ComponentModel;

namespace LicensedComponent
{
  public class MyLicFileLicenseProvider : LicFileLicenseProvider
  {
    private const string LicenseKeyFormat = "{0} license key: {1}";

    // this key is what makes you a billionaire
    private const string LicenseKey = "{2EDE0218-0996-41D8-9E32-6066F248A215}";

    protected override string GetKey(System.Type type)
    {
      return string.Format(LicenseKeyFormat, type.FullName, LicenseKey);
    }
  }
}</pre>

<p>There's only one thing left for this component to be ready for distribution: the license file. The LicFileLicenseProvider tells the .NET runtime to look for a license file. The license file should have a specific name, which is by convention equal to the following pattern: <strong>classFullName.lic</strong>. Just put it in the component's project.</p>

<p><img alt="" src="https://xavierdecosterblog.blob.core.windows.net/blog/2012-06-08/2012-06-08_1431.png" height="110" width="313" /></p>

<p>In this sample, this results in LicensedComponent.LicensedUserControl.lic</p>

<h1>Consuming a licensed component</h1>

<p>This is heavily dependent on the licensing mechanism obviously. I'll just explain how you can consume the above component to give you an idea. The end result with regard to the <strong>licenses.licx</strong> file and the NuGet package are the same, which is the goal of this post. For more advanced licensing strategies or inspiration, refer to <a href="http://www.codeguru.com/csharp/.net/net_framework/licensing/article.php/c5469/Licensed-Applications-using-the-NET-Framework.htm" target="_blank">this post</a>.</p>

<p>As mentioned in this post's introduction, the consumer needs to have a <em>licenses.licx</em> file (describing the list of components that are licensed). Because I used the simple LicFileLicenseProvider, the consuming application must also have a valid <em>.lic</em> file (for each component, containing it's license key). The latter is not needed if you implemented a different licensing mechanism that doesn't require such file (e.g. checking the registry or calling a web service).</p>

<p><img alt="" src="https://xavierdecosterblog.blob.core.windows.net/blog/2012-06-08/2012-06-08_1447.png" height="356" width="592" /></p>

<p>For a Windows Forms licensed user control, Visual Studio will create the licenses.licx file for you the moment you drag-n-drop the component onto another control in the designer. In the screenshot above, I just docked my control on the form.</p>

<p>The licenses.licx file contains the following, clearly indicating that the <em>LicensedComponent.LicensedUserControl</em> with version 1.0.0.0 contains a licensed control with the name <em>LicensedComponent.</em></p>

<pre class="brush: plain; gutter: false; first-line: 1; tab-size: 2;  toolbar: false;  width: 650px; height: 29px;">LicensedComponent.LicensedUserControl, LicensedComponent, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null</pre>

<p>However, where is the <strong>LicensedComponent.LicensedUserControl.lic</strong> file containing the required license key? Well, it must be found next to the assembly containing the licensed component. If this would be a NuGet package (which it already is, if you look at the solution), you'd find the assembly somewhere inside the $(SolutionDir)\Packages folder. More specifically: the $(SolutionDir)\Packages\LicensedComponent.1.0.0.0\lib\net40-client folder in this case. Its contents would look like this:</p>

<pre class="brush: plain; gutter: false; first-line: 1; tab-size: 2;  toolbar: false;  width: 650px; height: 26px;">LicensedComponent.LicensedUserControl license key: {2EDE0218-0996-41D8-9E32-6066F248A215}</pre>

<p>Why exactly this text? Check the <a href="#MyLicFileLicenseProvider" target="_blank">MyLicFileLicenseProvider</a> code again.</p>

<p>As you can see, there's more to it than just adding a reference to the *LicensedComponent.dll, *so the same is true for creating the NuGet package that will distribute it.</p>

<h1>Creating a "licensed" NuGet package</h1>

<p>The full <a href="http://docs.nuget.org/docs/reference/nuspec-reference" target="_blank">nuspec</a> file I used to package this licensed user control can be found below. The nuspec is located next to my .csproj file, while I manually created the licenses.licx file myself in the solution directory (one level up, which explains the relative path in the nuspec).</p>

<pre class="brush: xml; gutter: true; first-line: 1; tab-size: 2;  toolbar: false;  width: 650px; height: 414px;">&lt;?xml version="1.0"?&gt;
&lt;package&gt;
  &lt;metadata&gt;
    &lt;version&gt;$version$&lt;/version&gt;
    &lt;authors&gt;$author$&lt;/authors&gt;
    &lt;owners /&gt;
    &lt;id&gt;$id$&lt;/id&gt;
    &lt;title /&gt;
    &lt;requireLicenseAcceptance&gt;false&lt;/requireLicenseAcceptance&gt;
    &lt;description&gt;$description$&lt;/description&gt;
  &lt;/metadata&gt;
  &lt;files&gt;
    &lt;file src="..\licenses.licx"
          target="Content\Properties"/&gt;
    &lt;!--
      Ensure the .lic file is packaged next to its
      companion .dll inside the Lib folder
    --&gt;
    &lt;file src="LicensedComponent.LicensedUserControl.lic"
          target="Lib\net40-Client"/&gt;
  &lt;/files&gt;
&lt;/package&gt;</pre>

<p>If I want to give my package consumer a smooth install experience, I should ensure that the proper license is installed as well. In short, I'll simply ensure the <em>LicensedComponent.LicensedUserControl.lic</em> file is shipped next to its assembly, and copy the <em>licenses.licx</em> file into the target project.</p>

<p>Here's the point where you can finally make that remark about <em>why the hell are these license files not XML files</em>! :)</p>

<p>I'd strongly recommend you to create your own licensing mechanism, using XML, registry, services, etc. because my package will not be able to merge the contents of the licenses.licx file into the target project if this file already exists. You can work around it though using PowerShell if you use this file-based mechanism for your components.</p>

<h1>Securing the package?</h1>

<p>Ok, so you have a package which is worth something. It contains a licensed fully working version of your component (using this very basic licensing mechanism at least). How do I distribute it? Putting it on NuGet.org is a no-go, as everyone can simply consume it without paying the license fee.</p>

<p>Here's an alternative: create a private NuGet feed on <a href="http://www.myget.org" target="_blank">MyGet</a>. Simply give access to those people who paid for it, and secure it for others.</p>

<p><a title="MyGet.org - NuGet-as-a-Service" href="http://www.myget.org" target="_blank"><img alt="" src="https://xavierdecosterblog.blob.core.windows.net/blog/2012-06-08/myget_125x32.png" /></a></p>

<p>Again, I used a very basic licensing mechanism only to demonstrate how you can embed a file-based license into your NuGet package and pointed out you can secure your feed with granular access instead of worrying about your package. It's up to you to ensure your licensing mechanism doesn't support distributing the licensed package elsewhere! (this proof-of-concept doesn't mitigate this at all, so be warned!)</p>

<p>The code as well as the NuGet package are attached to this blog:</p>

<ul>
<li><a href="https://xavierdecosterblog.blob.core.windows.net/blog/2012-06-08/LicensedComponent.zip">LicensedComponent.zip (212.22 kb)</a></li>
<li><a href="https://xavierdecosterblog.blob.core.windows.net/blog/2012-06-08/ConsumingClient.zip">ConsumingClient.zip (22.23 kb)</a></li>
<li><a href="https://xavierdecosterblog.blob.core.windows.net/blog/2012-06-08/LicensedComponent.1.0.0.0.nupkg">LicensedComponent.1.0.0.0.nupkg (5.60 kb)</a></li>
</ul>

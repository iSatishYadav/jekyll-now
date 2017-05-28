---
published: false
---

---
layout:post
---
layout:post

---
title
permalink

10 Google Photos feature you don't know (probably)
/google-photos-hidden-features/
How turn any .NET app into an ADFS Claim Aware App using OWIN
===
Hi, in this post we'll see how you can convert any of existing .NET web app into 
1. Claim Aware
2. ADFS Integrated

So, let's get started. 

>There are many tutorials on Internet on _how to connect ADFS to an application_ but they don't tell how to use these claims received from ADFS into a real world application. 

First of all, we'll create two ASP.NET apps, one Web-forms and one MVC; so we have a fair idea on how to take care of each types of apps. 

If you're reading this article I'm sure you know how to create a new application from Visual Studio templates. 

Go to _Files- New- Project- Visual C#- ASP.NET Web Application._

Keep authentication as None. 

Now here are the important stuff. 
1. Right click on the main Project and go to Add- New Item. 
2. Select _OWIN Startup Class_, name it _Startup.cs_
3. Now this class should have an attribute `owin:startup`. 
4. The content of this class should look like this

````
using System;
....

public class Startup
{
  public static void Configure(IAppBuilder app)
  {
    app. 
  }
} 
````## A New Post

Enter text in [Markdown](http://daringfireball.net/projects/markdown/). Use the toolbar above, or click the **?** button for formatting help.

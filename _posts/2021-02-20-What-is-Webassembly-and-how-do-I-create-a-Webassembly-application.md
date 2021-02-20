---
layout: post
title: What is WebAssembly and how to create a WebAssembly app?
tags: ["WebAssembly", "Blazor", "ASP.NET Core", ".NET 5"]
excerpt_separator: <!--more-->
---

## Preamble

The browsers only understand JavaScript, however with rise of stuff that we use on browsers e.g. Games, Virtual Reality, Augumented Reality, JavaScript hasn't been able to catch up due to its inherent `interpreted` nature despite its powerful engines like V8.

Suppose you have a cool game or software developed in a low-level programming language e.g. C/C++ or a high-level programming language e.g. Java/C#/Rust/Go. With WebAssembly you can run these apps on a browser. Wihout re-writing them in JavaScript.

Wait, what?

<!--more-->

## What is WebAssembly?

> WebAssembly (abbreviated Wasm) is a binary instruction format for a stack-based virtual machine. Wasm is designed as a portable compilation target for programming languages, enabling deployment on the web for client and server applications.
> 
> -https://webassembly.org/

>WebAssembly, or wasm, is the most significant new technology to come to the web platform in a decade.  Technically speaking, it is a new, low-level, assembly-like language that runs efficiently on the existing web platform and is backward-compatible with its precursor, asm.js. Most people, however, will use the wasm format as a compiler target, translating their applications into web-ready modules that can run in modern web browsers at near-native speeds.
>
> -https://research.mozilla.org/webassembly

![W A S M](https://github.com/iSatishYadav/RunningWebAssemblyInBrowser/raw/master/images/WASM.png)


Check out [Milica Mihajlija](https://blog.logrocket.com/author/milicamihajlija/)'s awesome blog to know more.

## Blazor
`Blazor` is an open-source framework from Microsoft to develop `Component` based UIs using language you already know and love, `C#`. I'll be blunt and say:
> It's Microsoft's response to Angular and React.

Because why not?

But it's not why we're talking about Blazor here. The reason is, one of the hosting models of Blazor is WebAssembly.

Blazor's home page says: 
> Blazor can run your client-side C# code directly in the browser, using WebAssembly. Because it's real .NET running on WebAssembly, you can re-use code and libraries from server-side parts of your application.
> -https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor

## Let's Code a Webassemby App
Yeah, enough with the theory already. Let's get down to the code.
### Create a new Project
* Open Visual Studio and create a new _Blazor WebAssembly App_
* **[Optional]** Check _Progressive Web Application_. More on Progressive Web App in a later article.

    ![{43699453 F E18 4883 91 A6 E69481 D923 D5}](https://github.com/iSatishYadav/RunningWebAssemblyInBrowser/raw/master/images/{43699453-FE18-4883-91A6-E69481D923D5}.png)
    
* Let's have a look at the Code generated by the template:

    ![{221578 E D A39 F 4 A B2 8076 B5 F D B A D05801}](https://github.com/iSatishYadav/RunningWebAssemblyInBrowser/raw/master/images/{221578ED-A39F-4AB2-8076-B5FDBAD05801}.png)

> The generated app uses `WebAssebmlyHostBuilder`, similar to `WebHostBuilder` in `ASP.NET Core`.

* Run this shell application. A simple application with a Nav Bar saying Hello, world!


    ![{F E0 D2 E80 701 C 41 A4 B C E E 10 E09227 D F58}](https://github.com/iSatishYadav/RunningWebAssemblyInBrowser/raw/master/images/{FE0D2E80-701C-41A4-BCEE-10E09227DF58}.png)

* If you've checked the _Progressive Web Application_ checkbox, you'll see the standard Install icon, click on it.

    ![{B E6 E0 F70 8 B8 A 4661 8 B3 E 22 E E5 A64 B1 C7}](https://github.com/iSatishYadav/RunningWebAssemblyInBrowser/raw/master/images/{BE6E0F70-8B8A-4661-8B3E-22EE5A64B1C7}.png)
> The template had basic configuration setup for us to make this an installable PWA (Progressive Web App).


* Add a new Blazor `Component` named Hash which will calculate `SHA256` hash of a given input. 
    
    ![{1 E20 D D17 D1 C6 41 F E B1 D F E8 A81 E D29 B85}](https://github.com/iSatishYadav/RunningWebAssemblyInBrowser/raw/master/images/{1E20DD17-D1C6-41FE-B1DF-E8A81ED29B85}.png)

* Copy the code into the `Component`. This will create an text input - where we'll input our plain text, a button - on click of which we'll calculate the hash, and label - where the hash would be shown.

    ````csharp
    @page "/hash"
    <h1>Hash</h1>
    <p>Get SHA265 Hash via WebAssembly</p>
    <EditForm Model="@hashInput" OnSubmit="@CalculateHash">
        <div class="form-group">
            <InputText @bind-Value="hashInput" class="form-control" id="textInput" aria-describedby="emailHelp" placeholder="Enter text to calculate Hash" />
            <small id="hashHelp" class="form-text text-muted">@hashOutput</small>
        </div>
        <button type="submit" class="btn btn-primary">Submit</button>
    </EditForm>

    @code {
        private string hashInput = string.Empty;
        private string hashOutput;
        void CalculateHash()
        {
            hashOutput = GetSha256Hash(hashInput);
        }
        private static string GetSha256Hash(string plainText)
        {
            // Create a SHA256 hash from string   
            using var sha256Hash = System.Security.Cryptography.SHA256.Create();
            // Computing Hash - returns here byte array
            byte[] bytes = sha256Hash.ComputeHash(System.Text.Encoding.UTF8.GetBytes(plainText));

            // now convert byte array to a string   
            var stringbuilder = new System.Text.StringBuilder();
            for (int i = 0; i < bytes.Length; i++)
            {
                stringbuilder.Append(bytes[i].ToString("x2"));
            }
            return stringbuilder.ToString();
        }
    }

    ````

>This is not using any trick to call JavaScript/interop to calculate hash. This is a real C# code running in Browser, powered by WebAssembly.

* Run this application and enter a text in the input and press Submit!

    ![{E C D7614 E A A D7 4647 9206 F D18430367 D2}](https://github.com/iSatishYadav/RunningWebAssemblyInBrowser/raw/master/images/{ECD7614E-AAD7-4647-9206-FD18430367D2}.png)

## If the code is running in the Browser, where are all the `DLLs`?
* Behind the scene, Blazor generates a `blazor.boot.json` to determine which `DLLs` need to downloaded and then stores them into browser's Cache. You can verify it by going to Dev Tools..
 
    ![{64 B55 A B6 B692 41 D0 99 F4 73 C5 B9 F50 B24}](https://github.com/iSatishYadav/RunningWebAssemblyInBrowser/raw/master/images/{64B55AB6-B692-41D0-99F4-73C5B9F50B24}.png)
* Here's the full contents of the `blazor.boot.json`   

````json
{
  "cacheBootResources": true,
  "config": [ ],
  "debugBuild": true,
  "entryAssembly": "RunningWebAssemblyInBrowser",
  "icuDataMode": 0,
  "linkerEnabled": false,
  "resources": {
    "assembly": {
      "Microsoft.AspNetCore.Authorization.dll": "sha256-hBmEudhpdeLXLU5KNzx5bBkI9o4ZurJu15U6afmGyIg=",
      "Microsoft.AspNetCore.Components.dll": "sha256-YYHd2lVuYwtRTYNBtWL5k+alBHjMQGBqMwwgixqG9Ak=",
      "Microsoft.AspNetCore.Components.Forms.dll": "sha256-UCDJr7\/sSWmGNxe2PdesHLGuJ4DDhl80kt\/tIVZWHek=",
      "Microsoft.AspNetCore.Components.Web.dll": "sha256-3xdazV4ZzJeovsJfSuAf6fDNtYdN6s8\/Jb07Ad4ba14=",
      "Microsoft.AspNetCore.Components.WebAssembly.dll": "sha256-97KtZg0QVFDfIPBAh8dCa7c7uEsp4E6nZ4nQaVPUJio=",
      "Microsoft.AspNetCore.Metadata.dll": "sha256-tYBkgcpE9Uhi+xZAH+XLgtMNHASeCWzvIjDrtyKGN6o=",
      "Microsoft.Extensions.Configuration.dll": "sha256-x9zYR1t1is\/fStNpBiQnXyPdMpLLcQcra3w4RXGSg4o=",
      "Microsoft.Extensions.Configuration.Abstractions.dll": "sha256-NYecKM0ZpUvzj4spvgi6xUu80rkx8PX7fiRok\/BNaHw=",
      "Microsoft.Extensions.Configuration.Binder.dll": "sha256-JFLIykah3r5WjvztBuVIaMM9wfVswDM+On6k\/QVgeeg=",
      "Microsoft.Extensions.Configuration.FileExtensions.dll": "sha256-5tCmu87qBh0Xgsp\/dG\/x9WHctz\/J8YBmCHjt+PA6dn0=",
      "Microsoft.Extensions.Configuration.Json.dll": "sha256-OM0kYGXmRhgJOUP2Vf86pt4Ss7QB2\/fLPtfL4+p0Xz8=",
      "Microsoft.Extensions.DependencyInjection.dll": "sha256-u+gNHZeUqbnws+gIn1IfNiYF+mh0Ijeke856hVI3eiM=",
      "Microsoft.Extensions.DependencyInjection.Abstractions.dll": "sha256-D9y5RXbA\/sEzwk6cnGbGMKQv87bvOEEVycrLUTe0lGU=",
      "Microsoft.Extensions.FileProviders.Abstractions.dll": "sha256-aUKLFqSgB\/hwZc41dLVKoRSKRghShpfQPVMhoRzpz5M=",
      "Microsoft.Extensions.FileProviders.Physical.dll": "sha256-Cvhy7j568oPzBQKhakMhNkDr5tQBxE0LaY1wukd0Ll0=",
      "Microsoft.Extensions.FileSystemGlobbing.dll": "sha256-xrpMDo35bgwz\/m\/kwUvL9SAOxyEgSSwO57RiuhILHgI=",
      "Microsoft.Extensions.Logging.dll": "sha256-VlsKv5zXgdK0MrYHXr9XlUCXwuwNI7AUI\/C1PEFXcz4=",
      "Microsoft.Extensions.Logging.Abstractions.dll": "sha256-9UNtwDMFW86VtnTtJFk9xcc1hCnXFGXccFv3SkbgYy0=",
      "Microsoft.Extensions.Options.dll": "sha256-YfK6Zl0RmlJUqBJD7Khy7u4HEW2DRne0VSiqA6Yqs0U=",
      "Microsoft.Extensions.Primitives.dll": "sha256-f2PgoftqEkKRuuiAk6S1MHygOuqZBHzB0HOB3vR93TU=",
      "Microsoft.JSInterop.dll": "sha256-nRrEUPsQERx39N5116F8869epzUx5PHVOexlygwlEaw=",
      "Microsoft.JSInterop.WebAssembly.dll": "sha256-4vJK1Cg+6YCG2LXoMEVEqy8bqawD3HL16+WNXWIzVCU=",
      "System.IO.Pipelines.dll": "sha256-3ET+mpR7F058YRj9YaLHOVXBqOW6iUxtIpeiKPYw4PA=",
      "Microsoft.CSharp.dll": "sha256-p6Zo15zz3qX\/9gWFiC8okcmNldkS8Ql+qU\/m5khmjBw=",
      "Microsoft.VisualBasic.Core.dll": "sha256-lVbiJ8H9FeZTTzlcLegk3QRCf2YnDu3h1Pg30r6\/KxE=",
      "Microsoft.VisualBasic.dll": "sha256-27XnI6c\/jgvN+x7Imvb0pR+ug8LrRjLAHC1B5jzBH3M=",
      "Microsoft.Win32.Primitives.dll": "sha256-PR7CXQVWdMuF37s253BuNi745bMqCcJYS5E6wPf+Rfg=",
      "Microsoft.Win32.Registry.dll": "sha256-kTAtF1IXWX\/L7cCcxaSRUkEPOdZS4pthTlXbo4g8uck=",
      "System.AppContext.dll": "sha256-T5q0PB7XNDn0d+Mz0FiTcyzPvFy0EW+JrNQTsfuOCp0=",
      "System.Buffers.dll": "sha256-DY2oXyvGfYLFVlyU4SNiZo2SIJHIqr9RZiCHlaD8Po0=",
      "System.Collections.Concurrent.dll": "sha256-1DQHGUsrpx\/mZtuspx7IEWvkE9Zv2DuUNROTAEb9ZJY=",
      "System.Collections.Immutable.dll": "sha256-g350dh\/VTkEcPFFR4vHstjmhJ1l2EgopXTq\/yIt+xJQ=",
      "System.Collections.NonGeneric.dll": "sha256-\/+QHJftXvd83tuYCk5udahPfHfZjw8u+eFRtUf8ufjE=",
      "System.Collections.Specialized.dll": "sha256-bk2ntDFgfZshpgKJxYI9l4wn9cYq\/vIicaLknySuzyc=",
      "System.Collections.dll": "sha256-plWtdVlc96bY98XYkQjr6RPsxOyyexEYx0xumriD9o0=",
      "System.ComponentModel.Annotations.dll": "sha256-\/4QVH\/b7c6yfBAShkXdGdj3FSNQFVt3QfBU3qAl8wTs=",
      "System.ComponentModel.DataAnnotations.dll": "sha256-hXD9dobz1QSX1rO\/HtSor2DjQYFlrQy\/\/47dVC1XNW0=",
      "System.ComponentModel.EventBasedAsync.dll": "sha256-pIWFz52+cEMtDhKMGADByiGtWuX3InayhEpvPuBfnR4=",
      "System.ComponentModel.Primitives.dll": "sha256-h9zhj7Ec+X45gtOTKnLkuZc+1E3LWL9xaS3ZzUZ\/L5Y=",
      "System.ComponentModel.TypeConverter.dll": "sha256-XgyEa4+rXP0HZd8Mya+DSGZxSk\/h+OAaIaeHEZkvtS8=",
      "System.ComponentModel.dll": "sha256-P\/ZyJSylT7ByhRi7y1D6fjJrCiNWjY6E4YyqFDe8iJc=",
      "System.Configuration.dll": "sha256-5aFuY6vHcMKXN94DbD2ljy2C9zJnt8vvZgoBVJ6bR4E=",
      "System.Console.dll": "sha256-63jIoqF77sGHJLlk9LjKELcF\/gB6cSKqbavuYlQrkl0=",
      "System.Core.dll": "sha256-vRhwXIOlPNGiJOo5lTGbCyrdbtxYTUOlLae8rEowtow=",
      "System.Data.Common.dll": "sha256-vBYFRMPMjPReegS\/QIWv8ET0IHsJ5HUruS7p\/RnmoUE=",
      "System.Data.DataSetExtensions.dll": "sha256-9ouFWf4\/chBB5wePTXlEKsfka8Sf5oQLywhJ7IJTj6o=",
      "System.Data.dll": "sha256-VRNWxBfkFWO4hAecbuw97hECBJYjmPVoE9uOXObr53M=",
      "System.Diagnostics.Contracts.dll": "sha256-eOro5vVMpY2RRxW1Ats+bLLss1gmCP5Exy8KMCF3TQw=",
      "System.Diagnostics.Debug.dll": "sha256-Mx6Va5Ke2aE67yK1V5o\/58FbKzsU2pRq9ZixwmmJhhk=",
      "System.Diagnostics.DiagnosticSource.dll": "sha256-N82\/zZ6bTp5qm54XY2G5CFZszAivsPMC2tSNpjRQwiA=",
      "System.Diagnostics.FileVersionInfo.dll": "sha256-9MC78G0tIvG0OBWRA8wqYYgLWl3t+tu7CtfFkRrf3I4=",
      "System.Diagnostics.Process.dll": "sha256-6Kvb7HgBbCLdyvsizyuav8Ew6y9XzMDWcCgUlh1w5XU=",
      "System.Diagnostics.StackTrace.dll": "sha256-r62NWiDBYwfHlRL06iOFm5r8wtCRyTSzM8iSOsjm48I=",
      "System.Diagnostics.TextWriterTraceListener.dll": "sha256-6j1r9JB27fgi67gwKffLLfj\/PnE45LWyGXjdWRB5GQQ=",
      "System.Diagnostics.Tools.dll": "sha256-p3ebjyk5jy8c3DEJju5iB6ypoDzLBaaTuqWdKJLQreE=",
      "System.Diagnostics.TraceSource.dll": "sha256-aQY3ruS8l6F9q6HrwU1t26UQEkT14mANYQS2efB618o=",
      "System.Diagnostics.Tracing.dll": "sha256-O+PO3NIIFpTuOxFCwVtH5BdugCv+MHnfw673cC1YaE4=",
      "System.Drawing.Primitives.dll": "sha256-k3bnZCAtBzTDEO1VdrzVww1pIIX7Q6KdNYuuHIDBi1I=",
      "System.Drawing.dll": "sha256-cfyPcrn6meItuFqmvoDnn7Pm7mVZqN1nSPV0lFm5W64=",
      "System.Dynamic.Runtime.dll": "sha256-P8qbkVIFNYuDUt7AeLfhhH\/GuLS9fVp3ee0YfXekqdQ=",
      "System.Formats.Asn1.dll": "sha256-HQNpeMbYGwZV3AL0AzydH6mjoIzJ7kk8GbydgVJRldY=",
      "System.Globalization.Calendars.dll": "sha256-ccJNzFzMbv4aVE9H5q+mVz3ANi9X4HZdnz75EZ0zAgo=",
      "System.Globalization.Extensions.dll": "sha256-ojYst7YX13DyIPoHPp3WhSW3E+zP0Y7F\/K4MZfubDIw=",
      "System.Globalization.dll": "sha256-EDskdiGQt\/lxRFqgBkCSADLrcGhYo2fc5bdOeLp3\/o8=",
      "System.IO.Compression.Brotli.dll": "sha256-r0sZbHefR+zxVK2ToTDzpp62UPnlGBRMxsKwH7O8buM=",
      "System.IO.Compression.FileSystem.dll": "sha256-XvYbrvi94IC+cy1TGWQRC9kkKEM+cdCN1WjfHKmEiyw=",
      "System.IO.Compression.ZipFile.dll": "sha256-EuRd00rdJctu4zu1aFl6cIte4FS93zBW8Xe+turk4lk=",
      "System.IO.Compression.dll": "sha256-nuMLO7gHrv4iaBgQw5gACNBgUt\/oMqf00YTb7gZfjo8=",
      "System.IO.FileSystem.AccessControl.dll": "sha256-oCIdl7TDHWVsh0x\/lFLxFr5RcTescWhJxxBfYxRQ2P8=",
      "System.IO.FileSystem.DriveInfo.dll": "sha256-HaY7D6UIYI\/lDzKVY1CPUwssR8wAqau51Lw8+1Mn4Ng=",
      "System.IO.FileSystem.Primitives.dll": "sha256-2zHuiT7KjL5KljEI21zd4x3NQAoTgr19pjG59pvklVo=",
      "System.IO.FileSystem.Watcher.dll": "sha256-No9+dZk02ZrFKe1y3HivvcmPq1t3bpHCF7tmDoispxU=",
      "System.IO.FileSystem.dll": "sha256-jT3zgCoNgfNU3FX2nKgZi8iHTrWESGxqzDz3o6zbHOA=",
      "System.IO.IsolatedStorage.dll": "sha256-240Aw9Mpv7gn1l802RThaUyRwLC1BAxMESExQf5DeD0=",
      "System.IO.MemoryMappedFiles.dll": "sha256-8Utu35Cd+UGOn8FhgSgQPzMK1YRvlkfN54o\/+ArsY7w=",
      "System.IO.Pipes.AccessControl.dll": "sha256-Nf1ztWx2mHDcZK7qUD7RW\/Kiu+lcuKrJaPDDrt5ACZE=",
      "System.IO.Pipes.dll": "sha256-LYyJqrKb3d7p5Rka9wUtAyC89SpvFJ7bKruDQBoZkm0=",
      "System.IO.UnmanagedMemoryStream.dll": "sha256-dSVpQ5pFRhY5lxJ2StR+QpkprRTGEobF97HlZrlrTyU=",
      "System.IO.dll": "sha256-sMh00QNZvA5fnTerBTnAu4j5xBQKY17hJ1003af83uU=",
      "System.Linq.Expressions.dll": "sha256-QR+VKNXL1nuOtKF5Rt6Cv9FnXAdcvCRIY16s\/YdcW1s=",
      "System.Linq.Parallel.dll": "sha256-XBSLIvpQS2jO1P6u1VOR1KbOGi9SwncT7o082m1I0z8=",
      "System.Linq.Queryable.dll": "sha256-aWozxlAU9XAyZKmIZvo0Ls8TJVFAd0\/bbAEspQnyU3w=",
      "System.Linq.dll": "sha256-mZNofXWuF+qK4KbCra5T7274OI8MforBFrpJB1RmDd4=",
      "System.Memory.dll": "sha256-eIE3VkkVuFSeK80CNyN5sO\/aRy3vROXAND8tpjti+kc=",
      "System.Net.Http.Json.dll": "sha256-Re4jNYMjavVSEiIF04GSLzCpAO9xTn+FFBjyHz1ALuc=",
      "System.Net.Http.dll": "sha256-1eXLZRVYDxVtzdmt0XTxnSJtwPT0LawT0LS40pbwp2c=",
      "System.Net.HttpListener.dll": "sha256-0li0EC8npw6JYvdCQdNIYZ3BGuWrOwnrVgFdPj455R8=",
      "System.Net.Mail.dll": "sha256-96mhnzlP8\/r1US8ehOI8aKexrS91TRLsLSQmJa2\/9ho=",
      "System.Net.NameResolution.dll": "sha256-D7rNNBemHI7CZ3QD\/nhxtLC19Os0q8C5Z45+6oPXocA=",
      "System.Net.NetworkInformation.dll": "sha256-AEdL3RRLcc6Pe04yR2JJoj2TTKH+kZB3ZzghTMDHiN0=",
      "System.Net.Ping.dll": "sha256-REwr9U10bTNEaWhPddqS11rpokKHvPaEaFLnUDkY2a8=",
      "System.Net.Primitives.dll": "sha256-m8QHjTDQ1odVLRY0wYrCGy8v0r2hJePMg6djYpp1NK8=",
      "System.Net.Requests.dll": "sha256-QQeIoZktUvEBi9CDR12GEdevtpuTWU3jlSffq4WU3Ls=",
      "System.Net.Security.dll": "sha256-yK2F923LBxLmRTtUSBMpEyclH8EhHUvGYC0cfAsxsmU=",
      "System.Net.ServicePoint.dll": "sha256-S5wz\/TqYbT5ZcxNVsCgsdIqK8hnH3cydzSWfB+59JGA=",
      "System.Net.Sockets.dll": "sha256-2MnKeL8bqVEcvBzLHHDThKMh4kJxS7MqRHaEaw3DvaY=",
      "System.Net.WebClient.dll": "sha256-WeXVMv4QqSXENatsH9qMmHnjs9mi3gBKiZOcbmjRRFI=",
      "System.Net.WebHeaderCollection.dll": "sha256-yYD8LkeUaah32IW+6eHGCuHwzI1gMVI1iBI1WersEbE=",
      "System.Net.WebProxy.dll": "sha256-P5le18cxRGse36CUo2Ijdn1y5Yj7Zi6Vy3y1cO+tUL8=",
      "System.Net.WebSockets.Client.dll": "sha256-2aXJPOUzSjcr\/DAQTl8Usmpbjei5rv+Z50K\/RkN48fU=",
      "System.Net.WebSockets.dll": "sha256-2F7wLx5G9L4P8lmajVqLP2wqB5dTBSl5BPugXiM8Beo=",
      "System.Net.dll": "sha256-z5EIz7BdQOZUzyWVMQcnKsv+C8liDWQDDNFLzbLBbOg=",
      "System.Numerics.Vectors.dll": "sha256-RVDWQZwybU1T7JA24axvKKZ4gNL9YjGcqmctJaycxCg=",
      "System.Numerics.dll": "sha256-xMZmVPLy40iTKnnzlOJrUqo2GW+9\/hKZuUProZOlKy8=",
      "System.ObjectModel.dll": "sha256-fOPO2jTaXJ11Z\/ctHtQGjryhRlpR+4OpwxsFOnFpSXE=",
      "System.Private.DataContractSerialization.dll": "sha256-xUfZOJvPe3RWkcXKNrP831WaFYZ8LQ2ygTzycdNjliQ=",
      "System.Private.Runtime.InteropServices.JavaScript.dll": "sha256-TwhyOjyw\/t1oqYlPoTlGQ3kkbOGYdg2BCVBfXGVWT\/U=",
      "System.Private.Uri.dll": "sha256-t30bDa27pzmeRZHNoivkneQrLnFUasCPwhzq3HZWQTM=",
      "System.Private.Xml.Linq.dll": "sha256-Kq5QbQF7MaBPapfs2iFFZrHZzMx9EQbBGm\/qHhWvMiY=",
      "System.Private.Xml.dll": "sha256-sNKBw757AossmDoe2z+Ibjkf5aT61fbl1MqfI0IiCeE=",
      "System.Reflection.DispatchProxy.dll": "sha256-0ylRzzQANC3wvXSgk5AEcZQGPxQ0XdE4RGeRYnXOXgQ=",
      "System.Reflection.Emit.ILGeneration.dll": "sha256-hDyzSnWlWTbQSUryHFgm257jib27pw9KkQbugTDpxG0=",
      "System.Reflection.Emit.Lightweight.dll": "sha256-HB9tOUydeQVtSnh7JlmfXKFFX2+g8qml7fGaIvWYvFY=",
      "System.Reflection.Emit.dll": "sha256-EbGXdy5Omd\/o6cIw6AfzAhCkY\/EXQKR7r+KTAJWh3ig=",
      "System.Reflection.Extensions.dll": "sha256-S\/T+NGsBPA1FKIhnKNdbqJLG54qgTNspsI\/Bc34UvCo=",
      "System.Reflection.Metadata.dll": "sha256-\/cI+XTDQW\/09OGrodh4GyMAwmLJt89GtvVvF98OX5rQ=",
      "System.Reflection.Primitives.dll": "sha256-HVppx2fu5IlQrDN1mp4Co3u8fZdNyWCW5K4Jn68j+nI=",
      "System.Reflection.TypeExtensions.dll": "sha256-FNW7qYQylKoLtanq1V6oWBK+xla9aP0Bf7cE1soQgp0=",
      "System.Reflection.dll": "sha256-nbh3429RuBp77tFXD5f+2GbnYB4GeEtWIMGNJ4O6J44=",
      "System.Resources.Reader.dll": "sha256-s4d2Q4NH1D7RQ\/q9MeNWkd5gOW5Ala4lcs8Kipol5Ms=",
      "System.Resources.ResourceManager.dll": "sha256-MWlRrd3i0aNdqkq0ZfSATjMKuYJDAOFXY8kr5rDb0xA=",
      "System.Resources.Writer.dll": "sha256-R\/cVAeLoh2Wo5p1soRroZjYynJMFufnGl3oTSdkxrBs=",
      "System.Runtime.CompilerServices.Unsafe.dll": "sha256-xOzAgw4YllTtY5dxvkaL+bBakWnSjEOCLVm3WmG9Hew=",
      "System.Runtime.CompilerServices.VisualC.dll": "sha256-64MseFsKSo4fJo9+XHllJbaAOkikuRanX12kxHAB1ss=",
      "System.Runtime.Extensions.dll": "sha256-N+hMN6TqSS247PFKLrTW2GvusA7e1BD\/SCg\/AJNvdSQ=",
      "System.Runtime.Handles.dll": "sha256-zbxArgR8Bq8677AWVCoC9L7TumLsIhn4oqR+QcXDH\/E=",
      "System.Runtime.InteropServices.RuntimeInformation.dll": "sha256-7AG4IgI19b9Mxa3eAHUGGIrmTJ1bwjY51puL\/O2rCms=",
      "System.Runtime.InteropServices.dll": "sha256-tCS5vqTLsb780VPmr6zZ\/ZD6q6V90wtGgUBcLQKV\/YA=",
      "System.Runtime.Intrinsics.dll": "sha256-1xIyCjUKpb\/Wk6l\/\/g9N8\/B0zq1vWhKKT5rTpBKc\/fw=",
      "System.Runtime.Loader.dll": "sha256-s322UCNNFtMLPtH74ybprGOpAenhoC7FlU3uJtUYf8E=",
      "System.Runtime.Numerics.dll": "sha256-oBe9JDPomEjMOY6Jy5Rzy5THh6C+fwtwmQI0B7yZRnM=",
      "System.Runtime.Serialization.Formatters.dll": "sha256-syQOGf+UZDJJFavowgFmZUjoD5UqyuwzovstMG8huls=",
      "System.Runtime.Serialization.Json.dll": "sha256-R4HdbkMzJEBXmDCQh4o6J90lHF6hzuf6mdeSyUGeZA0=",
      "System.Runtime.Serialization.Primitives.dll": "sha256-jUi2q1kbxbgHf3APLNSqOewAZPK6ePQV\/Y3EmLvDuW0=",
      "System.Runtime.Serialization.Xml.dll": "sha256-S64QMr\/3yPcEBhKQR36UWmi6CsmzSt7geNE2ikxpMMQ=",
      "System.Runtime.Serialization.dll": "sha256-S21tLn\/wjul4vVuwyiXlgZAHTMHaWZ5NWo4d2XqPCds=",
      "System.Runtime.dll": "sha256-hkUkR0o\/EUy90UCmLwhKg29eW9Uq1G+2hw5yI8fqQbA=",
      "System.Security.AccessControl.dll": "sha256-lXjRcfPXnUubI4liDmluI91wEpMmXIuIQtzq4uz+1\/E=",
      "System.Security.Claims.dll": "sha256-IrIAmM1U9wM7\/57wAYAiIxzYKVZQ4ZRFQkeX8EjxN20=",
      "System.Security.Cryptography.Algorithms.dll": "sha256-OOc3FJpuiC2EAeXLEH9jCpky7rNmCf2y4eI1hvIdGcc=",
      "System.Security.Cryptography.Cng.dll": "sha256-IDva\/1eexgejbrb2pd4tGvmb8RpRLFtPQdKOWIfg52M=",
      "System.Security.Cryptography.Csp.dll": "sha256-I0sStjWvyER\/g1UAcQVHxlGP8WYPnn44nZyV2H6QLAQ=",
      "System.Security.Cryptography.Encoding.dll": "sha256-+30slIRCLurv028FqLXHLM7zGwRhSzKPgkz4qkOXGU0=",
      "System.Security.Cryptography.OpenSsl.dll": "sha256-6+guWgFku742cEMRLSjakHewNpKUChOBogCJr1SQYlE=",
      "System.Security.Cryptography.Primitives.dll": "sha256-eufChtizf\/fju66cjq3xgVKCY0\/yC+vnIyxSRyCSZJ0=",
      "System.Security.Cryptography.X509Certificates.dll": "sha256-P6zBjvyTDbh1nsfDJvlGxhrbV4MOm9\/tqlS1wKnICLE=",
      "System.Security.Principal.Windows.dll": "sha256-qG\/e0zJYRbHyAuuSDIAVVT4zDYzK6dOelzJY9EHnEFQ=",
      "System.Security.Principal.dll": "sha256-rgrGJ7Jy1iC3hTuJ0NxjQI9arq++FcNDNCtPfBgm38g=",
      "System.Security.SecureString.dll": "sha256-u8wie2itS0QgYE8Z5V1cYURbwzekLHjH3ULeWLa7RQo=",
      "System.Security.dll": "sha256-+bMm1weerGqqoFU1O3tqnk6afz1pwdDWoQ3gh2F7NZw=",
      "System.ServiceModel.Web.dll": "sha256-CBVh+lyviXYUGahDypluY9Z0bxw5bsazhaZ67SZ\/m8I=",
      "System.ServiceProcess.dll": "sha256-ObrNFHgy2AFOupUAPhqdZiauSIc+HLthrcfwhMZ6mPg=",
      "System.Text.Encoding.CodePages.dll": "sha256-vFAYLW61g74CrF5JDtFryR3VDo3C601Id2xATX\/OG2g=",
      "System.Text.Encoding.Extensions.dll": "sha256-7DVVBgKA1h4kVYSWFKgA0ehwJw1Ch0zjTYqhLozwTO4=",
      "System.Text.Encoding.dll": "sha256-g0djnHxQwr\/8z3gt5036ltfeOCiukDB74ZtkCr9k3HE=",
      "System.Text.Encodings.Web.dll": "sha256-5PrzT8OUL8av4T0GE9Yv\/JADt9mH9PL55AIUfCpXbF4=",
      "System.Text.Json.dll": "sha256-I7fC8A1gTkp5IHCSLdYv8lBxVBcvgv\/qPynNjMGxwc8=",
      "System.Text.RegularExpressions.dll": "sha256-djYleXTr7Uamm+EzSkA9e3cVrb6UGavE8GFOTarGKBk=",
      "System.Threading.Channels.dll": "sha256-II1Z1hlKcL98K5KsaUrfQT2KnkBvubTk8yL5vM+XO90=",
      "System.Threading.Overlapped.dll": "sha256-aek5eBYcHGUBoi4vxLqi4fjLuW8TXNNwfTlDk2vJ5dQ=",
      "System.Threading.Tasks.Dataflow.dll": "sha256-o4ntQJCPqewDh2YEkevZYtNL+aasZjjtk1wTpJpVGNU=",
      "System.Threading.Tasks.Extensions.dll": "sha256-XlTRz5WSLk9gN2uAfxD1EYJNvRrh0+6DzhkS8jDi+es=",
      "System.Threading.Tasks.Parallel.dll": "sha256-h0\/nVmiI7PVB2Tos17D\/74og+OmDu+JsCoXSHmr7O88=",
      "System.Threading.Tasks.dll": "sha256-FED4Gw+SuY8a75weI+V\/7NfCigOiBTM0GtX8MmeuaME=",
      "System.Threading.Thread.dll": "sha256-IwWzI0pRnUdmKGmAgI+p\/PrxA1wHcTK32p7K2SFKdak=",
      "System.Threading.ThreadPool.dll": "sha256-\/HMFOKLFhdin5KsvRIp7lZNSacXK5qeOEfV8V2UM+S0=",
      "System.Threading.Timer.dll": "sha256-3b\/gTvvFvw\/Lz4Sb5fCmUAoSvv9i4JPMdTwUxQX1bcU=",
      "System.Threading.dll": "sha256-ZDiYQKuQe6gS05zk8UntnCKDlfTZbOIWtELU\/yVQ8Ko=",
      "System.Transactions.Local.dll": "sha256-2o8XY+I5Mwai9hymKxjjIUvtSFqcw85z5lnuFB1wSBU=",
      "System.Transactions.dll": "sha256-cjsAkYOmGYiJOr71FGqGjCnw\/gj8OxyTjJwG56XjTfQ=",
      "System.ValueTuple.dll": "sha256-AjzmCQRcIZNbs1xK9cOM\/ndXTsD6HcYRgC7Vgj3usMA=",
      "System.Web.HttpUtility.dll": "sha256-t0tQVBglVqDw\/9l+IqqV4GxfqiwnjHI3AYqeWdLUWWQ=",
      "System.Web.dll": "sha256-GxHTbNOnsJPnOCfvCsdNjecDDj5MGS38+DEGT2BlxOU=",
      "System.Windows.dll": "sha256-jmZf0AK5pLklfLBG+cyWe3jY3IuWyIMHwWA2iP3jZ8c=",
      "System.Xml.Linq.dll": "sha256-GAkFnuE9zVjNkxGZqqTMedoDAzhM9ahvMSagamhkOdM=",
      "System.Xml.ReaderWriter.dll": "sha256-hHGof94rB58ENEmMeKaofrq3h4gEJr6K93lEC9g271c=",
      "System.Xml.Serialization.dll": "sha256-9+ZCQ4zSn3NbC\/nManDzMrzpw7rylfnR0++0kpfPbYo=",
      "System.Xml.XDocument.dll": "sha256-CvXjliGIezvScb6AUZOvD\/CUuMG6JS6kSm1QIYo34EQ=",
      "System.Xml.XPath.XDocument.dll": "sha256-bjSujhB4ust23EoYlGQYAPqLlvmLvc0UXSkZ+03EpVk=",
      "System.Xml.XPath.dll": "sha256-JOElDv2IjKmGuCNJqEFyyhEGPB7doy9WoxpF2DAnEGQ=",
      "System.Xml.XmlDocument.dll": "sha256-17JQqDvZBURrXjKqirmM+\/Pgr4VL5R6UIQSvU1cUGh0=",
      "System.Xml.XmlSerializer.dll": "sha256-gZP3A0FHfxOm6XYoJj35lsuRlsVudAO0PPzXjneAW2Q=",
      "System.Xml.dll": "sha256-GZy1hXhs2oSQCZokAn\/Sh5WqnBkPfrhU0IGzjBvV0aU=",
      "System.dll": "sha256-OOEghImLm2N7m3SVYkuSKqMFVoBDVnjZOMxQaiMVYEk=",
      "WindowsBase.dll": "sha256-HrFb5FWpXAvEO2lO5gi2rJPn9w4t71w3a0xEnrZdRiA=",
      "mscorlib.dll": "sha256-OoIy6q8Z4O5iSevqVXHfYC14mo\/kaxz74ydYX19CiAA=",
      "netstandard.dll": "sha256-FbW0xcZkySFb6B8yBpUE8XmaiMacDVdXdN9GeDH6xu4=",
      "System.Private.CoreLib.dll": "sha256-AQbVm+Z+NByScjRZWDWQnNUk5PBVgwCSWPLQN4YxxZs=",
      "RunningWebAssemblyInBrowser.dll": "sha256-ILu1dI7WEfCS42warORX3FWqhk3xQ6bIHHkKOl\/o41c="
    },
    "lazyAssembly": null,
    "pdb": {
      "RunningWebAssemblyInBrowser.pdb": "sha256-vfONhy33a3TutK1D+8w9Z0tPpet3tdhtpnTiLH8m7RA="
    },
    "runtime": {
      "dotnet.timezones.blat": "sha256-rFG55cwkAnfHzxmxwBL2tTwfj8X8cjcSEAlco4bc1eo=",
      "dotnet.wasm": "sha256-+NqBIg7FTMeA2q0mxzbehfuE2WPrnq53yqLsu47afbY=",
      "icudt.dat": "sha256-m7NyeXyxM+CL04jr9ui1Z6pVfMWwhHusuz5qNZWpAwA=",
      "icudt_CJK.dat": "sha256-91bygK5voY9lG5wxP0\/uj7uH5xljF9u7iWnSldT1Z\/g=",
      "icudt_EFIGS.dat": "sha256-DPfeOLph83b2rdx40cKxIBcfVZ8abTWAFq+RBQMxGw0=",
      "icudt_no_CJK.dat": "sha256-oM7Z6aN9jHmCYqDMCBwFgFAYAGgsH1jLC\/Z6DYeVmmk=",
      "dotnet.5.0.3.js": "sha256-sf1arnHfzSTa9MZNpdWBkQTecBMQBQRynOZ8+\/iLMUg="
    },
    "satelliteResources": null
  }
}
````
* These assemblies are stored as follows:

  ![{38 A D E A4 F A E C0 4926 9551 4 E1 C1449975 F}](https://github.com/iSatishYadav/RunningWebAssemblyInBrowser/raw/master/images/{38ADEA4F-AEC0-4926-9551-4E1C1449975F}.png)

> Notice the WASM file `dotnet.wasm`. This is where the magic happens. It's the CLR as a WASM module which runs our.NET Code on top of it.

## Can I use Webassebly today?
Check out this interactive usage chart from https://caniuse.com.
<p class="ciu_embed" data-feature="wasm" data-periods="future_1,current,past_1,past_2" data-accessible-colours="false">
<picture>
<source type="image/webp" srcset="https://caniuse.bitsofco.de/image/wasm.webp">
<source type="image/png" srcset="https://caniuse.bitsofco.de/image/wasm.png">
<img src="https://caniuse.bitsofco.de/image/wasm.jpg" alt="Data on support for the wasm feature across the major browsers from caniuse.com">
</picture>
</p>

## Live demo
Go to https://blog.satishyadav.com/RunningWebAssemblyInBrowser/hash a see the WASM in action. Go to Dev Tools are see for yourself.



## What's next?

* https://webassembly.org/
* https://dotnet.microsoft.com/learn/aspnet/blazor-tutorial/intro

Originally posted at: https://blog.satishyadav.com/What-is-Webassembly-and-how-do-I-create-a-Webassembly-application
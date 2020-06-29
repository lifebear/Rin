# lifebear ブランチ

.NET Core 3.x で動作するように修正しました。本家の vNext ブランチがリリースされれば、実質役目は終わりです。

メンテナンス方法

- 本家の vNext ブランチを監視する
- バージョンは被らないように 本家 + 100 としたうえで、プライベートな MyGet に push する

# <img src="docs/images/logo.svg" alt="" width="32" /> Rin
**R**equest/response **In**spector middleware for ASP.NET Core. like Glimpse.

[![Build Status](https://misuzilla.visualstudio.com/Rin/_apis/build/status/Rin-ASP.NET%20Core-CI)](https://misuzilla.visualstudio.com/Rin/_build/latest?definitionId=11)
[![NuGet version](https://badge.fury.io/nu/Rin.svg)](https://badge.fury.io/nu/Rin)

Rin captures HTTP requests to ASP.NET Core app and provides viewer for captured data. It's useful tool to debug your Web application (e.g. Web sites, API apps).

![](docs/images/Demo-01.gif)

# ✅ Features
## Capture requests and responses
Rin captures HTTP traffics between the ASP.NET Core app and any clients.

- Headers + Body
- Traces (`Microsoft.Extensions.Logging.ILogger`, log4net, ...)
- Unhandled Exception

## Inspect from Web browser in realtime

### View events timeline
Rin inspector displays events that occurred while processing a request.

![](docs/images/Screenshot-02.png)

### Preview a request/response body
Rin inspector can display request and response body with a preview. (e.g. JSON, Image, HTML, JavaScript ...)

![](docs/images/Screenshot-03.png)

### View related trace logs
Rin captures a request and response. Also, it captures logs while processing a request.

- Built-in `Microsoft.Extensions.Logging.ILogger` integration
- log4net Appender

### Save and export request/response
You can replay a request easily using cURL and LINQPad.

- Save request/response body
- Copy request as cURL and C#

### Integrate with ASP.NET Core MVC
- Record timings of view rendering and action execution
- In-View Inspector (like MiniProfiler)

![](docs/images/Screenshot-04.png)

# 📝 Requirements
- .NET Core 2.0+
- ASP.NET Core 2.1+
- ASP.NET Core MVC 2.1+
- Modern browser (e.g. Microsoft Edge, Google Chrome, Firefox, Safari...)
    - WebSocket connectivity

# ⚡ QuickStart

## Install NuGet Package
### Using Visual Studio
`Dependencies` -> `Manage NuGet Packages...` -> Search and install `Rin` and `Rin.Mvc` (if your project is built with ASP.NET Core MVC) package.

### Using dotnet command
```
dotnet add package Rin
dotnet add package Rin.Mvc
```

### Using Package Manager
```
Install-Package Rin
Install-Package Rin.Mvc
```

## Setup and configure Rin

### Program.cs
```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(configure =>
        {
            // Add: Enable Rin Logger
            configure.UseRinLogger();
        })
        .UseStartup<Startup>();
```

### Startup.cs

```csharp
public class Startup
{
    ...
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        
        services.AddMvc()
            // Add(option): Enable ASP.NET Core MVC support if the project built with ASP.NET Core MVC
            .AddRinMvcSupport()
            .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);

        // Add: Register Rin services
        services.AddRin();
    }
    ...
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            // Add: Enable request/response recording and serve a inspector frontend.
            // Important: `UseRin` (Middlewares) must be top of the HTTP pipeline.
            app.UseRin();

            // Add(option): Enable ASP.NET Core MVC support if the project built with ASP.NET Core MVC
            app.UseRinMvcSupport();

            app.UseDeveloperExceptionPage();

            // Add: Enable Exception recorder. this handler must be after `UseDeveloperExceptionPage`.
            app.UseRinDiagnosticsHandler();
        }
        ...
    }
}
```

### _Layout.cshtml (for ASP.NET Core MVC)
```cshtml
@inject Rin.Mvc.View.RinHelperService RinHelper
...
    <environment include="Development">
        <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
        <link rel="stylesheet" href="~/css/site.css" />
        @* Add: Enable In-View Inspector for ASP.NET Core MVC *@
        @RinHelper.RenderInViewInspector()
    </environment>
...
```

## Start the application and open Inspector on the web

Launch the app, then open `http://[Host:Port]/rin/` in the browser, you can see Rin Inspector now.

# 🔨 Develop and build Rin Inspector (client side)
Rin Inspector (client side) codes is separated from Rin core C# project. If you want to develop Rin (C#) or launch a sample project, you need to build and deploy the artifacts.

## [Rin.Frontend, Rin.Mvc.Frontend] Setup and start the development server
- `npm install`
- `npm run start`

## [Rin.Frontend] Build Rin/Resources.zip
- `npm run build`
- `npm run pack`

## [Rin.Mvc.Frontend] Build Rin.Mvc/EmbeddedResources
- `npm run build`
- `copy .\build\static\main.js* ..\Rin.Mvc\EmbeddedResources\`

# License
[MIT License](LICENSE)

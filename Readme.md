# NL.Serverless.AspNetCore
This repo provides code for hosting an AspNet Core App inside an Azure Function V3 HTTP Trigger with the new isolated worker.

## Prerequisites
1. Will to use the new isolated dotnet worker for functions (see the [repo](https://github.com/Azure/azure-functions-dotnet-worker))
1. .Net Core SDK >= 5.0.4
1. Install the [Azure Function Core Tools](https://learn.microsoft.com/en-us/azure/azure-functions/functions-run-local) for your preferred platform and language

## Instructions
1. Install the project template for dotnet and create a new project.
```
dotnet new install NL.Serverless.AspNetCore.Template
dotnet new serverless-aspnetcore -n Your.New.ProjectName
```
   Or, if you have cloned this repo locally, you can do
```
dotnet new install $path_to_this_repo/template
dotnet new serverless-aspnetcore -n Your.New.ProjectName
```
   A local edit-uninstall-install cycle goes like this:
```
dotnet new install $full_path_to_this_repo/template
dotnet new uninstall $full_path_to_this_repo/template
// make some edits
dotnet new serverless-aspnetcore
// Goto start
```

2. Happy coding your AspNet Core WebApp.
3. Run the Azure Function hosting the web app
```
func host start -build
```

## Deployment to Azure
1. Follow this instruction: https://github.com/Azure/azure-functions-dotnet-worker#create-the-azure-resources
2. Create an app setting within the function app.
   
   The name of the setting should be ``ASPNETCORE_TEST_CONTENTROOT_NAME_OF_YOUR_WEBAPP_ASSEMBLY`` and point the value to ``C:\home\site\wwwroot\``
   
   ![image](https://user-images.githubusercontent.com/6589385/131023539-7bb32c8d-4f47-4998-bf40-572299fc4836.png)


## Quirks
SignalR currently only works with Long Pooling.
Either connect with this transport method client side or configure your Hubs to just support Long Pooling (see [here](https://docs.microsoft.com/de-de/aspnet/core/signalr/configuration?view=aspnetcore-5.0&tabs=dotnet#advanced-http-configuration-options)).

## Application Insights / Logs
In order to see the logs of the Asp.Net Core app inside the log stream of Application Insights from the functions app you need to add the following lines:

1. Add the package reference of ``Microsoft.ApplicationInsights.AspNetCore`` to the .csproj of your web app
   
   ``<PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.17.0" />``

1. Register the Applications Insights services in the ``Statup.cs`` of your web app
   ```csharp
   services.AddApplicationInsightsTelemetry();
   ```

Please note that Application Insights has it´s own logging levels configured in the ``appsettings.json``. You can find the documentation [here](https://docs.microsoft.com/en-us/azure/azure-monitor/app/ilogger#control-logging-level).

Here is an example: 
```json
  ...
  "Logging": {
    "LogLevel": {
      "Default": "Information"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Information"
      }
    }
  },
  ...
```

If you want to use another instance of Application Insights for the web app you need to add the Instrumation Key in the ``appsettings.json``:
```json
   ...
      "ApplicationInsights": {
        "InstrumentationKey": "putinstrumentationkeyhere"
      },
   ...
```

## Samples
[Host the ASP.NET Boilerplate sample in an Azure Function](samples/ASP.NET%20Boilerplate)

## Credits
The basic idea for this is from this repo:
https://github.com/NicklausBrain/serverless-core-api

Thanks <a href="https://github.com/NicklausBrain">NicklausBrain</a>.

# How to create and deploy your first Azure Function with Visual Studio 2022 (.NET 9)

## 1. Download and install Visual Studio 2022 Community Edition (version 17.12)

https://visualstudio.microsoft.com/vs/preview/

![image](https://github.com/user-attachments/assets/138e866e-e58d-4468-a9a6-756dd1d69a0b)

## 2. Install Azure developting tools 

![image](https://github.com/user-attachments/assets/00327f33-1e76-4e18-94f2-8496685ae493)

## 3. Create a new project 

![image](https://github.com/user-attachments/assets/3a1f094d-492d-46a5-bbd6-8eb90319ea8b)

## 4. Select the Azure Function project template

![image](https://github.com/user-attachments/assets/6fdfe72a-14bb-43b2-b58f-36560d2183af)

## 5. Select the project name and location in your hard disk

![image](https://github.com/user-attachments/assets/e919b519-aba9-4644-8c64-b5c0df172c83)

## 6. Select the .NET 9 Framework and leave other options with the default values. Press the Create button

![image](https://github.com/user-attachments/assets/65f8c82e-5ab5-45af-8e6c-a97dec5e8cc0)

## 7. See the project folders and files structure

![image](https://github.com/user-attachments/assets/1679a177-9cb1-469b-9f28-f0be5a524e79)

## 8. Update the Nuget packages. Right click on Dependencies and select the menu option Manage Nuget Packages...

![image](https://github.com/user-attachments/assets/04b07d06-769f-41b9-abc3-727910e6e1e6)

## 9. Press in the Updates tab, select update all packages and then press the Update button

![image](https://github.com/user-attachments/assets/aee3f584-2d93-4068-9493-740a66caf06c)

## 10. Verify we updated all the Nuget Packages

![image](https://github.com/user-attachments/assets/90209b7d-cca9-47a6-a6fe-2a0483e0dbc7)

## 11. See the Azure Function source code

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.Functions.Worker;
using Microsoft.Extensions.Logging;

namespace FunctionApp1
{
    public class Function1
    {
        private readonly ILogger<Function1> _logger;

        public Function1(ILogger<Function1> logger)
        {
            _logger = logger;
        }

        [Function("Function1")]
        public IActionResult Run([HttpTrigger(AuthorizationLevel.Function, "get", "post")] HttpRequest req)
        {
            _logger.LogInformation("C# HTTP trigger function processed a request.");
            return new OkObjectResult("Welcome to Azure Functions!");
        }
    }
}
```

This code defines an Azure Function written in C# using the .NET 9 framework, specifically for **HTTP-triggered** functions

**Microsoft.AspNetCore.Http, Microsoft.AspNetCore.Mvc**: These namespaces provide classes for handling HTTP requests and returning responses.

**Microsoft.Azure.Functions.Worker**: Contains classes related to Azure Functions, including triggers.

**Microsoft.Extensions.Logging**: Provides logging capabilities to log information during function execution.

**Class Definition (Function1)**: This class represents the Azure Function. It has a constructor that accepts a logger (ILogger<Function1>) to log messages during the function's execution

**Function Constructor**: The constructor initializes the _logger field with an instance of a logger, enabling the function to log messages

The **[Function("Function1")]** attribute defines this method as an Azure Function named "Function1"

The method **Run** is the function's entry point. It accepts an HttpRequest object (triggered by either a **GET or POST HTTP request**), and it's decorated with the **[HttpTrigger]** attribute

The **AuthorizationLevel.Function** specifies that the function requires a valid function key for access

**Logging and Response**: Inside the Run method, the function logs a message using **_logger.LogInformation**

The method returns an **OkObjectResult** with a message "Welcome to Azure Functions!", which corresponds to an HTTP 200 OK response

## 12. Configure and register the Services in the middleware (Program.cs)

```csharp
using Microsoft.Azure.Functions.Worker;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

var host = new HostBuilder()
    .ConfigureFunctionsWebApplication()
    .ConfigureServices(services =>
    {
        services.AddApplicationInsightsTelemetryWorkerService();
        services.ConfigureFunctionsApplicationInsights();
    })
    .Build();

host.Run();
```

This code sets up and runs an Azure Functions application using the Azure Functions Worker SDK in a .NET 9 application

It primarily focuses on configuring the application's host and setting up services such as **Application Insights** for telemetry and logging

**Microsoft.Azure.Functions.Worker**: This provides functionality for building Azure Functions using the .NET Worker model, which allows for a decoupled, flexible environment outside of traditional ASP.NET Core

**Microsoft.Extensions.DependencyInjection**: This allows for dependency injection, letting you register services that the application can use

**Microsoft.Extensions.Hosting**: This namespace helps create and manage a host for the application, setting up the environment and services needed to run it

The **HostBuilder** is used to configure and build the **host** for the Azure Functions application

**.ConfigureFunctionsWebApplication()**: This method configures the application for Azure Functions, essentially setting it up to act as a Functions app

**ConfigureServices**: Inside this method, **services are registered** to the application's dependency injection (DI) container

**services.AddApplicationInsightsTelemetryWorkerService()**: This adds support for Application Insights telemetry, a monitoring tool that helps track and analyze application performance, exceptions, and logs

**services.ConfigureFunctionsApplicationInsights()**: Configures Application Insights specifically for Azure Functions to capture function-specific telemetry

**Build()**: After the host configuration is defined (including functions setup and telemetry services), the Build() method is called to **build the host**. This prepares the Azure Functions app for execution.

**host.Run()**: This method **runs the Azure Functions application**, making it ready to respond to function triggers (like HTTP requests, timer triggers, etc.).

## 13. Configuration files

### 13.1. launchSettings.json

This file is typically used in .NET projects **for configuring how the project is launched** when you run it locally (such as in Visual Studio or with .NET CLI)

```json
{
  "profiles": {
    "FunctionApp1": {
      "commandName": "Project",
      "commandLineArgs": "--port 7276",
      "launchBrowser": false
    }
  }
}
```

This file helps set up how the Azure Function is run locally, defining the port and behavior (like whether to open the browser)

**"commandName"**: "Project": Specifies that this project should be run directly when launched (in the case of Azure Functions, the function app is hosted)

**"commandLineArgs"**: "--port 7276": This argument tells the local development environment to start the function app on port 7276

**"launchBrowser"**: false: Indicates that the browser should not be launched when the project runs (since Azure Functions might not need a browser to be launched when testing locally)

### 13.3. serviceDependencies.json

This file defines external service dependencies (like Azure services) that your project relies on. 

```json
{
  "dependencies": {
    "appInsights1": {
      "type": "appInsights"
    },
    "storage1": {
      "type": "storage",
      "connectionId": "AzureWebJobsStorage"
    }
  }
}
```

In this case, it defines two services:

**"appInsights1"**: Indicates that the project uses Application Insights for logging and telemetry. The type is "appInsights"

**"storage1"**: Indicates that Azure Storage is required for the function. The connection to Azure Storage is through the AzureWebJobsStorage connection string, which is typically defined in local.settings.json or an environment variable

This file defines external services (like Azure Application Insights and Azure Storage) that your Azure Function depends on, helping manage and configure those dependencies

### 13.4. host.json 

The host.json file is used to configure the behavior of the Azure Functions runtime

```json
{
  "version": "2.0",
  "logging": {
      "applicationInsights": {
          "samplingSettings": {
              "isEnabled": true,
              "excludedTypes": "Request"
          },
          "enableLiveMetricsFilters": true
      }
  }
}
```

This file **configures logging and telemetry** for Azure Functions, particularly how data is handled by **Application Insights** (e.g., sampling to control the amount of telemetry data)

**"version"**: "2.0": Specifies that this is for version 2.0 of the Azure Functions runtime

**"logging"**: Configures logging behavior, particularly for Application Insights

**"samplingSettings"**: Enables sampling for telemetry data to reduce the amount of telemetry data sent to Application Insights

**"isEnabled"**: true: Enables sampling (reduces the amount of data sent to Application Insights, making telemetry more efficient)

**"excludedTypes"**: "Request": Excludes Request logs from being sampled

**"enableLiveMetricsFilters"**: true: Enables live metrics filters for Application Insights, allowing live monitoring of application performance and errors


### 13.5. local.settings.json

This file is used for **local development settings**, such as **connection strings** and **runtime-specific configurations**

When running locally, Azure Functions will read this file for settings

```json
{
  "IsEncrypted": false,
  "Values": {
      "AzureWebJobsStorage": "UseDevelopmentStorage=true",
      "FUNCTIONS_WORKER_RUNTIME": "dotnet-isolated"
  }
}
```

This file provides **local configuration settings**, such as **storage connection strings** and function **runtime configuration**. It’s primarily used during local development and testing

**"IsEncrypted"**: false: Specifies whether this file is encrypted (for local development, this is typically false)

**"AzureWebJobsStorage"**: "UseDevelopmentStorage=true": This sets the AzureWebJobsStorage connection string to use local development storage (i.e., Azure Storage Emulator or Azurite)

**"FUNCTIONS_WORKER_RUNTIME"**: "dotnet-isolated": Specifies the runtime environment for the Azure Functions. In this case, it uses .NET Isolated Worker, which decouples the Azure Functions runtime from the .NET process, allowing more flexibility in .NET versions and dependency management

## 14. Run your application and hit the Azure Function EndPoint

Press the running button in Visual Studio 2022 IDE

![image](https://github.com/user-attachments/assets/9d306e8a-8a17-4d99-a249-0e56a5c1ae27)

Click on the Azure Function Endpoint and see the result

![image](https://github.com/user-attachments/assets/fc1b5999-97c0-4796-adc4-41dc7283e081)

![image](https://github.com/user-attachments/assets/0035000f-3549-4681-bd15-84399a65a036)

## 15. How to deploy the Azure Function from Visual Studio 2022

Right click on the Azure Function project name and the select the menu option **Publish...**

![image](https://github.com/user-attachments/assets/4961ebe3-21eb-4b7c-b61b-fcacca878366)

Select where you would like to deploy your Azure Function and press the Next button

![image](https://github.com/user-attachments/assets/f93eb6aa-c73a-411a-af7a-8d2a6de3cd4c)

Select the Azure Service where to deploy your Azure Function

![image](https://github.com/user-attachments/assets/36882f79-6596-4485-9fad-fe196893ca43)

Create a new Azure Function instance in case there was not created previously in Azure Portal or with Azure CLI

![image](https://github.com/user-attachments/assets/1258c45b-e512-4db9-b16d-ad48dc778dbb)

![image](https://github.com/user-attachments/assets/6936460f-ea58-4c59-abb3-1f78c5aeacb1)

![image](https://github.com/user-attachments/assets/1c0de0ea-119b-4ee2-8a99-f80effd26214)

After creating the publishing profile press the Publish button

![image](https://github.com/user-attachments/assets/a4641fed-e870-47eb-b4e2-5a0e573b4ba0)

![image](https://github.com/user-attachments/assets/ebd85729-bb7a-46cc-bdac-e559943aba59)

Now we verify the deployment in Azure Portal

We press in the Function name **Function1**

![image](https://github.com/user-attachments/assets/80208412-81d2-486e-854e-2b58bb32517d)

Now press in the **Test/Run** button

![image](https://github.com/user-attachments/assets/0d16e958-d268-43d8-ad9f-484681519bf0)

And press in the **Run** button

![image](https://github.com/user-attachments/assets/be86bc9f-b6e0-4da1-b898-cd959c7cc7cc)

See the Azure Function output

![image](https://github.com/user-attachments/assets/0aaaa6a7-a0b0-42a4-ac05-5e7392d3f589)

## 16. (OPTIONAL) You can create an Azure Function in Azure Portal and the deploy your Azure Function from Visual Studio 2022

First create an Azure Function in Azure Portal 

It is important to select the same .NET Runtime Framework version in Azure Portal as we used for creating the Azure Function in Visual Studio 2022 IDE

![image](https://github.com/user-attachments/assets/7ba466cb-6d6d-4a70-8145-327caec39e69)

We select the **Consumption** and press the Select button 

![image](https://github.com/user-attachments/assets/b621aa09-a778-4dca-8e7b-8b2849f485e3)

We create a new Resource Group where to place all the services related to my new Azure Function

![image](https://github.com/user-attachments/assets/0147ea4a-704d-4b57-9a45-7bcb68d6bc8e)

![image](https://github.com/user-attachments/assets/4ea06800-a213-4a96-a281-1ac565180de9)

![image](https://github.com/user-attachments/assets/dbed164d-38b5-4caf-8567-0273cc99df31)

![image](https://github.com/user-attachments/assets/01dcb6ff-268a-4f5e-8644-0eb7f7f56f2f)

Select the Azure Function created in the Azure Portal

![image](https://github.com/user-attachments/assets/833de611-00a4-4cc3-977f-417fd6df9168)

After creating the publishing profile press the Publish button

![image](https://github.com/user-attachments/assets/4fa9e5ed-7a35-4de9-9923-14bf13bdba7a)

![image](https://github.com/user-attachments/assets/f52475ca-402c-4e3d-a6f3-08077a5b4bd4)

Now we verify the deployment in Azure Portal

We press in the Function name **Function1**

![image](https://github.com/user-attachments/assets/80208412-81d2-486e-854e-2b58bb32517d)

Now press in the **Test/Run** button

![image](https://github.com/user-attachments/assets/0d16e958-d268-43d8-ad9f-484681519bf0)

And press in the **Run** button

![image](https://github.com/user-attachments/assets/be86bc9f-b6e0-4da1-b898-cd959c7cc7cc)

See the Azure Function output

![image](https://github.com/user-attachments/assets/0aaaa6a7-a0b0-42a4-ac05-5e7392d3f589)

## 17. (OPTIONAL) You can compress your Azure Function as a ZIP file and deploy it with Azure CLI commands

We right click on the Auzre Function name and select Publish

![image](https://github.com/user-attachments/assets/9026e267-4947-4cff-9c3e-e758ce5bbd84)

Then we select the Folder option for publising and press the Next button

![image](https://github.com/user-attachments/assets/a2b56f38-a9ad-4753-a7bd-8be28e2b8ff1)

We search for the folder where to publish the Azure Function

![image](https://github.com/user-attachments/assets/21f89ae6-5c69-47f2-8d43-6e659d496fed)

And press in the Finish button

![image](https://github.com/user-attachments/assets/5024a36d-e702-41c7-9c10-bfef80e23fa8)

Now  press in the Publish botton

![image](https://github.com/user-attachments/assets/1c58faed-be7a-4b33-984b-e19de56f3fcb)

![image](https://github.com/user-attachments/assets/d29ab0db-90bb-4a79-a083-51c57144c576)

Verify the Publish folder

![image](https://github.com/user-attachments/assets/de636ffa-c5bb-4703-813e-28f4fb214ef8)

Create the ZIP file with Winrar

![image](https://github.com/user-attachments/assets/b1b56266-3b61-44f4-be29-e2bfa161ce9f)

See the ZIP file

![image](https://github.com/user-attachments/assets/f144f636-f218-4a1d-b56d-517f0e30df41)








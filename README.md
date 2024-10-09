# How to create your first Azure Function with Visual Studio 2022 and .NET 9

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

## 11. See the Azure Function source code:

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

This code defines an Azure Function written in C# using the .NET Core framework, specifically for HTTP-triggered functions. Here's a brief breakdown of its components:

Namespaces:

Microsoft.AspNetCore.Http, Microsoft.AspNetCore.Mvc: These namespaces provide classes for handling HTTP requests and returning responses.
Microsoft.Azure.Functions.Worker: Contains classes related to Azure Functions, including triggers.
Microsoft.Extensions.Logging: Provides logging capabilities to log information during function execution.
Class Definition (Function1):

This class represents the Azure Function.
It has a constructor that accepts a logger (ILogger<Function1>) to log messages during the function's execution.
Function Constructor:

The constructor initializes the _logger field with an instance of a logger, enabling the function to log messages.
Run Method:

The [Function("Function1")] attribute defines this method as an Azure Function named "Function1".
The method Run is the function's entry point. It accepts an HttpRequest object (triggered by either a GET or POST HTTP request), and it's decorated with the [HttpTrigger] attribute.
The AuthorizationLevel.Function specifies that the function requires a valid function key for access.
Logging and Response:

Inside the Run method, the function logs a message using _logger.LogInformation.
The method returns an OkObjectResult with a message "Welcome to Azure Functions!", which corresponds to an HTTP 200 OK response.
Summary:
This code defines an HTTP-triggered Azure Function that logs a message when it processes a request and responds with a welcome message to the client.

## 12. Configure and register the Services in the middleware (Program.cs)

## 13. 

## 14. Run your application and hit the Azure Function EndPoint




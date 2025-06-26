# Service Defaults aka Smart Defaults

## Introduction

.NET Aspire provides a set of smart defaults for services that are commonly used in .NET applications. These defaults are designed to help you get started quickly and provide a consistent experience across different types of applications. This includes:

- Telemetry: Metrics, Tracing, Logging
- Resiliency
- Health Checks
- Service Discovery

## Create Service Defaults Project

In this lab, we will be working with Visual Studio 2022.  You can find the **Sample Project** on the desktop of the lab machine and double-clicking that icon will open the project in Visual Studio.

1. [] Add a new project to the solution called `ServiceDefaults`:
   - Right-click on the solution and select **Add** > **New Project**.
   - Search for and select the **.NET Aspire Service Defaults** project template.
   - Name the project `ServiceDefaults`.
   - Keep the default location suggested in the dialog
   - Click **Next**
   - Select **.NET 9.0** for the framework version
   - .NET Aspire version should be set to **9.3**
   - Click **Create**.

    *Visual Studio*
    ![Visual Studio dialog to add a service defaults project](./images/vs-add-servicedefaults.png)

## Configure Service Defaults

1. [] Add a reference to the **ServiceDefaults** project in the **Api** and **MyWeatherHub** projects:
   - Right-click on the **Api** project and select **Add** > **Project Reference...**.
     - Check the **ServiceDefaults** project and click **OK**.
   - Right-click on the **MyWeatherHub** project and select **Add** > **Project Reference...**.
     - Check the **ServiceDefaults** project and click **OK**.

   > Pro Tip: In Visual Studio 2022, you can drag and drop the project onto another project to add a reference.

1. [] In both the **Api** and **MyWeatherHub** projects, update their **Program.cs** files, adding the following line immediately after their **var builder = WebApplication.CreateBuilder(args);** line:

   ```csharp
   builder.AddServiceDefaults();
   ```
1. [] In both the **Api** and **MyWeatherHub** projects, update their **Program.cs** files to add a reference to the new Default Endpoints added by **ServiceDefaults**.  Add this line just before the last `Map*` command in the **Program.cs** file:

 ```csharp
 app.MapDefaultEndpoints();
 ```

## Run the application

1. [] Run the application using a multiple-project launch configuration in Visual Studio:
   - Right click on the **MyWeatherHub** solution and go to properties. Select the **Api** and **MyWeatherHub** as startup projects, select **OK**.
     - ![Visual Studio solution properties](./images/vs-multiproject.png)
     - Click **Start** to start and debug both projects.
2. [] Test the application by navigating to the following URLs:
   - `https://localhost:7032/openapi/v1.json` - API
   - `https://localhost:7274/` - MyWeatherHub
3. [] You should see the API and the MyWeatherHub home page.
   1. [] You can also view the health checks for the API by navigating to `https://localhost:7032/health`.
4. [] You can also view the health checks for the MyWeatherHub by navigating to `https://localhost:7274/health`.
5. [] View the logs in the terminal to see the health checks and other telemetry data such as resiliency with Polly:

   ```bash-nocode
   Polly: Information: Execution attempt. Source: '-standard//Standard-Retry', Operation Key: '', Result: '200', Handled: 'False', Attempt: '0', Execution Time: '13.0649'
   ```

6. [] Click on 5 different cities and a "random" error will be thrown. You will see the Polly retry policy in action.

   ```bash-nocode
   Polly: Warning: Execution attempt. Source: '-standard//Standard-Retry', Operation Key: '', Result: '500', Handled: 'True', Attempt: '0', Execution Time: '9732.8258'
   Polly: Warning: Resilience event occurred. EventName: 'OnRetry', Source: '-standard//Standard-Retry', Operation Key: '', Result: '500'
   System.Net.Http.HttpClient.NwsManager.ClientHandler: Information: Sending HTTP request GET http://localhost:5271/forecast/AKZ318
   ```

## Summary

Introducing .NET Aspire smart defaults with the **ServiceDefaults** project template allows us to see the health of our applicaton with standard health-checks and resiliency with libraries like Polly.  We saw that introducing the retry mechanisms with Polly made our Api more robust and delivered more reliable results for our web users.
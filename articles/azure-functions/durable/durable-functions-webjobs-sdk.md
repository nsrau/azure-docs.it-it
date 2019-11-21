---
title: How to run Durable Functions as WebJobs - Azure
description: Imparare a codificare e configurare Funzioni durevoli per l'esecuzione in processi Web tramite WebJobs SDK.
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: d8dd0c86fbc520d0bd3ef6034891bd9871774b4a
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232734"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>How to run Durable Functions as WebJobs

By default, Durable Functions uses the Azure Functions runtime to host orchestrations. However, there may be certain scenarios where you need more control over the code that listens for events. This article shows you how to implement your orchestration using the WebJobs SDK. To see a more detailed comparison between Functions and WebJobs, see [Compare Functions and WebJobs](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

[Funzioni di Azure](../functions-overview.md) e l'estensione [Funzioni durevoli](durable-functions-overview.md) sono basate su [WebJobs SDK](../../app-service/webjobs-sdk-how-to.md). The job host in the WebJobs SDK is the runtime in Azure Functions. If you need to control behavior in ways not possible in Azure Functions, you can develop and run Durable Functions by using the WebJobs SDK yourself.

In version 3.x of the WebJobs SDK, the host is an implementation of `IHost`, and in version 2.x you use the `JobHost` object.

The chaining Durable Functions sample is available in a WebJobs SDK 2.x version: download or clone the [Durable Functions repository](https://github.com/azure/azure-functions-durable-extension/), and go to the *samples\\webjobssdk\\chaining* folder.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si abbia familiarità con le nozioni di base relative a WebJobs SDK, allo sviluppo della libreria di classi C# per Funzioni di Azure e a Funzioni durevoli. Per un'introduzione a questi argomenti, vedere le risorse seguenti:

* [Get started with the WebJobs SDK](../../app-service/webjobs-sdk-get-started.md) (Introduzione a WebJobs SDK)
* [Creare la prima funzione con Visual Studio](../functions-create-your-first-function-visual-studio.md)
* [Funzioni durevoli](durable-functions-sequence.md)

Per seguire la procedura descritta in questo articolo:

* [Install Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/) with the **Azure development** workload.

  If you already have Visual Studio, but don't have that workload, add the workload by selecting **Tools** > **Get Tools and Features**.

  È anche possibile usare [Visual Studio Code](https://code.visualstudio.com/), ma alcune istruzioni sono specifiche per Visual Studio.

* Installare ed eseguire [Emulatore di archiviazione di Azure](../../storage/common/storage-use-emulator.md) versione 5.2 o successive. In alternativa, è possibile aggiornare il file *App.config* con una stringa di connessione di Archiviazione di Azure.

## <a name="webjobs-sdk-versions"></a>Versioni di WebJobs SDK

Questo articolo illustra come sviluppare un progetto di WebJobs SDK 2.x (equivalente a Funzioni di Azure versione 1.x). Per informazioni sulla versione 3.x, vedere [WebJobs SDK 3.x](#webjobs-sdk-3x) più avanti in questo articolo.

## <a name="create-a-console-app"></a>Creare un'app console

To run Durable Functions as WebJobs, you must first create a console app. Un progetto di WebJobs SDK è semplicemente un progetto di app console con i pacchetti NuGet appropriati installati.

In the Visual Studio **New Project** dialog box, select **Windows Classic Desktop** > **Console App (.NET Framework)** . Nel file di progetto `TargetFrameworkVersion` deve essere `v4.6.1`.

Visual Studio also has a WebJob project template, which you can use by selecting **Cloud** > **Azure WebJob (.NET Framework)** . Questo modello comporta l'installazione di molti pacchetti, alcuni dei quali potrebbero non essere necessari.

## <a name="install-nuget-packages"></a>Installare i pacchetti NuGet

I pacchetti NuGet sono necessari per WebJobs SDK, le associazioni dei componenti di base, il framework di registrazione e l'estensione Durable Task. Here are **Package Manager Console** commands for those packages, with the latest stable version numbers as of the date this article was written:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.8.3
```

Occorre anche disporre di provider di registrazione. The following commands install the Azure Application Insights provider and the `ConfigurationManager`. Con `ConfigurationManager` è possibile ottenere la chiave di strumentazione di Application Insights dalle impostazioni dell'app.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

Il comando seguente consente di installare il provider della console:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>Codice JobHost

Having created the console app and installed the NuGet packages you need, you're ready to use Durable Functions. You do so by using JobHost code.

Per usare l'estensione Funzioni durevoli, chiamare `UseDurableTask` nell'oggetto `JobHostConfiguration` nel metodo `Main`:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

Per un elenco di proprietà che è possibile impostare nell'oggetto `DurableTaskExtension`, vedere [host.json](../functions-host-json.md#durabletask).

Il metodo `Main` consente di impostare anche i provider di registrazione. The following example configures the console and Application Insights providers.

```cs
static void Main(string[] args)
{
    using (var loggerFactory = new LoggerFactory())
    {
        var config = new JobHostConfiguration();

        config.DashboardConnectionString = "";

        var instrumentationKey =
            ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];

        config.LoggerFactory = loggerFactory
            .AddApplicationInsights(instrumentationKey, null)
            .AddConsole();

        config.UseTimers();
        config.UseDurableTask(new DurableTaskExtension
        {
            HubName = "MyTaskHub",
        });
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="functions"></a>Funzioni

Durable Functions in the context of WebJobs differs somewhat from Durable Functions in the context of Azure Functions. It's important to be aware of the differences as you write your code.

WebJobs SDK non supporta le funzionalità di Funzioni di Azure seguenti:

* [Attributo FunctionName](#functionname-attribute)
* [Trigger HTTP](#http-trigger)
* [API di gestione HTTP per Funzioni durevoli](#http-management-api)

### <a name="functionname-attribute"></a>Attributo FunctionName

In un progetto di WebJobs SDK il nome del metodo di una funzione è il nome della funzione. L'attributo `FunctionName` viene usato solo in Funzioni di Azure.

### <a name="http-trigger"></a>Trigger HTTP

WebJobs SDK non dispone di un trigger HTTP. Il client di orchestrazione del progetto di esempio usa un trigger timer:

```cs
public static async Task CronJob(
    [TimerTrigger("0 */2 * * * *")] TimerInfo timer,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger logger)
{
  ...
}
```

### <a name="http-management-api"></a>API di gestione HTTP

Non disponendo di un trigger HTTP, WebJobs SDK non ha un'[API di gestione HTTP](durable-functions-http-api.md).

In a WebJobs SDK project, you can call methods on the orchestration client object, instead of by sending HTTP requests. I metodi seguenti corrispondono alle tre attività eseguibili con l'API di gestione HTTP:

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

The orchestration client function in the sample project starts the orchestrator function, and then goes into a loop that calls `GetStatusAsync` every 2 seconds:

```cs
string instanceId = await client.StartNewAsync(nameof(HelloSequence), input: null);
logger.LogInformation($"Started new instance with ID = {instanceId}.");

DurableOrchestrationStatus status;
while (true)
{
    status = await client.GetStatusAsync(instanceId);
    logger.LogInformation($"Status: {status.RuntimeStatus}, Last update: {status.LastUpdatedTime}.");

    if (status.RuntimeStatus == OrchestrationRuntimeStatus.Completed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Failed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Terminated)
    {
        break;
    }

    await Task.Delay(TimeSpan.FromSeconds(2));
}
```

## <a name="run-the-sample"></a>Eseguire l'esempio

You've got Durable Functions set up to run as a WebJob, and you now have an understanding of how this will differ from running Durable Functions as standalone Azure Functions. At this point, seeing it work in a sample might be helpful.

Questa sezione fornisce una panoramica di come eseguire il [progetto di esempio](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining). Per istruzioni dettagliate che illustrano come eseguire un progetto di WebJobs SDK in locale e distribuirlo in un processo Web di Azure, vedere [Get started with the WebJobs SDK](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob) (Introduzione a WebJobs SDK).

### <a name="run-locally"></a>Esecuzione locale

1. Assicurarsi che l'emulatore di archiviazione sia in esecuzione (vedere [Prerequisiti](#prerequisites)).

1. If you want to see logs in Application Insights when you run the project locally:

    a. Create an Application Insights resource, and use the **General** app type for it.

    b. Salvare la chiave di strumentazione nel file *App.config*.

1. Eseguire il progetto.

### <a name="run-in-azure"></a>Esecuzione in Azure

1. Creare un'app Web e un account di archiviazione.

1. In the web app, save the storage connection string in an app setting named `AzureWebJobsStorage`.

1. Create an Application Insights resource, and use the **General** app type for it.

1. Save the instrumentation key in an app setting named `APPINSIGHTS_INSTRUMENTATIONKEY`.

1. Eseguire la distribuzione come processo Web.

## <a name="webjobs-sdk-3x"></a>WebJobs SDK 3.x

This article explains how to develop a WebJobs SDK 2.x project. If you're developing a [WebJobs SDK 3.x](../../app-service/webjobs-sdk-get-started.md) project, this section helps you understand the differences.

The main change introduced is the use of .NET Core instead of .NET Framework. To create a WebJobs SDK 3.x project, the instructions are the same, with these exceptions:

1. Creare un'app console di .NET Core. In the Visual Studio **New Project** dialog box, select  **.NET Core** > **Console App (.NET Core)** . Il file di progetto specifica che `TargetFramework` è `netcoreapp2.x`.

1. Choose the release version WebJobs SDK 3.x of the following packages:

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. Set the storage connection string and the Application Insights instrumentation key in an *appsettings.json* file, by using the .NET Core configuration framework. Ecco un esempio:

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. Change the `Main` method code to do this. Ecco un esempio:

   ```cs
   static void Main(string[] args)
   {
        var hostBuilder = new HostBuilder()
            .ConfigureWebJobs(config =>
            {
                config.AddAzureStorageCoreServices();
                config.AddAzureStorage();
                config.AddTimers();
                config.AddDurableTask(options =>
                {
                    options.HubName = "MyTaskHub";
                    options.AzureStorageConnectionStringName = "AzureWebJobsStorage";
                });
            })
            .ConfigureLogging((context, logging) =>
            {
                logging.AddConsole();
                logging.AddApplicationInsights(config =>
                {
                    config.InstrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                });
            })
            .UseConsoleLifetime();

        var host = hostBuilder.Build();

        using (host)
        {
            host.Run();
        }
   }
   ```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su WebJobs SDK, vedere [How to use the WebJobs SDK](../../app-service/webjobs-sdk-how-to.md) (Come usare WebJobs SDK).

---
title: Come eseguire funzioni durevoli come WebJobs - AzureHow to run Durable Functions as WebJobs - Azure
description: Imparare a codificare e configurare Funzioni durevoli per l'esecuzione in processi Web tramite WebJobs SDK.
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: d8dd0c86fbc520d0bd3ef6034891bd9871774b4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232734"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Come eseguire funzioni durevoli come WebJobs

Per impostazione predefinita, Funzioni permanenti usa il runtime di Funzioni di Azure per ospitare orchestrazioni. Tuttavia, ci possono essere alcuni scenari in cui è necessario un maggiore controllo sul codice in ascolto per gli eventi. In questo articolo viene illustrato come implementare l'orchestrazione utilizzando WebJobs SDK. Per un confronto più dettagliato tra Funzioni e WebJobs, vedere [Compare Functions e WebJobs](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

[Funzioni di Azure](../functions-overview.md) e l'estensione [Funzioni durevoli](durable-functions-overview.md) sono basate su [WebJobs SDK](../../app-service/webjobs-sdk-how-to.md). The job host in the WebJobs SDK is the runtime in Azure Functions. Se è necessario controllare il comportamento in modi non possibili in Funzioni di Azure, è possibile sviluppare ed eseguire funzioni durevoli usando WebJobs SDK manualmente.

Nella versione 3.x di WebJobs SDK, l'host è un'implementazione `IHost` `JobHost` di , e nella versione 2.x si utilizza l'oggetto.

L'esempio di concatenamento di funzioni durevoli è disponibile in una versione di WebJobs SDK 2.x: scaricare o clonare il [repository Funzioni permanenti](https://github.com/azure/azure-functions-durable-extension/)e passare alla cartella di *concatenamento samples\\webjobssdk\\* .

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si abbia familiarità con le nozioni di base relative a WebJobs SDK, allo sviluppo della libreria di classi C# per Funzioni di Azure e a Funzioni durevoli. Per un'introduzione a questi argomenti, vedere le risorse seguenti:

* [Get started with the WebJobs SDK](../../app-service/webjobs-sdk-get-started.md) (Introduzione a WebJobs SDK)
* [Creare la prima funzione con Visual Studio](../functions-create-your-first-function-visual-studio.md)
* [Funzioni permanenti](durable-functions-sequence.md)

Per seguire la procedura descritta in questo articolo:

* [Installare Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/) con il carico di lavoro di sviluppo di **Azure.Install** Visual Studio 2019 with the Azure development workload.

  Se si dispone già di Visual Studio, ma non del carico di lavoro, aggiungere il carico di lavoro selezionando **Strumenti** > **Ottieni strumenti e funzionalità**.

  È anche possibile usare [Visual Studio Code](https://code.visualstudio.com/), ma alcune istruzioni sono specifiche per Visual Studio.

* Installare ed eseguire [Emulatore di archiviazione di Azure](../../storage/common/storage-use-emulator.md) versione 5.2 o successive. In alternativa, è possibile aggiornare il file *App.config* con una stringa di connessione di Archiviazione di Azure.

## <a name="webjobs-sdk-versions"></a>Versioni di WebJobs SDK

Questo articolo illustra come sviluppare un progetto di WebJobs SDK 2.x (equivalente a Funzioni di Azure versione 1.x). Per informazioni sulla versione 3.x, vedere [WebJobs SDK 3.x](#webjobs-sdk-3x) più avanti in questo articolo.

## <a name="create-a-console-app"></a>Creare un'app console

Per eseguire Funzioni durevoli come Processi Web, è innanzitutto necessario creare un'app console. Un progetto di WebJobs SDK è semplicemente un progetto di app console con i pacchetti NuGet appropriati installati.

Nella finestra di dialogo **Nuovo progetto** di Visual Studio selezionare App console **desktop** > classica di Windows **(.NET Framework)**. Nel file di progetto `TargetFrameworkVersion` deve essere `v4.6.1`.

Visual Studio dispone anche di un modello di progetto WebJob, che è possibile usare selezionando **Cloud** > **Azure WebJob (.NET Framework)**. Questo modello comporta l'installazione di molti pacchetti, alcuni dei quali potrebbero non essere necessari.

## <a name="install-nuget-packages"></a>Installare i pacchetti NuGet

I pacchetti NuGet sono necessari per WebJobs SDK, le associazioni dei componenti di base, il framework di registrazione e l'estensione Durable Task. Ecco i comandi della Console di **gestione pacchetti** per tali pacchetti, con i numeri di versione stabili più recenti alla data in cui è stato scritto questo articolo:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.8.3
```

Occorre anche disporre di provider di registrazione. I comandi seguenti installano il `ConfigurationManager`provider di Azure Application Insights e il file . Con `ConfigurationManager` è possibile ottenere la chiave di strumentazione di Application Insights dalle impostazioni dell'app.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

Il comando seguente consente di installare il provider della console:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>Codice JobHost

Dopo aver creato l'app console e installato i pacchetti NuGet necessari, è possibile usare Funzioni durevoli. A tale scopo, utilizzare il codice JobHost.You do so by using JobHost code.

Per usare l'estensione Funzioni durevoli, chiamare `UseDurableTask` nell'oggetto `JobHostConfiguration` nel metodo `Main`:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

Per un elenco di proprietà che è possibile impostare nell'oggetto `DurableTaskExtension`, vedere [host.json](../functions-host-json.md#durabletask).

Il metodo `Main` consente di impostare anche i provider di registrazione. L'esempio seguente configura la console e i provider di Application Insights.

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

Funzioni durevoli nel contesto di WebJobs differisce in qualche modo da funzioni durevoli nel contesto di Funzioni di Azure.Durable Functions in the context of WebJobs differing somesome from Durable Functions in the context of Azure Functions. È importante essere consapevoli delle differenze durante la scrittura del codice.

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

In un progetto WebJobs SDK, è possibile chiamare i metodi sull'oggetto client dell'orchestrazione, anziché inviando richieste HTTP. I metodi seguenti corrispondono alle tre attività eseguibili con l'API di gestione HTTP:

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

La funzione client di orchestrazione nel progetto di esempio avvia la funzione `GetStatusAsync` dell'agente di orchestrazione e quindi passa in un ciclo che chiama ogni 2 secondi:The orchestration client function in the sample project starts the orchestrator function, and then goes into a loop that calls every 2 seconds:

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

Sono state configurate le funzioni durevoli per l'esecuzione come processo Web e si ha una conoscenza di come questa operazione differirà dall'esecuzione di funzioni durevoli come funzioni di Azure autonome. A questo punto, vederlo funzionare in un esempio potrebbe essere utile.

Questa sezione fornisce una panoramica di come eseguire il [progetto di esempio](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining). Per istruzioni dettagliate che illustrano come eseguire un progetto di WebJobs SDK in locale e distribuirlo in un processo Web di Azure, vedere [Get started with the WebJobs SDK](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob) (Introduzione a WebJobs SDK).

### <a name="run-locally"></a>Esecuzione locale

1. Assicurarsi che l'emulatore di archiviazione sia in esecuzione (vedere [Prerequisiti](#prerequisites)).

1. Se si desidera visualizzare i log in Application Insights quando si esegue il progetto in locale:If you want to see logs in Application Insights when you run the project locally:

    a. Creare una risorsa di Application Insights e usare il tipo di app **Generale.**

    b. Salvare la chiave di strumentazione nel file *App.config*.

1. Eseguire il progetto.

### <a name="run-in-azure"></a>Esecuzione in Azure

1. Creare un'app Web e un account di archiviazione.

1. Nell'app Web salvare la stringa di connessione `AzureWebJobsStorage`di archiviazione in un'impostazione dell'app denominata .

1. Creare una risorsa di Application Insights e usare il tipo di app **Generale.**

1. Salvare la chiave di strumentazione in un'impostazione dell'app denominata `APPINSIGHTS_INSTRUMENTATIONKEY`.

1. Eseguire la distribuzione come processo Web.

## <a name="webjobs-sdk-3x"></a>WebJobs SDK 3.x

In questo articolo viene illustrato come sviluppare un progetto WebJobs SDK 2.x. Se si sta sviluppando un progetto [WebJobs SDK 3.x,](../../app-service/webjobs-sdk-get-started.md) questa sezione consente di comprendere le differenze.

La modifica principale introdotta è l'uso di .NET Core anziché di .NET Framework. Per creare un progetto WebJobs SDK 3.x, le istruzioni sono le stesse, con queste eccezioni:

1. Creare un'app console di .NET Core. Nella finestra di dialogo **Nuovo progetto** di Visual Studio selezionare App console **.NET Core** > **(.NET Core)**. Il file di progetto specifica che `TargetFramework` è `netcoreapp2.x`.

1. Scegliere la versione di rilascio WebJobs SDK 3.x dei seguenti pacchetti:

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. Impostare la stringa di connessione di archiviazione e la chiave di strumentazione di Application Insights in un file appsettings.json usando il framework di configurazione di .NET Core.Set the storage connection string and the Application Insights instrumentation key in an *appsettings.json* file, by using the .NET Core configuration framework. Ad esempio:

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. Modificare `Main` il codice del metodo per eseguire questa operazione. Ad esempio:

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

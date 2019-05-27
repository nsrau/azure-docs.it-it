---
title: Come per l'esecuzione di funzioni permanenti come processi Web - Azure
description: Imparare a codificare e configurare Funzioni durevoli per l'esecuzione in processi Web tramite WebJobs SDK.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 39a757900b4307d702a0ce0ce1c20694418aa8dd
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65872828"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Come per l'esecuzione di funzioni permanenti come processi Web

Per impostazione predefinita, funzioni permanenti Usa il runtime di funzioni di Azure per le orchestrazioni di host. Tuttavia, potrebbero esserci alcuni scenari in cui è necessario un maggior controllo sul codice che attende gli eventi. Questo articolo illustra come implementare l'orchestrazione che utilizza WebJobs SDK. Per un confronto più dettagliato tra funzioni e processi Web, vedere [confrontare funzioni e processi Web](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

[Funzioni di Azure](../functions-overview.md) e l'estensione [Funzioni durevoli](durable-functions-overview.md) sono basate su [WebJobs SDK](../../app-service/webjobs-sdk-how-to.md). L'host di processo in WebJobs SDK è il runtime di funzioni di Azure. Se si desidera controllare il comportamento in modi non possibili in funzioni di Azure, è possibile sviluppare ed eseguire funzioni permanenti con WebJobs SDK manualmente.

Nella versione 3.x di WebJobs SDK, l'host è un'implementazione di `IHost`e nella versione 2.x è usare il `JobHost` oggetto.

L'esempio concatenamento di funzioni permanenti è disponibile in una versione di WebJobs SDK versione 2.x: scaricare o clonare la [repository delle funzioni permanenti](https://github.com/azure/azure-functions-durable-extension/)e passare alle *esempi\\webjobssdk\\concatenamento* cartella.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si abbia familiarità con le nozioni di base relative a WebJobs SDK, allo sviluppo della libreria di classi C# per Funzioni di Azure e a Funzioni durevoli. Per un'introduzione a questi argomenti, vedere le risorse seguenti:

* [Get started with the WebJobs SDK](../../app-service/webjobs-sdk-get-started.md) (Introduzione a WebJobs SDK)
* [Creare la prima funzione con Visual Studio](../functions-create-your-first-function-visual-studio.md)
* [Funzioni durevoli](durable-functions-sequence.md)

Per seguire la procedura descritta in questo articolo:

* [Installare Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/) con il **sviluppo in Azure** carico di lavoro.

  Se si ha già Visual Studio, ma non i carico di lavoro specifico, aggiungere il carico di lavoro selezionando **degli strumenti** > **Ottieni strumenti e funzionalità**.

  È anche possibile usare [Visual Studio Code](https://code.visualstudio.com/), ma alcune istruzioni sono specifiche per Visual Studio.

* Installare ed eseguire [Emulatore di archiviazione di Azure](../../storage/common/storage-use-emulator.md) versione 5.2 o successive. In alternativa, è possibile aggiornare il file *App.config* con una stringa di connessione di Archiviazione di Azure.

## <a name="webjobs-sdk-versions"></a>Versioni di WebJobs SDK

Questo articolo illustra come sviluppare un progetto di WebJobs SDK 2.x (equivalente a Funzioni di Azure versione 1.x). Per informazioni sulla versione 3.x, vedere [WebJobs SDK 3.x](#webjobs-sdk-3x) più avanti in questo articolo.

## <a name="create-a-console-app"></a>Creare un'app console

Per eseguire funzioni permanenti come processi Web, è innanzitutto necessario creare un'app console. Un progetto di WebJobs SDK è semplicemente un progetto di app console con i pacchetti NuGet appropriati installati.

In Visual Studio **nuovo progetto** finestra di dialogo **Desktop classico di Windows** > **App Console (.NET Framework)**. Nel file di progetto `TargetFrameworkVersion` deve essere `v4.6.1`.

Visual Studio include anche un modello di progetto processo Web, che è possibile usare selezionando **Cloud** > **processo Web di Azure (.NET Framework)**. Questo modello comporta l'installazione di molti pacchetti, alcuni dei quali potrebbero non essere necessari.

## <a name="install-nuget-packages"></a>Installare i pacchetti NuGet

I pacchetti NuGet sono necessari per WebJobs SDK, le associazioni dei componenti di base, il framework di registrazione e l'estensione Durable Task. Ecco **Console di gestione pacchetti** comandi per tali pacchetti, con i numeri di versione stabile più recente alla data di redazione di questo articolo:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.4.0
```

Occorre anche disporre di provider di registrazione. I seguenti comandi installano il provider di Azure Application Insights e `ConfigurationManager`. Con `ConfigurationManager` è possibile ottenere la chiave di strumentazione di Application Insights dalle impostazioni dell'app.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

Il comando seguente consente di installare il provider della console:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>Codice JobHost

Avere creato l'app console e installare i pacchetti NuGet è necessario, è possibile usare funzioni permanenti. Eseguire questa operazione usando codice JobHost.

Per usare l'estensione Funzioni durevoli, chiamare `UseDurableTask` nell'oggetto `JobHostConfiguration` nel metodo `Main`:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

Per un elenco di proprietà che è possibile impostare nell'oggetto `DurableTaskExtension`, vedere [host.json](../functions-host-json.md#durabletask).

Il metodo `Main` consente di impostare anche i provider di registrazione. Nell'esempio seguente consente di configurare la console e i provider di Application Insights.

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

Funzioni permanenti nel contesto di processi Web è diversa da funzioni permanenti nel contesto delle funzioni di Azure. È importante essere a conoscenza delle differenze quando si scrive il codice.

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

In un progetto WebJobs SDK, è possibile chiamare i metodi sull'oggetto client di orchestrazione, invece di inviando richieste HTTP. I metodi seguenti corrispondono alle tre attività eseguibili con l'API di gestione HTTP:

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

La funzione di client di orchestrazione nel progetto di esempio viene avviata la funzione di orchestrazione e quindi entra in un ciclo che chiama `GetStatusAsync` ogni 2 secondi:

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

Hai funzioni permanenti, impostare l'esecuzione come processo Web e sono ora la comprensione del modo in cui ciò sarà diversa dall'esecuzione di funzioni permanenti di funzioni di Azure autonomo. A questo punto, osservarne il funzionamento in un campione può essere utile.

Questa sezione fornisce una panoramica di come eseguire il [progetto di esempio](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining). Per istruzioni dettagliate che illustrano come eseguire un progetto di WebJobs SDK in locale e distribuirlo in un processo Web di Azure, vedere [Get started with the WebJobs SDK](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob) (Introduzione a WebJobs SDK).

### <a name="run-locally"></a>Esecuzione locale

1. Assicurarsi che l'emulatore di archiviazione sia in esecuzione (vedere [Prerequisiti](#prerequisites)).

1. Se si desidera visualizzare i log di Application Insights quando si esegue il progetto in locale:

    a. Creare una risorsa di Application Insights e usare la **generale** tipo di app per tale.

    b. Salvare la chiave di strumentazione nel file *App.config*.

1. Eseguire il progetto.

### <a name="run-in-azure"></a>Esecuzione in Azure

1. Creare un'app Web e un account di archiviazione.

1. Nell'app web, salvare la stringa di connessione di archiviazione in un'impostazione dell'app denominata `AzureWebJobsStorage`.

1. Creare una risorsa di Application Insights e usare la **generale** tipo di app per tale.

1. Salvare la chiave di strumentazione nell'impostazione dell'app denominata `APPINSIGHTS_INSTRUMENTATIONKEY`.

1. Eseguire la distribuzione come processo Web.

## <a name="webjobs-sdk-3x"></a>WebJobs SDK 3.x

Questo articolo illustra come sviluppare un progetto di WebJobs SDK versione 2.x. Se si sta sviluppando un [WebJobs SDK 3.x](../../app-service/webjobs-sdk-get-started.md) progetto, in questa sezione consente di comprendere le differenze.

La principale modifica introdotta è l'uso di .NET Core invece di .NET Framework. Per creare un progetto di WebJobs SDK 3.x, le istruzioni sono gli stessi, con le seguenti eccezioni:

1. Creare un'app console di .NET Core. In Visual Studio **nuovo progetto** finestra di dialogo **.NET Core** > **App Console (.NET Core)**. Il file di progetto specifica che `TargetFramework` è `netcoreapp2.x`.

1. Scegliere la versione di WebJobs SDK 3.x dei pacchetti seguenti:

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. Impostare la stringa di connessione di archiviazione e la chiave di strumentazione di Application Insights in un *appSettings. JSON* file, usando il framework di configurazione di .NET Core. Ad esempio:

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. Modifica il `Main` codice del metodo per eseguire questa operazione. Ad esempio:

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

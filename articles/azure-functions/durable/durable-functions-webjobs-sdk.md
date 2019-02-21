---
title: Come eseguire funzioni durevoli come processi Web - Azure
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
ms.openlocfilehash: 282b07a384ac6db5bfbc144ca06440f3a8f01a6a
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301197"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Come eseguire funzioni durevoli come processi Web

[Funzioni di Azure](../functions-overview.md) e l'estensione [Funzioni durevoli](durable-functions-overview.md) sono basate su [WebJobs SDK](../../app-service/webjobs-create.md). `JobHost` in WebJobs SDK è il runtime in Funzioni di Azure. Se è necessario controllare il comportamento di `JobHost` con modalità non disponibili in Funzioni di Azure, è possibile sviluppare ed eseguire funzioni durevoli usando manualmente WebJobs SDK. È quindi possibile eseguire le funzioni durevoli in un processo Web di Azure o in qualsiasi punto in cui sia in esecuzione un'applicazione console.

L'esempio di Funzioni durevoli di concatenamento è disponibile in una versione di WebJobs SDK: scaricare o clonare il [repository Funzioni durevoli](https://github.com/azure/azure-functions-durable-extension/) e andare alla cartella *samples\\webjobssdk\\chaining*.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si abbia familiarità con le nozioni di base relative a WebJobs SDK, allo sviluppo della libreria di classi C# per Funzioni di Azure e a Funzioni durevoli. Per un'introduzione a questi argomenti, vedere le risorse seguenti:

* [Get started with the WebJobs SDK](../../app-service/webjobs-sdk-get-started.md) (Introduzione a WebJobs SDK)
* [Creare la prima funzione con Visual Studio](../functions-create-your-first-function-visual-studio.md)
* [Funzioni durevoli](durable-functions-sequence.md)

Per seguire la procedura descritta in questo articolo:

* [Installare Visual Studio 2017 versione 15.6 o versioni successive](https://docs.microsoft.com/visualstudio/install/) con il carico di lavoro **Sviluppo di Azure**.

  Se si ha già Visual Studio ma non tale carico di lavoro, aggiungere il carico di lavoro selezionando **Strumenti > Ottieni strumenti e funzionalità**.

  È anche possibile usare [Visual Studio Code](https://code.visualstudio.com/), ma alcune istruzioni sono specifiche per Visual Studio.

* Installare ed eseguire [Emulatore di archiviazione di Azure](../../storage/common/storage-use-emulator.md) versione 5.2 o successive. In alternativa, è possibile aggiornare il file *App.config* con una stringa di connessione di Archiviazione di Azure.

## <a name="webjobs-sdk-versions"></a>Versioni di WebJobs SDK

Questo articolo illustra come sviluppare un progetto di WebJobs SDK 2.x (equivalente a Funzioni di Azure versione 1.x). Per informazioni sulla versione 3.x, vedere [WebJobs SDK 3.x](#webjobs-sdk-3x) più avanti in questo articolo.

## <a name="create-console-app"></a>Creare un'app console

Un progetto di WebJobs SDK è semplicemente un progetto di app console con i pacchetti NuGet appropriati installati.

Nella finestra di dialogo **Nuovo progetto** di Visual Studio selezionare **Desktop classico di Windows > App console (.NET Framework)**. Nel file di progetto `TargetFrameworkVersion` deve essere `v4.6.1`.

Visual Studio offre inoltre un modello di progetto di Processi Web, che è possibile usare selezionando **Cloud > Processo Web di Azure (.NET Framework)**. Questo modello comporta l'installazione di molti pacchetti, alcuni dei quali potrebbero non essere necessari.

## <a name="install-nuget-packages"></a>Installare i pacchetti NuGet

I pacchetti NuGet sono necessari per WebJobs SDK, le associazioni dei componenti di base, il framework di registrazione e l'estensione Durable Task. Di seguito sono riportati i comandi della **Console di Gestione pacchetti** per tali pacchetti, con i numeri di versione stabile più recenti alla data di pubblicazione di questo articolo:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.4.0
```

Occorre anche disporre di provider di registrazione. I comandi seguenti consentono di installare il provider di Application Insights e `ConfigurationManager`. Con `ConfigurationManager` è possibile ottenere la chiave di strumentazione di Application Insights dalle impostazioni dell'app.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

Il comando seguente consente di installare il provider della console:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>Codice JobHost

Per usare l'estensione Funzioni durevoli, chiamare `UseDurableTask` nell'oggetto `JobHostConfiguration` nel metodo `Main`:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

Per un elenco di proprietà che è possibile impostare nell'oggetto `DurableTaskExtension`, vedere [host.json](../functions-host-json.md#durabletask).

Il metodo `Main` consente di impostare anche i provider di registrazione. Nell'esempio seguente vengono configurati i provider della console e di Application Insights.

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

Esistono alcune differenze nel codice scritto per le funzioni di WebJobs SDK rispetto a quello scritto per il servizio Funzioni di Azure.

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

In un progetto di WebJobs SDK è possibile chiamare i metodi nell'oggetto del client di orchestrazione anziché inviare richieste HTTP. I metodi seguenti corrispondono alle tre attività eseguibili con l'API di gestione HTTP:

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

La funzione del client di orchestrazione nel progetto di esempio avvia la funzione dell'agente di orchestrazione e viene quindi eseguita in un ciclo che chiama `GetStatusAsync` ogni 2 secondi:

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

Questa sezione fornisce una panoramica di come eseguire il [progetto di esempio](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining). Per istruzioni dettagliate che illustrano come eseguire un progetto di WebJobs SDK in locale e distribuirlo in un processo Web di Azure, vedere [Get started with the WebJobs SDK](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob) (Introduzione a WebJobs SDK).

### <a name="run-locally"></a>Esecuzione locale

1. Assicurarsi che l'emulatore di archiviazione sia in esecuzione (vedere [Prerequisiti](#prerequisites)).

1. Per visualizzare i log in Application Insights quando si procede all'esecuzione in locale:

    a. Creare una risorsa di Application Insights, con tipo di app **Generale**.

    b. Salvare la chiave di strumentazione nel file *App.config*.

1. Eseguire il progetto.

### <a name="run-in-azure"></a>Esecuzione in Azure

1. Creare un'app Web e un account di archiviazione.

1. Nell'app Web salvare la stringa di connessione di archiviazione in un'impostazione dell'app denominata AzureWebJobsStorage.

1. Creare una risorsa di Application Insights, con tipo di app **Generale**.

1. Salvare la chiave di strumentazione in un'impostazione dell'app denominata APPINSIGHTS_INSTRUMENTATIONKEY.

1. Eseguire la distribuzione come processo Web.

## <a name="webjobs-sdk-3x"></a>WebJobs SDK 3.x

La principale modifica introdotta dalla versione 3.x è l'uso di .NET Core al posto di .NET Framework. Le istruzioni per la creazione di un progetto con la versione 3.x sono le stesse, con le eccezioni seguenti:

1. Creare un'app console di .NET Core. Nella finestra di dialogo **Nuovo progetto** di Visual Studio selezionare **.NET Core > App console (.NET Core)**. Il file di progetto specifica che `TargetFramework` è `netcoreapp2.0`.

1. Scegliere la versione preliminare 3.x dei pacchetti seguenti:

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. Modificare il codice del metodo `Main` per ottenere la stringa di connessione di archiviazione e la chiave di strumentazione di Application Insights da un file *appsettings.json* usando .NET Core Configuration Framework.  Ad esempio:

   ```cs
   static void Main(string[] args)
   {
       var builder = new ConfigurationBuilder()
           .SetBasePath(Directory.GetCurrentDirectory())
           .AddJsonFile("appsettings.json");

       var appSettingsConfig = builder.Build();

       using (var loggerFactory = new LoggerFactory())
       {
           var config = new JobHostConfiguration();

           config.DashboardConnectionString = "";
           config.StorageConnectionString =
               appSettingsConfig.GetConnectionString("AzureWebJobsStorage");
           var instrumentationKey =
               appSettingsConfig["APPINSIGHTS_INSTRUMENTATIONKEY"];

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

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su WebJobs SDK, vedere [How to use the WebJobs SDK](../../app-service/webjobs-sdk-how-to.md) (Come usare WebJobs SDK).
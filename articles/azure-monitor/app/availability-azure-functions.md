---
title: Creare ed eseguire test di disponibilità personalizzati usando Funzioni di AzureCreate and run custom availability tests using Azure Functions
description: Questo documento illustra come creare una funzione di Azure con TrackAvailability() che verrà eseguita periodicamente in base alla configurazione specificata nella funzione TimerTrigger.This doc cover how to create an Azure Function with TrackAvailability() that will run periodically according to the configuration given in TimerTrigger function. I risultati di questo test verranno inviati alla risorsa Application Insights, in cui sarà possibile eseguire query e avvisare i dati dei risultati di disponibilità. I test personalizzati consentono di scrivere test di disponibilità più complessi rispetto a quanto è possibile usando l'interfaccia utente del portale, monitorare un'app all'interno della rete virtuale di Azure, modificare l'indirizzo dell'endpoint o creare un test di disponibilità se non è disponibile nella tua area geografica.
ms.topic: conceptual
author: morgangrobin
ms.author: mogrobin
ms.date: 11/22/2019
ms.openlocfilehash: 476d66c51c10a5fcfb3cb0319c47b3338d28812c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665800"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Creare ed eseguire test di disponibilità personalizzati usando Funzioni di AzureCreate and run custom availability tests using Azure Functions

Questo articolo illustra come creare una funzione di Azure con TrackAvailability() che verrà eseguita periodicamente in base alla configurazione specificata nella funzione TimerTrigger con la propria logica di business. I risultati di questo test verranno inviati alla risorsa Application Insights, in cui sarà possibile eseguire query e avvisare i dati dei risultati di disponibilità. In questo modo è possibile creare test personalizzati simili a quelli che è possibile eseguire tramite il monitoraggio della [disponibilità](../../azure-monitor/app/monitor-web-app-availability.md) nel portale. I test personalizzati consentono di scrivere test di disponibilità più complessi rispetto a quanto è possibile usando l'interfaccia utente del portale, monitorare un'app all'interno della rete virtuale di Azure, modificare l'indirizzo dell'endpoint o creare un test di disponibilità anche se questa funzionalità non è disponibile nell'area geografica.

> [!NOTE]
> Questo esempio è progettato esclusivamente per mostrare i meccanismi del funzionamento della chiamata all'API TrackAvailability() all'interno di una funzione di Azure.This example is designed alone to show you the mechanics of how the TrackAvailability() API call works within an Azure Function. Non come scrivere il codice di test HTTP sottostante/logica di business che sarebbe necessario per trasformare questo in un test di disponibilità completamente funzionale. Per impostazione predefinita, se si illustra questo esempio, verrà creato un test di disponibilità che genererà sempre un errore.

## <a name="create-timer-triggered-function"></a>Creare la funzione attivata dal timerCreate timer triggered function

- Se si dispone di una risorsa di Application Insights:If you have an Application Insights Resource:
    - Per impostazione predefinita, Funzioni di Azure crea una risorsa di Application Insights, ma se si vuole usare una delle risorse già create sarà necessario specificarla durante la creazione.
    - Seguire le istruzioni su come creare una risorsa Funzioni di Azure e una [funzione attivata dal](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) timer (interrompere prima eseguire la pulizia) con le opzioni seguenti.
        -  Selezionare la scheda **Monitoraggio** nella parte superiore.

            ![ Creare un'app Funzioni di Azure con la risorsa di App Insights](media/availability-azure-functions/create-function-app.png)

        - Selezionare la casella di riepilogo a discesa Application Insights e digitare o selezionare il nome della risorsa.

            ![Selezione di risorse di Application Insights esistentiSelecting existing Application Insights resource](media/availability-azure-functions/app-insights-resource.png)

        - Selezionare **Revisione e creazione**
- Se non si dispone ancora di una risorsa di Application Insights creata per la funzione attivata dal timer:If you do not have an Application Insights Resource yet created for your timer triggered function:
    - Per impostazione predefinita, quando si crea l'applicazione Funzioni di Azure, verrà creata automaticamente una risorsa di Application Insights.By default when you are creating your Azure Functions application it will create an Application Insights resource for you.
    - Seguire le istruzioni su come creare una risorsa Funzioni di Azure e una [funzione attivata dal timer](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (interrompere prima della pulizia).

## <a name="sample-code"></a>Codice di esempio

Copiare il codice riportato di seguito nel file run.csx (questo sostituirà il codice preesistente). A tale scopo, passare all'applicazione Funzioni di Azure e selezionare la funzione di trigger timer a sinistra.

>[!div class="mx-imgBorder"]
>![Run.csx della funzione di Azure nel portale di AzureAzure function's run.csx in Azure portal](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> Per l'indirizzo dell'endpoint da utilizzare: `EndpointAddress= https://dc.services.visualstudio.com/v2/track`. A meno che la risorsa non si trovi in un'area come Azure per enti pubblici o Azure Cina, nel qual caso consultare questo articolo [sull'override degli endpoint predefiniti](https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification) e selezionare l'endpoint del canale di telemetria appropriato per l'area.

```C#
#load "runAvailabilityTest.csx"
 
using System;
using System.Diagnostics;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
 
// The Application Insights Instrumentation Key can be changed by going to the overview page of your Function App, selecting configuration, and changing the value of the APPINSIGHTS_INSTRUMENTATIONKEY Application setting.
// DO NOT replace the code below with your instrumentation key, the key's value is pulled from the environment variable/application setting key/value pair.
private static readonly string instrumentationKey = Environment.GetEnvironmentVariable("APPINSIGHTS_INSTRUMENTATIONKEY");
 
//[CONFIGURATION_REQUIRED]
// If your resource is in a region like Azure Government or Azure China, change the endpoint address accordingly.
// Visit https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification for more details.
private const string EndpointAddress = "https://dc.services.visualstudio.com/v2/track";
 
private static readonly TelemetryConfiguration telemetryConfiguration = new TelemetryConfiguration(instrumentationKey, new InMemoryChannel { EndpointAddress = EndpointAddress });
private static readonly TelemetryClient telemetryClient = new TelemetryClient(telemetryConfiguration);
 
public async static Task Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Entering Run at: {DateTime.Now}");
 
    if (myTimer.IsPastDue)
    {
        log.LogWarning($"[Warning]: Timer is running late! Last ran at: {myTimer.ScheduleStatus.Last}");
    }
 
    // [CONFIGURATION_REQUIRED] provide {testName} accordingly for your test function
    string testName = "AvailabilityTestFunction";
 
    // REGION_NAME is a default environment variable that comes with App Service
    string location = Environment.GetEnvironmentVariable("REGION_NAME");
 
    log.LogInformation($"Executing availability test run for {testName} at: {DateTime.Now}");
    string operationId = Guid.NewGuid().ToString("N");
 
    var availability = new AvailabilityTelemetry
    {
        Id = operationId,
        Name = testName,
        RunLocation = location,
        Success = false
    };
 
    var stopwatch = new Stopwatch();
    stopwatch.Start();
 
    try
    {
        await RunAvailbiltyTestAsync(log);
        availability.Success = true;
    }
    catch (Exception ex)
    {
        availability.Message = ex.Message;
 
        var exceptionTelemetry = new ExceptionTelemetry(ex);
        exceptionTelemetry.Context.Operation.Id = operationId;
        exceptionTelemetry.Properties.Add("TestName", testName);
        exceptionTelemetry.Properties.Add("TestLocation", location);
        telemetryClient.TrackException(exceptionTelemetry);
    }
    finally
    {
        stopwatch.Stop();
        availability.Duration = stopwatch.Elapsed;
        availability.Timestamp = DateTimeOffset.UtcNow;
 
        telemetryClient.TrackAvailability(availability);
        // call flush to ensure telemetry is sent
        telemetryClient.Flush();
    }
}

```

A destra sotto i file di visualizzazione, selezionare **Aggiungi**. Chiamare il nuovo file **function.proj** con la seguente configurazione.

```C#
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.8.2" /> <!-- Ensure you’re using the latest version -->
    </ItemGroup>
</Project>

```

>[!div class="mx-imgBorder"]
>![A destra, aggiungi. Denominare il file function.proj](media/availability-azure-functions/addfile.png)

A destra sotto i file di visualizzazione, selezionare **Aggiungi**. Chiamare il nuovo file **runAvailabilityTest.csx** con la configurazione seguente.

```C#
public async static Task RunAvailbiltyTestAsync(ILogger log)
{
    // Add your business logic here.
    throw new NotImplementedException();
}

```

## <a name="check-availability"></a>Verifica disponibilità

Per assicurarsi che tutto funzioni, è possibile esaminare il grafico nella scheda Disponibilità della risorsa di Application Insights.To make sure everything is working, you can look at the graph in the Availability tab of your Application Insights resource.

> [!NOTE]
> Se è stata implementata la propria logica di business in runAvailabilityTest.csx, verranno visualizzati risultati positivi come nelle schermate seguenti, se non è stato possibile, verranno visualizzati risultati non riusciti.

>[!div class="mx-imgBorder"]
>![Scheda Disponibilità con risultati positivi](media/availability-azure-functions/availtab.png)

Quando si configura il test con Funzioni di Azure si noterà che, a differenza dell'uso di **Aggiungi test** nella scheda Disponibilità, il nome del test non verrà visualizzato e non sarà possibile interagire con esso. I risultati vengono visualizzati, ma si ottiene una visualizzazione di riepilogo anziché la stessa visualizzazione dettagliata che si ottiene quando si crea un test di disponibilità tramite il portale.

Per visualizzare i dettagli della transazione end-to-end, selezionare **Riuscito** o **Operazione non riuscita** sotto il drill-down, quindi selezionare un campione. È inoltre possibile accedere ai dettagli della transazione end-to-end selezionando un punto dati nel grafico.

>[!div class="mx-imgBorder"]
>![Selezionare un test di disponibilità di esempioSelect a sample availability test](media/availability-azure-functions/sample.png)

>[!div class="mx-imgBorder"]
>![Dettagli transazione end-to-end](media/availability-azure-functions/end-to-end.png)

Se hai eseguito tutto così com'è (senza aggiungere la logica di business), vedrai che il test non è riuscito.

## <a name="query-in-logs-analytics"></a>Query nei log (Analisi)Query in Logs (Analytics)

È possibile usare Logs(analytics) per visualizzare i risultati della disponibilità, le dipendenze e altro ancora. Per ulteriori informazioni sui log, visitare [Log query overview](../../azure-monitor/log-query/log-query-overview.md).

>[!div class="mx-imgBorder"]
>![Risultati della disponibilità](media/availability-azure-functions/availabilityresults.png)

>[!div class="mx-imgBorder"]
>![Dipendenze](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>Passaggi successivi

- [Mappa dell'applicazione](../../azure-monitor/app/app-map.md)
- [Diagnostica delle transazioni](../../azure-monitor/app/transaction-diagnostics.md)

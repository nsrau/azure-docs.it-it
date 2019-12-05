---
title: Creare ed eseguire test di disponibilità personalizzati con funzioni di Azure
description: Questo documento illustra come creare una funzione di Azure con TrackAvailability () che verrà eseguito periodicamente in base alla configurazione specificata nella funzione TimerTrigger. I risultati di questo test verranno inviati alla risorsa Application Insights, in cui sarà possibile eseguire una query e inviare un avviso sui dati dei risultati di disponibilità. I test personalizzati consentiranno di scrivere test di disponibilità più complessi rispetto a quanto possibile tramite l'interfaccia utente del portale, monitorare un'app all'interno della VNET di Azure, modificare l'indirizzo dell'endpoint o creare un test di disponibilità se non è disponibile nella propria area.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: morgangrobin
ms.author: mogrobin
ms.date: 11/22/2019
ms.openlocfilehash: c7a8ffb9873fd70353f38bb2b2bbfdb584992377
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815700"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Creare ed eseguire test di disponibilità personalizzati con funzioni di Azure

Questo articolo illustra come creare una funzione di Azure con TrackAvailability () che verrà eseguito periodicamente in base alla configurazione specificata nella funzione TimerTrigger con la logica di business personalizzata. I risultati di questo test verranno inviati alla risorsa Application Insights, in cui sarà possibile eseguire una query e inviare un avviso sui dati dei risultati di disponibilità. In questo modo è possibile creare test personalizzati simili a quelli che è possibile eseguire tramite il [monitoraggio della disponibilità](../../azure-monitor/app/monitor-web-app-availability.md) nel portale. I test personalizzati consentiranno di scrivere test di disponibilità più complessi rispetto a quanto possibile tramite l'interfaccia utente del portale, monitorare un'app all'interno della VNET di Azure, modificare l'indirizzo dell'endpoint o creare un test di disponibilità anche se questa funzionalità non è disponibile nella propria area.

> [!NOTE]
> Questo esempio è progettato esclusivamente per illustrare i meccanismi di funzionamento della chiamata API TrackAvailability () all'interno di una funzione di Azure. Non viene illustrato come scrivere il codice di test HTTP sottostante o la logica di business necessaria per trasformarla in un test di disponibilità pienamente funzionante. Per impostazione predefinita, se si esamina questo esempio verrà creato un test di disponibilità che genererà sempre un errore.

## <a name="create-timer-triggered-function"></a>Crea funzione attivata dal timer

- Se si dispone di una risorsa Application Insights:
    - Per impostazione predefinita, funzioni di Azure crea una risorsa Application Insights ma se si vuole usare una delle risorse già create, è necessario specificarla durante la creazione.
    - Seguire le istruzioni su come [creare una risorsa di funzioni di Azure e una funzione attivata dal timer](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (arresta prima della pulizia) con le opzioni seguenti.
        -  Selezionare la scheda **monitoraggio** nella parte superiore.

            ![ Creare un'app funzioni di Azure con la propria risorsa di Application Insights](media/availability-azure-functions/create-function-app.png)

        - Selezionare la casella a discesa Application Insights e digitare o selezionare il nome della risorsa.

            ![Selezione della risorsa Application Insights esistente](media/availability-azure-functions/app-insights-resource.png)

        - Selezionare **Verifica + crea**
- Se non è ancora stata creata una risorsa di Application Insights per la funzione attivata dal timer:
    - Per impostazione predefinita, durante la creazione dell'applicazione funzioni di Azure verrà creata automaticamente una risorsa Application Insights.
    - Seguire le istruzioni su come [creare una risorsa di funzioni di Azure e una funzione attivata dal timer](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (arresta prima della pulizia).

## <a name="sample-code"></a>Codice di esempio

Copiare il codice seguente nel file run. CSX (verrà sostituito il codice preesistente). A tale scopo, passare all'applicazione funzioni di Azure e selezionare la funzione di trigger del timer a sinistra.

>[!div class="mx-imgBorder"]
>![Run. CSX della funzione di Azure in portale di Azure](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> Per l'indirizzo endpoint da usare: `EndpointAddress= https://dc.services.visualstudio.com/v2/track`. A meno che la risorsa non si trovi in un'area come Azure per enti pubblici o Azure Cina, in questo caso consultare questo articolo sull' [override degli endpoint predefiniti](https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification) e selezionare l'endpoint del canale di telemetria appropriato per la propria area.

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

Nella parte destra sotto Visualizza file selezionare **Aggiungi**. Chiamare la nuova **funzione file. proj** con la configurazione seguente.

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
>![a destra selezionare, Aggiungi. Denominare il file function. proj](media/availability-azure-functions/addfile.png)

Nella parte destra sotto Visualizza file selezionare **Aggiungi**. Chiamare il nuovo file **runAvailabilityTest. CSX** con la configurazione seguente.

```C#
public async static Task RunAvailbiltyTestAsync(ILogger log)
{
    // Add your business logic here.
    throw new NotImplementedException();
}

```

## <a name="check-availability"></a>Verifica disponibilità

Per assicurarsi che tutto funzioni, è possibile esaminare il grafico nella scheda disponibilità della risorsa Application Insights.

> [!NOTE]
> Se è stata implementata la logica di business personalizzata in runAvailabilityTest. CSX, i risultati visualizzati come negli screenshot riportati di seguito, se non sono stati visualizzati, si otterranno risultati non riusciti.

>[!div class="mx-imgBorder"]
>![scheda disponibilità con risultati riusciti](media/availability-azure-functions/availtab.png)

Quando si configura il test con funzioni di Azure, si noterà che, a differenza dell'uso di **Aggiungi test** nella scheda disponibilità, il nome del test non verrà visualizzato e non sarà possibile interagire con esso. I risultati vengono visualizzati, ma si ottiene una visualizzazione di riepilogo invece della stessa visualizzazione dettagliata ottenuta quando si crea un test di disponibilità tramite il portale.

Per visualizzare i dettagli della transazione end-to-end, selezionare **esito positivo** o **negativo** in drill into, quindi selezionare un esempio. È anche possibile ottenere i dettagli della transazione end-to-end selezionando un punto dati nel grafico.

>[!div class="mx-imgBorder"]
>![selezionare un test di disponibilità di esempio](media/availability-azure-functions/sample.png)

>[!div class="mx-imgBorder"]
>![dettagli della transazione end-to-end](media/availability-azure-functions/end-to-end.png)

Se è stato eseguito tutto come è (senza aggiungere la logica di business), si noterà che il test ha avuto esito negativo.

## <a name="query-in-logs-analytics"></a>Eseguire query nei log (analisi)

È possibile usare i log (Analytics) per visualizzare i risultati della disponibilità, le dipendenze e altro ancora. Per altre informazioni sui log, vedere [Cenni preliminari sulle query di log](../../azure-monitor/log-query/log-query-overview.md).

>[!div class="mx-imgBorder"]
>Risultati della disponibilità ![](media/availability-azure-functions/availabilityresults.png)

>[!div class="mx-imgBorder"]
>![Dipendenze](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>Passaggi successivi

- [Mappa delle applicazioni](../../azure-monitor/app/app-map.md)
- [Diagnostica delle transazioni](../../azure-monitor/app/transaction-diagnostics.md)

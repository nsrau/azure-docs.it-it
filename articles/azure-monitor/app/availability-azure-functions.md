---
title: Creare ed eseguire test di disponibilità personalizzati con funzioni di Azure
description: Questo documento illustra come creare una funzione di Azure con TrackAvailability () che verrà eseguito periodicamente in base alla configurazione specificata nella funzione TimerTrigger. I risultati di questo test verranno inviati alla risorsa Application Insights, in cui sarà possibile eseguire una query e inviare un avviso sui dati dei risultati di disponibilità. I test personalizzati consentiranno di scrivere test di disponibilità più complessi rispetto a quanto possibile tramite l'interfaccia utente del portale, monitorare un'app all'interno della VNET di Azure, modificare l'indirizzo dell'endpoint o creare un test di disponibilità se non è disponibile nella propria area.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: morgangrobin
ms.author: mogrobin
ms.date: 10/11/2019
ms.openlocfilehash: a2b29f2c24a3244cdc800d882e7c2ed8f943eabe
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677595"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Creare ed eseguire test di disponibilità personalizzati con funzioni di Azure

In questo articolo viene illustrato come creare una funzione di Azure con TrackAvailability () che verrà eseguito periodicamente in base alla configurazione specificata nella funzione TimerTrigger. I risultati di questo test verranno inviati alla risorsa Application Insights, in cui sarà possibile eseguire una query e inviare un avviso sui dati dei risultati di disponibilità. In questo modo è possibile creare test personalizzati simili a quelli che è possibile eseguire tramite il [monitoraggio della disponibilità](../../azure-monitor/app/monitor-web-app-availability.md) nel portale. I test personalizzati consentiranno di scrivere test di disponibilità più complessi rispetto a quanto possibile tramite l'interfaccia utente del portale, monitorare un'app all'interno della VNET di Azure, modificare l'indirizzo dell'endpoint o creare un test di disponibilità anche se questa funzionalità non è disponibile nella propria area.


## <a name="create-timer-triggered-function"></a>Crea funzione attivata dal timer

- Se si dispone di una risorsa Application Insights:
    - Per impostazione predefinita, funzioni di Azure crea una risorsa Application Insights ma se si vuole usare una delle risorse già create, è necessario specificarla durante la creazione.
    - Seguire le istruzioni su come [creare una risorsa di funzioni di Azure e una funzione attivata dal timer](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (arresta prima della pulizia) con le opzioni seguenti.
        -  Fare clic sulla sezione Application Insights prima di selezionare **Crea**.

            ![ Creare un'app funzioni di Azure con la propria risorsa di Application Insights](media/availability-azure-functions/create-function-app.png)

        - Fare clic su **Seleziona risorsa esistente** e digitare il nome della risorsa. Selezionare **applica**

            ![Selezione della risorsa Application Insights esistente](media/availability-azure-functions/app-insights-resource.png)

        - Selezionare **Crea**
- Se non è ancora stata creata una risorsa di Application Insights per la funzione attivata dal timer:
    - Per impostazione predefinita, durante la creazione dell'applicazione funzioni di Azure verrà creata automaticamente una risorsa Application Insights.
    - Seguire le istruzioni su come [creare una risorsa di funzioni di Azure e una funzione attivata dal timer](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (arresta prima della pulizia).

## <a name="sample-code"></a>Codice di esempio

Copiare il codice seguente nel file run. CSX (verrà sostituito il codice preesistente). A tale scopo, passare all'applicazione funzioni di Azure e selezionare la funzione di trigger del timer a sinistra.

![Run. CSX della funzione di Azure in portale di Azure](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> Per l'indirizzo endpoint da usare: `EndpointAddress= https://dc.services.visualstudio.com/v2/track`. A meno che la risorsa non si trovi in un'area come Azure per enti pubblici o Azure Cina, in questo caso consultare questo articolo sull' [override degli endpoint predefiniti](https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification) e selezionare l'endpoint del canale di telemetria appropriato per la propria area.

```C#
using System;
using System.Diagnostics;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

// [CONFIGURATION_REQUIRED] configure test timeout accordingly for which your request should run
private static readonly HttpClient HttpClient = new HttpClient { Timeout = TimeSpan.FromSeconds(30) };

// The Application Insights Instrumentation Key can be changed by going to the overview page of your Function App, selecting configuration, and changing the value of the APPINSIGHTS_INSTRUMENTATIONKEY Application setting.
//DO NOT replace the code below with your instrumentation key, the key's value is pulled from the environment variable/application setting key/value pair.
private static readonly string InstrumentationKey = Environment.GetEnvironmentVariable("APPINSIGHTS_INSTRUMENTATIONKEY");

// [CONFIGURATION_REQUIRED] Configure EndpointAddress
private static readonly TelemetryConfiguration TelemetryConfiguration = new TelemetryConfiguration(InstrumentationKey, new ServerTelemetryChannel() { EndpointAddress = "<EndpointAddress>" });
private static readonly TelemetryClient TelemetryClient = new TelemetryClient(TelemetryConfiguration);

[FunctionName("Function")]
public static async void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
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

    // [CONFIGURATION_REQUIRED] configure {uri} and {contentMatch} accordingly for your web app. {uri} is the website that you are testing the availability of, make sure to include http:// ot https:// in your url. If {contentMatch} is present on the page, the test will succeed, otherwise it will fail.  
    await AvailabilityTestRun(
        name: testName,
        location: location,
        uri: "<http://example.com>",
        contentMatch: "<Enter a short string of text that is present  in the body of the page your are testing>",
        log: log
    );
}

private static async Task AvailabilityTestRun(string name, string location, string uri, string contentMatch, ILogger log)
{
    log.LogInformation($"Executing availability test run for {name} at: {DateTime.Now}");

    string operationId = Guid.NewGuid().ToString("N");

    var availability = new AvailabilityTelemetry
    {
        Id = operationId,
        Name = name,
        RunLocation = location,
        Success = false
    };

    var stopwatch = new Stopwatch();
    stopwatch.Start();
    bool isMonitoringFailure = false;

    try
    {
        using (var httpResponse = await HttpClient.GetAsync(uri))
        {
            // add test results to availability telemetry property
            availability.Properties.Add("HttpResponseStatusCode", Convert.ToInt32(httpResponse.StatusCode).ToString());

            // check if response content contains specific text
            string content = httpResponse.Content != null ? await httpResponse.Content.ReadAsStringAsync() : "";
            if (httpResponse.IsSuccessStatusCode && content.Contains(contentMatch))
            {
                availability.Success = true;
                availability.Message = $"Test succeeded with response: {httpResponse.StatusCode}";
                log.LogTrace($"[Verbose]: {availability.Message}");
            }
            else if (!httpResponse.IsSuccessStatusCode)
            {
                availability.Message = $"Test failed with response: {httpResponse.StatusCode}";
                log.LogWarning($"[Warning]: {availability.Message}");
            }
            else
            {
                availability.Message = $"Test content does not contain: {contentMatch}";
                log.LogWarning($"[Warning]: {availability.Message}");
            }
        }
    }
    catch (TaskCanceledException e)
    {
        availability.Message = $"Test timed out: {e.Message}";
        log.LogWarning($"[Warning]: {availability.Message}");
    }
    catch (Exception ex)
    {
        // track exception when unable to determine the state of web app
        isMonitoringFailure = true;
        var exceptionTelemetry = new ExceptionTelemetry(ex);
        exceptionTelemetry.Context.Operation.Id = operationId;
        exceptionTelemetry.Properties.Add("TestName", name);
        exceptionTelemetry.Properties.Add("TestLocation", location);
        exceptionTelemetry.Properties.Add("TestUri", uri);
        TelemetryClient.TrackException(exceptionTelemetry);
        log.LogError($"[Error]: {ex.Message}");

        // optional - throw to fail the function
        throw;
    }
    finally
    {
        stopwatch.Stop();
        availability.Duration = stopwatch.Elapsed;
        availability.Timestamp = DateTimeOffset.UtcNow;

        // do not make an assumption as to the state of the web app when monitoring failures occur
        if (!isMonitoringFailure)
        {
            TelemetryClient.TrackAvailability(availability);
            log.LogInformation($"Availability telemetry for {name} is sent.");
        }

        // call flush to ensure telemetries are sent
        TelemetryClient.Flush();
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
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
    </ItemGroup>
</Project>

```

![A destra selezionare Aggiungi. Denominare il file function. proj](media/availability-azure-functions/addfile.png)

## <a name="check-availability"></a>Verifica disponibilità

Per assicurarsi che tutto funzioni, è possibile esaminare il grafico nella scheda disponibilità della risorsa Application Insights.

![Scheda disponibilità con risultati riusciti](media/availability-azure-functions/availtab.png)

Quando si configura il test con funzioni di Azure, si noterà che, a differenza dell'uso di **Aggiungi test** nella scheda disponibilità, il nome del test non verrà visualizzato e non sarà possibile interagire con esso. I risultati vengono visualizzati, ma si ottiene una visualizzazione di riepilogo invece della stessa visualizzazione dettagliata ottenuta quando si crea un test di disponibilità tramite il portale.

Per visualizzare i dettagli della transazione end-to-end, selezionare **esito positivo** o **negativo** in drill into, quindi selezionare un esempio. È anche possibile ottenere i dettagli della transazione end-to-end selezionando un punto dati nel grafico.

![Selezionare un test di disponibilità di esempio](media/availability-azure-functions/sample.png)

![Dettagli transazione end-to-end](media/availability-azure-functions/end-to-end.png)

## <a name="query-in-logs-analytics"></a>Eseguire query nei log (analisi)

È possibile usare i log (Analytics) per visualizzare i risultati della disponibilità, le dipendenze e altro ancora. Per altre informazioni sui log, vedere [Cenni preliminari sulle query di log](../../azure-monitor/log-query/log-query-overview.md).

![Risultati della disponibilità](media/availability-azure-functions/availabilityresults.png)

![Dipendenze](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>Passaggi successivi

- [Mappa delle applicazioni](../../azure-monitor/app/app-map.md)
- [Diagnostica delle transazioni](../../azure-monitor/app/transaction-diagnostics.md)

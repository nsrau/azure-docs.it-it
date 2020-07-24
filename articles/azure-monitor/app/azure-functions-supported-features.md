---
title: 'Applicazione Azure Insights: funzionalità supportate di funzioni di Azure'
description: Funzionalità supportate di Application Insights per Funzioni di Azure
ms.topic: reference
author: TimothyMothra
ms.author: tilee
ms.date: 4/23/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 333bba2b1d3cd83457196e38b827daa78199f235
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87033514"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Application Insights per le funzionalità supportate di Funzioni di Azure

Funzioni di Azure offre l'[integrazione predefinita](../../azure-functions/functions-monitoring.md) con Application Insights che è disponibile tramite l'interfaccia ILogger. Di seguito è riportato un elenco delle funzionalità attualmente supportate. Per un'[introduzione](../../azure-functions/functions-monitoring.md#enable-application-insights-integration), vedere la guida di Funzioni di Azure.

Per ulteriori informazioni sulle versioni del runtime di funzioni, vedere [qui](../../azure-functions/functions-versions.md).

Per ulteriori informazioni sulle versioni compatibili di Application Insights, vedere [dipendenze](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/).

## <a name="supported-features"></a>Caratteristiche supportate

| Funzioni di Azure                       | V1                | V2 & V3   | 
|-----------------------------------    |---------------    |------------------ |
| | | | 
| **Raccolta automatica di**        |                 |                   |               
| &bull; Richieste                     | Sì             | Sì               | 
| &bull; Eccezioni                   | Sì             | Sì               | 
| &bull; Contatori delle prestazioni         | Sì             | Sì               |
| &bull; Dipendenze                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |                 | Sì               | 
| &nbsp;&nbsp;&nbsp;&mdash; ServiceBus|                 | Sì               | 
| &nbsp;&nbsp;&nbsp;&mdash; EventHub  |                 | Sì               | 
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |                 | Sì               | 
| | | | 
| **Funzionalità supportate**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | Sì             | Sì               | 
| &nbsp;&nbsp;&nbsp;&mdash; Canale di controllo sicuro|                 | Sì               | 
| &bull; Campionamento                     | Sì             | Sì               | 
| &bull; Heartbeat                   |                 | Sì               | 
| | | | 
| **correlazione**                       |                   |                   |               
| &bull; ServiceBus                     |                   | Sì               | 
| &bull; EventHub                       |                   | Sì               | 
| | | | 
| **Configurabile**                      |                   |                   |           
| &bull;Completamente configurabile.<br/>Per istruzioni, vedere [Funzioni di Azure](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852).<br/>Per tutte le opzioni, vedere [Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration).               |                   | Sì                   | 


## <a name="performance-counters"></a>Contatori delle prestazioni

La raccolta automatica dei contatori delle prestazioni funziona solo sui computer Windows.


## <a name="live-metrics--secure-control-channel"></a>Metriche attive e canale di controllo sicuro

I criteri di filtri personalizzati specificati dall'utente vengono inviati al componente Metriche attive in Application Insights SDK. I filtri potrebbero contenere informazioni riservate, ad esempio ID cliente. È possibile proteggere il canale con una chiave API privata. Per istruzioni, vedere [Proteggere il canale di controllo](./live-stream.md#secure-the-control-channel).

## <a name="sampling"></a>campionamento

Per impostazione predefinita, Funzioni di Azure abilita il campionamento durante la configurazione. Per altre informazioni, vedere [Configurare il campionamento](../../azure-functions/functions-monitoring.md#configure-sampling).

Se il progetto prende una dipendenza da Application Insights SDK per eseguire il rilevamento manuale della telemetria, potrebbe verificarsi un comportamento strano se la configurazione del campionamento è diversa dalla configurazione del campionamento delle funzioni. 

È consigliabile usare la stessa configurazione delle funzioni. Con **funzioni V2**è possibile ottenere la stessa configurazione usando l'inserimento di dipendenze nel costruttore:

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;

public class Function1 
{

    private readonly TelemetryClient telemetryClient;

    public Function1(TelemetryConfiguration configuration)
    {
        this.telemetryClient = new TelemetryClient(configuration);
    }

    [FunctionName("Function1")]
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, ILogger logger)
    {
        this.telemetryClient.TrackTrace("C# HTTP trigger function processed a request.");
    }
}
```

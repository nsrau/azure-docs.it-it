---
title: Azure Application Insights - Funzionalità supportate di Funzioni di Azure | Microsoft Docs
description: Funzionalità supportate di Application Insights per Funzioni di Azure
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: ''
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 4/23/2019
ms.reviewer: mbullwin
ms.author: tilee
ms.openlocfilehash: 46e7f5b5376622be9a05265942b6aceb38feebac
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72372447"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Application Insights per le funzionalità supportate di Funzioni di Azure

Funzioni di Azure offre l'[integrazione predefinita](../../azure-functions/functions-monitoring.md) con Application Insights che è disponibile tramite l'interfaccia ILogger. Di seguito è riportato un elenco delle funzionalità attualmente supportate. Per un'[introduzione](../../azure-functions/functions-monitoring.md#enable-application-insights-integration), vedere la guida di Funzioni di Azure.

## <a name="supported-features"></a>Funzionalità supportate

| Funzioni di Azure                       | V1                | V2 (Ignite 2018)  | 
|-----------------------------------    |---------------    |------------------ |
| **Application Insights .NET SDK**   | **2.5.0**       | **2.9.1**         |
| | | | 
| **Raccolta automatica di**        |                 |                   |               
| &bull; Richieste                     | SÌ             | SÌ               | 
| &bull; Eccezioni                   | SÌ             | SÌ               | 
| &bull; Contatori delle prestazioni         | SÌ             | SÌ               |
| &bull; Dipendenze                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |                 | SÌ               | 
| &nbsp;&nbsp;&nbsp;&mdash; ServiceBus|                 | SÌ               | 
| &nbsp;&nbsp;&nbsp;&mdash; EventHub  |                 | SÌ               | 
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |                 | SÌ               | 
| | | | 
| **Funzionalità supportate**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | SÌ             | SÌ               | 
| &nbsp;&nbsp;&nbsp;&mdash; Canale di controllo sicuro|                 | SÌ               | 
| &bull; Campionamento                     | SÌ             | SÌ               | 
| &bull; Heartbeat                   |                 | SÌ               | 
| | | | 
| **Correlazione**                       |                   |                   |               
| &bull; ServiceBus                     |                   | SÌ               | 
| &bull; EventHub                       |                   | SÌ               | 
| | | | 
| **Configurabile**                      |                   |                   |           
| &bull;Completamente configurabile.<br/>Per istruzioni, vedere [Funzioni di Azure](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852).<br/>Per tutte le opzioni, vedere [Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration).               |                   | SÌ                   | 


## <a name="performance-counters"></a>Contatori delle prestazioni

La raccolta automatica dei contatori delle prestazioni funziona solo sui computer Windows.


## <a name="live-metrics--secure-control-channel"></a>Metriche attive e canale di controllo sicuro

I criteri di filtri personalizzati specificati dall'utente vengono inviati al componente Metriche attive in Application Insights SDK. I filtri potrebbero contenere informazioni riservate, ad esempio ID cliente. È possibile proteggere il canale con una chiave API privata. Per istruzioni, vedere [Proteggere il canale di controllo](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel).

## <a name="sampling"></a>campionamento

Per impostazione predefinita, Funzioni di Azure abilita il campionamento durante la configurazione. Per altre informazioni, vedere [Configurare il campionamento](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling).

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

---
title: Azure Application Insights - Funzionalità supportate di Funzioni di Azure | Microsoft Docs
description: Funzionalità supportate di Application Insights per Funzioni di Azure
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: ''
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 10/05/2018
ms.reviewer: mbullwin
ms.author: tilee
ms.openlocfilehash: 101c985178b8269b4ff542b94b057330d0c2652a
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59496062"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Application Insights per le funzionalità supportate di Funzioni di Azure

Funzioni di Azure offre l'[integrazione predefinita](https://docs.microsoft.com/azure/azure-functions/functions-monitoring) con Application Insights che è disponibile tramite l'interfaccia ILogger. Di seguito è riportato un elenco delle funzionalità attualmente supportate. Per un'[introduzione](https://github.com/Azure/Azure-Functions/wiki/App-Insights), vedere la guida di Funzioni di Azure.

## <a name="supported-features"></a>Funzionalità supportate

| Funzioni di Azure                       | V1                | V2 (Ignite 2018)  | 
|-----------------------------------    |---------------    |------------------ |
| **Application Insights .NET SDK**   | **2.5.0**       | **2.9.1**         |
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
| **Correlazione**                       |                   |                   |               
| &bull; ServiceBus                     |                   | Sì               | 
| &bull; EventHub                       |                   | Sì               | 
| | | | 
| **Configurabile**                      |                   |                   |           
| &bull;Completamente configurabile.<br/>Per istruzioni, vedere [Funzioni di Azure](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852).<br/>Per tutte le opzioni, vedere [Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration).               |                   | Sì                   | 


## <a name="performance-counters"></a>Contatori delle prestazioni

La raccolta automatica dei contatori delle prestazioni funziona solo sui computer Windows.


## <a name="live-metrics--secure-control-channel"></a>Metriche attive e canale di controllo sicuro

I criteri di filtri personalizzati specificati dall'utente vengono inviati al componente Metriche attive in Application Insights SDK. I filtri potrebbero contenere informazioni riservate, ad esempio ID cliente. È possibile proteggere il canale con una chiave API privata. Per istruzioni, vedere [Proteggere il canale di controllo](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel).

## <a name="sampling"></a>campionamento

Per impostazione predefinita, Funzioni di Azure abilita il campionamento durante la configurazione. Per altre informazioni, vedere [Configurare il campionamento](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling).

Se il progetto abbia una dipendenza da Application Insights SDK per eseguire operazioni manuali dati di telemetria di rilevamento, se la configurazione di campionamento è diversa rispetto alla configurazione di campionamento di funzioni potrebbe verificarsi un comportamento anomalo. 

È consigliabile usare la stessa configurazione come funzioni. Con **funzioni v2**, è possibile ottenere la stessa configurazione usando l'inserimento delle dipendenze nel costruttore:

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

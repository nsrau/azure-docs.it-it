---
title: Monitoraggio dell'utilizzo e delle prestazioni per le applicazioni desktop di Windows
description: Analizzare l'uso e le prestazioni dell'applicazione desktop di Windows con Application Insights.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 19040746-3315-47e7-8c60-4b3000d2ddc4
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: mbullwin
ms.openlocfilehash: 10044f17baee8fb4d7afe9223abdbb518952e71f
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2018
ms.locfileid: "52741859"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>Monitoraggio dell'utilizzo e delle prestazioni nelle applicazioni desktop di Windows classiche

Tutte le applicazioni ospitate in locale, in Azure e in altri cloud possono sfruttare i vantaggi di Application Insights. L'unica limitazione è la necessità di [consentire la comunicazione](app-insights-ip-addresses.md) al servizio Application Insights. Per il monitoraggio delle applicazioni della piattaforma UWP (Universal Windows Platform), è consigliabile usare [Visual Studio App Center](app-insights-mobile-center-quickstart.md).

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>Per inviare dati di telemetria ad Application Insights da un'applicazione di Windows classica
1. Nel [portale di Azure](https://portal.azure.com) [creare una risorsa di Application Insights](app-insights-create-new-resource.md). Scegliere l'app ASP.NET per il tipo di applicazione.
2. Eseguire una copia della chiave di strumentazione. Trovare la chiave nell'elenco a discesa Informazioni di base della nuova risorsa appena creata. 
3. In Visual Studio, modificare i pacchetti NuGet del progetto dell'app e aggiungere Microsoft.ApplicationInsights.WindowsServer. In alternativa, se si vuole ottenere solo l'API, senza i moduli di raccolta dei dati di telemetria standard, scegliere Microsoft.ApplicationInsights.
4. Impostare la chiave di strumentazione nel codice:
   
    `TelemetryConfiguration.Active.InstrumentationKey = "`*nome della chiave*`";`
   
    o in ApplicationInsights.config, se è installato uno dei pacchetti di telemetria standard:
   
    `<InstrumentationKey>`*chiave*`</InstrumentationKey>` 
   
    Se si utilizza Applicationinsights.config, assicurarsi che le proprietà in Esplora soluzione siano impostate su **Build Action = Content, Copy to Output Directory = Copy**.
5. [Usare l'API](app-insights-api-custom-events-metrics.md) per inviare dati di telemetria.
6. Eseguire l'app e visualizzare i dati di telemetria nella risorsa creata nel portale di Azure.

## <a name="telemetry"></a>Codice di esempio
```csharp

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.UserName;
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="next-steps"></a>Passaggi successivi
* [Creare un dashboard](app-insights-dashboards.md)
* [Ricerca diagnostica](app-insights-diagnostic-search.md)
* [Esplorare le metriche](app-insights-metrics-explorer.md)
* [Scrivere query di Analisi](app-insights-analytics.md)


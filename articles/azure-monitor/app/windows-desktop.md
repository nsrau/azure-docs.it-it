---
title: Monitoraggio dell'utilizzo e delle prestazioni per le applicazioni desktop di Windows
description: Analizzare l'uso e le prestazioni dell'applicazione desktop di Windows con Application Insights.
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 8234b9ba2c92fc64cfa8f598db99954e00caab45
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670832"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>Monitoraggio dell'utilizzo e delle prestazioni nelle applicazioni desktop di Windows classiche

Tutte le applicazioni ospitate in locale, in Azure e in altri cloud possono sfruttare i vantaggi di Application Insights. L'unica limitazione è la necessità di [consentire la comunicazione](../../azure-monitor/app/ip-addresses.md) al servizio Application Insights. Per il monitoraggio delle applicazioni della piattaforma UWP (Universal Windows Platform), è consigliabile usare [Visual Studio App Center](../../azure-monitor/learn/mobile-center-quickstart.md).

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>Per inviare dati di telemetria ad Application Insights da un'applicazione di Windows classica
1. Nel [portale di Azure](https://portal.azure.com)[creare una risorsa di Application Insights](../../azure-monitor/app/create-new-resource.md ). Scegliere l'app ASP.NET per il tipo di applicazione.
2. Eseguire una copia della chiave di strumentazione. Trovare la chiave nell'elenco a discesa Informazioni di base della nuova risorsa appena creata. 
3. In Visual Studio, modificare i pacchetti NuGet del progetto dell'app e aggiungere Microsoft.ApplicationInsights.WindowsServer. In alternativa, se si vuole ottenere solo l'API, senza i moduli di raccolta dei dati di telemetria standard, scegliere Microsoft.ApplicationInsights.
4. Impostare la chiave di strumentazione nel codice:
   
    `TelemetryConfiguration.Active.InstrumentationKey = "` *la chiave* `";`
   
    o in ApplicationInsights.config, se è installato uno dei pacchetti di telemetria standard:
   
    `<InstrumentationKey>`*nome della chiave*`</InstrumentationKey>` 
   
    Se si utilizza Applicationinsights.config, assicurarsi che le proprietà in Esplora soluzione siano impostate su **Build Action = Content, Copy to Output Directory = Copy**.
5. [Usare l'API](../../azure-monitor/app/api-custom-events-metrics.md) per inviare dati di telemetria.
6. Eseguire l'app e visualizzare i dati di telemetria nella risorsa creata nella portale di Azure.

## <a name="telemetry"></a>Codice di esempio
```csharp
using Microsoft.ApplicationInsights;

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(System.ComponentModel.CancelEventArgs e)
        {
            e.Cancel = true;

            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="override-storage-of-computer-name"></a>Sostituisci archiviazione del nome del computer

Per impostazione predefinita, l'SDK raccoglie e archivia il nome computer del sistema che emette i dati di telemetria. Per eseguire l'override della raccolta è necessario usare un inizializzatore di telemetria:

**Scrivere personalizzata telemetryinitializer personalizzati come indicato di seguito.**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here, you can pass an empty string if needed.
                  telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```
Creare un'istanza dell'inizializzatore nel metodo `Program.cs` `Main()` seguente impostazione della chiave di strumentazione:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

   static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
        }
```

## <a name="next-steps"></a>Passaggi successivi
* [Creare un dashboard](../../azure-monitor/app/overview-dashboard.md)
* [Ricerca diagnostica](../../azure-monitor/app/diagnostic-search.md)
* [Esplorare le metriche](../../azure-monitor/app/metrics-explorer.md)
* [Scrivere query di Analisi](../../azure-monitor/app/analytics.md)


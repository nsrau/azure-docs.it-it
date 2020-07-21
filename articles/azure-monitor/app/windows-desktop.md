---
title: Monitoraggio dell'utilizzo e delle prestazioni per le applicazioni desktop di Windows
description: Analizzare l'uso e le prestazioni dell'applicazione desktop di Windows con Application Insights.
ms.topic: conceptual
ms.date: 06/11/2020
ms.openlocfilehash: ddb602536e1b8bbc987c4ba366e2007163c814ec
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86499189"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>Monitoraggio dell'utilizzo e delle prestazioni nelle applicazioni desktop di Windows classiche

Tutte le applicazioni ospitate in locale, in Azure e in altri cloud possono sfruttare i vantaggi di Application Insights. L'unica limitazione è la necessità di [consentire la comunicazione](../../azure-monitor/app/ip-addresses.md) al servizio Application Insights. Per il monitoraggio delle applicazioni della piattaforma UWP (Universal Windows Platform), è consigliabile usare [Visual Studio App Center](../../azure-monitor/learn/mobile-center-quickstart.md).

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>Per inviare dati di telemetria ad Application Insights da un'applicazione di Windows classica
1. Nel [portale di Azure](https://portal.azure.com)[creare una risorsa di Application Insights](../../azure-monitor/app/create-new-resource.md ). 
2. Eseguire una copia della chiave di strumentazione.
3. In Visual Studio, modificare i pacchetti NuGet del progetto dell'app e aggiungere Microsoft.ApplicationInsights.WindowsServer. In alternativa, scegliere Microsoft. ApplicationInsights se si vuole solo l'API di base, senza i moduli standard della raccolta dei dati di telemetria.
4. Impostare la chiave di strumentazione nel codice:
   
    `TelemetryConfiguration.Active.InstrumentationKey = "` *nome della chiave* `";`
   
    o in ApplicationInsights.config, se è installato uno dei pacchetti di telemetria standard:
   
    `<InstrumentationKey>`*chiave*`</InstrumentationKey>` 
   
    Se si utilizza Applicationinsights.config, assicurarsi che le proprietà in Esplora soluzione siano impostate su **Build Action = Content, Copy to Output Directory = Copy**.
5. [Usare l'API](../../azure-monitor/app/api-custom-events-metrics.md) per inviare dati di telemetria.
6. Eseguire l'app e visualizzare i dati di telemetria nella risorsa creata nella portale di Azure.

## <a name="example-code"></a><a name="telemetry"></a>Codice di esempio

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

Per impostazione predefinita, l'SDK raccoglie e archivia il nome computer del sistema che emette i dati di telemetria.

Il nome del computer viene usato da Application Insights piano [tariffario Enterprise (per nodo) legacy](./pricing.md#legacy-enterprise-per-node-pricing-tier) per finalità di fatturazione interne. Per impostazione predefinita, se si usa un inizializzatore di telemetria per eseguire l'override `telemetry.Context.Cloud.RoleInstance` , viene inviata una proprietà separata che conterrà `ai.internal.nodeName` comunque il valore del nome del computer. Questo valore non verrà archiviato con i dati di telemetria Application Insights, ma viene usato internamente all'inserimento per consentire la compatibilità con le versioni precedenti con il modello di fatturazione legacy basato su nodi.

Se si usa il piano [tariffario Enterprise (per nodo) legacy](./pricing.md#legacy-enterprise-per-node-pricing-tier) e si deve semplicemente eseguire l'override dell'archiviazione del nome del computer, usare un inizializzatore di telemetria:

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
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleInstance))
            {
                // Set custom role name here. Providing an empty string will result
                // in the computer name still be sent via this property.
                  telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```

Creare un'istanza dell'inizializzatore nel `Program.cs` `Main()` metodo seguente impostando la chiave di strumentazione:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

   static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
            //...
        }
```

## <a name="override-transmission-of-computer-name"></a>Sostituzione della trasmissione del nome del computer

Se non si usa il piano [tariffario Enterprise (per nodo) legacy](./pricing.md#legacy-enterprise-per-node-pricing-tier) e si vuole impedire completamente la telemetria contenente il nome del computer, è necessario usare un processore di telemetria.

### <a name="telemetry-processor"></a>Processore di telemetria

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;


namespace WindowsFormsApp2
{
    public class CustomTelemetryProcessor : ITelemetryProcessor
    {
        private readonly ITelemetryProcessor _next;

        public CustomTelemetryProcessor(ITelemetryProcessor next)
        {
            _next = next;
        }

        public void Process(ITelemetry item)
        {
            if (item != null)
            {
                item.Context.Cloud.RoleInstance = string.Empty;
            }

            _next.Process(item);
        }
    }
}
```

Creare un'istanza del processore di telemetria nel `Program.cs` `Main()` metodo seguente impostando la chiave di strumentazione:

```csharp
using Microsoft.ApplicationInsights.Extensibility;

namespace WindowsFormsApp2
{
    static class Program
    {
        static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
            builder.Use((next) => new CustomTelemetryProcessor(next));
            builder.Build();
            //...
        }
    }
}

```

> [!NOTE]
> Sebbene sia tecnicamente possibile usare un processore di telemetria come descritto in precedenza anche se si usa il piano [tariffario Enterprise (per nodo) legacy](./pricing.md#legacy-enterprise-per-node-pricing-tier), questa operazione comporterà il rischio di overbilling a causa dell'impossibilità di distinguere correttamente i nodi per i prezzi per nodo.

## <a name="next-steps"></a>Passaggi successivi
* [Creare un dashboard](../../azure-monitor/app/overview-dashboard.md)
* [Ricerca diagnostica](../../azure-monitor/app/diagnostic-search.md)
* [Esplorare le metriche](../../azure-monitor/platform/metrics-charts.md)
* [Scrivere query di Analisi](../log-query/log-query-overview.md)

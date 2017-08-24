---
title: Aggregazione di eventi di Azure Service Fabric con EventFlow | Microsoft Docs
description: Informazioni sull'aggregazione e la raccolta di eventi con EventFlow per il monitoraggio e la diagnostica dei cluster di Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: dekapur
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: b8b4c3e48136c47eff6154abcf7ea5d7720be827
ms.contentlocale: it-it
ms.lasthandoff: 05/31/2017


---

# <a name="event-aggregation-and-collection-using-eventflow"></a>Aggregazione e raccolta di eventi con EventFlow

[Microsoft Diagnostics EventFlow](https://github.com/Azure/diagnostics-eventflow) può indirizzare gli eventi da un nodo a una o più destinazioni di monitoraggio. Poiché è incluso come pacchetto NuGet nel progetto del servizio, il codice e la configurazione di EventFlow vanno di pari passo con il servizio, eliminando i problemi di configurazione specifici di ogni nodo indicati in precedenza e relativi a Diagnostica di Azure. EventFlow viene eseguito nel processo del servizio e si connette direttamente agli output configurati. A causa della connessione diretta, EventFlow funziona con le distribuzioni dei servizi in Azure, nei contenitori e in locale. Se si esegue EventFlow in scenari a densità elevata, ad esempio in un contenitore, è necessario prestare attenzione perché ogni pipeline di EventFlow stabilisce una connessione esterna. Pertanto, se si ospitano molti processi, si ottengono molte connessioni in uscita. Questo non costituisce un problema per le applicazioni di Service Fabric, perché tutte le repliche di `ServiceType` sono eseguite nello stesso processo e ciò limita il numero di connessioni in uscita. EventFlow offre anche i filtri per gli eventi, in modo che vengano inviati solo gli eventi corrispondenti al filtro specificato.

## <a name="setting-up-eventflow"></a>Configurazione di EventFlow

I file binari di EventFlow sono disponibili come set di pacchetti NuGet. Per aggiungere EventFlow a un progetto di servizio di Service Fabric, fare clic sul progetto in Esplora soluzioni e scegliere "Gestisci pacchetti NuGet". Passare alla scheda "Sfoglia" e cercare "`Diagnostics.EventFlow`":

![Pacchetti NuGet di EventFlow nell'interfaccia utente di gestione pacchetti NuGet di Visual Studio](./media/service-fabric-diagnostics-event-aggregation-eventflow/eventflow-nuget.png)

Verrà visualizzato un elenco dei vari pacchetti evidenziati, con etichetta "Input" e "Output". EventFlow supporta diversi provider di accesso e analizzatori. Il servizio che ospita EventFlow deve includere pacchetti appropriati a seconda dell'origine e della destinazione dei log applicazioni. Oltre al pacchetto di base ServiceFabric, è anche necessario aver configurato almeno un Input e un Output. Ad esempio, è possibile aggiungere i pacchetti seguenti agli eventi EventSource inviati ad Application Insights:

* `Microsoft.Diagnostics.EventFlow.Input.EventSource` per acquisire dati dalla classe EventSource del servizio e da oggetti EventSource standard, ad esempio *Microsoft-ServiceFabric-Services* e *Microsoft-ServiceFabric-Actors*
* `Microsoft.Diagnostics.EventFlow.Output.ApplicationInsights`, i log verranno inviati a una risorsa di Azure Application Insights
* `Microsoft.Diagnostics.EventFlow.ServiceFabric`, consente l'inizializzazione della pipeline EventFlow dalla configurazione del servizio di Service Fabric e segnala eventuali problemi tramite l'invio di dati di diagnostica in forma di report sull'integrità di Service Fabric

>[!NOTE]
>Per il pacchetto `Microsoft.Diagnostics.EventFlow.Input.EventSource` il progetto di servizio deve puntare a .NET Framework 4.6 o versione successiva. Assicurarsi di impostare il framework di destinazione corretto nelle proprietà del progetto prima di installare questo pacchetto.

Dopo aver installato tutti i pacchetti, è necessario configurare e abilitare EventFlow nel servizio.

## <a name="configuring-and-enabling-log-collection"></a>Configurazione e abilitazione della raccolta di log
La pipeline EventFlow, responsabile dell'invio dei log, viene creata da una specifica archiviata in un file di configurazione. Il pacchetto `Microsoft.Diagnostics.EventFlow.ServiceFabric` installa un file di configurazione EventFlow iniziale nella cartella `PackageRoot\Config` della soluzione denominata `eventFlowConfig.json`. Questo file di configurazione deve essere modificato per l'acquisizione di dati dalla classe `EventSource` del servizio predefinito, oltre a qualsiasi altro input che si desidera configurare, e per l'invio di dati alla destinazione appropriata.

Ecco un esempio di *eventFlowConfig.json* basato sui pacchetti NuGet indicati in precedenza:
```json
{
  "inputs": [
    {
      "type": "EventSource",
      "sources": [
        { "providerName": "Microsoft-ServiceFabric-Services" },
        { "providerName": "Microsoft-ServiceFabric-Actors" },
        // (replace the following value with your service's ServiceEventSource name)
        { "providerName": "your-service-EventSource-name" }
      ]
    }
  ],
  "filters": [
    {
      "type": "drop",
      "include": "Level == Verbose"
    }
  ],
  "outputs": [
    {
      "type": "ApplicationInsights",
      // (replace the following value with your AI resource's instrumentation key)
      "instrumentationKey": "00000000-0000-0000-0000-000000000000"
    }
  ],
  "schemaVersion": "2016-08-11"
}
```

Il nome dell'oggetto ServiceEventSource del servizio è il valore della proprietà Name dell'oggetto `EventSourceAttribute` applicato alla classe ServiceEventSource. Tutto questo è specificato nel file `ServiceEventSource.cs`, che fa parte del codice del servizio. Ad esempio, nel frammento di codice seguente il nome dell'oggetto ServiceEventSource è *MyCompany-Application1-Stateless1*:

```csharp
[EventSource(Name = "MyCompany-Application1-Stateless1")]
internal sealed class ServiceEventSource : EventSource
{
    // (rest of ServiceEventSource implementation)
}
```

Si noti che il file `eventFlowConfig.json` fa parte del pacchetto di configurazione del servizio. Le modifiche apportate a questo file possono essere incluse in aggiornamenti del servizio completi o della sola configurazione, in base ai controlli di integrità dell'aggiornamento di Service Fabric e al ripristino dello stato precedente automatico in caso di errore di aggiornamento. Per altre informazioni, vedere [Aggiornamento delle applicazioni di Service Fabric](service-fabric-application-upgrade.md).

La sezione dei *filtri* della configurazione consente di personalizzare ulteriormente le informazioni che si intende far passare attraverso la pipeline EventFlow fino agli output, consentendo di eliminare o includere determinate informazioni o di modificare la struttura dei dati dell'evento. Per altre informazioni sui filtri, controllare i [filtri EventFlow](https://github.com/Azure/diagnostics-eventflow#filters).

Il passaggio finale consiste nel creare un'istanza della pipeline EventFlow nel codice di avvio del servizio, situato nel file `Program.cs`:

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric;
using Microsoft.ServiceFabric.Services.Runtime;

// **** EventFlow namespace
using Microsoft.Diagnostics.EventFlow.ServiceFabric;

namespace Stateless1
{
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>
        private static void Main()
        {
            try
            {
                // **** Instantiate log collection via EventFlow
                using (var diagnosticsPipeline = ServiceFabricDiagnosticPipelineFactory.CreatePipeline("MyApplication-MyService-DiagnosticsPipeline"))
                {

                    ServiceRuntime.RegisterServiceAsync("Stateless1Type",
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

                    Thread.Sleep(Timeout.Infinite);
                }
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
                throw;
            }
        }
    }
}
```

Il nome passato come parametro del metodo `CreatePipeline` di `ServiceFabricDiagnosticsPipelineFactory` è il nome dell'*entità di integrità* che rappresenta la pipeline di raccolta di log EventFlow. Questo nome viene usato se EventFlow rileva un errore e lo segnala tramite il sottosistema di integrità di Service Fabric.

### <a name="using-service-fabric-settings-and-application-parameters-to-in-eventflowconfig"></a>Uso delle impostazioni di infrastruttura di Service Fabric e dei parametri dell'applicazione in eventFlowConfig

EventFlow supporta l'uso delle impostazioni di Service Fabric e dei parametri dell'applicazione per configurare le impostazioni di EventFlow. È possibile fare riferimento ai parametri delle impostazioni di Service Fabric usando questa sintassi speciale per i valori:

```json
servicefabric:/<section-name>/<setting-name>
``` 

`<section-name>` è il nome della sezione di configurazione di Service Fabric e `<setting-name>` è l'impostazione di configurazione che indica il valore che verrà usato per configurare un'impostazione EventFlow. Per altre informazioni su come effettuare questa operazione, passare a [Support for Service Fabric settings and application parameters](https://github.com/Azure/diagnostics-eventflow#support-for-service-fabric-settings-and-application-parameters) (Supporto per le impostazioni e i parametri dell'applicazione Service Fabric).

## <a name="verification"></a>Verifica

Avviare il servizio e osservare la finestra dell'output di debug di Visual Studio. Dopo l'avvio del servizio, dovrebbero essere visibili le prove dell'invio di record del servizio all'output configurato. Passare alla piattaforma di analisi e visualizzazione di eventi e confermare che i registri vengono visualizzati. Questa operazione potrebbe richiedere alcuni minuti.

## <a name="next-steps"></a>Passaggi successivi

* [Event Analysis and Visualization with Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) (Analisi e visualizzazione degli eventi con Application Insights)
* [Event Analysis and Visualization with OMS](service-fabric-diagnostics-event-analysis-oms.md) (Analisi e visualizzazione degli eventi con OMS)
* [Documentazione di EventFlow](https://github.com/Azure/diagnostics-eventflow)

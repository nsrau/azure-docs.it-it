---
title: Raccogliere log direttamente da un processo del servizio di Azure Service Fabric | Microsoft Azure
description: Questo articolo illustra come usare le applicazioni di Service Fabric per inviare log direttamente a una posizione centralizzata, come Azure Application Insights o Elasticsearch, senza basarsi sull&quot;agente di Diagnostica di Azure.
services: service-fabric
documentationcenter: .net
author: karolz-ms
manager: rwike77
editor: 
ms.assetid: ab92c99b-1edd-4677-8c28-4e591d909b47
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/18/2017
ms.author: karolz
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 08f7b57985382f2abbb90ba1e13a30f40b38917f
ms.contentlocale: it-it
ms.lasthandoff: 06/15/2017


---
# <a name="collect-logs-directly-from-an-azure-service-fabric-service-process"></a>Raccogliere log direttamente da un processo del servizio di Azure Service Fabric
## <a name="in-process-log-collection"></a>Raccolta di log nel processo
Nei servizi di **Azure Service Fabric** è possibile raccogliere log applicazioni tramite l'[estensione Diagnostica di Azure](service-fabric-diagnostics-how-to-setup-wad.md) se il set di origini e destinazioni di log è di piccole dimensioni e non cambia spesso e se il mapping tra le origini e le relative destinazioni è semplice. In caso contrario, è possibile consentire ai servizi di inviare i log direttamente a una posizione centralizzata. Questo processo è noto come **raccolta di log nel processo** e presenta diversi vantaggi potenziali:

* *Facilità di configurazione e distribuzione*

    * La configurazione della raccolta dei dati di diagnostica è solo una parte della configurazione del servizio ed è facile mantenerla sempre "sincronizzata" con il resto dell'applicazione.
    * È facile ottenere la configurazione per ogni applicazione o servizio.
        * La raccolta di log basata su agenti richiede in genere una distribuzione e configurazione separate dell'agente di diagnostica, che costituisce un'attività amministrativa aggiuntiva e una potenziale fonte di errori. Spesso è consentita solo un'istanza dell'agente per ogni macchina virtuale (nodo) e la configurazione dell'agente è condivisa tra tutte le applicazioni e i servizi in esecuzione nel nodo. 

* *Flessibilità*
   
    * L'applicazione può inviare i dati ogni volta che è necessario, purché sia disponibile una libreria client che supporta il sistema di archiviazione dati di destinazione. È possibile aggiungere nuove destinazioni in base alle esigenze.
    * È possibile implementare complesse regole di acquisizione, filtro e aggregazione dati.
    * La raccolta di log basata su agenti è spesso limitata dai sink di dati supportati dall'agente. Alcuni agenti sono estendibili.

* *Accesso al contesto e ai dati di applicazione interni*
   
    * Il sottosistema di diagnostica in esecuzione nel processo dell'applicazione o del servizio può facilmente aumentare le tracce con informazioni contestuali.
    * Nella raccolta di log basata su agenti i dati devono essere inviati a un agente tramite un meccanismo di comunicazione interprocesso, ad esempio Event Tracing for Windows (ETW). Questo meccanismo può imporre limitazioni aggiuntive.

È possibile combinare i due metodi di raccolta e trarre vantaggio da entrambi. In realtà, potrebbe essere la soluzione migliore per molte applicazioni. La raccolta basata su agenti è una soluzione naturale per la raccolta di log correlati all'intero cluster e a singoli nodi del cluster. Rispetto alla raccolta di log nel processo, è un modo molto più affidabile per diagnosticare i problemi di avvio e gli arresti anomali del servizio. In presenza di molti servizi in esecuzione all'interno di un cluster di Service Fabric, se ogni servizio esegue autonomamente la raccolta di log nel processo si avrà un gran numero di connessioni in uscita dal cluster. Questo può mettere a dura prova sia il sottosistema di rete che la destinazione di log. Un agente come [**Diagnostica di Azure**](../cloud-services/cloud-services-dotnet-diagnostics.md) può raccogliere dati da più servizi e inviarli tutti tramite un numero limitato di connessioni, migliorando così la velocità effettiva. 

Questo articolo illustra come configurare una raccolta di log nel processo usando la [**libreria open-source EventFlow**](https://github.com/Azure/diagnostics-eventflow). È possibile usare altre librerie per lo stesso scopo, ma EventFlow ha il vantaggio di essere stata progettata specificamente per la raccolta di log nel processo e per supportare i servizi di Service Fabric. Come destinazione di log viene usato [**Azure Application Insights**](https://azure.microsoft.com/services/application-insights/). Sono supportate anche altre destinazioni, ad esempio [**Hub eventi**](https://azure.microsoft.com/services/event-hubs/) o [**Elasticsearch**](https://www.elastic.co/products/elasticsearch). È sufficiente installare il pacchetto NuGet appropriato e configurare la destinazione nel file di configurazione EventFlow. Per altre informazioni sulle destinazioni di log diverse da Application Insights, vedere la [documentazione di EventFlow](https://github.com/Azure/diagnostics-eventflow).

## <a name="adding-eventflow-library-to-a-service-fabric-service-project"></a>Aggiunta della libreria EventFlow a un progetto di servizio di Service Fabric
I file binari di EventFlow sono disponibili come set di pacchetti NuGet. Per aggiungere EventFlow a un progetto di servizio di Service Fabric, fare clic sul progetto in Esplora soluzioni e scegliere "Gestisci pacchetti NuGet". Passare alla scheda "Sfoglia" e cercare "`Diagnostics.EventFlow`":

![Pacchetti NuGet di EventFlow nell'interfaccia utente di gestione pacchetti NuGet di Visual Studio][1]

Il servizio che ospita EventFlow deve includere pacchetti appropriati a seconda dell'origine e della destinazione dei log applicazioni. Aggiungere i pacchetti seguenti: 

* `Microsoft.Diagnostics.EventFlow.Inputs.EventSource` 
    * Per acquisire dati dalla classe EventSource del servizio e da oggetti EventSource standard, ad esempio *Microsoft-ServiceFabric-Services* e *Microsoft-ServiceFabric-Actors*.
* `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights` 
    * I log verranno inviati a una risorsa di Azure Application Insights.  
* `Microsoft.Diagnostics.EventFlow.ServiceFabric` 
    * Consente l'inizializzazione della pipeline EventFlow dalla configurazione del servizio di Service Fabric e segnala eventuali problemi tramite l'invio di dati di diagnostica in forma di report sull'integrità di Service Fabric.

> [!NOTE]
> Per il pacchetto `Microsoft.Diagnostics.EventFlow.Inputs.EventSource` il progetto di servizio deve puntare a .NET Framework 4.6 o versione successiva. Assicurarsi di impostare il framework di destinazione corretto nelle proprietà del progetto prima di installare questo pacchetto. 

Dopo aver installato tutti i pacchetti, è necessario configurare e abilitare EventFlow nel servizio.

## <a name="configuring-and-enabling-log-collection"></a>Configurazione e abilitazione della raccolta di log
La pipeline EventFlow, responsabile dell'invio dei log, viene creata da una specifica archiviata in un file di configurazione. Il pacchetto `Microsoft.Diagnostics.EventFlow.ServiceFabric` installa un file di configurazione di iniziale di EventFlow nella cartella `PackageRoot\Config` della soluzione. Il nome del file è `eventFlowConfig.json`. Questo file di configurazione deve essere modificato per l'acquisizione di dati dalla classe `EventSource` del servizio predefinito e l'invio di dati al servizio Application Insights.

> [!NOTE]
> Si presuppone che si abbia familiarità con il servizio **Azure Application Insights** e che sia disponibile una risorsa di Application Insights da usare per monitorare il servizio di Service Fabric. Per altre informazioni, vedere [Creare una risorsa di Application Insights](../application-insights/app-insights-create-new-resource.md).

Aprire il file `eventFlowConfig.json` nell'editor e modificarne il contenuto, come illustrato di seguito. Assicurarsi di sostituire il nome ServiceEventSource e la chiave di strumentazione di Application Insights in base ai commenti. 

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

> [!NOTE]
> Il nome dell'oggetto ServiceEventSource del servizio è il valore della proprietà Name dell'oggetto `EventSourceAttribute` applicato alla classe ServiceEventSource. Tutto questo è specificato nel file `ServiceEventSource.cs`, che fa parte del codice del servizio. Ad esempio, nel frammento di codice seguente il nome dell'oggetto ServiceEventSource è *MyCompany-Application1-Stateless1*:
> ```csharp
> [EventSource(Name = "MyCompany-Application1-Stateless1")]
> internal sealed class ServiceEventSource : EventSource
> {
>    // (rest of ServiceEventSource implementation)
>} 
> ```

Si noti che il file `eventFlowConfig.json` fa parte del pacchetto di configurazione del servizio. Le modifiche apportate a questo file possono essere incluse in aggiornamenti del servizio completi o della sola configurazione, in base ai controlli di integrità dell'aggiornamento di Service Fabric e al ripristino dello stato precedente automatico in caso di errore di aggiornamento. Per altre informazioni, vedere [Aggiornamento delle applicazioni di Service Fabric](service-fabric-application-upgrade.md).

Il passaggio finale consiste nel creare un'istanza della pipeline EventFlow nel codice di avvio del servizio, situato nel file `Program.cs`. L'esempio seguente mostra le aggiunte correlate a EventFlow contrassegnate con commenti, a partire da `****`:

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

## <a name="verification"></a>Verifica
Avviare il servizio e osservare la finestra dell'output di debug di Visual Studio. Dopo l'avvio del servizio, dovrebbero essere visibili le prove dell'invio di record di Application Insights Telemetry. Aprire un Web browser e passare alla risorsa di Application Insights. Aprire la scheda "Ricerca", nella parte superiore del pannello predefinito "Panoramica". Dopo alcuni istanti, le tracce dovrebbero essere visibili nel portale di Application Insights:

![Portale di Application Insights con i log da un'applicazione di Service Fabric][2]

## <a name="next-steps"></a>Passaggi successivi
* [Altre informazioni sulla diagnostica e il monitoraggio di un servizio di infrastruttura di servizi](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
* [Documentazione di EventFlow](https://github.com/Azure/diagnostics-eventflow)


<!--Image references-->
[1]: ./media/service-fabric-diagnostics-collect-logs-without-an-agent/eventflow-nugets.png
[2]: ./media/service-fabric-diagnostics-collect-logs-without-an-agent/ai-traces.png


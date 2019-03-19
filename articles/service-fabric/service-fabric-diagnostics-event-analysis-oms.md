---
title: I log di analisi eventi di Service Fabric Azure con monitoraggio di Azure | Microsoft Docs
description: Informazioni sulla visualizzazione e l'analisi di eventi usando i log di monitoraggio di Azure per il monitoraggio e diagnostica dei cluster di Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/21/2019
ms.author: srrengar
ms.openlocfilehash: 2f3106b33ab0cbea95efe2ac42c05a8543719190
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57246917"
---
# <a name="event-analysis-and-visualization-with-azure-monitor-logs"></a>Analisi degli eventi e la visualizzazione con i log di monitoraggio di Azure
 Log di monitoraggio di Azure raccoglie e analizza i dati di telemetria dalle applicazioni e servizi ospitati nel cloud e fornisce gli strumenti di analisi che consentono di ottimizzare la disponibilità e le prestazioni. Questo articolo illustra come eseguire query nei log di monitoraggio di Azure per ottenere informazioni dettagliate e risolvere i problemi di ciò che avviene nel cluster. Vengono affrontate le seguenti domande comuni:

* Com'è possibile risolvere i problemi relativi agli eventi di integrità?
* Com'è possibile sapere quando un nodo diventa inattivo?
* Com'è possibile sapere se i servizi dell'applicazione sono stati avviati o arrestati?

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview-of-the-log-analytics-workspace"></a>Panoramica dell'area di lavoro di Log Analitica

>[!NOTE] 
>Mentre l'archiviazione di diagnostica è abilitata per impostazione predefinita al momento della creazione del cluster, è tuttavia necessario configurare l'area di lavoro di Log Analytics per la lettura dall'archiviazione di diagnostica.

Monitoraggio di Azure vengono registrati i dati raccolti dalle risorse gestite, tra cui una tabella di archiviazione di Azure o un agente e li gestisce in un repository centrale. I dati possono essere quindi usati per analisi, avvisi e visualizzazioni o altre esportazioni. Monitoraggio di Azure Registra eventi supporta, i dati sulle prestazioni o altri dati personalizzati. Consulta [passaggi per configurare l'estensione diagnostica di aggregazione di eventi](service-fabric-diagnostics-event-aggregation-wad.md) e [passaggi per creare un'area di lavoro di Log Analitica per la lettura degli eventi nell'archiviazione](service-fabric-diagnostics-oms-setup.md) per assicurarsi che i dati vengono trasmessi a monitoraggio di Azure log.

Dopo la ricezione dei dati per i log di monitoraggio di Azure, Azure dispone di numerose *soluzioni di monitoraggio* che sono soluzioni predefinite o i dashboard operativi per monitorare i dati in ingresso, personalizzati per diversi scenari. Sono inclusi una soluzione di *Analisi Service Fabric* e una soluzione *contenitori*, le due soluzioni di diagnostica e monitoraggio più importanti se si usano i cluster di Service Fabric. In questo articolo viene descritto come usare la soluzione Analisi Service Fabric, che viene creata con l'area di lavoro.

## <a name="access-the-service-fabric-analytics-solution"></a>Accedere alla soluzione Analisi Service Fabric

Nel [portale di Azure](https://portal.azure.com), passare al gruppo di risorse in cui è stata creata la soluzione Service Fabric Analitica.

Selezionare la risorsa **ServiceFabric\<nomeareadilavoroOMS\>**.

In `Summary` vengono visualizzati riquadri sotto forma di grafo per ogni soluzione abilitata, tra cui uno per Service Fabric. Fare clic sul grafo **Service Fabric** per passare alla soluzione Analisi Service Fabric.

![Soluzione Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

L'immagine seguente mostra la home page della soluzione Analitica di Service Fabric. La presente pagina offre una visualizzazione snapshot delle operazioni eseguite nel cluster.

![Soluzione Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

 Se è stata abilitata la diagnostica al momento della creazione del cluster, è possibile visualizzare gli eventi per 

* [Eventi del cluster di Service Fabric](service-fabric-diagnostics-event-generation-operational.md)
* [Eventi del modello di programmazione Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Eventi relativi al modello di programmazione Reliable Services](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Oltre agli eventi predefiniti di Service Fabric, è possibile raccogliere eventi di sistema più dettagliati [aggiornando la configurazione dell'estensione di diagnostica](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

## <a name="view-service-fabric-events-including-actions-on-nodes"></a>Eventi di visualizzazione Service Fabric, ad esempio azioni sui nodi

Nella pagina Analisi Service Fabric fare clic sul grafo di **Eventi di Service Fabric**.

![Canale operativo della soluzione Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

Fare clic su **Elenco** per visualizzare gli eventi in un elenco. Qui è possibile osservare tutti gli eventi di sistema che sono stati raccolti. Per riferimento, si tratta dal **WADServiceFabricSystemEventsTable** in archiviazione di Azure account e, in modo analogo, gli eventi reliable services e Reliable actors visualizzati accanto provengono da tali tabelle corrispondenti.
    
![Canale operativo della query](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

In alternativa, è possibile fare clic sulla lente di ingrandimento a sinistra e usare il linguaggio di query Kusto per trovare gli elementi desiderati. Ad esempio, per trovare tutte le azioni eseguite sui nodi nel cluster, è possibile usare la query seguente. Gli ID evento usati di seguito sono disponibili nelle [informazioni di riferimento sugli eventi del canale operativo](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

È possibile eseguire query su molti più campi, ad esempio nodi specifici (Computer) o il servizio di sistema (TaskName).

## <a name="view-service-fabric-reliable-service-and-actor-events"></a>Visualizzare gli eventi di Reliable Services e Reliable Actors di Service Fabric

Nella pagina Analisi Service Fabric fare clic sul grafo di **Reliable Services**.

![Reliable Services della soluzione Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

Fare clic su **Elenco** per visualizzare gli eventi in un elenco. Qui è possibile visualizzare gli eventi relativi a Reliable Services. È possibile visualizzare eventi diversi per l'avvio e il completamento del servizio runasync, come in genere accade durante le distribuzioni e gli aggiornamenti. 

![Reliable Services della query](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_service_events.png)

Gli eventi relativi a Reliable Actors possono essere visualizzati in modo simile. Per configurare eventi più dettagliati per Reliable Actors, è necessario modificare `scheduledTransferKeywordFilter` nel file di configurazione per l'estensione di diagnostica (illustrato di seguito). I dettagli sui valori per questi elementi sono disponibili nelle [informazioni di riferimento sugli eventi di Reliable Actors](service-fabric-reliable-actors-diagnostics.md#keywords).

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

Il linguaggio di query Kusto è potente. Un'altra query importante che è possibile eseguire consente di trovare i nodi che generano il maggior numero di eventi. La query nello screenshot seguente illustra gli eventi operativi di Service Fabric aggregati con il servizio e il nodo specificati.

![Eventi della query per nodo](media/service-fabric-diagnostics-event-analysis-oms/oms_kusto_query.png)

## <a name="next-steps"></a>Passaggi successivi

* Per abilitare il monitoraggio dell'infrastruttura, ovvero i contatori delle prestazioni, vedere come [aggiungere l'agente di Log Analytics](service-fabric-diagnostics-oms-agent.md). L'agente raccoglie i contatori delle prestazioni e li aggiunge all'area di lavoro esistente.
* Per i cluster in locale, i log di monitoraggio di Azure offre un Gateway (Proxy di inoltro HTTP) che può essere utilizzato per inviare dati a log di monitoraggio di Azure. Per ulteriori informazioni in [connettere computer senza accesso a Internet per i log di monitoraggio di Azure usando il gateway Log Analitica](../azure-monitor/platform/gateway.md).
* Configurare gli [avvisi automatizzati](../log-analytics/log-analytics-alerts.md) in modo da semplificare il rilevamento e la diagnostica.
* Acquisire familiarità con le [ricerca log e l'esecuzione di query](../log-analytics/log-analytics-log-searches.md) funzionalità fornite come parte dei log di monitoraggio di Azure.
* Ottenere una panoramica più dettagliata dei log di monitoraggio di Azure e sui vantaggi offerti, leggere [What ' s i log di monitoraggio di Azure?](../operations-management-suite/operations-management-suite-overview.md).

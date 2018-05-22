---
title: Analisi di eventi di Azure Service Fabric con Log Analytics | Microsoft Docs
description: Informazioni sulla visualizzazione e l'analisi di eventi con Log Analytics per il monitoraggio e la diagnostica dei cluster di Azure Service Fabric.
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
ms.date: 04/16/2018
ms.author: srrengar
ms.openlocfilehash: b51f7dc43f390152b2b0be223541e381bbddd3c6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2018
---
# <a name="event-analysis-and-visualization-with-log-analytics"></a>Analisi e visualizzazione di eventi con Log Analytics

Log Analytics, anche noto come OMS (Operations Management Suite), è una raccolta di servizi di gestione che consentono di monitorare e diagnosticare le applicazioni e i servizi ospitati nel cloud. In questo articolo viene descritto come eseguire query in Log Analytics per ottenere informazioni e risolvere i problemi che si verificano nel cluster. Vengono affrontate le seguenti domande comuni:

* Com'è possibile risolvere i problemi relativi agli eventi di integrità?
* Com'è possibile sapere quando un nodo diventa inattivo?
* Com'è possibile sapere se i servizi dell'applicazione sono stati avviati o arrestati?

## <a name="log-analytics-workspace"></a>Area di lavoro di Log Analytics

Log Analytics raccoglie i dati dalle risorse gestite, tra cui una tabella di archiviazione o un agente di Azure, e li gestisce in un repository centrale. I dati possono essere quindi usati per analisi, avvisi e visualizzazioni o altre esportazioni. Log Analytics supporta i dati sulle prestazioni, sugli eventi o altri dati personalizzati. Per verificare il flusso dei dati in Log Analytics, vedere la [procedura per configurare l'estensione di diagnostica per l'aggregazione di eventi](service-fabric-diagnostics-event-aggregation-wad.md) e la [procedura per creare un'area di lavoro di Log Analytics per la lettura degli eventi nell'archiviazione](service-fabric-diagnostics-oms-setup.md).

Dopo la ricezione dei dati da Log Analytics, Azure dispone di numerose *soluzioni di gestione*, ovvero soluzioni predefinite per monitorare i dati in ingresso, personalizzati in base a diversi scenari. Sono inclusi una soluzione di *Analisi Service Fabric* e una soluzione *contenitori*, le due soluzioni di diagnostica e monitoraggio più importanti se si usano i cluster di Service Fabric. In questo articolo viene descritto come usare la soluzione Analisi Service Fabric, che viene creata con l'area di lavoro.

## <a name="access-the-service-fabric-analytics-solution"></a>Accedere alla soluzione Analisi Service Fabric

1. Andare al gruppo di risorse in cui è stata creata la soluzione Analisi Service Fabric. Selezionare la risorsa **ServiceFabric\<NomeAreaDiLavoroOMS\>** e passare alla relativa pagina di panoramica.

2. Nella pagina di panoramica fare clic sul collegamento nella parte superiore per passare al portale OMS

    ![Collegamento al portale di OMS](media/service-fabric-diagnostics-event-analysis-oms/oms-portal-link.png)

3. A questo punto viene visualizzato il portale di OMS ed è possibile vedere le soluzioni che sono state abilitate. Fare clic sul grafo denominato Service Fabric (prima immagine seguente) per passare alla soluzione Service Fabric (seconda immagine seguente)

    ![Soluzione OMS SF](media/service-fabric-diagnostics-event-analysis-oms/oms-workspace-all-solutions.png)

    ![Soluzione OMS SF](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics-new.png)

L'immagine precedente è la home page della soluzione Analisi Service Fabric. Viene fornita una visualizzazione di riepilogo delle operazioni eseguite nel cluster. Se è stata abilitata la diagnostica al momento della creazione del cluster, è possibile visualizzare gli eventi per 

* [Canale operativo](service-fabric-diagnostics-event-generation-operational.md): operazioni generali eseguite dalla piattaforma Service Fabric (raccolta di servizi di sistema).
* [Eventi del modello di programmazione Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Eventi relativi al modello di programmazione Reliable Services](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Oltre al canale operativo, è possibile raccogliere eventi di sistema più dettagliati [aggiornando la configurazione dell'estensione di diagnostica](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)

### <a name="view-operational-events-including-actions-on-nodes"></a>Visualizzare gli eventi operativi che includono azioni sui nodi

1. Nella pagina Analisi Service Fabric del portale di OMS fare clic sul grafo per il canale operativo

    ![Canale operativo della soluzione OMS SF](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics-new-operational.png)

2. Fare clic su Tabella per visualizzare gli eventi in un elenco. Qui è possibile osservare tutti gli eventi di sistema che sono stati raccolti. Come riferimento, questi elementi provengono da WADServiceFabricSystemEventsTable nell'account di Archiviazione di Azure. Analogamente, gli eventi di Reliable Services e Reliable Actors visualizzati accanto provengono dalle rispettive tabelle.
    
    ![Canale operativo della query OMS](media/service-fabric-diagnostics-event-analysis-oms/oms-query-operational-channel.png)

In alternativa, è possibile fare clic sulla lente di ingrandimento a sinistra e usare il linguaggio di query Kusto per trovare gli elementi desiderati. Ad esempio, per trovare tutte le azioni eseguite sui nodi nel cluster, è possibile usare la query seguente. Gli ID evento usati di seguito sono disponibili nelle [informazioni di riferimento sugli eventi del canale operativo](service-fabric-diagnostics-event-generation-operational.md)

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

È possibile eseguire query su molti più campi, ad esempio nodi specifici (Computer) o il servizio di sistema (TaskName).

### <a name="view-service-fabric-reliable-service-and-actor-events"></a>Visualizzare gli eventi di Reliable Services e Reliable Actors di Service Fabric

1. Nella pagina Analisi Service Fabric del portale di OMS fare clic sul grafo per Reliable Services

    ![Reliable Services della soluzione OMS SF](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics-reliable-services.png)

2. Fare clic su Tabella per visualizzare gli eventi in un elenco. Qui è possibile visualizzare gli eventi relativi a Reliable Services. È possibile visualizzare eventi diversi per l'avvio e il completamento del servizio runasync, come in genere accade durante le distribuzioni e gli aggiornamenti. 

    ![Reliable Services della query OMS](media/service-fabric-diagnostics-event-analysis-oms/oms-query-reliable-services.png)

Gli eventi relativi a Reliable Actors possono essere visualizzati in modo simile. Per configurare eventi più dettagliati per Reliable Actors, è necessario modificare `scheduledTransferKeywordFilter` nel file di configurazione per l'estensione di diagnostica (illustrato di seguito). I dettagli sui valori per questi elementi sono disponibili nelle [informazioni di riferimento sugli eventi di Reliable Actors](service-fabric-reliable-actors-diagnostics.md#keywords)

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

Il linguaggio di query Kusto è potente. Un'altra query importante che è possibile eseguire consente di trovare i nodi che generano il maggior numero di eventi. La query nello screenshot seguente mostra gli eventi di Reliable Services aggregati con il servizio e il nodo specifico

![Eventi della query OMS per nodo](media/service-fabric-diagnostics-event-analysis-oms/oms-query-events-per-node.png)

## <a name="next-steps"></a>Passaggi successivi

* Per abilitare il monitoraggio dell'infrastruttura, ovvero i contatori delle prestazioni, vedere come [aggiungere l'agente OMS](service-fabric-diagnostics-oms-agent.md). L'agente raccoglie i contatori delle prestazioni e li aggiunge all'area di lavoro esistente.
* Per i cluster locali, OMS offre un Gateway, ovvero un proxy di inoltro HTTP, che può essere usato per inviare i dati a OMS. Per altre informazioni leggere [Connettere computer senza accesso a Internet a OMS usando il gateway OMS](../log-analytics/log-analytics-oms-gateway.md)
* Configurare OMS per impostare [avvisi automatizzati](../log-analytics/log-analytics-alerts.md) in modo da semplificare il rilevamento e la diagnostica
* Acquisire familiarità con le funzionalità di [ricerca log e query](../log-analytics/log-analytics-log-searches.md) incluse in Log Analytics
* Per avere una panoramica più dettagliata di Log Analytics e dei vantaggi offerti, leggere [Informazioni su Log Analytics](../operations-management-suite/operations-management-suite-overview.md)

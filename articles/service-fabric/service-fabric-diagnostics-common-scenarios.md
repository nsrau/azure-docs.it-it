---
title: Scenari comuni di diagnosi per Azure Service Fabric | Microsoft Docs
description: Informazioni su come risolvere i problemi degli scenari comuni con Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/16/2018
ms.author: srrengar
ms.openlocfilehash: bd7a7e0288ced0219a0600034b273d1acba6b09b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659641"
---
# <a name="diagnose-common-scenarios-with-service-fabric"></a>Scenari comuni di diagnosi con Service Fabric

Questo articolo illustra gli scenari comuni affrontati dagli utenti nell'ambito del monitoraggio e della diagnostica con Service Fabric. Gli scenari indicati coprono tutti e 3 i livelli di Service Fabric: infrastruttura, cluster e applicazione. Ogni soluzione usa gli strumenti di monitoraggio di Azure, ovvero Application Insights e Log Analytics, per completare ogni scenario. I passaggi in ogni soluzione offrono agli utenti un'introduzione su come usare Application Insights e Log Analytics nel contesto di Service Fabric.

## <a name="prerequisites-and-recommendations"></a>Prerequisiti e indicazioni

Le soluzioni in questo articolo useranno gli strumenti seguenti. È consigliabile installarli e configurarli:

* [Application Insights con Service Fabric](service-fabric-tutorial-monitoring-aspnet.md)
* [Abilitare Diagnostica di Azure nel cluster](service-fabric-diagnostics-event-aggregation-wad.md)
* [Configurare un'area di lavoro di OMS Log Analytics](service-fabric-diagnostics-oms-setup.md)
* [Agente di OMS per tenere traccia dei contatori delle prestazioni](service-fabric-diagnostics-oms-agent.md)

## <a name="how-can-i-see-unhandled-exceptions-in-my-application"></a>Come visualizzare le eccezioni non gestite nell'applicazione

1. Passare alla risorsa di Application Insights con cui l'applicazione è configurata.
2. Fare clic su *Cerca* in alto a sinistra, quindi fare clic sul filtro nel pannello successivo.

    ![Panoramica di Application Insights](media/service-fabric-diagnostics-common-scenarios/ai-search-filter.png)

3. Verranno visualizzati molti tipi di eventi (tracce, richieste, eventi personalizzati). Scegliere "Eccezione" come filtro.

    ![Elenco di filtri AI](media/service-fabric-diagnostics-common-scenarios/ai-filter-list.png)

    Facendo clic su un'eccezione nell'elenco, è possibile vedere altri dettagli, incluso il contesto del servizio se si usa Service Fabric Application Insights SDK.

    ![Eccezione AI](media/service-fabric-diagnostics-common-scenarios/ai-exception.png)

## <a name="how-do-i-view-which-http-calls-are-used-in-my-services"></a>Come visualizzare le chiamate HTTP usate nei servizi

1. Nella stessa risorsa di Application Insights è possibile filtrare in base alle "richieste" invece che alle eccezioni e visualizzare tutte le richieste effettuate
2. Se si usa Service Fabric Application Insights SDK, è possibile visualizzare una rappresentazione visiva della connessione tra i servizi e il numero di richieste con esito positivo e negativo. A sinistra fare clic su "Mappa delle applicazioni"

    ![Pannello Mappa delle app di AI](media/service-fabric-diagnostics-common-scenarios/app-map-blade.png) ![Mappa delle app di AI](media/service-fabric-diagnostics-common-scenarios/app-map-new.png)

    Per altre informazioni sulla mappa delle app, vedere la [documentazione sulla mappa delle applicazioni](../application-insights/app-insights-app-map.md)

## <a name="how-do-i-create-an-alert-when-a-node-goes-down"></a>Come creare un avviso quando un nodo diventa inattivo

1. Il cluster di Service Fabric tiene traccia degli eventi dei nodi. Passare alla risorsa della soluzione Analisi Service Fabric denominata **ServiceFabric(NameofResourceGroup)**
2. Fare clic sul grafico nella parte inferiore del pannello intitolato "Riepilogo"

    ![Soluzione OMS](media/service-fabric-diagnostics-common-scenarios/oms-solution-azure-portal.png)

3. Sono disponibili molti grafici e riquadri con diverse metriche. Fare clic su uno dei grafici per passare alla ricerca log, dove è possibile cercare gli eventi del cluster o i contatori delle prestazioni.
4. Immettere la query seguente. Questi ID evento sono inclusi nella [documentazione di riferimento sugli eventi dei nodi](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventId >= 25623 or EventId <= 25626
    ```

5. Fare clic su "Nuova regola di avviso" nella parte superiore. Da questo momento, ogni volta che arriva un evento basato su questa query, si riceverà un avviso con il metodo di comunicazione scelto.

    ![Nuovo avviso di OMS](media/service-fabric-diagnostics-common-scenarios/oms-create-alert.png)

## <a name="how-can-i-be-alerted-of-application-upgrade-rollbacks"></a>Come essere avvisati dei ripristini dello stato precedente dell'aggiornamento dell'applicazione?

1. Nella stessa finestra di ricerca log di prima immettere la query seguente per i ripristini dello stato precedente dell'aggiornamento. Questi ID evento sono inclusi nella [documentazione di riferimento sugli eventi dell'applicazione](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventId == 29623 or EventId == 29624
    ```

2. Fare clic su "Nuova regola di avviso" nella parte superiore. Da questo momento, ogni volta che arriva un evento basato su questa query, si riceverà un avviso.

## <a name="how-do-i-see-container-metrics"></a>Come visualizzare le metriche dei contenitori

Nella stessa visualizzazione con tutti i grafici si noteranno alcuni riquadri per le prestazioni dei contenitori. Sono necessari l'agente di OMS e la [soluzione Monitoraggio contenitori](service-fabric-diagnostics-oms-containers.md) per popolare questi riquadri.

![Metriche dei contenitori di OMS](media/service-fabric-diagnostics-common-scenarios/containermetrics.png)

>[!NOTE]
>Per instrumentare i dati di telemetria dall'**interno** del contenitore, sarà necessario aggiungere il [pacchetto nuget di Application Insights per i contenitori](https://github.com/Microsoft/ApplicationInsights-servicefabric#microsoftapplicationinsightsservicefabric--for-service-fabric-lift-and-shift-scenarios).

## <a name="how-can-i-monitor-performance-counters"></a>Come monitorare i contatori delle prestazioni

1. Dopo aver aggiunto l'agente di OMS al cluster, è necessario aggiungere i contatori delle prestazioni specifici di cui si vuole tenere traccia. Passare alla pagina dell'area di lavoro di OMS nel portale. Nella pagina della soluzione la scheda dell'area di lavoro è nel menu a sinistra.

    ![Scheda Area di lavoro OMS](media/service-fabric-diagnostics-common-scenarios/workspacetab.png)

2. Nella pagina dell'area di lavoro fare clic su "Impostazioni avanzate" nello stesso menu a sinistra.

    ![Impostazioni avanzate di OMS](media/service-fabric-diagnostics-common-scenarios/advancedsettingsoms.png)

3. Fare clic su Dati > Contatori delle prestazioni di Windows (Dati > Contatori delle prestazioni di Linux per i computer Linux) per avviare la raccolta di contatori specifici dai nodi tramite l'agente di OMS. Ecco alcuni esempi del formato dei contatori da aggiungere

    * `.NET CLR Memory(<ProcessNameHere>)\\# Total committed Bytes`
    * `Processor(_Total)\\% Processor Time`
    * `Service Fabric Service(*)\\Average milliseconds per request`

    Nella guida introduttiva vengono usati i nomi di processo VotingData e VotingWeb, quindi il codice per tenere traccia di questi contatori sarà simile ai seguenti

    * `.NET CLR Memory(VotingData)\\# Total committed Bytes`
    * `.NET CLR Memory(VotingWeb)\\# Total committed Bytes`

    ![Contatori delle prestazioni di OMS](media/service-fabric-diagnostics-common-scenarios/omsperfcounters.png)

4. In questo modo sarà possibile visualizzare come l'infrastruttura gestisce i carichi di lavoro e impostare avvisi pertinenti in base all'utilizzo delle risorse. È ad esempio possibile impostare un avviso se l'utilizzo totale del processore è superiore al 90% o inferiore al 5%. Il nome del contatore usato in questo caso sarà "% Processor Time". A questo scopo, è possibile creare una regola di avviso per la query seguente:

    ```kusto
    Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" | where CounterValue >= 90 or CounterValue <= 5.
    ```

## <a name="how-do-i-track-performance-of-my-reliable-services-and-actors"></a>Come tenere traccia delle prestazioni di Reliable Services e Actors

Per tenere traccia delle prestazioni di Reliable Services o Actors nelle applicazioni, è consigliabile aggiungere anche i contatori Actor di Service Fabric, Metodo Actor, Service e Metodo Service. È possibile aggiungere questi contatori in modo simile a quello dello scenario precedente. Di seguito sono disponibili alcuni esempi di contatori delle prestazioni di Reliable Services e Actors da aggiungere in OMS

* `Service Fabric Service(*)\\Average milliseconds per request`
* `Service Fabric Service Method(*)\\Invocations/Sec`
* `Service Fabric Actor(*)\\Average milliseconds per request`
* `Service Fabric Actor Method(*)\\Invocations/Sec`

Fare clic su questi collegamenti per l'elenco completo dei contatori delle prestazioni per Reliable [Services](service-fabric-reliable-serviceremoting-diagnostics.md) e [Actors](service-fabric-reliable-actors-diagnostics.md)

## <a name="next-steps"></a>Passaggi successivi

* [Configurare gli avvisi in AI](../application-insights/app-insights-alerts.md) per ricevere una notifica sulle modifiche apportate alle prestazioni o all'uso
* [Rilevamento intelligente in Application Insights](../application-insights/app-insights-proactive-diagnostics.md) esegue un'analisi proattiva dei dati di telemetria che vengono inviati ad AI per avvisare l'utente in caso di potenziali problemi di prestazioni
* Altre informazioni sugli [avvisi](../log-analytics/log-analytics-alerts.md) di OMS Log Analytics per agevolare il rilevamento e la diagnostica.
* Per i cluster locali, OMS offre un Gateway, ovvero un proxy di inoltro HTTP, che può essere usato per inviare i dati a OMS. Per altre informazioni leggere [Connettere computer senza accesso a Internet a OMS usando il gateway OMS](../log-analytics/log-analytics-oms-gateway.md)
* Acquisire familiarità con le funzionalità di [ricerca log e query](../log-analytics/log-analytics-log-searches.md) incluse in Log Analytics
* Per avere una panoramica più dettagliata di Log Analytics e dei vantaggi offerti, leggere [Informazioni su Log Analytics](../operations-management-suite/operations-management-suite-overview.md)

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
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 2eb395b4f3d922aa116e01c5de080a54d81e10ff
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58118647"
---
# <a name="diagnose-common-scenarios-with-service-fabric"></a>Scenari comuni di diagnosi con Service Fabric

Questo articolo illustra gli scenari comuni affrontati dagli utenti nell'ambito del monitoraggio e della diagnostica con Service Fabric. Gli scenari presentati illustrano tutti i 3 livelli di Service Fabric: applicazione, cluster e infrastruttura. Ogni soluzione Usa Application Insights e log di monitoraggio di Azure, strumenti di monitoraggio di Azure per completare ogni scenario. I passaggi in ogni soluzione concedere agli utenti un'introduzione su come usare Application Insights e log di monitoraggio di Azure nel contesto di Service Fabric.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-recommendations"></a>Prerequisiti e indicazioni

Le soluzioni in questo articolo useranno gli strumenti seguenti. È consigliabile installarli e configurarli:

* [Application Insights con Service Fabric](service-fabric-tutorial-monitoring-aspnet.md)
* [Abilitare Diagnostica di Azure nel cluster](service-fabric-diagnostics-event-aggregation-wad.md)
* [Configurare un'area di lavoro di Log Analitica](service-fabric-diagnostics-oms-setup.md)
* [Agente di Log Analytics per tenere traccia dei contatori delle prestazioni](service-fabric-diagnostics-oms-agent.md)

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

    Per altre informazioni sulla mappa delle app, vedere la [documentazione sulla mappa delle applicazioni](../azure-monitor/app/app-map.md)

## <a name="how-do-i-create-an-alert-when-a-node-goes-down"></a>Come creare un avviso quando un nodo diventa inattivo

1. Il cluster di Service Fabric tiene traccia degli eventi dei nodi. Passare alla risorsa della soluzione Analisi Service Fabric denominata **ServiceFabric(NameofResourceGroup)**
2. Fare clic sul grafico nella parte inferiore del pannello intitolato "Riepilogo"

    ![Soluzioni di log di monitoraggio di Azure](media/service-fabric-diagnostics-common-scenarios/oms-solution-azure-portal.png)

3. Sono disponibili molti grafici e riquadri con diverse metriche. Fare clic su uno dei grafici per passare alla ricerca log, dove è possibile cercare gli eventi del cluster o i contatori delle prestazioni.
4. Immettere la query seguente. Questi ID evento sono inclusi nella [documentazione di riferimento sugli eventi dei nodi](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID >= 25622 and EventID <= 25626
    ```

5. Fare clic su "Nuova regola di avviso" nella parte superiore. Da questo momento, ogni volta che arriva un evento basato su questa query, si riceverà un avviso con il metodo di comunicazione scelto.

    ![Monitoraggio di Azure Registra nuovo avviso](media/service-fabric-diagnostics-common-scenarios/oms-create-alert.png)

## <a name="how-can-i-be-alerted-of-application-upgrade-rollbacks"></a>Come essere avvisati dei ripristini dello stato precedente dell'aggiornamento dell'applicazione?

1. Nella stessa finestra di ricerca log di prima immettere la query seguente per i ripristini dello stato precedente dell'aggiornamento. Questi ID evento sono inclusi nella [documentazione di riferimento sugli eventi dell'applicazione](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID == 29623 or EventID == 29624
    ```

2. Fare clic su "Nuova regola di avviso" nella parte superiore. Da questo momento, ogni volta che arriva un evento basato su questa query, si riceverà un avviso.

## <a name="how-do-i-see-container-metrics"></a>Come visualizzare le metriche dei contenitori

Nella stessa visualizzazione con tutti i grafici si noteranno alcuni riquadri per le prestazioni dei contenitori. Sono necessari l'agente di Log Analytics e la [soluzione Monitoraggio contenitori](service-fabric-diagnostics-oms-containers.md) per popolare questi riquadri.

![Metriche del contenitore di Log Analytics](media/service-fabric-diagnostics-common-scenarios/containermetrics.png)

>[!NOTE]
>Per instrumentare i dati di telemetria dall'**interno** del contenitore, sarà necessario aggiungere il [pacchetto nuget di Application Insights per i contenitori](https://github.com/Microsoft/ApplicationInsights-servicefabric#microsoftapplicationinsightsservicefabric--for-service-fabric-lift-and-shift-scenarios).

## <a name="how-can-i-monitor-performance-counters"></a>Come monitorare i contatori delle prestazioni

1. Dopo aver aggiunto l'agente di Log Analytics al cluster, è necessario aggiungere i contatori delle prestazioni specifici di cui si vuole tenere traccia. Passare alla pagina dell'area di lavoro di Log Analytics nel portale. Nella pagina della soluzione la scheda dell'area di lavoro è nel menu a sinistra.

    ![Scheda dell'area di lavoro di Log Analytics](media/service-fabric-diagnostics-common-scenarios/workspacetab.png)

2. Nella pagina dell'area di lavoro fare clic su "Impostazioni avanzate" nello stesso menu a sinistra.

    ![Impostazioni avanzate di Log Analytics](media/service-fabric-diagnostics-common-scenarios/advancedsettingsoms.png)

3. Fare clic su Dati > Contatori delle prestazioni di Windows (Dati > Contatori delle prestazioni di Linux per i computer Linux) per avviare la raccolta di contatori specifici dai nodi tramite l'agente di Log Analytics. Ecco alcuni esempi del formato dei contatori da aggiungere

   * `.NET CLR Memory(<ProcessNameHere>)\\# Total committed Bytes`
   * `Processor(_Total)\\% Processor Time`

     Nella guida introduttiva vengono usati i nomi di processo VotingData e VotingWeb, quindi il codice per tenere traccia di questi contatori sarà simile ai seguenti

   * `.NET CLR Memory(VotingData)\\# Total committed Bytes`
   * `.NET CLR Memory(VotingWeb)\\# Total committed Bytes`

     ![Contatori delle prestazioni Log Analytics](media/service-fabric-diagnostics-common-scenarios/omsperfcounters.png)

4. In questo modo sarà possibile visualizzare come l'infrastruttura gestisce i carichi di lavoro e impostare avvisi pertinenti in base all'utilizzo delle risorse. È ad esempio possibile impostare un avviso se l'utilizzo totale del processore è superiore al 90% o inferiore al 5%. Il nome del contatore usato in questo caso sarà "% Processor Time". A questo scopo, è possibile creare una regola di avviso per la query seguente:

    ```kusto
    Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" | where CounterValue >= 90 or CounterValue <= 5.
    ```

## <a name="how-do-i-track-performance-of-my-reliable-services-and-actors"></a>Come tenere traccia delle prestazioni di Reliable Services e Actors

Per monitorare le prestazioni di Actors o Reliable Services nelle applicazioni, è necessario raccogliere contatori Service Fabric Actors, un metodo attore, servizio e il metodo di servizio. Di seguito sono riportati esempi di reliable service e l'attore i contatori delle prestazioni da raccogliere

>[!NOTE]
>I contatori delle prestazioni di Service Fabric non possono essere raccolti dall'agente di Log Analitica attualmente, ma possono essere raccolti da [altre soluzioni di diagnostica](service-fabric-diagnostics-partners.md)

* `Service Fabric Service(*)\\Average milliseconds per request`
* `Service Fabric Service Method(*)\\Invocations/Sec`
* `Service Fabric Actor(*)\\Average milliseconds per request`
* `Service Fabric Actor Method(*)\\Invocations/Sec`

Fare clic su questi collegamenti per l'elenco completo dei contatori delle prestazioni per Reliable [Services](service-fabric-reliable-serviceremoting-diagnostics.md) e [Actors](service-fabric-reliable-actors-diagnostics.md)

## <a name="next-steps"></a>Passaggi successivi

* [Configurare gli avvisi in AI](../azure-monitor/app/alerts.md) per ricevere una notifica sulle modifiche apportate alle prestazioni o all'uso
* [Rilevamento intelligente in Application Insights](../azure-monitor/app/proactive-diagnostics.md) esegue un'analisi proattiva dei dati di telemetria che vengono inviati ad AI per avvisare l'utente in caso di potenziali problemi di prestazioni
* Altre informazioni sui log di monitoraggio di Azure [avvisi](../log-analytics/log-analytics-alerts.md) per semplificare il rilevamento e la diagnostica.
* Per i cluster in locale, i log di monitoraggio di Azure offre un gateway (Proxy di inoltro HTTP) che può essere utilizzato per inviare dati a log di monitoraggio di Azure. Per ulteriori informazioni in [connettere computer senza accesso a Internet per i log di monitoraggio di Azure usando il gateway Log Analitica](../azure-monitor/platform/gateway.md)
* Acquisire familiarità con le [ricerca log e l'esecuzione di query](../log-analytics/log-analytics-log-searches.md) funzionalità fornite come parte dei log di monitoraggio di Azure
* Ottenere una panoramica più dettagliata dei log di monitoraggio di Azure e sui vantaggi offerti, leggere [What ' s i log di monitoraggio di Azure?](../operations-management-suite/operations-management-suite-overview.md)

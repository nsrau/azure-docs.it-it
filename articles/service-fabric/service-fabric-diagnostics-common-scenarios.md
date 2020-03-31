---
title: Azure Service Fabric Diagnostica scenari comuni
description: Informazioni sulla risoluzione dei problemi relativi agli scenari di monitoraggio e diagnostica comuni nelle applicazioni di Azure Service Fabric.Learn about troubleshooting common monitoring and diagnostic scenarios within Azure Service Fabric applications.
ms.topic: article
ms.date: 02/25/2019
ms.openlocfilehash: 3c7f027bad71d48db5fba002f778f23db8225fa5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906942"
---
# <a name="diagnose-common-scenarios-with-service-fabric"></a>Scenari comuni di diagnosi con Service Fabric

Questo articolo illustra gli scenari comuni affrontati dagli utenti nell'ambito del monitoraggio e della diagnostica con Service Fabric. Gli scenari indicati coprono tutti e 3 i livelli di Service Fabric: infrastruttura, cluster e applicazione. Ogni soluzione usa Application Insights e i log di Monitoraggio di Azure, gli strumenti di monitoraggio di Azure, per completare ogni scenario. The steps in each solution give users an introduction on how to use Application Insights and Azure Monitor logs in the context of Service Fabric.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-recommendations"></a>Prerequisiti e indicazioni

Le soluzioni in questo articolo useranno gli strumenti seguenti. È consigliabile installarli e configurarli:

* [Application Insights con Service Fabric](service-fabric-tutorial-monitoring-aspnet.md)
* [Abilitare Diagnostica di Azure nel cluster](service-fabric-diagnostics-event-aggregation-wad.md)
* [Configurare un'area di lavoro Log Analytics](service-fabric-diagnostics-oms-setup.md)
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

    ![Mappa dell'app](media/service-fabric-diagnostics-common-scenarios/app-map-blade.png) ![AI Blade AI](media/service-fabric-diagnostics-common-scenarios/app-map-new.png)

    Per altre informazioni sulla mappa delle app, vedere la [documentazione sulla mappa delle applicazioni](../azure-monitor/app/app-map.md)

## <a name="how-do-i-create-an-alert-when-a-node-goes-down"></a>Come creare un avviso quando un nodo diventa inattivo

1. Il cluster di Service Fabric tiene traccia degli eventi dei nodi. Passare alla risorsa della soluzione Analisi Service Fabric denominata **ServiceFabric(NameofResourceGroup)**
2. Fare clic sul grafico nella parte inferiore del pannello intitolato "Riepilogo"

    ![Soluzione di log di Monitoraggio di AzureAzure Monitor logs solution](media/service-fabric-diagnostics-common-scenarios/oms-solution-azure-portal.png)

3. Sono disponibili molti grafici e riquadri con diverse metriche. Fare clic su uno dei grafici per passare alla ricerca log, dove è possibile cercare gli eventi del cluster o i contatori delle prestazioni.
4. Immettere la query seguente. Questi ID evento sono inclusi nella [documentazione di riferimento sugli eventi dei nodi](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID >= 25622 and EventID <= 25626
    ```

5. Fare clic su "Nuova regola di avviso" nella parte superiore. Da questo momento, ogni volta che arriva un evento basato su questa query, si riceverà un avviso con il metodo di comunicazione scelto.

    ![Nuovo avviso nei log di Monitoraggio di AzureAzure Monitor logs New Alert](media/service-fabric-diagnostics-common-scenarios/oms-create-alert.png)

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

1. Dopo aver aggiunto l'agente di Log Analytics al cluster, è necessario aggiungere i contatori delle prestazioni specifici di cui si vuole tenere traccia. Passare alla pagina dell'area di lavoro di Log Analytics nel portale: dalla pagina della soluzione la scheda dell'area di lavoro si trova nel menu a sinistra.

    ![Scheda dell'area di lavoro Log Analytics](media/service-fabric-diagnostics-common-scenarios/workspacetab.png)

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

Per tenere traccia delle prestazioni di Reliable Services o Degli attori nelle applicazioni, è necessario raccogliere anche i contatori Attore, Metodo attore, Metodo attore, Servizio e Metodo di servizio. Di seguito sono riportati esempi di contatori affidabili delle prestazioni di servizi e attori per

>[!NOTE]
>I contatori delle prestazioni di Service Fabric non possono attualmente essere raccolti dall'agente di Log Analytics, ma possono essere raccolti da [altre soluzioni diagnosticheService](service-fabric-diagnostics-partners.md) Fabric performance counters cannot be collected by the Log Analytics agent currently, but can be collected by other diagnostic solutions

* `Service Fabric Service(*)\\Average milliseconds per request`
* `Service Fabric Service Method(*)\\Invocations/Sec`
* `Service Fabric Actor(*)\\Average milliseconds per request`
* `Service Fabric Actor Method(*)\\Invocations/Sec`

Fare clic su questi collegamenti per l'elenco completo dei contatori delle prestazioni per Reliable [Services](service-fabric-reliable-serviceremoting-diagnostics.md) e [Actors](service-fabric-reliable-actors-diagnostics.md)

## <a name="next-steps"></a>Passaggi successivi

* [Cercare errori comuni di attivazione del pacchetto di codice](./service-fabric-diagnostics-code-package-errors.md)
* [Configurare gli avvisi in AI](../azure-monitor/app/alerts.md) per ricevere una notifica sulle modifiche apportate alle prestazioni o all'uso
* [Rilevamento intelligente in Application Insights](../azure-monitor/app/proactive-diagnostics.md) esegue un'analisi proattiva dei dati di telemetria che vengono inviati ad AI per avvisare l'utente in caso di potenziali problemi di prestazioni
* Altre informazioni sugli [avvisi](../log-analytics/log-analytics-alerts.md) dei log di Monitoraggio di Azure per facilitare il rilevamento e la diagnostica.
* Per i cluster locali, i log di Monitoraggio di Azure offrono un gateway (proxy di inoltro HTTP) che può essere usato per inviare dati ai log di Monitoraggio di Azure.For on-premises clusters, Azure Monitor logs offers a gateway (HTTP Forward Proxy) that can be used to send data to Azure Monitor logs. Per altre informazioni, vedere [Connessione di computer senza accesso a Internet ai log di Monitoraggio di Azure tramite il gateway di Log AnalyticsRead](../azure-monitor/platform/gateway.md) more about that in Connecting computers without Internet access to Azure Monitor logs using the Log Analytics gateway
* Acquisire familiarità con le funzionalità di [ricerca dei log e di query](../log-analytics/log-analytics-log-searches.md) offerte come parte dei log di Monitoraggio di Azure
* Ottenere una panoramica più dettagliata dei log di Monitoraggio di Azure e delle offerte, vedere [Che cos'è](../operations-management-suite/operations-management-suite-overview.md) la pagina Dei log di Monitoraggio di Azure?

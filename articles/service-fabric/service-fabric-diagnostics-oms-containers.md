---
title: Monitorare i contenitori con i log di Monitoraggio di AzureMonitor containers with Azure Monitor logs
description: Usare i log di Monitoraggio di Azure per il monitoraggio dei contenitori in esecuzione nei cluster di Azure Service Fabric.Use Azure Monitor logs for monitoring containers running on Azure Service Fabric clusters.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 8d4231de13da3f8b2960bd4852136f803a97a546
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614435"
---
# <a name="monitor-containers-with-azure-monitor-logs"></a>Monitorare i contenitori con i log di Monitoraggio di AzureMonitor containers with Azure Monitor logs
 
Questo articolo illustra i passaggi necessari per configurare la soluzione di monitoraggio del contenitore di log di Monitoraggio di Azure per visualizzare gli eventi del contenitore. Per configurare il cluster per raccogliere gli eventi del contenitore, vedere questa [esercitazione dettagliata](service-fabric-tutorial-monitoring-wincontainers.md). 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="set-up-the-container-monitoring-solution"></a>Configurazione della soluzione di monitoraggio dei contenitori

> [!NOTE]
> È necessario configurare i log di Monitoraggio di Azure per il cluster e distribuire l'agente Log Analytics nei nodi. In caso contrario, seguire i passaggi descritti in [Configurare i log](service-fabric-diagnostics-oms-setup.md) di Monitoraggio di Azure e Aggiungere prima [l'agente di Log Analytics a un cluster.](service-fabric-diagnostics-oms-agent.md)

1. Dopo aver configurato il cluster con i log di Monitoraggio di Azure e l'agente di Log Analytics, distribuire i contenitori. Attendere il completamento della distribuzione dei contenitori prima di passare al passaggio successivo.

2. In Azure Marketplace cercare *Soluzione Monitoraggio contenitori* e fare clic sulla risorsa **Soluzione Monitoraggio contenitori** visualizzata nella categoria Monitoraggio e gestione.

    ![Aggiunta della soluzione Contenitori](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Creare la soluzione all'interno della stessa area di lavoro precedentemente creata per il cluster. Questa modifica attiva automaticamente l'agente, che inizierà a raccogliere i dati di Docker sui contenitori. Dopo circa 15 minuti vengono visualizzati i log e le statistiche in ingresso, come illustrato nella figura seguente.

    ![Dashboard di base di Log Analytics](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

L'agente abilita la raccolta di diversi log specifici del contenitore su cui è possibile eseguire query nei log di Monitoraggio di Azure o usati per visualizzare gli indicatori di prestazioni. I tipi di log raccolti sono:

* ContainerInventory: informazioni su posizione, nome e immagini dei contenitori
* ContainerImageInventory: informazioni sulle immagini distribuite, inclusi ID o dimensioni
* ContainerLog: log degli errori specifici, log Docker (stdout e così via) e altre voci
* ContainerServiceLog: comandi del daemon Docker che sono stati eseguiti
* Perf: contatori delle prestazioni, inclusi cpu, memoria, traffico di rete, I/O del disco e metriche personalizzate dei contenitori dai computer host



## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulla [soluzione Contenitori di log di Monitoraggio di Azure.Learn](../azure-monitor/insights/containers.md)more about Azure Monitor logs Containers solution .
* Per altre info sull'orchestrazione dei contenitori in Service Fabric, vedere [Service Fabric e contenitori](service-fabric-containers-overview.md)
* Acquisire familiarità con le funzionalità di [ricerca dei log e di query](../log-analytics/log-analytics-log-searches.md) offerte come parte dei log di Monitoraggio di Azure
* Configurare i log di Monitoraggio di Azure per configurare le regole di avviso automatiche per facilitare il rilevamento e la diagnosticaConfigure Azure Monitor logs to set up [automated alerting](../log-analytics/log-analytics-alerts.md) rules to aid in detecting and diagnostics
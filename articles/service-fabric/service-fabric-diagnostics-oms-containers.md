---
title: Monitorare i contenitori in Azure Service Fabric con log di monitoraggio di Azure | Microsoft Docs
description: Usare i log di monitoraggio di Azure per monitorare i contenitori in esecuzione nei cluster Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: d03d68560502821b9c343be983d9f7b5a83ed977
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60321898"
---
# <a name="monitor-containers-with-azure-monitor-logs"></a>Monitorare i contenitori con log di monitoraggio di Azure
 
Questo articolo illustra i passaggi necessari per configurare la soluzione monitoraggio contenitori di monitoraggio di Azure i log per visualizzare gli eventi del contenitore. Per configurare il cluster per raccogliere gli eventi del contenitore, vedere questa [esercitazione dettagliata](service-fabric-tutorial-monitoring-wincontainers.md). 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="set-up-the-container-monitoring-solution"></a>Configurazione della soluzione di monitoraggio dei contenitori

> [!NOTE]
> È necessario disporre delle monitoraggio di Azure, i log impostato per il cluster, nonché distribuire nei nodi l'agente di Log Analitica. Se si non, seguono i passaggi descritti in [configurare i log di monitoraggio di Azure](service-fabric-diagnostics-oms-setup.md) e [aggiungere l'agente di Log Analitica per un cluster](service-fabric-diagnostics-oms-agent.md) prima.

1. Dopo aver configurato il cluster con i log di monitoraggio di Azure e l'agente di Log Analitica, distribuire i contenitori. Attendere il completamento della distribuzione dei contenitori prima di passare al passaggio successivo.

2. In Azure Marketplace cercare *Soluzione Monitoraggio contenitori* e fare clic sulla risorsa **Soluzione Monitoraggio contenitori** visualizzata nella categoria Monitoraggio e gestione.

    ![Aggiunta della soluzione Contenitori](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Creare la soluzione all'interno della stessa area di lavoro precedentemente creata per il cluster. Questa modifica attiva automaticamente l'agente, che inizierà a raccogliere i dati di Docker sui contenitori. Dopo circa 15 minuti vengono visualizzati i log e le statistiche in ingresso, come illustrato nella figura seguente.

    ![Dashboard di base di Log Analytics](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

L'agente consente la raccolta di diversi log specifici per il contenitore che può essere eseguita una query nei log di monitoraggio di Azure o utilizzato per visualizzare gli indicatori di prestazioni. I tipi di log raccolti sono:

* ContainerInventory: informazioni su posizione, nome e immagini dei contenitori
* ContainerImageInventory: informazioni sulle immagini distribuite, inclusi ID o dimensioni
* ContainerLog: log degli errori specifici, log Docker (stdout e così via) e altre voci
* ContainerServiceLog: comandi del daemon Docker che sono stati eseguiti
* Perf: contatori delle prestazioni, inclusi cpu, memoria, traffico di rete, I/O del disco e metriche personalizzate dei contenitori dai computer host



## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulle [monitoraggio di Azure registra soluzione contenitori](../azure-monitor/insights/containers.md).
* Per altre info sull'orchestrazione dei contenitori in Service Fabric, vedere [Service Fabric e contenitori](service-fabric-containers-overview.md)
* Acquisire familiarità con le [ricerca log e l'esecuzione di query](../log-analytics/log-analytics-log-searches.md) funzionalità fornite come parte dei log di monitoraggio di Azure
* Configurare i log di monitoraggio di Azure per configurare [avvisi automatizzati](../log-analytics/log-analytics-alerts.md) regole per semplificare il rilevamento e la diagnostica
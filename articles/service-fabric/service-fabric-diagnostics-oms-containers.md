---
title: Monitorare i contenitori in Azure Service Fabric con Log Analytics | Microsoft Docs
description: Usare Log Analytics per monitorare i contenitori in esecuzione all'interno di cluster di Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/1/2017
ms.author: dekapur
ms.openlocfilehash: 1de7e58eecc80e306920ab17884290dfddf8efa8
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2018
---
# <a name="monitor-containers-with-log-analytics"></a>Monitorare i contenitori con Log Analytics
 
In questo articolo vengono illustrati i passaggi necessari per configurare la soluzione di monitoraggio del contenitore di Log Analytics di OMS per visualizzare gli eventi del contenitore. Per configurare il cluster per raccogliere gli eventi del contenitore, vedere questa [esercitazione dettagliata](service-fabric-tutorial-monitoring-wincontainers.md).

## <a name="set-up-the-container-monitoring-solution"></a>Configurazione della soluzione di monitoraggio dei contenitori

> [!NOTE]
> È necessario configurare Log Analytics per i cluster e distribuire l'agente di OMS nei nodi. In caso contrario, eseguire prima i passaggi in [Configurare Log Analytics](service-fabric-diagnostics-oms-setup.md) e in [Aggiungere l'agente di OMS a un cluster](service-fabric-diagnostics-oms-agent.md).

1. Dopo aver configurato il cluster con Log Analytics e l'agente di OMS, distribuire i contenitori. Attendere il completamento della distribuzione dei contenitori prima di passare al passaggio successivo.

2. In Azure Marketplace cercare *Soluzione Monitoraggio contenitori* e fare clic sulla risorsa **Soluzione Monitoraggio contenitori** visualizzata nella categoria Monitoraggio e gestione.

    ![Aggiunta della soluzione Contenitori](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Creare la soluzione all'interno della stessa area di lavoro precedentemente creata per il cluster. Questa modifica attiva automaticamente l'agente, che inizierà a raccogliere i dati di Docker sui contenitori. Dopo circa 15 minuti vengono visualizzati i log e le statistiche in ingresso, come illustrato nella figura seguente.

    ![Dashboard OMS di base](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

L'agente consente la raccolta di diversi log specifici per il contenitore che possono essere sottoposti a query in OMS o usati per gli indicatori di prestazioni visualizzati. I tipi di log raccolti sono:

* ContainerInventory: informazioni su posizione, nome e immagini dei contenitori
* ContainerImageInventory: informazioni sulle immagini distribuite, inclusi ID o dimensioni
* ContainerLog: log degli errori specifici, log Docker (stdout e così via) e altre voci
* ContainerServiceLog: comandi del daemon Docker che sono stati eseguiti
* Perf: contatori delle prestazioni, inclusi cpu, memoria, traffico di rete, I/O del disco e metriche personalizzate dei contenitori dai computer host



## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulla [soluzione Monitoraggio contenitori di OMS](../log-analytics/log-analytics-containers.md).
* Per altre info sull'orchestrazione dei contenitori in Service Fabric, vedere [Service Fabric e contenitori](service-fabric-containers-overview.md)
* Acquisire familiarità con le funzionalità di [ricerca log e query](../log-analytics/log-analytics-log-searches.md) incluse in Log Analytics
* Configurare Log Analytics per configurare regole per gli [avvisi automatizzati](../log-analytics/log-analytics-alerts.md), semplificando il rilevamento e la diagnostica
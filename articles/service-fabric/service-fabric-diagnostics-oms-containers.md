---
title: Monitorare i contenitori in Azure Service Fabric con Log Analytics di OMS | Microsoft Docs
description: Usare Log Analytics di OMS per monitorare i contenitori eseguiti nei cluster di Azure Service Fabric.
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
ms.date: 11/1/2017
ms.author: dekapur
ms.openlocfilehash: 17121041520160d0d76832bbdbe74ad6a649fdd8
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2017
---
# <a name="monitor-containers-with-oms-log-analytics"></a>Monitorare i contenitori con Log Analytics di OMS
 
Questo articolo illustra i passaggi necessari per configurare il monitoraggio dei contenitori per il cluster. Per altre informazioni, vedere la sezione relativa al [monitoraggio dei contenitori in Service Fabric](service-fabric-diagnostics-event-analysis-oms.md#monitoring-containers). Per visualizzare un'esercitazione dettagliata su questo argomento, è anche possibile vedere [Monitorare i contenitori di Windows in Service Fabric usando OMS](service-fabric-tutorial-monitoring-wincontainers.md).

## <a name="set-up-the-container-monitoring-solution"></a>Configurazione della soluzione di monitoraggio dei contenitori

> [!NOTE]
> È necessario configurare Log Analytics di OMS per i cluster e distribuire l'agente di OMS sui nodi. In caso contrario, eseguire prima i passaggi relativi alla [configurazione di Log Analytics di OMS](service-fabric-diagnostics-oms-setup.md) e all'[aggiunta dell'agente di OMS a un cluster](service-fabric-diagnostics-oms-agent.md).

1. Dopo aver configurato il cluster con Log Analytics di OMS e l'agente di OMS, distribuire i contenitori. Attendere il completamento della distribuzione dei contenitori prima di passare al passaggio successivo.

2. In Azure Marketplace cercare *Soluzione Monitoraggio contenitori* e fare clic sulla risorsa **Soluzione Monitoraggio contenitori** visualizzata nella categoria Monitoraggio e gestione.

    ![Aggiunta della soluzione Contenitori](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Creare la soluzione all'interno della stessa area di lavoro precedentemente creata per il cluster. Questa modifica attiva automaticamente l'agente, che inizierà a raccogliere i dati di Docker sui contenitori. Dopo circa 15 minuti vengono visualizzati i log e le statistiche in ingresso.

## <a name="next-steps"></a>Passaggi successivi
* Per altre info sull'orchestrazione dei contenitori in Service Fabric, vedere [Service Fabric e contenitori](service-fabric-containers-overview.md)
* Acquisire familiarità con le funzionalità di [ricerca log e query](../log-analytics/log-analytics-log-searches.md) incluse in Log Analytics
* Configurare OMS per regole di [avvisi automatizzati](../log-analytics/log-analytics-alerts.md) per semplificare il rilevamento e la diagnostica
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
ms.openlocfilehash: 7a775b6d23c144c81650bb3608ee6a117475a9ba
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="monitor-containers-with-log-analytics"></a>Monitorare i contenitori con Log Analytics
 
Questo articolo illustra i passaggi necessari per configurare il monitoraggio dei contenitori per il cluster. Per altre informazioni, vedere la sezione relativa al [monitoraggio dei contenitori in Service Fabric](service-fabric-diagnostics-event-analysis-oms.md#monitoring-containers). Per visualizzare un'esercitazione dettagliata su questo argomento, è anche possibile vedere [Monitorare i contenitori di Windows in Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).

## <a name="set-up-the-container-monitoring-solution"></a>Configurazione della soluzione di monitoraggio dei contenitori

> [!NOTE]
> È necessario configurare Log Analytics per i cluster e distribuire l'agente di OMS nei nodi. In caso contrario, eseguire prima i passaggi in [Configurare Log Analytics](service-fabric-diagnostics-oms-setup.md) e in [Aggiungere l'agente di OMS a un cluster](service-fabric-diagnostics-oms-agent.md).

1. Dopo aver configurato il cluster con Log Analytics e l'agente di OMS, distribuire i contenitori. Attendere il completamento della distribuzione dei contenitori prima di passare al passaggio successivo.

2. In Azure Marketplace cercare *Soluzione Monitoraggio contenitori* e fare clic sulla risorsa **Soluzione Monitoraggio contenitori** visualizzata nella categoria Monitoraggio e gestione.

    ![Aggiunta della soluzione Contenitori](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Creare la soluzione all'interno della stessa area di lavoro precedentemente creata per il cluster. Questa modifica attiva automaticamente l'agente, che inizierà a raccogliere i dati di Docker sui contenitori. Dopo circa 15 minuti vengono visualizzati i log e le statistiche in ingresso.

## <a name="next-steps"></a>Passaggi successivi
* Per altre info sull'orchestrazione dei contenitori in Service Fabric, vedere [Service Fabric e contenitori](service-fabric-containers-overview.md)
* Acquisire familiarità con le funzionalità di [ricerca log e query](../log-analytics/log-analytics-log-searches.md) incluse in Log Analytics
* Configurare Log Analytics per configurare regole per gli [avvisi automatizzati](../log-analytics/log-analytics-alerts.md), semplificando il rilevamento e la diagnostica
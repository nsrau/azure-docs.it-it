---
title: Monitoraggio delle posizioni dei dati in monitoraggio di Azure | Microsoft Docs
description: Descrive le diverse posizioni in cui i dati di monitoraggio vengono archiviati in Azure, inclusa la piattaforma dati di monitoraggio di Azure.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/21/2019
ms.author: bwren
ms.openlocfilehash: 6f6071bc1d3d7514d22658a07810690ec8a8056b
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71972750"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>Monitoraggio delle posizioni dei dati in monitoraggio di Azure

Monitoraggio di Azure è basato su una [piattaforma di dati](data-platform.md) di [log](data-platform-logs.md) e [metriche](data-platform-metrics.md) , come descritto in [Azure Monitor Data Platform](data-platform.md). I dati di monitoraggio delle risorse di Azure possono essere scritti in altre posizioni, prima che vengano copiati in monitoraggio di Azure o per supportare altri scenari. 

## <a name="monitoring-data-locations"></a>Percorsi dei dati di monitoraggio

La tabella seguente identifica le diverse posizioni in cui vengono inviati i dati di monitoraggio in Azure e i diversi metodi di accesso.

| Location | Descrizione | Metodi di accesso |
|:---|:---|:---|:--|
| Metriche di monitoraggio di Azure | Database di serie temporali ottimizzato per l'analisi di dati timestamp. | [Esplora metriche](metrics-getting-started.md)<br>[API metriche di monitoraggio di Azure](/rest/api/monitor/metrics) |
| Log di Monitoraggio di Azure    | Log Analytics area di lavoro basata su Azure Esplora dati, che fornisce un potente motore di analisi e un linguaggio di query avanzato. | [Log Analytics](../log-query/portals.md)<br>[API Log Analytics](https://dev.loganalytics.io/)<br>[API Application Insights](https://dev.applicationinsights.io/reference/get-query) |
| Log attività | I dati del log attività sono particolarmente utili quando vengono inviati ai log di monitoraggio di Azure per analizzarli con altri dati, ma vengono raccolti autonomamente, in modo da poterli visualizzare direttamente nel portale di Azure. | [Portale di Azure](activity-log-view.md#azure-portal)<br>[API per gli eventi di Monitoraggio di Azure](/rest/api/monitor/eventcategories) |
| Archiviazione di Azure | Alcune origini dati scriveranno direttamente in archiviazione di Azure e richiedono la configurazione per spostare i dati nei log. È anche possibile inviare dati ad archiviazione di Azure per l'archiviazione e l'integrazione con sistemi esterni.  | [Analisi archiviazione](/rest/api/storageservices/storage-analytics)<br>[Esplora server](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| Hub eventi | Inviare dati a hub eventi di Azure per lo streaming in altre posizioni. | [Acquisisci nella risorsa di archiviazione](../../event-hubs/event-hubs-capture-overview.md)  |
| Monitoraggio di Azure per le macchine virtuali | Monitoraggio di Azure per le macchine virtuali archivia i dati di integrità del carico di lavoro in un percorso personalizzato usato dall'esperienza di monitoraggio nel portale di Azure. | [Portale di Azure](../insights/vminsights-overview.md)<br>[API REST di monitoraggio del carico di lavoro](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[API REST di integrità delle risorse di Azure](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| Avvisi | Avvisi creati da monitoraggio di Azure. | [Portale di Azure](alerts-managing-alert-instances.md)<br>[API REST di gestione degli avvisi](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>Passaggi successivi

- Vedere le diverse origini dei [dati di monitoraggio raccolti da monitoraggio di Azure](data-sources.md).
- Informazioni sui [tipi di dati di monitoraggio raccolti da monitoraggio di Azure](data-platform.md) e su come visualizzare e analizzare questi dati.

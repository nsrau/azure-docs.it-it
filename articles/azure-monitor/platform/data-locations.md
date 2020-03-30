---
title: Monitoraggio dei percorsi dei dati in Monitoraggio di Azure Documenti Microsoft
description: Descrive le diverse posizioni in cui vengono archiviati i dati di monitoraggio in Azure, inclusa la piattaforma dati di Monitoraggio di Azure.Describes the different locations where monitoring data is stored in Azure including the Azure Monitor data platform.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/21/2019
ms.openlocfilehash: 7d4459867081d920fefb9471b1a682d21040da9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666616"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>Monitoraggio degli percorsi dati in Monitoraggio di AzureMonitoring data locations in Azure Monitor

Monitoraggio di Azure si basa su una [piattaforma dati](data-platform.md) di log e [metriche,](data-platform-metrics.md) come descritto nella piattaforma dati di Monitoraggio di Azure.Azure Monitor is based on a data platform of [Logs](data-platform-logs.md) and Metrics as described in Azure Monitor [data platform](data-platform.md). I dati di monitoraggio dalle risorse di Azure possono essere scritti in altre posizioni, prima di essere copiati in Monitoraggio di Azure o per supportare scenari aggiuntivi. 

## <a name="monitoring-data-locations"></a>Monitoraggio delle posizioni dei dati

La tabella seguente identifica le diverse posizioni in cui vengono inviati i dati di monitoraggio in Azure e i diversi metodi per accedervi.

| Location | Descrizione | Metodi di accesso |
|:---|:---|:---|:--|
| Metriche di Monitoraggio di AzureAzure Monitor Metrics | Database di serie temporali ottimizzato per l'analisi dei dati con timestamp. | [Esplora metriche](metrics-getting-started.md)<br>[API Metriche di Monitoraggio di AzureAzure Monitor Metrics API](/rest/api/monitor/metrics) |
| Log di Monitoraggio di Azure    | Area di lavoro di Log Analytics basata su Azure Data Explorer che offre un potente motore di analisi e un linguaggio di query avanzato. | [Analisi dei log](../log-query/portals.md)<br>[Log Analytics API](https://dev.loganalytics.io/)<br>[API Application Insights](https://dev.applicationinsights.io/reference/get-query) |
| Log attività | I dati del log attività sono particolarmente utili quando vengono inviati ai log di Monitoraggio di Azure per analizzarli con altri dati, ma vengono anche raccolti da soli in modo che possano essere visualizzati direttamente nel portale di Azure.Data from the Activity log is most useful when sent to Azure Monitor Logs to analyze it with other data, but it's also collected on its own so it can be directly viewed in the Azure portal. | [Portale di Azure](activity-log-view.md#azure-portal)<br>[API per gli eventi di Monitoraggio di Azure](/rest/api/monitor/eventcategories) |
| Archiviazione di Azure | Alcune origini dati scriveranno direttamente nell'archiviazione di Azure e richiedono la configurazione per spostare i dati in Logs.Some data sources will write directly to Azure storage and require configuration to move data into Logs. È anche possibile inviare dati all'archiviazione di Azure per l'archiviazione e l'integrazione con sistemi esterni.  | [di Analisi archiviazione](/rest/api/storageservices/storage-analytics)<br>[Esplora server](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| Hub eventi | Inviare dati agli hub eventi di Azure per trasmetterle in streaming ad altre posizioni. | [Acquisizione in memoria](../../event-hubs/event-hubs-capture-overview.md)  |
| Monitoraggio di Azure per le macchine virtuali | Monitoraggio di Azure per le macchine virtuali archivia i dati di integrità del carico di lavoro in una posizione personalizzata usata dall'esperienza di monitoraggio nel portale di Azure.Azure Monitor for VMs stores workload health data in a custom location that's used by its monitoring experience in the Azure portal. | [Portale di Azure](../insights/vminsights-overview.md)<br>[API REST di monitoraggio del carico di lavoroWorkload monitor REST API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[API REST per l'integrità delle risorse di AzureAzure Resource health REST API](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| Avvisi | Avvisi creati da Monitoraggio di Azure.Alerts created by Azure Monitor. | [Portale di Azure](alerts-managing-alert-instances.md)<br>[API REST di gestione degli avvisi](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>Passaggi successivi

- Vedere le diverse origini dei dati di [monitoraggio raccolti da Monitoraggio di Azure.See](data-sources.md)the different sources of monitoring data collected by Azure Monitor .
- Informazioni sui [tipi di dati di monitoraggio raccolti da Monitoraggio di Azure](data-platform.md) e su come visualizzarli e analizzarli.

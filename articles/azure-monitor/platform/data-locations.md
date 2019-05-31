---
title: Monitoraggio delle posizioni dei dati in Monitoraggio di Azure | Microsoft Docs
description: Descrive i diversi percorsi in cui i dati di monitoraggio vengono archiviati in Azure, inclusa la piattaforma di dati di monitoraggio di Azure.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/21/2019
ms.author: bwren
ms.openlocfilehash: 1d92973e32e9c694b1d0488753b9a701e7d71a5d
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416905"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>Percorsi dei dati di monitoraggio in Monitoraggio di Azure

Monitoraggio di Azure si basa su un [piattaforma dati](data-platform.md) dei [registri](data-platform-logs.md) e [metriche](data-platform-metrics.md) come descritto in [piattaforma dati di monitoraggio di Azure](data-platform.md). Monitoraggio dei dati dalle risorse di Azure può essere scritti in altri percorsi, uno prima di essere copiate per monitoraggio di Azure o per supportare scenari aggiuntivi. 

## <a name="monitoring-data-locations"></a>Percorsi dei dati di monitoraggio

Nella tabella seguente identifica i diversi percorsi in cui vengono inviati dati di monitoraggio di Azure e i diversi metodi per accedere ad esso.

| Località | Descrizione | Metodi di accesso |
|:---|:---|:---|:--|
| Metriche di monitoraggio di Azure | Database di serie temporali che è ottimizzato per l'analisi dei dati con data / ora. | [Metrics Explorer](metrics-getting-started.md)<br>[API delle metriche di monitoraggio di Azure](/rest/api/monitor/metrics) |
| Log di monitoraggio di Azure    | Area di lavoro di Analitica basato su Esplora dati di Azure che fornisce un linguaggio di query motore e rich potenti analisi di log. | [Log Analytics](../log-query/portals.md)<br>[API di log Analitica](https://dev.loganalytics.io/)<br>[API di Application Insights](https://dev.applicationinsights.io/reference/get-query) |
| Log attività | I dati dal log attività sono particolarmente utili quando viene inviato al log di monitoraggio di Azure per l'analisi con altri dati, ma verrà raccolti anche autonomamente in modo che possono essere visualizzata direttamente nel portale di Azure. | [Portale di Azure](activity-log-view.md#azure-portal)<br>[API per gli eventi di Monitoraggio di Azure](/rest/api/monitor/eventcategories) |
| Archiviazione di Azure | Alcune origini dati scritta direttamente nell'archiviazione di Azure e richiedono una configurazione per spostare i dati nei log. È anche possibile inviare i dati nell'archiviazione di Azure per l'archiviazione e per l'integrazione con sistemi esterni.  | [Analisi archiviazione](/rest/api/storageservices/storage-analytics)<br>[Esplora server](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| Hub eventi | Inviare dati a hub eventi di Azure per eseguirne il flusso in altre posizioni. | [Acquisizione in archiviazione](../../event-hubs/event-hubs-capture-overview.md)  |
| Monitoraggio di Azure per le macchine virtuali | Monitoraggio per le macchine virtuali di Azure archivia i dati di integrità del carico di lavoro in un percorso personalizzato che viene usato per l'esperienza di monitoraggio nel portale di Azure. | [Portale di Azure](../insights/vminsights-overview.md)<br>[API REST di monitoraggio del carico di lavoro](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[Integrità risorse di Azure l'API REST](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| Avvisi | Avvisi creati da monitoraggio di Azure. | [Portale di Azure](alerts-managing-alert-instances.md)<br>[API REST di gestione avvisi](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>Passaggi successivi

- Vedere le diverse fonti [i dati di monitoraggio raccolti da monitoraggio di Azure](data-sources.md).
- Scopri le [tipi di dati di monitoraggio raccolti da monitoraggio di Azure](data-platform.md) e come visualizzare e analizzare i dati.

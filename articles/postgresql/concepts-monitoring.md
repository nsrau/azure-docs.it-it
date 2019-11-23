---
title: Monitor and Tune in Azure Database for PostgreSQL - Single Server
description: This article describes monitoring and tuning features in Azure Database for PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 799b2b6ee6074472a951e69dec7e9a87056373b4
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384032"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---single-server"></a>Monitorare e ottimizzare Database di Azure per PostgreSQL - Server singolo
Il monitoraggio dei dati relativi ai server facilita la risoluzione dei problemi e l'ottimizzazione in relazione al carico di lavoro. Database di Azure per PostgreSQL offre varie opzioni di monitoraggio che consentono di ottenere informazioni dettagliate sul comportamento del server.

## <a name="metrics"></a>Metriche
Database di Azure per PostgreSQL offre varie metriche che consentono di ottenere informazioni approfondite sul comportamento delle risorse che supportano il server PostgreSQL. Ogni metrica viene emessa con una frequenza di un minuto e ha una cronologia che può arrivare fino a 30 giorni. È possibile configurare avvisi in base alle metriche. Per indicazioni dettagliate, vedere l'articolo su come [configurare gli avvisi](howto-alert-on-metric.md). Le altre attività includono la configurazione di azioni automatiche, l'esecuzione di analisi avanzate e l'archiviazione della cronologia. Per altre informazioni, vedere [Panoramica delle metriche in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Elenco delle metriche
Per Database di Azure per PostgreSQL sono disponibili le metriche seguenti:

|Metrica|Nome visualizzato per la metrica|Unità|Description|
|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Percentuale di CPU in uso.|
|memory_percent|Percentuale memoria|Percentuale|Percentuale di memoria in uso.|
|io_consumption_percent|IO percent (Percentuale IO)|Percentuale|Percentuale di I/O in uso.|
|storage_percent|Percentuale archiviazione|Percentuale|Percentuale di spazio di archiviazione usata rispetto al massimo del server.|
|storage_used|Risorse di archiviazione usate|Byte|Quantità di spazio di archiviazione in uso. Lo spazio di archiviazione usato dal servizio può includere file di database, log delle transazioni e log del server.|
|storage_limit|Limite archiviazione|Byte|Spazio di archiviazione massimo per il server.|
|serverlog_storage_percent|Percentuale di archiviazione dei log del server|Percentuale|Percentuale di spazio di archiviazione dei log del server usata rispetto allo spazio di archiviazione massimo dei log del server per il server.|
|serverlog_storage_usage|Archiviazione dei log del server usata|Byte|Quantità di spazio di archiviazione dei log del server in uso.|
|serverlog_storage_limit|Server Log storage limit (Limite archiviazione log server)|Byte|Spazio di archiviazione massimo dei log del server per il server.|
|active_connections|Connessioni attive|Conteggio|Numero di connessioni al server attive.|
|connections_failed|Connessioni non riuscite|Conteggio|Numero di connessioni al server non riuscite.|
|network_bytes_egress|Rete in uscita|Byte|Rete in uscita tra connessioni attive.|
|network_bytes_ingress|Rete in ingresso|Byte|Rete in ingresso tra connessioni attive.|
|backup_storage_used|Risorse di backup in uso|Byte|Quantità di risorse di backup usate.|
|pg_replica_log_delay_in_bytes|Max Lag Across Replicas (Ritardo massimo tra repliche)|Byte|The lag in bytes between the master and the most-lagging replica. Questa metrica è disponibile solo nel server master.|
|pg_replica_log_delay_in_seconds|Replica Lag (Ritardo replica)|Secondi|The time since the last replayed transaction. This metric is available for replica servers only.|

## <a name="server-logs"></a>Log del server
È possibile abilitare l'accesso al server. These logs are also available through Azure Diagnostic Logs in [Azure Monitor logs](../azure-monitor/log-query/log-query-overview.md), Event Hubs, and Storage Account. Per altre informazioni sull'accesso, visitare la pagina dei [log del server](concepts-server-logs.md).

## <a name="query-store"></a>Archivio query
[Query Store](concepts-query-store.md) keeps track of query performance over time including query runtime statistics and wait events. La funzionalità salva in modo permanente le informazioni sulle prestazioni dei runtime di query in un database di sistema denominato **azure_sys** nello schema query_store. È possibile controllare la raccolta e l'archiviazione dei dati tramite vari controlli di configurazione.

## <a name="query-performance-insight"></a>Analisi delle prestazioni di query
[Informazioni dettagliate prestazioni query](concepts-query-performance-insight.md) funziona in combinazione con Query Store per fornire visualizzazioni accessibili dal portale di Azure. Questi grafici consentono di identificare le principali query che influiscono sulle prestazioni. Query Performance Insightis accessible from the **Support + troubleshooting** section of your Azure Database for PostgreSQL server's portal page.

## <a name="performance-recommendations"></a>Raccomandazioni per le prestazioni
La funzionalità [Raccomandazioni per le prestazioni](concepts-performance-recommendations.md) identifica le opportunità per migliorare le prestazioni dei carichi di lavoro. Performance Recommendations provides you with recommendations for creating new indexes that have the potential to improve the performance of your workloads. Per generare le raccomandazioni sugli indici, la funzionalità prende in considerazione diverse caratteristiche del database, tra cui lo schema e il carico di lavoro segnalati da Query Store. Dopo avere implementato le raccomandazioni per le prestazioni, i clienti devono testare le prestazioni per valutare l'impatto di tali modifiche. 

## <a name="service-health"></a>Integrità dei servizi
[Azure Service health](../service-health/overview.md) provides a view of all service health notifications in your subscription. You can set up Service Health alerts to notify you via your preferred communication channels when there are issues or changes that may affect the Azure services and regions you use.

You can view scheduled maintenance events for Azure Database for PostgreSQL - Single Server by using the **planned maintenance** event type. To learn how to create **service health alerts**, visit the [Create activity log alerts on service notifications](../service-health/alerts-activity-log-service-notifications.md) article.

> [!IMPORTANT]
> The planned maintenance notifications is available in preview for EAST US and UK South only.

## <a name="next-steps"></a>Passaggi successivi
- Per informazioni sulla creazione di un avviso per una metrica, vedere [Come configurare gli avvisi](howto-alert-on-metric.md).
- Per altre informazioni sull'accesso alle metriche e la relativa esportazione con il portale di Azure, l'API REST o l'interfaccia della riga di comando, vedere [Panoramica delle metriche in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- Leggere il blog Microsoft sulle [procedure consigliate per il monitoraggio del server](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/).

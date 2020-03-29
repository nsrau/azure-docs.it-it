---
title: Monitorare e ottimizzare - Hyperscale (Citus) - Database di Azure per PostgreSQLMonitor and tune - Hyperscale (Citus) - Azure Database for PostgreSQL
description: Questo articolo descrive le funzionalità di monitoraggio e ottimizzazione nel database di Azure per PostgreSQL - Hyperscale (Citus)This article describes monitoring and tuning features in Azure Database for PostgreSQL - Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d2e9fcd6f6292c1da76e725e90deda4547b3682d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975517"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---hyperscale-citus"></a>Monitorare e ottimizzare il database di Azure per PostgreSQL - Hyperscale (Citus)Monitor and tune Azure Database for PostgreSQL - Hyperscale (Citus)

Il monitoraggio dei dati relativi ai server facilita la risoluzione dei problemi e l'ottimizzazione in relazione al carico di lavoro. Hyperscale (Citus) fornisce varie opzioni di monitoraggio per fornire informazioni dettagliate sul comportamento dei nodi in un gruppo di server.

## <a name="metrics"></a>Metriche

Hyperscale (Citus) fornisce metriche per ogni nodo in un gruppo di server. Le metriche forniscono informazioni dettagliate sul comportamento delle risorse di supporto. Ogni metrica viene emessa con una frequenza di un minuto e ha una cronologia che può arrivare fino a 30 giorni.

Oltre a visualizzare i grafici delle metriche, è possibile configurare gli avvisi. Per indicazioni dettagliate, vedere l'articolo su come [configurare gli avvisi](howto-hyperscale-alert-on-metric.md).  Altre attività includono l'impostazione di azioni automatizzate, l'esecuzione di analisi avanzate e la cronologia di archiviazione. Per altre informazioni, vedere [Panoramica delle metriche in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Elenco delle metriche

Queste metriche sono disponibili per i nodi Hyperscale (Citus):

|Metrica|Nome visualizzato per la metrica|Unità|Descrizione|
|---|---|---|---|
|active_connections|Connessioni attive|Conteggio|Numero di connessioni al server attive.|
|cpu_percent|Percentuale CPU|Percentuale|Percentuale di CPU in uso.|
|Iops|IOPS|Conteggio|Vedere la definizione di IOPS e la [velocità effettiva di HyperscaleSee](concepts-hyperscale-configuration-options.md) the [IOPS definition](../virtual-machines/linux/premium-storage-performance.md#iops) and Hyperscale throughput|
|memory_percent|Percentuale memoria|Percentuale|Percentuale di memoria in uso.|
|network_bytes_ingress|Rete in ingresso|Byte|Rete in ingresso tra connessioni attive.|
|network_bytes_egress|Rete in uscita|Byte|Rete in uscita tra connessioni attive.|
|storage_percent|Percentuale archiviazione|Percentuale|Percentuale di spazio di archiviazione usata rispetto al massimo del server.|
|storage_used|Uso archiviazione|Byte|Quantità di spazio di archiviazione in uso. Lo spazio di archiviazione usato dal servizio può includere file di database, log delle transazioni e log del server.|

Azure non fornisce metriche aggregate per l'intero cluster, ma le metriche per più nodi possono essere posizionate nello stesso grafico.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su come configurare avvisi](howto-hyperscale-alert-on-metric.md) per istruzioni sulla creazione di un avviso in una metrica.

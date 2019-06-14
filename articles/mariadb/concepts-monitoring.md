---
title: Monitoraggio in Database di Azure per MariaDB
description: Questo articolo illustra le metriche di monitoraggio e avviso per Database di Azure per MariaDB, che includono statistiche relative a CPU, spazio di archiviazione e connessioni.
author: andrela
ms.author: ajlam
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: 8625441f836256028362fc327873383f5b46620c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065735"
---
# <a name="monitoring-in-azure-database-for-mariadb"></a>Monitoraggio in Database di Azure per MariaDB
Il monitoraggio dei dati relativi ai server facilita la risoluzione dei problemi e l'ottimizzazione in relazione al carico di lavoro. Database di Azure per MariaDB offre varie metriche che consentono di ottenere informazioni dettagliate sul comportamento del server.

## <a name="metrics"></a>metrics
Tutte le metriche di Azure hanno una frequenza di un minuto e offrono una cronologia di 30 giorni. È possibile configurare avvisi in base alle metriche. Le altre attività includono la configurazione di azioni automatiche, l'esecuzione di analisi avanzate e l'archiviazione della cronologia. Per altre informazioni, vedere [Panoramica delle metriche in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Per indicazioni dettagliate, vedere l'articolo su come [configurare gli avvisi](howto-alert-metric.md).

### <a name="list-of-metrics"></a>Elenco delle metriche
Per Database di Azure per MariaDB sono disponibili le metriche seguenti:

|Metrica|Nome visualizzato per la metrica|Unità|Descrizione|
|---|---|---|---|
|cpu_percent|Percentuale CPU|Percent|Percentuale di CPU in uso.|
|memory_percent|Percentuale memoria|Percent|Percentuale di memoria in uso.|
|io_consumption_percent|IO percent (Percentuale IO)|Percent|Percentuale di I/O in uso.|
|storage_percent|Percentuale archiviazione|Percent|Percentuale di spazio di archiviazione usata rispetto al massimo del server.|
|storage_used|Uso archiviazione|Byte|Quantità di spazio di archiviazione in uso. Lo spazio di archiviazione usato dal servizio può includere file di database, log delle transazioni e log del server.|
|serverlog_storage_percent|Percentuale di archiviazione dei log del server|Percent|Percentuale di spazio di archiviazione dei log del server usata rispetto allo spazio di archiviazione massimo dei log del server per il server.|
|serverlog_storage_usage|Archiviazione dei log del server usata|Byte|Quantità di spazio di archiviazione dei log del server in uso.|
|serverlog_storage_limit|Limite di archiviazione dei log del server|Byte|Spazio di archiviazione massimo dei log del server per il server.|
|storage_limit|Limite archiviazione|Byte|Spazio di archiviazione massimo per il server.|
|active_connections|Connessioni attive|Conteggio|Numero di connessioni al server attive.|
|connections_failed|Connessioni non riuscite|Conteggio|Numero di connessioni al server non riuscite.|
|network_bytes_egress|Rete in uscita|Byte|Rete in uscita tra connessioni attive.|
|network_bytes_ingress|Rete in ingresso|Byte|Rete in ingresso tra connessioni attive.|

## <a name="server-logs"></a>Log del server

È possibile abilitare la registrazione delle query lente nel server. Questi log sono disponibili anche tramite i log di diagnostica di Azure nell'Account di archiviazione, hub eventi e log monitoraggio di Azure. Per altre informazioni sull'accesso, visitare la pagina dei  [log del server](concepts-server-logs.md).

## <a name="query-store"></a>Archivio query

[Query Store](concepts-query-store.md) è una funzionalità di anteprima pubblica che tiene traccia delle prestazioni di query nel tempo, incluse informazioni su statistiche dei runtime di query ed eventi di attesa. La funzionalità rende persistenti le informazioni sulle prestazioni di runtime di query nella **mysql** dello schema. È possibile controllare la raccolta e l'archiviazione dei dati tramite vari controlli di configurazione.

## <a name="query-performance-insight"></a>Informazioni dettagliate prestazioni query

[Informazioni dettagliate prestazioni query](concepts-query-performance-insight.md) funziona in combinazione con Query Store per fornire visualizzazioni accessibili dal portale di Azure. Questi grafici consentono di identificare le principali query che influiscono sulle prestazioni. Query Performance Insight è disponibile in anteprima pubblica ed è accessibile nel **prestazioni intelligenti** sezione del Database di Azure per la pagina del portale del server di MariaDB.

## <a name="performance-recommendations"></a>Raccomandazioni per le prestazioni

La funzionalità [Raccomandazioni per le prestazioni](concepts-performance-recommendations.md) identifica le opportunità per migliorare le prestazioni dei carichi di lavoro. La versione di anteprima pubblica di Raccomandazioni per le prestazioni fornisce consigli per la creazione di nuovi indici che possono migliorare le prestazioni dei carichi di lavoro. Per generare le raccomandazioni sugli indici, la funzionalità prende in considerazione diverse caratteristiche del database, tra cui lo schema e il carico di lavoro segnalati da Query Store. Dopo avere implementato le raccomandazioni per le prestazioni, i clienti devono testare le prestazioni per valutare l'impatto di tali modifiche.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sull'accesso alle metriche e la relativa esportazione con il portale di Azure, l'API REST o l'interfaccia della riga di comando, vedere [Panoramica delle metriche in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
  - Per informazioni sulla creazione di un avviso per una metrica, vedere [Come configurare gli avvisi](howto-alert-metric.md).

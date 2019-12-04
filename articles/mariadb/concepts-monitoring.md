---
title: Monitoraggio-database di Azure per MariaDB
description: Questo articolo illustra le metriche di monitoraggio e avviso per Database di Azure per MariaDB, che includono statistiche relative a CPU, spazio di archiviazione e connessioni.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 15a396a86103f41f49d3b49878ec51c1e71add40
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74772480"
---
# <a name="monitoring-in-azure-database-for-mariadb"></a>Monitoraggio in Database di Azure per MariaDB
Il monitoraggio dei dati relativi ai server facilita la risoluzione dei problemi e l'ottimizzazione in relazione al carico di lavoro. Database di Azure per MariaDB offre varie metriche che consentono di ottenere informazioni dettagliate sul comportamento del server.

## <a name="metrics"></a>Metriche
Tutte le metriche di Azure hanno una frequenza di un minuto e offrono una cronologia di 30 giorni. È possibile configurare avvisi in base alle metriche. Le altre attività includono la configurazione di azioni automatiche, l'esecuzione di analisi avanzate e l'archiviazione della cronologia. Per altre informazioni, vedere [Panoramica delle metriche in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Per indicazioni dettagliate, vedere l'articolo su come [configurare gli avvisi](howto-alert-metric.md).

### <a name="list-of-metrics"></a>Elenco delle metriche
Per Database di Azure per MariaDB sono disponibili le metriche seguenti:

|Metrica|Nome visualizzato per la metrica|Unità|Description|
|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Percentuale di CPU in uso.|
|memory_percent|Percentuale memoria|Percentuale|Percentuale di memoria in uso.|
|io_consumption_percent|IO percent (Percentuale IO)|Percentuale|Percentuale di I/O in uso.|
|storage_percent|Percentuale archiviazione|Percentuale|Percentuale di spazio di archiviazione usata rispetto al massimo del server.|
|storage_used|Risorse di archiviazione usate|Byte|Quantità di spazio di archiviazione in uso. Lo spazio di archiviazione usato dal servizio può includere file di database, log delle transazioni e log del server.|
|serverlog_storage_percent|Percentuale di archiviazione dei log del server|Percentuale|Percentuale di spazio di archiviazione dei log del server usata rispetto allo spazio di archiviazione massimo dei log del server per il server.|
|serverlog_storage_usage|Archiviazione dei log del server usata|Byte|Quantità di spazio di archiviazione dei log del server in uso.|
|serverlog_storage_limit|Server Log storage limit (Limite archiviazione log server)|Byte|Spazio di archiviazione massimo dei log del server per il server.|
|storage_limit|Limite archiviazione|Byte|Spazio di archiviazione massimo per il server.|
|active_connections|Connessioni attive|Conteggio|Numero di connessioni al server attive.|
|connections_failed|Connessioni non riuscite|Conteggio|Numero di connessioni al server non riuscite.|
|network_bytes_egress|Rete in uscita|Byte|Rete in uscita tra connessioni attive.|
|network_bytes_ingress|Rete in ingresso|Byte|Rete in ingresso tra connessioni attive.|

## <a name="server-logs"></a>Log del server

È possibile abilitare la registrazione delle query lente nel server. Questi log sono disponibili anche tramite i log di diagnostica di Azure in log di monitoraggio di Azure, Hub eventi e account di archiviazione. Per altre informazioni sull'accesso, visitare la pagina dei  [log del server](concepts-server-logs.md).

## <a name="query-store"></a>Archivio query

[Query Store](concepts-query-store.md) tiene traccia delle prestazioni delle query nel tempo, incluse le statistiche di runtime di query e gli eventi di attesa. La funzionalità rende permanente le informazioni sulle prestazioni di query runtime nello schema **MySQL** . È possibile controllare la raccolta e l'archiviazione dei dati tramite vari controlli di configurazione.

## <a name="query-performance-insight"></a>Analisi delle prestazioni di query

[Informazioni dettagliate prestazioni query](concepts-query-performance-insight.md) funziona in combinazione con Query Store per fornire visualizzazioni accessibili dal portale di Azure. Questi grafici consentono di identificare le principali query che influiscono sulle prestazioni. Informazioni dettagliate prestazioni query è accessibile nella sezione **prestazioni intelligenti** della pagina del portale del database di Azure per il server MariaDB.

## <a name="performance-recommendations"></a>Raccomandazioni per le prestazioni

La funzionalità [Raccomandazioni per le prestazioni](concepts-performance-recommendations.md) identifica le opportunità per migliorare le prestazioni dei carichi di lavoro. Suggerimenti sulle prestazioni fornisce consigli per la creazione di nuovi indici che possono migliorare le prestazioni dei carichi di lavoro. Per generare le raccomandazioni sugli indici, la funzionalità prende in considerazione diverse caratteristiche del database, tra cui lo schema e il carico di lavoro segnalati da Query Store. Dopo avere implementato le raccomandazioni per le prestazioni, i clienti devono testare le prestazioni per valutare l'impatto di tali modifiche.

## <a name="service-health"></a>Integrità dei servizi
[Integrità dei servizi di Azure](../service-health/overview.md) offre una visualizzazione di tutte le notifiche sull'integrità del servizio nella sottoscrizione. È possibile configurare gli avvisi di integrità dei servizi per inviare notifiche tramite i canali di comunicazione preferiti quando si verificano problemi o modifiche che potrebbero influire sui servizi e le aree di Azure usati.

È possibile visualizzare gli eventi di manutenzione pianificata per database di Azure per MariaDB usando il tipo di evento **manutenzione pianificata** . Per informazioni su come creare gli **avvisi di integrità del servizio**, vedere l'articolo [creare avvisi del log attività per le notifiche del servizio](../service-health/alerts-activity-log-service-notifications.md) .

> [!IMPORTANT]
> Le notifiche di manutenzione pianificata sono disponibili in anteprima solo per Stati Uniti ORIENTAli e Regno Unito meridionale.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sull'accesso alle metriche e la relativa esportazione con il portale di Azure, l'API REST o l'interfaccia della riga di comando, vedere [Panoramica delle metriche in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
  - Per informazioni sulla creazione di un avviso per una metrica, vedere [Come configurare gli avvisi](howto-alert-metric.md).

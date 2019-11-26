---
title: Monitoraggio in Database di Azure per MySQL
description: Questo articolo illustra le metriche di monitoraggio e avviso per Database di Azure per MySQL, che includono statistiche relative a CPU, spazio di archiviazione e connessioni.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: df03f8ba0e522aacd305b6337e506f53e309660a
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384052"
---
# <a name="monitoring-in-azure-database-for-mysql"></a>Monitoraggio in Database di Azure per MySQL
Il monitoraggio dei dati relativi ai server facilita la risoluzione dei problemi e l'ottimizzazione per il carico di lavoro. Database di Azure per MySQL offre varie metriche che consentono di ottenere informazioni approfondite sul comportamento del server.

## <a name="metrics"></a>Metrica
Tutte le metriche di Azure hanno una frequenza di un minuto e offrono una cronologia di 30 giorni. È possibile configurare avvisi in base alle metriche. Per indicazioni dettagliate, vedere l'articolo su come [configurare gli avvisi](howto-alert-on-metric.md). Le altre attività includono la configurazione di azioni automatiche, l'esecuzione di analisi avanzate e l'archiviazione della cronologia. Per altre informazioni, vedere [Panoramica delle metriche in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Elenco delle metriche
Per Database di Azure per MySQL sono disponibili le metriche seguenti:

|Metrica|Nome visualizzato per la metrica|Unità|DESCRIZIONE|
|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Percentuale di CPU in uso.|
|memory_percent|Percentuale memoria|Percentuale|Percentuale di memoria in uso.|
|io_consumption_percent|Percentuale IO|Percentuale|Percentuale di I/O in uso.|
|storage_percent|Percentuale archiviazione|Percentuale|Percentuale di spazio di archiviazione usata rispetto al massimo del server.|
|storage_used|Uso archiviazione|Byte|Quantità di spazio di archiviazione in uso. Lo spazio di archiviazione usato dal servizio può includere file di database, log delle transazioni e log del server.|
|serverlog_storage_percent|Server Log storage percent (Percentuale archiviazione log server)|Percentuale|Percentuale di spazio di archiviazione dei log del server usata rispetto allo spazio di archiviazione massimo dei log del server per il server.|
|serverlog_storage_usage|Server Log storage used (Archiviazione log server usata)|Byte|Quantità di spazio di archiviazione dei log del server in uso.|
|serverlog_storage_limit|Limite di archiviazione dei log del server|Byte|Spazio di archiviazione massimo dei log del server per il server.|
|storage_limit|Limite archiviazione|Byte|Spazio di archiviazione massimo per il server.|
|active_connections|Connessioni attive|Numero|Numero di connessioni al server attive.|
|connections_failed|Connessioni non riuscite|Numero|Numero di connessioni al server non riuscite.|
|seconds_behind_master|Intervallo di replica in secondi|Numero|Numero di secondi di ritardo del server di replica rispetto al server master.|
|network_bytes_egress|Network Out|Byte|Rete in uscita tra connessioni attive.|
|network_bytes_ingress|Network In|Byte|Rete in ingresso tra connessioni attive.|
|backup_storage_used|Risorse di backup in uso|Byte|Quantità di risorse di backup usate.|

## <a name="server-logs"></a>Log del server
È possibile abilitare la registrazione lenta della query e del controllo nel server. Questi log sono disponibili anche tramite i log di diagnostica di Azure in log di monitoraggio di Azure, Hub eventi e account di archiviazione. Per altre informazioni sulla registrazione, vedere gli articoli [log di controllo](concepts-audit-logs.md) e log di [query lente](concepts-server-logs.md) .

## <a name="query-store"></a>Archivio query
[Query Store](concepts-query-store.md) è una funzionalità che tiene traccia delle prestazioni delle query nel tempo, incluse le statistiche di runtime di query e gli eventi di attesa. La funzionalità rende permanente le informazioni sulle prestazioni di query runtime nello schema **MySQL** . È possibile controllare la raccolta e l'archiviazione dei dati tramite vari controlli di configurazione.

## <a name="query-performance-insight"></a>Informazioni dettagliate sulle prestazioni delle query
[Informazioni dettagliate prestazioni query](concepts-query-performance-insight.md) funziona in combinazione con Query Store per fornire visualizzazioni accessibili dal portale di Azure. Questi grafici consentono di identificare le principali query che influiscono sulle prestazioni. Informazioni dettagliate prestazioni query è accessibile nella sezione **prestazioni intelligenti** della pagina del portale del database di Azure per il server MySQL.

## <a name="performance-recommendations"></a>Raccomandazioni per le prestazioni
La funzionalità [Raccomandazioni per le prestazioni](concepts-performance-recommendations.md) identifica le opportunità per migliorare le prestazioni dei carichi di lavoro. Suggerimenti sulle prestazioni fornisce consigli per la creazione di nuovi indici che possono migliorare le prestazioni dei carichi di lavoro. Per generare le raccomandazioni sugli indici, la funzionalità prende in considerazione diverse caratteristiche del database, tra cui lo schema e il carico di lavoro segnalati da Query Store. Dopo avere implementato le raccomandazioni per le prestazioni, i clienti devono testare le prestazioni per valutare l'impatto di tali modifiche.

## <a name="service-health"></a>Integrità del servizio
[Integrità dei servizi di Azure](../service-health/overview.md) offre una visualizzazione di tutte le notifiche sull'integrità del servizio nella sottoscrizione. È possibile configurare gli avvisi di integrità dei servizi per inviare notifiche tramite i canali di comunicazione preferiti quando si verificano problemi o modifiche che potrebbero influire sui servizi e le aree di Azure usati.

È possibile visualizzare gli eventi di manutenzione pianificata per database di Azure per MySQL usando il tipo di evento **manutenzione pianificata** . Per informazioni su come creare gli **avvisi di integrità del servizio**, vedere l'articolo [creare avvisi del log attività per le notifiche del servizio](../service-health/alerts-activity-log-service-notifications.md) .

> [!IMPORTANT]
> Le notifiche di manutenzione pianificata sono disponibili in anteprima solo per Stati Uniti ORIENTAli e Regno Unito meridionale.

## <a name="next-steps"></a>Passaggi successivi
- Per informazioni sulla creazione di un avviso per una metrica, vedere [Come configurare gli avvisi](howto-alert-on-metric.md).
- Per altre informazioni sull'accesso alle metriche e la relativa esportazione con il portale di Azure, l'API REST o l'interfaccia della riga di comando, vedere [Panoramica delle metriche in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- Leggere il blog Microsoft sulle [procedure consigliate per il monitoraggio del server](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-mysql-monitoring/).

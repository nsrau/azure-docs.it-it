---
title: Monitoraggio - Database di Azure per MariaDBMonitoring - Azure Database for MariaDB
description: Questo articolo illustra le metriche di monitoraggio e avviso per Database di Azure per MariaDB, che includono statistiche relative a CPU, spazio di archiviazione e connessioni.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 20a2066dc0228fe9c2fee09387d96bf6aafeb32f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531975"
---
# <a name="monitoring-in-azure-database-for-mariadb"></a>Monitoraggio in Database di Azure per MariaDB
Il monitoraggio dei dati relativi ai server facilita la risoluzione dei problemi e l'ottimizzazione in relazione al carico di lavoro. Database di Azure per MariaDB offre varie metriche che consentono di ottenere informazioni dettagliate sul comportamento del server.

## <a name="metrics"></a>Metriche
Tutte le metriche di Azure hanno una frequenza di un minuto e offrono una cronologia di 30 giorni. È possibile configurare avvisi in base alle metriche. Le altre attività includono la configurazione di azioni automatiche, l'esecuzione di analisi avanzate e l'archiviazione della cronologia. Per altre informazioni, vedere [Panoramica delle metriche in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Per indicazioni dettagliate, vedere l'articolo su come [configurare gli avvisi](howto-alert-metric.md).

### <a name="list-of-metrics"></a>Elenco delle metriche
Per Database di Azure per MariaDB sono disponibili le metriche seguenti:

|Metrica|Nome visualizzato per la metrica|Unità|Descrizione|
|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Percentuale di CPU in uso.|
|memory_percent|Percentuale memoria|Percentuale|Percentuale di memoria in uso.|
|io_consumption_percent|Percentuale IO|Percentuale|Percentuale di I/O in uso.|
|storage_percent|Percentuale archiviazione|Percentuale|Percentuale di spazio di archiviazione usata rispetto al massimo del server.|
|storage_used|Uso archiviazione|Byte|Quantità di spazio di archiviazione in uso. Lo spazio di archiviazione usato dal servizio può includere file di database, log delle transazioni e log del server.|
|serverlog_storage_percent|Percentuale di archiviazione dei log del server|Percentuale|Percentuale di spazio di archiviazione dei log del server usata rispetto allo spazio di archiviazione massimo dei log del server per il server.|
|serverlog_storage_usage|Archiviazione dei log del server usata|Byte|Quantità di spazio di archiviazione dei log del server in uso.|
|serverlog_storage_limit|Limite di archiviazione dei log del server|Byte|Spazio di archiviazione massimo dei log del server per il server.|
|storage_limit|Limite archiviazione|Byte|Spazio di archiviazione massimo per il server.|
|active_connections|Connessioni attive|Conteggio|Numero di connessioni al server attive.|
|connections_failed|Connessioni non riuscite|Conteggio|Numero di connessioni al server non riuscite.|
|network_bytes_egress|Rete in uscita|Byte|Rete in uscita tra connessioni attive.|
|network_bytes_ingress|Rete in ingresso|Byte|Rete in ingresso tra connessioni attive.|

## <a name="server-logs"></a>Log del server

È possibile abilitare la registrazione delle query lente nel server. Questi log sono disponibili anche tramite i log di diagnostica di Azure nei log di Monitoraggio di Azure, negli hub eventi e nell'account di archiviazione. Per altre informazioni sull'accesso, visitare la pagina dei  [log del server](concepts-server-logs.md).

## <a name="query-store"></a>Archivio query

[Query Store](concepts-query-store.md) tiene traccia delle prestazioni delle query nel tempo, incluse le statistiche di runtime delle query e gli eventi di attesa. La funzionalità rende persistenti le informazioni sulle prestazioni di runtime di query nello schema **mysql.** È possibile controllare la raccolta e l'archiviazione dei dati tramite vari controlli di configurazione.

## <a name="query-performance-insight"></a>Informazioni dettagliate prestazioni query

[Informazioni dettagliate prestazioni query](concepts-query-performance-insight.md) funziona in combinazione con Query Store per fornire visualizzazioni accessibili dal portale di Azure. Questi grafici consentono di identificare le principali query che influiscono sulle prestazioni. Informazioni dettagliate sulle prestazioni delle query è accessibile nella sezione Prestazioni intelligenti della pagina del portale del server MariaDB del database di Azure.Query Performance Insight is accessible in the **Intelligent Performance** section of your Azure Database for MariaDB server's portal page.

## <a name="performance-recommendations"></a>Raccomandazioni per le prestazioni

La funzionalità [Raccomandazioni per le prestazioni](concepts-performance-recommendations.md) identifica le opportunità per migliorare le prestazioni dei carichi di lavoro. Consigli sulle prestazioni fornisce suggerimenti per la creazione di nuovi indici che possono migliorare le prestazioni dei carichi di lavoro. Per generare le raccomandazioni sugli indici, la funzionalità prende in considerazione diverse caratteristiche del database, tra cui lo schema e il carico di lavoro segnalati da Query Store. Dopo avere implementato le raccomandazioni per le prestazioni, i clienti devono testare le prestazioni per valutare l'impatto di tali modifiche.

## <a name="planned-maintenance-notification"></a>Notifica di manutenzione pianificata

Le notifiche di **manutenzione pianificata** consentono di ricevere avvisi per la manutenzione pianificata imminente nel database di Azure per MariaDB.Planned maintenance notifications allow you to receive alerts for upcoming planned maintenance to your Azure Database for MariaDB. Queste notifiche sono integrate con la manutenzione pianificata [di Integrità dei](../service-health/overview.md) servizi e consentono di visualizzare tutte le operazioni di manutenzione pianificata per le sottoscrizioni in un'unica posizione. Consente inoltre di ridimensionare la notifica ai gruppi di destinatari giusti per gruppi di risorse diversi, in quanto potrebbero essere presenti contatti diversi responsabili di risorse diverse. Riceverai la notifica della manutenzione imminente 72 ore prima dell'evento.

> [!Note]
> Faremo ogni tentativo di fornire la notifica di **manutenzione pianificata** 72 ore di preavviso per tutti gli eventi. Tuttavia, in caso di patch critiche o di sicurezza, le notifiche potrebbero essere inviate più vicino all'evento o essere omesse.

### <a name="to-receive-planned-maintenance-notification"></a>Per ricevere una notifica di manutenzione pianificata

1. Nel [portale](https://portal.azure.com) selezionare **Integrità del servizio**.
2. Nella sezione **Avvisi** selezionare **Avvisi integrità**.
3. Selezionare **: Aggiungi avviso di integrità** del servizio e compilare i campi.
4. Compilare i campi obbligatori. 
5. Scegliere il **tipo di evento**, selezionare Manutenzione **pianificata** o **Seleziona tutto**
6. In **Gruppi di** azioni definire come si desidera ricevere l'avviso (ricevere un messaggio di posta elettronica, attivare un'app per la logica e così via).  
7. Verificare che Abilita regola al momento della creazione sia impostato su Sì.Ensure Enable rule upon creation is set to Yes.
8. Selezionare **Crea regola di avviso** per completare l'avviso

Per la procedura dettagliata su come creare avvisi di integrità del **servizio,** vedere [Creare avvisi del log attività nelle notifiche del servizio](../service-health/alerts-activity-log-service-notifications.md).

> [!IMPORTANT]
> Le notifiche di manutenzione pianificata sono attualmente in anteprima

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sull'accesso alle metriche e la relativa esportazione con il portale di Azure, l'API REST o l'interfaccia della riga di comando, vedere [Panoramica delle metriche in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
  - Per informazioni sulla creazione di un avviso per una metrica, vedere [Come configurare gli avvisi](howto-alert-metric.md).

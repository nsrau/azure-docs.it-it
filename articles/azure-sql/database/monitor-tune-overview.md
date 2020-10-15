---
title: Monitoraggio e ottimizzazione delle prestazioni
description: Panoramica delle funzionalità di monitoraggio e ottimizzazione delle prestazioni e della metodologia nel database SQL di Azure e in Azure SQL Istanza gestita.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, sstein
ms.date: 09/30/2020
ms.openlocfilehash: 6c8d048d43a16191cc7b1245ad2d686ba2ca22ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91596981"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database-and-azure-sql-managed-instance"></a>Monitoraggio e ottimizzazione delle prestazioni del database SQL di Azure e di Istanza gestita di SQL di Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Per monitorare le prestazioni di un database nel database SQL di Azure e Istanza gestita di Azure SQL, iniziare monitorando le risorse di CPU e i/o usate dal carico di lavoro in relazione al livello di prestazioni del database scelto per la selezione di un livello di servizio e di prestazioni specifico. A tale scopo, il database SQL di Azure e Azure SQL Istanza gestita emettono metriche delle risorse che possono essere visualizzate nella portale di Azure o usando uno di questi strumenti di gestione SQL Server: [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) o [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

Il database SQL di Azure offre una serie di Advisor di database per fornire indicazioni intelligenti per l'ottimizzazione delle prestazioni e le opzioni di ottimizzazione automatica per migliorare le prestazioni. Inoltre, Informazioni dettagliate prestazioni query Mostra i dettagli sulle query responsabili della maggior parte dell'utilizzo di CPU e IO per i database singoli e in pool.

Il database SQL di Azure e Azure SQL Istanza gestita offrono funzionalità avanzate di monitoraggio e ottimizzazione supportate dall'intelligenza artificiale per facilitare la risoluzione dei problemi e ottimizzare le prestazioni dei database e delle soluzioni. È possibile scegliere di configurare l' [esportazione di flussi](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) di questi [Intelligent Insights](intelligent-insights-overview.md) e altri log delle risorse del database e le metriche in una delle diverse destinazioni per l'utilizzo e l'analisi, in particolare tramite analisi [SQL](../../azure-monitor/insights/azure-sql.md). Analisi SQL di Azure è una soluzione di monitoraggio cloud avanzata per il monitoraggio delle prestazioni di tutti i database su larga scala e tra più sottoscrizioni in un'unica visualizzazione. Per un elenco dei log e delle metriche che è possibile esportare, vedere [telemetria diagnostica per l'esportazione](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#diagnostic-telemetry-for-export)

SQL Server dispone di funzionalità di monitoraggio e diagnostica personalizzate utilizzate da database SQL e SQL Istanza gestita, ad esempio [archivio query](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) e [viste a gestione dinamica (DMV)](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views). Vedere [monitoraggio con DMV](monitoring-with-dmvs.md) per gli script da monitorare per diversi problemi di prestazioni.

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Funzionalità di monitoraggio e ottimizzazione nell'portale di Azure

Nel portale di Azure, il database SQL di Azure e Azure SQL Istanza gestita consentono di monitorare le metriche delle risorse. Il database SQL di Azure fornisce gli Advisor di database e Informazioni dettagliate prestazioni query fornisce indicazioni per l'ottimizzazione delle query e l'analisi delle prestazioni delle query. Nella portale di Azure è possibile abilitare l'ottimizzazione automatica per i [server SQL logici](logical-servers.md) e i relativi database singoli e in pool.

> [!NOTE]
> I database con un utilizzo estremamente basso possono essere visualizzati nel portale con un utilizzo inferiore a quello effettivo. A causa del modo in cui i dati di telemetria vengono emessi durante la conversione di un valore Double nell'intero più vicino, determinati importi di utilizzo minori di 0,5 verranno arrotondati a 0, causando una perdita di granularità dei dati di telemetria emessi. Per informazioni dettagliate, vedere [metriche del database ridotto e del pool elastico arrotondamento a zero](#low-database-and-elastic-pool-metrics-rounding-to-zero).

### <a name="azure-sql-database-and-azure-sql-managed-instance-resource-monitoring"></a>Monitoraggio delle risorse del database SQL di Azure e di Azure SQL Istanza gestita

È possibile monitorare rapidamente un'ampia gamma di metriche delle risorse nella portale di Azure nella visualizzazione **metrica** . Queste metriche consentono di verificare se un database sta raggiungendo il 100% del processore, della memoria o delle risorse di i/o. Una percentuale elevata di DTU o processore, oltre a una percentuale di i/o elevata, indica che il carico di lavoro potrebbe richiedere più risorse di CPU o IO. Potrebbe inoltre indicare query che devono essere ottimizzate.

  ![Metriche delle risorse](./media/monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors-in-azure-sql-database"></a>Advisor di database nel database SQL di Azure

Il database SQL di Azure include gli [Advisor di database](database-advisor-implement-performance-recommendations.md) che forniscono indicazioni per l'ottimizzazione delle prestazioni per i database singoli e in pool. Queste raccomandazioni sono disponibili nell'portale di Azure e con [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseadvisor). È anche possibile abilitare l' [ottimizzazione automatica](automatic-tuning-overview.md) in modo che il database SQL di Azure possa implementare automaticamente le indicazioni di ottimizzazione.

### <a name="query-performance-insight-in-azure-sql-database"></a>Informazioni dettagliate prestazioni query nel database SQL di Azure

[Informazioni dettagliate prestazioni query](query-performance-insight-use.md) Mostra le prestazioni della portale di Azure delle query più lunghe e più lunghe per i database singoli e in pool.

### <a name="low-database-and-elastic-pool-metrics-rounding-to-zero"></a>Metriche di database e pool elastici di basso livello fino a zero

A partire dal 2020 settembre, i database con utilizzo estremamente basso possono essere visualizzati nel portale con un utilizzo inferiore rispetto a quello effettivo. A causa del modo in cui i dati di telemetria vengono emessi durante la conversione di un valore Double nell'intero più vicino, determinati importi di utilizzo minori di 0,5 verranno arrotondati a 0, causando una perdita di granularità dei dati di telemetria emessi.

Ad esempio: si consideri una finestra di 1 minuto con i quattro punti dati seguenti: 0,1, 0,1, 0,1, 0,1, questi valori bassi vengono arrotondati per difetto a 0, 0, 0, 0 e presentano una media pari a 0. Se uno dei punti dati è maggiore di 0,5, ad esempio: 0,1, 0,1, 0,9, 0,1, vengono arrotondati a 0, 0, 1, 0 e indicano una media di 0,25.

Metriche del database interessate:
- cpu_percent
- log_write_percent
- workers_percent
- sessions_percent
- physical_data_read_percent
- dtu_consumption_percent2
- xtp_storage_percent

Metriche del pool elastico interessate:
- cpu_percent
- physical_data_read_percent
- log_write_percent
- memory_usage_percent
- data_storage_percent
- peak_worker_percent
- peak_session_percent
- xtp_storage_percent
- allocated_data_storage_percent


## <a name="generate-intelligent-assessments-of-performance-issues"></a>Genera valutazioni intelligenti dei problemi di prestazioni

[Intelligent Insights](intelligent-insights-overview.md) per il database SQL di Azure e azure SQL istanza gestita usa l'Intelligence incorporata per monitorare continuamente l'utilizzo del database tramite l'intelligenza artificiale e rilevare gli eventi che provocano una riduzione delle prestazioni. Intelligent Insights rileva automaticamente i problemi di prestazioni con i database in base ai tempi di attesa, agli errori o ai timeout di esecuzione delle query. Una volta rilevata, viene eseguita un'analisi dettagliata che genera un log delle risorse (denominato sqlinsights) con una [valutazione intelligente dei problemi](intelligent-insights-troubleshoot-performance.md). Questa valutazione è costituita da un'analisi della causa radice del problema di prestazioni del database e, dove possibile, da raccomandazioni per il miglioramento delle prestazioni.

Intelligent Insights è una funzionalità unica dell'intelligence integrata di Azure che offre i vantaggi seguenti:

- Monitoraggio proattivo
- Analisi delle prestazioni personalizzate
- Rilevamento tempestivo della riduzione delle prestazioni del database
- Analisi della causa radice dei problemi rilevati
- Raccomandazioni per il miglioramento delle prestazioni
- Capacità di aumentare il numero di istanze in centinaia di migliaia di database
- Impatto positivo sulle risorse DevOps e sul costo totale di proprietà

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>Abilitare l'esportazione del flusso di metriche e log delle risorse

È possibile abilitare e configurare l' [esportazione di flussi di dati di telemetria di diagnostica](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) in una delle diverse destinazioni, incluso il registro delle risorse Intelligent Insights. Usare [analisi SQL](../../azure-monitor/insights/azure-sql.md) e altre funzionalità per utilizzare questi dati di telemetria diagnostici aggiuntivi per identificare e risolvere i problemi di prestazioni.

È possibile configurare le impostazioni di diagnostica per trasmettere le categorie di metriche e i log delle risorse per i singoli database, i database in pool, i pool elastici, le istanze gestite e i database di istanza a una delle risorse di Azure seguenti.

### <a name="log-analytics-workspace-in-azure-monitor"></a>Area di lavoro Log Analytics in monitoraggio di Azure

È possibile trasmettere le metriche e i log delle risorse a un' [area di lavoro log Analytics in monitoraggio di Azure](../../azure-monitor/platform/resource-logs-collect-workspace.md). I dati trasmessi qui possono essere usati da [SQL Analytics](../../azure-monitor/insights/azure-sql.md), una soluzione di monitoraggio solo cloud che consente di monitorare in modo intelligente i database che includono report sulle prestazioni, avvisi e raccomandazioni di mitigazione. I dati trasmessi a un'area di lavoro di Log Analytics possono essere analizzati con altri dati di monitoraggio raccolti e consentono anche di sfruttare altre funzionalità di monitoraggio di Azure, ad esempio avvisi e visualizzazioni.

### <a name="azure-event-hubs"></a>Hub eventi di Azure

È possibile trasmettere le metriche e i log delle risorse a [Hub eventi di Azure](../../azure-monitor/platform/resource-logs-stream-event-hubs.md). Trasmissione di dati di telemetria diagnostica a hub eventi per fornire le funzionalità seguenti:

- **Trasmettere log a sistemi di registrazione e telemetria di terze parti**

  Trasmettere tutte le metriche e i log delle risorse a un singolo hub eventi per inviare tramite pipe i dati di log a uno strumento SIEM o log Analytics di terze parti.
- **Creare una piattaforma di registrazione e telemetria personalizzata**

  La natura di pubblicazione-sottoscrizione altamente scalabile degli hub eventi consente di inserire in modo flessibile le metriche e i log delle risorse in una piattaforma di telemetria personalizzata. Per informazioni dettagliate, vedere [progettazione e dimensionamento di una piattaforma di telemetria su scala globale in hub eventi di Azure](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) .
- **Visualizzare l'integrità del servizio tramite lo streaming dei dati in Power BI**

  USA Hub eventi, analisi di flusso e Power BI per trasformare i dati di diagnostica in informazioni quasi in tempo reale sui servizi di Azure. Per informazioni dettagliate su questa soluzione, vedere [analisi di flusso e Power bi: un dashboard di analisi in tempo reale per il flusso di dati](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard) .

### <a name="azure-storage"></a>Archiviazione di Azure

Trasmettere le metriche e i log delle risorse ad [archiviazione di Azure](../../azure-monitor/platform/resource-logs-collect-storage.md). Usare archiviazione di Azure per archiviare grandi quantità di dati di telemetria di diagnostica per una frazione del costo delle due opzioni di streaming precedenti.

## <a name="use-extended-events"></a>USA eventi estesi 

Inoltre, è possibile utilizzare [gli eventi estesi](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) in SQL Server per il monitoraggio e la risoluzione dei problemi avanzati. L'architettura degli eventi estesi consente agli utenti di raccogliere quanti più o meno dati necessari per la risoluzione dei problemi o per identificare un problema di prestazioni. Per informazioni sull'uso di eventi estesi nel database SQL di Azure, vedere [eventi estesi nel database SQL di Azure](xevent-db-diff-from-svr.md).

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sulle raccomandazioni per le prestazioni intelligenti per i database singoli e in pool, vedere [consigli sulle prestazioni di database Advisor](database-advisor-implement-performance-recommendations.md).
- Per altre informazioni sul monitoraggio automatico delle prestazioni del database con diagnostica automatizzata e analisi della causa radice dei problemi di prestazioni, vedere [Intelligent Insights in Azure SQL](intelligent-insights-overview.md).

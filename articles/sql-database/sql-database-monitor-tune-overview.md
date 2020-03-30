---
title: Monitoraggio e ottimizzazione delle prestazioni
description: Panoramica delle funzionalità e della metodologia di monitoraggio e ottimizzazione delle prestazioni nel database SQL di Azure.An overview of monitoring and performance tuning capabilities and methodology in Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 837d88665c1fdffe902c9c478e5d6dc65a2e402a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268730"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database"></a>Monitoraggio e ottimizzazione delle prestazioni nel database SQL di AzureMonitoring and performance tuning in Azure SQL Database

Per monitorare le prestazioni di un database nel database SQL di Azure, avviare monitorando le risorse di CPU e I/O usate dal carico di lavoro in relazione al livello di prestazioni del database scelto nella selezione di un livello di servizio e di un livello di prestazioni specifici. A tale scopo, il database SQL di Azure genera metriche delle risorse che possono essere visualizzate nel portale di Azure o usando uno di questi strumenti di gestione SQL: [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) o SQL Server Management [Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

Per i database singoli e in pool, il database SQL di Azure offre una serie di advisor di database per fornire consigli intelligenti per l'ottimizzazione delle prestazioni e opzioni di ottimizzazione automatica per migliorare le prestazioni. Inoltre, Query Performance Insight mostra i dettagli sulle query responsabili della maggior parte dell'utilizzo della CPU e delle operazioni di I/O per i database singoli e in pool.

Il database SQL di Azure offre funzionalità di monitoraggio e ottimizzazione aggiuntive supportate dall'intelligenza artificiale per facilitare la risoluzione dei problemi e l'ottimizzazione delle prestazioni dei database e delle soluzioni. È possibile scegliere di configurare [l'esportazione](sql-database-metrics-diag-logging.md) di streaming di queste [analisi intelligenti](sql-database-intelligent-insights.md) e di altri log e metriche delle risorse del database SQL in una delle diverse destinazioni per l'utilizzo e l'analisi, in particolare utilizzando SQL [Analytics](../azure-monitor/insights/azure-sql.md). Analisi SQL di Azure è una soluzione di monitoraggio cloud avanzata per il monitoraggio delle prestazioni di tutti i database SQL di Azure su larga scala e tra più sottoscrizioni in un'unica visualizzazione. Per un elenco dei log e delle metriche che è possibile esportare, vedere [telemetria di diagnostica per l'esportazioneFor](sql-database-metrics-diag-logging.md#diagnostic-telemetry-for-export-for-azure-sql-database) a list of the logs and metrics that you can export, see diagnostic telemetry for export

Infine, SQL ha le proprie funzionalità di monitoraggio e diagnostica con [l'archivio](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) query di SQL Server e le viste a [gestione dinamica (DMV)](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views). Vedere [Monitoraggio tramite DMV](sql-database-monitoring-with-dmvs.md) per gli script da monitorare per una serie di problemi di prestazioni.

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Funzionalità di monitoraggio e ottimizzazione nel portale di AzureMonitoring and tuning capabilities in the Azure portal

Nel portale di Azure il database SQL di Azure fornisce il monitoraggio delle metriche delle risorse per tutti i tipi di distribuzione. Inoltre, per i database singoli e in pool, gli advisor di database e Query Performance Insight forniscono suggerimenti per l'ottimizzazione delle query e l'analisi delle prestazioni delle query. Infine, nel portale di Azure, è possibile abilitare automatico per i server logici e i relativi database singoli e in pool.

### <a name="sql-database-resource-monitoring"></a>Monitoraggio delle risorse del database SQLSQL Database resource monitoring

È possibile monitorare rapidamente le metriche delle risorse seguenti nel portale di Azure nella visualizzazione **Metriche:You** can quickly monitor the following resource metrics in the Azure portal in the Metrics view:

- **Utilizzo DTU**

  Verificare se un database o un pool elastico raggiunge il 100% dell'utilizzo di DTU per un periodo di tempo prolungato. L'utilizzo elevato del dTU indica che il carico di lavoro potrebbe richiedere più risorse di CPU o I/O.High DTU usage indicates that your workload might need more CPU or IO resources. Potrebbe anche indicare le query che devono essere ottimizzate.
- **Utilizzo della CPU**

  Verificare se un database, un pool elastico o un'istanza gestita raggiunge il 100% dell'utilizzo della CPU per un periodo di tempo prolungato. Una CPU elevata indica che il carico di lavoro potrebbe richiedere più risorse di CPU o I/O.High CPU indicates that your workload might need more CPU or IO resources. Potrebbe anche indicare le query che devono essere ottimizzate.
- **Utilizzo I/O**

  Verificare se un database, un pool elastico o un'istanza di gestione raggiunge i limiti di I/O dell'archiviazione sottostante. L'utilizzo elevato di I/O indica che il carico di lavoro potrebbe richiedere più risorse di CPU o I/O.High IO usage indicates that your workload might need more CPU or IO resources. Potrebbe anche indicare le query che devono essere ottimizzate.

  ![Metriche delle risorse](./media/sql-database-monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors"></a>Consulenti di database
' Database SQL di Azure include [consulenti di database](sql-database-advisor.md) che forniscono consigli di ottimizzazione delle prestazioni per database singoli e in pool. Questi consigli sono disponibili nel portale di Azure e tramite [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseadvisor). È inoltre possibile abilitare [l'ottimizzazione automatica](sql-database-automatic-tuning.md) in modo che il database SQL possa implementare automaticamente questi suggerimenti per l'ottimizzazione.

### <a name="query-performance-insight"></a>Informazioni dettagliate prestazioni query

[Informazioni dettagliate sulle prestazioni](sql-database-query-performance.md) delle query mostra le prestazioni nel portale di Azure delle query di utilizzo più lungo e più longeve per i database singoli e in pool.

## <a name="generate-intelligent-assessments-of-performance-issues"></a>Generare valutazioni intelligenti dei problemi di prestazioni

Azure SQL Database [Intelligent Insights](sql-database-intelligent-insights.md) usa l'intelligenza integrata per monitorare continuamente l'utilizzo del database tramite l'intelligenza artificiale e rilevare eventi didisturbatori che causano una riduzione delle prestazioni. Intelligent Insights rileva automaticamente i problemi di prestazioni con i database nel database SQL di Azure in base a tempi di attesa, errori o timeout di esecuzione delle query. Una volta rilevata, viene eseguita un'analisi dettagliata che genera un log delle risorse (denominato SQLInsights) con una [valutazione intelligente dei problemi.](sql-database-intelligent-insights-troubleshoot-performance.md) Questa valutazione è costituita da un'analisi della causa radice del problema di prestazioni del database e, dove possibile, da raccomandazioni per il miglioramento delle prestazioni.

Intelligent Insights è una funzionalità unica dell'intelligence integrata di Azure che offre i vantaggi seguenti:

- Monitoraggio proattivo
- Analisi delle prestazioni personalizzate
- Rilevamento tempestivo della riduzione delle prestazioni del database
- Analisi della causa radice dei problemi rilevati
- Raccomandazioni per il miglioramento delle prestazioni
- Capacità di aumentare il numero di istanze in centinaia di migliaia di database
- Impatto positivo sulle risorse DevOps e sul costo totale di proprietà

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>Abilitare l'esportazione di streaming delle metriche e dei log delle risorseEnable the streaming export of metrics and resource logs

È possibile abilitare e configurare [l'esportazione di streaming dei dati di telemetria di diagnostica](sql-database-metrics-diag-logging.md) in una delle diverse destinazioni, incluso il log delle risorse di Intelligent Insights.You can enable and configure the streaming export of diagnostic telemetry to one of several destinations, including the Intelligent Insights resource log. Usare [ANALISI SQL](../azure-monitor/insights/azure-sql.md) e altre funzionalità per usare questi dati di telemetria di diagnostica aggiuntivi per identificare e risolvere i problemi di prestazioni.

Le impostazioni di diagnostica vengono configurate per trasmettere categorie di metriche e log delle risorse per singoli database, database in pool, pool elastici, istanze gestite e database di istanza a una delle risorse di Azure seguenti.

### <a name="log-analytics-workspace-in-azure-monitor"></a>Area di lavoro di Log Analytics in Monitoraggio di AzureLog Analytics workspace in Azure monitor

È possibile trasmettere metriche e log delle risorse a un'area di lavoro di [Log Analytics in Monitoraggio di Azure.You](../azure-monitor/platform/resource-logs-collect-workspace.md)can stream metrics and resource logs to a Log Analytics workspace in Azure Monitor . I dati trasmessi in questo campo possono essere utilizzati da [SQL Analytics](../azure-monitor/insights/azure-sql.md), una soluzione di monitoraggio solo cloud che fornisce un monitoraggio intelligente dei database che include report sulle prestazioni, avvisi e consigli per l'attenuazione. I dati trasmessi in un'area di lavoro di Log Analytics possono essere analizzati con altri dati di monitoraggio raccolti e consentono inoltre di sfruttare altre funzionalità di Monitoraggio di Azure, ad esempio avvisi e visualizzazioni.

### <a name="azure-event-hubs"></a>Hub eventi di Azure

È possibile trasmettere le metriche e i log delle risorse agli [hub eventi di Azure.](../azure-monitor/platform/resource-logs-stream-event-hubs.md) Streaming dei dati di telemetria di diagnostica negli hub eventi per fornire le funzionalità seguenti:Streaming diagnostic telemetry to event hubs to provide the following functionality:

- **Registri di flusso nei sistemi di telemetria e registrazione di terze parti**

  Trasmettetutte le metriche e i log delle risorse a un singolo hub eventi per reindirizzare i dati di log a uno strumento SIEM o log di terze parti.
- **Creare una piattaforma di telemetria e registrazione personalizzataBuild a custom telemetry and logging platform**

  La natura di pubblicazione e sottoscrizione altamente scalabile degli hub eventi consente di inserire in modo flessibile metriche e log delle risorse in una piattaforma di telemetria personalizzata. Per informazioni dettagliate, vedere Progettazione e ridimensionamento di una piattaforma di [telemetria con scalabilità globale negli hub eventi di Azure.See Designing](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) and Sizing a Global Scale Telemetry Platform on Azure Event Hubs for details.
- **Visualizzare l'integrità del servizio trasmettendo i dati in Power BIView service health by streaming data to Power BI**

  Usa Hub eventi, Analisi di flusso e Power BI per trasformare i dati di diagnostica in informazioni dettagliate quasi in tempo reale sui tuoi servizi di Azure. Per informazioni dettagliate su questa soluzione, vedere [Analisi di flusso e Power BI: dashboard](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard) di analisi in tempo reale per lo streaming dei dati.

### <a name="azure-storage"></a>Archiviazione di Azure

Eseguire il flusso di metriche e log delle risorse in [Archiviazione di Azure.](../azure-monitor/platform/resource-logs-collect-storage.md) Usare Archiviazione di Azure per archiviare grandi quantità di dati di telemetria di diagnostica per una frazione del costo delle due opzioni di streaming precedenti.

## <a name="use-extended-events-in-the-sql-database-engine"></a>Usare eventi estesi nel motore di database SQLUse extended events in the SQL database engine

Inoltre, è possibile utilizzare [gli eventi estesi](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) in SQL per il monitoraggio avanzato aggiuntivo e la risoluzione dei problemi. L'architettura degli eventi estesi consente agli utenti di raccogliere la quantità di dati necessaria per risolvere o identificare un problema di prestazioni. Per informazioni sull'utilizzo di eventi estesi nel database SQL, vedere [Eventi estesi nel database SQL](sql-database-xevent-db-diff-from-svr.md).

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sui consigli sulle prestazioni intelligenti per i database singoli e in pool, vedere [Database advisor performance recommendations](sql-database-advisor.md).
- Per altre informazioni sul monitoraggio automatico delle prestazioni del database con diagnostica automatizzata e analisi della causa radice dei problemi di prestazioni, vedere [Intelligent Insights in Azure SQL](sql-database-intelligent-insights.md).
'''''''''
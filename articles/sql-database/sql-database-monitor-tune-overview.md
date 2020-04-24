---
title: Monitoraggio e ottimizzazione delle prestazioni
description: Panoramica delle funzionalità di monitoraggio e ottimizzazione delle prestazioni e della metodologia nel database SQL di Azure.
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
# <a name="monitoring-and-performance-tuning-in-azure-sql-database"></a>Monitoraggio e ottimizzazione delle prestazioni nel database SQL di Azure

Per monitorare le prestazioni di un database nel database SQL di Azure, iniziare monitorando le risorse di CPU e i/o usate dal carico di lavoro in relazione al livello di prestazioni del database scelto per la selezione di un livello di servizio e di prestazioni specifico. A tale scopo, il database SQL di Azure genera metriche delle risorse che possono essere visualizzate nel portale di Azure o usando uno di questi strumenti di gestione SQL: [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) o [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

Per i database singoli e in pool, il database SQL di Azure offre una serie di Advisor di database per fornire indicazioni intelligenti per l'ottimizzazione delle prestazioni e le opzioni di ottimizzazione automatica per migliorare le prestazioni. Inoltre, Informazioni dettagliate prestazioni query Mostra i dettagli sulle query responsabili della maggior parte dell'utilizzo di CPU e IO per i database singoli e in pool.

Il database SQL di Azure offre funzionalità di monitoraggio e ottimizzazione aggiuntive supportate dall'intelligenza artificiale per facilitare la risoluzione dei problemi e ottimizzare le prestazioni dei database e delle soluzioni. È possibile scegliere di configurare l' [esportazione di flussi](sql-database-metrics-diag-logging.md) di questi [Intelligent Insights](sql-database-intelligent-insights.md) e altri log delle risorse del database SQL e le metriche in una delle diverse destinazioni per l'utilizzo e l'analisi, in particolare tramite analisi [SQL](../azure-monitor/insights/azure-sql.md). Analisi SQL di Azure è una soluzione di monitoraggio cloud avanzata per il monitoraggio delle prestazioni di tutti i database SQL di Azure su larga scala e tra più sottoscrizioni in un'unica visualizzazione. Per un elenco dei log e delle metriche che è possibile esportare, vedere [telemetria diagnostica per l'esportazione](sql-database-metrics-diag-logging.md#diagnostic-telemetry-for-export-for-azure-sql-database)

Infine, SQL offre funzionalità di monitoraggio e diagnostica personalizzate con [SQL Server query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) e [viste a gestione dinamica (DMV)](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views). Vedere [monitoraggio con DMV](sql-database-monitoring-with-dmvs.md) per gli script da monitorare per diversi problemi di prestazioni.

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Funzionalità di monitoraggio e ottimizzazione nell'portale di Azure

Nel portale di Azure, il database SQL di Azure consente di monitorare le metriche delle risorse per tutti i tipi di distribuzione. Per i database singoli e in pool, gli Advisor di database e Informazioni dettagliate prestazioni query forniscono indicazioni per l'ottimizzazione delle query e l'analisi delle prestazioni delle query. Infine, nella portale di Azure è possibile abilitare l'attivazione automatica per i server logici e i rispettivi database singoli e in pool.

### <a name="sql-database-resource-monitoring"></a>Monitoraggio delle risorse del database SQL

È possibile monitorare rapidamente le metriche delle risorse seguenti nell'portale di Azure nella visualizzazione **metrica** :

- **Utilizzo di DTU**

  Verificare se un database o un pool elastico raggiunge il 100% dell'utilizzo di DTU per un periodo di tempo prolungato. Elevato utilizzo di DTU indica che il carico di lavoro potrebbe richiedere più risorse di CPU o IO. Potrebbe inoltre indicare query che devono essere ottimizzate.
- **Utilizzo della CPU**

  Verificare se un database, un pool elastico o un'istanza gestita sta raggiungendo il 100% dell'utilizzo della CPU per un lungo periodo di tempo. CPU elevata indica che il carico di lavoro potrebbe richiedere più risorse di CPU o IO. Potrebbe inoltre indicare query che devono essere ottimizzate.
- **Utilizzo i/o**

  Verificare se un database, un pool elastico o un'istanza di gestione sta raggiungendo i limiti di i/o dell'archiviazione sottostante. Utilizzo i/o elevato indica che il carico di lavoro potrebbe richiedere più risorse di CPU o IO. Potrebbe inoltre indicare query che devono essere ottimizzate.

  ![Metriche delle risorse](./media/sql-database-monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors"></a>Advisor per database
Il database SQL di Azure include gli [Advisor di database](sql-database-advisor.md) che forniscono indicazioni per l'ottimizzazione delle prestazioni per i database singoli e in pool. Queste raccomandazioni sono disponibili nell'portale di Azure e con [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseadvisor). È anche possibile abilitare l' [ottimizzazione automatica](sql-database-automatic-tuning.md) in modo che il database SQL possa implementare automaticamente le indicazioni di ottimizzazione.

### <a name="query-performance-insight"></a>Informazioni dettagliate prestazioni query

[Informazioni dettagliate prestazioni query](sql-database-query-performance.md) Mostra le prestazioni della portale di Azure delle query più lunghe e più lunghe per i database singoli e in pool.

## <a name="generate-intelligent-assessments-of-performance-issues"></a>Genera valutazioni intelligenti dei problemi di prestazioni

Il database SQL di Azure [Intelligent Insights](sql-database-intelligent-insights.md) usa l'Intelligence incorporata per monitorare continuamente l'utilizzo del database tramite l'intelligenza artificiale e rilevare gli eventi che provocano una riduzione delle prestazioni. Intelligent Insights rileva automaticamente i problemi di prestazioni con i database nel database SQL di Azure in base ai tempi di attesa, agli errori o ai timeout di esecuzione delle query. Una volta rilevata, viene eseguita un'analisi dettagliata che genera un log delle risorse (denominato sqlinsights) con una [valutazione intelligente dei problemi](sql-database-intelligent-insights-troubleshoot-performance.md). Questa valutazione è costituita da un'analisi della causa radice del problema di prestazioni del database e, dove possibile, da raccomandazioni per il miglioramento delle prestazioni.

Intelligent Insights è una funzionalità unica dell'intelligence integrata di Azure che offre i vantaggi seguenti:

- Monitoraggio proattivo
- Analisi delle prestazioni personalizzate
- Rilevamento tempestivo della riduzione delle prestazioni del database
- Analisi della causa radice dei problemi rilevati
- Raccomandazioni per il miglioramento delle prestazioni
- Capacità di aumentare il numero di istanze in centinaia di migliaia di database
- Impatto positivo sulle risorse DevOps e sul costo totale di proprietà

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>Abilitare l'esportazione del flusso di metriche e log delle risorse

È possibile abilitare e configurare l' [esportazione di flussi di dati di telemetria di diagnostica](sql-database-metrics-diag-logging.md) in una delle diverse destinazioni, incluso il registro delle risorse Intelligent Insights. Usare [analisi SQL](../azure-monitor/insights/azure-sql.md) e altre funzionalità per utilizzare questi dati di telemetria diagnostici aggiuntivi per identificare e risolvere i problemi di prestazioni.

È possibile configurare le impostazioni di diagnostica per trasmettere le categorie di metriche e i log delle risorse per i singoli database, i database in pool, i pool elastici, le istanze gestite e i database di istanza a una delle risorse di Azure seguenti.

### <a name="log-analytics-workspace-in-azure-monitor"></a>Area di lavoro Log Analytics in monitoraggio di Azure

È possibile trasmettere le metriche e i log delle risorse a un' [area di lavoro log Analytics in monitoraggio di Azure](../azure-monitor/platform/resource-logs-collect-workspace.md). I dati trasmessi qui possono essere usati da [SQL Analytics](../azure-monitor/insights/azure-sql.md), una soluzione di monitoraggio solo cloud che consente di monitorare in modo intelligente i database che includono report sulle prestazioni, avvisi e raccomandazioni di mitigazione. I dati trasmessi a un'area di lavoro di Log Analytics possono essere analizzati con altri dati di monitoraggio raccolti e consentono anche di sfruttare altre funzionalità di monitoraggio di Azure, ad esempio avvisi e visualizzazioni.

### <a name="azure-event-hubs"></a>Hub eventi di Azure

È possibile trasmettere le metriche e i log delle risorse a [Hub eventi di Azure](../azure-monitor/platform/resource-logs-stream-event-hubs.md). Trasmissione di dati di telemetria diagnostica a hub eventi per fornire le funzionalità seguenti:

- **Trasmettere log a sistemi di registrazione e telemetria di terze parti**

  Trasmettere tutte le metriche e i log delle risorse a un singolo hub eventi per inviare tramite pipe i dati di log a uno strumento SIEM o log Analytics di terze parti.
- **Creare una piattaforma di registrazione e telemetria personalizzata**

  La natura di pubblicazione-sottoscrizione altamente scalabile degli hub eventi consente di inserire in modo flessibile le metriche e i log delle risorse in una piattaforma di telemetria personalizzata. Per informazioni dettagliate, vedere [progettazione e dimensionamento di una piattaforma di telemetria su scala globale in hub eventi di Azure](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) .
- **Visualizzare l'integrità del servizio tramite lo streaming dei dati in Power BI**

  USA Hub eventi, analisi di flusso e Power BI per trasformare i dati di diagnostica in informazioni quasi in tempo reale sui servizi di Azure. Per informazioni dettagliate su questa soluzione, vedere [analisi di flusso e Power bi: un dashboard di analisi in tempo reale per il flusso di dati](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard) .

### <a name="azure-storage"></a>Archiviazione di Azure

Trasmettere le metriche e i log delle risorse ad [archiviazione di Azure](../azure-monitor/platform/resource-logs-collect-storage.md). Usare archiviazione di Azure per archiviare grandi quantità di dati di telemetria di diagnostica per una frazione del costo delle due opzioni di streaming precedenti.

## <a name="use-extended-events-in-the-sql-database-engine"></a>Usare gli eventi estesi nel motore di database SQL

Inoltre, è possibile utilizzare [gli eventi estesi](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) in SQL per ulteriori funzionalità di monitoraggio e risoluzione dei problemi. L'architettura degli eventi estesi consente agli utenti di raccogliere quanti più o meno dati necessari per la risoluzione dei problemi o per identificare un problema di prestazioni. Per informazioni sull'uso di eventi estesi nel database SQL, vedere [eventi estesi nel database SQL](sql-database-xevent-db-diff-from-svr.md).

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sulle raccomandazioni per le prestazioni intelligenti per i database singoli e in pool, vedere [consigli sulle prestazioni di database Advisor](sql-database-advisor.md).
- Per altre informazioni sul monitoraggio automatico delle prestazioni del database con diagnostica automatizzata e analisi della causa radice dei problemi di prestazioni, vedere [Intelligent Insights in Azure SQL](sql-database-intelligent-insights.md).
'''''''''
---
title: 'Gestibilità e monitoraggio: attività delle query, utilizzo delle risorse'
description: Informazioni sulle funzionalità disponibili per gestire e monitorare Azure Synapse Analytics. Usare il portale di Azure e Dynamic Management View (DMV, viste a gestione dinamica) per comprendere l'attività di query e l'utilizzo delle risorse del data warehouse.
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/11/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 0b7accec9fdce1ad81a08aee17b37d655409948b
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607564"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>Monitoraggio dell'utilizzo delle risorse e dell'attività delle query in Azure Synapse AnalyticsMonitoring resource utilization and query activity in Azure Synapse Analytics

Azure Synapse Analytics offre un'esperienza di monitoraggio avanzata all'interno del portale di Azure per visualizzare informazioni dettagliate sul carico di lavoro del data warehouse. Il portale di Azure è lo strumento consigliato per il monitoraggio del data warehouse, in quanto fornisce periodi di conservazione, avvisi, raccomandazioni, grafici personalizzabili, nonché dashboard di metriche e log configurabili. Il portale consente inoltre di integrarsi con altri servizi di monitoraggio di Azure, ad esempio Monitoraggio di Azure (log) con L'analisi dei log per fornire un'esperienza di monitoraggio olistica non solo per il data warehouse, ma anche per l'intera piattaforma di analisi di Azure per un'esperienza di monitoraggio integrata. Questa documentazione descrive le funzionalità di monitoraggio disponibili per ottimizzare e gestire la piattaforma di analisi. 

## <a name="resource-utilization"></a>Utilizzo delle risorse

Le metriche seguenti sono disponibili nel portale di Azure per Synapse SQL. Tali metriche vengono rilevate tramite il [Monitoraggio di Azure](../../azure-monitor/platform/data-collection.md#metrics?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).


| Nome misurazione             | Descrizione                                                  | Tipo di aggregazione |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| Percentuale CPU          | Utilizzo della CPU in tutti i nodi per il data warehouse      | Avg, Min, Max    |
| Percentuale di I/O di dati      | Utilizzo di I/O in tutti i nodi per il data warehouse       | Avg, Min, Max    |
| Percentuale memoria       | Utilizzo della memoria (SQL Server) in tutti i nodi per il data warehouse | Avg, Min, Max   |
| Query attive          | Numero di query attive in esecuzione nel sistema             | SUM              |
| Query in coda          | Numero di query in coda in attesa di avviare l'esecuzione          | SUM              |
| Connessioni riuscite  | Numero di connessioni ai dati riuscite                 | Somma, Conteggio       |
| Connessioni non riuscite      | Numero di connessioni al data warehouse non riuscite           | Somma, Conteggio       |
| Blocco da parte del firewall     | Numero di accessi al data warehouse bloccati     | Somma, Conteggio       |
| Limite DWU               | Obiettivo del livello di servizio del data warehouse                | Avg, Min, Max    |
| Percentuale DWU          | Valore massimo tra percentuale di CPU e percentuale di I/O        | Avg, Min, Max    |
| Uso DWU                | Limite DWU * percentuale DWU                                   | Avg, Min, Max    |
| Percentuale dei riscontri nella cache    | (Riscontri nella cache/Mancato riscontro nella cache) * 100, dove "riscontri nella cache" è la somma di tutte le occorrenze di segmenti columnstore della cache SSD locale e "mancato riscontro nella cache" è il mancato riscontro di segmenti columnstore nella cache SSD locale sommato tra tutti i nodi | Avg, Min, Max    |
| Percentuale della cache utilizzata   | (Cache usata/Capacità della cache) * 100, dove "cache usata" è la somma di tutti i byte nella cache SSD locale in tutti i nodi e "capacità della cache" è la somma della capacità di archiviazione della cache SSD locale in tutti i nodi | Avg, Min, Max    |
| Percentuale di tempdb locale | Uso di tempdb locale in tutti i nodi di calcolo: i valori vengono generati ogni cinque minuti | Avg, Min, Max    |
| Dimensioni di archiviazione dati (GB) | Dimensione totale dei dati caricati nel database. Ciò include i dati che risiedano in tabelle CCI e non CCI in cui le dimensioni delle tabelle non CCI sono misurate in base alle dimensioni totali del file di database | SUM |
| Dimensioni ripristino di emergenza (GB) | Dimensioni totali del backup geografico eseguito ogni 24 ore | SUM |
| Dimensioni archiviazione snapshot (GB)Snapshot Storage size (GB) | Dimensioni totali degli snapshot acquisiti per fornire punti di ripristino del database. Sono inclusi snapshot automatizzati e definiti dall'utente. | SUM |

Aspetti da considerare quando si visualizzano le metriche e si impostano gli avvisi:Things to consider when viewing metrics and setting alerts:

- DWU utilizzato rappresenta solo una **rappresentazione generale dell'utilizzo** nel pool SQL e non deve essere un indicatore completo dell'utilizzo. Per determinare se aumentare o ridurre la scalabilità, considerare tutti i fattori che possono essere interessati da DWU, ad esempio la concorrenza, la memoria, tempdb e la capacità della cache adattiva. È [consigliabile eseguire il carico di lavoro con impostazioni DWU diverse](sql-data-warehouse-manage-compute-overview.md#finding-the-right-size-of-data-warehouse-units) per determinare il modo migliore per soddisfare gli obiettivi aziendali.
- Le connessioni non riuscite e riuscite vengono segnalate per un particolare data warehouse, non per il server logico
- La percentuale di memoria riflette l'utilizzo anche se il data warehouse è in stato di inattività, ma non riflette il consumo di memoria del carico di lavoro attivo. Usare e tenere traccia di questa metrica insieme ad altri (tempdb, cache gen2) per prendere una decisione olistica se la scalabilità per una capacità di cache aggiuntiva aumenterà le prestazioni del carico di lavoro per soddisfare le esigenze.

## <a name="query-activity"></a>Attività query

Per un'esperienza a livello di codice durante il monitoraggio di Synapse SQL tramite T-SQL, il servizio fornisce un set di viste a gestione dinamica (DMV). Queste viste sono utili durante la risoluzione dei problemi e l'identificazione dei colli di bottiglia nelle prestazioni con il carico di lavoro.

Per visualizzare l'elenco delle DMV applicabili a Synapse SQL, fare riferimento a questa [documentazione](sql-data-warehouse-reference-tsql-system-views.md#sql-data-warehouse-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Metriche e registrazione diagnostica

Sia le metriche che i log possono essere esportati in Monitoraggio di Azure, in particolare nel componente log di Monitoraggio di [Azure,](../../azure-monitor/log-query/log-query-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) e possono essere utilizzati a livello di programmazione tramite query di [log.](../../azure-monitor/log-query/get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) La latenza del log per Synapse SQL è di circa 10-15 minuti. Per ulteriori informazioni sui fattori che influiscono sulla latenza, vedere la documentazione seguente.

## <a name="next-steps"></a>Passaggi successivi

Le seguenti guide introduttive descrivono scenari e casi d'uso comuni in cui avviene il monitoraggio e la gestione del data warehouse:

- [Monitorare il carico di lavoro del data warehouse con viste a gestione dinamica](/sql-data-warehouse/sql-data-warehouse-manage-monitor?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  

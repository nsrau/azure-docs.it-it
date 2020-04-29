---
title: Gestibilità e monitoraggio-attività di query, utilizzo delle risorse
description: Informazioni sulle funzionalità disponibili per la gestione e il monitoraggio di Azure sinapsi Analytics. Usare il portale di Azure e Dynamic Management View (DMV, viste a gestione dinamica) per comprendere l'attività di query e l'utilizzo delle risorse del data warehouse.
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/09/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: d38c0df45da3a751a456846813543a4ce5de98eb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416205"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>Monitoraggio dell'utilizzo delle risorse e dell'attività di query in Azure sinapsi Analytics

Azure sinapsi Analytics offre un'esperienza di monitoraggio completa all'interno del portale di Azure per ottenere informazioni dettagliate sul carico di lavoro del data warehouse. Il portale di Azure è lo strumento consigliato per il monitoraggio del data warehouse, in quanto fornisce periodi di conservazione, avvisi, raccomandazioni, grafici personalizzabili, nonché dashboard di metriche e log configurabili. Il portale consente anche di eseguire l'integrazione con altri servizi di monitoraggio di Azure, ad esempio monitoraggio di Azure (log) con log Analytics, per offrire un'esperienza di monitoraggio olistica solo per le data warehouse, ma anche per l'intera piattaforma di analisi di Azure per un'esperienza di monitoraggio integrata. Questa documentazione descrive le funzionalità di monitoraggio disponibili per ottimizzare e gestire la piattaforma di analisi con SQL Analytics.

## <a name="resource-utilization"></a>Utilizzo delle risorse

Nella portale di Azure per SQL Analytics sono disponibili le metriche seguenti. Tali metriche vengono rilevate tramite il [Monitoraggio di Azure](../../azure-monitor/platform/data-collection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#metrics).

| Nome misurazione             | Descrizione                                                  | Tipo di aggregazione |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| Percentuale CPU          | Utilizzo della CPU in tutti i nodi per il data warehouse      | Media, min, max    |
| Percentuale di I/O di dati      | Utilizzo di I/O in tutti i nodi per il data warehouse       | Media, min, max    |
| Percentuale di memoria       | Utilizzo della memoria (SQL Server) in tutti i nodi per il data warehouse | Media, min, max   |
| Query attive          | Numero di query attive in esecuzione nel sistema             | SUM              |
| Query in coda          | Numero di query in coda in attesa di avvio dell'esecuzione          | SUM              |
| Connessioni riuscite  | Numero di connessioni riuscite (account di accesso) per il database | Somma, conteggio       |
| Connessioni non riuscite      | Numero di connessioni non riuscite (account di accesso) per il database | Somma, conteggio       |
| Blocco da parte del firewall     | Numero di accessi al data warehouse bloccati     | Somma, conteggio       |
| Limite DWU               | Obiettivo del livello di servizio del data warehouse                | Media, min, max    |
| Percentuale DWU          | Valore massimo tra percentuale di CPU e percentuale di I/O        | Media, min, max    |
| Uso DWU                | Limite DWU * percentuale DWU                                   | Media, min, max    |
| Percentuale dei riscontri nella cache    | (Riscontri nella cache/Mancato riscontro nella cache) * 100, dove "riscontri nella cache" è la somma di tutte le occorrenze di segmenti columnstore della cache SSD locale e "mancato riscontro nella cache" è il mancato riscontro di segmenti columnstore nella cache SSD locale sommato tra tutti i nodi | Media, min, max    |
| Percentuale della cache utilizzata   | (Cache usata/Capacità della cache) * 100, dove "cache usata" è la somma di tutti i byte nella cache SSD locale in tutti i nodi e "capacità della cache" è la somma della capacità di archiviazione della cache SSD locale in tutti i nodi | Media, min, max    |
| Percentuale di tempdb locale | Uso di tempdb locale in tutti i nodi di calcolo: i valori vengono generati ogni cinque minuti | Media, min, max    |
| Dimensioni di archiviazione dati (GB) | Dimensioni totali del database. Sono inclusi lo spazio utilizzato, riservato e non allocato. Lo spazio non allocato viene mantenuto per il database per ottimizzare le prestazioni di query e di caricamento. | SUM |
| Dimensioni del ripristino di emergenza (GB) | Dimensioni totali del backup geografico effettuato ogni 24 ore | SUM |
| Dimensioni di archiviazione snapshot (GB) | Dimensioni totali degli snapshot effettuati per fornire punti di ripristino del database. Sono inclusi gli snapshot automatici e definiti dall'utente. | SUM |

Aspetti da considerare quando si visualizzano le metriche e si configurano gli avvisi:

- DWU usato rappresenta solo una **rappresentazione di alto livello dell'utilizzo** nel pool SQL e non è destinato a essere un indicatore completo dell'utilizzo. Per determinare se aumentare o ridurre le prestazioni, prendere in considerazione tutti i fattori che possono essere interessati da DWU, ad esempio concorrenza, memoria, tempdb e capacità della cache adattiva. Si consiglia di [eseguire il carico di lavoro in impostazioni DWU diverse](sql-data-warehouse-manage-compute-overview.md#finding-the-right-size-of-data-warehouse-units) per determinare il funzionamento migliore per soddisfare gli obiettivi aziendali.
- Connessioni non riuscite e riuscite per un particolare data warehouse non per il server logico
- La percentuale di memoria riflette l'uso anche se il data warehouse è in stato di inattività, ma non riflette il consumo di memoria del carico di lavoro attivo. Usare e tenere traccia di questa metrica insieme ad altri (tempdb, cache Gen2) per prendere una decisione olistica su se il ridimensionamento per la capacità aggiuntiva della cache aumenterà le prestazioni del carico di lavoro per soddisfare i requisiti.

## <a name="query-activity"></a>Attività query

Per un'esperienza a livello di codice durante il monitoraggio di SQL Analytics tramite T-SQL, il servizio fornisce un set di viste a gestione dinamica (DMV). Queste viste sono utili durante la risoluzione dei problemi e l'identificazione dei colli di bottiglia nelle prestazioni con il carico di lavoro.

Per visualizzare l'elenco di DMV che si applicano a sinapsi SQL, fare riferimento a questa [documentazione](../sql/reference-tsql-system-views.md#sql-pool-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Metriche e registrazione diagnostica 

Sia le metriche che i log possono essere esportati in monitoraggio di Azure, in particolare il componente [log di monitoraggio di Azure](../../azure-monitor/log-query/log-query-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) , a cui è possibile accedere a livello di codice tramite query di [log](../../azure-monitor/log-query/get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). La latenza del log per analisi SQL è circa 10-15 minuti. Per ulteriori informazioni sui fattori che influiscono sulla latenza, consultare la documentazione seguente.

## <a name="next-steps"></a>Passaggi successivi

Nella Guida dettagliata seguente vengono descritti gli scenari e i casi di utilizzo comuni per il monitoraggio e la gestione dei data warehouse:

- [Monitorare il carico di lavoro del data warehouse con viste a gestione dinamica](sql-data-warehouse-manage-monitor.md)

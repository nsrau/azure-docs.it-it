---
title: Facilità di gestione e monitoraggio - Attività di query, utilizzo delle risorse
description: Informazioni sulle funzionalità disponibili per gestire e monitorare Azure Synapse Analytics. Usare il portale di Azure e Dynamic Management View (DMV, viste a gestione dinamica) per comprendere l'attività di query e l'utilizzo delle risorse del data warehouse.
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/09/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: f66efb9112a9342122f5b56ab11b862ce3c7c61b
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93314439"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>Monitoraggio dell'attività di query e dell'utilizzo delle risorse in Azure Synapse Analytics

Azure Synapse Analytics offre una ricca esperienza di monitoraggio nel portale di Azure per scoprire informazioni dettagliate sul carico di lavoro del data warehouse. Il portale di Azure è lo strumento consigliato per il monitoraggio del data warehouse, in quanto fornisce periodi di conservazione, avvisi, raccomandazioni, grafici personalizzabili, nonché dashboard di metriche e log configurabili. Il portale consente anche l'integrazione con altri servizi di monitoraggio di Azure come Monitoraggio di Azure (log) con Log Analytics per offrire un'esperienza di monitoraggio olistica non solo per il data warehouse, ma anche per l'intera piattaforma di analisi di Azure per un'esperienza di monitoraggio integrata. Questa documentazione descrive le funzionalità di monitoraggio disponibili per ottimizzare e gestire la piattaforma analitica con Synapse SQL.

## <a name="resource-utilization"></a>Utilizzo delle risorse

Nel portale di Azure sono disponibili le metriche seguenti per Synapse SQL. Tali metriche vengono rilevate tramite il [Monitoraggio di Azure](../../azure-monitor/platform/data-collection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#metrics).

| Nome misurazione             | Descrizione                                                  | Tipo di aggregazione |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| Percentuale CPU          | Utilizzo della CPU in tutti i nodi per il data warehouse      | Avg, Min, Max    |
| Percentuale di I/O di dati      | Utilizzo di I/O in tutti i nodi per il data warehouse       | Avg, Min, Max    |
| Percentuale di memoria       | Utilizzo di memoria (SQL Server) in tutti i nodi per il data warehouse | Avg, Min, Max   |
| Query attive          | Numero di query attive in esecuzione nel sistema             | SUM              |
| Query in coda          | Numero di query in coda in attesa dell'avvio dell'esecuzione          | SUM              |
| Connessioni riuscite  | Numero di connessioni (accessi) riuscite al database | Sum, Count       |
| Connessioni non riuscite      | Numero di connessioni (accessi) non riuscite al database | Sum, Count       |
| Blocco da parte del firewall     | Numero di accessi al data warehouse bloccati     | Sum, Count       |
| Limite DWU               | Obiettivo del livello di servizio del data warehouse                | Avg, Min, Max    |
| Percentuale DWU          | Valore massimo tra percentuale di CPU e percentuale di I/O        | Avg, Min, Max    |
| Uso DWU                | Limite DWU * percentuale DWU                                   | Avg, Min, Max    |
| Percentuale dei riscontri nella cache    | (Riscontri nella cache/Mancato riscontro nella cache) * 100, dove "riscontri nella cache" è la somma di tutte le occorrenze di segmenti columnstore della cache SSD locale e "mancato riscontro nella cache" è il mancato riscontro di segmenti columnstore nella cache SSD locale sommato tra tutti i nodi | Avg, Min, Max    |
| Percentuale della cache utilizzata   | (Cache usata/Capacità della cache) * 100, dove "cache usata" è la somma di tutti i byte nella cache SSD locale in tutti i nodi e "capacità della cache" è la somma della capacità di archiviazione della cache SSD locale in tutti i nodi | Avg, Min, Max    |
| Percentuale di tempdb locale | Uso di tempdb locale in tutti i nodi di calcolo: i valori vengono generati ogni cinque minuti | Avg, Min, Max    |

Aspetti da considerare quando si visualizzano le metriche e si configurano gli avvisi:

- Uso DWU è solo una **rappresentazione di alto livello dell'utilizzo** nel pool SQL e non deve essere inteso come un indicatore complessivo dell'utilizzo. Per determinare se aumentarlo o ridurlo, prendere in considerazione tutti i fattori che possono essere interessati dal valore DWU, ad esempio concorrenza, memoria, tempdb e capacità della cache adattiva. È consigliabile [eseguire il carico di lavoro con impostazioni DWU diverse](sql-data-warehouse-manage-compute-overview.md#finding-the-right-size-of-data-warehouse-units) per determinare l'impostazione più adatta a soddisfare gli obiettivi di business.
- Le connessioni non riuscite e riuscite vengono segnalate per una data warehouse particolare, non per il server stesso.
- La percentuale di memoria riflette l'utilizzo anche se il data warehouse è in stato di inattività, ma non riflette il consumo di memoria del carico di lavoro attivo. Usare e tenere traccia di questa metrica insieme ad altri fattori (tempdb, cache Gen2) per un approccio olistico per determinare se il ridimensionamento con l'aggiunta di capacità della cache aumenterà le prestazioni del carico di lavoro per soddisfare i requisiti.

## <a name="query-activity"></a>Attività di query

Per un'esperienza programmatica durante il monitoraggio di Synapse SQL tramite T-SQL, il servizio mette a disposizione un set di DMV (Dynamic Management View, vista a gestione dinamica). Queste viste sono utili durante la risoluzione dei problemi e l'identificazione dei colli di bottiglia nelle prestazioni con il carico di lavoro.

Per visualizzare l'elenco delle DMV valide per Synapse SQL, fare riferimento a questa [documentazione](../sql/reference-tsql-system-views.md#dedicated-sql-pool-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Metriche e registrazione diagnostica 

Le metriche e i log possono essere esportati in Monitoraggio di Azure, in particolare il componente dei [log di Monitoraggio di Azure](../../azure-monitor/log-query/log-query-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json), ed è possibile accedervi a livello programmatico tramite [query di log](../../azure-monitor/log-query/get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). La latenza dei log per Synapse SQL è circa 10-15 minuti. Per altre informazioni sui fattori che influiscono sulla latenza, visitare la documentazione seguente.

## <a name="next-steps"></a>Passaggi successivi

La guida pratica seguente descrive scenari e casi d'uso comuni durante il monitoraggio e la gestione del data warehouse:

- [Monitorare il carico di lavoro del data warehouse con viste a gestione dinamica](sql-data-warehouse-manage-monitor.md)

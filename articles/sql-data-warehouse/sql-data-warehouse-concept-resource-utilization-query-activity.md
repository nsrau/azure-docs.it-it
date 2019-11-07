---
title: Gestibilità e monitoraggio-attività di query, utilizzo delle risorse
description: Informazioni sulle funzionalità disponibili per gestire e monitorare Azure SQL Data Warehouse. Usare il portale di Azure e Dynamic Management View (DMV, viste a gestione dinamica) per comprendere l'attività di query e l'utilizzo delle risorse del data warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/09/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 1a210e2622212ed59dfa12f9f9a108c6ffe08714
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692904"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-sql-data-warehouse"></a>Monitoraggio dell'attività di query e dell'utilizzo delle risorse in Azure SQL Data Warehouse
Azure SQL Data Warehouse offre una ricca esperienza di monitoraggio nel portale di Azure per esplorare informazioni dettagliate sul carico di lavoro del data warehouse. Il portale di Azure è lo strumento consigliato per il monitoraggio del data warehouse, in quanto fornisce periodi di conservazione, avvisi, raccomandazioni, grafici personalizzabili, nonché dashboard di metriche e log configurabili. Il portale consente anche di eseguire l'integrazione con altri servizi di monitoraggio di Azure, come Operations Management Suite (OMS) e monitoraggio di Azure (log), per offrire un'esperienza di monitoraggio olistica solo per i data warehouse ma anche per l'intera analisi di Azure piattaforma per un'esperienza di monitoraggio integrata. Questa documentazione descrive le funzionalità di monitoraggio disponibili per ottimizzare e gestire la piattaforma analitica con SQL Data Warehouse. 

## <a name="resource-utilization"></a>Utilizzo delle risorse 
Le metriche seguenti sono disponibili nel portale di Azure per SQL Data Warehouse. Tali metriche vengono rilevate tramite il [Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection#metrics).


| Nome della metrica             | Descrizione                                                  | Tipo di aggregazione |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| Percentuale CPU          | Utilizzo della CPU in tutti i nodi per il data warehouse      | Massima          |
| Percentuale di I/O di dati      | Utilizzo di I/O in tutti i nodi per il data warehouse       | Massima          |
| Percentuale di memoria       | Utilizzo della memoria (SQL Server) in tutti i nodi per il data warehouse | Massima          |
| Connessioni riuscite  | Numero di connessioni ai dati riuscite                 | Totale            |
| Connessioni non riuscite      | Numero di connessioni al data warehouse non riuscite           | Totale            |
| Blocco da parte del firewall     | Numero di accessi al data warehouse bloccati     | Totale            |
| Limite DWU               | Obiettivo del livello di servizio del data warehouse                | Massima          |
| Percentuale DWU          | Valore massimo tra percentuale di CPU e percentuale di I/O        | Massima          |
| Uso DWU                | Limite DWU * percentuale DWU                                   | Massima          |
| Percentuale dei riscontri nella cache    | (Riscontri nella cache/Mancato riscontro nella cache) * 100, dove "riscontri nella cache" è la somma di tutte le occorrenze di segmenti columnstore della cache SSD locale e "mancato riscontro nella cache" è il mancato riscontro di segmenti columnstore nella cache SSD locale sommato tra tutti i nodi | Massima          |
| Percentuale della cache utilizzata   | (Cache usata/Capacità della cache) * 100, dove "cache usata" è la somma di tutti i byte nella cache SSD locale in tutti i nodi e "capacità della cache" è la somma della capacità di archiviazione della cache SSD locale in tutti i nodi | Massima          |
| Percentuale di tempdb locale | Uso di tempdb locale in tutti i nodi di calcolo: i valori vengono generati ogni cinque minuti | Massima          |

> Aspetti da considerare quando si visualizzano le metriche e si configurano gli avvisi:
>
> - Connessioni non riuscite e riuscite per un particolare data warehouse non per il server logico
> - La percentuale di memoria riflette l'uso anche se il data warehouse è in stato di inattività, ma non riflette il consumo di memoria del carico di lavoro attivo. Usare e tenere traccia di questa metrica insieme ad altri (tempdb, cache Gen2) per prendere una decisione olistica su se il ridimensionamento per la capacità aggiuntiva della cache aumenterà le prestazioni del carico di lavoro per soddisfare i requisiti.


## <a name="query-activity"></a>Attività query
Per un'esperienza programmatica durante il monitoraggio di SQL Data Warehouse tramite T-SQL, il servizio fornisce un set di viste a gestione dinamica (DMV). Queste viste sono utili durante la risoluzione dei problemi e l'identificazione dei colli di bottiglia nelle prestazioni con il carico di lavoro.

Per visualizzare l'elenco delle viste a gestione dinamica fornite da SQL Data Warehouse, fare riferimento a questa [documentazione](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Metriche e registrazione diagnostica
Sia le metriche che i log possono essere esportati in monitoraggio di Azure, in particolare il componente [log di monitoraggio di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) , a cui è possibile accedere a livello di codice tramite query di [log](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata). La latenza del log per SQL Data Warehouse è circa 10-15 minuti. Per ulteriori informazioni sui fattori che influiscono sulla latenza, consultare la documentazione seguente.


## <a name="next-steps"></a>Passaggi successivi
Le seguenti guide introduttive descrivono scenari e casi d'uso comuni in cui avviene il monitoraggio e la gestione del data warehouse:

- [Monitorare il carico di lavoro del data warehouse con viste a gestione dinamica](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)

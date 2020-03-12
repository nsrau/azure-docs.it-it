---
title: Gestibilità e monitoraggio-attività di query, utilizzo delle risorse
description: Informazioni sulle funzionalità disponibili per la gestione e il monitoraggio di Azure sinapsi Analytics. Usare il portale di Azure e Dynamic Management View (DMV, viste a gestione dinamica) per comprendere l'attività di query e l'utilizzo delle risorse del data warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/11/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 82bf6f9a78a46659cc2e0955895c6e1a6e6eb3aa
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096633"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>Monitoraggio dell'utilizzo delle risorse e dell'attività di query in Azure sinapsi Analytics
Azure sinapsi Analytics offre un'esperienza di monitoraggio completa all'interno del portale di Azure per ottenere informazioni dettagliate sul carico di lavoro del data warehouse. Il portale di Azure è lo strumento consigliato per il monitoraggio del data warehouse, in quanto fornisce periodi di conservazione, avvisi, raccomandazioni, grafici personalizzabili, nonché dashboard di metriche e log configurabili. Il portale consente anche di eseguire l'integrazione con altri servizi di monitoraggio di Azure, ad esempio monitoraggio di Azure (log) con log Analytics, per offrire un'esperienza di monitoraggio olistica solo per le data warehouse, ma anche per l'intera piattaforma di analisi di Azure per un sistema integrato Esperienza di monitoraggio. Questa documentazione descrive le funzionalità di monitoraggio disponibili per ottimizzare e gestire la piattaforma di analisi con SQL Analytics. 

## <a name="resource-utilization"></a>Utilizzo delle risorse 
Nella portale di Azure per SQL Analytics sono disponibili le metriche seguenti. Tali metriche vengono rilevate tramite il [Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection#metrics).


| Nome misurazione             | Descrizione                                                  | Tipo di aggregazione |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| Percentuale CPU          | Utilizzo della CPU in tutti i nodi per il data warehouse      | Media, min, max    |
| Percentuale di I/O di dati      | Utilizzo di I/O in tutti i nodi per il data warehouse       | Media, min, max    |
| Percentuale di memoria       | Utilizzo della memoria (SQL Server) in tutti i nodi per il data warehouse | Media, min, max   |
| Query attive          | Numero di query attive in esecuzione nel sistema             | SUM              |
| Query in coda          | Numero di query in coda in attesa di avvio dell'esecuzione          | SUM              |
| Connessioni riuscite  | Numero di connessioni ai dati riuscite                 | Somma, conteggio       |
| Connessioni non riuscite      | Numero di connessioni al data warehouse non riuscite           | Somma, conteggio       |
| Blocco da parte del firewall     | Numero di accessi al data warehouse bloccati     | Somma, conteggio       |
| Limite DWU               | Obiettivo del livello di servizio del data warehouse                | Media, min, max    |
| Percentuale DWU          | Valore massimo tra percentuale di CPU e percentuale di I/O        | Media, min, max    |
| Uso DWU                | Limite DWU * percentuale DWU                                   | Media, min, max    |
| Percentuale dei riscontri nella cache    | (Riscontri nella cache/Mancato riscontro nella cache) * 100, dove "riscontri nella cache" è la somma di tutte le occorrenze di segmenti columnstore della cache SSD locale e "mancato riscontro nella cache" è il mancato riscontro di segmenti columnstore nella cache SSD locale sommato tra tutti i nodi | Media, min, max    |
| Percentuale della cache utilizzata   | (Cache usata/Capacità della cache) * 100, dove "cache usata" è la somma di tutti i byte nella cache SSD locale in tutti i nodi e "capacità della cache" è la somma della capacità di archiviazione della cache SSD locale in tutti i nodi | Media, min, max    |
| Percentuale di tempdb locale | Uso di tempdb locale in tutti i nodi di calcolo: i valori vengono generati ogni cinque minuti | Media, min, max    |
| Dimensioni di archiviazione dei dati | Dimensioni totali dei dati caricati nel database. Sono inclusi i dati che risiedono nelle tabelle CCI e non CCI, in cui la dimensione delle tabelle non CCI viene misurata in base alle dimensioni totali del file di database | SUM |
| Dimensioni del ripristino di emergenza | Dimensioni totali del backup geografico effettuato ogni 24 ore | SUM |
| Dimensioni di archiviazione snapshot | Dimensioni totali degli snapshot effettuati per fornire punti di ripristino del database. Sono inclusi gli snapshot automatici e definiti dall'utente. | SUM |

Aspetti da considerare quando si visualizzano le metriche e si configurano gli avvisi:

- DWU usato rappresenta solo una **rappresentazione di alto livello dell'utilizzo** nel pool SQL e non è destinato a essere un indicatore completo dell'utilizzo. Per determinare se aumentare o ridurre le prestazioni, prendere in considerazione tutti i fattori che possono essere interessati da DWU, ad esempio concorrenza, memoria, tempdb e capacità della cache adattiva. Si consiglia di [eseguire il carico di lavoro in impostazioni DWU diverse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-compute-overview#finding-the-right-size-of-data-warehouse-units) per determinare il funzionamento migliore per soddisfare gli obiettivi aziendali.
- Connessioni non riuscite e riuscite per un particolare data warehouse non per il server logico
- La percentuale di memoria riflette l'uso anche se il data warehouse è in stato di inattività, ma non riflette il consumo di memoria del carico di lavoro attivo. Usare e tenere traccia di questa metrica insieme ad altri (tempdb, cache Gen2) per prendere una decisione olistica su se il ridimensionamento per la capacità aggiuntiva della cache aumenterà le prestazioni del carico di lavoro per soddisfare i requisiti.


## <a name="query-activity"></a>Attività query
Per un'esperienza a livello di codice durante il monitoraggio di SQL Analytics tramite T-SQL, il servizio fornisce un set di viste a gestione dinamica (DMV). Queste viste sono utili durante la risoluzione dei problemi e l'identificazione dei colli di bottiglia nelle prestazioni con il carico di lavoro.

Per visualizzare l'elenco di DMV fornito da SQL Analytics, fare riferimento a questa [documentazione](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Metriche e registrazione diagnostica
Sia le metriche che i log possono essere esportati in monitoraggio di Azure, in particolare il componente [log di monitoraggio di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) , a cui è possibile accedere a livello di codice tramite query di [log](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata). La latenza del log per analisi SQL è circa 10-15 minuti. Per ulteriori informazioni sui fattori che influiscono sulla latenza, consultare la documentazione seguente.


## <a name="next-steps"></a>Passaggi successivi
Le seguenti guide introduttive descrivono scenari e casi d'uso comuni in cui avviene il monitoraggio e la gestione del data warehouse:

- [Monitorare il carico di lavoro del data warehouse con viste a gestione dinamica](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)

---
title: Ottimizzazione delle prestazioni con memorizzazione nella cache dei set di risultati
description: Panoramica della funzionalità di memorizzazione nella cache del set di risultati per il pool SQL Synapse in Azure Synapse AnalyticsResult set caching feature overview for Synapse SQL pool in Azure Synapse Analytics
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.custom: azure-synapse
ms.openlocfilehash: 4eef8a3a83456a9f2066311b9339b26b83afa009
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633793"
---
# <a name="performance-tuning-with-result-set-caching"></a>Ottimizzazione delle prestazioni con memorizzazione nella cache dei set di risultati

Quando la memorizzazione nella cache del set di risultati è abilitata, ANALISI SQL memorizza automaticamente nella cache i risultati delle query nel database utente per un utilizzo ripetitivo.  Ciò consente alle esecuzioni successive di query di ottenere risultati direttamente dalla cache persistente in modo che il ricalcolo non è necessario.   La memorizzazione nella cache del set di risultati migliora le prestazioni delle query e riduce l'utilizzo delle risorse di calcolo.  Inoltre, le query che utilizzano il set di risultati memorizzati nella cache non utilizzano slot di concorrenza e pertanto non vengono conteggiate rispetto ai limiti di concorrenza esistenti. Per motivi di sicurezza, gli utenti possono accedere ai risultati memorizzati nella cache solo se dispongono delle stesse autorizzazioni di accesso ai dati degli utenti che creano i risultati memorizzati nella cache.  

## <a name="key-commands"></a>Comandi da tastiera

[Attivare/disattivare la memorizzazione nella cache del set di risultati per un database utente](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[Attivare/disattivare la memorizzazione nella cache del set di risultati per un database utente](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[Attivare/disattivare la memorizzazione nella cache del set di risultati per una sessione](/sql/t-sql/statements/set-result-set-caching-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[Controllare la dimensione del set di risultati memorizzato nella cacheCheck the size of cached result set](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  

[Pulire la cache](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>Elementi non memorizzati nella cacheWhat's not cached  

Dopo l'attivazione della memorizzazione nella cache dei set di risultati per un database, i risultati vengono memorizzati nella cache per tutte le query fino al riempimento della cache, ad eccezione delle query seguenti:

- Query che usano funzioni non deterministiche come DateTime.Now()
- Query che usano funzioni definite dall'utente
- Query che utilizzano tabelle con sicurezza a livello di riga o di colonna abilitata
- Query che restituiscono dati con dimensioni di riga maggiori di 64 KB

> [!IMPORTANT]
> Le operazioni per creare la cache del set di risultati e recuperare i dati dalla cache si verificano nel nodo di controllo di un'istanza del pool SQL Synapse.
> Quando la memorizzazione nella cache del set di risultati è attivata, l'esecuzione di query che restituiscono set di risultati di grandi dimensioni, ad esempio più di un milione di righe, può causare un utilizzo elevato della CPU nel nodo di controllo e rallentare nel complesso la risposta alle query nell'istanza.  Queste query vengono in genere usate durante l'esplorazione dei dati o le operazioni ETL (estrazione, trasformazione e caricamento). Per evitare il sovraccarico del nodo di controllo e la comparsa di problemi di prestazioni, gli utenti devono DISATTIVARE la memorizzazione nella cache del set di risultati nel database prima di eseguire query di questo tipo.  

Eseguire questa query per il tempo impiegato dalle operazioni di memorizzazione nella cache del set di risultati per una query:Run this query for the time taken by result set caching operations for a query:

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command
FROM sys.dm_pdw_request_steps
WHERE request_id  = <'request_id'>;
```

Di seguito è riportato un output di esempio per una query eseguita con la memorizzazione nella cache del set di risultati disabilitata.

![Query-steps-with-rsc-disabled](./media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

Di seguito è riportato un output di esempio per una query eseguita con la memorizzazione nella cache del set di risultati abilitata.

![Query-steps-with-rsc-enabled](./media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>Quando vengono utilizzati i risultati memorizzati nella cacheWhen cached results are used

Il set di risultati memorizzato nella cache viene riutilizzato per una query se vengono soddisfatti tutti i requisiti seguenti:

- L'utente che esegue la query ha accesso a tutte le tabelle a cui viene fatto riferimento nella query.
- È presente una corrispondenza esatta tra la nuova query e la query precedente che ha generato la cache dei set di risultati.
- Non sono presenti dati o modifiche dello schema nelle tabelle da cui è stato generato il set di risultati memorizzato nella cache.

Eseguire questo comando per verificare se una query è stata eseguita con un riscontro nella cache dei risultati. La colonna result_set_cache restituisce 1 per l'hit della cache, 0 per i valori negativi della cache e negativi per motivi per cui la memorizzazione nella cache del set di risultati non è stata utilizzata. Controllare [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) per i dettagli.

```sql
SELECT request_id, command, result_set_cache FROM sys.dm_pdw_exec_requests
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>Gestire i risultati memorizzati nella cache

Le dimensioni massime della cache dei set di risultati sono pari a 1 TB per ogni database.  I risultati memorizzati nella cache vengono invalidati automaticamente quando cambiano i dati della query sottostante.  

Lo sfratto della cache viene gestito automaticamente da SQL Analytics seguendo questa pianificazione:The cache sviction is managed by SQL Analytics automatically following this schedule:

- Ogni 48 ore se il set di risultati non è stato utilizzato o è stato invalidato.
- Quando la cache del set di risultati si avvicina alla dimensione massima.

Gli utenti possono svuotare manualmente l'intera cache del set di risultati utilizzando una di queste opzioni:Users can manually empty the entire result set cache by using one of these options:

- Disattivare la funzionalità di memorizzazione nella cache dei set di risultati per il database
- Eseguire DBCC DROPRESULTSETCACHE mentre si è connessi al database

La sospensione di un database non svuota il set di risultati memorizzato nella cache.  

## <a name="next-steps"></a>Passaggi successivi

Per altri suggerimenti sullo sviluppo, vedere la [panoramica dello sviluppo](sql-data-warehouse-overview-develop.md).

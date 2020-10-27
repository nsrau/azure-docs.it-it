---
title: Ottimizzazione delle prestazioni con memorizzazione nella cache dei set di risultati
description: Panoramica della funzionalità di memorizzazione nella cache dei set di risultati per il pool Synapse SQL in Azure Synapse Analytics
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.custom: azure-synapse
ms.openlocfilehash: 933ec541e358f1839c1b4d24acd19e439ea26375
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92541282"
---
# <a name="performance-tuning-with-result-set-caching"></a>Ottimizzazione delle prestazioni con memorizzazione nella cache dei set di risultati

Quando è abilitata la memorizzazione nella cache dei set di risultati, Synapse SQL memorizza automaticamente i risultati delle query nel database dell'utente per l'uso ricorrente.  In questo modo, le esecuzioni di query successive ottengono risultati direttamente dalla cache permanente, rendendo superfluo il ricalcolo.   La memorizzazione nella cache dei set di risultati migliora le prestazioni delle query e riduce l'utilizzo delle risorse di calcolo.  Inoltre, le query che usano set di risultati memorizzati nella cache non usano slot di concorrenza e pertanto non vengono prese in considerazione per i limiti di concorrenza esistenti. Per motivi di sicurezza, gli utenti possono accedere solo ai risultati memorizzati nella cache se hanno le stesse autorizzazioni di accesso ai dati degli utenti che hanno creato i risultati memorizzati nella cache.  

## <a name="key-commands"></a>Comandi principali

[Attivare/disattivare la memorizzazione nella cache dei set di risultati per un database utente](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[Attivare/disattivare la memorizzazione nella cache dei set di risultati per una sessione](/sql/t-sql/statements/set-result-set-caching-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[Controllare le dimensioni del set di risultati memorizzato nella cache](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  

[Pulire la cache](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>Elementi non memorizzati nella cache  

Dopo l'attivazione della memorizzazione nella cache dei set di risultati per un database, i risultati vengono memorizzati nella cache per tutte le query fino al riempimento della cache, ad eccezione delle query seguenti:

- Query con funzioni predefinite o espressioni di runtime che non sono deterministiche anche quando non sono presenti modifiche nei dati o nella query delle tabelle di base. Ad esempio DateTime. Now (), GETDATE ().
- Query che usano funzioni definite dall'utente
- Query che usano tabelle con la sicurezza a livello di riga o a livello di colonna abilitata
- Query che restituiscono dati con dimensioni di riga maggiori di 64 KB
- Query che restituiscono dati di grandi dimensioni (maggiori di 10 GB) 
>[!NOTE]
> - Alcune funzioni non deterministiche e espressioni di runtime possono essere deterministiche per eseguire query ripetitive sugli stessi dati. Ad esempio, ROW_NUMBER ().  
> - Utilizzare ORDER BY nella query se l'ordine o la sequenza di righe nel set di risultati della query è importante per la logica dell'applicazione.
> - Se i dati nelle colonne ORDER BY non sono univoci, non esiste alcun ordine di riga garantito per le righe con gli stessi valori nelle colonne ORDER BY, indipendentemente dal fatto che la memorizzazione nella cache del set di risultati sia abilitata o disabilitata.

> [!IMPORTANT]
> Le operazioni per creare la cache dei set di risultati e recuperare i dati dalla cache vengono eseguite nel nodo di controllo di un'istanza del pool Synapse SQL.
> Quando la memorizzazione nella cache dei set di risultati è attivata, l'esecuzione di query che restituiscono set di risultati di grandi dimensioni, ad esempio maggiori di 1 GB, può causare una limitazione elevata della larghezza di banda nel nodo di controllo e rallentare nel complesso la risposta alle query nell'istanza.  Queste query vengono in genere usate durante l'esplorazione dei dati o le operazioni ETL (estrazione, trasformazione e caricamento). Per evitare il sovraccarico del nodo di controllo e la comparsa di problemi di prestazioni, gli utenti devono DISATTIVARE la memorizzazione nella cache del set di risultati nel database prima di eseguire query di questo tipo.  

Eseguire questa query per trovare il tempo impiegato dalle operazioni di memorizzazione nella cache dei set di risultati per una query:

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command
FROM sys.dm_pdw_request_steps
WHERE request_id  = <'request_id'>;
```

Ecco un esempio di output di una query eseguita con la memorizzazione nella cache dei set di risultati disabilitata.

![Screenshot mostra i risultati della query, inclusi il tipo e il comando del percorso.](./media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

Ecco un esempio di output di una query eseguita con la memorizzazione nella cache dei set di risultati abilitata.

![Screenshot mostra i risultati della query con il comando selezionato * da [D W ResultCache D b] punto d b o denominato out.](./media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>Quando vengono usati i risultati memorizzati nella cache

Il set di risultati memorizzato nella cache viene riutilizzato per una query se vengono soddisfatti tutti i requisiti seguenti:

- L'utente che esegue la query ha accesso a tutte le tabelle a cui viene fatto riferimento nella query.
- È presente una corrispondenza esatta tra la nuova query e la query precedente che ha generato la cache dei set di risultati.
- Non sono presenti dati o modifiche dello schema nelle tabelle da cui è stato generato il set di risultati memorizzato nella cache.

Eseguire questo comando per verificare se una query è stata eseguita con un riscontro nella cache dei risultati. La colonna result_cache_hit restituisce 1 per il riscontro nella cache, 0 per un mancato riscontro nella cache e valori negativi per i motivi per cui non è stata usata la memorizzazione nella cache del set di risultati. Per informazioni dettagliate, vedere [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

```sql
SELECT request_id, command, result_cache_hit FROM sys.dm_pdw_exec_requests
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>Gestire i risultati memorizzati nella cache

Le dimensioni massime della cache dei set di risultati sono pari a 1 TB per ogni database.  I risultati memorizzati nella cache vengono automaticamente invalidati quando i dati della query sottostante cambiano.  

L'eliminazione della cache viene gestita automaticamente da Synapse SQL seguendo questa pianificazione:

- Ogni 48 ore, se il set di risultati non è stato usato o è stato invalidato.
- Quando la cache dei set di risultati sta per raggiungere le dimensioni massime.

Per svuotare manualmente l'intera cache dei set di risultati, è possibile usare una di queste opzioni:

- Disattivare la funzionalità di memorizzazione nella cache dei set di risultati per il database
- Eseguire DBCC DROPRESULTSETCACHE mentre si è connessi al database

La sospensione di un database non svuoterà la cache dei set di risultati.  

## <a name="next-steps"></a>Passaggi successivi

Per altri suggerimenti sullo sviluppo, vedere la [panoramica dello sviluppo](sql-data-warehouse-overview-develop.md).

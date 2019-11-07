---
title: Ottimizzazione delle prestazioni con memorizzazione nella cache dei set di risultati
description: Panoramica della funzionalità di memorizzazione nella cache dei set di risultati per Azure SQL Data Warehouse
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.custom: seo-lt-2019
ms.openlocfilehash: 461320b9c3ed48176fb60fe695704c582edcd552
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692946"
---
# <a name="performance-tuning-with-result-set-caching"></a>Ottimizzazione delle prestazioni con memorizzazione nella cache dei set di risultati  
Quando è abilitata la memorizzazione nella cache del set di risultati, Azure SQL Data Warehouse memorizza automaticamente nella cache i risultati delle query nel database utente per l'utilizzo ripetitivo.  In questo modo, le esecuzioni di query successive ottengono risultati direttamente dalla cache permanente, quindi il ricalcolo non è necessario.   La memorizzazione nella cache del set di risultati migliora le prestazioni delle query e riduce l'utilizzo delle risorse di calcolo.  Inoltre, le query che utilizzano i set di risultati memorizzati nella cache non utilizzano slot di concorrenza e pertanto non vengono conteggiati rispetto ai limiti di concorrenza esistenti. Per la sicurezza, gli utenti possono accedere solo ai risultati memorizzati nella cache se hanno le stesse autorizzazioni di accesso ai dati degli utenti che creano i risultati memorizzati nella cache.  

## <a name="key-commands"></a>Comandi chiave
[Attivare/disattivare la memorizzazione nella cache del set di risultati per un database utente](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)

[Attivare/disattivare la memorizzazione nella cache del set di risultati per una sessione](https://docs.microsoft.com/sql/t-sql/statements/set-result-set-caching-transact-sql?view=azure-sqldw-latest)

[Controllare le dimensioni del set di risultati memorizzato nella cache](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?view=azure-sqldw-latest)  

[Pulire la cache](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>Elementi non memorizzati nella cache  

Una volta attivata la memorizzazione nella cache del set di risultati per un database, i risultati vengono memorizzati nella cache per tutte le query finché la cache non è piena, ad eccezione di queste query:
- Query che usano funzioni non deterministiche come DateTime. Now ()
- Query che usano funzioni definite dall'utente
- Query che utilizzano tabelle con sicurezza a livello di riga o a livello di colonna abilitata
- Query che restituiscono dati con dimensioni di riga maggiori di 64KB

> [!IMPORTANT]
> Le operazioni per creare la cache dei set di risultati e recuperare i dati dalla cache avvengono nel nodo di controllo di un'istanza di data warehouse. Quando la memorizzazione nella cache del set di risultati è attivata, l'esecuzione di query che restituiscono set di risultati di grandi dimensioni, ad esempio > 1 milione di righe, può causare un utilizzo elevato della CPU sul nodo di controllo e rallentare la risposta complessiva della query sull'istanza.  Queste query vengono usate comunemente durante l'esplorazione dei dati o le operazioni ETL. Per evitare il sovraccarico del nodo di controllo e causare problemi di prestazioni, gli utenti devono disattivare la memorizzazione nella cache del set di risultati nel database prima di eseguire questi tipi di query.  

Eseguire questa query per il tempo impiegato dalle operazioni di memorizzazione nella cache dei set di risultati per una query:

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command 
FROM sys.dm_pdw_request_steps 
WHERE request_id  = <'request_id'>; 
```

Di seguito è riportato un esempio di output per una query eseguita con la memorizzazione nella cache dei set di risultati disabilitata.

![Query-passaggi-con-RSC-disabilitato](media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

Di seguito è riportato un esempio di output per una query eseguita con la memorizzazione nella cache dei set di risultati abilitata.

![Query-passaggi-con-RSC-abilitato](media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>Quando vengono usati i risultati memorizzati nella cache

Il set di risultati memorizzato nella cache viene riutilizzato per una query se tutti i requisiti seguenti sono soddisfatti:
- L'utente che esegue la query ha accesso a tutte le tabelle a cui viene fatto riferimento nella query.
- Esiste una corrispondenza esatta tra la nuova query e la query precedente che ha generato la cache del set di risultati.
- Non sono presenti dati o modifiche dello schema nelle tabelle in cui è stato generato il set di risultati memorizzato nella cache.

Eseguire questo comando per verificare se una query è stata eseguita con un riscontro nella cache dei risultati. Se si verifica un riscontro nella cache, result_cache_hit restituirà 1.

```sql
SELECT request_id, command, result_cache_hit FROM sys.dm_pdw_exec_requests 
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>Gestire i risultati memorizzati nella cache 

Le dimensioni massime della cache del set di risultati sono pari a 1 TB per database.  I risultati memorizzati nella cache vengono automaticamente invalidati quando i dati della query sottostante cambiano.  

La rimozione della cache viene gestita da Azure SQL Data Warehouse automaticamente dopo questa pianificazione: 
- Ogni 48 ore se il set di risultati non è stato usato o è stato invalidato. 
- Quando la cache del set di risultati si avvicina alla dimensione massima.

Gli utenti possono svuotare manualmente l'intera cache dei set di risultati utilizzando una di queste opzioni: 
- Disattivare la funzionalità cache del set di risultati per il database 
- Eseguire DBCC DROPRESULTSETCACHE durante la connessione al database

Se si sospende un database, il set di risultati memorizzato nella cache non verrà vuoto.  

## <a name="next-steps"></a>Passaggi successivi
Per altri suggerimenti sullo sviluppo, vedere [Panoramica sullo sviluppo per SQL Data Warehouse](sql-data-warehouse-overview-develop.md). 

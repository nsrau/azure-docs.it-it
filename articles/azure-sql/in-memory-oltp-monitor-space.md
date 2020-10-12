---
title: Monitorare l'archiviazione in memoria di XTP
description: Stimare e monitorare l'uso e la capacità dell'archiviazione in memoria XTP e risolvere l'errore di capacità 41823
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: juliemsft
ms.author: jrasnick
ms.reviewer: genemi
ms.date: 01/25/2019
ms.openlocfilehash: 2134cf1fda5f0f1699feb46582813d198304f92e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91616381"
---
# <a name="monitor-in-memory-oltp-storage-in-azure-sql-database-and-azure-sql-managed-instance"></a>Monitorare In-Memory archiviazione OLTP nel database SQL di Azure e in Azure SQL Istanza gestita
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Quando si usa [OLTP in memoria](in-memory-oltp-overview.md), i dati nelle tabelle ottimizzate per la memoria e le variabili di tabella si trovano nell'archiviazione OLTP in memoria.

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>Determinare se i dati rientrano nel limite di archiviazione OLTP in memoria

Determinare i limiti di archiviazione dei diversi livelli di servizio. Ogni livello di servizio Premium e Business Critical ha dimensioni massime di archiviazione OLTP in memoria.

- [Limiti delle risorse basate su DTU-database singolo](database/resource-limits-dtu-single-databases.md)
- [Limiti delle risorse basate su DTU-pool elastici](database/resource-limits-dtu-elastic-pools.md)
- [Limiti delle risorse basate su vCore - database singoli](database/resource-limits-vcore-single-databases.md)
- [Limiti delle risorse basate su vCore - pool elastici](database/resource-limits-vcore-elastic-pools.md)
- [limiti delle risorse basate su vCore-istanza gestita](managed-instance/resource-limits.md)

La stima dei requisiti di memoria per una tabella ottimizzata per la memoria funziona allo stesso modo per SQL Server come nel database SQL di Azure e in Azure SQL Istanza gestita. Sono necessari alcuni minuti per esaminare i [requisiti di memoria](/sql/relational-databases/in-memory-oltp/estimate-memory-requirements-for-memory-optimized-tables)per la stima.

Le righe di tabella, le righe di variabile di tabella e gli indici vengono inclusi nel calcolo delle dimensioni massime dei dati utente. ALTER TABLE, inoltre, necessita di spazio sufficiente per creare una nuova versione dell'intera tabella e dei relativi indici.

Una volta superato questo limite, le operazioni di inserimento e aggiornamento potrebbero non riuscire con l'errore 41823 per i database singoli nel database SQL di Azure e i database in Azure SQL Istanza gestita e l'errore 41840 per i pool elastici nel database SQL di Azure. A questo punto è necessario eliminare dati per recuperare memoria oppure aggiornare il livello di servizio o le dimensioni di calcolo del database.

## <a name="monitoring-and-alerting"></a>Monitoraggio e avviso

È possibile monitorare l'uso dell'archiviazione in memoria sotto forma di percentuale del limite di archiviazione per le dimensioni di calcolo nel [portale di Azure](https://portal.azure.com/):

1. Nel pannello Database individuare la casella Utilizzo risorse e fare clic su Modifica.
2. Selezionare la metrica `In-Memory OLTP Storage percentage`.
3. Per aggiungere un avviso, selezionare la casella Utilizzo risorse per aprire il pannello Metrica, quindi fare clic su Aggiungi avviso.

In alternativa, usare la query seguente per visualizzare l'utilizzo dell'archiviazione in memoria:

```sql
    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats
```

## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>Correggere situazioni di archiviazione OLTP non in memoria - Errori 41823 e 41840

Se si raggiunge il limite di archiviazione OLTP in memoria nel database, le operazioni INSERT, UPDATE, ALTER e CREATE hanno esito negativo con un messaggio di errore 41823 (per i database singoli) o con un errore 41840 (per i pool elastici). Entrambi gli errori comportano l'interruzione della transazione attiva.

I messaggi di errore 41823 e 41840 indicano che le tabelle e le variabili di tabella del database o del pool ottimizzate per la memoria hanno raggiunto la dimensione massima di archiviazione OLTP in memoria.

Per risolvere l'errore:

- Eliminare i dati dalle tabelle ottimizzate per la memoria, eseguendo potenzialmente l'offload dei dati in tabelle tradizionali basate su disco oppure
- Aggiornare il livello di servizio selezionandone uno con risorse di archiviazione in memoria sufficienti per i dati da mantenere nelle tabelle ottimizzate per la memoria.

> [!NOTE]
> In rari casi, gli errori 41823 e 41840 possono essere temporanei, con il risultato che lo spazio di archiviazione OLTP in memoria si rivela sufficiente ed è possibile eseguire nuovamente l'operazione con esito positivo. Pertanto, quando si verifica l'errore 41823 o 41840 per la prima volta, è consigliabile monitorare lo spazio complessivo di archiviazione OLTP in memoria e provare a eseguire nuovamente l'operazione. Per altre informazioni sulla logica di ripetizione dei tentativi, vedere [Rilevamento dei conflitti e logica di ripetizione dei tentativi](https://docs.microsoft.com/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni aggiuntive sul monitoraggio, vedere [monitoraggio tramite le viste a gestione dinamica](database/monitoring-with-dmvs.md).

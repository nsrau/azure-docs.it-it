---
title: Monitorare l'archiviazione in memoria XTP | Microsoft Docs
description: "Stimare e monitorare l'uso e la capacità delle risorse di archiviazione in memoria XTP e risolvere l'errore di capacità 41823"
services: sql-database
documentationcenter: 
author: jodebrui
manager: jhubbard
editor: 
ms.assetid: b617308e-692c-4938-8fa2-070034a3ecef
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: jodebrui
ms.openlocfilehash: 1e7088e80cc86e3c7cf8ae8ea180d797de613e71
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2018
---
# <a name="monitor-in-memory-oltp-storage"></a>Monitorare l'archiviazione OLTP in memoria
Quando si usa [OLTP in memoria](sql-database-in-memory.md), i dati nelle tabelle ottimizzate per la memoria e le variabili di tabella si trovano nell'archiviazione OLTP in memoria. Ogni livello di servizio Premium ha dimensioni massime di archiviazione OLTP in memoria documentate nell'articolo sui [limiti delle risorse del database singolo](sql-database-resource-limits.md#single-database-storage-sizes-and-performance-levels) e sui [limiti delle risorse del pool elastico](sql-database-resource-limits.md#elastic-pool-change-storage-size). Dopo il superamento di questo limite, è possibile che le operazioni di inserimento e aggiornamento abbiano esito negativo con errore 41823 per i database autonomi ed errore 41840 per i pool elastici. A questo punto è necessario eliminare dati per recuperare memoria oppure aggiornare il livello di prestazioni del database.

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>Determinare se i dati rientrano nel limite di archiviazione OLTP in memoria
Determinare i limiti di archiviazione dei diversi livelli di servizio Premium. Vedere le informazioni sui [limiti delle risorse del database singolo](sql-database-resource-limits.md#single-database-storage-sizes-and-performance-levels) e sui [limiti delle risorse del pool elastico](sql-database-resource-limits.md#elastic-pool-change-storage-size).

La stima dei requisiti di memoria per una tabella ottimizzata per la memoria in SQL Server è analoga alla stima eseguita nel database SQL di Azure. È consigliabile rivedere l'articolo corrispondente in [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

Le righe di tabella, le righe di variabile di tabella e gli indici vengono inclusi nel calcolo delle dimensioni massime dei dati utente. ALTER TABLE, inoltre, necessita di spazio sufficiente per creare una nuova versione dell'intera tabella e dei relativi indici.

## <a name="monitoring-and-alerting"></a>Monitoraggio e avviso
È possibile monitorare l'uso dell'archiviazione in memoria come percentuale del limite di archiviazione per il livello di prestazioni nel [portale di Azure](https://portal.azure.com/): 

1. Nel pannello Database individuare la casella Utilizzo risorse e fare clic su Modifica.
2. Selezionare la metrica `In-Memory OLTP Storage percentage`.
3. Per aggiungere un avviso, selezionare la casella Utilizzo risorse per aprire il pannello Metrica, quindi fare clic su Aggiungi avviso.

In alternativa, usare la query seguente per visualizzare l'utilizzo delle risorse di archiviazione in memoria:

    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats


## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>Correggere situazioni di archiviazione OLTP non in memoria - Errori 41823 e 41840
Se si raggiunge il limite di archiviazione OLTP in memoria nel database, le operazioni INSERT, UPDATE, ALTER e CREATE hanno esito negativo con messaggi di errore 41823 (per i database autonomi) o 41840 (per i pool elastici). Entrambi gli errori comportano l'interruzione della transazione attiva.

I messaggi di errore 41823 e 41840 indicano che le tabelle e le variabili di tabella del database o del pool ottimizzate per la memoria hanno raggiunto la dimensione massima di archiviazione OLTP in memoria.

Per risolvere l'errore:

* Eliminare i dati dalle tabelle ottimizzate per la memoria, eseguendo potenzialmente l'offload dei dati in tabelle tradizionali basate su disco oppure
* Aggiornare il livello di servizio selezionandone uno con risorse di archiviazione in memoria sufficienti per i dati da mantenere nelle tabelle ottimizzate per la memoria.

> [!NOTE] 
> In rari casi, gli errori 41823 e 41840 possono essere temporanei, con il risultato che lo spazio di archiviazione OLTP in memoria si rivela sufficiente ed è possibile eseguire nuovamente l'operazione con esito positivo. Pertanto, quando si verifica l'errore 41823 o 41840 per la prima volta, è consigliabile monitorare lo spazio complessivo di archiviazione OLTP in memoria e provare a eseguire nuovamente l'operazione. Per altre informazioni sulla logica di ripetizione dei tentativi, vedere [Rilevamento dei conflitti e logica di ripetizione dei tentativi](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic).

## <a name="next-steps"></a>Passaggi successivi
Per le linee guida sul monitoraggio, vedere [Monitoraggio del database SQL di Azure tramite le visualizzazioni di gestione dinamica](sql-database-monitoring-with-dmvs.md).

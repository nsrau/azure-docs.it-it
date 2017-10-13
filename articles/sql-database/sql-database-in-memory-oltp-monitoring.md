---
title: Monitorare l'archiviazione in memoria XTP | Documentazione Microsoft
description: "Stimare e monitorare l'uso e la capacità delle risorse di archiviazione in memoria XTP e risolvere l'errore di capacità 41823"
services: sql-database
documentationcenter: 
author: jodebrui
manager: jhubbard
editor: 
ms.assetid: b617308e-692c-4938-8fa2-070034a3ecef
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jodebrui
ms.openlocfilehash: e953b60493c5a7c7a7ad74533471bd321d42abef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-in-memory-oltp-storage"></a>Monitorare l'archiviazione OLTP in memoria
Quando si usa [OLTP in memoria](sql-database-in-memory.md), i dati nelle tabelle con ottimizzazione per la memoria e le variabili di tabella si trovano nell'archiviazione OLTP in memoria. Ogni livello di servizio Premium ha dimensioni massime di archiviazione OLTP in memoria documentate nell'articolo sui [limiti delle risorse del database singolo](sql-database-resource-limits.md#single-database-storage-sizes-and-performance-levels) e sui [limiti delle risorse del pool elastico](sql-database-resource-limits.md#elastic-pool-change-storage-size). Dopo il superamento di questo limite, è possibile che le operazioni di inserimento e aggiornamento abbiano esito negativo con errore 41823. Sarà quindi necessario eliminare dati per recuperare memoria oppure aggiornare il livello di prestazioni del database.

## <a name="determine-whether-data-will-fit-within-the-in-memory-storage-cap"></a>Determinare se i dati rientreranno nel limite di archiviazione in memoria
Determinare i limiti di archiviazione dei diversi livelli di servizio Premium. Vedere le informazioni sui [limiti delle risorse del database singolo](sql-database-resource-limits.md#single-database-storage-sizes-and-performance-levels) e sui [limiti delle risorse del pool elastico](sql-database-resource-limits.md#elastic-pool-change-storage-size).

La stima dei requisiti di memoria per una tabella con ottimizzazione per la memoria in SQL Server è analoga alla stima eseguita nel database SQL di Azure. È consigliabile rivedere l'argomento corrispondente su [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

Si noti che la tabella e le righe di variabili tabella, oltre agli indici, vengono incluse nel calcolo delle dimensioni massime dei dati utente. ALTER TABLE, inoltre, necessita di spazio sufficiente per creare una nuova versione dell'intera tabella e dei relativi indici.

## <a name="monitoring-and-alerting"></a>Monitoraggio e avviso
È possibile monitorare l'uso dell'archiviazione in memoria come percentuale del limite di archiviazione per il livello di prestazioni nel [portale di Azure](https://portal.azure.com/): 

1. Nel pannello Database individuare la casella Utilizzo risorse e fare clic su Modifica.
2. Selezionare la metrica `In-Memory OLTP Storage percentage`.
3. Per aggiungere un avviso, selezionare la casella Utilizzo risorse per aprire il pannello Metrica, quindi fare clic su Aggiungi avviso.

In alternativa, usare la query seguente per visualizzare l'utilizzo delle risorse di archiviazione in memoria:

    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats


## <a name="correct-out-of-memory-situations---error-41823"></a>Correggere le situazioni di memoria insufficiente - Errore 41823
Se la memoria è insufficiente, le operazioni INSERISCI, AGGIORNA e CREA avranno esito negativo con messaggio di errore 41823.

Il messaggio di errore 41823 indica che le tabelle con ottimizzazione per la memoria e le variabili tabella hanno superato le dimensioni massime.

Per risolvere l'errore:

* Eliminare i dati dalle tabelle con ottimizzazione per la memoria, eseguendo potenzialmente l'offload dei dati in tabelle tradizionali basate su disco oppure
* Aggiornare il livello di servizio selezionandone uno con risorse di archiviazione in memoria sufficienti per i dati da mantenere nelle tabelle con ottimizzazione con memoria.

## <a name="next-steps"></a>Passaggi successivi
Per le linee guida sul monitoraggio, vedere [Monitoraggio del database SQL di Azure tramite le visualizzazioni di gestione dinamica](sql-database-monitoring-with-dmvs.md).

---
title: Monitorare l&quot;archiviazione in memoria XTP | Documentazione Microsoft
description: "Stimare e monitorare l&quot;uso e la capacità delle risorse di archiviazione in memoria XTP e risolvere l&quot;errore di capacità 41823"
services: sql-database
documentationcenter: 
author: jodebrui
manager: jhubbard
editor: 
ms.assetid: b617308e-692c-4938-8fa2-070034a3ecef
ms.service: sql-database
ms.custom: monitor and tune
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: jodebrui
translationtype: Human Translation
ms.sourcegitcommit: 09c2332589b1170b411c6f45f4109fb8048887e2
ms.openlocfilehash: 86b2358b045bd5bc0c9f4fc2674efd03b098212f


---
# <a name="monitor-in-memory-oltp-storage"></a>Monitorare l'archiviazione OLTP in memoria
Quando si usa [OLTP in memoria](sql-database-in-memory.md), i dati nelle tabelle con ottimizzazione per la memoria e le variabili di tabella si trovano nell'archiviazione OLTP in memoria. Ogni livello di servizio Premium ha dimensioni di archiviazione OLTP in memoria massime documentate nell'articolo [Livelli di servizio del database SQL](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels). Dopo il superamento di questo limite, è possibile che le operazioni di inserimento e aggiornamento abbiano esito negativo con errore 41823. Sarà quindi necessario eliminare dati per recuperare memoria oppure aggiornare il livello di prestazioni del database.

## <a name="determine-whether-data-will-fit-within-the-in-memory-storage-cap"></a>Determinare se i dati rientreranno nel limite di archiviazione in memoria
Determinare il limite di archiviazione: consultare l'articolo [Livelli di servizio del database SQL](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels) per informazioni sui limiti di archiviazione dei diversi livelli di servizio Premium.

La stima dei requisiti di memoria per una tabella con ottimizzazione per la memoria in SQL Server è analoga alla stima eseguita nel database SQL di Azure. È consigliabile rivedere l'argomento corrispondente su [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

Si noti che la tabella e le righe di variabili tabella, oltre agli indici, vengono incluse nel calcolo delle dimensioni massime dei dati utente. ALTER TABLE, inoltre, necessita di spazio sufficiente per creare una nuova versione dell'intera tabella e dei relativi indici.

## <a name="monitoring-and-alerting"></a>Monitoraggio e avviso
È possibile monitorare l'uso dell'archiviazione in memoria come percentuale del [limite di archiviazione per il livello di prestazioni](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels) nel [portale](https://portal.azure.com/) di Azure: 

* Nel pannello Database individuare la casella Utilizzo risorse e fare clic su Modifica.
* Quindi selezionare la metrica `In-Memory OLTP Storage percentage`.
* Per aggiungere un avviso, selezionare la casella Utilizzo risorse per aprire il pannello Metrica, quindi fare clic su Aggiungi avviso.

In alternativa, usare la query seguente per visualizzare l'utilizzo delle risorse di archiviazione in memoria:

    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats


## <a name="correct-out-of-memory-situations---error-41823"></a>Correggere le situazioni di memoria insufficiente - Errore 41823
Se la memoria è insufficiente, le operazioni INSERISCI, AGGIORNA e CREA avranno esito negativo con messaggio di errore 41823.

Il messaggio di errore 41823 indica che le tabelle con ottimizzazione per la memoria e le variabili tabella hanno superato le dimensioni massime.

Per risolvere l'errore:

* Eliminare i dati dalle tabelle con ottimizzazione per la memoria, eseguendo potenzialmente l'offload dei dati in tabelle tradizionali basate su disco oppure
* Aggiornare il livello di servizio selezionandone uno con risorse di archiviazione in memoria sufficienti per i dati da mantenere nelle tabelle con ottimizzazione con memoria.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sul [Monitoraggio del database SQL di Azure tramite le visualizzazioni di gestione dinamica](sql-database-monitoring-with-dmvs.md)



<!--HONumber=Dec16_HO1-->



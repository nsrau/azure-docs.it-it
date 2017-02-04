---
title: Suggerimenti sull&quot;ottimizzazione delle prestazioni del database SQL | Documentazione Microsoft
description: Suggerimenti per l&quot;ottimizzazione delle prestazioni nel database SQL di Azure tramite valutazione e miglioramento.
services: sql-database
documentationcenter: 
author: v-shysun
manager: felixwu
editor: 
keywords: ottimizzazione delle prestazioni di sql, ottimizzazione delle prestazioni del database, suggerimenti di ottimizzazione delle prestazioni di sql ottimizzazione delle prestazioni del database sql
ms.assetid: eb7b3f66-3b33-4e1b-84fb-424a928a6672
ms.service: sql-database
ms.custom: monitor and tune
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2016
ms.author: v-shysun
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: 298c5289a9e58127de208c26fd8bbbbdfd715024


---
# <a name="sql-database-performance-tuning-tips"></a>Suggerimenti sull'ottimizzazione delle prestazioni del database SQL
È possibile modificare il [livello di servizio](sql-database-service-tiers.md) di un database autonomo o aumentare il numero di eDTU del pool elastico in qualsiasi momento per migliorare le prestazioni, ma è consigliabile prima di tutto identificare le opportunità per migliorare e ottimizzare le prestazioni delle query. Spesso le prestazioni non ottimali del database sono dovute a indici mancanti e query non ottimizzate correttamente. Questo articolo fornisce indicazioni per l'ottimizzazione delle prestazioni nel database SQL.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="steps-to-evaluate-and-tune-database-performance"></a>Passaggi per valutare e ottimizzare le prestazioni del database
1. Nel [portale di Azure](https://portal.azure.com)fare clic su **Database SQL**, selezionare il database e quindi usare il grafico di monitoraggio per identificare le risorse che stanno per raggiungere i valori massimi. L'utilizzo di DTU viene visualizzato per impostazione predefinita. Fare clic su **Modifica** per modificare l'intervallo di tempo e i valori indicati.
2. Usare [Informazioni dettagliate prestazioni query](sql-database-query-performance.md) per valutare le query con le DTU e quindi usare [Advisor per database SQL](sql-database-advisor.md) per visualizzare raccomandazioni relative alla creazione e all'eliminazione di indici, alla parametrizzazione di query e alla correzione di problemi di schema.
3. È possibile usare le viste a gestione dinamica (DMV), gli eventi estesi (XEvent) e l'archivio di query in SSMS per ottenere i parametri delle prestazioni in tempo reale. Per suggerimenti dettagliati su ottimizzazione e monitoraggio, vedere l' [argomento con le linee guida sulle prestazioni](sql-database-performance-guidance.md) .

> [!IMPORTANT] 
> È consigliabile usare sempre la versione più aggiornata di Management Studio per restare sincronizzati con gli aggiornamenti di Microsoft Azure e del database SQL. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
>

## <a name="steps-to-improve-database-performance-with-more-resources"></a>Passaggi per migliorare le prestazioni del database con più risorse
1. Per i database autonomi, è possibile [modificare i livelli di servizio](sql-database-scale-up.md) su richiesta per migliorare le prestazioni del database.
2. Per più database, è consigliabile usare [pool elastici](sql-database-elastic-pool-guidance.md) per la scalabilità automatica delle risorse.




<!--HONumber=Dec16_HO2-->



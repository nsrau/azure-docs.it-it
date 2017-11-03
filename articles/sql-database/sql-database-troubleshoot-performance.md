---
title: Monitoraggio e ottimizzazione delle prestazioni nel database SQL di Azure | Microsoft Docs
description: Suggerimenti per l'ottimizzazione delle prestazioni nel database SQL di Azure tramite valutazione e miglioramento.
services: sql-database
documentationcenter: 
author: v-shysun
manager: felixwu
editor: 
keywords: ottimizzazione delle prestazioni di sql, ottimizzazione delle prestazioni del database, suggerimenti di ottimizzazione delle prestazioni di sql ottimizzazione delle prestazioni del database sql
ms.assetid: eb7b3f66-3b33-4e1b-84fb-424a928a6672
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/13/2017
ms.author: v-shysun
ms.openlocfilehash: 4eb344bf9e87ef1a8f469268211588e51696164e
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2017
---
# <a name="monitoring-and-performance-tuning"></a>Monitoraggio e ottimizzazione delle prestazioni

Database SQL di Azure è gestito automaticamente ed è un servizio di dati flessibile che consente di monitorare l'utilizzo, aggiungere o rimuovere facilmente risorse (CPU, memoria, io), trovare indicazioni per migliorare le prestazioni del database o lasciare che il database si adatti al carico di lavoro e ottimizzi automaticamente le prestazioni.

Questo articolo offre una panoramica delle opzioni di monitoraggio e di ottimizzazione delle prestazioni disponibili nel database SQL di Azure.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="monitoring-and-troubleshooting-database-performance"></a>Monitoraggio e risoluzione dei problemi relativi alle prestazioni del database

Database SQL di Azure consente di monitorare facilmente l'utilizzo del database e di identificare le query che potrebbero causare problemi di prestazioni. È possibile monitorare le prestazioni del database usando le visualizzazioni di sistema o il portale di Azure. Per il monitoraggio e la risoluzione dei problemi di prestazioni del database sono disponibili le opzioni seguenti:

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Database SQL**, selezionare il database e quindi usare il grafico di monitoraggio per identificare le risorse che stanno per raggiungere i valori massimi. L'utilizzo di DTU viene visualizzato per impostazione predefinita. Fare clic su **Modifica** per modificare l'intervallo di tempo e i valori indicati.
2. Usare [Informazioni dettagliate prestazioni query](sql-database-query-performance.md) per identificare le query che consumano la maggior parte delle risorse.
3. È possibile usare le viste a gestione dinamica (DMV), gli eventi estesi (`XEvents`) e l'archivio di query in SSMS per ottenere i parametri delle prestazioni in tempo reale.

Vedere l'[argomento sulle indicazioni relative alle prestazioni](sql-database-performance-guidance.md) per trovare le tecniche che è possibile usare per migliorare le prestazioni del database SQL di Azure, se si individua un problema durante l'utilizzo di questi report o queste viste.

> [!IMPORTANT] 
> È consigliabile usare sempre la versione più aggiornata di Management Studio per restare sincronizzati con gli aggiornamenti di Microsoft Azure e del database SQL. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
>

## <a name="optimize-database-to-improve-performance"></a>Ottimizzare il database per migliorare le prestazioni

Database SQL di Azure consente di individuare le opportunità per migliorare e ottimizzare le prestazioni delle query senza modificare le risorse esaminando le [indicazioni per ottimizzare le prestazioni](sql-database-advisor.md). Spesso le prestazioni non ottimali del database sono dovute a indici mancanti e query non ottimizzate correttamente. È possibile applicare queste indicazioni per migliorare le prestazioni del carico di lavoro.
È anche possibile impostare il database SQL di Azure in modo che [ottimizzi automaticamente le prestazioni delle query](sql-database-automatic-tuning.md) applicando tutte le indicazioni identificate e verificando l'effettivo miglioramento delle prestazioni del database. È possibile usare le opzioni seguenti per migliorare le prestazioni del database:

1. Usare [Advisor per database SQL ](sql-database-advisor-portal.md) per visualizzare le indicazioni per creare e rimuovere indici, parametrizzare le query e correggere i problemi di schema.
2. [Abilitare l'ottimizzazione automatica](sql-database-automatic-tuning-enable.md) e consentire al database SQL di Azure di correggere automaticamente i problemi di prestazioni identificati.

## <a name="improving-database-performance-with-more-resources"></a>Miglioramento delle prestazioni del database con più risorse

Se alla fine non esistono elementi eseguibili che possono migliorare le prestazioni del database, è possibile modificare la quantità di risorse disponibili nel database SQL di Azure. È possibile assegnare più risorse modificando il [livello di servizio](sql-database-service-tiers.md) di un database autonomo o aumentare il numero di eDTU di un pool elastico in qualsiasi momento.
1. Per i database autonomi, è possibile [modificare i livelli di servizio](sql-database-service-tiers.md) su richiesta per migliorare le prestazioni del database.
2. Per più database, è consigliabile usare [pool elastici](sql-database-elastic-pool-guidance.md) per la scalabilità automatica delle risorse.

## <a name="tune-and-refactor-application-or-database-code"></a>Ottimizzare ed eseguire il refactoring del codice del database o dell'applicazione

È possibile modificare il codice dell'applicazione per usare il database in modo più ottimale, modificare gli indici, forzare i piani o usare i suggerimenti per adattare manualmente il database al carico di lavoro. Alcuni suggerimenti e indicazioni per l'ottimizzazione manuale e la riscrittura del codice sono disponibili nell'[argomento relativo alle indicazioni sulle prestazioni](sql-database-performance-guidance.md).


## <a name="next-steps"></a>Passaggi successivi

- Per abilitare l'ottimizzazione automatica nel database SQL di Azure e delegare la gestione completa del carico di lavoro alla funzionalità di ottimizzazione automatica, vedere [Abilitare l'ottimizzazione automatica](sql-database-automatic-tuning-enable.md).
- Per usare l'ottimizzazione manuale, è possibile esaminare le [raccomandazioni per l'ottimizzazione nel portale di Azure](sql-database-advisor-portal.md) e applicare manualmente quelle che consentono di migliorare le prestazioni delle query.
- Modificare le risorse disponibili nel database modificando i [livelli di servizio Database SQL di Azure](sql-database-performance-guidance.md)

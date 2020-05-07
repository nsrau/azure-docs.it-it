---
title: Raccomandazioni per sinapsi SQL
description: Informazioni sulle raccomandazioni di sinapsi SQL e sul modo in cui vengono generate
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/30/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 546945d70554adbb28f19a3153faa67495e55f04
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82607751"
---
# <a name="synapse-sql-recommendations"></a>Raccomandazioni per sinapsi SQL

Questo articolo descrive i consigli di sinapsi SQL serviti Azure Advisor.  

Analisi SQL fornisce consigli per garantire che il carico di lavoro data warehouse sia costantemente ottimizzato per le prestazioni. Le raccomandazioni sono strettamente integrate con [Azure Advisor](../../advisor/advisor-performance-recommendations.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) per fornire le procedure consigliate direttamente nel [portale di Azure](https://aka.ms/Azureadvisor). SQL Analytics raccoglie i dati di telemetria e i consigli relativi alle superfici per il carico di lavoro attivo su cadenza giornaliera. Gli scenari di raccomandazione supportati sono descritti di seguito insieme a come applicare le azioni consigliate.

Puoi [controllare subito le tue raccomandazioni](https://aka.ms/Azureadvisor) . 

## <a name="data-skew"></a>Asimmetria dati

Durante l'esecuzione del carico di lavoro, l'asimmetria dei dati può causare uno spostamento dati aggiuntivo o colli di bottiglia nelle risorse. La documentazione seguente descrive come identificare le asimmetrie dei dati e come evitarle selezionando una chiave di distribuzione ottimale.

- [Identificare e rimuovere un'asimmetria](sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice)

## <a name="no-or-outdated-statistics"></a>Statistiche No o obsolete

La presenza di statistiche non ottimali può influire gravemente sulle prestazioni delle query in quanto può causare la generazione di piani di query non ottimali da parte di SQL Query Optimizer. La documentazione seguente descrive le procedure consigliate per la creazione e l'aggiornamento delle statistiche:

- [Creazione e aggiornamento delle statistiche delle tabelle](sql-data-warehouse-tables-statistics.md)

Per visualizzare l'elenco delle tabelle interessate da questi consigli, eseguire lo [script T-SQL](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables)seguente. Advisor esegue in modo continuo lo stesso script T-SQL per generare tali consigli.

## <a name="replicate-tables"></a>Replicare tabelle

Per consigli sulla tabella replicata, Advisor rileva candidati di tabella basati sulle caratteristiche fisiche seguenti:

- Dimensione tabella replicata
- Numero di colonne
- Tipo di distribuzione della tabella
- Numero di partizioni

Advisor sfrutta in modo continuativo l'euristica basata sul carico di lavoro, ad esempio frequenza di accesso alla tabella, righe restituite in media e soglie sulle dimensioni e l'attività dei data warehouse, per verificare che vengano generati consigli di alta qualità.

Nella sezione seguente viene descritta l'euristica basata sul carico di lavoro che è possibile trovare nella portale di Azure per ogni raccomandazione della tabella replicata:

- Media analisi: percentuale media di righe restituite dalla tabella per ogni accesso alla tabella negli ultimi sette giorni.
- Lettura frequente, nessun aggiornamento: indica che la tabella non è stata aggiornata negli ultimi sette giorni durante la visualizzazione dell'attività di accesso.
- Percentuale di lettura/aggiornamento: la percentuale della frequenza di accesso alla tabella rispetto all'aggiornamento negli ultimi sette giorni.
- Attività: misura l'utilizzo in base all'attività di accesso. Questa attività confronta l'attività di accesso alla tabella relativa all'attività di accesso alla tabella media tra le data warehouse negli ultimi sette giorni.

Attualmente Advisor visualizza al massimo quattro candidati di tabella replicata alla volta con indici columnstore cluster con priorità per l'attività più elevata.

> [!IMPORTANT]
> I consigli relativi alla tabella replicata non costituiscono una prova completa e non prendono in considerazione operazioni di spostamento dei dati. Microsoft sta lavorando per l'aggiunta di questa opzione come elemento euristico, ma nel frattempo è sempre opportuno convalidare il carico di lavoro dopo aver applicato il consiglio. Per altre informazioni sulle tabelle replicate, consultare la [documentazione](design-guidance-for-replicated-tables.md#what-is-a-replicated-table) seguente.


## <a name="adaptive-gen2-cache-utilization"></a>Utilizzo della cache adattivo (Gen2)
Quando si dispone di un working set di grandi dimensioni, è possibile riscontrare una percentuale bassa di riscontri nella cache e un utilizzo elevato della cache. Per questo scenario, è consigliabile aumentare le prestazioni per aumentare la capacità della cache ed eseguire di nuovo il carico di lavoro. Per ulteriori informazioni, vedere la [documentazione](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-how-to-monitor-cache)seguente. 

## <a name="tempdb-contention"></a>Contesa di tempdb

Le prestazioni delle query possono peggiorare quando si verifica una contesa di tempdb elevata.  La contesa di tempdb può essere eseguita tramite tabelle temporanee definite dall'utente o quando è presente una grande quantità di spostamento dei dati. Per questo scenario, è possibile ridimensionare l'allocazione di tempdb e [configurare le classi di risorse e la gestione del carico di lavoro](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-management) per fornire una maggiore quantità di memoria alle query. 

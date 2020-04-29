---
title: Raccomandazioni per sinapsi SQL
description: Informazioni sulle raccomandazioni di sinapsi SQL e sul modo in cui vengono generate
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/05/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 17877a1ef5d949fbbee080b6157844ac5b516fe7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80633675"
---
# <a name="synapse-sql-recommendations"></a>Raccomandazioni per sinapsi SQL

Questo articolo descrive i consigli di sinapsi SQL serviti Azure Advisor.  

Analisi SQL fornisce consigli per garantire che il carico di lavoro data warehouse sia costantemente ottimizzato per le prestazioni. Le raccomandazioni sono strettamente integrate con [Azure Advisor](../../advisor/advisor-performance-recommendations.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) per fornire le procedure consigliate direttamente nel [portale di Azure](https://aka.ms/Azureadvisor). SQL Analytics raccoglie i dati di telemetria e i consigli relativi alle superfici per il carico di lavoro attivo su cadenza giornaliera. Gli scenari di raccomandazione supportati sono descritti di seguito insieme a come applicare le azioni consigliate.

Puoi [controllare subito le tue raccomandazioni](https://aka.ms/Azureadvisor) . Questa funzionalità è attualmente applicabile solo ai data warehouse di seconda generazione.

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

Di seguito vengono descritti l'euristica basata sul carico di lavoro che è possibile trovare nel portale di Azure per ogni consiglio relativo alla tabella replicata:

- Media analisi: percentuale media di righe restituite dalla tabella per ogni accesso alla tabella negli ultimi sette giorni.
- Lettura frequente, nessun aggiornamento: indica che la tabella non è stata aggiornata negli ultimi sette giorni durante la visualizzazione dell'attività di accesso.
- Percentuale di lettura/aggiornamento: la percentuale della frequenza di accesso alla tabella rispetto all'aggiornamento negli ultimi sette giorni.
- Attività: misura l'utilizzo in base all'attività di accesso. Viene confrontata l'attività di accesso alla tabella rispetto all'attività media di accesso alla tabella nel data warehouse negli ultimi sette giorni.

Attualmente Advisor visualizza al massimo quattro candidati di tabella replicata alla volta con indici columnstore cluster con priorità per l'attività più elevata.

> [!IMPORTANT]
> I consigli relativi alla tabella replicata non costituiscono una prova completa e non prendono in considerazione operazioni di spostamento dei dati. Microsoft sta lavorando per l'aggiunta di questa opzione come elemento euristico, ma nel frattempo è sempre opportuno convalidare il carico di lavoro dopo aver applicato il consiglio. Contattare sqldwadvisor@service.microsoft.com se si individuano consigli relativi alla tabella replicata che causano una regressione del carico di lavoro. Per altre informazioni sulle tabelle replicate, consultare la [documentazione](design-guidance-for-replicated-tables.md#what-is-a-replicated-table) seguente.

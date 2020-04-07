---
title: Priorità del carico di lavoro
description: Indicazioni per l'impostazione dell'importanza per le query del pool SQL Synapse in Azure Synapse Analytics.Guidance for setting importance for Synapse SQL pool queries in Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 43ee14784b6049e9b5c1a78e733e72bbc45f915d
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80744050"
---
# <a name="azure-synapse-analytics-workload-importance"></a>Importanza del carico di lavoro di Azure Synapse AnalyticsAzure Synapse Analytics workload importance

Questo articolo illustra in che modo l'importanza del carico di lavoro può influenzare l'ordine di esecuzione per le richieste del pool SQL Synapse in Azure Synapse.This article explains how workload importance can influence the order of execution for Synapse SQL pool requests in Azure Synapse.

## <a name="importance"></a>priorità

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

Le esigenze aziendali possono richiedere che i carichi di lavoro di data warehousing siano più importanti di altri.  Si consideri uno scenario in cui i dati di vendita mission-critical vengono caricati prima della chiusura del periodo fiscale.  I carichi di dati per altre origini, ad esempio i dati meteo, non dispongono di slati rigorosi. L'impostazione di un'elevata importanza per una richiesta di caricamento dei dati di vendita e una bassa importanza per una richiesta di caricamento dei dati meteo garantisce che il caricamento dei dati di vendita acceda al primo accesso alle risorse e venga completato più rapidamente.

## <a name="importance-levels"></a>Livelli di importanza

Ci sono cinque livelli di importanza: basso, below_normal, normale, above_normal e alto.  Alle richieste che non impostano l'importanza viene assegnato il livello predefinito di normale. Le richieste con lo stesso livello di importanza hanno lo stesso comportamento di pianificazione attualmente esistente.

## <a name="importance-scenarios"></a>Scenari di importanza

Oltre allo scenario di importanza di base descritto in precedenza con i dati relativi alle vendite e alle condizioni meteorologiche, esistono altri scenari in cui l'importanza del carico di lavoro consente di soddisfare le esigenze di elaborazione e query dei dati.

### <a name="locking"></a>Blocco

L'accesso ai blocchi per l'attività di lettura e scrittura è un'area di contesa naturale. Attività quali il [cambio di partizione](sql-data-warehouse-tables-partition.md) o [RENAME OBJECT](/sql/t-sql/statements/rename-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) richiedono blocchi con privilegi elevati.  Senza l'importanza del carico di lavoro, synapse SQL pool in Azure Synapse ottimizza per la velocità effettiva. L'ottimizzazione per la velocità effettiva significa che quando le richieste in esecuzione e in coda hanno le stesse esigenze di blocco e le stesse risorse sono disponibili, le richieste in coda possono ignorare le richieste con esigenze di blocco più elevate arrivate nella coda delle richieste in precedenza. Una volta applicata l'importanza del carico di lavoro alle richieste con esigenze di blocco più elevate. La richiesta con maggiore importanza verrà eseguita prima della richiesta con un'importanza inferiore.

Prendere in considerazione gli esempi seguenti:

- Q1 sta eseguendo e selezionando attivamente i dati da SalesFact.
- Q2 è in coda in attesa del completamento del Primo Trimestre.  È stato inviato alle 9 del mattino e sta tentando di partizionare i nuovi dati in SalesFact.
- Q3 viene inviato alle 9:01 am e desidera selezionare i dati da SalesFact.

Se Q2 e Q3 hanno la stessa importanza e Q1 è ancora in esecuzione, Q3 inizierà l'esecuzione. Q2 continuerà ad attendere un blocco esclusivo su SalesFact.  Se Q2 ha un'importanza superiore a Q3, Q3 attenderà fino al completamento del secondo trimestre prima di poter iniziare l'esecuzione.

### <a name="non-uniform-requests"></a>Richieste non uniformi

Un altro scenario in cui l'importanza può aiutare a soddisfare le richieste di query è quando vengono inviate richieste con classi di risorse diverse.  Come è stato menzionato in precedenza, con la stessa importanza, il pool SQL Synapse in Azure Synapse ottimizza la velocità effettiva. Quando le richieste di dimensioni miste (ad esempio smallrc o mediumrc) vengono accodate, il pool Synapse SQL sceglierà la prima richiesta in arrivo che rientra nelle risorse disponibili. Se viene applicata l'importanza del carico di lavoro, la richiesta di priorità più alta viene pianificata successivamente.
  
Si consideri l'esempio seguente in DW500c:

- Q1, Q2, Q3 e Q4 eseguono query smallrc.
- Q5 viene inviato con la classe di risorse mediumrc alle 9:00.
- Il 6t viene inviato con la classe di risorse smallrc alle 9:01.

Poiché Q5 è mediumrc, richiede due slot di concorrenza. Q5 deve attendere il completamento di due delle query in esecuzione.  Tuttavia, quando una delle query in esecuzione (Q1-Q4) viene completata, Q6 viene pianificato immediatamente perché esistono le risorse per eseguire la query.  Se Q5 ha un'importanza superiore a quella del 6, Q6 attende che Q5 sia in esecuzione prima di iniziare l'esecuzione.

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sulla creazione di un classificatore, vedere [CREATE WORKLOAD CLASSIFIER (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  
- Per altre informazioni sulla classificazione del carico di lavoro, vedere [Classificazione del carico di lavoro.](sql-data-warehouse-workload-classification.md)  
- Vedere la Guida introduttiva [Creare il classificatore](quickstart-create-a-workload-classifier-tsql.md) del carico di lavoro per informazioni su come creare un classificatore del carico di lavoro.
- Vedere gli articoli sulle procedure per [configurare la priorità del carico di lavoro](sql-data-warehouse-how-to-configure-workload-importance.md) e per [gestire e monitorare la priorità del carico di lavoro](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
- Consultare [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) per visualizzare le query e la loro priorità.

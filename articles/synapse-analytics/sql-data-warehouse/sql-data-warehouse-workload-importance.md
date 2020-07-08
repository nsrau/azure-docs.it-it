---
title: Priorità del carico di lavoro
description: Linee guida per l'impostazione dell'importanza per le query del pool SQL sinapsi in Azure sinapsi Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 1b2c71d7bf9e796af77e9a2a4a3a31152f2ca884
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85212344"
---
# <a name="azure-synapse-analytics-workload-importance"></a>Importanza del carico di lavoro di analisi sinapsi di Azure

Questo articolo illustra in che modo l'importanza del carico di lavoro può influenzare l'ordine di esecuzione per le richieste del pool SQL sinapsi in sinapsi di Azure.

## <a name="importance"></a>Importanza

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

Le esigenze aziendali possono richiedere che i carichi di lavoro di data warehousing siano più importanti rispetto ad altri.  Si consideri uno scenario in cui i dati di vendita mission-critical vengono caricati prima della chiusura del periodo fiscale.  I caricamenti di dati per altre origini, ad esempio i dati meteorologici, non hanno contratti di contratto severi L'impostazione di un'importanza elevata per una richiesta di caricamento dei dati di vendita e di bassa importanza a una richiesta di caricamento dei dati meteorologici garantisce che il carico dei dati delle vendite ottenga il primo accesso alle risorse e venga completato più rapidamente.

## <a name="importance-levels"></a>Livelli di importanza

Sono disponibili cinque livelli di importanza: Low, below_normal, Normal, above_normal e High.  Alle richieste che non impostano l'importanza viene assegnato il livello predefinito normale. Le richieste con lo stesso livello di importanza hanno lo stesso comportamento di pianificazione attualmente esistente.

## <a name="importance-scenarios"></a>Scenari di importanza

Oltre agli scenari di importanza fondamentale descritti in precedenza con le vendite e i dati meteorologici, esistono altri scenari in cui l'importanza del carico di lavoro aiuta a soddisfare le esigenze di elaborazione dei dati e di query

### <a name="locking"></a>Blocco

L'accesso ai blocchi per l'attività di lettura e scrittura è un'area di contesa naturale. Attività quali il [cambio di partizione](sql-data-warehouse-tables-partition.md) o l' [oggetto di ridenominazione](/sql/t-sql/statements/rename-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) richiedono blocchi con privilegi elevati.  Senza importanza del carico di lavoro, il pool SQL sinapsi in sinapsi di Azure ottimizza la velocità effettiva. L'ottimizzazione per la velocità effettiva significa che quando le richieste in esecuzione e in coda hanno le stesse esigenze di blocco e sono disponibili risorse, le richieste in coda possono ignorare le richieste con esigenze di blocco più elevate che arrivano prima nella coda di richieste. Una volta applicata l'importanza del carico di lavoro alle richieste con esigenze di blocco più elevate. La richiesta con maggiore importanza verrà eseguita prima della richiesta con priorità più bassa.

Prendere in considerazione gli esempi seguenti:

- Q1 sta eseguendo attivamente e selezionando i dati da SalesFact.
- Q2 è in coda in attesa del completamento del Q1.  È stato inviato alle 9.00 e sta provando a partizionare i nuovi dati di cambio in SalesFact.
- Q3 viene inviato a 9:01am e desidera selezionare i dati da SalesFact.

Se Q2 e Q3 hanno la stessa importanza e il Q1 è ancora in esecuzione, verrà avviata l'esecuzione del terzo trimestre. Q2 continuerà ad attendere un blocco esclusivo su SalesFact.  Se Q2 ha una maggiore importanza del Q3, il Q3 resterà in attesa fino al termine del Q2 prima di poter iniziare l'esecuzione.

### <a name="non-uniform-requests"></a>Richieste non uniformi

Un altro scenario in cui l'importanza può essere utile per soddisfare le richieste di query è quando vengono inviate richieste con classi di risorse diverse.  Come indicato in precedenza, con la stessa importanza, il pool SQL sinapsi in Azure sinapsi ottimizza la velocità effettiva. Quando le richieste di dimensioni miste, ad esempio smallrc o mediumrc, vengono accodate, il pool SQL sinapsi sceglierà la prima richiesta in arrivo che rientra nelle risorse disponibili. Se viene applicata l'importanza del carico di lavoro, la richiesta di importanza più elevata viene pianificata successivamente.
  
Si consideri l'esempio seguente in DW500c:

- Q1, Q2, Q3 e Q4 eseguono query smallrc.
- Q5 viene inviato con la classe di risorse mediumrc alle 9.00.
- Q6 viene inviato con la classe di risorse smallrc a 9:01am.

Poiché Q5 è mediumrc, richiede due slot di concorrenza. Q5 deve attendere il completamento di due delle query in esecuzione.  Tuttavia, quando una delle query in esecuzione (Q1-Q4) viene completata, Q6 viene pianificato immediatamente perché sono presenti risorse per eseguire la query.  Se Q5 ha una priorità più elevata rispetto a Q6, Q6 attende che la versione Q5 sia in esecuzione prima di poter iniziare l'esecuzione.

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sulla creazione di un classificatore, vedere la pagina relativa alla creazione di un [classificatore del carico di lavoro (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  
- Per altre informazioni sulla classificazione del carico di lavoro, vedere [Classificazione del carico di lavoro](sql-data-warehouse-workload-classification.md).  
- Per informazioni su come creare un classificatore del carico di lavoro, vedere la Guida introduttiva [creare un classificatore](quickstart-create-a-workload-classifier-tsql.md) .
- Vedere gli articoli sulle procedure per [configurare la priorità del carico di lavoro](sql-data-warehouse-how-to-configure-workload-importance.md) e per [gestire e monitorare la priorità del carico di lavoro](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
- Consultare [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) per visualizzare le query e la loro priorità.

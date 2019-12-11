---
title: Priorità del carico di lavoro
description: Linee guida per l'impostazione dell'importanza per le query in Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 05/01/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 28d239d47b46a5aafdf65c72ef826a0efb79f52b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974634"
---
# <a name="azure-sql-data-warehouse-workload-importance"></a>Importanza del carico di lavoro Azure SQL Data Warehouse

Questo articolo illustra in che modo l'importanza del carico di lavoro può influenzare l'ordine di esecuzione per le richieste di SQL Data Warehouse.

## <a name="importance"></a>priorità

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

Le esigenze aziendali possono richiedere che i carichi di lavoro di data warehousing siano più importanti rispetto ad altri.  Si consideri uno scenario in cui i dati di vendita mission-critical vengono caricati prima della chiusura del periodo fiscale.  I caricamenti di dati per altre origini, ad esempio i dati meteorologici, non hanno contratti di contratto severi Impostando un'importanza elevata per una richiesta di caricamento dei dati di vendita e di minore importanza a una richiesta di caricamento dei dati, il caricamento dei dati delle vendite ottiene il primo accesso alle risorse e viene completato più rapidamente.

## <a name="importance-levels"></a>Livelli di importanza

Sono disponibili cinque livelli di importanza: Low, below_normal, Normal, above_normal e High.  Alle richieste che non impostano l'importanza viene assegnato il livello predefinito normale. Le richieste con lo stesso livello di importanza hanno lo stesso comportamento di pianificazione attualmente esistente.

## <a name="importance-scenarios"></a>Scenari di importanza

Oltre agli scenari di importanza fondamentale descritti in precedenza con le vendite e i dati meteorologici, esistono altri scenari in cui l'importanza del carico di lavoro aiuta a soddisfare le esigenze di elaborazione dei dati e di query

### <a name="locking"></a>Blocco

L'accesso ai blocchi per l'attività di lettura e scrittura è un'area di contesa naturale. Attività quali il [cambio di partizione](/azure/sql-data-warehouse/sql-data-warehouse-tables-partition) o l' [oggetto di ridenominazione](/sql/t-sql/statements/rename-transact-sql) richiedono blocchi con privilegi elevati.  Senza importanza del carico di lavoro, SQL Data Warehouse ottimizza la velocità effettiva.  L'ottimizzazione per la velocità effettiva significa che quando le richieste in esecuzione e in coda hanno le stesse esigenze di blocco e sono disponibili risorse, le richieste in coda possono ignorare le richieste con esigenze di blocco più elevate che arrivano prima nella coda di richieste.  Una volta applicata l'importanza del carico di lavoro alle richieste con esigenze di blocco più elevate, le richieste con priorità più elevata verranno eseguite prima della richiesta con priorità più bassa.

Si consideri l'esempio seguente:

Q1 sta eseguendo attivamente e selezionando i dati da SalesFact.
Q2 è in coda in attesa del completamento del Q1.  È stato inviato alle 9.00 e sta provando a partizionare i nuovi dati di cambio in SalesFact.
Q3 viene inviato a 9:01am e desidera selezionare i dati da SalesFact.

Se Q2 e Q3 hanno la stessa importanza e il Q1 è ancora in esecuzione, verrà avviata l'esecuzione del terzo trimestre. Q2 continuerà ad attendere un blocco esclusivo su SalesFact.  Se Q2 ha una maggiore importanza del Q3, il Q3 resterà in attesa fino al termine del Q2 prima di poter iniziare l'esecuzione.

### <a name="non-uniform-requests"></a>Richieste non uniformi

Un altro scenario in cui l'importanza può essere utile per soddisfare le richieste di query è quando vengono inviate richieste con classi di risorse diverse.  Come indicato in precedenza, con la stessa importanza SQL Data Warehouse ottimizza la velocità effettiva. Quando vengono accodate richieste di dimensioni miste, ad esempio smallrc o mediumrc, SQL Data Warehouse sceglierà la prima richiesta in arrivo che rientra nelle risorse disponibili. Se viene applicata l'importanza del carico di lavoro, la richiesta di importanza più elevata viene pianificata successivamente.
  
Si consideri l'esempio seguente in DW500c:

Q1, Q2, Q3 e Q4 eseguono query smallrc.
Q5 viene inviato con la classe di risorse mediumrc alle 9.00.
Q6 viene inviato con la classe di risorse smallrc a 9:01am.

Poiché Q5 è mediumrc, richiede due slot di concorrenza. Q5 deve attendere il completamento di due delle query in esecuzione.  Tuttavia, quando una delle query in esecuzione (Q1-Q4) viene completata, Q6 viene pianificato immediatamente perché sono presenti risorse per eseguire la query.  Se Q5 ha una priorità più elevata rispetto a Q6, Q6 attende che la versione Q5 sia in esecuzione prima di poter iniziare l'esecuzione.

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sulla creazione di un classificatore, vedere la pagina relativa alla creazione di un [classificatore del carico di lavoro (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql).  
- Per ulteriori informazioni sulla classificazione del carico di lavoro SQL Data Warehouse, vedere [classificazione dei carichi di lavoro](sql-data-warehouse-workload-classification.md).  
- Per informazioni su come creare un classificatore del carico di lavoro, vedere la Guida introduttiva [creare un classificatore](quickstart-create-a-workload-classifier-tsql.md) .
- Vedere gli articoli sulle procedure per [configurare la priorità del carico di lavoro](sql-data-warehouse-how-to-configure-workload-importance.md) e per [gestire e monitorare la priorità del carico di lavoro](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
- Consultare [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) per visualizzare le query e la loro priorità.

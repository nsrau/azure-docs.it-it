---
title: Importanza del carico di lavoro | Microsoft Docs
description: Linee guida per l'impostazione di priorità per le query in Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
ms.date: 03/13/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 12e7d9bc22eff14bbf302aed50080412d04a40d3
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2019
ms.locfileid: "59616629"
---
# <a name="sql-data-warehouse-workload-importance-preview"></a>Importanza del carico di lavoro di SQL Data Warehouse (anteprima)

Questo articolo illustra come priorità del carico di lavoro possono influenzare l'ordine di esecuzione per le richieste di SQL Data Warehouse.

> [!Note]
> La classificazione del carico di lavoro è disponibile in anteprima in SQL Data Warehouse Gen2. Anteprima carico di lavoro gestione classificazione e priorità è per le compilazioni con una data di rilascio di 9 aprile 2019 o versione successiva.  Gli utenti consigliabile evitare di usare le compilazioni precedenti a tale data per il test di carico di lavoro gestione.  Per determinare se la compilazione è in grado di gestione del carico di lavoro, eseguire select @@version quando si è connessi all'istanza di SQL Data Warehouse.

## <a name="importance"></a>priorità

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

Esigenze aziendali possono richiedere carichi di lavoro per essere più importante rispetto ad altri di data warehousing.  Si consideri uno scenario in cui i dati di vendita mission-critical viene caricati prima di chiudere periodo fiscale.  Dati vengono caricati per le altre origini, ad esempio i dati meteo non dispone di contratti di servizio di tipo strict.   L'impostazione di elevata importanza per una richiesta di caricamento dei dati di vendita e importanza bassa a una richiesta di caricamento se i dati assicura il caricamento dei dati di vendita ottiene prima di accesso alle risorse e viene completato più velocemente.

## <a name="importance-levels"></a>Livelli di importanza

Esistono cinque livelli di importanza: bassa, below_normal, normale, above_normal e alta.  Le richieste che non impostano la priorità vengono assegnate il livello predefinito Normal.  Le richieste che hanno lo stesso livello di priorità hanno lo stesso comportamento di pianificazione che esista già.

## <a name="importance-scenarios"></a>Scenari di importanza

Oltre lo scenario di importanza di base descritto in precedenza con vendite e i dati meteo, esistono altri scenari in cui la priorità del carico di lavoro consente di soddisfare l'elaborazione dati e l'esecuzione di query alle esigenze.

### <a name="locking"></a>Blocco

Accedere a blocchi per la lettura e scrittura di attività è un'area di contesa naturale.  Le attività, ad esempio [cambio della partizione](/azure/sql-data-warehouse/sql-data-warehouse-tables-partition) oppure [RENAME OBJECT](/sql/t-sql/statements/rename-transact-sql) richiedono i blocchi con privilegi elevati.  Senza importanza del carico di lavoro, SQL Data Warehouse Ottimizza per velocità effettiva.  Ottimizza per velocità effettiva significa che durante l'esecuzione e le richieste in coda sono le stesse esigenze di bloccante e sono disponibili risorse, le richieste in coda possono ignorare le richieste con richiede il blocco superiore che è arrivato nella coda di richieste in precedenza.  Dopo aver applicato le richieste con blocco maggiore importanza del carico di lavoro è necessario. Richiesta con un'importanza superiore verrà eseguito prima della richiesta con priorità inferiore.

Si consideri l'esempio seguente:

Q1 è attivamente in esecuzione e selezione di dati da SalesFact.
(Domanda 2) viene accodato in attesa di Q1 per il completamento.  Inviata alle 9:00 e si sta provando a nuovi dati switch partition in SalesFact.
(Domanda 3) viene inviato alle 9:00: 01 e richiede di selezionare dati da SalesFact.

Se Q2 e Q3 hanno la stessa importanza e l'esecuzione di Q1, Q3 avvia l'esecuzione. Q2 rimarrà in attesa di un blocco esclusivo su SalesFact.  Se (domanda 2) ha un'importanza superiore rispetto a (domanda 3), Q3 attenderà fino al termine del Q2 prima che possa iniziare l'esecuzione.

### <a name="non-uniform-requests"></a>Richieste non-uniform

Un altro scenario in cui importanza può aiutare a soddisfare le richieste di query è quando vengono inviate le richieste con diverse classi di risorse.  Come accennato precedentemente, con la stessa importanza, SQL Data Warehouse Ottimizza per velocità effettiva.  Quando sono in coda le richieste di dimensioni misto (ad esempio smallrc o mediumrc), SQL Data Warehouse sceglierà la richiesta in arrivo prima che si adattino alle risorse disponibili.  Se viene applicata l'importanza del carico di lavoro, la richiesta di importanza più alta è pianificata successivo.
  
Si consideri l'esempio seguente su DW500c:

Q1, Q2, T3 e T4 eseguono le query smallrc.
(Domanda 5) viene inviato con la classe di risorse mediumrc alle 9:00.
(Domanda 6) viene inviato con la classe risorsa smallrc alle 9:00: 01.

Poiché mediumrc (domanda 5), richiede due slot di concorrenza.  (Domanda 5) deve rimanere in attesa per due delle query in esecuzione per il completamento.  Tuttavia, quando viene completata una delle query in esecuzione (Q1 a Q4), (domanda 6) è pianificato immediatamente perché le risorse disponibili per eseguire la query.  Se (domanda 5) ha un'importanza superiore rispetto a (domanda 6), (domanda 6) attende (domanda 5) è in esecuzione prima che possa iniziare l'esecuzione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla classificazione del carico di lavoro di SQL Data Warehouse, vedere [classificazione del carico di lavoro di SQL Data Warehouse](sql-data-warehouse-workload-classification.md) e [crea un classificatore di carico di lavoro](quickstart-create-a-workload-classifier-tsql.md). Consultare [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) per visualizzare le query e la loro priorità.

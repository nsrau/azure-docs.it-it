---
title: Note sulla versione di Azure SQL Data Warehouse - Aprile 2018 | Microsoft Docs
description: Note sulla versione di Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/23/2018
ms.author: anjangsh
ms.reviewer: jrasnick
ms.openlocfilehash: 5fd2d5e2022cc1cf552ee7b525dcc484cc718f1f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65912252"
---
# <a name="whats-new-in-azure-sql-data-warehouse-april-2018"></a>Novità di Azure SQL Data Warehouse Aprile 2018
Azure SQL Data Warehouse riceve continuamente miglioramenti. Questo articolo illustra le nuove funzionalità e le modifiche introdotte nel mese di aprile 2018.

## <a name="ability-to-truncate-a-partition-before-a-switch"></a>Possibilità di troncare una partizione prima di un cambio
I clienti usano spesso il cambio di partizione come modello per caricare i dati da una tabella a un'altra modificando i metadati della tabella tramite la sintassi `ALTER TABLE SourceTable SWITCH PARTITION X TO TargetTable PARTITION X`. SQL Data Warehouse non supporta il cambio di partizione quando la partizione di destinazione contiene dati. Se la partizione di destinazione contiene già dei dati, il cliente deve troncare la partizione di destinazione e quindi eseguire il cambio.

SQL Data Warehouse supporta ora questa operazione in una singola istruzione T-SQL.

```sql
ALTER TABLE SourceTable 
    SWITCH PARTITION X TO TargetTable PARTITION X
    WITH (TRUNCATE_TARGET_PARTITION = ON)
```
Per altre informazioni, vedere l'articolo [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).

## <a name="improved-query-compilation-performance"></a>Prestazioni di compilazione delle query migliorate
SQL Data Warehouse ha introdotto un set di modifiche per migliorare la fase di compilazione delle query distribuite. Queste modifiche migliorano i tempi di compilazione delle query di **10 volte** riducendo i runtime di esecuzione complessivi delle query. Queste modifiche sono più evidenti nei data warehouse con un numero elevato di oggetti (tabelle, funzioni, viste, procedure).

## <a name="dbcc-commands-do-not-consume-concurrency-slots-behavior-change"></a>I comandi DBCC non usano gli slot di concorrenza (modifica funzionale)
SQL Data Warehouse supporta un subset di [comandi DBCC](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) T-SQL, ad esempio [DBCC DROPCLEANBUFFERS](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropcleanbuffers-transact-sql). In precedenza, questi comandi avrebbero utilizzato uno [slot di concorrenza](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#concurrency-slots) riducendo il numero di carichi/query utente che poteva essere eseguito. I comandi `DBCC` vengono ora eseguiti in una coda locale che non utilizza uno slot di risorse migliorando le prestazioni di esecuzione complessive delle query.

## <a name="updated-error-message-for-excessive-literals-behavior-change"></a>Messaggio di errore aggiornato per un numero eccessivo di valori letterali (modifica funzionale)
In precedenza, SQL Data Warehouse avrebbe incluso un conteggio *approssimativo* quando una query conteneva troppi valori letterali.
```
Msg 100086
Cannot have more than 20,000 literals in the query. The query contains [n] literals.
```

Il messaggio di errore è stato aggiornato per indicare solo che è stato raggiunto il limite di valori letterali.
```
Msg 100086
The number of literals in the query is beyond the limit. Please rewrite your query.
```

Per altre informazioni sui limiti massimi, vedere la sezione [Query](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits#queries) dell'articolo [Limiti di capacità](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits).

## <a name="removed-the-syspdwdatabasemappings-view-behavior-change"></a>Vista SYS.PDW_DATABASE_MAPPINGS rimossa (modifica funzionale)
La vista `sys.pdw_database_mappings` non viene usata in SQL Data Warehouse. In precedenza, l'istruzione SELECT di questa vista non avrebbe restituito alcun risultato. La vista è stata rimossa. 

## <a name="next-steps"></a>Passaggi successivi
Dopo aver appreso alcune informazioni su SQL Data Warehouse, vedere come [creare un SQL Data Warehouse][create a SQL Data Warehouse] rapidamente. Se non si ha familiarità con Azure, il [glossario di Azure][Azure glossary] può essere utile quando si incontrano termini nuovi. Oppure vedere alcune delle altre risorse disponibili per SQL Data Warehouse.  

* [Casi di successo dei clienti]
* [Blog]
* [Richieste di funzionalità]
* [Video]
* [Blog Customer Advisory Team]
* [Forum su Stack Overflow]
* [Twitter]


[Blog]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blog Customer Advisory Team]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Casi di successo dei clienti]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Richieste di funzionalità]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum su Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Video]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

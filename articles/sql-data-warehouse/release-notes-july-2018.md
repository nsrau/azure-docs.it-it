---
title: Note sulla versione di Azure SQL Data Warehouse - Luglio 2018 | Microsoft Docs
description: Note sulla versione di Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/06/2018
ms.author: anjangsh
ms.reviewer: jrasnick
ms.openlocfilehash: 4ec90bef9228eccb9556d07bb5680deb00e2550e
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65912182"
---
# <a name="whats-new-in-azure-sql-data-warehouse-july-2018"></a>Novità di Azure SQL Data Warehouse Luglio 2018
Azure SQL Data Warehouse riceve continuamente miglioramenti. Questo articolo illustra le nuove funzionalità e le modifiche introdotte nel mese di luglio 2018.

## <a name="lightning-fast-query-performance"></a>Prestazioni delle query ad alta velocità
[Azure SQL Data Warehouse](https://aka.ms/sqldw) imposta nuovi benchmark delle prestazioni con l'introduzione della funzione di accesso immediato ai dati che migliora le operazioni casuali. La funzione di accesso immediato ai dati riduce il sovraccarico per le operazioni di spostamento dei dati usando operazioni dirette tra SQL Server e i dati nativi di SQL Server. L'integrazione diretta con il motore di SQL Server per lo spostamento dei dati significa che SQL Data Warehouse è ora **più veloce del 67% rispetto ad Amazon Redshift** con un carico di lavoro derivato dal noto standard di settore [TPC Benchmark™ H (TPC-H)](http://www.tpc.org/tpch/).

![Azure SQL Data Warehouse è più veloce ed economico rispetto ad Amazon Redshift](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/eb3b908a-464d-4847-b384-9f296083a737.png)
<sub>Fonte: [Gigaom Research Analyst Report: Data Warehouse in the Cloud Benchmark](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/)</sub> (Report degli analisti della ricerca Gigaom: benchmark di data warehouse nel cloud)

Oltre alle prestazioni di runtime, il report [Gigaom Research](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/) ha misurato anche il rapporto prezzo-prestazioni per quantificare il costo di carichi di lavoro specifici. SQL Data Warehouse è risultato **meno caro di almeno il 23%** rispetto a Redshift per carichi di lavoro di 30 TB. Grazie alla capacità di SQL Data Warehouse di ridimensionare in modo elastico le risorse di calcolo, nonché di sospendere e riprendere i carichi di lavoro, i clienti pagano solo quando il servizio è in uso, riducendo ulteriormente i costi.
![Azure SQL Data Warehouse è più veloce ed economico rispetto ad Amazon Redshift](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/cb76447e-621e-414b-861e-732ffee5345a.png)
<sub>Fonte: [Gigaom Research Analyst Report: Data Warehouse in the Cloud Benchmark](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/)</sub> (Report degli analisti della ricerca Gigaom: benchmark di data warehouse nel cloud)

### <a name="query-concurrency"></a>Concorrenza delle query
SQL Data Warehouse garantisce anche che i dati siano accessibili all'interno delle organizzazioni. Microsoft ha migliorato il servizio in modo da supportare 128 query simultanee e consentire così a più utenti di eseguire query sullo stesso database e non essere bloccati da altre richieste. In confronto, Amazon Redshift limita a 50 il numero massimo di query simultanee, ostacolando l'accesso ai dati all'interno dell'organizzazione.

SQL Data Warehouse è in grado di offrire questi miglioramenti a livello di prestazioni e concorrenza delle query senza alcun aumento di prezzo grazie all'esclusiva architettura con separazione tra archiviazione e calcolo.

## <a name="finer-granularity-for-cross-region-and-server-restores"></a>Maggiore granularità per i ripristini tra aree e server
Ora è possibile eseguire il ripristino tra aree e server usando qualsiasi punto di ripristino, invece di selezionare i backup con ridondanza geografica che vengono eseguiti ogni 24 ore. I ripristini tra aree e server sono supportati sia per i punti di ripristino automatici che per quelli definiti dall'utente, consentendo una maggiore granularità per una protezione dei dati aggiuntiva. Con la disponibilità di più punti di ripristino, è possibile avere la certezza che il data warehouse sarà logicamente coerente durante il ripristino tra aree.

![Comando di ripristino - Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6ac23972-9ec0-4502-ab10-7b6bc1a3d947.png)
![Opzioni di ripristino - Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6c63bd0e-9c52-414d-b4be-d3bd3774ee08.png)

Per altre informazioni, vedere il post di blog [Accelerated and Flexible Restore Points](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/) (Punti di ripristino accelerati e flessibili).

## <a name="20-minute-restorations"></a>Ripristini in 20 minuti
SQL Data Warehouse offre ora il ripristino di qualsiasi data warehouse **in meno di 20 minuti** nella stessa area, indipendentemente dalla dimensione del database. Il tempo di ripristino è applicabile sia in caso di ripristino nello stesso server logico che in un diverso server logico nella stessa area. Inoltre, il processo di snapshot è stato migliorato per ridurre il tempo necessario per creare un punto di ripristino. Ai livelli di prestazioni più bassi (impostazioni DWU inferiori), il miglioramento consente di *dimezzare* il tempo per la creazione degli snapshot.

Per altre informazioni, vedere il post di blog [Accelerated and Flexible Restore Points](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/) (Punti di ripristino accelerati e flessibili).

## <a name="custom-restoration-configurations"></a>Configurazioni di ripristino personalizzate
Ora è possibile modificare il livello di prestazioni (DWU) durante il ripristino nel portale di Azure. È anche possibile eseguire il ripristino in un data warehouse di seconda generazione aggiornato. Tramite il ripristino in un'istanza di seconda generazione, è ora possibile valutare l'impatto del sistema di seconda generazione prima dell'[aggiornamento del data warehouse di prima generazione](https://docs.microsoft.com/azure/sql-data-warehouse/upgrade-to-latest-generation).

![Configurazione di ripristino personalizzata - Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/f4c410c7-8515-409c-a983-0976792b8628.png)

## <a name="spdescribeundeclaredparameters"></a>SP_DESCRIBE_UNDECLARED_PARAMETERS
La stored procedure [sp_describe_undeclared_parameters](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql) viene spesso usata dagli strumenti per ottenere i metadati sui parametri in batch Transact-SQL. La procedura restituisce una riga per ogni parametro nel batch con le informazioni sul tipo dedotto per tale parametro. 

```sql
EXEC sp_describe_undeclared_parameters
    @tsql = 
    N'SELECT
        object_id, name, type_desc
      FROM
        sys.indexes
      WHERE
        object_id = @id;'
```

Il set di risultati include i metadati sul parametro `@id` (i risultati indicati sono parziali)
```
parameter_ordinal | name | suggested_system_type_id | suggested_system_type_name
--------------------------------------------------------------------------------
1                 | @id  | 56                       | int
```
## <a name="sprefreshsqlmodule"></a>SP_REFRESHSQLMODULE
La stored procedure [sp_refreshsqlmodule](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-refreshsqlmodule-transact-sql) aggiorna i metadati per un oggetto di database se i metadati sottostanti sono diventati obsoleti in seguito a modifiche degli oggetti sottostanti. Questa situazione può verificarsi nel caso in cui vengano modificate le tabelle di base per una vista e la vista non sia stata ricreata. In questo modo è possibile evitare la procedura di eliminazione e ricreazione degli oggetti dipendenti.

L'esempio seguente mostra una vista che diventa obsoleta in seguito alla modifica della tabella sottostante. Si noterà che i dati sono corretti in merito alla modifica della prima colonna (da 1 a Mollie), ma il nome della colonna non è valido e la seconda colonna non è presente. 
```sql
CREATE TABLE base_table (Id INT);
GO

INSERT INTO base_table (Id) VALUES (1);
GO

CREATE VIEW base_view AS SELECT * FROM base_table;
GO

SELECT * FROM base_view;
GO

-- Id
-- ----
-- 1

DROP TABLE base_table;
GO

CREATE TABLE base_table (fname VARCHAR(10), lname VARCHAR(10));
GO

INSERT INTO base_table (fname, lname) VALUES ('Mollie', 'Gallegos');
GO

SELECT * FROM base_view;
GO

-- Id
-- ----------
-- Mollie

EXEC sp_refreshsqlmodule @Name = 'base_view';
GO

SELECT * FROM base_view;
GO

-- fname     | lname
-- ---------- ----------
-- Mollie    | Gallegos
```

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

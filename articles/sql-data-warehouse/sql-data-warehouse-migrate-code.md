---
title: Eseguire la migrazione del codice SQL in SQL Data Warehouse | Documentazione Microsoft
description: Suggerimenti per la migrazione del codice SQL in Azure SQL Data Warehouse per lo sviluppo di soluzioni.
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 19c252a3-0e41-4eec-9d3e-09a68c7e7add
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0ff5ad648d429da433170301205eafb850be5d81


---
# <a name="migrate-your-sql-code-to-sql-data-warehouse"></a>Eseguire la migrazione del codice SQL in SQL Data Warehouse
Quando si esegue la migrazione del codice da un altro database su SQL Data Warehouse, è molto probabile che debbano essere apportate modifiche alla base di codice. Alcune funzionalità di SQL Data Warehouse possono migliorare in modo significativo le prestazioni perché sono progettate per funzionare direttamente in modalità distribuita. Per mantenere tuttavia le prestazioni e la scalabilità, alcune funzionalità non sono disponibili.

## <a name="common-t-sql-limitations"></a>Limitazioni comuni di T-SQL
L'elenco seguente riepiloga le funzionalità principali che non sono supportate in Azure SQL Data Warehouse. I collegamenti consentono di realizzare soluzioni alternative per la caratteristica non supportata:

* [Join ANSI sugli aggiornamenti][Join ANSI sugli aggiornamenti] (Join ANSI sugli aggiornamenti)
* [ANSI joins on deletes][Join ANSI sulle eliminazioni] (Join ANSI sulle eliminazioni)
* [istruzione merge][Istruzione merge]
* Join tra database
* [cursori][Cursori]
* [SELECT..INTO][SELECT..INTO]
* [INSERT..EXEC][INSERT..EXEC]
* Clausola di output
* Funzioni inline definite dall'utente
* Funzioni a istruzioni multiple
* [espressioni di tabella comune](#Common-table-expressions)
* [espressioni tabella comune ricorsive (CTE)] (#Recursive-common-table-expressions-(CTE)
* Funzioni e procedure CLR
* Funzione $partition
* Variabili di tabella
* Parametri di valori di tabella
* Transazioni distribuite
* Commit / rollback
* Transazione di salvataggio
* Contesti di esecuzione (EXECUTE AS)
* [raggruppamento per clausola con opzioni di rollup/cubo/set di raggruppamento][Raggruppamento per clausola con opzioni di rollup/cubo/set di raggruppamento]
* [livelli di annidamento superiori a 8][Livelli di annidamento superiori a 8]
* [aggiornamento tramite visualizzazioni][Aggiornamento tramite visualizzazioni]
* [uso di select per l'assegnazione di variabili][Uso di select per l'assegnazione di variabili]
* [nessun tipo di dati MAX per stringhe SQL dinamiche][Nessun tipo di dati MAX per stringhe SQL dinamiche]

Fortunatamente è possibile ovviare alla maggior parte di queste limitazioni. Le spiegazioni sono fornite dagli articoli pertinenti a cui viene fatto riferimento più indietro.

## <a name="supported-cte-features"></a>Funzionalità CTE supportate
Le espressioni di tabella comune (CTE) sono parzialmente supportate in SQL Data Warehouse.  Attualmente sono supportate le funzionalità CTE seguenti:

* Un'espressione CTE può essere specificata in un'istruzione SELECT.
* Un'espressione CTE può essere specificata in un'istruzione CREATE VIEW.
* Un'espressione CTE può essere specificata in un'istruzione CREATE TABLE AS SELECT (CTAS).
* Un'espressione CTE può essere specificata in un'istruzione CREATE REMOTE TABLE AS SELECT (CRTAS).
* Un'espressione CTE può essere specificata in un'istruzione CREATE EXTERNAL TABLE AS SELECT (CETAS).
* È possibile fare riferimento a una tabella remota da un'espressione CTE.
* È possibile fare riferimento a una tabella esterna da un'espressione CTE.
* In un'espressione CTE possono essere definite più definizioni di query CTE.

## <a name="cte-limitations"></a>Limitazioni CTE
Le espressioni di tabella comune presentano alcune limitazioni in SQL Data Warehouse, tra cui:

* Un'espressione CTE deve essere seguita da una singola istruzione SELECT. Le istruzioni INSERT, UPDATE, DELETE e MERGE non sono supportate.
* Un'espressione di tabella comune che include riferimenti a se stessa (un'espressione di tabella comune ricorsiva) non è supportata (vedere la sezione sotto).
* Non è consentito specificare più di una clausola WITH in un'espressione CTE. Se, ad esempio, CTE_query_definition contiene una sottoquery, tale sottoquery non può contenere una clausola WITH annidata che definisce un'altra CTE.
* Una clausola ORDER BY non può essere usata in CTE_query_definition, tranne quando viene specificata una clausola TOP.
* Quando un'espressione CTE viene usata in un'istruzione che fa parte di un batch, l'istruzione precedente deve essere seguita da un punto e virgola.
* Quando vengono usate in istruzioni preparate da sp_prepare, le espressioni CTE si comporteranno esattamente come le altre istruzioni SELECT in PDW. Tuttavia, se le CTE vengono usate come parte di istruzioni CETAS preparate da sp_prepare, il comportamento può variare rispetto a SQL Server e altre istruzioni PDW per la modalità di implementazione del binding per sp_prepare. Se l'istruzione SELECT che fa riferimento alla CTE usa una colonna non corretta che non esiste nella CTE, sp_prepare passa senza rilevare l'errore, che invece viene generato durante sp_execute.

## <a name="recursive-ctes"></a>CTE ricorsive
Le CTE ricorsive non sono supportate in SQL Data Warehouse.  È possibile completare la migrazione delle CTE ricorsive tramite una procedura suddivisa in più passaggi. In genere è possibile usare un ciclo e popolare una tabella temporanea mentre si scorrono le query provvisorie ricorsive. Una volta che viene popolata la tabella temporanea, è quindi possibile restituire i dati come un unico set di risultati. Un approccio simile è stato usato per risolvere `GROUP BY WITH CUBE` nell'articolo relativo al [raggruppamento per clausola con opzioni di rollup/cubo/set di raggruppamento][Raggruppamento per clausola con opzioni di rollup/cubo/set di raggruppamento].

## <a name="unsupported-system-functions"></a>Funzioni di sistema non supportate
Anche alcune funzioni di sistema non sono supportate. Alcune di queste funzioni principali che in genere vengono usate nel data warehousing sono:

* NEWSEQUENTIALID()
* @@NESTLEVEL()
* @@IDENTITY()
* @@ROWCOUNT()
* ROWCOUNT_BIG
* ERROR_LINE()

Per alcuni di questi problemi esiste una soluzione alternativa.

## <a name="rowcount-workaround"></a>soluzione alternativa @@ROWCOUNT
Per risolvere la mancanza di supporto per @@ROWCOUNT,, creare una stored procedure per recuperare l'ultimo conteggio delle righe da sys.dm_pdw_request_steps e quindi eseguire `EXEC LastRowCount` dopo un'istruzione DML.

```sql
CREATE PROCEDURE LastRowCount AS
WITH LastRequest as 
(   SELECT TOP 1    request_id
    FROM            sys.dm_pdw_exec_requests
    WHERE           session_id = SESSION_ID()
    AND             resource_class IS NOT NULL
    ORDER BY end_time DESC
),
LastRequestRowCounts as
(
    SELECT  step_index, row_count
    FROM    sys.dm_pdw_request_steps
    WHERE   row_count >= 0
    AND     request_id IN (SELECT request_id from LastRequest)
)
SELECT TOP 1 row_count FROM LastRequestRowCounts ORDER BY step_index DESC
;
```

## <a name="next-steps"></a>Passaggi successivi
Per un elenco completo di tutte le istruzioni T-SQL supportate, vedere [Argomenti Transact-SQL][Argomenti Transact-SQL].

<!--Image references-->

<!--Article references-->
[Join ANSI sugli aggiornamenti]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[Join ANSI sulle eliminazioni]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[Istruzione merge]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[INSERT..EXEC]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[Argomenti Transact-SQL]: ./sql-data-warehouse-reference-tsql-statements.md

[Cursori]: ./sql-data-warehouse-develop-loops.md
[SELECT..INTO]: ./sql-data-warehouse-develop-ctas.md#selectinto
[Raggruppamento per clausola con opzioni di rollup/cubo/set di raggruppamento]: ./sql-data-warehouse-develop-group-by-options.md
[Livelli di annidamento superiori a 8]: ./sql-data-warehouse-develop-transactions.md
[Aggiornamento tramite visualizzazioni]: ./sql-data-warehouse-develop-views.md
[Uso di select per l'assegnazione di variabili]: ./sql-data-warehouse-develop-variable-assignment.md
[Nessun tipo di dati MAX per stringhe SQL dinamiche]: ./sql-data-warehouse-develop-dynamic-sql.md

<!--MSDN references-->

<!--Other Web references-->



<!--HONumber=Nov16_HO3-->


